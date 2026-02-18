# 🌬️ Komfovent Smart Automation

<<<<<<< HEAD
Profesionalus rekuperatoriaus valdymo ir adaptyvaus CO₂ mokymosi sprendimas, sukurtas **Node-RED** aplinkai ir integruotas su **Home Assistant**.  

Sistema dinamiškai reguliuoja vėdinimą realiu laiku, prisitaiko prie gyventojų ritmo ir yra audituojama **Gemini** dirbtinio intelekto, siekiant maksimalaus komforto bei energijos efektyvumo.
=======
Profesionalus rekuperatoriaus valdymo ir adaptyvaus $CO_2$ mokymosi sprendimas, sukurtas **Node-RED** aplinkai ir integruotas su **Home Assistant**. Sistema dinamiškai reguliuoja vėdinimą realiu laiku, prisitaiko prie gyventojų ritmo per savarankišką mokymąsi ir yra audituojama **Gemini** dirbtinio intelekto, siekiant maksimalaus komforto bei energijos efektyvumo.
>>>>>>> b58913592a75cf5a86b6acefe8a14e062f21f6ad

<img width="1450" height="508" alt="Screenshot 2026-02-12 at 22 46 20" src="https://github.com/user-attachments/assets/40f036b9-5d66-4f8a-9ecc-0869ea52c75c" />

---

## ⚠ Projekto Paskirtis (Disclaimer)

<<<<<<< HEAD
Projektas sukurtas asmeniniams poreikiams. GitHub repozitorija skirta versijų sekimui.  

Sistema nėra universali – ją naudojate savo rizika ir be oficialaus palaikymo.

=======
Projektas sukurtas asmeniniams poreikiams. GitHub repozitorija skirta versijų sekimui. Sistema nėra universali; ją naudojate savo rizika be palaikymo.

>>>>>>> b58913592a75cf5a86b6acefe8a14e062f21f6ad
**SVARBU:** Ši sistema (architektūra, programinis kodas ir dokumentacija) sukurta bendradarbiaujant su dirbtinio intelekto modeliais (**Gemini** ir **ChatGPT**).

---

## 🧠 Realaus Laiko Valdymo Logika

Sprendimai priimami griežta prioritetų tvarka (nuo aukščiausio):

1. **STOP FLAGS** – Aptikus klaidą ar techninį režimą, sistema stabdoma.
2. **Vonios OVR** – Drėgmei >80% aktyvuojamas 100% režimas. Išsijungia tik jei <70% išsilaiko 5 min.
3. **Armed Away** – Jei signalizacija „Armed Away“ ir CO₂ ≥ 800 ppm, atliekamas vienkartinis išvėdinimas, po to sistema išjungiama.
4. **Gartraukis (HOOD)** – Automatinė slėgio kompensacija pagal gartraukio galią.
5. **Langų apsauga** – Jei langai atidaryti >10 min., vėdinimas stabdomas.
6. **Day / Night režimas** – Naktį palaikomas bazinis 35%, dieną reguliuojama pagal CO₂.

---

<<<<<<< HEAD
## 🍳 Gartraukio (HOOD) Režimas

Sistema automatiškai kompensuoja gartraukio ištraukiamą orą didindama tiekimą (IN) ir ribodama ištraukimą (OUT).

### Lygiai pagal galią

| Gartraukio galia | IN (Tiekimas) | OUT (Ištraukimas) |
|------------------|--------------|-------------------|
| ≥ 75W (L1)      | 80%          | 45%               |
| ≥ 105W (L2)     | 90%          | 50%               |
| ≥ 145W (L3)     | 100%         | 55%               |

Tikslas – sumažinti neigiamą slėgį namuose ir išvengti oro siurbimo per plyšius.

### Post-run (po išjungimo)

- Trukmė: 5 / 8 / 12 min. (pagal sesijos max lygį)
- Režimas: **IN 65% / OUT 65%**
- Tikslas: ramus, subalansuotas kvapų pašalinimas

> Jei langai ilgai atidaryti – HOOD režimas ignoruojamas.

---

## 📊 Bazinis Vėdinimas Pagal CO₂
=======
## 📊 Bazinis Vėdinimas Pagal CO₂ Laiptus
>>>>>>> b58913592a75cf5a86b6acefe8a14e062f21f6ad

| CO₂ lygis (ppm) | Ventiliatoriaus greitis (%) |
|-----------------|----------------------------|
| < 650          | 20%                        |
| 650–709        | 45%                        |
| 710–849        | 55%                        |
| 850–1099       | 70%                        |
| ≥ 1100         | 100%                       |

Sistema reguliuoja **IN ir OUT vienodai** (subalansuotas režimas), išskyrus HOOD atvejį.

---

<<<<<<< HEAD
## 📈 Aktyvus Vėdinimas (Rate Boost)
=======
## 📈 Aktyvus Vėdinimas
>>>>>>> b58913592a75cf5a86b6acefe8a14e062f21f6ad

Sistema reaguoja į CO₂ kilimo greitį (ppm/min), ne tik į absoliučią reikšmę.

- **Slenkstis:** ≥ 12 ppm/min
- Reikalingi 2 patvirtinimai iš eilės
- **Hold:** 15 min
- **Cooldown:** 20 min tarp ciklų
- Leidžiama tik jei CO₂ ≥ (auto slenkstis − 50)
- Jei CO₂ pradeda kristi – Boost nutraukiamas anksčiau

