# Prednaska 1

### Poziadavky na absolvovanie
- 2 semestralne prace
- 30/50 minimum
- skuska 13/25, 13/25 (pisomna, ustna) minumum

### Vyznam pouzivanie vhodnych US
- rychlost aplikacie
	- setrenie energie
	- AI ekologicky neusporne
- pamatova narocnost
- rychlejsi vyvoj aplikacie
Hardver nikdy nesplni vsetky naroky

# Prednaska 2
### Ako vybrat vhodnu US
- Disk alebo operacna pamat
- *Explicitna implementacia je vseobecne lepsie, pre potencionalne menutie pamate* 
### Memory Manager
- Eliminacia memory manazera - vytvoritit listovu strukturu kde budeme ukladat volne miesto, kde alokujeme miesto na zaciatku -> zrychli program
- v jave sa da nastavit mnozstvo RAM, ktore ma program k dispozicii

### B-strom
Preorder - najskor otec, potom synovia
Postorder - najskor synovia, potom otec3
In Order - zlava doprava
BVS nepodporuje duplicitu klucov, ale vieme prisposobit linearnym zoznamom

### Cvicenie 2
OOP
- enkapsulacia - zapuzdrenie atributov a metod
- dedicnost - subclass dedi vsetky atributy aj metody (aj private, ale nema priamy pristup)
- access modifiers - chrani kod a robi prehladnejsi interface triedy
	- public
	- private
	- protected
- polymorfizmus - 
- interface dovoli viacnasobnu dedicnost, hierarchia dovoli dedit len z jednej triedy

# Prednaska 3
Pokial ma prehliadka zlozitost O(n), tak mozme hovorit, ze ju nepodporuje, lebo nema vyznam. Mozme pouzit LinkedList

Primarny kluc musi byt unikatny
Sekundarne kluce nemusia byt unikatne

Struktury vychadzajuce z BVS nepodporuju viacrozmerne vyhladavanie

K-D strom
- K-dimenzionalny strom
- mozme efektivne naplnit strom ak mu dame linkedlist, kde hodnoty zoradime podla x, vyberieme median (root) a z dvoch mnozin zase zoradime podla y a opakujeme
- pokial mazeme a bulbeme nadol, malo by to byt maximalne k-krat (mozno k+1)
- poslednu situaciu z poznamiek nemusime vediet na skusku
- v semestralke nemusi byt delete
- delete je velmi zlozity na implementaciu

Quadstrom
- dvojrozmerny strom
- konstruktor potrebuje minimum a maximum pre x a y
- vratit stred
- narocne urobit co najvseobecnejsie
- hlbka moze byt prilis velka -> pamatovo neefektivna
- nastavime maximalnu vysku stromu, pokial dosiahneme maximum, tak do posledneho budeme davat zoznam do ktoreho budeme vkladat zaznamy
- mozme drzat zoznam v kazdom vrhole a vkladat do linkedlistov objekty, ktore sa nedaju posunut nizsie