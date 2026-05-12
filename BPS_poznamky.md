# BPS – Poznámky na učenie

---

## 1.P – CIA triáda a základné pojmy

### CIA – základné vlastnosti bezpečnosti

- **Confidentiality (Utajenie/Dôvernosť)**
  - Len autorizované osoby majú prístup k dátam, vedia ich meniť, zdieľať, pracovať s nimi
  - Ochrana pred neoprávneným zverejnením alebo prístupom
  - Príklad: šifrovanie – bez správneho kľúča sa k dátam nedostaneš

- **Integrity (Integrita)**
  - Dáta sa menia len autorizovaným spôsobom; zabraňuje neoprávnenej manipulácii
  - Príklad: kontrolné sumy (checksums), digitálny podpis
  - Príklad: ECC pamäť opravuje bitové chyby, aby dáta ostali konzistentné

- **Availability (Dostupnosť)**
  - Systémové zdroje a dáta dostupné autorizovaným používateľom vtedy, keď ich potrebujú
  - Príklad: zálohovanie – ak disk zlyhá, dáta sa obnovia

### Ďalšie vlastnosti (rozšírenie CIA)

- **Autentickosť správ** – overenie, že dáta/správa naozaj patria konkrétnej osobe/zdroju
- **Nepopierateľnosť** – záznam o tom, kto, kedy, čo vykonal (logy, audit trail); nikto nemôže poprieť svoju akciu

---

### Základné pojmy

- **Hrozba (Threat)** – potenciálne nebezpečenstvo; môže byť hacker, prírodná katastrofa, zlodej údajov
- **Zraniteľnosť (Vulnerability)** – diera v systéme, ktorú útočník môže zneužiť; príklad: slabé heslo, neaktualizovaný softvér
- **Útok (Attack)** – konkrétny krok/operácia na narušenie bezpečnostnej politiky
  - **Pasívny útok** – len počúvanie, nedotýka sa zdrojov (napr. odpočúvanie WiFi)
  - **Aktívny útok** – priamy zásah; napr. DNS cache poisoning – podvrhnutie odpovede DNS
  - **Vnútorný** – útočník je insider
  - **Vonkajší** – útočník prichádza zvonka
- **Protiopatrenia (Countermeasures)** – zariadenia, procedúry, techniky na minimalizáciu hrozieb
  - Cieľ: **Prevent → Detect → Recover**
  - Pozor: protiopatrenia môžu samy o sebe priniesť nové zraniteľnosti (opravený kód môže mať ďalšie chyby)
- **Riziko** – nikdy nie je nulové; len minimalizujeme
- **Bezpečnostná politika** – formálna množina pravidiel, ktoré regulujú používanie IKT na dosiahnutie CIA vlastností

### Vlastnosti, ktoré by mal mať bezpečný systém

- Autentifikácia, Kontrola prístupu, Dátová utajenosť, Dostupnosť, Dátová integrita, Nepopierateľnosť

---

## 2.P – Šifrovanie

### Symetrické šifrovanie

- Rovnaký kľúč na šifrovanie aj dešifrovanie
- Kľúč musí byť zdieľaný medzi odosielateľom a príjemcom (a musí ostať tajný)
- Potrebujeme silný algoritmus + bezpečnú distribúciu kľúča

#### Blokové šifrovanie

- Dáta sa rozsekajú na bloky (64 alebo 128 bitov) – ECB režim
- Každý blok sa šifruje rovnakým kľúčom
- Použitie: úložiská, pevné disky
- Algoritmy: **DES, 3DES, AES**

#### Prúdové šifrovanie

- Bajt XOR pseudonáhodný kľúč → šifrovaný bajt
- Aproximuje Vermannovu schému (za určitých podmienok absolútne bezpečná)
- Ak každá správa iným kľúčom → absolútne bezpečné
- Ak rovnaký kľúč pre 2 správy → diferenciálna analýza dokáže prečítať obsah!
- Použitie: sieťová prevádzka, HTTPS; algoritmy: **RC4, RC5**

#### Algoritmy symetrického šifrovania

- **DES** – 64 bit vstup, 56 bit kľúč (z 64 bit sa 8 ignoruje); 16 rotácií; dnes zastaraný
- **3DES** – zobrali kľúč 112/168 bit; postup: šifruj kľúčom1 → dešifruj kľúčom2 → šifruj kľúčom3; pri dešifrovaní opačne; pomalší ako AES
- **AES** – 128 bit vstup, kľúč 128/192/256 bit; súčasný štandard

#### Základné útoky na šifrovanie

- **Brute force** – skúša všetky možné kombinácie kľúčov, kým nenájde správny
- **Cryptanalysis** – analytický útok; hľadá matematické slabiny v algoritme; snaží sa odvodiť kľúč

