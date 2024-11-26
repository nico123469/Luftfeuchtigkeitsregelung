# !!!WIP!!!

# Blueprint: Luftentfeuchter Automatisierung 

Dieses Blueprint automatisiert die Steuerung eines Luftentfeuchters basierend auf Innen- und Außentemperatur sowie dem Taupunkt im Innenraum. Die Automatisierung unterscheidet zwischen warmem und kaltem Wetter (Außentemperatur höher oder niedriger als der Taupunkt innen, [Kondensationsrisiko]). Die jeweiligen Grenzen der Hysterese können dabei frei gewählt werden. Zusätzlich gibt es einen Override-Schalter, welcher die Automatisierung dauerhaft in den Kaltwettermodus schaltet.

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
  - Außen-Luftfeuchtigkeitssensor (`sensor`)
  - Außen-Temperatursensor (`sensor`)

- **Schalter**:
  - Entfeuchter-Schalter (`switch`)
    zum Beispiel über einen Smartplug

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
  - Wähle beim Hinzufügen der Automatisierung die passenden Entitäten für deine Sensoren und Schalter aus.
---
## Anpassung
  - ### Zeitintervall
    - Ändere den Wert im time_pattern-Trigger, um die Überprüfungshäufigkeit anzupassen:
```yaml
trigger:
  - platform: time_pattern
    minutes: "/5"  # Alle 5 Minuten
