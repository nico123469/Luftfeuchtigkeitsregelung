# Blueprint: Luftentfeuchter Automatisierung 

Dieses Blueprint automatisiert die Steuerung eines Luftentfeuchters basierend auf Innen- und Außenluftfeuchtigkeit sowie dem Taupunkt im Innenraum und der Außentemperatur. Die Automatisierung unterscheidet zwischen warmem und kaltem Wetter (Außentemperatur höher oder niedriger als der Taupunkt innen, [Kondensationsrisiko]). Die jeweiligen Grenzen der Hysterese können dabei frei gewählt werden. Zusätzlich gibt es einen Override-Schalter, welcher die Automatisierung dauerhaft in den Kaltwettermodus schaltet.

---

## Funktion der Automatisierung

Dieses Blueprint automatisiert die Steuerung eines Luftentfeuchters basierend auf Innen- und Außentemperatur sowie der Luftfeuchtigkeit. Die Hauptfunktionen der Automatisierung sind:

1. **Taupunktberechnung**: Mithilfe der Innenraum-Sensoren für Temperatur und Luftfeuchtigkeit wird der Taupunkt berechnet. Der Taupunkt ist die Temperatur, bei der die Luftfeuchtigkeit kondensieren würde.

2. **Wetterunterscheidung**: Die Automatisierung unterscheidet zwischen warmem und kaltem Wetter, basierend auf der Außentemperatur im Vergleich zum höchsten Taupunktwert im Innenraum:
   - **Warmwetter-Modus**: Wenn die Außentemperatur höher als der höchste Innen-Taupunkt ist.
   - **Kaltwetter-Modus**: Wenn die Außentemperatur niedriger als der höchste Innen-Taupunkt ist (erhöhtes Kondensationsrisiko).

3. **Hysterese-Grenzen**: Für beide Wettermodi (warm/kalt) können jeweils individuelle Ober- und Untergrenzen der Luftfeuchtigkeit festgelegt werden, um den Luftentfeuchter zu steuern:
   - **Einschalten des Luftentfeuchters**: Wenn die mittlere Luftfeuchtigkeit im Innenraum den festgelegten Maximalwert überschreitet.
   - **Ausschalten des Luftentfeuchters**: Wenn die mittlere Luftfeuchtigkeit unter den festgelegten Minimalwert fällt.

4. **Regelmäßige Überprüfung**: Die Automatisierung prüft alle fünf Minuten die aktuellen Werte der Sensoren, um gegebenenfalls den Luftentfeuchter ein- oder auszuschalten.

5. **Manuelle Override-Funktion**: Es gibt einen Override-Schalter, der den Kaltwettermodus manuell aktivieren kann, unabhängig von der Außentemperatur. Dies ermöglicht es, den Luftentfeuchter auch bei moderaten Außentemperaturen gezielt im Kaltwettermodus zu betreiben. (um z.B. Wäsche schneller zu trocknen)

---

## Anforderungen

- **Sensoren**:
  - Innen-Luftfeuchtigkeitssensor(en) (`sensor`)
  - Innen-Temperatursensor(en) (`sensor`)
    Wenn mehrere Sensoren für den Innenraum angegeben werden, wird für die Steuerung das Arythmetische mittel der Luftfeuchtigkeitsmessungen verwendet.
  - Außen-Luftfeuchtigkeitssensor (`sensor`)
  - Außen-Temperatursensor (`sensor`)

- **Schalter**:
  - Entfeuchter-Schalter (`switch`)
    zum Beispiel über einen Smartplug.

- **Variablen**:
  - `input_boolean.manual_cold_weather_override`: Überschreibt die Erkennung kalter Wetterbedingungen.
  - `input_number.max_humidity_warm`: Maximal zulässige Luftfeuchtigkeit bei warmem Wetter.
  - `input_number.min_humidity_warm`: Minimal zulässige Luftfeuchtigkeit bei warmem Wetter.
  - `input_number.max_humidity_cold`: Maximal zulässige Luftfeuchtigkeit bei kaltem Wetter.
  - `input_number.min_humidity_cold`: Minimal zulässige Luftfeuchtigkeit bei kaltem Wetter.