---

### Message Authentication Code (MAC)

- Cieľ: overiť, že správa prišla od konkrétneho používateľa a nebola pozmenená
- **Postup:** správa + tajný kľúč → algoritmus → odtlačok (MAC); odtlačok sa prilepí k správe
- Správa nie je šifrovaná – cieľom nie je skryť obsah, ale ochrániť integritu
- Ak chce útočník zmeniť správu, musí vygenerovať nový odtlačok, ale nepozná tajný kľúč
- Príjemca nezávisle vygeneruje MAC a porovná s priloženým → ak sa zhodujú, správa je autentická
- Pozor: MAC ≠ MAC adresa (kontrola prístupu na sieťovej vrstve)

---

### Hashovacia funkcia

- **Vstup:** ľubovoľná dĺžka; **Výstup:** fixná dĺžka (napr. 256 bitov)
- Jednosmerná – z hashu sa nedá dostať späť k pôvodnému vstupu
- Výsledok by sa mal líšiť v nadpolovičnej väčšine bitov aj pri malej zmene vstupu
  - Príklad: 50-bitový hash, zmena 1 bitu na vstupe → zmena aspoň 26 bitov na výstupe
- **Kolízia** – dve rôzne správy s rovnakým hashom; veľký problém, čo najmenej pravdepodobná
- Algoritmy: **SHA-1, SHA-256, MD5**

---

### Asymetrické šifrovanie

- Dva kľúče: **verejný** (zdieľaný so svetom) a **privátny** (tajný, len u mňa)
- Čo zašifrujem verejným kľúčom, dešifruje len privátny kľúč (a naopak)
- Použitie: šifrovanie správ, digitálne podpisy, výmena kľúčov

#### Diffie-Hellman výmena kľúčov

1. Dohodnú sa na veľkom prvočísle `P` a generátore `g`
2. Každý si vytvorí privátny kľúč
3. Verejný kľúč = `g ^ privátny_kľúč mod P`
4. Vymenia si verejné kľúče
5. Zdieľaný tajný kľúč = `verejný_kľúč_druhého ^ môj_privátny_kľúč mod P`
6. Výsledok na oboch stranách je rovnaký (matematická zákonitosť)

#### Digitálny podpis

- Kombinácia hashu + asymetrického šifrovania
- **Postup odosielateľa:** z správy vytvorím odtlačok (hash) → odtlačok zašifrujem svojím **privátnym** kľúčom → prilepím k správe = digitálny podpis
- **Postup príjemcu:** dešifruje priložený podpis **verejným** kľúčom odosielateľa → nezávisle vypočíta odtlačok správy → porovná; ak sa zhodujú → správa je autentická a nemodifikovaná
- Výhoda: podpisujeme len odtlačok (kratší) → rýchlosť

#### Digitálny certifikát

- Rieši otázku: ako viem, že verejný kľúč naozaj patrí Ferkovi/webovému serveru?
- Certifikačná autorita (CA) za poplatok vytvorí certifikát: moje info + môj verejný kľúč + info o CA → CA to všetko podpíše **svojím privátnym kľúčom**
- Obsah certifikátu: identita vlastníka, verejný kľúč, info o CA, dátum platnosti, domain name
- Platnosť certifikátu: ~1 rok

---

### Náhodné a pseudonáhodné čísla

- **Náhodné (True Random)** – nedeterministické; nedajú sa predpovedať; fyzikálne javy
- **Pseudonáhodné (PRNG)** – generované algoritmom; rovnaký seed = rovnaká postupnosť; deterministické
- Požiadavky na generátor: rovnomerné rozloženie čísel; z predchádzajúcich čísel nie je možné predpovedať ďalšie

---

## 3.P – Používateľská autentifikácia

### Definícia

- Proces overovania, že identita, za ktorú sa vydávame, je v systéme platná
- **Identifikácia** – poviem kto som (login, scan zrenice, ID karta)
- **Verifikácia** – systém overí, či naozaj som to ja (na základe zdieľanej informácie)

### Spôsoby autentifikácie

- **Znalosti** – niečo viem: heslo, PIN, bezpečnostná otázka
- **Vlastníctvo** – niečo mám: token, smart karta, mobilné zariadenie, bankomatová karta
- **Biometria**
  - Statická: odtlačok prsta, rozpoznanie tváre, scan zrenice (nemenia sa)
  - Dynamická: hlas, rukopis (môžu sa meniť)

---

### Zraniteľnosti hesiel

