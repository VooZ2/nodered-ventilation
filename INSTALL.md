# 🛠 Installation & Configuration Guide

This project is a **DIY home ventilation optimization system** built
around **Home Assistant**, **Node‑RED**, and **Komfovent ventilation
units**.

The system was developed for a specific home automation environment and
**is not a plug‑and‑play solution**. Running it in another environment
requires **adaptation, technical understanding, and configuration
changes**.

------------------------------------------------------------------------

# ⚠ Responsibility and Adaptation

## Personal Responsibility

Adapting this system to your own environment is entirely **your
responsibility**.

You will likely need to modify:

-   Home Assistant **Entity IDs**
-   Node‑RED **flow configuration**
-   sensor mappings
-   automation logic

Every home installation is different, therefore some customization will
always be required.

------------------------------------------------------------------------

## AI Assistance

This project was developed with assistance from **Gemini** and
**ChatGPT**.

When adapting the system to your own setup, AI tools can help with:

-   analyzing Node‑RED flows
-   adapting sensor names
-   rewriting logic blocks
-   troubleshooting configuration issues

------------------------------------------------------------------------

## Support Disclaimer

This repository is shared **for educational and experimental purposes**.

The author does **not provide individual support or consulting**.\
If the system does not work in your environment, you are expected to
investigate and adapt it yourself.

------------------------------------------------------------------------

# 📂 System Modules

For the system to work correctly (**v5 architecture**), import the
modules in the following order:

### 1. `komfovent-control.json`

Main ventilation control engine responsible for:

-   system priority logic
-   fan speed control
-   boost logic
-   hood integration
-   safety guards

### 2. `komfovent-learning.json`

Adaptive CO₂ learning module responsible for:

-   automatic CO₂ threshold adjustment
-   occupancy‑aware learning
-   day/week pattern detection

### 3. `komfovent-logging.json`

Centralized logging and telemetry:

-   telemetry storage
-   control event logging
-   historical data collection
-   predictive model data source

### 4. `komfovent-filters.json`

Filter lifetime monitoring:

-   effective filter runtime
-   filter wear estimation
-   maintenance reminders

### 5. `komfovent-ai_gateway.json`

AI integration layer providing:

-   Telegram interface
-   AI‑assisted system interpretation
-   audit snapshots
-   predictive diagnostics

------------------------------------------------------------------------

# ⚙ Minimum Requirements

Before importing the flows, ensure that the following components exist
in your **Home Assistant** environment.

------------------------------------------------------------------------

## 1️⃣ Helper Entities

### `input_number.co2_on_threshold`

Numeric helper used by the adaptive learning module.

The system automatically updates this value **daily at 03:00 (local
time)**.

------------------------------------------------------------------------

## 2️⃣ Required Directories

Inside the **Node‑RED container**, the following directories must exist
(or be mounted as volumes):

    /data/logs/komfovent/
    /data/state/komfovent/

### `/data/logs/komfovent/`

Used for long‑term telemetry logging.\
Data is stored in **JSONL format**.

### `/data/state/komfovent/`

Used for persistent state storage including:

-   filter state
-   learning variables
-   runtime data

This ensures the system can recover state after restarts.

------------------------------------------------------------------------

## 3️⃣ Centralized Configuration

All **Home Assistant Entity IDs** must be configured in one place.

Node‑RED node:

`Init: Global Settings`

Location:

`Komfovent - Module: Logging`

After modifying configuration values, press the **Inject** button to
load them into global context.

------------------------------------------------------------------------

# 🌡 Required Sensors

The following sensors are required for the system to operate correctly.

### Indoor CO₂ Sensor

Example:

`sensor.indoor_co2`

Used for:

-   ventilation activation
-   predictive analysis
-   learning model input

------------------------------------------------------------------------

### Indoor Humidity Sensor

Example:

`sensor.indoor_humidity`

Used for:

-   humidity‑based ventilation boost
-   bathroom moisture control

------------------------------------------------------------------------

### Outdoor Temperature Sensor

Example:

`sensor.outdoor_temperature`

Used for:

-   environmental context
-   predictive model calibration
-   ventilation strategy adjustments

------------------------------------------------------------------------

# 👍 Recommended Sensors

These sensors are not strictly required but **greatly improve system
behaviour**.

  Sensor                   Purpose
  ------------------------ -------------------------------------------
  Window sensors           Detect prolonged window ventilation
  Alarm state              Determine occupancy
  Bathroom humidity        Trigger OVR ventilation
  Kitchen hood power       Detect hood usage and compensate pressure
  Intake air temperature   Diagnostics and airflow analysis

------------------------------------------------------------------------

## Window Sensors

Examples:

-   `binary_sensor.window_living_room`
-   `binary_sensor.window_bedroom`

Used by the **Windows Guard** logic.\
If windows remain open longer than the configured threshold, ventilation
may be reduced or disabled.

------------------------------------------------------------------------

## Alarm / Occupancy State

Recommended entity:

`alarm_control_panel.home`

When the alarm is **armed**, the system assumes the home is unoccupied.\
Learning algorithms will ignore CO₂ data during that period.

------------------------------------------------------------------------

## Bathroom Humidity Sensor

Example:

`sensor.bathroom_humidity`

Used to activate **OVR ventilation boost** during shower or high
moisture events.

------------------------------------------------------------------------

## Kitchen Hood Power Sensor

Example:

`sensor.hood_socket_power`

Used to detect kitchen hood activity and activate **pressure
compensation mode**.

------------------------------------------------------------------------

# 🤖 AI Contribution

This project --- including its **architecture, code structure, and
documentation** --- was developed in collaboration with **AI models
(Gemini and ChatGPT)**.

AI assistance helped with:

-   architecture design
-   telemetry schema
-   Node‑RED flow structure
-   documentation generation

However, **human supervision is required during deployment and
configuration**.

------------------------------------------------------------------------

# ⚠ Important

This project is intended for **advanced Home Assistant users** familiar
with:

-   Node‑RED
-   Home Assistant entities
-   container environments
-   automation debugging

Expect some manual configuration and troubleshooting when deploying the
system.
