
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