- **Slovníkový útok** – testuje 200-500 tisíc slov zo slovníka (jazykové slová, mená, dátumy)
- **Brute force** – skúša všetky kombinácie
- **Populárne heslá** – 000000, password, 123456
- **Špecifický účet** – cielený útok na konkrétneho používateľa (napr. admin/root)
- **Sociálne inžinierstvo** – získanie hesla manipuláciou osoby
- **Odchod od PC bez zamknutia** – systém prístupný pre každého
- **Heslo na papieriku** – fyzické odcudzenie
- **Rovnaké heslo na viacerých systémoch** – kompromitácia jedného = kompromitácia všetkých
- **Elektrické monitorovanie** – odpočúvanie klávesnice

### Protiopatrenia voči útokom na heslá

- Limit pokusov (po 10 pokusoch varovanie/blokovanie)
- Rýchly mechanizmus na zmenu kompromitovaných hesiel
- Politika silných hesiel (dĺžka, špeciálne znaky)
- Uzamknutie pracovnej stanice po dobe nečinnosti
- Rôzne heslá pre rôzne systémy
- Edukácia používateľov, počítačom generované heslá
- **Reaktívne kontrolovanie** – pravidelná kontrola, upozornenie na slabé heslá
- **Proaktívne kontrolovanie** – systém sám útočí na vlastné heslá a uzamkne slabé

---

### Hashovanie hesiel (Unix)

- Heslo uložené NIE v čistom texte, ale ako odtlačok
- **Soľ (Salt)** – náhodná dodatočná hodnota pridaná k heslu pred hashovaním
  - Každý používateľ má inú soľ → rovnaké heslá majú rôzne odtlačky v DB
  - Sťažuje útok pomocou Rainbow tabuliek
- **Uloženie:** User ID | Salt | Hash code
- **Overenie:** systém zoberie salt z DB + zadané heslo → vypočíta hash → porovná s uloženým
- **Rainbow tabuľky** – predpočítané hashe; ak útočník ukradne DB, hľadá reťazce v tabuľke

---

### Biometrická autentifikácia

- Senzor extrahuje biometrické vlastnosti → uloží do DB (viac skenov pre presnosť)
- Pri overení: nový sken → porovnanie s DB → ak dostatočne podobné → prístup
- **Problémy:** nie vždy rovnaký sken; dva rôzni ľudia môžu mať podobné odtlačky; treba nastaviť threshold
  - Veľký threshold → pustí nelegitímneho
  - Malý threshold → nepustí ani legitímneho (napr. špinavý prst)

### Vzdialená autentifikácia

- Systém pošle **náhodné číslo (challenge)** → používateľ ho upraví (napr. +1) + pridá odtlačok hesla → zašifruje → odošle → server overí
- **Token (Google Authenticator)** – generuje číslo podľa časového intervalu; pošle sa zašifrované
- **Statická biometria na diaľku** – odtlačok skenu + náhodné číslo → zašifrované → server overí
- **Dynamická biometria** – systém posiela aj náhodnú sekvenciu, ktorú treba vysloviť/napísať

### Kerberos

- Protokol autentifikácie; 3 komponenty:
  1. **Authentication Server (AS)** – overí meno + heslo → vydá **Ticket-Granting Ticket** (vstupenka)
  2. **Ticket-Granting Server (TGS)** – overí tiket → vydá tiket pre konkrétnu službu (ako turniket)
  3. **Služba** – overí tiket → povolí prístup (ako usadenie na konkrétne miesto)

---

## 4.P – Kontrola prístupu

### Definícia

- Prevencia neautorizovaného prístupu k systémovým zdrojom
- Chráni: **utajenie** (neprečíta), **integritu** (nezmení), **dostupnosť** (nezmaže)
- Postup: Autentifikácia → Autorizačná databáza → Access Control Function → Systémové zdroje → Audit

### Základné elementy kontroly prístupu

- **Subjekt** – používateľ, skupina, proces, svet
- **Objekt** – súbor, sieť, tlačiareň, disk, databáza
- **Práva** – read, write, execute, delete, create, search
- Tieto 3 veci musia byť v autorizačnej databáze

### Požiadavky kontroly prístupu

- **Spoľahlivý vstup** – neobíditeľný; ochrana pred injekciou škodlivého kódu
- **Najmenej privilégií** – používateľ má len tie práva, ktoré nevyhnutne potrebuje
- **Rozdelenie práv** – viacero adminov s rôznymi právami (žiadny admin so 100% právami)
- **Otvorená/uzatvorená politika** – čo sa smie (ostatné zakázané) alebo čo sa nesmie (ostatné povolené)

---

### DAC – Discretionary Access Control

- **Vlastník** objektu definuje prístupové práva
- Vizualizácia ako **matica**: riadky = používatelia, stĺpce = objekty, bunky = práva (Own, R, W)
- Keď vytvoríš súbor, si jeho vlastníkom a nastavíš práva
- **Problém:** pri veľa dátach a používateľoch ťažko spravovateľné
- **Riešenie:** kontrola prístupu je priamo súčasťou súborového systému (nie voľná matica)

