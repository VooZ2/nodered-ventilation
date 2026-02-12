# Pakeitimų istorija

Visi reikšmingi šio projekto pakeitimai dokumentuojami šiame faile.

Versijos numeruojamos pagal Semantic Versioning principą:

MAJOR.MINOR.PATCH

- MAJOR – architektūriniai ar esminiai logikos pakeitimai
- MINOR – naujos funkcijos
- PATCH – klaidų taisymai ir smulkūs patobulinimai

---

## [4.0.0] - 2026-02-12

Pagrindinis leidimas – sujungta rekuperatoriaus valdymo logika ir adaptyvus CO₂ mokymosi modulis.

### Pridėta

Adaptyvus CO₂ mokymosi modulis:
- Automatinis kasdienis įsijungimo slenksčio perskaičiavimas
- Profilinė analizė:
  - Darbo dienomis – analizuojami tik nustatyti aktyvūs laikotarpiai
  - Savaitgaliais – visa 24 val. paros juosta
- Proporcinė adaptacija:
  - delta = -round((avgRate - targetRate) * K)
  - Dienos pokytis ribojamas iki ±25 ppm
- Slenksčio ribos: 550–950 ppm

Mokymosi apsaugos (Learning Guards):
- Stability Lock:
  - Mokymasis nevykdomas jei:
    - max CO₂ < 650 ppm
    - paros svyravimas < 100 ppm
- Gap Guard:
  - Mokymasis blokuojamas jei prarasta daugiau nei 35% tikėtinų duomenų taškų

CO₂ kilimo greičio režimas (Rate-Based Boost):
- Reaguoja į CO₂ kilimo tempą (ppm/min)
- Lygiai:
  - ≥10 → 55%
  - ≥18 → 65%
  - ≥30 → 80%
- 10 minučių išlaikymo mechanizmas
- Boost neaktyvuojamas jei CO₂ < 600 ppm

Alarm fallback mechanizmas:
- Po Home Assistant persikrovimo naudojama paskutinė žinoma signalizacijos būsena
- Apsaugo nuo netikėto sistemos įsijungimo

Langų saugiklis:
- Jei langai atidaryti ilgiau nei 10 min – sistema išjungiama
- Automatinis atstatymas uždarius langus

Vonios OVR su histereze:
- Įjungimas >80% drėgmės
- Išjungimas tik jei <70% išsilaiko 5 min

STOP FLAGS blokavimas:
- Kritinės būsenos atveju sistema nedelsiant stabdoma
- Generuojamas pranešimas apie būsenos pasikeitimą

Telemetrija:
- sensor.current_co2_rate
- binary_sensor.rate_boost_active
- sensor.komfovent_logic_mode
- Vidinė ~14 dienų istorija kalibravimui

### Pakeista

- Mokymosi slenkstis integruotas į pagrindinę valdymo logiką
- Standartizuota node pavadinimų struktūra (angliški pavadinimai)
- Node komentarai palikti tik lietuvių kalba
- Patobulintas desired_key generavimas (įtrauktas recup_is_on)
- Rate telemetrija neberašo dirbtinių „0“ reikšmių

### Architektūra

- Atskirtas:
  - Valdymo srautas (Control Flow)
  - Mokymosi srautas (Learning Flow)
- RBE filtras prieš komandų vykdymą
- Veiksmų maršrutizavimas pagal „kind“
- Moduliniai komandų paruošimo mazgai

### Patikimumo patobulinimai

- HA persikrovimo apsauga
- Alternatyvūs HA būsenų skaitymo keliai
- Ištaisyta 03:00 LT profilio logikos klaida
- Apsauga nuo savaitgalio/darbo dienos neatitikimo

---

## [3.2.2]

### Pataisyta

- Teisingas CO₂ entity mapping
- Sutvarkytas recup_is_on laukas
- Ištaisyta rate telemetrija (neberašo 0)
- Suderintas schedule pavadinimas ir crontab

---

## [3.x]

- Pradinė rate-based boost logika
- Fiksuotas CO₂ slenkstis
- Bazinis STOP + OVR prioritetų modelis
