# Luftentfeuchter Automatisierung Blueprint

Dieses Blueprint automatisiert die Steuerung eines Luftentfeuchters basierend auf Innen- und Außentemperatur sowie der relativen Luftfeuchtigkeit. Es ermöglicht eine dynamische Anpassung der Luftfeuchtigkeitsgrenzen über `input_number`-Entitäten und bietet die Möglichkeit, kaltes Wetter manuell zu überschreiben.

---

## Features

- **Dynamische Feuchtigkeitsgrenzen**: 
  - Verwende `input_number`-Entitäten, um die minimalen und maximalen Luftfeuchtigkeitswerte für warme und kalte Wetterbedingungen zu definieren.
  - Änderungen an den Entitäten wirken sich sofort auf die Automatisierung aus.

- **Taupunktberechnung**: 
  - Berechnet die Taupunkte auf Basis der Innen-Luftfeuchtigkeit und -Temperatur.
  - Erkennt kalte Wetterbedingungen automatisch anhand des höchsten Taupunkts im Vergleich zur Außentemperatur.

- **Manueller Wetter-Override**:
  - Ein `input_boolean` ermöglicht es, kaltes Wetter manuell zu erzwingen.

- **Zeitbasierte Steuerung**:
  - Die Automatisierung wird alle 5 Minuten ausgeführt, um die Bedingungen zu überprüfen und den Luftentfeuchter ein- oder auszuschalten.

- **Schaltlogik**:
  - Aktiviert den Luftentfeuchter, wenn die Luftfeuchtigkeit über den festgelegten Maximalwert steigt.
  - Deaktiviert den Luftentfeuchter, wenn die Luftfeuchtigkeit unter den festgelegten Minimalwert fällt.

---

## Anforderungen

- **Sensoren**:
  - Innen-Luftfeuchtigkeitssensor(en) (`sensor`)
  - Innen-Temperatursensor(en) (`sensor`)
  - Außen-Luftfeuchtigkeitssensor (`sensor`)
  - Außen-Temperatursensor (`sensor`)

- **Schalter**:
  - Entfeuchter-Schalter (`switch`)

- **Optionale Entitäten**:
  - `input_boolean.manual_cold_weather_override`: Überschreibt die Erkennung kalter Wetterbedingungen.
  - `input_number.max_humidity_warm`: Maximal zulässige Luftfeuchtigkeit bei warmem Wetter.
  - `input_number.min_humidity_warm`: Minimal zulässige Luftfeuchtigkeit bei warmem Wetter.
  - `input_number.max_humidity_cold`: Maximal zulässige Luftfeuchtigkeit bei kaltem Wetter.
  - `input_number.min_humidity_cold`: Minimal zulässige Luftfeuchtigkeit bei kaltem Wetter.

---

## Installation

### 1. Blueprint importieren

Lade die YAML-Datei dieses Blueprints in deinen `blueprints/automation`-Ordner.

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
### 3. Blueprint konfigurieren

- Gehe in die Automatisierungsoberfläche von Home Assistant.
- Wähle "Blueprint verwenden" und suche das importierte **Luftentfeuchter Automatisierung Blueprint**.
- Fülle die erforderlichen Felder aus:
  - Wähle die Sensoren für die Innen-Luftfeuchtigkeit und Innen-Temperatur.
  - Gib die Sensoren für Außen-Luftfeuchtigkeit und Außen-Temperatur an.
  - Wähle den Schalter für den Luftentfeuchter.
- Optional:
  - Gib die Entität für den manuellen Wetter-Override an (`input_boolean.manual_cold_weather_override`).
  - Nutze die vordefinierten `input_number`-Entitäten, um die Feuchtigkeitsgrenzen dynamisch anzupassen.
- Speichere die Automatisierung.

Sobald dies abgeschlossen ist, steuert das Blueprint deinen Luftentfeuchter basierend auf den definierten Bedingungen.
