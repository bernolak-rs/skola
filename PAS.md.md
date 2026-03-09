# Prednaska 1

X = mnozina hodnot - napr. jazdy na bicykli
Xi = konkretna statisticka hodnota - napr. konkretna jazda
zakladny subor - namerane hodnoty za urcitu dobu
n - rozsah statistickeho suboru
ni = absolutna pocetnost (2,1,3,5,3,...)
fi = relativna pocetnost (0,06; 0,03; 0,09; ...)
Ni / Nq= komulativna pocetnost - vyjadrena poctom (2,5,10,17,...)
Hi / Hq = komulativna pocetnost - vyjadrena castou celku (0,06; 0,16; 0,32; ...)
smerodajna odchylka - typicka odchilka od centra

| ai  | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    |
| --- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| ni  | 2    | 3    | 5    | 7    | 6    | 4    | 2    | 1    | 1    |
| fi  | 0.06 | 0.1  | 0.16 | 0.24 | 0.19 | 0.13 | 0.06 | 0.03 | 0.03 |
| Nq  | 2    | 5    | 10   | 17   | 23   | 27   | 29   | 30   | 31   |
| Hq  | 0.06 | 0.16 | 0.32 | 0.56 | .75  | .88  | .94  | .97  | 1.00 |


Statisticky znak
- Kvalitativny 
	- maju konecny pocet hodnot
	- maximalne ordinalne znaky
	- nie je mozne ich zmysluplne vyjadrit pomocou cisel
- Kvantitativny: 
	- kardinalne statisticke znaky
	- mozne vyjadrit pomocou ciselnej hodnoty
	- Spojity znak
	- Diskretny znak
- Zaradenie odrinalnych statistickych znakov nie je jednoznacne -> daju sa zoradit -> maju v sebe okrem kvality aj isty aspekt kvantity
### Nominalny znak 
- sú tie znaky, ktorých prípustné hodnoty sú názvy alebo kategórie, ku ktorým sú štatistické jednotky priradené
- napr: farba bicykla, typ bicykla, výrobná značka bicykla
### Ordinalny znak
- sú znaky, ktorých možné realizácie sú zoraditeľné, ale odstup medzi nimi nie je interpretovateľný
- napr: znamky na vysokej skole -> A-FX
### Intervalovy znak
- predstavujú vyššiu úroveň znakov, pretože ich prípustné hodnoty je nie len možné zoradiť, ale aj interpretovať odstupy medzi nimi
- Medzi intervalové znaky zaraďujeme tie, pri ktorých je nulový bod určený dohodou, ako je napríklad 0◦ C alebo rok 0, ktorým začína náš letopočet. Príkladom intervalového štatistického znaku je rok výroby bicykla
- má zmysel povedať, že jeden je o tri roky skôr vyrobený ako iný. No hovoriť, že jeden je dvakrát skôr vyrobený ako druhý nie je zmysluplné
### Pomerovy znak
- sú znaky s najvyššou výpovednou hodnotou
- V ich prípade je zmysluplne interpretovateľný aj pomer medzi ich jednotlivými realizáciami -> je to dané prirodzenou existenciou nuly
- Ak sledujeme napr. počet výjazdov na bicykli za mesiac, tak je prirodzene dané, že 0 výjazdov je v prvej sekunde prvého dňa daného mesiaca. Ak zistíme, že Karol za sledované obdobie bol jazdiť 16-krát a Jozef len 4-krát, tak môžeme tieto údaje interpretovať nasledujúcim spôsobom:
	1. Karol jazdil častejšie ako Jozef,
	2. Karol absolvoval o 12 jázd viac ako Jozef,
	3. Karol jazdil 4-krát častejšie ako Jozef.

| Uroven      | Pocetnost | Usporiadat | Diferencia | Pomer |
| ----------- | --------- | ---------- | ---------- | ----- |
| nominalna   | ano       | nie        | nie        | nie   |
| ordinalna   | ano       | ano        | nie        | nie   |
| intervalova | ano       | ano        | ano        | nie   |
| pomerna     | ano       | ano        | ano        | ano   |
- Intervalové a pomerné štatistické znaky sa často uvádzajú pod spoločným názvom **metrické (kardinálne) štatistické znaky**.

