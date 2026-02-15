# 🌬️ Komfovent Smart Automation (v4.3.0)

Profesionalus rekuperatoriaus valdymo ir adaptyvaus CO₂ mokymosi sprendimas, sukurtas **Node-RED** aplinkai ir integruotas su **Home Assistant**. 

Sistema dinamiškai reguliuoja vėdinimą realiu laiku, prisitaiko prie gyventojų ritmo per savarankišką mokymąsi ir yra audituojama dirbtinio intelekto, siekiant maksimalaus komforto bei energijos efektyvumo.

<img width="1450" height="508" alt="Screenshot 2026-02-12 at 22 46 20" src="https://github.com/user-attachments/assets/40f036b9-5d66-4f8a-9ecc-0869ea52c75c" />

## ⚠ Projekto Paskirtis (Disclaimer)

Šis projektas yra kuriamas ir naudojamas tik asmeniniams poreikiams. GitHub repozitorija naudojama kaip versijų ir pakeitimų sekimo įrankis, o ne kaip viešai vystomas ar universalus sprendimas.

Sistema nėra sukurta taip, kad ją būtų galima lengvai perimti ir pritaikyti be papildomo konfigūravimo. Kiekvienas gali naudoti ar adaptuoti kodą savo rizika, tačiau aš neteikiu palaikymo ar individualių pritaikymo konsultacijų.

**SVARBU:** Šis projektas (architektūra, programinis kodas ir dokumentacija) sukurtas  su dirbtinio intelekto modeliais (**Gemini** ir **ChatGPT**).

---

## 🧠 Realaus Laiko Valdymo Logika (Real-time Control Flow)

Sprendimas priimamas griežta prioritetų tvarka (nuo aukščiausio):

1. **Kritiniai Stabdžiai (Hard Blocks):** Aptikus įrenginio klaidą arba techninės priežiūros režimą, rekuperatorius stabdomas.
2. **Drėgmės Kontrolė (Bathroom OVR):** Aktyvuojama, kai drėgmė >80%. Išsijungia tik jei <70% išsilaiko 5 min. (100% ventiliatoriaus greitis).
3. **Apsaugos Režimas (Armed Away):** Jei signalizacija „Armed Away“, vykdomas vienkartinis išvėdinimas (jei CO₂ ≥ 800 ppm), vėliau – OFF režimas energijos taupymui.
4. **Langų Saugiklis (Window Guard):** Jei langai atidaryti >10 min., vėdinimas stabdomas.
5. **Paros Ciklas (Day/Night Mode):** Naktį palaikomas bazinis 35% srautas, dieną (>18°C) vėdinimas aktyvuojamas tik pasiekus kritinius CO₂ lygius.

---

## 📊 Bazinis Vėdinimas Pagal CO₂ Laiptus (Base CO₂ Ventilation Steps)

| CO₂ lygis (ppm) | Ventiliatoriaus greitis (%) |
| :--- | :--- |
| < 600 | 20% |
| 600–749 | 30% |
| 750–899 | 45% |
| 900–1099 | 70% |
| ≥ 1100 | 100% |

---

## 📈 Aktyvus Vėdinimas (Rate-Based Boost)

Sistema reaguoja ne tik į CO₂ lygį, bet ir į jo kilimo greitį (**ppm/min**).

* **Analizės Langas:** 15 min. slankusis vidurkis.
* **Stabilizacija:** Reikalingi 2 iš eilės patvirtinimai, 15 min. „Hold“ mechanizmas ir 20 min. „Cooldown“ tarp ciklo pradžių.
* **Saugumas:** „Boost“ leidžiamas tik jei CO₂ ≥ (slenkstis - 50).
* **Soft-stop:** Jei CO₂ koncentracija pradeda kristi, „Boost“ nutraukiamas anksčiau laiko.

---

## 🤖 Adaptyvus CO₂ Mokymasis (Adaptive Learning Module)

Kas naktį 03:00 (LT) analizuojamos paskutinės 24 valandos ir koreguojamas vėdinimo slenkstis.

* **Proporcinė Adaptacija:** `delta = -round((avgRate - TARGET_RATE) * K)`.
* **Parametrai:** `TARGET_RATE = 60 ppm/h`, `K = 0.2`, maksimali korekcija ±25 ppm per parą.
* **Mokymosi Saugikliai (Guards):**
    * **Stability Lock:** Blokuoja mokymąsi, jei paros dinamika per maža.
    * **Gap Guard:** Blokuoja mokymąsi, jei trūksta >35% duomenų.
    * **Alarm Block:** Blokuoja mokymąsi, jei namuose nieko nėra (Signalizacija ≠ Disarmed).

---

## 👁️ Gemini Analizė (Gemini Shadow Analysis)

Pradedant nuo **v4.3.0**, įdiegtas lygiagretus DI audito modulis, naudojantis `gemini-2.0-flash`.

* **Veikimo Principas:** Po matematinio skaičiavimo Gemini gauna paros telemetrijos ištrauką.
* **Užduotis:** DI vertina ar algoritmas nesuklydo dėl trumpalaikių anomalijų (pvz. gaminimo šuolių) ir pateikia savo rekomendaciją per Telegram lietuvių kalba.
* **Tikslas:** Suteikti antrą „nuomonę“ prieš fiziškai keičiant sistemos parametrus.

---

## 🧹 Filtrų Stebėjimas (Filter Monitoring)

Specializuotas modulis filtrų užterštumo ir efektyvumo sekimui.

* **Tikslumas:** Skaičiuojamos realios ventiliatoriaus darbo valandos (`filter_eff_hours`), o ne tik kalendorinės dienos.
* **Prognozavimas:** Sistema seka dienas nuo paskutinio atstatymo (`filter_days_since_reset`) ir įspėja apie artėjantį aptarnavimą.

---

## 🗂️ Duomenų Kaupimas (Logging Module)

Sistemos pagrindas – **v4 Unified Schema**, užtikrinanti, kad visi moduliai kalbėtų ta pačia „kalba“.

* **Snapshot Stub:** Kiekvienas įrašas (telemetrija, kontrolė, mokymasis) turi bazinį kontekstą: laikas (LT), `occupied`, `mode`, `co2`, `outdoor`, `humidity`.
* **Formatas:** JSONL (1 eilutė = 1 objektas), paruošta Machine Learning analizei.
* **Retention:** Automatinis duomenų saugojimo terminas optimizuotas 60-čiai dienų.

---

## 🛡️ Atsparumo Mechanizmai (Resilience Mechanisms)

* **Fallback:** Paskutinės žinomos būsenos išsaugojimas po Home Assistant restarto.
* **RBE Filtras:** Užtikrina, kad į rekuperatorių nebūtų siunčiamos perteklinės, nesikeičiančios komandos.
* **Timezone Safety:** Naudojama `Intl.DateTimeFormat` su `Europe/Vilnius` zona, apsauganti nuo klaidų keičiantis vasaros/žiemos laikui.

---

## 🔢 Versija (Version)

**v4.3.0** — Pilna Gemini integracija, suvienodinti Snapshot logai, įdiegtas protingas laiko zonų valdymas.

<img width="1353" height="876" alt="Screenshot 2026-02-12 at 22 44 38" src="https://github.com/user-attachments/assets/1fe7d7c5-12a0-4346-b5e2-797aa92df2a8" />
