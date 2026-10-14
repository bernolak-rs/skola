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

**Segmentovy strom**


**K-D strom**
- K-dimenzionalny strom
- mozme efektivne naplnit strom ak mu dame linkedlist, kde hodnoty zoradime podla x, vyberieme median (root) a z dvoch mnozin zase zoradime podla y a opakujeme
- pokial mazeme a bubleme nadol, malo by to byt maximalne k-krat (mozno k+1)
- poslednu situaciu z poznamiek nemusime vediet na skusku
- v semestralke nemusi byt delete
- delete je velmi zlozity na implementaciu

**Quadstrom**
- dvojrozmerny strom
- konstruktor potrebuje minimum a maximum pre x a y
- vratit stred
- narocne urobit co najvseobecnejsie
- hlbka moze byt prilis velka -> pamatovo neefektivna
- nastavime maximalnu vysku stromu, pokial dosiahneme maximum, tak do posledneho budeme davat zoznam do ktoreho budeme vkladat zaznamy
- mozme drzat zoznam v kazdom vrhole a vkladat do linkedlistov objekty, ktore sa nedaju posunut nizsie
- vysoko efektivny pri bodovych datach, horsie pri obdlznikoch

# Prednaska 4
**Octstrom**
- vhodny pri detekcii obrazu
**Redukcia farebnej hlbky**
- z RGB 2^24 urobime jeden bajt, kde vyberieme 8 farieb, ktore predstavuju nahradu a optimalizaciu - spriemerujeme farby
- vyuzite hlavne v starych web strankach
- oct-strom bol typickym riesenim - orezeme menej vyznamne bity

**Stromy**
Mozme:
- vyvazovat strom - vysoka rezia
- vlozit nahodnost, aby sme predisli zoradenenemu vkladaniu - nebude to dokonale

### AVL strom
- Andelson-Velskij a Landis strom (podla mien, asi autori)
- najjhorsie o 45% moze zdegenerovat
find:
- typicky vychadza z BVS - moze dedit - **find** je identicke
vkladanie:
- pri **vkladani**, ak nemame referenciu na otca, vkladame cestu do zasobnika
- ratazme rozdiel velkosti medzi lavym a pravym synom (podla poctu vrcholov) vlavo -1 a vpravo +1
- pri vkladani mozme spravit bud jednoduchu lavu alebo pravu rotaciu alebo lavo-pravu alebo pravo-lavu
- vynimka ak dostaneme ±2
mazanie:
- mazeme podpobne ako pri BVS - vymazeme a nahradime inorder/postorder nasledovnikom
- po rotacii musime upravit vyvazovacie faktory najskor v podstrome, potom smerom hore az po root
- vynimka ak dostaneme ±1

### Splay tree
- samoorganizujuci BVS
- operacie splay na vrhole N nam vrati strom s korenom N
- vhodny na vyhladavanie castych prvkov, na vrchole mame prvky ku ktorym chceme casto pristupovat

### Treap
- Tree(BVS) + heap
- Heap - otec musi mat vacsiu prioritu ako syn
- priorita - komparator funkcia
- Kluc(BVS) - Priorita(Heap)
- priorita je interna, pouzivatel k nej nema pristup a ani ju nenastavuje, API vyzera podobne ako BVS
- vyuziva nahodnost pridelovania priorit pre balancovanie
- pri vkladani vlozime ako do BVS a potom rotujeme kym nesedia priority
- pri mazani rotujeme nadol po list a potom vymazeme

### 2-3 strom
- perfektne vyvazeny strom - dlzka cesty do kazdeho listu je rovnaka
- kazdy strom ma 2-3 potomkov
- klucom moze byt cokolvek
- viaccestny strom :(
- vkladanie do obrazkov treba vediet - matematicky zapis netreba

# Cvicenie 4 - Semestralna prace pokyny
**AUŠ S1**
- Termin: Pondelok 12:00
**Quad tree**
- zoznam na kazdom node alebo na liste - vkladame 2rozmerny objekt, kluc musi byt univerzalny - kluc definovany ako "komparator" - bude vediet robit compare, nemal by to byt double
- x, y alebo 2d priestorovy objekt (obdlzniky) - nemusime vediet vkladat, staci objekt

**K-D tree**
- pocet dimenzii - kluc musi byt porovnatelny, vracia -1, 0, 1, 
- jedno Generikum implementuje compare a posielame mu dimenzie (Comparator)
- moze byt na jednu K, K alebo na 2 K implements comparator, V implementuje do stringu genericke triedy
- intervalove hladanie na K-D strome je najtazsia implementacia z tejto casti, in order a zlozitost musi byt vzdy lepsia ako N, idealne co najmensia

Mozem vyhadzovat vynimky? - Samozrejme

**Tester**
- Oscilacia okolo prazdnej struktury - rovnaka pravdepodobnost na insert a delete
- Ale treba aj testovat plne struktury - bud naplnime a potom oscilujeme s rovnakou pravdepodobnostou alebo zvysime pravdepodobnost insert oproti delete
- Moze nam to padat na inserte ale moze byt chyba aj v delete, mohlo to byt 1000 operacii dozadu
- Cakame 1000 prvkov, ale najdeme len 900, moze byt chyba pri rotacii a chybna referencia na koren
- Mozme testovat cely sorted inorder strom s arraylistom v ktorom mame kopie
- Kontrolavat aj cez rovnaky seed
- Ak dostaneme chybu na 10000 kroku, tak skusime najst nasadu pomocou iterovania cez nasada a hladame do 10 operacii prejavenie chyby
- snazime sa najst chybu do 5-10 minut, ak nenajdeme musime najst sposob, ak najst chybu
- treba otestovat AVL
- find - porovname interval zo stromu a z arraylistu, dolezite porovnavat data a nie len pocet
- robime rovnake operacie aj na quadstrome aj na k-d strome