
This document summarizes the changes made to split `open-gluco-server-microservice` into a clearer microservice-style architecture.

## Goal

The original server mixed multiple responsibilities in one Flask process:  
- HTTP API routing
- JWT authentication protection
- remember-me session refresh middleware
- PostgreSQL initialization
- InfluxDB initialization
- background CGM polling for Dexcom and LibreLinkUp
- Docker orchestration for frontend, API, database, and InfluxDB  

The refactor separates these responsibilities so the HTTP API and the CGM polling worker can run as independent services while still sharing common code.  

## New Runtime Services

The Docker Compose stack now runs these services:
- `api`: Flask/Gunicorn HTTP API on port `8000`
- `frontend`: Vite frontend on port `5173`
- `cgm-scheduler`: background worker that polls CGM providers and writes readings to InfluxDB
- `postgres`: relational database for users, sessions, profiles, connections, and cholesterol records
- `influxdb`: time-series database for CGM glucose readings
  
The key architectural change is that CGM polling no longer needs to run inside the API process.

## Main Code Changes

### `api/server.py`

Before:
- Created the Flask app
- Registered routes
- Initialized PostgreSQL and InfluxDB
- Contained remember-me refresh middleware
- Contained the CGM scheduler loop
- Contained the `token_required` decorator

After:
- Creates the Flask app
- Configures CORS
- Initializes PostgreSQL and InfluxDB
- Registers route blueprints
- Attaches session middleware from `api/session.py`
- Starts the CGM scheduler only when enabled

  

This made `server.py` an application factory instead of a file containing most of the backend behavior.
### `api/security.py`
New file.
Contains:
- `token_required`
- JWT cookie extraction
- JWT validation
- password-change invalidation check

Route files now import:  
```python
from ..security import token_required
```

instead of importing `token_required` from `api.server`.

This removes circular coupling between route modules and the Flask app factory.

### `api/session.py` 
New file.
Contains remember-me session refresh behavior: 
- checks `opengluco_remember_me`
- rotates remember-me token
- creates a fresh short-lived JWT
- attaches refreshed cookies after request processing

The Flask app registers this middleware using:
```python
register_session_refresh(app)
```
### `api/services/cgm_scheduler.py`

New file.
Contains the extracted CGM polling service:
- loads Dexcom and LibreLinkUp connections from PostgreSQL
- decrypts stored provider passwords using Fernet
- polls provider APIs
- writes glucose values to InfluxDB
- uses PostgreSQL advisory locks so only one scheduler instance runs
- supports repeated polling using a timer

This class is used in two ways:
- API process: `start_if_enabled(app)`
- worker process: `run_forever()`

### `cgm_worker.py`

New file.
Standalone entrypoint for the CGM scheduler container.
It:
- sets up logging
- initializes PostgreSQL tables
- initializes InfluxDB bucket
- starts the `CGMScheduler` worker loop 

The Docker service runs:
```bash
python cgm_worker.py
```

## Docker Compose Changes

### Added `cgm-scheduler`

The new service uses the same image as the API but runs a different command:
  

```yaml

cgm-scheduler:
build: .
command: ["python", "cgm_worker.py"]
depends_on:
- influxdb
- postgres
env_file:
- .env
environment:
- ENABLE_CGM_SCHEDULER=true
restart: unless-stopped
```

### Disabled scheduler in the API container
The API service now sets:

```yaml
environment:
- ENABLE_CGM_SCHEDULER=false
```

This prevents duplicate background polling inside the HTTP API process.

### Removed hard-coded `container_name`
Hard-coded container names such as:

- `opengluco-api`
- `opengluco-frontend`
- `postgres`
- `influxdb`

were removed.
Reason:
Docker container names are global on the machine. This repo has multiple Compose projects, and fixed names caused collisions and network issues.

Using Compose-generated names keeps each project isolated while still allowing service DNS names like:
- `postgres`
- `influxdb`
- `api`
inside the Compose network.

### Removed Postgres host port mapping
The original mapping:
```yaml
ports:
- "5432:5432"
```
was removed from `postgres`.
Reason:
- Another Postgres process/container was already using host port `5432`, causing startup failure.
- The API does not need Postgres exposed on the host. It only needs to reach Postgres inside Docker using:

```env
POSTGRES_HOST=postgres
```

## Runtime Issues Fixed
### Frontend was not reachable on `localhost:5173`
Cause:
- Only `influxdb` was running. Other services were in `Created` state.

Fix:
- Started the full Compose stack:
```bash
docker compose up -d
```
Verified Vite was listening on:
```text
http://localhost:5173/
```
### Account creation failed
Cause:
- The API could not resolve/connect to `postgres`.
Logs showed:
```text
failed to resolve host 'postgres'
```
Root cause:
- The `postgres` container was not attached correctly to the same Compose network as the API, partly due to hard-coded global container names and container conflicts.

Fix:
- removed fixed `container_name` entries
- recreated the stack
- removed the conflicting Postgres host port binding

After the fix, `/signup` returned:

```text
HTTP/1.1 201 CREATED
```

with:
```json
{
"message": "User registered.",
"warning": "Verification email could not be sent right now."
}
```

The warning is expected unless SMTP variables are configured.

## Environment Variables That Matter

For Docker networking, these values should use service names:

```env
POSTGRES_HOST=postgres
INFLUXDB_HOST=http://influxdb:8086
```

Required security/config values include:

```env
JWT_SECRET=...
FERNET_KEY=...
POSTGRES_DB=...
POSTGRES_USER=...
POSTGRES_PASSWORD=...
DOCKER_INFLUXDB_INIT_ORG=...
DOCKER_INFLUXDB_INIT_BUCKET=...
DOCKER_INFLUXDB_INIT_ADMIN_TOKEN=...
```

Optional SMTP settings for verification/reset emails:

```env
SMTP_HOST=...
SMTP_PORT=...
SMTP_FROM=...
SMTP_USER=...
SMTP_PASS=...
```

## How To Run

From:
```bash

open-gluco-server-microservice

```

run:
```bash

docker compose up -d --build

```

Check status:
```bash

docker compose ps

```

View logs:
```bash

docker compose logs -f api

docker compose logs -f cgm-scheduler

docker compose logs -f frontend

```

Stop everything:
```bash

docker compose down

```

## Verification Performed
The following checks were run:

```bash

python -m compileall open-gluco-server-microservice

```

and direct endpoint checks:
  
```bash
curl -I http://localhost:5173
curl -i http://localhost:8000/
curl -i -X POST http://localhost:8000/signup ...
```

Signup was verified to return `201 CREATED`. 

## Result
The backend is now split into:
- a web API service
- a background CGM scheduler service
- shared internal modules for security, session refresh, database access, InfluxDB access, and provider polling

This is not a full distributed rewrite of every domain into separate deployable services, but it establishes the core microservice-style boundary that mattered most: separating request/response API traffic from long-running CGM background work.