### UNIX File Access Control

- Základná dátová štruktúra pre súbor/adresár: **inode** (len číslo)
- V inode: vlastník, oprávnenia, ďalšie metadáta
- Viacero mien môže ukazovať na jeden inode (hardlinky)
- **12 bitov oprávnení:** user (rwx) | group (rwx) | other (rwx) + špeciálne bity
- **SetUID** – ak je nastavený, proces beží pod identitou vlastníka súboru (nie spúšťajúceho); príklad: `passwd` – každý si vie zmeniť vlastné heslo
- **SetGID** – proces beží pod identitou skupiny vlastníka
- **Sticky bit** – typicky na `/tmp`; práva 777, ale každý môže mazať len vlastné súbory

---

### MAC – Mandatory Access Control

- **Administrátor** (nie vlastník) definuje politiku – čo sa smie a akým spôsobom
- Princíp **nálepkovania (labeling):** súbory dostanú labels (napr. farby) a používatelia tiež
- Prístup k objektu len ak máš správnu nálepku
- Každý subjekt musí prejsť cez kontrolu nálepiek
- Príklad: **SELinux** – určité časti OS sú označené vyššou prioritou; bez správnej nálepky systém nepustí
- **MAC má nadradenosť nad DAC** – ak je SELinux aktívny, MAC kontrola prebehne pred DAC

### RBAC – Role-Based Access Control

- Oprávnenia sa priraďujú **rolám**, nie priamo používateľom
- Každý používateľ má priradenú rolu (alebo viac rolí)
- Rola = skupina operácií, ktoré môžeš/nemôžeš vykonať
- **Hierarchia:** vyššia rola dedí oprávnenia nižšej; príklad: Production Engineer má práva svojej roly + Engineer1 + Engineering Dept
- Treba riešiť: exkluzívne roly (nemôžu byť súčasne), max. počet rolí, povinné kombinácie rolí
- Príklady: Discord (pleb → mod → owner), MAIS

---

## 5.P – Threat Modeling

### SDL – Security Development Lifecycle

- **Fázy:** Požiadavky → Dizajn → Implementácia → Verifikácia → Release → Response (podpora)
- Bezpečnosť treba riešiť od začiatku

### Threat Modeling

- Proces porozumenia rizík číhajúcich na systém a ich minimalizácie protiopatreniami
- **Výhody:** hrozby sa nájdu skoro; môžu sa tým zaoberať aj neexperti
- **Nevýhody:** treba tréning; zaberá čas
- **Kedy:** najlepšie v dizajnovej fáze – čím neskôr, tým drahšie opravy
- Nástroj: **Microsoft SDL Threat Modeling Tool**

### Threat Modeling Process

1. **Diagramming** – dátové tokové diagramy (DFD)
2. **Listovanie hrozieb** – identifikovanie pre každý prvok diagramu
3. **Nájdenie protiopatrení:**
   - Prerobiť návrh
   - Štandardné protiopatrenia
   - Vlastné protiopatrenia (neoverené – riziko)
   - Akceptovanie rizika (riziko minimálne, protiopatrenie extrémne drahé)
4. **Validácia** – overenie, že protiopatrenia nenarušili pôvodné požiadavky

---

### DFD – Dátové tokové diagramy

| Prvok | Popis |
|---|---|
| **External Entity** | Vonkajšia entita mimo kontroly aplikácie (ľudia, externé systémy) |
| **Process** | Kód – natívne spustiteľné súbory, .NET assemblies |
| **Data Store** | Dáta v kľude – databázy, registry |
| **Data Flow** | Tok dát medzi prvkami – volania funkcií, sieťové dáta |

- Entity ↔ Process ✓ | Entity ↔ Entity ✗ | Entity ↔ Data Store ✗
- Process môže komunikovať sám so sebou aj s Data Store

---

### STRIDE model

| Skratka | Hrozba | Vzťah k CIA | Protiopatrenie |
|---|---|---|---|
| **S** – Spoofing | Falšovanie identity | Autentifikácia | IPsec, Hash, Digitálny podpis |
| **T** – Tampering | Neautorizovaná zmena dát | Integrita | ACL, Digitálny podpis |
| **R** – Repudiation | Popieranie vykonanej akcie | Nepopierateľnosť | Silná autentifikácia, Audit/logy |
| **I** – Information Disclosure | Únik informácií | Utajenie | Šifrovanie, ACL |
| **D** – Denial of Service | Degradácia dostupnosti služby | Dostupnosť | ACL, Kvóty (limity) |
| **E** – Elevation of Privilege | Navýšenie práv bez autorizácie | Autorizácia | ACL, roly, skupiny, kontrola prístupu |

