# 🌬️ Komfovent Smart Automation

<img width="1450" height="508" alt="Screenshot 2026-02-12 at 22 46 20" src="https://github.com/user-attachments/assets/40f036b9-5d66-4f8a-9ecc-0869ea52c75c" />

Profesionalus rekuperatoriaus valdymo ir adaptyvaus CO₂ mokymosi
sprendimas, sukurtas **Node-RED** aplinkai ir integruotas su **Home
Assistant**.

Sistema dinamiškai reguliuoja vėdinimą realiu laiku, prisitaiko prie
gyventojų ritmo ir optimizuoja energijos sąnaudas.

------------------------------------------------------------------------

# 🧠 Realaus laiko valdymo logika (Control Flow)

Sprendimas priimamas griežta prioritetų tvarka (nuo aukščiausio):

## 1️⃣ STOP FLAGS (Hard Block)

-   Aptikus įrenginio klaidą -- rekuperatorius išjungiamas.
-   Automatikos vykdymas sustabdomas.
-   Siunčiamas pranešimas (jei nustatyta).

## 2️⃣ Vonios OVR (Histerezė)

-   Įjungiamas \>80% drėgmei.
-   Išjungiamas tik jei \<70% išsilaiko 5 min.
-   Maksimalus ventiliatoriaus greitis (100%).

## 3️⃣ ARMED_AWAY režimas

-   Jei signalizacija „armed_away":
    -   Vienkartinis pilnas išvėdinimas, jei CO₂ ≥ 800 ppm.
    -   Vėliau -- OFF (energijos taupymas).

## 4️⃣ Langų saugiklis

-   Jei langai atidaryti \>10 min -- rekuperatorius išjungiamas.

## 5️⃣ Diena / Naktis režimas

-   **Naktis** -- minimalus 35%, bet gali kilti pagal CO₂.
-   **Diena (\>18°C)** -- įjungiamas tik jei CO₂ ≥ 800 ppm.
-   **Kitu atveju** -- valdymas pagal CO₂ laiptus.

------------------------------------------------------------------------

# 📊 CO₂ laiptai (Base Ventilation Steps)

 | CO₂ (ppm) | Fan (%) |
|----------:|--------:|
| < 600     | 20%     |
| 600–749   | 30%     |
| 750–899   | 45%     |
| 900–1099  | 70%     |
| ≥ 1100    | 100%    |

------------------------------------------------------------------------

# 📈 Rate-Based Boost (CO₂ kilimo greitis)

Sistema reaguoja ne tik į CO₂ lygį, bet ir į jo **kilimo greitį
(ppm/min)**.

## Slenksčiai:

| Kilimo tempas | Fan (%) |
|--------------:|--------:|
| ≥ 10 ppm/min  | 55%     |
| ≥ 18 ppm/min  | 65%     |
| ≥ 30 ppm/min  | 80%     |

## Saugikliai:

-   Aktyvuojamas tik jei CO₂ ≥ 600 ppm
-   Maks. fan = 80%
-   Hold laikotarpis = 10 min
-   Deadband = ±2 ppm/min

------------------------------------------------------------------------

# 🤖 Adaptyvus CO₂ mokymasis (Learning Module)

Sistema kas parą analizuoja paskutinių 24h duomenis ir koreguoja
įsijungimo slenkstį.

## Profiliai

-   Darbo dienomis analizuojamos tik nustatytos aktyvios valandos.
-   Savaitgaliais analizuojama visa 24h.

## Proporcinė adaptacija

delta = -round((avgRate - TARGET_RATE) \* K)

Kur: - TARGET_RATE = 60 ppm/h - K = 0.2 - max ±25 ppm per parą

## Learning Guards (apsaugos)

### Stability Lock

-   Jei max CO₂ \< 650 ppm → mokymasis nevykdomas
-   Jei svyravimo diapazonas \< 100 ppm → mokymasis nevykdomas

### Gap Guard

-   Jei trūksta \>35% duomenų taškų → adaptacija blokuojama

------------------------------------------------------------------------

# 📡 Telemetrija (Home Assistant)

Sukuriami diagnostikos sensoriai:

-   Logikos režimas (Diena, Naktis, Išvykę, STOP, OVR, CO₂ kilimas)
-   CO₂ kilimo greitis (ppm/min)
-   Rate boost aktyvumo indikatorius
  
Vidinė atmintis saugo iki 14 dienų istoriją kalibravimui.

<img width="1353" height="876" alt="Screenshot 2026-02-12 at 22 44 38" src="https://github.com/user-attachments/assets/1fe7d7c5-12a0-4346-b5e2-797aa92df2a8" />

------------------------------------------------------------------------

# 🛡 Atsparumo mechanizmai (Fail‑Safes)

-   Alarm fallback po HA restarto
-   Robust HA state reading
-   RBE filtras (komandos tik pasikeitus būsenai)
-   Hard stop sauga STOP FLAGS atveju

------------------------------------------------------------------------

# 🔢 Versija

**v4.0.0**
