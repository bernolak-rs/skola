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

-- implicitny kurzor je cyklus for

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

**na teste vsetko co sme robili na cviceniach po 5 tyzden (aj xml a json, kolekcie nebudu, iba ako sucast xml a json alebo teoria), smernik tam su**
```sql
isdangling
```
**prakticky nebudu kolekcie, generovanie prikazov,**

# Prednaska 5
CLOB - textovy subor 
NCLOB - textovy subor s netypickymi znakmi
BLOB - obrazky
BFILE - subor ulozeny mimo databazy a v db je len smernik

INTERNE 
- viem vykonavat zmeny pomocou transakcii
- BLOB, CLOB, NCLOB - su medzi sebou priamo konvertovatelne
- varchar2 max 4 KB rozsireny rezim je 32 KB

EXTERNE
- velky subor typicky
- nevieme modifikovat data pomocou transakcii - readonly
- BFILE value je null

Directory
- sluzi na pristup k suboru mimo DBS
- vlastnikom je admin dbs ale ked ho vytvorime dostavame prava Read a Write

FOR UPDATE - da nam zamok na data ktore modifikujeme
- po update volame commit; aby sme odstranili zamok

Ked pouzijeme delete vymazeme fotku a smernik (zaznam v tabulke), ale ak mame odkaz v inej tablulke ostane nam dangling pointer, mozme pouzit EMPTY_BLOB(), ktory zrusi smernik, ak vymazem vsetky smerniky, tak vymaze objekt

LOBy nevieme porovnat efektivne, lebo musime prejst cele subory

Securefiles
- encrypcia
- deduplikacia
- kompresia

Sifrovacie kluce vzdy mimo databazy - do struktury waller
- ak by sme stratili pristup do databaze a potrebujeme ju obnovit


# Prednaska 6
Table Access Full
Dirty block pamate vznika, ked modifikujeme udaj v pamati a necommitneme

Typy zamkov -riadkovy:
- exkluzivny - modifikacia
- zdielany - select - v oracle neexistuje(to nam dovoli sa pozriet na zaznam ktory niekto modifikuje)

Commit:
1. find
2. load
3. lock
4. update
5. commit
6. unlock

UNDO - v databaze, kym je aktivna transakcia(moze byt aj dlhsie -> pouziva sa na AS OF query), rollback
REDO - v pamati, kym necommitnem. pouziva sa na commit zmien, pokial nastavime no logging, tak sa nam nastavia indexy na unusable a musime ich rebuildnut, inak nam klesne vykon

flashback (DDL) obnovi tabulku po commite -> pokazi to indexy a treba ich prebudovat.
```sql
flashback table os_udaje to timestamp(sysdate - 1);

alter index xxx rebuild;
```

nad primarnym klucom je vzdy index - je to strom(key, value) - B+ strom, jeho listy su spojone obojstrannym zretazenym listom a ma v nich data -> dobre sa zoraduje/prechadza

```sql
select meno, priezvisko
  from os_udaje
  where rod_cislo='123456/7890'
  where meno='Peter'
  where substr(meno, 1, 1)='P'
```

RC - identifikujeme podla PK 
ROWID - nepouzivame na indexovanie zaznamov, lebo pri exporte stratime cudzie kluce

RC, ROWID - pri where rod_cislo="123456/7890"
MENO, ROWID - pri where meno='Peter'
substr..., PRIEZVISKO, MENO - pri where substr(meno, 1, 1)='P'

vzdy chceme pouzit where z najmensim indexom, odstrani najvacsie mnozstvo nechcenych dat na zaciatku

hlbka b+ stromu s 2 milionmi je priblizne 5-6
null hodnoty nie su indexovane
chceme co najviac unikatnych hodnot

# Prednaska 7
Index

Reverzny index (123 -> 321) pomaha s vyvazovanim stromu, lebo ked vkladame sekvenciu tak by sa vsetko davalo za sebou ale ked otocime sa to rozhadze

ID in (123, 124, 125, 126) je rychlejsi alebo rovnaky ako (vykonava to paralelne)
ID between 123 and 126
ID >= 123 and ID <= 126

descending order pri viacerych atributoch v order by

plan vykonania sa cita od dola nahor

nas vlastny index nemusi byt pouzity aj ked vyzera, ze sa hodi na dotaz. napr ked ked vyhladavame rc, meno, priezvisko a mame taky index ale aj tak sa pouzije databazovy unique index, lebo je rychlejsi

Index range scan
Index unique scan
Flow index scan
Table access by index rowid

rozdiel medzi scan podla unique a vlastneho(m, p) je pri 15-20%

implicitne sa vytvaraju kluce pre vsetkych kandidatov PK v tabulke

constraint_type
- U - unique
- C - check constraint on table
- P - primary key

DBMS_METADATA package

invisible - nepouziva sa v selectoch, iba v insert, update, delete
unusable - nepouziva sa vobec
virtualny index - ma len hlavicku, nie je realne vytvoreny, ale pouziva sa na rozhodnutie, ci ho bude oprimalizator pouzivat

BitMap index
- malo roznych hodnot ale vela zaznamov

Index organized data
- Key (PK) + data
- data mozu mat overflow segment
Hash index
- podobne hash mape
- moc sa nepouziva, backwards compatibilita
- problem najst a menit hashovaciu funkciu, aby sa nam nepreplnil nejaky bucket

Table access full
index unique scan
index range scan
index full scan
Index fast full scan
iss

b+tree index nevie manazovat null values

nested loop - pouzijeme ked je tabulka mala
inak hash match alebo merge join(hlada match)

analiticke funckie:
- row_number
- rank
- dense_rank

agregacne funckie:
- count
- sum
- min
- max
- avg
- xml
- json
- listagg



