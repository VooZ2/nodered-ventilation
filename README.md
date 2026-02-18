# 🌬️ Komfovent Smart Automation

Profesionalus rekuperatoriaus valdymo ir adaptyvaus CO₂ mokymosi sprendimas, sukurtas **Node-RED** aplinkai ir integruotas su **Home Assistant**.  

Sistema dinamiškai reguliuoja vėdinimą realiu laiku, prisitaiko prie gyventojų ritmo ir yra audituojama **Gemini** dirbtinio intelekto, siekiant maksimalaus komforto bei energijos efektyvumo.

<img width="1450" height="508" alt="Screenshot 2026-02-12 at 22 46 20" src="https://github.com/user-attachments/assets/40f036b9-5d66-4f8a-9ecc-0869ea52c75c" />

---

## ⚠ Projekto Paskirtis (Disclaimer)

Projektas sukurtas asmeniniams poreikiams. GitHub repozitorija skirta versijų sekimui.  

Sistema nėra universali – ją naudojate savo rizika ir be oficialaus palaikymo.

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

| CO₂ lygis (ppm) | Ventiliatoriaus greitis (%) |
|-----------------|----------------------------|
| < 650          | 20%                        |
| 650–709        | 45%                        |
| 710–849        | 55%                        |
| 850–1099       | 70%                        |
| ≥ 1100         | 100%                       |

Sistema reguliuoja **IN ir OUT vienodai** (subalansuotas režimas), išskyrus HOOD atvejį.

---

## 📈 Aktyvus Vėdinimas (Rate Boost)

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

---

## 📊 Telemetrija ir Vizualizacija

Sistema užtikrina aiškų ir skaidrų duomenų stebėjimą realiuoju laiku. Visi sprendimai fiksuojami struktūrizuotuose loguose ir gali būti analizuojami per Home Assistant ar DI asistentą.

- **Realaus laiko rodikliai:** CO₂ (ppm), ventiliatorių IN / OUT greitis (%), lauko ir tiekiamo oro temperatūra, drėgmė bei HOOD būsena (`idle / active / post_run`).
- **Režimų stebėjimas:** Diena / Naktis, Vonios OVR, Armed Away, Rate Boost ir Gartraukio lygis su aktyvia kompensacija.
- **Istoriniai duomenys:** JSONL logai saugo CO₂ dinamiką, kilimo greitį (ppm/min), IN/OUT pokyčius ir HOOD epizodus.
- **Efektyvumo įžvalgos:** Galima įvertinti, kada ir kodėl aktyvuojamas Boost, kiek trunka HOOD post-run fazė bei kaip keičiasi namų mikroklimatas paros eigoje.
- **AI analizė:** Gemini naudoja kontekstinį sistemos „snapshot“, kad paaiškintų sprendimus ir aptiktų galimas anomalijas.

Visa telemetrija generuojama Node-RED viduje; Home Assistant naudojamas tik sensorių ir valdymo integracijai.

<img width="1353" height="876" alt="Screenshot 2026-02-12 at 22 44 38" src="https://github.com/user-attachments/assets/1fe7d7c5-12a0-4346-b5e2-797aa92df2a8" />

---

## 🧹 Filtrų Stebėjimas

- Skaičiuojamos realios ventiliatoriaus darbo valandos
- Rodomas filtrų nusidėvėjimo procentas
- Sistema įspėja apie artėjantį aptarnavimą

---

## 🗂️ Duomenų Kaupimas

- JSONL logai su CO₂, IN/OUT %, HOOD, OVR ir temperatūra
- 2 mėn. saugojimo politika
- Pilnas kontekstinis snapshot DI analizei

---

## 🔢 Versija

**v4.4.0** 