---

## 6.P – Malware (Škodlivý kód)

### Definícia

- Program uložený do systému podvodom/neštandardným spôsobom
- Primárny cieľ: kompromitácia CIA vlastností (dáta, aplikácie, OS)

### Klasifikácia malwaru

- Podľa spôsobu šírenia
- Podľa payloadu (škodlivá činnosť)
- Podľa toho, či potrebuje hostiteľa alebo nie:
  - **Potrebuje hostiteľa:** Vírus
  - **Nepotrebuje hostiteľa (samostatný):** Červ, Trojan
  - **Replikuje sa:** Vírus, Červ
  - **Nereplikuje sa:** Spam, Trojan

---

### Vírus

- Potrebuje obeť – program/súbor, do ktorého sa uloží a čaká na aktiváciu (trigger)
- Po spustení infikovaného programu vírus prevezme kontrolu → vykoná payload → spustí pôvodnú aplikáciu
- **Fázy vírusu:**
  1. **Spiaca** – čaká na inicializáciu (žiadna aktivita)
  2. **Triggering** – aktivácia na základe podmienky (dátum, spustenie súboru)
  3. **Propagácia** – vytváranie kópií seba samého do iných programov
  4. **Spustenie** – vykonáva payload
- **Štruktúra vírusu:** infekčný mechanizmus + trigger + payload + hlavná časť programu
- Vírusy sa skrývajú kompresiou – komprimujú súbory a prikladajú sa k nim

#### Klasifikácia vírusov

**Podľa cieľa:**
- **Boot vírus** – prevezme kontrolu pri štarte systému; šíri sa pri boote
- **Infektor súborov** – infikuje spustiteľné súbory (.exe)
- **Makro vírus** – skript v dokumente (Word, Excel)
- **Multipartitný** – viacero spôsobov infekcie naraz

**Podľa spôsobu skrývania:**
- **Šifrovaný** – šifruje sa náhodným kľúčom pri každej infekcii
- **Stealth** – beží skryte v OS, nie je priamo viditeľný
- **Polymorfický** – pri každej infekcii je iný (mení kód)
- **Metamorfický** – pri každej inštancii sa celý kód prepíše; najťažšie detegovateľný

---

### Červ

- Autonómny program; existuje bez nositeľa; automaticky vykonáva payload
- Šíri sa cez: počítačové siete, emaily, zdieľanie súborov, vzdialené prihlásenie
- Vlastnosti: multiplatformový, multi-exploit, rýchle šírenie, polymorfický/metamorfický
- Príklad: **Morris worm** (1988)

### Ďalšie typy

- **Mobilný kód** – program dodateľný bez zmeny na rôzne platformy; nosič vírusov/červov/trojanov
- **Drive-By-Download** – zneužitie zraniteľnosti webového prehliadača pri návšteve stránky
- **Sociálne inžinierstvo:** spam (nosič malwaru), phishing (podvodný email/link), spear-phishing (cielený útok na konkrétnu osobu po prieskume)
- **Trojan** – predstiera funkčnosť legitímnej aplikácie

---

### Payload typy

- **Systémová korupcia** – zničenie dát; **Ransomware** – zašifruje dáta a žiada výkupné
- **Botnet/Agent** – bot dostáva príkazy na diaľku (Remote Control Facility); DDoS, spam, keylogging, šírenie malwaru
- **Krádež informácií:**
  - **Keylogger** – zachytáva stlačené klávesy; môže byť infikovaný ovládač klávesnice
  - **Spyware** – ovládanie kamery, mikrofónu, sledovanie spúšťaných aplikácií
  - **Phishing** – podvodný email s odkazom na falošnú stránku
  - **Spear-phishing** – phishing na mieru po prieskume obete
- **Backdoor** – zadné vrátka; pôvodne na testovanie, nechávajú sa aj v produkcii; odhalenie = zneužitie
- **Rootkit** – neviditeľné časti kódu v OS; typy: perzistentný, pamäťový, user-mode, kernel-mode, VM-based

---

### Antivírus – 4 generácie

1. **1. generácia** – skenuje a hľadá reťazce/vzorky z databázy; nedeteguje neznáme
2. **2. generácia** – heuristický skener + kontrolné sumy; pri inštalácii urobí odtlačky dôležitých súborov; ak sa zmenia → upozornenie
3. **3. generácia** – pamäťo-rezidentný; v reálnom čase sleduje čo sa vykonáva; deteguje anomálie okamžite
4. **4. generácia (Generic Decryption)** – virtuálny procesor spúšťa aplikácie; ak deteguje podozrivé inštrukcie → označí ako problém

---

## 7.P – Email bezpečnosť

### Ako funguje email

