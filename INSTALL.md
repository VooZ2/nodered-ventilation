# Komfovent Smart Automation - Diegimo instrukcija

## Sistemos reikalavimai

Prieš importuojant srautus būtina užtikrinti, kad Home Assistant
aplinkoje egzistuoja šios būsenos (entity):

### Privalomi jutikliai ir būsenos

-   Signalizacijos būsena (pvz. `alarm_control_panel.home`)
-   CO₂ jutiklis (pvz. `sensor.oro_stotele_carbon_dioxide`)
-   Vonios drėgmės jutiklis (pvz. `sensor.bathroom_humidity`)
-   Lauko temperatūros jutiklis (pvz.
    `sensor.boiler_outside_temperature`)
-   Durų / langų kontaktinis sensorius (pvz. `binary_sensor.langai`)
-   Rekuperatoriaus start/stop jungiklis (pvz. `switch.start_stop`)
-   Oro padavimo lygio valdymas (pvz. `number.intake_level_1`)
-   Oro ištraukimo lygio valdymas (pvz. `number.exhaust_level_1`)
-   OVR relė (jei naudojama) (pvz. `switch.recup_switch_relay`)
-   Pranešimų siuntimo paslauga (pvz. `notify.mobile_app_*`)

Jei naudojami kitokie pavadinimai - jie turi būti atnaujinti
funkciniuose mazguose po importo.

------------------------------------------------------------------------

## 1. Flow importavimas

1.  Atsisiųskite reikalingus failus:
    -   `komfovent-control.json`
    -   `komfovent-learning.json`
2.  Node‑RED → Meniu → **Import**
3.  Įklijuokite arba įkelkite failą
4.  Pasirinkite naują skirtuką (Tab)

------------------------------------------------------------------------

## 2. Adaptyvaus CO₂ slenksčio helper

Home Assistant aplinkoje turi būti sukurtas:

`input_number.co2_on_threshold`

Šį helperį sistema automatiškai atnaujina kas parą.

------------------------------------------------------------------------

## 3. Paleidimas

Po importo rekomenduojama:

-   Perkrauti Node‑RED
-   Patikrinti, ar visi entity pasiekiami
-   Patikrinti Node status indikatorius

------------------------------------------------------------------------

## 4. Diagnostika

Node‑RED status indikatoriai rodo:

-   Aktyvų logikos režimą
-   STOP arba saugiklių būsenas
-   CO₂ tendenciją
-   Adaptyvų slenkstį

Home Assistant aplinkoje matomi papildomi sistemos sensoriai logikos
stebėjimui.

------------------------------------------------------------------------

## Pastabos

-   Jei dalis jutiklių laikinai nepasiekiami, mokymosi modulis gali būti
    blokuojamas (apsaugos mechanizmai).
-   Srautai sukurti veikti kartu - kontrolės ir mokymosi moduliai turi
    būti importuoti abu.
