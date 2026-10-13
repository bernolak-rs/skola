# Prednaska 1
### Datove typy
- interval

*oracle ukazka*
```sql

select rod_cislo, meno, priezvisko, os_cislo, rocnik
	from os_udaje o join student s on (o.rod_cislo=s.rod_cislo)
	order by rocnik asc nulls first;

select o.rod_cislo, meno, priezvisko, os_cislo, rocnik
 from os_udaje o left join student s on (o.rod_cislo=s.rod_cislo)
  order by rocnik asc nulls first; -- nulls last
  
select o.rod_cislo, meno, priezvisko, os_cislo, rocnik
 from os_udaje o left join student s on (o.rod_cislo=s.rod_cislo)
   where rocnik=2 -- 18 rows
   order by rocnik asc nulls first; -- nulls last  
   

   
select o.rod_cislo, meno, priezvisko, os_cislo, rocnik
 from os_udaje o left join student s on (o.rod_cislo=s.rod_cislo
                                         and rocnik=2) 
   order by rocnik asc nulls first; -- nulls last     
   
select *
 from os_udaje natural join student;
 
 --------------------------
 create table konta
  as (select meno, priezvisko, 
        os_cislo, priezvisko||round(dbms_random.value(1,5)) as login
       from os_udaje join student using(rod_cislo)); 

drop table konta;
select * from konta;       

create user login identified by substr(meno, 1,1)|| substr(priezvisko, 1,1)||os_cislo;

select 'create user '|| login || ' identified by ' || substr(meno, 1,1)|| substr(priezvisko, 1,1)||os_cislo||';'
 from konta;
 
 
create user Kvet2 identified by MK200000;

-- anonymny blok
-- proc, funkcia

-- declare
-- begin
 -- exception
-- end

begin
 for i in (select 'create user '|| login || ' identified by ' || substr(meno, 1,1)|| substr(priezvisko, 1,1)||os_cislo as prikaz
 from konta)
  loop
   dbms_output.put_line(i.prikaz);
   -- execute immediate(i.prikaz);
  end loop;
end;
/

-- select into
-- kurzor - sekvencny

set serveroutput on; 

declare 
 cursor cur_prikaz is (select 'create user '|| login || ' identified by ' || substr(meno, 1,1)|| substr(priezvisko, 1,1)||os_cislo as prikaz
 from konta);
 hodnota varchar(500);

begin
 open cur_prikaz;
  loop
   fetch cur_prikaz into hodnota;
   exit when cur_prikaz%notfound;
   dbms_output.put_line(hodnota);
      -- execute immediate(hodnota);
  end loop;
 close cur_prikaz; 
end;
/

--------------------------------------------------
select meno, priezvisko, '19'||substr(rod_cislo, 1,2) as rok_narodenia from os_udaje 
 order by rok_narodenia desc
  fetch first 5 rows only ; --limit
  -- with ties
  -- offset

select * from
(select meno, priezvisko, '19'||substr(rod_cislo, 1,2) as rok_narodenia, 
       row_number() over(order by '19'||substr(rod_cislo, 1,2) desc) as RN
  from os_udaje)
   where RN<=3;  
   
select * from
(select meno, priezvisko, '19'||substr(rod_cislo, 1,2) as rok_narodenia, 
       rank() over(order by '19'||substr(rod_cislo, 1,2) desc) as RN
  from os_udaje)
   where RN<=10;     
   
select * from
(select meno, priezvisko, '19'||substr(rod_cislo, 1,2) as rok_narodenia, 
       dense_rank() over(order by '19'||substr(rod_cislo, 1,2) desc) as RN
  from os_udaje)
   where RN<=10;   
   
-------------------
select meno, priezvisko, count(distinct cis_predm) as pocet
 from os_udaje left join student using(rod_cislo)
               left join zap_predmety using(os_cislo)
  group by meno, priezvisko, rod_cislo
   order by pocet desc
   fetch first 3 rows only;   

select * 
from
(select meno, priezvisko, count(distinct cis_predm) as pocet, 
       row_number() over(order by count(distinct cis_predm) desc) as rn
 from os_udaje left join student using(rod_cislo)
               left join zap_predmety using(os_cislo)
  group by meno, priezvisko, rod_cislo)
   where rn<=3;   
   
select * 
from
(select meno, priezvisko, count(distinct cis_predm) as pocet, rocnik,
       rank() over(partition by rocnik order by count(distinct cis_predm) desc) as rn
 from os_udaje left join student using(rod_cislo)
               left join zap_predmety using(os_cislo)
  group by meno, priezvisko, rod_cislo, rocnik)
   where rn<=3
    order by rocnik, rn;   
   
   
   
       
       
select meno, priezvisko, count(distinct cis_predm) 
 from os_udaje left join student using(rod_cislo)
               left join zap_predmety using(os_cislo)
  group by meno, priezvisko, rod_cislo, cis_predm;        

```