1. Klient (Outlook, Gmail) vytvorí email (hlavička: od, komu, predmet, dátum)
2. Kontaktuje najbližší **SMTP server** → server overí oprávnenie a adresu príjemcu
3. SMTP server + **DNS** nájdu MX záznamy cieľovej domény
4. Správa sa odošle na vzdialený SMTP server príjemcu → ten urobí kontroly
5. Príjemca sa prihlási → email sa zobrazí v klientovi (cez POP3/IMAP)

### Email hrozby

- **Spam** – neúnosná záťaž na príjemcu (filtering, výpočtový výkon, úložisko); narušenie dostupnosti
- **Phishing, Malware, Ransomware**
- **Data security** – ochrana sieťovej prevádzky a mailových serverov

---

### Ochrana emailu

#### Reverse DNS

- Forward DNS: doména → IP; Reverse DNS: IP → doména
- Mailový server si pri spojení overí: je hostname deklarovaného servera naozaj zviazaný s touto IP?
- Príklad: server tvrdí, že je `microsoft.com`, ale jeho IP adresa nesedí → email zahodí do spamu

#### SPF – Sender Policy Framework

- DNS záznam obsahujúci zoznam IP adries, ktoré smú odosielať poštu za danú doménu
- Prijímací server sa spýta DNS: existuje SPF záznam pre túto doménu? Je IP odosielateľa na zozname?
- Ak áno → email prijatý; ak nie → spam

#### DKIM – DomainKeys Identified Mail

- Využíva asymetrické šifrovanie (digitálny podpis)
- Odosielací mailový server podpíše každý email **privátnym kľúčom**
- Odtlačok emailu sa podpíše privátnym kľúčom a priloží k emailu
- Prijímací server si z DNS stiahne **verejný kľúč** odosielacieho servera → overí podpis
- Ak sedí → email doručí; ak nie → spam alebo zahodí

---

### PGP – Pretty Good Privacy

- Šifrovaný prenos na úrovni samotného emailu (end-to-end)

#### PGP autentifikácia

1. Z správy vytvorí odtlačok (hash)
2. Odtlačok podpíše **privátnym kľúčom** odosielateľa
3. Výsledok skomprimuje (kompresia zahmlí dáta + zmenší veľkosť)
4. Príjemca: dekomprimuje → dešifruje odtlačok **verejným kľúčom** odosielateľa → nezávisle vypočíta odtlačok → porovná

#### PGP šifrovanie

1. Správu skomprimuje
2. Vygeneruje náhodný **session key** (dočasný symetrický kľúč)
3. Správu zašifruje symetricky session kľúčom (symetrické = rýchle)
4. Session kľúč zašifruje **verejným kľúčom príjemcu**
5. Oboje spojí do jednej správy
6. Príjemca: privátnym kľúčom dešifruje session kľúč → session kľúčom dešifruje správu → dekomprimuje

#### PGP autentifikácia + šifrovanie

- Najprv: odtlačok + podpis → kompresia → symetrické šifrovanie + zašifrovaný session kľúč
- Príjemca: dešifruj session kľúč → dešifruj správu → dekomprimuj → over odtlačky

#### Radix-64

- Starý emailový systém prenášal len 6-bitové znaky; moderné dáta sú 8-bitové
- Radix-64: 3 bajty (24 bit) → 4 × 6-bitové znaky → email sa zväčší o 1/3
- Znaky: a-Z, 0-9, +/

#### Dôvera ku kľúčom v PGP – Web of Trust

- Na rozdiel od certifikátov (CA) PGP nemá centrálnu autoritu
- **Web of Trust** – kľúč je viazaný na emailovú adresu; čím viac emailovej komunikácie, tým väčšia dôvera
- Verejné PGP kľúčové servery – možno tam zverejniť kľúč, klienti ho stiahnu automaticky

---

## 8.P – Firewall a IPS

### Prečo potrebujeme firewall

- Každý počítač pripojený na internet je potenciálne ohrozený
- Cieľ: filtrovanie všetkej komunikácie cez centrálny bod; nesmie existovať spôsob, ako ho obísť
- Definuje bezpečnostná politika: čo sa smie a nesmie komunikovať

### Bezpečnostná politika firewallu

- Kontrola **služieb** – webový server, mailový server, DNS
- Kontrola **smeru** – komunikácia von / komunikácia dovnútra
- Kontrola **používateľa** – na základe IP adresy, MAC adresy
- Kontrola **správania** – napr. sťahovanie súborov

### Schopnosti a limitácie

- **Schopnosti:** centrálny bod, monitorovanie, súčasť VPN
- **Limity:** ak si používatelia prinášajú vlastné zariadenia s internetom → obídenie firewallu

---

### Typy firewallov

