# Pakeitimų istorija

Visi reikšmingi šio projekto pakeitimai dokumentuojami šiame faile.

Versijos numeruojamos pagal Semantic Versioning principą:

MAJOR.MINOR.PATCH

- MAJOR – architektūriniai ar esminiai logikos pakeitimai  
- MINOR – naujos funkcijos  
- PATCH – klaidų taisymai ir smulkūs patobulinimai  

---

## [4.1.0] - 2026-02-13

Rate Boost stabilizacija, signalizacijos integracija į mokymąsi ir filtrų nusidėvėjimo modelis.

---

### Pridėta

#### Stabilizuotas Rate-Based Boost (v4.1)

- 15 min slankus CO₂ kilimo langas
- 2 iš eilės patvirtinimai prieš startą
- Cooldown mechanizmas tarp boost aktyvacijų (20 min)
- Soft-hold nutraukimas, jei CO₂ krenta sparčiau nei -3 ppm/min
- Boost leidžiamas tik jei CO₂ ≥ (autoThreshold - 50)
- Minimalus `boost_log` (vidinis 24h žurnalas)

---

#### Alarm-aware Learning

- CO₂ log įrašuose saugoma signalizacijos būsena
- Adaptacijai naudojami tik įrašai, kai:
  - `alarm === "disarmed"`
- Pridėtas `ALARM_BLOCK` indikatorius node status’e
- Mokymasis nebevyksta iš tuščių namų duomenų

---

### Pakeista

- Rate logika perkelta į stabilų deterministinį modelį
- Boost nebeaktyvuojamas dėl trumpalaikių 5 min šuolių
- Learning apsaugos išplėstos (Alarm-aware filtravimas)
- Node komentarai atnaujinti (v4.1 dokumentacija)
- Telemetrija nebeužteršiama klaidingais „0“ rate duomenimis

---

### Architektūra

Sistema suskirstyta į aiškius modulius:

- Control Flow (Decide Desired)
- Learning Flow (CO₂ threshold adaptacija)
- Boost stabilizacija
- Filtrų modelis
- Telemetrija

Visi moduliai veikia nepriklausomai, bet dalijasi `flow` kintamaisiais.

---

### Patikimumo patobulinimai

- Apsauga nuo klaidingo mokymosi tuščiuose namuose
- Boost stabilizacija prieš Netatmo 5 min atnaujinimo efektą
- 03:00 LT profilio klaidos eliminavimas
- Soft-stop mechanizmas boost režime
- Vidinė 14 dienų CO₂ istorija kalibravimui

---

## [4.0.0] - 2026-02-12

Pagrindinis leidimas – sujungta rekuperatoriaus valdymo logika ir adaptyvus CO₂ mokymosi modulis.

### Pridėta

Adaptyvus CO₂ mokymosi modulis:
- Automatinis kasdienis įsijungimo slenksčio perskaičiavimas
- Profilinė analizė:
  - Darbo dienomis – aktyvūs laikotarpiai
  - Savaitgaliais – 24h analizė
- Proporcinė adaptacija (K=0.2)
- Dienos pokytis ribojamas ±25 ppm
- Slenksčio ribos: 550–950 ppm

Learning Guards:
- Stability Lock
- Gap Guard

Rate-Based Boost (ankstesnė versija):
- CO₂ kilimo analizė (ppm/min)
- 10 min hold mechanizmas

Vonios OVR su histereze:
- >80% įjungimas
- <70% + 5 min išjungimas

STOP FLAGS:
- Kritinis blokavimas

Windows Guard:
- >10 min atviri langai → išjungimas

Alarm fallback mechanizmas:
- Paskutinės žinomos būsenos naudojimas po HA restart

---

## [3.x]

- Pradinė rate-based boost logika
- Fiksuotas CO₂ slenkstis
- Bazinis STOP + OVR prioritetų modelis