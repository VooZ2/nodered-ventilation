# 🌬️ Komfovent Rekuperatoriaus Automatika v3.2.3

Išmani rekuperatoriaus valdymo sistema, sukurta **Node-RED** aplinkoje ir integruota su **Home Assistant**.

Sistema dinamiškai reguliuoja vėdinimo intensyvumą realiu laiku, užtikrindama:

- ✅ optimalią oro kokybę  
- ✅ energijos taupymą  
- ✅ stabilų ir prognozuojamą veikimą  

---

# 🚀 Pagrindinės funkcijos

## 🔁 Griežta prioritetų hierarchija
Kritinės būsenos (pvz., STOP FLAGS ar saugos signalai) visada turi aukštesnį prioritetą nei komforto ar CO₂ logika.

## 📈 Išmanusis CO₂ kilimo sekimas (Rate Boost)
Sistema reaguoja ne tik į aukštą CO₂ lygį, bet ir į jo kilimo greitį (**ppm/min**).

Tai leidžia:
- užkardyti oro kokybės suprastėjimą dar prieš jam įvykstant  
- padidinti ventiliaciją tik tada, kai to realiai reikia  

## 🌗 Automatinis režimų perjungimas
Skirtinga logika taikoma:

- 🌙 Naktį  
- ☀️ Dieną  
- 🔥 Šiltu sezonu (>18°C)  
- 🏠 Išvykus iš namų  

## 📊 Pilna telemetrija
Home Assistant rodomi sensoriai pateikia:

- dabartinį CO₂ kilimo greitį  
- aktyvų „Boost“ režimą  
- pasirinktą logikos būseną su atributais  

---

# 🏗️ Valdymo logika (Prioritetų seka)

Sprendimas priimamas tokia tvarka (nuo svarbiausio):

1. 🛑 **STOP FLAGS**  
   Kritinis sustabdymas aptikus sistemos klaidą.

2. 🚿 **Vonios OVR**  
   Drėgmei viršijus 80% → įjungiamas maksimalus vėdinimas.  
   Išjungiamas tik kai <70% išsilaiko 5 min.

3. 🛡️ **ARMED_AWAY režimas**  
   Energijos taupymas išvykus.  
   Galimas vienkartinis CO₂ „Boost“ išvėdinimui.

4. 🪟 **Langų saugiklis**  
   Jei langai atidaryti ilgiau nei 10 min → vėdinimas stabdomas.

5. 📈 **Rate Boost (CO₂ kilimo greitis)**  
   Jei CO₂ ≥ 600 ppm ir greitai kyla → laikinai padidinamas ventiliatoriaus greitis.

6. 🌙🌤️ **Diena / Naktis**  
   Standartinis režimas pagal laiką ir CO₂ laiptus.

---

# ⚙️ Techniniai saugikliai

| Saugiklis | Funkcija |
|------------|----------|
| **Alarm Fallback** | HA restarto metu naudojama paskutinė žinoma saugi signalizacijos būsena iš atminties |
| **Robust States** | Fallback keliai nuskaitant HA būsenų cache |
| **RBE Filtras** | Komandos siunčiamos tik pasikeitus būsenai |
| **Hold Timer** | „Rate Boost“ režimas išlaikomas min. 10 min. |
| **Low-CO₂ Guard** | Rate Boost neaktyvuojamas, jei CO₂ < 600 ppm |

---

# 📊 Sukuriami Home Assistant sensoriai

Srautas automatiškai maitina šiuos subjektus:

### `sensor.komfovent_logic`
Dabartinis logikos režimas (lietuviškai), pvz.:

- Diena  
- Naktis  
- CO₂ kilimas  
- OVR  
- STOP  

### `sensor.current_co2_rate`
CO₂ kilimo greitis (ppm/min).

### `binary_sensor.rate_boost_active`
Indikatorius, rodantis aktyvų „Boost“ režimą.

---

# 🛠️ Naudojami pagrindiniai entity

- `alarm_control_panel.home` — Signalizacija  
- `sensor.oro_stotele_carbon_dioxide` — CO₂ jutiklis  
- `sensor.bathroom_humidity` — Drėgmės jutiklis  
- `binary_sensor.langai` — Langų kontaktai  
- `sensor.boiler_outside_temperature` — Lauko temperatūra  
- `switch.start_stop` — Rekuperatoriaus valdymas  
- `number.intake_level_1` — Įsiurbimo ventiliatoriaus greitis  
- `number.exhaust_level_1` — Ištraukimo ventiliatoriaus greitis  

---

# 📦 Architektūra

- Node-RED — sprendimų priėmimo logika  
- Home Assistant — sensoriai ir UI  
- Context Storage — telemetrijos kaupimas (iki ~14 dienų)  

---

# 🔮 Tolimesni patobulinimai

- STOP FLAGS debounce filtras  
- CO₂ glitch apsauga (>2000 ppm sanity cap)  
- Minimalus palaikomas ventiliavimas vasarą  
- Automatinis force_mode reset po testų
- CO₂ auto threshold learning integracija

---

# 📜 Versija

**v3.2.3**  
Stabili versija su:

- pataisyta robust state logika  
- LT režimų atvaizdavimu  
- išvalyta Rate telemetry schema  
- stabilizuotu RBE veikimu  