#### Bezstavový (Packetový) firewall

- Pracuje na vrstve 3 (sieťovej); vidí každý packet zvlášť
- Prechádza pravidlá → BLOCK (zahodí) alebo ALLOW (prepustí)
- Nevie, čo bolo pred packetom a čo príde po ňom (bez kontextu)
- **Výhody:** jednoduchý, rýchly
- **Nevýhody:** pri fragmentovaných dátach nevidí škodlivý obsah; náchylný na TCP/IP chyby; pri veľa weboch treba strašne veľa pravidiel

#### Stavový firewall

- Sleduje **stav TCP spojenia** (SYN → SYN-ACK → ACK)
- Vie usúdiť: ak som ja inicioval komunikáciu, odpoveď ju prepúšťam automaticky
- Ak z internetu príde komunikácia, ktorú som si nevyžiadal → blokuje
- **Výhody:** inteligentnejší, menej pravidiel; efektívnejší

#### Aplikačný firewall (Proxy server)

- Pracuje na vrstve 7 (aplikačnej)
- **Proxy** je prostredník: klient → proxy → server (a späť)
- Vidí kompletný protokol (URL, obsah stránky, sťahované súbory)
- Môže blokovať konkrétne stránky, filtrovať obsah, kontrolovať súbory na malware
- **Výhody:** najdetailnejší pohľad; najbezpečnejší
- **Nevýhody:** proxy musí poznať každý protokol zvlášť; pomalší

#### Circuit level gateway

- Pracuje na vrstve 5/6 (session layer)
- Proxy na úrovni spojení, nie aplikačných protokolov
- Komunikácia A→proxy (protokol P1), proxy→B (protokol P2) – môžu byť rôzne
- Vytvára virtuálne spojenie; prenáša dáta bez hlbokej inšpekcie obsahu

---

### Ďalšie typy firewallov

- **Bastion host** – fyzický systém s firewallom; len nevyhnutné služby; vyžaduje autentifikáciu; ideálne read-only; jednoduchý na kontrolu
- **Host-Based firewall** – v OS každého PC; von = povolené, dovnútra = zakázané (výnimky: lokálna sieť); ušitý na mieru
- **Personal Firewall** – obdoba Host-Based na domácom PC

### Firewall konfigurácia

- **Štandardná:** Internet → External firewall → DMZ (web/email/DNS servery) → Internal firewall → Interná sieť
- **DMZ (demilitarizovaná zóna)** – medzi dvoma firewallmi; verejne prístupné servery
- **Distribuovaná konfigurácia** – každá podsieť má vlastný firewall

---

### IPS – Intrusion Prevention System

- Samostatné zariadenie spolupracujúce s firewallom; automaticky eliminuje útoky
- Prehľadáva každý packet a hľadá vzory/reťazce škodlivého kódu
- Ak nájde → zahodí packet alebo dá firewallu pokyn blokovať všetku komunikáciu z danej IP
- Princíp: **vzorová detekcia** (pattern matching) alebo **anomálna detekcia** (odchýlka od normálu)

#### NIPS – Network IPS

- Monitoruje **celú sieť**; nachádza sa v sieťovej infraštruktúre
- Hľadá vzory alebo anomálie v sieťovej prevádzke

#### Hostiteľský IPS

- Nainštalovaný priamo v **OS konkrétneho zariadenia**
- Sleduje abnormálne stavy systému; posiela notifikácie alebo priamo zasiahne
- Funguje na rovnakom princípe ako NIPS (vzory + anomálie)

---

## 9.P – Zálohovanie a RAID

### Záloha vs Archív

- **Záloha** – kópia dát; v prípade chyby rýchla obnova do konzistentného stavu; originál ostáva
- **Archív** – kópia dát s ktorými sa narába len občasne; originál sa po archivácii zvyčajne zmaže (šetrí miesto)
- Záloha môže byť aj dôkaz autorstva (forenzná analýza, legislatíva)

### Prečo zálohovať

- Obnova po neautorizovanej modifikácii alebo zmazaní dát
- Legislatívno-právne požiadavky (zdravotníctvo, financie, poľnohospodárstvo – rôzne pravidlá)
- Forenzná analýza – zálohy pomáhajú odhaliť čo sa stalo pri bezpečnostnom incidente
- Potvrdenie autorstva (napr. fotografie)

---

### Tvorba záloh

#### Fault-Tolerant System

- Redundantné hardware komponenty: napájanie, procesory, pamäte, prenosové linky, disky
- Ak jeden komponent vypadne → systém beží ďalej
- Zahŕňa aj redundantné internetové pripojenie
- Cloud (AWS/Azure) ponúka architektúry pre 99.9999% dostupnosť

#### Hierarchical Storage System

