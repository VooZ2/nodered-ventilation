# Pakeitimų istorija

Visi reikšmingi šio projekto pakeitimai dokumentuojami šiame faile.

Versijos numeruojamos pagal Semantic Versioning principą:

MAJOR.MINOR.PATCH

- MAJOR – architektūriniai ar esminiai logikos pakeitimai  
- MINOR – naujos funkcijos  
- PATCH – klaidų taisymai ir smulkūs patobulinimai

---

# \[5.0.0\] -- 2026-03-16

### Predictive ventiliacijos valdymas ir AI Gateway architektūra

Ši versija yra esminis architektūrinis atnaujinimas. Sistema papildyta
**predictive valdymo moduliu**, leidžiančiu prognozuoti CO₂ augimą ir iš
anksto adaptuoti ventiliacijos intensyvumą. Taip pat įdiegtas **AI
Gateway sluoksnis**, leidžiantis interpretuoti sistemos būseną ir
istoriją per Telegram sąsają.

------------------------------------------------------------------------

## Pridėta

### Predictive Control Engine

-   Įdiegtas **predictive režimas**, galintis adaptuoti ventiliaciją
    pagal CO₂ prognozę.
-   Skaičiuojamos **30 ir 60 min CO₂ prognozės** pagal istorinius
    telemetrijos duomenis.
-   Naudojamas **bucket-based elgsenos modelis** ir rizikos įvertinimas.
-   Predictive aktyvacija priklauso nuo modelio brandos, prognozės ir
    sistemos būsenos.

### AI Gateway

-   Įdiegtas **AI interpretacijos sluoksnis** per Telegram.
-   AI gali interpretuoti:
    -   sistemos būseną
    -   telemetrijos istoriją
    -   predictive modelio būseną.

### Audit Snapshot

-   Įdiegtas struktūruotas **audit snapshot** generavimas iš logų.
-   Naudojamas AI analizei ir diagnostikai.

------------------------------------------------------------------------

## Pakeista

### Control log schema

Control log papildytas naujais laukais:

-   `predictive_active`
-   `predictive_risk`
-   `co2_forecast_30`
-   `co2_forecast_60`

Schema atnaujinta į **v8 (Predictive-aware Control Logging)**.

### Control prioritetų modelis

Predictive režimas įtrauktas į valdymo prioritetų seką:

    STOP FLAGS
    OVR
    Alarm Away
    Windows
    Hood
    Predictive
    Day/Night
    Rate Boost

Predictive negali perrašyti aukštesnio prioriteto saugos režimų.

------------------------------------------------------------------------

## Architektūra

Nuo šios versijos sistema turi tris pagrindinius sluoksnius:

    Control Logic
    Predictive Engine
    AI Gateway

AI sluoksnis interpretuoja sistemos veikimą, bet **tiesiogiai jos
nevaldo**.

------------------------------------------------------------------------

## Rezultatas

Sistema dabar gali:

-   prognozuoti CO₂ augimą
-   adaptuoti ventiliacijos intensyvumą iš anksto
-   pateikti AI pagrįstą sistemos veikimo interpretaciją.

---

# [v4.4.2] – 2026-02-18

Pilnas DI stabilizavimo atnaujinimas: duomenų sinchronizacija, „nėra duomenų“ klaidos pašalinimas ir toninio stiliaus suvienodinimas

Šioje versijoje Komfovent DI sluoksnis buvo iš esmės peržiūrėtas ir stabilizuotas. Pagrindinis tikslas – pašalinti situacijas, kai asistentas atsakydavo „neturiu duomenų“, nors realūs sistemos parametrai buvo prieinami. Taip pat suvienodintas atsakymų tonas ir užtikrinta teisinga prioritetų interpretacija hipotetiniuose scenarijuose.

---

## Patobulinta

### DI duomenų kontekstas (AI Context)

- Atkurta ir sustiprinta Home Assistant būsenų paieškos logika (fallback į kelias `states` struktūras).
- Sinchronizuotas `last_desired` objektas su DI kontekstu – užtikrintas teisingas `fan_in`, `fan_out` ir `pressure_state` perdavimas.
- Įtrauktos aktyvios Boost, Hold ir Cooldown būsenos su realiu laiko skaičiavimu.
- Stabilizuota slėgio logika (positive / negative / balanced) pagal realius ventiliatorių santykius.

