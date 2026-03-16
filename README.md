# 🌬️ Komfovent Smart Automation

Professional ventilation automation and adaptive CO₂ learning system built for **Node-RED** and integrated with **Home Assistant**.

The system dynamically adjusts ventilation in real time, adapts to occupant behavior, and can be audited by **Gemini AI** to maximize comfort and energy efficiency.

<img width="1450" height="508" alt="Screenshot 2026-02-12 at 22 46 20" src="https://github.com/user-attachments/assets/40f036b9-5d66-4f8a-9ecc-0869ea52c75c" />

---

# ⚠ Project Disclaimer

This project was created for **personal use**.  
The GitHub repository exists primarily for **version tracking and documentation**.

The system is **not plug-and-play**. It may require adaptation for your own environment.

**Important:**  
The system architecture, code, and documentation were developed with assistance from **Gemini** and **ChatGPT** AI models.

---

# ✨ Key Features

### 🧠 Intelligent Ventilation Control
- Real-time CO₂-based ventilation regulation
- Multi-level priority control system
- Automatic day/night operation
- Adaptive learning based on historical data

### 🍳 Kitchen Hood Compensation
- Detects hood activity via power sensor
- Creates **positive pressure** to prevent smoke return
- Dynamic IN/OUT fan balancing
- Automatic post-run odor removal

### 📈 Rate-Based Boost
- Detects **CO₂ rise speed (ppm/min)**
- Activates ventilation boost before CO₂ peaks
- Smart hold and cooldown logic
- Early stop when air quality improves

### 🤖 AI-Assisted Analysis
- Gemini AI interprets system behavior
- Natural language queries via Telegram
- Smart log gating for efficient analysis
- Automatic anomaly explanations

---

# 🧠 Real-Time Control Logic

Decisions are executed using strict priority order:

1. **STOP FLAGS** – System halted during faults or technical modes  
2. **Bathroom OVR** – >80% humidity activates full ventilation  
3. **Armed Away** – One-time ventilation when house is empty  
4. **Kitchen Hood** – Pressure compensation mode  
5. **Window Guard** – Ventilation reduced if windows remain open  
6. **Day / Night Mode** – Baseline ventilation profile

---

# 🍳 Kitchen Hood Mode

The system compensates for kitchen hood extraction by increasing supply airflow and limiting exhaust airflow, creating **positive pressure** inside the home.

### Power Levels

| Hood Power | Supply | Exhaust |
|------------|--------|--------|
| ≥ 85W (L1) | 80% | 45% |
| ≥ 105W (L2) | 90% | 50% |
| ≥ 145W (L3) | 100% | 55% |

Goal: prevent smoke return and cold air infiltration.

### Post-Run

After hood shutdown:

- Duration: **5 / 8 / 12 minutes**
- Mode: **IN 65% / OUT 65%**
- Purpose: balanced odor removal

> Hood compensation is ignored if windows remain open.

---

# 📊 Base Ventilation (CO₂)

| CO₂ (ppm) | Fan Speed |
|----------|----------|
| < 650 | 20% |
| 650–709 | 45% |
| 710–849 | 55% |
| 850–1099 | 70% |
| ≥ 1100 | 100% |

IN and OUT airflow remain **balanced** except during hood compensation.

---

# 📈 Active Ventilation Boost

The system reacts to **CO₂ rise speed**, not only the absolute level.

Boost conditions:

- Threshold ≥ **12 ppm/min**
- **2 confirmations** required
- **Hold:** 15 minutes
- **Cooldown:** 20 minutes
- Allowed only if CO₂ ≥ *(auto threshold − 50)*

Boost raises airflow to **at least 55%**.

---

# 🤖 Adaptive CO₂ Learning

The system recalculates the optimal activation threshold daily using **14-day history**.

Features:

- Separate weekday / weekend patterns
- Hood events excluded from learning
- Predictive risk evaluation

---

# 🤖 Gemini Analysis

Gemini AI acts as an **intelligent ventilation engineer**.

Interaction methods:

### Technical Commands
- `/status` — current system state
- `/co2` — indoor CO₂
- `/fan` — fan speed

### Natural Language Queries

Examples:

> *“When was the kitchen hood active today?”*  
> *“Why did ventilation increase now?”*

The system uses **Smart Gating** to send logs to the AI model **only when necessary**.

<img width="1256" height="871" alt="Screenshot 2026-02-18 at 20 52 42" src="https://github.com/user-attachments/assets/11930ba9-006c-4084-bae4-642ccb76077f" />

---

# 📊 Telemetry and Home Assistant

All system decisions are logged and observable in real time.

Available telemetry:

- CO₂ concentration
- fan speeds (IN / OUT)
- outdoor temperature
- supply air temperature
- humidity
- hood activity

Operational states include:

- Day / Night
- Bathroom OVR
- Armed Away
- Rate Boost
- Hood compensation

Home Assistant acts primarily as the **sensor and integration layer**, while all logic runs inside **Node-RED**.

<img width="1353" height="876" alt="Screenshot 2026-02-12 at 22 44 38" src="https://github.com/user-attachments/assets/1fe7d7c5-12a0-4346-b5e2-797aa92df2a8" />

---

# 🧹 Filter Monitoring

The system tracks filter wear based on **real fan runtime**.

Features:

- effective filter runtime calculation
- filter wear percentage
- maintenance reminders

---

# 🗂 Data Storage

System data is stored as **JSONL logs**.

Key principles:

- versioned telemetry schema
- long-term historical storage
- compatibility with evolving sensor sets
- suitable for AI-based analysis

---

# ⚙ Minimum Requirements

To run the system you need:

### Software
- **Home Assistant**
- **Node-RED**
- **Komfovent ventilation unit**

### Required Sensors
- Indoor **CO₂ sensor**
- Indoor **humidity sensor**
- **Outdoor temperature** sensor

### Recommended Sensors
- window sensors
- kitchen hood power sensor
- bathroom humidity sensor
- alarm / occupancy state
