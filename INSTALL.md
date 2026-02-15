# 🛠️ Diegimo ir Konfigūravimo Gairės  
## (Installation & Configuration Guidelines)

Šis projektas yra asmeninis „pasidaryk pats“ (DIY) tipo vėdinimo optimizavimo sprendimas, sukurtas specifinei namų ekosistemai. Sistema nėra universali, todėl jos naudojimas kitoje aplinkoje reikalauja individualaus pritaikymo ir techninio supratimo.

---

# ⚠ Atsakomybė ir Pritaikymas  
## (Responsibility and Adaptation)

### 🔹 Asmeninė atsakomybė  
Sistemos pritaikymas tavo namams, jutiklių pavadinimų (Entity ID) keitimas ir logikos derinimas yra tik tavo atsakomybė.

### 🔹 DI pagalba  
Kadangi projektas sukurtas bendradarbiaujant su **Gemini** ir **ChatGPT**, primygtinai rekomenduojama naudoti šiuos įrankius kodo analizei bei pritaikymui tavo poreikiams.  
Dirbtinis intelektas puikiai supranta šių srautų struktūrą ir gali padėti perrašyti mazgus pagal tavo naudojamus jutiklius.

### 🔹 Jokio palaikymo  
Autorius neteikia jokio techninio palaikymo, konsultacijų ar klaidų taisymo paslaugų.  
Jei sistema neveikia tavo aplinkoje – sprendimų ieškokite savarankiškai.

---

# 📂 Sistemos Failai  
## (System Files)

Norint, kad sistema veiktų korektiškai (**v4.3.0**), rekomenduojama importuoti visus modulius nurodyta tvarka:

- **komfovent-control.json**  
  Pagrindinis valdymo variklis, priimantis vėdinimo sprendimus.

- **komfovent-learning.json**  
  Adaptyvus CO₂ slenksčio skaičiavimas ir paros analizė.

- **komfovent-logging.json**  
  Centralizuotas duomenų kaupimas (v5 schema) ir telemetrija.

- **komfovent-filters.json**  
  Filtrų efektyvaus darbo stebėjimas ir nusidėvėjimo modelis.

- **komfovent-ai_gateway.json**  
  Sąsaja su Telegram botu ir Gemini API „šešėlinei analizei“.

---

# ⚙️ Minimalūs Reikalavimai  
## (Minimum Requirements)

Prieš paleidžiant srautus, Home Assistant aplinkoje privalo egzistuoti šie elementai:

---

## 1️⃣ Pagalbiniai elementai (Helpers)

- **input_number.co2_on_threshold**  
  Skaitinis helperis, kurį sistema automatiškai atnaujina kas parą (03:00 LT).

---

## 2️⃣ Katalogų struktūra (File System)

Node-RED konteineryje privalo būti sukurti šie katalogai (arba atitinkamai sumontuoti „volumes“):

- `/data/logs/komfovent/`  
  Skirtas JSONL duomenų kaupimui.

- `/data/state/komfovent/`  
  Skirtas filtrų būsenos ir kitų kintamųjų išsaugojimui po sistemos persikrovimo.

---

## 3️⃣ Centralizuota konfigūracija (Global Settings)

Visi HA jutiklių pavadinimai (Entity ID) privalo būti sukonfigūruoti vienoje vietoje – mazge:

**„Init: Global Settings“**  
(skiltyje *Komfovent - Module: Logging*)

Pakeitus nustatymus, būtina paspausti **Inject** mygtuką, kad reikšmės būtų įrašytos į globalią atmintį.

---

# 🤖 DI Indėlis  
## (AI Disclaimer)

Šis projektas (architektūra, programinis kodas ir ši dokumentacija) sukurtas glaudžiai bendradarbiaujant su dirbtinio intelekto modeliais (**Gemini** ir **ChatGPT**).  

Tai užtikrina nuoseklų duomenų struktūrizavimą, tačiau reikalauja žmogaus priežiūros diegimo metu.