### Prompt Builder optimizacija

- Įdiegtas dinaminis „gating“ – logai siunčiami tik jei klausiama apie praeitį, taisyklės tik jei klausiama apie scenarijus.
- Pašalintos „n/a“ reikšmės – jei duomenų nėra, sakinys tiesiog neformuojamas.
- Supaprastintas faktų blokas – formuojamas tik iš realiai turimų parametrų.
- Įtvirtinta griežta toninė kontrolė: be pasisveikinimų, be empatijos, be techninių kintamųjų pavadinimų.

---

## Loginiai pataisymai

- Ištaisyta prioritetų interpretacija hipotetiniuose atsakymuose.
- Aiškiai apibrėžta, kad aukštesnis prioritetas visada blokuoja žemesnį.
- Užtikrinta, kad Gartraukis ir Langai turi aukštesnį prioritetą nei Rate Boost.
- Stabilizuota Boost scenarijų analizė (≥12 ppm/min + CO₂ slenkstis).

---

## Ištaisyta

- Pašalinta kritinė klaida, kai DI teigdavo „neturiu informacijos apie CO₂“, nors sensorius veikė.
- Ištaisyta klaida, kai ppm/min rodiklis buvo ignoruojamas, jei Boost neaktyvus.
- Pašalintas neteisingas teiginys, kad Rate Boost turi aukštesnį prioritetą nei Gartraukis.
- Sumažintas tokenų naudojimas (~30–50% mažesnis prompt dydis įprastais klausimais).

---

## Rezultatas

DI dabar:

- Atsako faktais, ne spekuliacijomis.
- Naudoja realius sensorių duomenis.
- Teisingai interpretuoja sistemos prioritetus.
- Kalba trumpai, profesionaliai ir draugiškai („Tu“ forma).
- Stabiliai veikia be „nėra duomenų“ atsakymų.

---

## v4.4.1 — Bugfix (AI context + fan desired + hood/state consistency)

### Pataisyta
- **AI context (Control):** sutvarkytas `fan_desired` konteksto formavimas, kad būtų teisingai skaičiuojamas iš `flow.last_desired` ir atskirai pateikiami:
  - `fan_desired_in`
  - `fan_desired_out`
  - `fan_desired` (kaip max iš IN/OUT, jei bent vienas yra validus)
- **Logging (Control):** užtikrinta, kad į control log įrašoma `intake_temp` reikšmė kartu su `fan_desired_in/out` (istorijai ir analitikai).
- **Actions/OVR:** patvirtintas ir sutvarkytas OVR relės valdymas „tik pasikeitus būsenai“, kad į HA nesiųstų perteklinių `switch_on/off` (mažiau triukšmo ir apkrovos).
- **Hood state:** suderintas HOOD būsenos (active/post_run) sekimas ir `desired_key` įtraukimas (kad RBE teisingai praleistų fazių pasikeitimus).

---

## [4.4.0] - 2026-02-18

### Gartraukio (HOOD) režimo integracija, atskiras IN/OUT valdymas ir stabilizuotas post-run mechanizmas

---

## Pridėta

### Pilna gartraukio (HOOD) režimo integracija į Control modulį

- Gartraukio lygio nustatymas pagal rozetės galią (`sensor.hood_socket_power`).
- 3 lygiai su atskirais IN/OUT procentais:
  - **L1:** IN 80% / OUT 45%
  - **L2:** IN 90% / OUT 50%
  - **L3:** IN 100% / OUT 55%
- Automatinis post-run režimas (5/8/12 min. pagal sesijos max lygį).
- Post-run fazėje subalansuotas vėdinimas: **IN 65% / OUT 65%**.
- HOOD būsenų sekimas:
  - `hood_state`: `idle | active | post_run`
  - `hood_level`: 1–3
  - `hood_level_max_session`
  - `hood_postrun_until`

### Atskiras tiekiamo ir ištraukiamo oro valdymas

- Įdiegti `fan_in` ir `fan_out` laukai vietoje vieno bendro `fan`.
- `Build Actions` atnaujintas:
  - Jei yra atskiri HA entitetai → siunčia atskiras IN ir OUT komandas.
  - Jei nėra → fallback į bendrą fan entitetą.

