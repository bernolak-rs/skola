# Databazove Systemy

## Basics

DBS (Databázový systém) = DB (databáza) + SRBD (System riadenia bázy dát)

Databázový systém tvoria:
- Dáta
- Hardvér
- Softvér
- Používatelia

DML (Data Manipulation Language) - Select, Update, Delete, Insert  
DDL (Data Definition Language) - Create, Alter, Drop - automaticky robi commit  
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

| Skratka | Meaning                   | Popis                                                                                                                                 |
| ------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| `NN`    | Not Null                  | Hodnota musi byt zadana, nemoze byt Null                                                                                              |
| `PK`    | Primary Key               | Primarny, unikatny kluc v tabulke, je `NN`, **je najviac jeden** (moze byt kompozitny - skladat sa z viacerych atributov?), minimalny |
| `FK`    | Foreign Key               | Odkazuje na primarny kluc z inej tabulky, moze byt aj Null                                                                            |
| `PFK`   | Primary Foreign Key       | Primary + Foreign                                                                                                                     |
| `KPK`   | Kandidat primarneho kluca | Nieco, co teoreticky moze byt tiez primarny kluc, Unikatny,not null, minimalny                                                        |
| SK      | Super kluc                | Unikatny,not null, nie minimalny                                                                                                      |

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

Kardinalita
- 1:1 - Jeden student by mal prave jedny osobne udaje a naopak
- 1:N - Student - Zap_predmety, os_udaje - student
- M:N - musime vytvorit novu tabulku - asociativna entita, ktora bude mat kompozitny primarny kluc zostaveny z prepojenych tabuliek - napr. zap_predmety zo vztahu student - predmet - vytvara identifikacny vztah
- 0 - znaci sa ako prazdna gulicka v diagrame o
- 1- sa znaci ciarkou |
- N - sa znaci rozvetvenim

M:N vznika asociacna entita

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

# Prednaska 5
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
AVG
- ignoruju NULL hodnoty napr. 
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