Priklad
V roku 2023 sa na istej fakulte uskutočnil prieskum. Študentov Bc. stupňa štúdia sa pýtali na ich cyklistické zvyklosti. Dotazník bol rozdaný 341 študentom a vrátilo sa 311 kompletne vyplnených dotazníkov. Prostredníctvom dotazníka boli sledované nasledovné znaky:
-  Typ bicykla - kvalitatívny, nominálny znak;
- Počet jázd za mesiac - diskrétny, kvantitatívny, pomerný znak;
- Nájazd km za rok - (kvázi) spojitý, kvantitatívny, pomerný znak.


| ai  | a1  | a2  | a3  |
| --- | --- | --- | --- |
| ni  | n1  | n2  | n3  |

# Prednaska 2 - Deskriptivna Statistika

## Ciselne charakteristiky
- kvantifikovat data
	- **Charakteristiku polohy** 
		- centrum dat - moze by aritmeticky priemer, median, modus (podla toho co sa hodi) - miesto, kde by sa mala nachadzat vacsina bodov
		- popisuje cislo/hodnotu okolo, ktorej su ostatne hodnoty
	- **Charakteristiku variability** - mierka rozptylenia - rozptylenie od centra
- Das aber muss einen Sinn ergeben!! - Ale toto musi davat zmysel

- **Triedenie dat** - rozdelenie do intervalov - casto pri spojitych datach - moze dojst k strate informacii

| ai  | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    |
| --- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| ni  | 2    | 3    | 5    | 7    | 6    | 4    | 2    | 1    | 1    |
| fi  | 0.06 | 0.1  | 0.16 | 0.24 | 0.19 | 0.13 | 0.06 | 0.03 | 0.03 |
| Nq  | 2    | 5    | 10   | 17   | 23   | 27   | 29   | 30   | 31   |
| Hq  | 0.06 | 0.16 | 0.32 | 0.56 | .75  | .88  | .94  | .97  | 1.00 |
Robustny - odolny
### Priemer 
- ma vyznam len pri kvantitativnych datach

### Modus
- Najcastejsie vyskytujuca hodnota
- xmod alebo x^
- xmod = 3
- jedina ciselna charakteristika polohy pouzitelna pre vsetky urovne dat
- pri metrickych premennych ho uprednostnime pred priemerom, ak nas zaujima typicka hodnota
- BIMODUS - 2 skupiny a spravi sa bimodalne rozdelenie na 2
- modus moze byt interval? alebo spriemerovat?
- Slabiny: 
	- nemusi existovat
	- moze byt najpocetnejsia krajna hodnota ktora je daleko od ostatnych
	- pri spojitych datach nevhodne
	- velka nestabilita
	- mnohopocetnost
### Median

| najazdene km  | 300 | 400 | 500 | 600 | 700 | 8400 |
| ------------- | --- | --- | --- | --- | --- | ---- |
| pocet jazdcov | 1   | 6   | 6   | 5   | 1   | 1    |
| Ni            | 1   | 7   | 13  | 18  | 19  | 20   |
20/2 -> X(10); X(11) = 500; 500 -> (500 + 500) / 2 = 500 = xmed

x- (priemer) = 890 km.

3,4,4,4,4,4,4,5,5,5,5,5,5,6,6,6,6,6,7,84 -> xmed = 5
- STRED VARIACNEHO RADU
- x~ alebo xmed
- 0,1,2,3,4,5, 6000000000 -> x~= 3
- 0,1,2,3,4,5,6,7 -> x~=3,5
- slabiny:
	- nezachyti zmenu v jednej z polovic dat
	- narocnost na spracovanie rozsiahlych datasetoch
	- nevhodne pre male subory
	- nestabilny pri opakovanom nahodnom vybere
	- matematicky nepoddajny - spolocny median dvoch spojenych suborov
- vzdy rozdeli variacny rad na 2 polovice
- #{xi<=xmed}>=n/2 und #{xi>=xmed}>=n/2 -> Median najde aspon polovicu hodnot z celej mnoziny ktore su mensie ako on a aspon polovicu hodnot ktore su vacsie ako on
	
## Kedy co?
Priemer - vhodny pri priblizne symetrickom rozdeleni bez vyraznych odlahlych hodnot
Modus - vhodny pre nominalne znaky alebo na urcenie najcastejsej kategorie
Median - najodolnejsi

