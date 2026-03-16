# 🌬️ Komfovent Smart Ventilation Automation

Advanced **predictive ventilation control system** built with
**Node‑RED** and integrated with **Home Assistant**.

Originally developed with a Komfovent ventilation unit, but the control
logic is **device‑agnostic** and can be adapted to other ventilation
systems that support control through Home Assistant.

<img width="1450" height="508" alt="Screenshot 2026-02-12 at 22 46 20" src="https://github.com/user-attachments/assets/40f036b9-5d66-4f8a-9ecc-0869ea52c75c" />

------------------------------------------------------------------------

# 🔮 Predictive Ventilation Engine

The **Predictive Engine** is the core of the system.

Instead of reacting only to current CO₂ values, the system predicts
future air quality and adjusts ventilation **before discomfort occurs**.

### What the model evaluates

The predictive model continuously analyzes:

-   current CO₂ concentration
-   CO₂ rise rate (ppm/min)
-   historical behavior patterns
-   occupancy context
-   time-of-day patterns

### What it calculates

The model produces:

-   **CO₂ forecast (+30 minutes)**
-   **CO₂ forecast (+60 minutes)**
-   **risk score (0‑10)**
-   **forecast confidence**

### Prediction model

The system builds a **historical matrix** from telemetry logs using time
buckets:

-   weekday vs weekend
-   hour of day
-   occupancy state

Each bucket stores historical averages such as:

-   mean CO₂
-   CO₂ variability
-   average rise rate
-   rate variance

Using these statistics and the current CO₂ trend, the engine estimates
how likely air quality is to deteriorate soon.

### Control impact

Predictive control **does not override safety priorities**.

It only takes control when:

-   predictive mode is enabled
-   the model is marked **LIVE READY**
-   no higher priority mode is active

Higher priority states include:

-   STOP FLAGS
-   Bathroom OVR
-   Armed Away
-   Window Guard
-   Kitchen Hood mode

This ensures predictive control **enhances comfort without breaking
system safety logic**.

------------------------------------------------------------------------

# ✨ Key Features

### 🧠 Intelligent Ventilation Control

-   real‑time CO₂ based ventilation
-   multi‑priority decision engine
-   adaptive learning model
-   predictive ventilation adjustments

### 🍳 Kitchen Hood Compensation

-   detects hood usage via power sensor
-   automatic **positive pressure mode**
-   dynamic supply/exhaust balancing
-   automatic post‑run odor removal

### 📈 Rate‑Based Boost

-   reacts to **CO₂ rise speed**
-   anticipates occupancy events
-   hold and cooldown protection
-   early termination when air improves

### 🤖 AI‑Assisted Diagnostics

-   Gemini AI interprets system behavior
-   natural language queries via Telegram
-   smart log gating for efficient analysis
-   anomaly explanations

------------------------------------------------------------------------

# 🧠 Real‑Time Control Logic

Ventilation decisions follow strict priority order:

1.  **STOP FLAGS** -- system halted during faults or maintenance
2.  **Bathroom OVR** -- humidity \>80% activates maximum ventilation
3.  **Armed Away** -- short ventilation cycle when house is empty
4.  **Kitchen Hood** -- pressure compensation mode
5.  **Window Guard** -- ventilation reduced when windows are open
6.  **Day / Night Mode** -- baseline ventilation profile

------------------------------------------------------------------------

# 🍳 Kitchen Hood Mode

The system compensates for kitchen hood extraction by increasing supply
airflow and reducing exhaust airflow, creating **positive pressure**
inside the home.

  Hood Power   Supply   Exhaust
  ------------ -------- ---------
  ≥ 85W        80%      45%
  ≥ 105W       90%      50%
  ≥ 145W       100%     55%

### Post‑Run

After the hood turns off:

-   duration: **5 / 8 / 12 minutes**
-   mode: **IN 65% / OUT 65%**
-   purpose: balanced odor removal

------------------------------------------------------------------------

# 📊 Base Ventilation (CO₂)

  CO₂ (ppm)   Fan Speed
  ----------- -----------
  \<650       20%
  650‑709     45%
  710‑849     55%
  850‑1099    70%
  ≥1100       100%

IN and OUT airflow remain balanced except during hood compensation.

------------------------------------------------------------------------

# 📈 Active Ventilation Boost

Boost activates based on **CO₂ rise rate**, not just absolute level.

Conditions:

-   threshold ≥ **12 ppm/min**
-   two confirmations required
-   **15 min hold**
-   **20 min cooldown**
-   allowed only if CO₂ ≥ (auto threshold − 50)

Boost raises airflow to at least **55%**.

------------------------------------------------------------------------

# 🤖 AI Interaction

Gemini AI acts as a **virtual ventilation engineer**.

Example commands:

    /status
    /co2
    /fan

You can also ask natural language questions:

-   "Why did ventilation increase now?"
-   "When was the hood active today?"

The system uses **Smart Gating**, sending historical logs to AI only
when necessary.

<img width="1256" height="871" alt="Screenshot 2026-02-18 at 20 52 42" src="https://github.com/user-attachments/assets/11930ba9-006c-4084-bae4-642ccb76077f" />

------------------------------------------------------------------------

# 📊 Telemetry

All decisions are logged and observable.

Available telemetry includes:

-   CO₂ levels
-   fan speeds (IN / OUT)
-   outdoor temperature
-   intake air temperature
-   humidity
-   hood activity

Node‑RED performs all calculations; Home Assistant provides sensors and
integrations.

<img width="1256" height="871" alt="Screenshot 2026-02-18 at 20 52 42" src="https://github.com/user-attachments/assets/11930ba9-006c-4084-bae4-642ccb76077f" />

------------------------------------------------------------------------

# 🧹 Filter Monitoring

A dedicated module tracks filter wear using **real fan runtime**.

Features:

-   effective runtime calculation
-   filter wear percentage
-   maintenance reminders

------------------------------------------------------------------------

# ⚙ Minimum Requirements

### Software

-   Home Assistant
-   Node‑RED

### Ventilation Device

Any system controllable through Home Assistant (examples):

-   Komfovent
-   Zehnder
-   Brink
-   Systemair
-   other Modbus/API units

### Required Sensors

-   indoor CO₂ sensor
-   indoor humidity sensor
-   outdoor temperature sensor

### Recommended Sensors

-   window sensors
-   kitchen hood power sensor
-   bathroom humidity sensor
-   alarm / occupancy state

------------------------------------------------------------------------

# ⚠ Disclaimer

This project was created for **personal use** and shared for educational
purposes.

The architecture, code and documentation were developed with assistance
from **Gemini** and **ChatGPT**.

Use and adapt the system **at your own responsibility**.
