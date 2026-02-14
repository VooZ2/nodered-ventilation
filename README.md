# 🌬️ Komfovent Smart Automation

Profesionalus rekuperatoriaus valdymo ir adaptyvaus CO₂ mokymosi sprendimas, 
sukurtas Node-RED aplinkai ir integruotas su Home Assistant.

Sistema dinamiškai reguliuoja vėdinimą realiu laiku, prisitaiko prie
gyventojų ritmo ir optimizuoja energijos sąnaudas.

<img width="1450" height="508" alt="Screenshot 2026-02-12 at 22 46 20" src="https://github.com/user-attachments/assets/40f036b9-5d66-4f8a-9ecc-0869ea52c75c" />

## ⚠ Projekto paskirtis (Disclaimer)

Šis projektas yra kuriamas ir naudojamas tik asmeniniams poreikiams. GitHub repozitorija naudojama kaip versijų ir pakeitimų sekimo įrankis, o ne kaip viešai vystomas ar universalus sprendimas.

Sistema nėra sukurta taip, kad ją būtų galima lengvai perimti ir pritaikyti be papildomo konfigūravimo. Kiekvienas gali naudoti ar adaptuoti kodą savo rizika, tačiau aš neteikiu palaikymo ar individualių pritaikymo konsultacijų.

---

# 🧠 Realaus laiko valdymo logika (Control Flow)

Sprendimas priimamas griežta prioritetų tvarka (nuo aukščiausio):

## 1️⃣ STOP FLAGS (Hard Block)

- Aptikus įrenginio klaidą – rekuperatorius išjungiamas
- Automatikos vykdymas sustabdomas
- Gali būti siunčiamas pranešimas

## 2️⃣ Vonios OVR (Histerezė)

- Įjungiamas >80% drėgmei
- Išjungiamas tik jei <70% išsilaiko 5 min
- Maksimalus ventiliatoriaus greitis (100%)

## 3️⃣ ARMED_AWAY režimas

- Jei signalizacija „armed_away“:
  - Vienkartinis pilnas išvėdinimas, jei CO₂ ≥ 800 ppm
  - Vėliau – OFF režimas (energijos taupymas)

## 4️⃣ Langų saugiklis

- Jei langai atidaryti >10 min – rekuperatorius išjungiamas

## 5️⃣ Diena / Naktis režimas

- Naktis – minimalus 35%, bet gali kilti pagal CO₂
- Diena (>18°C) – įjungiamas tik jei CO₂ ≥ 800 ppm
- Kitu atveju – valdymas pagal CO₂ laiptus

---

# 📊 CO₂ laiptai (Base Ventilation Steps)

| CO₂ (ppm) | Fan (%) |
|------------|----------|
| < 600      | 20%      |
| 600–749    | 30%      |
| 750–899    | 45%      |
| 900–1099   | 70%      |
| ≥ 1100     | 100%     |

---

# 📈 Rate-Based Boost (CO₂ kilimo greitis)

Sistema reaguoja ne tik į CO₂ lygį, bet ir į jo kilimo greitį (ppm/min).

## Stabilizacija (v4.1)

- 15 min slankus analizės langas
- 2 iš eilės patvirtinimai
- 15 min hold mechanizmas
- 20 min cooldown tarp startų
- Soft-stop jei CO₂ pradeda kristi
- Boost leidžiamas tik jei CO₂ ≥ (threshold - 50)

---

# 📡 Telemetrija (Home Assistant)

- Logikos režimo sensorius
- CO₂ kilimo greitis
- Rate boost indikatorius
- Adaptyvus threshold

<img width="1353" height="876" alt="Screenshot 2026-02-12 at 22 44 38" src="https://github.com/user-attachments/assets/1fe7d7c5-12a0-4346-b5e2-797aa92df2a8" />

---

# 🤖 Adaptyvus CO₂ mokymasis (Learning Module)

Kasdien 03:00 (LT) analizuojamos paskutinės 24h.

## Profiliai

- Darbo dienos – tik aktyvios valandos
- Savaitgaliai – visa 24h para

## Proporcinė adaptacija

delta = -round((avgRate - TARGET_RATE) * K)

- TARGET_RATE = 60 ppm/h  
- K = 0.2  
- max ±25 ppm per parą  
- Ribos: 550–950 ppm  

## Learning Guards

- Stability Lock – jei dinamika per maža
- Gap Guard – jei trūksta >35% duomenų
- Alarm Block – jei signalizacija ≠ disarmed

---

# 🗂️ Duomenų kaupimas (Logging Module)

Komfovent sistemos istorinių duomenų kaupimo ir analitinės bazės modulis.  
Skirtas ilgalaikei telemetrijai, modelių validacijai ir ateities prognozavimui.

Nuo **v4.2.0** logging schema laikoma stabilia analitine baze.

## 🎯 Paskirtis

- Rašo telemetrijos snapshot kas 10 min
- Priima įrašus iš:
  - Control flow
  - Learning flow
  - Filters flow
- Saugo JSONL formatu (1 eilutė = 1 JSON objektas)
- Vykdo automatinį 60 dienų retention
- Paruošta predictive / ML analizei

---

# 🛡 Atsparumo mechanizmai

- STOP FLAGS hard stop
- Alarm fallback po HA restarto
- RBE filtras prieš komandas
- Robust HA state reading

---

# 🔢 Versija

v4.2.0