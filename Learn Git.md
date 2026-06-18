git log -n 5 - vypisat poslednych 5 commitov
--decorate=<no, full, short>
--oneline
## Porcelain vs Plumbing

## Global vs Local

## Branching
- `git branch <name>` - create branch `<name>`
- `git switch <name>` - switch to branch `<name>`
- `git switch -c <name>` - create and switch to branch `<name>`
- `git checkout <name>` - old way to switch branches

git log --oneline --graph --all 
git log --oneline --graph --parents
git log --oneline --decorate --graph --parents
- `--graph` - ascii representation of git

## Git merge
- go to main/master branch
- `git merge <merging branch>`
git log --oneline --decorate --graph --parents

`git switch -c update_dune <COMMITHASH>` - creating branch on selecting node with COMMITHASH
`git rebase main` - from new branch rebase onto main

we should not rebase public branch onto anything.
its okay to rebase my own branch onto other branches including main

git reset --soft 7cef054
git reset --hard COMMITHASH

--hard to discard all the changes - return to before editing
--soft returns to before commit

`git branch -d <branch_name>`
- delete branch
## Remote
`git remote add <name> <url/path>`
git fetch - downloads copies from .git/objects from remote to current

`git log <remote>/<branch>`

`git merge <remote>/<branch>`

`git push origin <localbranch>:<remotebranch>`

Run `git config set pull.rebase false` to make sure git will merge on a pull

## My Solo Workflow

When I'm working by myself, I usually stick to a single branch, `main`. I mostly use Git on solo projects to keep a backup remotely and to keep a history of my changes. I only rarely use separate branches.

1. Make changes to files
2. `git add .` (or `git add <files>` if I only want to add specific files)
3. `git commit -m "a message describing the changes"`
4. `git push origin main`

It really is that simple for most solo work. `git log`, `git reset`, and some others are, of course, useful from time to time, but the above is the core of what I do day-to-day.

# My Team Workflow

When you're working with a _team_, Git gets a bit more involved (and we'll cover more of this in part 2 of this course). Here's what I do:

- Update my local `main` branch with `git pull origin main`
- Checkout a new branch for the changes I want to make with `git switch -c <branchname>`
- Make changes to files
- `git add .`
- `git commit -m "a message describing the changes"`
- `git push origin <branchname>` (I push to the _new_ branch name, not `main`)
- Open a [pull request](https://docs.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests) on GitHub to merge my changes into `main`
- Ask a team member to review my pull request
- Once approved, click the "Merge" button on GitHub to merge my changes into `main`
- Delete my feature branch, and repeat with a new branch for the next set of changes

## Gitignore
Wildcard - `*.txt`
Rooted Pattern - `/main.py` - ignores only main.py files in root dir
Negetation - order matters
	`*.txt  
	!/important.txt`
Comments - `#`
