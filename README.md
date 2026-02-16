# 🌬️ Komfovent Smart Automation

Profesionalus rekuperatoriaus valdymo ir adaptyvaus $CO_2$ mokymosi sprendimas, sukurtas **Node-RED** aplinkai ir integruotas su **Home Assistant**. Sistema dinamiškai reguliuoja vėdinimą realiu laiku, prisitaiko prie gyventojų ritmo per savarankišką mokymąsi ir yra audituojama **Gemini** dirbtinio intelekto, siekiant maksimalaus komforto bei energijos efektyvumo.

<img width="1450" height="508" alt="Screenshot 2026-02-12 at 22 46 20" src="https://github.com/user-attachments/assets/40f036b9-5d66-4f8a-9ecc-0869ea52c75c" />

## ⚠ Projekto Paskirtis (Disclaimer)

Projektas sukurtas asmeniniams poreikiams. GitHub repozitorija skirta versijų sekimui. Sistema nėra universali; ją naudojate savo rizika be palaikymo.

**SVARBU:** Ši sistema (architektūra, programinis kodas ir dokumentacija) sukurta bendradarbiaujant su dirbtinio intelekto modeliais (**Gemini** ir **ChatGPT**).

---

## 🧠 Realaus Laiko Valdymo Logika

Sprendimas priimamas griežta prioritetų tvarka (nuo aukščiausio):

1. **Kritiniai Stabdžiai (Hard Blocks):** Aptikus įrenginio klaidą arba techninės priežiūros režimą, rekuperatorius stabdomas.
2. **Drėgmės Kontrolė (Bathroom OVR):** Aktyvuojama, kai drėgmė >80%. Išsijungia tik jei <70% išsilaiko 5 min. (100% ventiliatoriaus greitis).
3. **Apsaugos Režimas (Armed Away):** Jei signalizacija „Armed Away“, vykdomas vienkartinis išvėdinimas (jei CO₂ ≥ 800 ppm), vėliau – OFF režimas energijos taupymui.
4. **Langų Saugiklis (Window Guard):** Jei langai atidaryti >10 min., vėdinimas stabdomas.
5. **Paros Ciklas (Day/Night Mode):** Naktį palaikomas bazinis 35% srautas, dieną (>18°C) vėdinimas aktyvuojamas tik pasiekus kritinius CO₂ lygius.

---

## 📊 Bazinis Vėdinimas Pagal CO₂ Laiptus

| CO₂ lygis (ppm) | Ventiliatoriaus greitis (%) |
| :--- | :--- |
| < 600 | 20% |
| 600–749 | 30% |
| 750–899 | 45% |
| 900–1099 | 70% |
| ≥ 1100 | 100% |

---

## 📈 Aktyvus Vėdinimas

Sistema reaguoja ne tik į CO₂ lygį, bet ir į jo kilimo greitį (**ppm/min**).

* **Analizės Langas:** 15 min. slankusis vidurkis.
* **Stabilizacija:** Reikalingi 2 iš eilės patvirtinimai, 15 min. „Hold“ mechanizmas ir 20 min. „Cooldown“ tarp ciklo pradžių.
* **Saugumas:** „Boost“ leidžiamas tik jei CO₂ ≥ (slenkstis - 50).
* **Soft-stop:** Jei CO₂ koncentracija pradeda kristi, „Boost“ nutraukiamas anksčiau laiko.

---

## 🤖 Adaptyvus CO₂ Mokymasis

Sistema remiasi **Predictive Matrix** modeliu, kuris eliminuoja statinių parametrų poreikį:
* **Matricos stebėjimas**: DI nuolat seka matricos brandą (sukauptų dienų skaičių) ir pasitikėjimo lygį (*Confidence Level*), užtikrindamas sprendimų tikslumą.
* **Automatinė adaptacija**: Kasdien perskaičiuojamas optimalus įsijungimo slenkstis pagal pastarųjų 14 dienų istorinius duomenis.
* **Profilinė analizė**: Atskiriami darbo dienų ir savaitgalių vėdinimo modeliai, prisitaikant prie kintančio namų užimtumo.

---

### 🤖 Gemini Analizė ir Auditas

Sistemoje integruotas **Gemini** (Google AI) gateway modulis, veikiantis kaip vėdinimo ekspertas:
* **Interaktyvus asistentas**: Per Telegram teikia žmogiškai suformuluotas suvestines apie temperatūrą, OVR būsenas ir sistemos brandą.
* **Sprendimų auditas**: AI nuolat analizuoja logus, nustatydamas anomalijas (pvz., tiekiamo oro temperatūros nuokrypius) ar efektyvumo spragas.
* **Griežtas komunikacijos standartas**: DI atsakymai formuojami be techninių šiukšlių, pradedant tiesioginiais faktais ir naudojant taisyklingą lietuvių kalbą.

---

## 🧹 Filtrų Stebėjimas

Specializuotas modulis filtrų užterštumo ir efektyvumo sekimui.

* **Tikslumas:** Skaičiuojamos realios ventiliatoriaus darbo valandos (`filter_eff_hours`), o ne tik kalendorinės dienos.
* **Prognozavimas:** Sistema seka dienas nuo paskutinio atstatymo (`filter_days_since_reset`) ir įspėja apie artėjantį aptarnavimą.

---

## 🗂️ Duomenų Kaupimas

Sistemos „atmintis“ saugoma struktūrizuotuose `.jsonl` failuose:
* **v7 Unified Schema**: Kiekvienas įrašas fiksuoja $CO_2$, greitį, paduodamo oro temperatūrą (`sensor.intake_air_temperature`) bei OVR būsenas.
* **Kontekstinis Snapshot**: DI asistentas kaskart gauna pilną sistemos vaizdą, įskaitant signalizacijos būseną bei filtrų nusidėvėjimą.

---

## 🛡️ Atsparumo Mechanizmai

Sukurta maksimaliam patikimumui užtikrinti (*High Availability*):
* **Alarm Fallback**: Naudojama paskutinė žinoma signalizacijos būsena, jei ryšys su HA sutrinka.
* **Windows Guard**: Automatinis vėdinimo stabdymas aptikus atvirus langus (timeout: 10 min.).
* **Stop Flags**: Kritinis blokavimas aptikus rekuperatoriaus klaidas ar aliarmo signalus.
* **OVR Hysteresis**: Išorinio valdymo (pvz., vonios drėgmės) prioritetas su apsauga nuo dažno junginėjimo.

---

## 🔢 Versija (Version)

**v4.3.1** — Pilna Gemini integracija, suvienodinti Snapshot logai, įdiegtas protingas laiko zonų valdymas.

---

<img width="1353" height="876" alt="Screenshot 2026-02-12 at 22 44 38" src="https://github.com/user-attachments/assets/1fe7d7c5-12a0-4346-b5e2-797aa92df2a8" />
