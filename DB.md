# Databazove Systemy

## Basics

DBS (Databázový systém) = DB (databáza) + SRBD (System riadenia bázy dát)

Databázový systém tvoria:
- Dáta
- Hardvér
- Softvér
- Používatelia

DML (Data Manipulation Language) - Select, Update, Delete, Insert  
DDL (Data Definition Language) - Create, Alter, Drop - automaticky robi commit  (aj pri triggri)
DCL (Data Control Language) - Grant, Revoke
TCL (Transaction Control Language) - Commit, Rollback

SQL je neproceduralny jazyk - Zjednodušene povedané, v **neprocedurálnom jazyku** nehovoríte počítaču, **AKO** má niečo urobiť (krok po kroku), ale hovoríte mu, **ČO** má byť výsledkom.

### Datove typy

- `VARCHAR(5)` - ako keby string s variabilnou dlzkou (max 5 znakov)
- `VARCHAR2(5)` - novsia verzia `VARCHAR`
- `CHAR(5)` - ako keby string s pevne danou dlzkou 5

- `NUMBER(x, y)` - ciselna hodnota
  - `x` - maximalny pocet znakov ktore bude mat cislo
  - `y` - pocet znakov za desatinnou ciarkou
  - `NUMBER(10, 0) = NUMBER(10) = INTEGER(10)`
- `SMALLINT`, `FLOAT`, `DOUBLE`

- `DATE` - datum
- `TIME` - cas
- `DATETIME` - datum aj cas
- `TIMESTAMP(9)` - presnejsie ako sekundy (`9 = sekunda *`$10^{-9}$ )
- Oracle ma iba `DATE`, ktore je akokeby `DATETIME`

Formaty datumov

- `YYYY` / `YY` / `RRRR` / `RR`
- `MM`
- `DD`
- `HH` (12-hodinovy cas) / `HH24` (24-hodinovy cas)
- `MI`
- `SS`

| Skratka | Meaning                   | Popis                                                                                                                       |
| ------- | ------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| `NN`    | Not Null                  | Hodnota musi byt zadana, nemoze byt Null                                                                                    |
| `PK`    | Primary Key               | unikatny a minimalny kluc v tabulke, je `NN`, **je najviac jeden** (moze byt kompozitny - skladat sa z viacerych atributov) |
| `FK`    | Foreign Key               | Odkazuje na primarny kluc z inej tabulky, moze byt aj Null                                                                  |
| `PFK`   | Primary Foreign Key       | Primary + Foreign                                                                                                           |
| `KPK`   | Kandidat primarneho kluca | Nieco, co teoreticky moze byt tiez primarny kluc, Unikatny,not null, minimalny                                              |
| SK      | Super kluc                | Unikatny,not null, nie minimalny, set atributov, urcujucich cely zaznam                                                     |
| AK      | Alternativny kluc         | Je KPK ale nie je PK                                                                                                        |

## SELECT

```sql
SELECT * FROM studenti  -- vypise vsetko o vsetkych zaznamoch z tabulky studenti

SELECT meno, priezvisko, os_cislo FROM studenti  -- vypise iba meno, priezvisko a osobne cislo z tabulky studenti

SELECT meno, priezvisko
FROM studenti
WHERE meno='Peter'
-- vypise vsetkych Petrov z tabulky

SELECT meno AS m FROM studenti  -- alias, namiesto "meno" sa odteraz pouziva "m"

SELECT meno, rocnik+1 AS novy_rocnik FROM studenti
```

Praca s `Null`

```sql
-- NEMOZEME SPRAVIT TOTO
SELECT * FROM students WHERE ulica!=null

-- Treba spravit toto
SELECT * FROM students WHERE ulica IS NOT null
```

Zliepanie viac tabuliek dokopy - `JOIN`

```sql
SELECT *
FROM osobne_udaje
JOIN student
USING (rodne_cislo)
-- spoji tabulky "osobne_udaje" a "student" podla kluca "rodne_cislo"
-- toto sa da pouzit iba ak obidve tabulky obsahuju "rodne_cislo", teda PK a FK su rovnake

SELECT *
FROM osobne_udaje
JOIN student
ON (osobne_udaje.cislo = student.cislo)
```

Popis tabulky - `description`

```sql
DESCRIBE osobne_udaje
DESC osobne_udaje
```

`Like` - pouzitie akoze wildcards spolu so znakmi `%` a `_`

```sql
-- Znak % specifikuje lubovolny pocet znakov
SELECT *
FROM osobne_udaje
WHERE meno LIKE "%a%"  -- vsetci co maju v mene "a"

-- Znak _ specifikuje jeden znak
SELECT *
FROM osobne_udaje
WHERE meno LIKE "_a%"  -- vsetci co maju na druhom mieste "a"
```

`Dual` - specialna tabulka s jednym zaznamom (dummy)

```sql
-- Ak by sme spustili nasledovny prikaz, vratilo by nam to tolko odpovedi kolko je zaznamov v tabulke
SELECT 1+1 FROM studenti

-- Ak chceme vratit iba jednu odpoved, mozeme pouzit tabulku dual, v ktorej je iba jeden zaznam
SELECT 1+1 FROM dual

```

Datumy

```sql
SELECT to_date('05-01-2025', 'DD-MM-YYYY') FROM dual; -- formatovanie datumu

SELECT to_char(sysdate, 'DD.MM.YY') FROM dual;  -- dnesny datum

extract(year from dat) = ...
trunc(dat)=trunc(sysdate)

SELECT sysdate+1 FROM dual  -- dnesny datum + 1 den
SELECT add_months(sysdate, 2)  -- za 2 mesiace
```

Nejake selectiky

```sql
select meno,priezvisko
    from os_udaje join student using (rod_cislo)
    where rocnik=2;

select meno,priezvisko
    from os_udaje
    where rod_cislo IN (select rod_cislo from student where rocnik=2);
```

# Anti join

```sql
select meno,priezvisko
    from os_udaje
    where rod_cislo not in (select rod_cislo from student);

select meno,priezvisko
    from os_udaje
    where rod_cislo in (select rod_cislo from student where rocnik is null);
    -- cez IN sa neda spravit, vnoreny select nikdy nic nevrati lebo rocnik je NN

select meno,priezvisko
    from os_udaje o
    where NOT EXISTS
    (select 'x' from student s where s.rod_cislo=o.rod_cislo);
    -- univerzalnejsie riesenie - mozeme dat viac podmienok
```

Toto bude dolezite pri INSERT, DELETE a UPDATE

# Insert
- Prikaz Insert ma dve formy:
	- VALUES - vlozenie LEN JEDNEHO riadku pomocou priameho zadania novych hodnot
	- SELECT - vlozenie viacerych riadkov predpripravenych dat z pomocnych tabuliek.

```sql
INSERT into os_udaje
 VALUES ('561224/1234', 'Michal', 'Kvet', null, null, null);
 
 INSERT into os_udaje (meno, priezvisko, rod_cislo) 
	 (select meno, priezvisko, rod_cislo FROM nove_osoby);

-- Prepis NOT EXISTS
INSERT into os_udaje(meno, priezvisko, rod_cislo)
 SELECT meno, priezvisko, rod_cislo
  FROM priklad.db2.os_udaje
   WHERE rod_cislo not in (SELECT rod_cislo FROM os_udaje);

desc priklad_db2.os_udaje;
```

# UPDATE