### Analyticke funkcie
row_number() over
rank() over
denserank() over
partition by

# Prednaska 2
# ADBS -- Objekty a kolekcie (skrátené poznámky)
## 1. Objektový typ

Objektový typ má **špecifikáciu** (atribúty + deklarácie metód) a **TYPE
BODY** (implementáciu).

``` sql
CREATE TYPE t_osoba AS OBJECT (
id INTEGER,
meno VARCHAR2(30),
MEMBER FUNCTION vypis RETURN VARCHAR2
);
/
```

- `MEMBER` -- metóda objektu, pozná `SELF`.
- `STATIC` -- metóda typu, `SELF` nemá.
- `SELF` -- aktuálna inštancia objektu.

## 2. Konštruktory
**Implicitný konštruktor** vytvorí Oracle automaticky; parametre
zodpovedajú atribútom.

``` sql
t_osoba(1, 'Adam')
```
Explicitný:
``` sql
CONSTRUCTOR FUNCTION t_osoba(...) RETURN SELF AS RESULT
```
## 3. Dedičnosť

``` sql
CREATE TYPE t_student UNDER t_osoba (
rocnik INTEGER
);
/
```
  
- `UNDER` -- dedenie
- `NOT FINAL` -- z typu možno dediť
- `NOT INSTANTIABLE` -- abstraktný typ/metóda
- `OVERRIDING` -- prepísanie zdedenej metódy
  
## 4. Objektová tabuľka

``` sql
CREATE TABLE osoby_tab OF t_osoba (
PRIMARY KEY(id)
);
```

  
`VALUE(alias)` vráti celý objekt:
  
``` sql
SELECT VALUE(o) FROM osoby_tab o;
SELECT VALUE(o).meno FROM osoby_tab o;
SELECT VALUE(o).vypis() FROM osoby_tab o;
```


## 5. TREAT a IS OF
  
`TREAT` slúži na pretypovanie:
  
``` sql
TREAT(VALUE(a) AS t_osobne)
```
  
Kontrola typu:
  
``` sql
obj IS OF (t_auto) -- typ alebo jeho potomok
obj IS OF (ONLY t_auto) -- presne t_auto
```


## 6. REF a DEREF
  
- `REF(objekt)` → referencia na objekt
- `DEREF(referencia)` → objekt
  
``` text
objekt --REF--> referencia
referencia --DEREF--> objekt
```
  
## 7. MAP a ORDER
  
Slúžia na porovnávanie/triedenie objektov. Objektový typ môže mať
maximálne **jednu** triediacu metódu (`MAP` alebo `ORDER`).
  
Pri `ORDER`:
  
``` text
< 0 SELF je menší
= 0 rovnaké
> 0 SELF je väčší
```
  
# 8. Kolekcie
  
Typ Veľkosť SQL Riedka
-------------- -------------- ----- --------
Index-by neohraničená nie áno
Nested table neohraničená áno áno
VARRAY ohraničená áno nie
### Index-by

Iba PL/SQL:
``` sql
TYPE t_cisla IS TABLE OF INTEGER INDEX BY BINARY_INTEGER;
```
  
### Nested table

``` sql
CREATE TYPE t_cisla AS TABLE OF INTEGER;
/
```
Neohraničená, použiteľná v DB, po mazaní môže byť riedka.
### VARRAY
``` sql
CREATE TYPE t_cisla AS VARRAY(10) OF INTEGER;
/
```
Má pevné maximum, je hustá a zachováva poradie.
## 9. Metódy kolekcií
``` text
COUNT počet prvkov
LIMIT maximálny počet
FIRST/LAST prvý/posledný index
NEXT/PRIOR ďalší/predchádzajúci index
EXISTS(i) či index existuje
EXTEND rozšírenie
TRIM odstránenie z konca
DELETE vymazanie
```

Pre riedku kolekciu:
``` sql
i := c.FIRST;
WHILE i IS NOT NULL LOOP
DBMS_OUTPUT.PUT_LINE(c(i));
i := c.NEXT(i);
END LOOP;
```
## 10. BULK COLLECT
Načíta viac SQL riadkov naraz do kolekcie:
``` sql
SELECT *
	BULK COLLECT INTO studenti
	FROM student;
```

# Keywords ťahák