---

## Installation

### 1. Blueprint importieren

### Option 1: My Home Assistant

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fnico123469%2FLuftfeuchtigkeitsregelung%2Fraw%2Frefs%2Fheads%2Fmain%2FLuftentfeuchter_Automatisierung.yaml)

### Option 2: Direct Link
   
   - Gehe in Home Assistant zu **Einstellungen** → **Blueprints** → **Blueprint importieren**.
   - Füge den [Link](https://github.com/nico123469/Luftfeuchtigkeitsregelung/raw/refs/heads/main/Luftentfeuchter_Automatisierung.yaml) in das **URL-Feld** ein und klicke auf **Importieren**.


### 2. Entitäten konfigurieren

Erstelle die benötigten `input_number`- und `input_boolean`-Entitäten in deiner `configuration.yaml`:

```yaml
input_boolean:
  manual_cold_weather_override:
    name: Manueller Kaltes Wetter Override
    icon: mdi:snowflake

input_number:
  max_humidity_warm:
    name: Max Luftfeuchtigkeit Warm
    initial: 60
    min: 0
    max: 100
    step: 1
    unit_of_measurement: '%'
  min_humidity_warm:
    name: Min Luftfeuchtigkeit Warm
    initial: 42
    min: 0
    max: 100
    step: 1
    unit_of_measurement: '%'
  max_humidity_cold:
    name: Max Luftfeuchtigkeit Kalt
    initial: 55
    min: 0
    max: 100
    step: 1
    unit_of_measurement: '%'
  min_humidity_cold:
    name: Min Luftfeuchtigkeit Kalt
    initial: 42
    min: 0
    max: 100
    step: 1
    unit_of_measurement: '%'
```
---
## Anpassung
  - ### Zeitintervall
    - Ändere den Wert im time_pattern-Trigger, um die Überprüfungshäufigkeit anzupassen:
```yaml
trigger:
  - platform: time_pattern
    minutes: "/5"  # Alle 5 Minuten
```
- ### Grenzwerte
    - Ändere die Werte der einzelnen `input_number`.

---

## Beispiel Aufbau
### 1.Hardware
   - [SwitchBot](https://eu.switch-bot.com/products/switchbot-indoor-outdoor-thermo-hygrometer) Indoor/Outdoor Thermo-Hygrometer
   - [IKEA](https://www.ikea.com/at/de/p/tretakt-steckdose-smart-80540349/) Tradfri TRETAKT Smartplug
### 2.Aufbau
   Zwei Thermo-Hygrometer im wohnzimmer an unterschiedlichen Orten.
   Den Luftentfeuchter mit dem Smartplug angesteckt.
   Wetter-API für Außen-Messwerte.
### 3.Konfiguration
![Screenshot der Konfiguration meiner Automatisierung.](https://github.com/nico123469/Luftfeuchtigkeitsregelung/blob/main/Screenshot01.png?raw=true)
```yaml
alias: Luftentfeuchter Automatisierung
description: ""
use_blueprint:
  path: github.com/Luftentfeuchter_Automatisierung.yaml
  input:
    indoor_humidity_sensors:
      - sensor.switchbot_th_01_hum
      - sensor.switchbot_th_02_hum
    indoor_temperature_sensors:
      - sensor.switchbot_th_01_temp
      - sensor.switchbot_th_02_temp
    outdoor_humidity_sensor: sensor.weather_home_hum
    outdoor_temperature_sensor: sensor.weather_home_temp
    dehumidifier_switch: switch.dehumidifier
```
---

# ToDo✔️:


- ⭕ Bit um fest zu stellen ob die automatisierung im Kalt- oder Warmwetter-Modus ist.
- ⭕ Variable für verwendete hum innen
- ⭕ Variable für verwendete hum außen
- ⭕ Variable für min TP
- ⭕ Variable für verwendete temp außen
- ⭕ input fenstersensoren für autoabschaltung