## Kvantil
- xp - p-kvantil
- Rozdeluje na 2 casti
- x0.20 -> aspon 20 percent je menej a 80 percent je viac
- #{xi<=xp} >= p*n und #{xi>=xp} >= (1- p)*n
-1,0,1,2,3,4,5,6,7,99
x0.30 = ?
(i) = p * n = 0,30 * 10 -> da nam index -> 3 => x0.30 = (x(3) + x(4)) / 2 = 1,5
patri celym cislam

x0.35 = 3.5 => x4 = 2
nepatri celym cislam

Kvartil - rozdeluje data na 4 casti:
- Q1 - dolny kvartil x0.25
- Q2 - stredny kvartil - median
- Q3

## Histogram
- musia byt spojite data
  
## Boxplot
- visualizacia rozlozenia dat
- rozdelene na kvartily

# Prednaska 3
### Nahodna premenna
- Náhodná premenná X je funkcia , ktorá každému elementárnemu javu náhodného pokusu priradí reálne číslo, t. j. X : Ω → R.
- Premenná (štatistický znak) X sa nazýva náhodná premenná, ak jej hodnoty sú výsledkom náhodného pokusu. Číslo x ∈ R, ktoré nadobudne náhodná premenná X pri jednom opakovaní náhodného pokusu, sa nazýva realizácia náhodnej premennej X.
- PRIRADIT NAHODNEMU POKUSU CISLO

TX - mnozina realizacii (obor hodnot) nahodnej premennej X

### Diskretna nahodna premenna
- Náhodná premenná X sa nazýva diskrétna, ak nadobúda len konečne veľa alebo
spočítateľne veľa hodnôt x1 , x2 , ..., xk .
- TX = {x1 , x2 , ..., xk } = {x ∈ R|P(X = x) > 0}
- napriklad hod kockou
- napriklad hod mincou
### Pravdepodobnostna funkcia
- Nech X je náhodná premenná s realizáciami TX = {x1 , x2 , ..., xk }. Potom funkciu
p(xi ) = P(X = xi )
nazývame pravdepodobnostná funkcia diskrétnej náhodnej premennej X , ak platí
![[Pasted image 20260303233320.png|164]]

Pravdepodobnostnú funkciu p(xi ) môžeme vyjadriť v princípe tromi spôsobmi:
- Tabuľka rozdelenia pravdepodobností
![[Pasted image 20260303233517.png]]
- pomocou stĺpcového grafu, kde výška stĺpca zodpovedá pravdepodobnosti danej realizácie
![[Pasted image 20260303234946.png]]
- analyticky - pomocou predpisu funkcie
![[Pasted image 20260303233536.png]]

### Spojita nahodna premenna
- Náhodná premenná X sa nazýva spojitá, ak existuje nezáporná funkcia f (x) ≥ 0, že
pre každý interval ⟨a, b⟩ platí
![[Pasted image 20260303233849.png]]
f(x) -> hustota rozdelenia pravdepodobnosti náhodnej premennej X
	-> modeluje rozdelenie pravdepodobnosti spojitej náhodnej premennej X

### Distribucna funkcia
![[Pasted image 20260303234059.png]]
- aka je pravdepodobnost ze hodnota je mensia alebo rovno ako hodnota x
Nech F (x) je distribučná funkcia náhodnej premennej X . Potom
- pre každé x ∈ R platí 0 ≤ F (x) ≤ 1;
- lim F (x) = 1 a  lim F (x) = 0;
  x→∞              x→−∞
- F (x) je neklesajúca;
- F (x) je sprava spojitá.

### Ocakavana (stredna) hodnota E(X)
- Stredná hodnota E (X ) je číslo, ku ktorému sa priemer výsledkov pokusu blíži pri veľkom počte opakovaní.
- Je to teoretický ekvivalent priemeru.
![[Pasted image 20260303234417.png]]

### Disperzia D(X)
- Disperzia náhodnej premennej je číslo, ku ktorému sa rozptyl zo vzorky blíži pri veľkom počte meraní.
- Disperzia D(X ) je teoretickým ekvivalentom rozptylu s 2 .
![[Pasted image 20260303234641.png]]
![[Pasted image 20260304075522.png]]
### Hustota rozdelenia pravdepodobnosti
- pravdepodobnost z intervalu
- musi byt spojita