-  Prıkazom Update mozeme zmenit viacere hodnoty LEN jednej tabulky
-  Ak je podmienka na zmenu riadkov z inej tabulky, je potrebne pouzit vnoreny Select
-  Ak mame nove hodnoty v pomocnej tabulke, je potrebny select prıkaz v casti SET. Aby nedoslo k strate dat, je potrebne doplnit aj podmienku, aby boli menene len riadky, ku ktorym mame hodnoty v pomocnej tabulke.


```sql
UPDATE student
SET ukoncenie=sysdate,
	stav='V'
 WHERE os_cislo=500424

UPDATE student
 SET ukoncenie=sysdate
  WHERE exists (
   SELECT 'x' FROM os_udaje
    WHERE os_udaje.rod_cislo=student.rod_cislo
	    AND substr(meno, 1, 1)='M'
  );
  
UPDATE student
 SET ukoncenie=sysdate
  WHERE rod_cislo NOT IN (SELECT
						   FROM os_udaje
						    WHERE ulica IS null);
						    
UPDATE zap_predmety zp
 SET ects=(select ects
			from predmet_bod pb
			 where zp.skrok=pb.skrok
			  and zp.cis_predm=pb.cis_predm)
  WHERE EXISTS (select 'x' --SEMI JOIN - IN alebo EXIST
				 from predmet_bod pb2
				  where
				   zp.skrok=pb2.skrok
				    and zp.cis_predm=pb2.cis_predm);
```

# DELETE

- Sluzi na vymazanie hodnot LEN z jednej tabulky
- Ak je podmienka z inej tabulky, musıme pouzit vnoreny select

```sql
-- Chceme vymazat ziakov 2. rocnika => najskor musime vymazat zaznamy na ktore sa odkazuju
DELETE FROM zap_predmety
 WHERE os_cislo in (
		select os_cislo 
		 from zap_predmety
		  where rocnik=2
 );

DELETE FROM student
 where rocnik=2;
 
-- Vymazte osoby, ktore maju v krstnom mene pismeno "a"
delete from zap_predmety
 where exists(select 'x' from student s
			   where exists (select 'x'
			    from os_udaje o
			     where o.rod_cislo=s.rod_cislo
			      and meno like '%a%')
				and s.os_cislo=zap_predmety.os_cislo);
				
delete from student
 where rod_cislo IN (select rod_cislo
					  from os_udaje
					   where lower(meno) like '%a%');

delete from os_udaje
 where lower(meno) like '%a%';
```

```sql
--Chceme zmenit rod_cislo studenta
--Najskor dame insert noveho rod_cisla
insert into os_udaje
 select '841108/3456', meno, priezvisko, ulica, psc, obec
  from os_udaje
   where rod_cislo='841108/3456';

update student
 set rod_cislo='841108/3456'
  where rod_cislo='841106/3456';
  
delete from os_udaje
 where rod_cislo='841106/3456';
 
```

## Vztahy medzi tabulkami
Typ vztahu
----- Neidentifikacny vztah (ciarkovana ciara) - FK
____Identifikacny vztah (plna ciara) - PFK

Kardinalita vztahu
- 1:1 - Jeden student by mal prave jedny osobne udaje a naopak
- 1:N - Student - Zap_predmety, os_udaje - student
- M:N - musime vytvorit novu tabulku - asociativna entita, ktora bude mat kompozitny primarny kluc zostaveny z prepojenych tabuliek - napr. zap_predmety zo vztahu student - predmet - vytvara identifikacny vztah
- 0 - znaci sa ako prazdna gulicka v diagrame o
- 1- sa znaci ciarkou |
- N - sa znaci rozvetvenim

M:N vznika asociacna entita

```sql
alter table prvok
	add pocet number
	constraint pocet_rozsah check (pocet >= 10 and pocet <= 50);
	
alter table prvok 
	add pocet number;
alter table prvok
	add constraint pocet_rozsah
	check (pocet between 10 and 50);
	
alter table zap_predmety
  add constraint znamka
  check (vysledok='A' or vysledok='B' or vysledok='C' or vysledok='D' or
  vysledok='E' or vysledok='F');
  
alter table zap_predmety
  add constraint znamka
  check (vysledok in ('A', 'B', 'C', 'D', 'E', 'F'));
```

Clenstvo
- povinne - FK - NN
- nepovinne - FK - moze byt null - Hrac - Klub (hrac nie je v klube id_klub is null)

Rekurzivny vztah - Hierarchia
- neidentifikacny - nemozme mat nekonecno zaznamov
- osoba ma matku ako FK ale matka je tiez osoba a ma matku
- 1:1 alebo 1:N

Viacnasobny vztah
- napr bakalarska praca - osoba - bc - vztahy veduci, oponent, predseda
- napr let - letisko, letisko_pril, letisko_odlet -> 2 vztahy medzi tabulkami

```sql
select s.meno, s.priezvisko, v.meno, v.priezvisko, o.meno, o.priezvisko
	from bc join osoba v on (veduci=v.oc)
		join osoba o on (oponent=o.oc)
			join osoba s on(student=s.oc)

```

Typy atributov
- atomicke
- neatomicke - rod_cislo, studijna_skupina
	- skupinove (strukturovane)
	- viachodnotove atributy

Identifikacny kluc - PK alebo Kandidat PK

ZAP_PR(ŠTUDENT:(0,m),PREDMET:(0,60))
Konkrétny študent môže mať počas celého štúdia zapísaných maximálne 60 predmetov, pričom počet študentov na daný predmet počas existencie fakulty je neobmedzeny