### RBE raktas išplėstas

- `desired_key` dabar apima:
  - `fan_in`
  - `fan_out`
  - `hood_state`
  - `hood_level`
- Užtikrina, kad post-run perėjimas realiai suveiktų ir nebūtų blokuojamas RBE.

---

## Pakeista

### HOOD prioritetų logika

- Gartraukis ignoruojamas, jei `windows_long_open = true`.
- HOOD blokuoja Rate Boost aktyvaciją.
- HOOD turi aukštesnį prioritetą nei Day/Night režimas.

### Post-run OFF debounce stabilizacija

- Ištaisyta logikos spraga, dėl kurios post-run kartais neprasidėdavo dėl galios šokinėjimo.
- OFF kandidatas neberesetinamas triukšmo zonoje (60–75W).

### Build Actions struktūra

- Vietoje netiesioginių laukų dabar formuojamas `msg.payload` masyvas:
  - `switch_on`
  - `switch_off`
  - `set_number`
- Suderinta su `Split Actions` ir `Route Action by Kind` mazgais.

### Control Log schema praplėsta

- Pridėti:
  - `fan_in_desired`
  - `fan_out_desired`
- `fan_desired` dabar skaičiuojamas kaip `max(fan_in, fan_out)` suderinamumui.

---

## Sutvarkyta

### RBE blokavimo klaida

- Ankstesnė logika stebėjo tik `fan`, todėl IN/OUT pokyčiai nebuvo laikomi pasikeitimu.
- Dėl to post-run režime fan procentai realiai neatsinaujindavo.

### Armed Boost suderinamumas

- `Force OFF After Armed Boost` atnaujintas naudoti `fan_in` ir `fan_out`.

### Post-run nepersijungimo problema

- Ištaisyta situacija, kai galios reikšmės trumpam pakildavo virš OFF ribos ir neleisdavo stabiliai pereiti į post-run.

---

## Architektūrinė pastaba

Nuo šios versijos:

- Sistema nebėra vieno fan procento logika.
- Control modulis tapo dvikanalis (Supply/Extract).
- HOOD režimas veikia kaip slėgio kompensavimo mechanizmas, o ne papildomas ištraukimas.

---

## [4.3.1] - 2026-02-16

### Tiekiamo oro temperatūros, OVR integracija ir DI komunikacijos standartas

**Pridėta:**
- Įdiegta pilna tiekiamo oro temperatūros (`intake_temp`) stebėsena visuose lygmenyse: nuo HA sensoriaus iki DI asistento.
- Pridėtas išorinio valdymo (OVR) būsenos sekimas tiesiai iš `bath_ovr_active` loginio kintamojo ir HA relės.
- Duomenų schema atnaujinta į **v7 (Historical Intake Data)**, leidžiančią kaupti istorinius temperatūros duomenis būsimai analizei.

**Pakeista:**
- **DI bendravimo standartas:** Sugriežtintos Gemini taisyklės – uždraustas Markdown formatavimas (žvaigždutės), panaikinti mandagumo filtrai ir įvestas tiesioginių faktų prioritetas.
- **Matricos brandos komunikacija:** DI asistentas instruktuotas nebenaudoti techninio kodo `training_days_actual`, o pranešti apie mokymosi eigą žmogiškai (pvz., „duomenys kaupiami 5 dienas“).
- **Būsenų logika:** OVR ir Boost būsenos suvienodintos į dvi fazes: **Aktyvus** arba **Išjungtas**.
- **Statuso suvestinė:** Telegram komanda `/status` papildyta temperatūra, OVR bei Boost būsenomis, naudojant pilną lietuvišką terminologiją.

**Sutvarkyta:**
- **Sensorių ID sinchronizacija:** Pataisytas temperatūros jutiklio ID į `sensor.intake_air_temperature` pagal HA duomenis.
- **Srauto klaida:** Pataisytas „Intake air temperature“ mazgo klaidingas `msg.topic` nustatymas (pakeistas iš `humidity` į `intake_temp`).
- **DI matomumas:** Ištaisyta kritinė klaida, dėl kurios Gemini nematė temperatūros reikšmės, nors ji buvo HA sistemoje.