``` text
MEMBER metóda objektu, SELF
STATIC metóda typu, bez SELF
NOT FINAL možno dediť
NOT INSTANTIABLE abstraktný
UNDER dedenie
OVERRIDING prepísanie metódy

VALUE celý objekt
TREAT pretypovanie
IS OF kontrola typu
ONLY presný typ
REF objekt -> referencia
DEREF referencia -> objekt

Index-by PL/SQL, neohraničená, riedka
Nested table SQL + PL/SQL, neohraničená
VARRAY SQL + PL/SQL, ohraničená, hustá
```
# Prednaska 3
element
```xml
<meno>Adam</meno> 
```
atribut
```xml
<meno krstne="Adam"></meno> 
```

XML je case sensitive
EXISTSNODE vrati 1 ak existuje a 0 ak nie
EXTRACT vrati XML elementy aj s tagmi
XMLROOT formatovanie
EXTRACTVALUE vrati XML hodnoty bez tagov
LIKE 
TRIM

```sql
-- Získanie atribútu cez „@“

select value(s)
	from studenti_xml s
	where EXTRACTVALUE(value(s),'/student/@oc')=500423;
```

EXTRACTVALUE by malo vratit vzdy jeden vysledok, musime viacere zaobalit do root elementu

UPDATEXML

SYS_XMLGen()
XMLElement()
XMLAttributes
XMLForest
XMLAgg - agreguje elementy do pola (s group by)

# Prednaska 4
## JSON
- JS object notation
- Key-Value pair
- jednoduchsi ako XML
- lahko citatelny pre ludi
- pouzivany na posielanie dat medzi serverom a klientom
- narozdiel od RDBs sa indexuje pole od 0
## JSON v Oracle
```sql
Create table books_json(id integer, doc CLOB,
	constraint valid_json_cons CHECK (doc is json));

select b.doc.Title
from books_json b;

-- it is a string format -> case sensitive!
-- we need to use b alias otherwise doesn't work
```

**JSON_EXISTS** - umozni nam pouzit hodnotu JSONu ako podmienku
```sql
select b.doc.Title
	from books_json b
	where json_exists(b.doc, '$.Detail.Status');
	
select b.doc.Title
	from books_json b
	where json_exists(b.doc, '$.Detail?(@.Status=="sold-out")');
```

Definened parameter
- pri selecte sa vytvori plan vykonania, ktory sa ulozi do operacnej pamate a pri pouziti takehoto parametru nemusime vkuse vytvarat novy plan vykonania ale odkazovat sa na povodny hash ID
```sql
select b.doc.Title
	from books_json b
	where json_exists(b.doc, '$.Detail?(@.Publication_year==$PY)‘
					passing 2020 as "PY");
```

**JSON_VALUE**
- vracia skalarnu hodnotu - pokial nie je skalar vrati null
```sql
select b.doc
	from books_json b
	where json_value(b.doc, '$.Detail.In_stock' returning number)=0;
```

JSON_TABLE
- nacita data z jsonu do tabulky
- lateralny join
- vracia skalar - inak null (skalar nemoze byt pole, json, objekt)
- nested - sluzi aby sme z pola vytvorili relacnu hodnotu

## Generovanie JSON z RLDBs
**JSON_OBJECT**
- vytvori JSON objekt
```sql
select json_object('name' value meno || ' ' || priezvisko,
	'student_id' value os_cislo
	ABSENT ON NULL)
-- absent on null nam vynecha null atribut namiesto toho aby ho nastavil na null ako left join

select json_object(*) -- nemusime select json_object(meno, priezvisko)
from os_udaje
left join student using(rod_cislo);
```

**JSON_ARRAY**
- vytvori pole elementov rovnakeho typu
```sql
json_array(min(vysledok), max(vysledok))
```
vrati ["C", "E"]
**JSON_OBJECTAGG**
- vrati JSON
- agreguje nam objekty
**JSON_ARRAYAGG**
- vrati JSON hodnoty
- Agregacna!!

## Index
```sql
create index ind_json
	on books_json(JSON_value(doc, '$.Title'));

Select b.doc
	from books_json b
	where JSON_value(doc, '$.Title')= 'Databázové systémy';
```
- znizi naklady na prehliadku

## Update
```sql
update books_json b
  set b.doc=json_mergepatch(b.doc,
  '{"Detail":
	  {
	  "Publisher": "Oracle Press",
	  "Publication_year": 2021,
	  "ISBN": "xxx",
	  "Language": "English",
	  "Pages": 520,
	  "Price": 50
	  }
}')
where b.doc.Title='APEX';
```

JSON_SERIALIZE
```sql
select json_serialize(b.doc pretty)
	from books_json b
	where b.doc.Title='APEX';
```
# Kolekcie
- su zoradene
VARRAY
Index By Table
Nested Table

**na teste vsetko co sme robili na cviceniach po 5 tyzden (aj xml a json, kolekcie nebudu, iba ako sucast xml a json alebo teoria)**