Linearny zapis
OU(#RC, M, P)
R(OU, ST)
ST(#OC, RC, Rocnik, ...)

```sql
--DDL - Create, Alter, Drop - implicitne volaju commit
create table os_udaje
 (rod_cislo char(11) priamary key,
  meno varchar(20) not null,
  priezvisko varchar(20) not null,
  obec varchar(20)
 );
 
desc os_udaje;

create table st_odbory
 (id_odboru integer,
  id_zamerania integer,
  popis varchar(20),
  primary key(id_odboru, id_zamerania) -- kompozitny PK
 );
 
 create table st_odbory
 (id_odboru integer,
  id_zamerania integer,
  popis varchar(20)
 );
 
 alter table st_odbory add primary key(id_odboru, id_zamerania); -- kompozitny PK
-- ALTER
 -- ADD
 -- MODIFY
 -- DROP 
alter table os_udaje add psc char(5);
alter table os_udaje modify psc not null;
alter table os_udaje drop;

-- Vytvaranie NOT null atributu
alter table student add rocnik char(1);
update student set rocnik='x';
alter table student modify rocnik not null;

-- Mozme vkladat len %2 cisla
create table T(id integer);
alter table T add primary key(id);
alter table T add check(mod(id,2)=0);
insert into t values(3); -- nepojde
```


# Cvicenie 4
Dokoncil som 4. hierarchiu
```sql
--- Na precvicenie DDL z cvika
SELECT * FROM soc_poistovna_bc.p_osoba;
```


# Prednaska 4

```sql
create table st_odbory
 (id_odboru integer,
  id_zamerania integer,
  popis varchar(20)
 );
 
alter table st_odbory add primary key(id_odboru, id_zamerania); -- kompozitny PK

alter table osoba modify meno not null;

alter table osoba drop column mesto;

alter table osoba add email varchar(20) not null;

-- Email musi obsahovat zavinac
alter table osoba add constraint email_kontrola
	check(email like '%@%');
	
alter table osoba modify ...

alter table osoba drop constraint email_kontrola;
select * from user_constraints; -- vypisat constraints
alter table osoba drop constraint SYS_C0025863; -- vymazat bez pomocou ID z minuleho prikazu
------------------------
create table st
 (os_cislo integer,
  rocnik integer,
  datum_zapisu date,
  constraint pk_st primary key(os_cislo)
);
alter table st add rc char(11);
alter table st add foreign key(rc) reference osoba(rod_cislo);

desc st; -- vypise st tabulku
-- typ - neidentifikacny. vztah
-- kardinalita - 1:N
-- clenstvo - null - nepovinne

-- typ - identifikacny
alter table st drop constraint pk_st;
alter table st add primary key(os_cislo, rc); -- CHYBA, NIE JE MINIMALNY TERAZ

-- kardinalita - 1:1
alter table st modify rc unique;

-- clenstvo - povinne
alter table st modify rc not null;

-- Chceme zmenit naspat na neidentifikacny
select constraint_name
  from user_constraints
   where table_name='ST'
    and constraint_type='P' -- dostaneme sys ID pre constraint na PK, lebo sme ho nepomenovali

alter table st drop constraint SYS_C001244;

alter table st add primary key(os_cislo);

create table asoc_tab
(
	rc char(11),
	oc integer,
	hodnota integer check(mod(hodnota,2)=0),
	primary key(rc, oc);
);

alter table asoc_table add foreign key(rc) references osoba(rod_cislo);
alter table asoc_tab add constraint FK_st foreign key(oc) references st(os_cislo);
-- M:N - vznikla asoc. entita, 1:N - 2x
--------------------------------------------------

desc student;

create sequence sekv;

select sekv.nextval
 from dual;
 
select sekv.currval
 from dual;
 
-- sekvencia sa nevrati rollbackom
alter sequence sekv increment by 2;
alter sequence sekv cycle;
alter sequence sekv maxvalue 100;
drop sequence sekv;
------------------------------------------
create sequence sekv
	start with 10
	 minvalue 5
	 maxvalue 20
	 increament by 2;
	 
alter sequence sekv cycle cache 2; -- CACHE nam nacita dany pocet hodnot sekvencie aby sme nemuseli frust nacitavat
select sekv.nextval from dual;
----------------------------------------------
-- DCL - Data control language
-- GRANT - pridanie pristupoveho prava
-- REVOKE - odobranie pristupoveho prava
-- musime mat prava ne udelovanie a odoberanie prav
grant XXXXXXXXX to XXXXXXXXXXX;

grant select, update on st to PASTIERIK2; -- pridame select na tabulku 'st' kolegovi
grant ALL on st to PASTIERIK2; -- ALL - select, update, delete, insert
grant create table to pastierik2;
grant select on st to PUBLIC; -- VSETCI mozu pouzivat select

revoke select, update, delete, insert on st from pastierik2;
revoke select on st from kvet3; -- NEPOJDE, majitel tabuliek ma vzdy prava

grant select on st to pastierik2 with grant option; -- objektove prava, moze aj dalej udelovat a odoberat prava na select
grant select on st to pastierik2 with ADMIN option; -- objektove prava 

select * from kvet3.st; -- kolega ale musi pouzivat plne kvalifikovane meno


```

# Cvicenie 5

Na teste bude aj DDL

```sql
--Uloha 2
SELECT meno, priezvisko, MOD(substr(rod_cislo, 3, 2), 50) mesiac_narodenia
  from p_osoba
  where MOD(substr(rod_cislo, 3, 2), 50) = to_char(add_months(sysdate, -1), 'MM')
  order by mesiac_narodenia;
-- NEMOZME POUZIT ALIAS vo where lebo sa vykona pred definiciou aliasu v selecte

SELECT TRUNC(add_months(sysdate, -1), 'MM')
  from dual;

select meno, priezvisko
  from p_osoba o1
  where exists(
	  select 'x'
	  from p_osoba o2
	  where o1.meno = o2.meno and
		  o1.priezvisko = o2.priezvisko and
		  o1.rod_cislo != o2.rod_cislo
  )
  order by o1.priezvisko, o1.meno;
```

# Prednaska 5 - Opakovanie, Agregacne funkcie
NOT IN nepouzivat pri kompozitnom PK

```sql
select os_cislo, meno, priezvisko
  from os_udaje join student using (rod_cislo)
    where os_cislo in (
        select os_cislo
          from zap_predmety
            where vysledok='A'
    );-- vrati vsetky s Ackom

select os_cislo, meno, priezvisko
  from os_udaje join student using (rod_cislo)
    where os_cislo not in (
        select os_cislo
          from zap_predmety
            where vysledok<>'A'
    );-- vrati iba cistych Ackarov
    
-- ROD_CISLO - PK - os_udaje
-- ROD_CISLO - FK - student

-- FK sa odkazuje na existujucu hodnotu PK
select * from os_udaje;

insert into os_udaje
 select '841106/xxxx', meno, priezvisko, ulica, psc, obec from os_udaje
   where rod_cislo='841106/3456';
   
update student set rod_cislo='841106/xxxx' where rod_cislo='841106/3456';

delete from os_udaje where rod_cislo='841106/3456';

rollback;

-- soc poistovna
insert into poberatel
  select -id_poberatela, rod_cislo, id_typu, perc_vyj, dat_od, dat_do
    from poberatel;
    
update p_prispevky
  set id_poberatel=-id_poberatel;
  
delete from p_poberatel
  where id_poberatela > 0;
  
--DU vyber osobu soc. poistovna a zmen jej rod_cislo

select meno, priezvisko, os_cislo
  from os_udaje join student using(rod_cislo)
    where os_cislo is null;
    
select meno, priezvisko, os_cislo
  from os_udaje join student using(rod_cislo)
    where os_cislo is not null;
    
select * from student;

-- INNER JOIN - join
-- SEMI JOIN - IN, EXISTS
-- ANTI JOIN - NOT IN, NOT EXISTS

-- OUTER JOIN

select meno, priezvisko, os_cislo
  from os_udaje left OUTER join student using(rod_cislo)
    order by os_cislo nulls first;
    
select meno, priezvisko, os_cislo
  from os_udaje right OUTER join student using(rod_cislo)
    order by os_cislo nulls first; -- toto vrati to iste ako inner join kvoli FK
    
-- FULL JOIN
select *
  from p_platitel FULL join p_poistenie using(id_platitela);
  
select *
  from p_platitel FULL join p_poistenie on
    (p_platitel.id_platitela=p_poistenie.id_platitela)
      order by p_platitel.id_platitela nulls first;
      
-- zbytocny join, da sa to cez not exists alebo not in (anti join)
select meno, priezvisko
  from os_udaje left join student using(rod_cislo)
    where os_cislo is null;
    
-- lepsie spravit takto
select meno, priezvisko
  from os_udaje os
    where not exists (
        select 'x' from student st
          where os.rod_cislo=st.rod_cislo
    );
    
select meno, priezvisko
  from os_udaje
    where rod_cislo not in (
        select rod_cislo from student
    );
```

## Agregacne funkcie
MIN
MAX
SUM
COUNT
- count(*) - spocita riadky
AVG
- ignoruju NULL hodnoty napr. 
V group by musime mat atributy ktore mame v selecte, cize to co napiseme za select, to musi by v group by
```sql
select count(dat_zapisu) from student; -- moze byt NULL a tie ignoruje
```
```sql
select rocnik, count(*)
  from student
    group by ROCNIK;
    
select meno, priezvisko, count(*) as pocet
  from os_udaje join student using (rod_cislo)
    group by meno, priezvisko, rod_cislo;
    
select meno, priezvisko, count(os_cislo) as pocet
  from os_udaje left join student using (rod_cislo)
    group by meno, priezvisko, rod_cislo
      order by priezvisko, meno;
      
select meno, priezvisko, os_cislo, count(distinct cis_predm), count(cis_predm)
  from os_udaje join student using (rod_cislo)
                join zap_predmety using (os_cislo)
    group by meno, priezvisko, os_cislo;
    
select meno, priezvisko, count(os_cislo) as pocet
  from os_udaje left join student using (rod_cislo)
    where count(os_cislo)>1 -- ZLE!!!!
    group by meno, priezvisko, rod_cislo
      order by priezvisko, meno;
      
select meno, priezvisko, count(os_cislo) as pocet
  from os_udaje left join student using (rod_cislo)
    group by meno, priezvisko, rod_cislo
      having count(os_cislo)>1;


select meno, priezvisko, count(os_cislo) as pocet
  from os_udaje left join student using (rod_cislo)
                     join zap_predmety using (os_cislo)
    group by meno, priezvisko, rod_cislo;


    
select meno, priezvisko, count(os_cislo) as pocet
  from os_udaje left join student using (rod_cislo)
                     join zap_predmety using (os_cislo)
    group by meno, priezvisko, rod_cislo
      having sum(ects)=(select max(sum(ects))
                            from zap_predmety
                              group by os_cislo);
                              
    
select meno, priezvisko, count(os_cislo) as pocet
  from os_udaje left join student using (rod_cislo)
                     join zap_predmety using (os_cislo)
    group by meno, priezvisko, rod_cislo
      having sum(ects)=(select max(pocet_kreditov)
                          from (select sum(ects) as pocet_kreditov
                            from zap_predmety
                              group by os_cislo));
                              

select meno, priezvisko, count(distinct cis_predm) -- pocet predmetov
  from ucitel join zap_predmety on (ucitel.os_cislo=prednasajuci)
    group by meno, priezvisko, ucitel.os_cislo;
                              
select meno, priezvisko, count(distinct cis_predm)
  from ucitel join zap_predmety on (ucitel.os_cislo=prednasajuci)
    group by meno, priezvisko, ucitel.os_cislo
      having count(distinct cis_predm) = (select max(count(distinct cis_predm))
                                            from zap_predmety
                                              group by prednasajuci); -- ucitel s najviac predmetmi

                       
select os_cislo, cis_predm, zp.skrok, zp.ects, pb.ects
  from zap_predmety zp join predmet using(cis_predm)
                    join predmet_bod pb using(cis_predm)
    where zp.skrok=pb.skrok
      and zp.ects <> pb.ects;
      
                
select os_cislo, cis_predm, zp.skrok, zp.ects, pb.ects
  from zap_predmety zp join predmet_bod pb using(cis_predm)
    where zp.ects <> pb.ects;

--vcerajsok
select sysdate-1 from dual;

select to_char(sysdate-1, 'DD.MM.YYYY')
  from dual;

select to_char(trunc(sysdate, 'MM') - 1, 'DD.MM.YYYY')
  from dual;

DAT between (sysdate -2) and sysdate;

dat +30
add_months(sysdate, 1)

months_between() --mesiace medzi datumami

select dat_od, dat_do, dat_do-dat_od pocet_dni, 
  months_between(dat_do, dat_od) pocet_mesiacov
  months_between(dat_do, dat_od) / 12 pocet_rokov
  from p_poberatel
    where dat_do is not null;

select to_date('19'||substr(rod_cislo, 1, 2)||'.' ||mod(substr(rod_cislo, 3, 2), 50)||'.'
	||substr(rod_cislo, 5,2), 'YYYY.MM.DD')
  from os_udaje;
  
substr(datum, 1, 2); -- NIKDY
select to_char(sysdate, 'MM') from dual;

```
NA TESTE BUDE -
select, insert, update, delete
antijoin, semijoin
prava
ddl
VSETKO CO SME MALI ODCVICENE DOTERAZ

# Prednaska 6 - PL/SQL - Procedury, Funkcie, Triggre
- nepomenovane bloky prikazov
- pomenovane bloky prikazov - procedury, funkcie
- triggre:
	- viazu sa na DML
	- [DEKLARACNA CAST] begin TELO[exception handler(viaze sa na telo)] end; / (terminator)
- v sql nemame procedury(nevracaju navratovu hodnotu)
SELECT x INTO y - musi vratit 1 zaznam 

```sql
v_priezvisko os_udaje.priezvisko%type --da rovnaky typ ako os_udaje.priezvisko
os_udaje%rowtype

premenna := vyraz; --priradenie
v_datum := sysdate;
select sysdate into v_datum from dual; --musi vratit prave 1 riadok inak CHYBA

select f1,f2 into v_f1, v_f2 from dual;

v_osoba os_udaje%rowtype;
select * into v_osoba
 from os_udaje
  where rod_cislo = '123456/1234';

declare
 v_prem varchar(20);
 v_cislo integer:=1;
begin
 v_prem:=case when v_cislo=1 then 'jednotka'
              when v_cislo=2 then 'dvojka'
              end;            --KED NENAJDE CASE TAK NASTAVI NA NULL (v ORACLE je null="")
   dbms_output.put_line(v_prem);
end;
/

CYKLY loop, while, for
begin
 for i in 1..5
  loop
   dbms_output.put_line(i);
  end loop;
end;
/

begin
 for i in reverse 1..5
  loop
   dbms_output.put_line(i);
  end loop;
end;
/

set serveroutput on; --aby sme mohli vidiet output

select to_char(sysdate, 'DD.MM.YYYY HH.MI.SS') from dual;
select to_char(sysdate, 'month') from dual;


-- globalne premenne printujeme pomocou print, dbms_output.put_line nefunguje 
VARIABLE v_meno varchar2(50);
print v_meno;

Exceptions -- -20 999; -20 000 
SQLERRM -- error message
SQLCODE -- cislo erroru

-- V triggri neodchytavame vynimku ale vyvolavame
declare 
 v_cislo integer;
 delenie_nulou exception;
 PRAGMA EXCEPTION_INIT(delenie_nulou, -01476);
begin
 v_cislo:=5/0;
 raise_application_error(-20000, 'chyba'); -- -20 999; -20 000
 exception 
  when delenie_nulou then dbms_output.put_line('Delenie nulou');
  when others then 
   dbms_output.put_line(SQLCODE);
   dbms_output.put_line(SQLERRM);
end;
/

create table log_tab(os_cislo integer, kto varchar(20), kedy date);

create or replace trigger trig_st
after update on student
for each row
begin
 insert into log_tab values(:new.os_cislo, user, sysdate);
end;
/

create or replace trigger trig_st_zmena_oc
before update on student
for each row
begin
 if :old.os_cislo<>:new.os_cislo then 
  raise_application_error(-20000, 'Nemozes menit osobne cislo');
end if;
end;
/

update student set rocnik=2 
 where rocnik=1;
 
select * from log_tab; 

rollback;
```

# Prednaska 7 - Normalizacia
Determinant - mnozina atributov z ktorej vychadzaju ine atributy
1. cast 
	- odstranit duplicity
	- odstranit vypocitatelne
	- atributy musia byt **atomicke** 
2. cast - uplna FZ
	- uplna FZ - atribut je zavisly od celeho primarneho kluca (cize ked je KPK kompozitny)
	- (CP, SK_ROK, OC) -> (RC ->Meno)
3. cast
	- odstranit tranzitivne FZ (A->B->C=A->C)
	- odrazeme na konci(A->B-/>C =(spravime 2 tabulky) A->B   B->C)
	- vztah 1:1 treba premysliet ci to ma prinos

## Funkcna zavislost (FZ)
- vztah medzi 2 zavislostami
- A -> B, podla A viem jednoznacne povedat B
- OC -> RC, RC->Pohlavie to znamena OC -> Pohlavie - tranzitivnost
- determinant je ten na lavej strane pri prikladoch hore (vieme z neho vyvodit lavu stranu)
- (CP, SK_ROK, OC) -> Vysledok
- Login<->OC - vzajomna funkcna zavislost
- A->B trivialna, elementarna, uplna
### Typy zavislosti
- Elementarna FZ - A→B, keď atribút A je atomický
- Uplna FZ - (OsobneCislo, CisloPredmetu)→vysledok, Je to vtedy, keď na určenie výsledku **potrebuješ celú ľavú stranu**. Ak by si z nej niečo odobral, už ten výsledok nezistíš.
- Vzajomna FZ - A <-> B
- Tranzitivna FZ - R(A, B, C), A -> B, B -> C, tranzitivna je A -> C
- Trivialna FZ - R(A, B) A -> B, ked B je podmnozinou A, (OC, RC) -> (OC, RC), (OC, RC) -> OC
- Netrivialna FZ - R(A, B) A -> B, aspoň jeden z množiny atribútov B nie je z množiny atribútov A, (OC, RC) -> (OC, Meno)
- Uplne netrivialna FZ - R(A, B) A -> B, ani je z mnoziny atributov B nie je z mnoziny atributov A, (OC, RC) -> (Meno, Priezvisko)


## 3NF
1NF - odstranit duplicity, vsetky vypocitatelne, atomicke atributy
2NF - kazdy atribut ktory netvori kluc je zavisli od celej mnoziny PK
3NF - nemam tam tranzitivny FZ od primarneho kluca

## Boyce- Codd normalna forma
Kazdy determinant je kandidatom primarneho kluca.
Vychadza z 1NF
OC -> RC -> Meno
 |              -> Priezvisko
V              -> Ulica
ST_SK      -> PSC 
         -> obec

moze byt tabulka s jednym atributom ktory je KPK ale nema determinant!!!

# Prednaska 8 - Velka noc
# Prednaska 9 - View
```sql
create or replace view v_student
as
 select meno, priezvisko, rod_cislo, os_cislo, st_odbor, st_zameranie,
    rocnik, st_skupina
  from student join os_udaje using (rod_cislo)
   where rocnik=2;
   
desc os_udaje;

insert into v_student
 values('Michal', 'Kvet', '551124/1234', 20000, 100, 0, 1, '5ZI011');

create or replace trigger trig_v_st
 instead of insert on v_student
  --for each row -- instead of je automaticky for each row
begin
 insert into os_udaje(meno, priezvisko, rod_cislo)
  values(:new.meno, :new.priezvisko, :new.rod_cislo);
 insert into student(rod_cislo, os_cislo, st_odbor, st_zameranie, rocnik, st_skupina)
  values(:new.rod_cislo, :new.os_cislo, :new.st_odbor, :new.st_zameranie, 
        :new.rocnik, :new.st_skupina); -- hodnoty mozme aj natvrdo napisat sem
end;
/

rollback;

create or replace view v_osoba_P
 as select * from os_udaje
  where meno like 'P%'
   --with check option
   ;

select * from v_osoba;

insert into v_osoba values('551124/1234', 'Michal', 'Kvet', null, null, null);

create or replace view v_osoba_K
 as select * from v_osoba_P
  where priezvisko like 'K%'
   with check option; -- check nie je determenisticka

insert v_osoba_K values('551124/1234', 'Michal', 'Kvet', null, null, null);

----------------------------------------------------------------------------
-- Cured
-- C - column - NULL, NOT NULL, UNIQUE - KPK
-- U - user - suvisi aj s datumami, s mnozstvom zapisani predmetu
-- R - referential - FK - NULL, ref. PK
-- E - entity - PK - unique, minimal, NN
-- D - domain - datovy typ, domena - check

desc zap_predmety;
-- odtial som nestihal
create table ucitel_tab
 (os_cislo integer,
  meno varchar2(20), 
  priezvisko varchar2(20),
  );

create table katedra_tab
(
    id_katedry integer,
    
)
```
# Prednaska 10 - Cas
sysdate = server cas
current_date = klient cas

date
timestamp je presnejso ako date

D den v tyzdni
DD den v mesiaci
DDD den v roku
Day 
MM cislo mesiaca
Month meno mesiaca
Mon meno mesiaca
YYYY / RRRR rok
YY / RR posledne 2 cifry roku
HH24 hodina
MI minuta
SS sekunda
W week poradove cislo tyzdna v mesiaci
WW week poradove cislo tyzdna v roku
Q quartal

select to_date('5.2024', 'MM.YYYY') <- vrati 1.5 2024
select to_date('5.2024', 'DD.YYYY') <- vrati 5.aktualny mesiac 2024

last_day = posledny den mesiaca

next_date funkcia, ktora funguje ako next_day, ale aj v amerike aj u nas

select DATE '12-4-2024' from dual;

## Kurzory
- objekt v jazyku SQL a spristupnuje zaznamy z prikazu select
DECLARE
OPEN
LOOP
FETCH
UPDATE/DELETE
END LOOP
CLOSE - v oracle sa dealokuje

Typy kurzorov
- sekvencny kurzor
- posuvny kurzor
- kurzor pre zmeny

## TCL
Chcem aby presla operacia bud cela, alebo vobec  
Napr. bankove transakcie

- `COMMIT` - potvrdit zmeny
- `ROLLBACK` - zahodit vsetky zmeny

Ak raz spravim commit, uz mi ziadny rollback nepomoze  
Rollback sa vracia ku poslednemu commitu (resp. na zaciatok)

**Transakcia** - ako transakcia sa chape, postupnost operacii read a write, vratane vypoctovych krokov. Niekedy sa sem radi aj dotaz. Na ulozenie zmien po transakcii sa pouzivaju TCL prikazy(Commit, Rollback). 
### ACID - Vlastnosti Transakcie
- atomicity - atomickost - Ak jedna časť transakcie zlyhá (napr. pri prevode peňazí sa peniaze odčítajú, ale nepripíšu), celá transakcia sa zruší (**ROLLBACK**) a databáza sa vráti do pôvodného stavu.
- consistency -  CRUDE,  Transakcia musí previesť databázu z jedného platného stavu do druhého platného stavu
- isolation - Transakcie bežia nezávisle od seba. Výsledok jednej transakcie nesmie byť viditeľný pre ostatné transakcie, kým nie je potvrdený (**COMMIT**)
- durability - trvanlivost, Akonáhle dostaneš potvrdenie o commite, zmeny sú zapísané v trvalom úložisku

## Relacna Integrita
Zakladom je mnozina integritnych obmedzeni, ktore reprezentuju tvrdenia o datach, ktore musia v databaza platit

### CRUDE
**Column**
- stlpcova
- sa viaze na jeden atribut
- NULL / NN, UNIQUE 
**Reference** 
- referencna
- sa viaze na FK 1/1,2
- FK musi ma bud referovat na nejaky KPK z inej tabulky s ktorou je vo vztahu alebo byt null
**Entity**
- entitna
- na celu tabulku
- PK atributy musia byt NN a PK musi byt unikatny
**Domain**
- domenova
- na viacero atributov v jednej tabulke
- CHECK, IF *kontrola hodnoty*
**User** 
- uzivatelska
- na viacero tabuliek a atributov
- Deklarativny - Check, Proceduralny - Trigger
- "Dávajú dáta zmysel? Nie je cena záporná? Je dátum dodania neskorší ako dátum objednávky?"

**Domain vs User**
- Doménová integrita určuje **množinu prípustných hodnôt** pre konkrétny stĺpec. Rieši to, či je hodnota vôbec „vhodného typu“ pre danú oblasť.
- User integrity sú **špecifické biznis pravidlá**, ktoré nevyplývajú len z typu dát, ale z logiky tvojej aplikácie alebo firmy. Často porovnáva viacero stĺpcov medzi sebou.

c - tabulka pouzivatel musi mat id vyplnene a unikatne
u - nemozme mat skorej zaciatok studia ako koniec
r - nemozme mat odkaz v tabulke objednavka - fk na zakaznika, ktory neexistuje
e - musime mat v tabulke student unikatne id napr os_cislo
d - vek nemozme by zaporne cislo
### Vlastnosti relacie
- neobsahuje duplicitne n-tice
- n-tice su neusporiadane
- m-tice atributov su neusporiadane
- hodnoty atributov su atomicke
ANNU
- A - atomicke atributy
- N - neusporiadanost riadkov
- N - neusporiadanost stlpcov
- U - unikatnost riadkov pomocou PK
### Kardinalita Relacie/Tabulky
- pocet zaznamov

# 2. Zapocet
not in not exists
joiny
agregacne funkcie
kurzor jednoduchsi (spojit tabulky a zaznamy textovo)
procedury/funkcie/triggre
views
Selecty 2 
Alter joiny 
Right join 
Left join 
Agregacne funkcie 
Having 
Where 
Group by 
In exists 
Pohľad trigger procedúra funkcia 
Vymazavanie z tabuľky 
Pridať check constraint
Dnešný zápočet(formulácia nie je totožná, ale princíp ostáva):

- Trigger: zabezpečte aby vkladaný zamestnanci neboli mladší 16 rokov
- view: urobte pohlaď, ktorý zobrazí ZTP, ktorým končí status o mesiac
- funkcia: zobrazte príspevky na základe roku
- Selecty:
    
    - vypíšte poberateľov, ktorý majú aspoň 3 príspevky
    - vypíšte sumu odvodov poistencov
    - (niečo so sumou, už nepamätám)
    
- ABCD: tabuľka ktorá má inserty a zvoľ čo vráti 3
- Bonus: vypíš poberatelov, ktorý mali aspoň 5 príspevok vyplatené za posledné dva mesiace

1.Vypíšte koľko unikátnych typov príspevku bolo vyplatených v minulom roku 
2.Vypíšte priemernú sumu odvodenu do poisťovne za minuly rok 
3.Napíš trigger ktorý zabezpečí ze aktualizáciu záznamu v tabuľke p_poistenje môže vykonať len používateľ admin_poistovna 
4.Vytvorte funkciu ktorá pre zadanú osobu vráti informáciu o tom ci je akt. Zamestnaná Tieto otazky som si zapamätal čo som mal dnes

1. cca taketo ulohy boli na dnesnom započte: 1.Výtvor funkciu ktorá vráti počet zamestnancov vo firme na základe ICO. 
2. nápis trigger ktorý zabezpečí že sa nedá pridať nový zamestnávateľ do mesta s názvom ‘Bratislava’ v sobotu alebo nedelu
3. Ku každému okresu výpis jeho nazov. Ak ma mesta, vypíšte ich názvy. Zobrazíte aj okresy bez miest
4. pre každé mesto výpis počet osôb, ktoré v nom bývajú. V prípade že nikto uveďte 0. 
5. Výtvor pohľad ktorý bude obsahovať všetky zamestnané osoby spolu s informáciami o zamestnávateľovi 
6. Výpis koľko poistencov je aktuálne oslobodených od platenia príspevkov 
7. ABCD otazka Ktorý prikaz ti vratí čislo 3 
8. Asi to bol ten bonus CREATE TABLE s nejakými podmienkami

# Prednaska 12
RBO - rule based optimization - poradie prikazov - stary styl
CBO - cost based optimization - podla nakladov na vypocet
### select robi:
- projekcia - redukovanie mnozstva stlpcov -- napr. *, meno, priezvisko, rc, ...
- selekcia - redukovanie mnozstva riadkov -- napr. za where keyword

## spojenie:
- inner
- outer - left, right, full

### prirodzene spojenie / natural join 
- spojim tabulky bez using / on na zaklade rovnakeho nazvu atributu
- pokial nemam atribut s rovnakym nazov atributov, tak spoji kartezskym sucinom
```sql
select *
  from os_udaje natural join student;
```

### V podmienke on mozme pouzit = != < >
- ked chcem spojit napriklad roznych ludi z rovnakej tabulky
### Kartezsky sucin
```sql
select * 
  from tab cross join tab2;

select *
  from student join st_odbory using(st_odbor); -- tiez KS
 
```

### Legacy joins
```sql 
select *
  from os_udaje, student
  where student.rod_cislo=os_udaje.rod_cislo; -- stary sposob spajania tabuliek

select *
  from os_udaje, student
  where student.rod_cislo(+)=os_udaje.rod_cislo; -- stary sposob spajania tabuliek

select rod_cislo, meno, priezvisko, os_cislo, rocnik
 from os_udaje join student using (rod_cislo); -- projekcia, spojenie

select rod_cislo, meno, priezvisko, null, null
  from os_udaje
    where not exists (
	    select 'x' from student
	      where student.rod_cislo=os_udaje.rod_cislo
    ); -- projekcia, selekcia

```

### Zjednotenie
```sql
-- LEFT JOIN
-- zjednotenie - UNION
select rod_cislo, meno, priezvisko, os_cislo, rocnik
 from os_udaje join student using (rod_cislo)
UNION
select rod_cislo, meno, priezvisko, null, null
  from os_udaje
    where not exists (
	    select 'x' from student
	      where student.rod_cislo=os_udaje.rod_cislo
    )
    order by os_cislo nulls first; -- zjednotenie, selekcia, projekcia a spojenie  
    
-- union kompatibilne selekty 
-- 1. rovnaky pocet atributov
-- 2. rovnake typy
-- 3. poradie atributov
-- 4. vyznam hodnot atributov

-- rozny vyznam atributov !!
select meno from os_udaje
UNION
select priezvisko from os_udaje;

UNION -- potlaca duplicity
UNION ALL -- to nerobi
```

### Rozdiel
```sql
select meno, priezvisko from os_udaje
MINUS
select meno, priezvisko from ucitel;
```

### Prienik
```sql

select meno, priezvisko from os_udaje
INTERSECT -- prienik
select meno, priezvisko from ucitel;

```

### Delenie
```sql
create table st1 as select os_cislo, rod_cislo, st_zameranie, rocnik
					 from student;

create table st2 as select os_cislo, st_odbor, st_skupina, stav, ukoncenie, dat_zapisu
					 from student;

select *
  from st1 join st2 using (os_cislo) -- povodny student
```
- musime spajat Kandidatom primarneho kluca


Priprava na skusku

1. Ako by ste zistili kardinalitu relacie pre tabulku P_ZAMESTNAVATEL.
```sql
select count(*)
	from p_zamestnavatel;
```
2. Urcte determinanty: (X, Y)->Z; Z->X; Z->W
Determinant
(X, Y) -> Z a tranzitivne X a W
Z -> X, W

3. Pre kazde mesto vypiste pocet nezamestnanych osob, ktore v nom byvaju (ak su v meste vsetci zamestnani, vypiste 0).
	```sql
	select m.n_mesta, count(*)
		from p_mesto m
		left join p_osoba o on m.psc=o.psc
		left join p_zamestnanec z o.rod_cislo=z.rod_cislo
		where z.dat_od < sysdate and
			(z.dat_do > sysdate or z.dat_do is null)
		group by m.n_mesto;
	```
4. Vymazte typy prispevkov, ktore nikto nikdy nepoberal. Pozor na referencnu integritu.
```sql
delete from p_historia
	where id_typu not in (
		select id_typu
			from p_poberatel;
	);
delete from p_typ_prispevku
	where id_typu not in (
		select id_typu
			from p_poberatel;
	);
```
5. Urcte determinanty: D->C; (A, B) -> D; C->D
Determint->Funkcne Zavisle
D->C
(A, B) -> D, tranzitivne C
C->D
C<->D vzajomne zavisle
6. Definujte primarny kluc pre tabulku P_ZAMESTNANEC:
```sql
alter table p_zamestnanec
	add constraint pk_p_zamestnanec primary key(rod_cislo, dat_do)
```
7. Definujte cudzi kluc:
	- Mnozina atributov, ktora je definovana v inej ralacii ako kandidat primarneho kluca
8. Vlozte do tabulky osoba z tabulky nove_osoby (rovnaka struktura). vlozte len tie, ktore tam este nie su.
```sql
insert into osoba (rod_cislo, meno, priezvisko, ulica, psc, obec)
	select rod_cislo, meno, priezvisko, ulica, psc, obec
		from nove_osoby no
		where not exists (
			select 'x' from osoba o
				where no.rod_cislo=o.rod_cislo 
		);
```
9. Definujte, o aky typ funkcnej zavislosti ide:
	- A<->A - Trivialna zavislost

10. Napiste trigger, ktorym zabezpecite, ze novy poistny zaznam (p_poistenie) sa neda vytvorit, ak osoba ma aktualne aktivne poistenie.
	```sql
	create or replace trigger nove_poistenie
	before insert on p_poistenie
	for each row
	declare
		v_pocet integer
	begin
		select into v_pocet
			from p_poistenie
			where rod_cislo = :new.rod_cislo
				and (dat_do is null or dat_do > trunc(sysdate))
				and dat_od < trunc(sysdate);
		if v_pocet > 0 then
		  raise_application_error(-20000, 'Uz existuje aktivne poistenie pre osobu');
		end if;			
	end;
	/
	```

## I. ZÁKLADNÉ POJMY A VZŤAHY
1. Vysvetli rozdiel medzi doménou a atribútom.
	- Doména - mnozina hodnot, ktore splnaju typ alebo velkost pre dany atribut
	- Atribut - stlpec v tablke, jedna premenna tabulky, ktora priraduje entitam hodnotu, ktoru obsahuje kazdy zaznam tejto entity(moze byt aj null)
2. Definuj pojem entita, inštancia a atribút v kontexte databáz.
	- Entita - predstavuje jednu tabulku
	- Instancia - instancia predstavuje zaznam alebo riadok, ktory je vytvoreny podla vzoru entity a ma svoje hodnoty
	- Atribut - stlpec tabulky, prideluje nejaku hodnotu vlastnosti entity
3. Vysvetli pojem relácia a uveď základné vlastnosti relácie (ANNU).
	- ANNU:
		- A - atomicke atributy
		- N - neusporiadanost riadkov
		- N - neusporiadanost stlpcov
		- U - unikatnost riadkov pomocou PK
4. Aký je rozdiel medzi fyzickou a logickou nezávislosťou dát?
5. Čo je kurzor a na čo slúži v databázovom systéme?
	- kurzor je objekt jazyka SQL, ktory nam spristupnuje jednotlive zaznamy v mnozine ziskanej prikazom SELECT
6. Čo je zdieľanosť dát a prečo je dôležitá?
	- umoznuje nam poskytovat data viacerym klientom naraz, dolezita pre subeznost aplikacie, odstranuje redundanciu, pridava konzistenciu
7. Vysvetli pojem union-kompatibilita. Ktoré operácie ju vyžadujú?
	- dve mnoziny su union kompatibilne ak maju rovnaku mnozinu atributov. cize rovnaky pocet atributov s rovnakymi typmi
8. Uveď dva príklady unárnych a dva binárnych operácií relačnej algebry.
	- Unarne - projekcia, selekcia
	- Binarne - kartezsky sucin, prienik, rozdiel, zjednotenie, delenie, spojenie
9. Vysvetli rozdiel medzi projekciou a selekciou v relačnej algebre.
	- Projekcia - redukcia stlpcov (atributy)
	- Selekcia - redukcia riakov  (pocet zaznamov)
10. 
11. Čo je primárny kľúč? Aké vlastnosti musí mať?
	- Primarny kluc je unikatny a minimalny. Musi byt not null a da sa nim jednoznacne ukazat na jeden zaznam (riadok) tabulky. Oznacujeme ho PK
12. Čo je kandidátsky kľúč? Ako sa líši od superkľúča?
	- Kandidatsky kluc je nieco co moze byt primarny kluc a ma jeho vlasnosti ako minimalnost, unikatnost a not null.
	- Superkluc nesplna podmienku minimalnosti, cize moze obsahovat zbytocne atributy
	- Plati teda ze kazdy KPK je SK ale nie kazdy SK je KPK
13. Platí: každý superkľúč je zároveň aj kandidátsky kľúč? Odôvodni.
	- Neplati. Superkluc nemusi splnat podmienku KPK minimalnosti. Plati teda ze kazdy KPK je SK ale nie kazdy SK je KPK.
14. Čo je determinant? Môže byť každý determinant kandidátsky kľúč?
	- Determinant je mnozina atributov, na ktorych je nejaky iny atribut funkcne zavisly. Na to aby bol nas determinant KPK musi byt na nom funkcne zavisly kazdy atribut. Mozme ale povedat ze kazdy KPK je determinant, nie naopak
15. Definuj pojem funkčná závislosť.
	 - Funkcna zavislost znamena ze vieme podla determinanta urcit nejaky atribut. Ak A -> B, tvrdime, ze B je funkcne zavisli od A 
16. Vysvetli nasledovné typy funkčných závislostí: triviálna, netriviálna, elementárna, úplná, parciálna, tranzitívna, úplne netriviálna, vzájomná.
	- trivialna - ked zavisli atribut je podmnozinou determinantu: A, B -> B
	- netrivialna - ked aspon jeden zavisli atribut mnoziny B, nie je podmnozinou A, A -> B
	- elementarna - ked je atribut A je atomicky: A -> B
	- uplna - ked potrebujem celu lavu stranu na urcenie pravej strany: (A, B) -> C
	- parcialna - ked mame (A, B) kluc, ale na C staci A -> C - Zakazuje 2NF
	- tranzitivna - A -> B, B -> C ==> A -> C - Zakazuje 3NF
	- uplne netrivialna - ked ani jeden atribut nie je podmnozinou determinantu: A, B -> C, D
	- vzajomna - ked su mnoziny zavisle jedna od druhej (navzajom): A <-> B
17. Vysvetli pojmy zjednotenie a dekompozícia množiny funkčných závislostí.
	- zjednotenie mnozin funkcnych zavislosti - A -> B, A -> C = A -> (B, C)
	- dekompozicia mnoziny funkcnych zavislosti - A -> (B, C) = A -> B, A -> C
18. Aká je podmienka pre splnenie BCNF? Uveď definíciu presne.
	- Relacia je v BCNF vtedy a len vtedy, ked kazdy determinant je Kandidatom PK.
19. Vysvetli stručne, čo znamená, že relácia je v 1NF. Uveď príklad porušenia.
	- Na to aby bola relacia v 1NF, musime odstranit vsetky opakujuce sa podskupiny dat, cize kazdy atribut musi byt atomicky.
	- Napr atribut meno, ktory by obsahoval meno aj priezvisko.
20. Kedy je relácia v 2NF? Uveď príklad relácie, ktorá je v 1NF, ale nie v 2NF.
	- Relacia je v 2NF vtedy a len vtedy, ked je v 1NF a ked neklucovy atribut je uplne zavisli od celeho primarneho kluca.
	- Pr pre 1NF bez 2NF je: (RC, OC) -> DatumNarodenia. Datum narodenia je zavisli len od RC
21. Kedy je relácia v 3NF? Aký je hlavný rozdiel oproti 2NF?
	- Relacia je v 3NF vtedy a len vtedy, ak je v 2NF ked kazdy neklucovy atribut nie je tranzitivne zavisly od primarneho kluca.
	- V 2NF chceme odstranit parcialne zavislosti a v 3NF tranzitivne.
22. Definuj Boyce-Coddovu normálnu formu (BCNF).
	- Relacia R je v BCNF, vtedy a len vtedy, ked kazdy determinant je kandidatom primarneho kluca.
	- Silnejsia ako 3NF
23. 
24. Vysvetli rozdiel medzi identifikačným a neidentifikačným vzťahom. Ako ich rozpoznáš zo schémy alebo SQL kódu?
	- Identifikacny - plna ciara, FK je sucastou PK, cize mame PFK
	- Neidentifikacny - ciarkovana ciara, FK nie je sucastou PK, cize name FK
25.  Čo je povinné a nepovinné členstvo? Ako sa určuje z SQL kódu?
	- Povinne - FK je NOT NULL, zaznam sa musi odkazovat na nejaky PK z inej tabulky
	- Nepovinne - FK moze byt NULL, zaznam sa moze odkazovat na nejaky PK z inej tabulky
26. V nasledujúcom opise urči typ členstva a kardinalitu:  
    „Zamestnanec môže mať pridelené maximálne jedno auto, každé auto musí patriť jednému zamestnancovi.“
	- Kardinalita: 0..1 : 1
	- Chceme aby sa auto odkazovalo na zamestnanca pomocou FK, teda clenstvo je povinne.
	- Ak by sme museli mat FK v zamestnancovi, clenstvo by bolo nepovinne
27. Na čo sa viaže podmienka NOT NULL pri určovaní členstva vo vzťahu?
	- Viaze sa na FK v tabulke, ktora nim referencuje zaznam z inej tabulky. NOT NULL znamena, ze mame povinne clenstvo
28. Vysvetli ako sa určuje kardinalita medzi entitami pri identifikačnom a neidentifikačnom vzťahu.
	- Pokial pri identifikacnom vztahu mame PK, tvoreny este inym atributom, vieme, ze mame 1:N. Ak je PK iba tento jeden atribut, ktory je PFK, tak vieme ze mame 1:1
	- Ak mame neidentifikacny vztah, tak mame 1:N, pokial FK nie oznaceny ako UNIQUE, vtedy ide o 1:1
29. Aký je rozdiel medzi atomickým a viacnásobným atributom? Uveď príklad pre každý.
	- atomicky - je atribut, ktory je dalej nedelitelny, napr: Priezvisko
	- viacnasobny - atribut, ktory obsahuje viacero hodnot, napr: Inventar (obsahuje objekty, ktore vlastni hrac, ulozene napr v texte)
30. Vysvetli, čo je determinantný atribút a v čom sa líši od kandidátskeho kľúča.
	- determinantsky atribut je atribut na ktorom je funkcne zavisla mnozina inych atributov. nemusi jednoznacne a minimalne identifikovat riadok tabulky 
	- kpk je minimalny, unikatny identifikator zaznamu tabulky
	- kazdy kpk je determinant, ale nie kazdy determinant je kpk
31. Uveď štyri hlavné typy dátovej integrity v databázach.
	- Stlpcova, Entitna, Domenova, Referencna, Uzivatelska
32. Vysvetli, čo je integrita entít. Vzťahuje sa len na PK?
	- Atribut, ktory je oznaceny ako PK nesmie nadobudat nedefinovane hodnoty (musi byt not null).
	- Vztahuje sa len na PK
33. Čo znamená referenčná integrita a ako sa zabezpečuje v SQL?
	- Referencna integrita sa tyka vztahu medzi hlavnou a zavislou relaciou. Hlavna relacia ma PK kluc na ktory sa zavisla relacia odkazuje FK. FK sa musi odkazovat na hodnotu ktora existuje.
```sql
	  Alter table nazov
		  add constraint fk_nazov1_nazov2
		  foreign key (foreign_key) references (primary_key)
```
34. Vysvetli, čo je doménová a používateľská integrita. Uveď príklad.
	- Domenova - Udava ake hodnoty su pripustne na priradenie pre urcity atribut zaznamu, kontroluje typ, alebo dlzku cisla / retazca
	- Pouzivatelska - Tiez udava pripustne hodnoty, ale kontroluje ich logicke hodnoty, napr. interval, pritomnost podretazca
35. 
36. Vysvetli základné vlastnosti transakcií – čo znamená ACID?
	- A - Atomicity - transakcia prejde uplne alebo vobec
	- C - Consistency - transkacia prevedie databazu z platneho do platneho stavu
	- I - Isolation - zmeny transakcie nie su viditelna pre inych klientov, az kym ich nepotvrdime commit 
	- D - Durability - ako nahle dostaneme commit, zmeny su zapisane na trvalom ulozisku
37. Na čo slúži SAVEPOINT a ako sa používa pri transakcii?
	- savepoint sluzi ako miesto navratu, kde sa vieme vratit po vykonani nejakych prikazov, bez toho aby sme museli robit rollback na celu transakciu. vratime sa na miesto savepointu.
38. Aký je rozdiel medzi COMMIT a ROLLBACK? Ako ovplyvnia vložené údaje?
	- Commit - zapise vsetky transakcie do perzistentnej pamate
	- Rollback - vrati nas na miesto posledneho commitu


Koniec


 1.  Majme atribút od v tabuľke zahradnictvo_evidencia, ktorý nech má dátový typ date. Zabezpečte, aby hodnota mesiaca nebola 11, 12, 1 alebo 2
	```sql
	alter table zahradnictvo_evidencia
		add constraint chk_od
		check (extract(month from od) not in (11, 12, 1, 2))
	```

Koniec