Boost režime IN ir OUT pakeliami bent iki 55%.

---

## 🤖 Adaptyvus CO₂ Mokymasis

<<<<<<< HEAD
Sistema kasdien perskaičiuoja optimalų įsijungimo slenkstį pagal paskutinių 14 dienų istoriją.

- Atskiriami darbo dienų ir savaitgalių modeliai
- HOOD epizodai neįtraukiami į mokymosi statistiką
- Predictive modelis vertina rizikos lygį ir pasitikėjimą

---

## 🤖 Gemini Analizė ir Auditas

Integruotas **Gemini** veikia kaip vėdinimo ekspertas:

- Telegram komanda `/status` rodo CO₂, režimą, IN/OUT %, OVR ir HOOD būseną
- DI analizuoja logus ir ieško anomalijų
- Atsakymai pateikiami aiškia, žmogiška lietuvių kalba
=======
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
>>>>>>> b58913592a75cf5a86b6acefe8a14e062f21f6ad

---

## 📊 Telemetrija ir Vizualizacija
<<<<<<< HEAD
=======

Sistema užtikrina pilną duomenų skaidrumą realiuoju laiku. Visi kritiniai parametrai yra vizualizuojami, leidžiančiame stebėti ne tik esamą būseną, bet ir istorinius pokyčius.

### 🔍 Skydelio galimybės

* **Realaus laiko rodikliai**: Tikslus $CO_2$ lygis (ppm), temperatūra (°C), oro drėgmė (%) ir momentinis energijos suvartojimas (W).
* **Sistemos būsenos**: Aiškus vėdinimo režimų (Diena/Naktis), filtrų nusidėvėjimo (2.3 d. iki patikros) ir klaidų pranešimų (Alarm) stebėjimas.
* **Interaktyvūs grafikai**: 
    * **Vėdinimo intensyvumas**: Ventiliatorių greičio (%) koreliacija su nustatytu automatiniu slenksčiu (Threshold: 700 ppm).
    * **Aplinkos dinamika**: Detalios drėgmės ir $CO_2$ kitimo kreivės, padedančios identifikuoti namų mikroklimato dėsningumus.
* **Efektyvumo analizė**: Sukauptos elektros energijos sąnaudos (kWh) pagal dienas ir mėnesius, leidžiančios optimizuoti sistemos kaštus.

<img width="1652" height="1115" alt="Screenshot 2026-02-17 at 09 21 01" src="https://github.com/user-attachments/assets/f85249c9-f6d1-405a-8661-0a2364c896bd" />


---

## 🧹 Filtrų Stebėjimas
>>>>>>> b58913592a75cf5a86b6acefe8a14e062f21f6ad

Sistema užtikrina aiškų ir skaidrų duomenų stebėjimą realiuoju laiku. Visi sprendimai fiksuojami struktūrizuotuose loguose ir gali būti analizuojami per Home Assistant ar DI asistentą.

- **Realaus laiko rodikliai:** CO₂ (ppm), ventiliatorių IN / OUT greitis (%), lauko ir tiekiamo oro temperatūra, drėgmė bei HOOD būsena (`idle / active / post_run`).
- **Režimų stebėjimas:** Diena / Naktis, Vonios OVR, Armed Away, Rate Boost ir Gartraukio lygis su aktyvia kompensacija.
- **Istoriniai duomenys:** JSONL logai saugo CO₂ dinamiką, kilimo greitį (ppm/min), IN/OUT pokyčius ir HOOD epizodus.
- **Efektyvumo įžvalgos:** Galima įvertinti, kada ir kodėl aktyvuojamas Boost, kiek trunka HOOD post-run fazė bei kaip keičiasi namų mikroklimatas paros eigoje.
- **AI analizė:** Gemini naudoja kontekstinį sistemos „snapshot“, kad paaiškintų sprendimus ir aptiktų galimas anomalijas.

Visa telemetrija generuojama Node-RED viduje; Home Assistant naudojamas tik sensorių ir valdymo integracijai.

<img width="1353" height="876" alt="Screenshot 2026-02-12 at 22 44 38" src="https://github.com/user-attachments/assets/1fe7d7c5-12a0-4346-b5e2-797aa92df2a8" />

---

<<<<<<< HEAD
## 🧹 Filtrų Stebėjimas

- Skaičiuojamos realios ventiliatoriaus darbo valandos
- Rodomas filtrų nusidėvėjimo procentas
- Sistema įspėja apie artėjantį aptarnavimą

---

## 🗂️ Duomenų Kaupimas

- JSONL logai su CO₂, IN/OUT %, HOOD, OVR ir temperatūra
- 2 mėn. saugojimo politika
- Pilnas kontekstinis snapshot DI analizei
=======
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
>>>>>>> b58913592a75cf5a86b6acefe8a14e062f21f6ad

---

## 🔢 Versija

<<<<<<< HEAD
**v4.4.0** 
=======
**v4.3.1** — Pilna Gemini integracija, suvienodinti Snapshot logai, įdiegtas protingas laiko zonų valdymas.
>>>>>>> b58913592a75cf5a86b6acefe8a14e062f21f6ad
