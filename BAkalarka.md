Datumy:
31.3. - ziadost na inzinierske studium

17.4. - odovzdat elektronicky bakalarsku pracu veducemu a tutorovi

6.maj - vytlacena praca, odovzdat v informacnom centre
vytlacit v edise
6.maj - nahrat elektronicky do zaverecne prece s prilohami, kontrola protokol originality(ten poslat aj veducemu)

2.6. - uzatvorene vsetky predmety

11.-12. jun statnice

1.7. promocie

Dalsie funkcie stranky:
Ziskat vlastnu API pre semantic scholar
Nutné pridať
Wireframe
Diagramy
Testy

Vylepšenia
HTTPS
Mobilne GUI

Co mozes dopisat
presnejsia implementacia pripojenia na databazu

i wanna create browser mockup that has SmartSearch name in the middle and test prompt under it with pill design
I'll create a browser mockup with SmartSearch branding and a pill-shaped search input.

I've created a browser mockup featuring SmartSearch with a clean, modern design. The mockup includes realistic browser chrome with window controls and an address bar, the SmartSearch branding prominently displayed in the center, and a pill-shaped search input with an integrated search button below it.

docker
pouzivame debian, alpine mal problem s C kniznicami a debian matchoval s rust: slim-bookworm

Dopis
Funkcie na databaze - upravenie pripajania na databazu kvoli dockery
web server
frontend doplnt technicke informacie k jednotlivym oknam
testovanie
Rust doc
Pouzivatelska prirucka
Zaver

## 1. Úvod (1,5 – 2 strany)

- **Motivácia:** Prečo je v dnešnej dobe dôležité zjednocovať výsledky z viacerých zdrojov.
    
- **Problém:** Náročnosť manuálneho prehľadávania viacerých špecializovaných platforiem.
    
- **Cieľ práce:** Návrh a implementácia efektívneho, rýchleho a bezpečného meta vyhľadávača s moderným webovým rozhraním.
    
- **Prínos:** Vytvorenie nástroja, ktorý šetrí čas a poskytuje relevantnejšie výsledky vďaka agregácii.
    

---

## 2. Analýza problematiky (5 – 7 strán)

- **2.1 Analýza potreby:** Detailný rozbor fenoménu informačného prehltenia a fragmentácie dát na internete.
    
- **2.2 Teória vyhľadávania:**
    
    - Princíp fungovania indexových vyhľadávačov (Crawler -> Index -> Query).
        
    - Princíp meta vyhľadávania (bez vlastného indexu, dopytovanie v reálnom čase).
        
- **2.3 Existujúce riešenia:** Rešerš nástrojov ako DuckDuckGo (pôvodne meta), SearXNG (open-source meta vyhľadávač) a ich silné/slabé stránky.
    

---

## 3. Analýza a výber technológií (6 – 8 strán)

- **3.1 Porovnanie programovacích jazykov:**
    
    - Kritériá (výkon, asynchrónnosť, bezpečnosť, skúsenosti).
        
    - Analýza kandidátov: Python, C++, Rust (podrobná obhajoba výberu Rustu).
        
- **3.2 Ekosystém jazyka Rust:**
    
    - **Tokio:** Runtime pre asynchrónne I/O operácie.
        
    - **Serde:** Framework pre efektívnu serializáciu a deserializáciu dát.
        
    - **Axum/Actix-web:** Výber webového frameworku pre backend.
        
    - **Reqwest:** HTTP klient pre komunikáciu s externými API.
        
- **3.3 Výber informačných zdrojov:** Analýza dostupných API (Google, Bing, Wikipedia) a legálne aspekty automatizovaného získavania dát (scraping vs. API).
    

---

## 4. Návrh konceptu a architektúry (5 – 7 strán)

- **4.1 Konceptuálny návrh:** Diagramy toku dát (Data Flow) – od dopytu používateľa cez paralelné spracovanie až po zobrazenie výsledkov.
    
- **4.2 Architektúra systému:** Návrh modulov (Engine Trait, Aggregator, UI).
    
- **4.3 Dizajn používateľského rozhrania:** Drôtové modely (Wireframy) pre hlavnú stránku a zobrazenie výsledkov.
    
- **4.4 Dátový model:** Definícia štruktúr (`struct`) v Ruste pre unifikovaný výsledok vyhľadávania.
    

---

## 5. Implementácia (8 – 10 strán)

- **5.1 Štruktúra projektu:** Rozbor organizácie kódu a modulov (`src/engines`, `src/handlers`, atď.).
    
- **5.2 Paralelné dopytovanie:** Implementácia asynchrónnej logiky (použitie `futures::future::join_all` na súbežné volanie zdrojov).
    
- **5.3 Normalizácia dát:** Proces transformácie rôznych formátov (JSON z API, HTML zo scrapingu) do jednotnej štruktúry.
    
- **5.4 Algoritmus zoraďovania (Ranking):** Logika zoradenia výsledkov (napr. podľa relevancie, rýchlosti zdroja alebo duplicity).
    

---

## 6. Testovanie a hodnotenie výkonu (4 – 5 strán)

- **6.1 Metodika testovania:** Definícia testovacích scenárov a použitých nástrojov na meranie latencie.
    
- **6.2 Výsledky meraní:** Porovnanie časov odozvy pri rôznom počte zapojených zdrojov.
    
- **6.3 Kvalita výsledkov:** Subjektívne a objektívne zhodnotenie relevantnosti nájdených informácií.
    

---

## 7. Dokumentácia a nasadenie (2 – 3 strany)

- **7.1 Nasadenie aplikácie:** Postup kompilácie (`cargo build --release`) a možnosti nasadenia (napr. Docker kontajner).
    
- **7.2 Používateľská príručka:** Návod na spustenie a ovládanie aplikácie doplnený o screenshoty finálneho produktu.
    

---

## Záver (1 – 1,5 strany)

- Zhodnotenie splnenia cieľov práce.
    
- Prínos práce pre autora (nadobudnuté skúsenosti s jazykom Rust).
    
- Návrhy na ďalšie vylepšenia (pridanie personalizácie, podpora ďalších jazykov, pokročilé filtrovanie).