- Hierarchia úložísk podľa: kapacity, rýchlosti prístupu, spoľahlivosti, ceny
- Príklad: RAM (najrýchlejšia) → SSD → HDD → pásková mechanika (najpomalšia, najlacnejšia)

#### Disk Mirroring, SAN, NAS (pozri nižšie)

#### Logovanie a obnova

- Záznamy: kedy, kto, kde sa zálohovalo → management a orientácia v zálohách

---

### RAID

Množina nezávislých diskov vytvárajúcich jeden logický celok.

- **RAID 0 (Striping)** – dáta rozkladá striedavo na všetky disky; väčšia kapacita, rýchle paralelné čítanie; ak odíde 1 disk → stratíme **všetko**; nebezpečný
- **RAID 1 (Mirroring)** – rovnaké dáta na 2+ diskoch; zápis pomalší, čítanie rýchle (paralelné); ak odíde disk → systém stále funguje; čas na výmenu
- **RAID 5 (Striping + Parita)** – min. 3 disky; dáta + parita rozložené na všetkých; parita: `Ap = A1 XOR A2 XOR A3`; kapacita = n-1; ak odíde 1 disk → dopočítame z parity
- **RAID 6** – ako RAID 5 ale parita na 2 diskoch; prežije výpadok **2 diskov** súčasne; primárny cieľ = spoľahlivosť
- **RAID 10 (RAID 1+0)** – min. 4 disky; dvojice v RAID 1 → spojené v RAID 0; výkon + spoľahlivosť; za určitých okolností prežije výpadok 2 diskov (napr. Disk0 a Disk3)

*Bezpečné RAIDy: RAID 1, 5, 6, 10 (RAID 0 nie)*

---

### SAN – Storage Area Network

- Dedikované úložné zariadenie s RAIDom
- Vyexportuje **virtuálne blokové disky** serverom cez sieť
- Servery nemusia mať vlastné disky; SAN rieši spoľahlivosť, kapacitu, efektívnosť
- Databázy (napr. Oracle) môžu pristupovať priamo k zariadeniu bez súborového systému

### NAS – Network Attached Storage

- Skrinka s RAIDom; exportuje **súborový systém** cez sieť (NFS, CFS, AFS)
- Jednoduchší prístup ako SAN; pripojíš si virtuálny disk po sieti
- Použitie: zdieľanie súborov vo firme, domáce NAS

---

### Možnosti zálohovania

- Zálohovací software
- Externé médiá: prenosné disky, optické (CD/DVD), páskové zariadenia, USB
- **Labeling** – označenie zálohy (napr. "DOVOLENKA 2021")
- **Indexovanie a archivácia** – číslovanie médií
- **Registre záloh** – pri Windowse dôležité (konfigurácia systému)
- **Bootable záloha** – spustiteľná verzia OS
- **Cloud záloha** – odporúčaný súčasný spôsob; treba riešiť bezpečnosť a spoľahlivosť cloudu

---

### Stratégia zálohovania

#### Exkluzívny prístup

- Počas zálohovania nikto k súborom nepristupuje → záloha je konzistentná
- **Snapshot** – zmrazenie systému v čase; nastavenie read-only; zálohujem zmrazené dáta; súborový systém sám rieši ďalšie zmeny

#### Typy záloh

| Typ | Čo zálohujem | Obnova | Potrebné miesto |
|---|---|---|---|
| **Plná** | Všetky dáta | Len plná záloha | Najviac |
| **Diferenciálna** | Zmeny od poslednej **plnej** | Plná + posledná dif. | Stredne |
| **Inkrementálna** | Zmeny od poslednej **akejkoľvek** | Plná + všetky inkr. pred chybou | Najmenej |
| **Delta** | Zmenené časti v súboroch (byte level) | Podobne ako inkr. | Najmenej |
| **Version (Git)** | Každá verzia súboru | Ľubovoľná verzia | Záleží |

---

### Safeguarding Backups

- **Environmentálna protekcia** – fyzická ochrana pred poškodením (požiar, voda, krádež)
- **Onsite** – záloha v tej istej budove; rýchlo dostupná; riziko spoločnej katastrofy
- **Offsite** – záloha mimo budovy (iné mesto, vzdialené DC, sejf v banke); treba riešiť transport a dostupnosť
- Odporúčanie: **3-2-1 pravidlo** – 3 kópie, 2 rôzne médiá, 1 offsite

### Odstránenie/zničenie dát

- **Formátovanie nestačí** – bloky sú len označené ako voľné, dáta fyzicky ostávajú
- **Mazanie** – označí blok ako voľný, dáta zostávajú; treba špeciálny SW, ktorý premazáva náhodnými 0 a 1
- **Skartovanie** – fyzické zničenie disku; najspoľahlivejšie