---

## [4.3.0] - 2026-02-15

### DI Šešėlinė analizė ir Schemų unifikavimas

**Pridėta:**
- Gemini 2.0 Flash integracija paros vėdinimo auditui.
- Filtrų stebėjimo modulis su efektyvių valandų skaičiavimu.
- Centralizuotas HA Entity ID ir versijų valdymas globaliame kontekste.
- Lietuviškas loginis režimų atvaizdavimas (STOP, OVR, Naktis, Diena).

**Pakeista:**
- Duomenų schema atnaujinta į **v5 (Unified Snapshot Stub)**.
- Visi kodo blokai tapo dinamiški (versijos nebeįrašytos ranka).
- `boost_active` automatikai dabar remiasi tik vidiniu Node-RED indikatoriumi.

**Sutvarkyta:**
- Laiko zonų nesutapimai (visur įvestas Europe/Vilnius formatas).
- Išvalyti asmeniniai Telegram ID iš kodo komentarų.

---

## [4.2.0] - 2026-02-14

### Logging ir telemetrijos stabilizavimo versija

Ši versija orientuota į telemetrijos nuoseklumą, duomenų schemos stabilizavimą ir pasirengimą predikcinei analitikai.

---

### Pridėta

- `mode` laukas telemetrijoje dabar imamas iš `sensor.komfovent_logic_2`
- Fallback mechanizmas alarm būsenai (naudojama paskutinė žinoma reikšmė)
- `rate` reikšmės fallback į vidinį 15 min slankaus lango skaičiavimą
- Telemetrijos schemos suvienodinimas ilgalaikiam duomenų kaupimui
- Stabilizuotas `boost_active` šaltinis (tik iš HA binary sensor)

---

### Pakeista

- `mode` nebėra hardcodintas kaip `"TELEMETRY"`
- `rate` laukas:
  - Naudoja HA sensorių, jei prieinamas
  - Jei neprieinamas – skaičiuojamas iš slankaus lango
- `boost_active` logikoje pilnai suvienodintas (nebenaudojamas flow context kaip alternatyvus šaltinis)
- Telemetrijos įrašai garantuotai neturi `null` tarpų `rate` lauke

---

### Sutvarkyta

- Pašalinti epizodiniai `rate: null` įrašai
- Išvengta skirtingų `boost_active` interpretacijų (HA vs flow)
- Pagerintas telemetrijos stabilumas po restart arba HA uždelsimo

---

### Vidiniai pakeitimai

- Logging modulis paruoštas predikcinei analitikai
- Stabilizuota telemetrijos schema prieš ilgalaikio dataset kaupimą
- Nustatyta nauja bazinė versija švariems duomenims rinkti nuo 2026-02-14

---

## [4.1.1] - 2026-02-13

### 🐛 Bugfix

**Rate telemetry stabilumo pataisymas**

- Išspręsta problema, kai `sensor.co2_kilimo_greitis_ppm_min` Home Assistant rodydavo `unknown`
- Užtikrinta, kad sensorius visada turi reikšmę:
  - Jei yra naujas rate → siunčiama aktuali reikšmė
  - Jei nėra → naudojama paskutinė žinoma
  - Jei duomenys per seni → automatiškai nustatoma 0
- Pašalinti grafiko „unknown“ tarpai

---

### ⚙ Patobulinimai

**CO₂ kilimo greičio stabilizavimas**

- Įdiegtas minimalus ΔCO₂ filtras (10 ppm), kad būtų ignoruojamas smulkus triukšmas
- Įdiegtas EMA smoothing (α = 0.35) lygesnei kreivei
- Pridėta kokybės klasifikacija:
  - `ok`
  - `stable`
  - `gap`
  - `no_co2`
  - `stale_timeout`

**Papildomi atributai telemetrijoje**

- `raw_rate`
- `ema_rate`
- `quality`
- `stable`
- `delta_co2`
- `delta_min`

---

### 🔄 Elgsenos pakeitimai

- Maži CO₂ svyravimai dabar laikomi „stabilu“, vietoj dirbtinių šuolių grafike
- Po ilgesnio laikotarpio be atnaujinimų rate automatiškai nustatomas į 0 (apsauga nuo pakibusios reikšmės)

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
