# ESPHome-Froeling-Lambdatronic_3200

> **Fork mit Erweiterungen für Hackgut-Kessel (Fröling T4e)**
> Original: [GyroGearl00se/ESPHome-Froeling-Lambdatronic_3200](https://github.com/GyroGearl00se/ESPHome-Froeling-Lambdatronic_3200) – alle Original-Dateien bleiben unverändert nutzbar.

## Erweiterungen in diesem Fork

| Datei | Inhalt |
|---|---|
| `allgemein.yaml` | Werte, die jeder Kessel hat: Betriebsstunden Ascheschnecke, Tages-/Gesamtertrag, aufgenommene Energie |
| `hackgut.yaml` | **Hackgut (z. B. T4e):** Stromaufnahme Austrag-/Förder-/Stokerschnecke, Zellradschleuse, Rührwerk, Schnecke 1/2, Betriebsstunden |
| `pellet.yaml` | **Pellet:** Stromaufnahme Stokerschnecke und Zellradschleuse, Betriebsstunden Stoker – zusammen mit `austragung.yaml` verwenden |
| `fehler.yaml` | Fehlerpuffer (Register 33001–33003): aktive Meldungen im Klartext, „Meldung aktiv“, „Aschebox voll“ |
| `boiler_02.yaml`, `puffer_02.yaml` | Zweiter Boiler bzw. zweiter Puffer |
| `froeling-t4e-schwaxi.yaml` | Beispiel einer kompletten Geräte-Konfiguration (T4e, ESP32 DevKit, RS232 an GPIO1/GPIO3) |

`hackgut.yaml` und `pellet.yaml` definieren teilweise dieselben Sensoren – **nur eine der beiden Dateien einbinden.**

Hinweise T4e:
- Förderschnecke (30030) und Zellradschleuse (30031) liefern beim T4e identische Werte (gemeinsame Messung).
- Rührwerk und Schnecke 1/2 (30094–30096) hängen von der Austragung ab und können 0 bleiben.
- Die „Stromaufnahme der Ascheschnecke“ (Adresse 148) gibt es laut Fröling nicht und wird nicht mehr ausgelesen.

Getestet mit: Fröling T4e, Lambdatronic 3200, ESPHome 2026.10+.

### Einbindung

```yaml
packages:
  remote_package:
    url: https://github.com/schwaxi/ESPHome-Froeling-Lambdatronic_3200
    ref: main
    files:
      - allgemein.yaml
      - fehler.yaml
      - kessel.yaml
      - heizkreis01.yaml
      - boiler_01.yaml
      - puffer_01.yaml
      - hackgut.yaml        # Hackgut
      # - pellet.yaml       # Pellet ...
      # - austragung.yaml   # ... zusammen mit austragung.yaml
    refresh: 1d
```

API-Key, OTA-Passwort und WLAN gehören in die `secrets.yaml` von ESPHome (`!secret ...`), nicht ins Repository.

---

![Dashboard - AllInOne](docs/ha_dashboard_aio.png)
![Dashboard - Kessel](docs/ha_dashboard_kessel.png)
![Dashboard - Heizkörper](docs/ha_dashboard_heizkoerper.png)
![Dashboard - Boiler 01](docs/ha_dashboard_boiler01.png)
![Dashboard - Puffer 01](docs/ha_dashboard_puffer01.png)
![Dashboard - Austragung](docs/ha_dashboard_austragung.png)
![Dashboard - Zirkulationspumpe](docs/ha_dashboard_zirk-pumpe.png)

## ESHome RS232 to TTL - ESP32 - Read data from Froeling / Lambdatronic 3200

I'm testing this with my Fröling SP Dual 20 (V 50.04 B 05.19). But I assume it could also work for other models that have the Lambdatronic 3200 control unit. Feedback appreciated.
I requested and received from Fröling support the ModBus communication protocol for V 50.04 B 05.19 (see pdf file).

### Parts
  
Wemos D1 Mini ESP32:
<https://www.amazon.de/dp/product/B08BTRQNB3>

MAX3232 DB9 RS232 TTL converter:
<https://www.amazon.de/gp/product/B0BNQ41QKQ>

![wiring diagram](docs/wemos-rs3232-ttl.png)
  
|  Wemos | GPIO17  | GPIO16  | GND  | 3V3 (3,3V)  |
|---|---|---|---|---|
| MAX3232  | TX  |  RX |  GND | VCC  |

Connect an RS232 (DB9) cable between the "MAX3232 DB9 RS232 TTL converter" and your boiler's mainboard (COM2).

### Boiler Settings

- Click on the user icon
- Enter code "-7"
- Settings > General Settings > MODBUS Settings > Modbus Protokoll RTU (1)
- Settings > General Settings > MODBUS Settings > use Modbus Protokoll 2014 -> yes
- Settings > General Settings > MODBUS Settings > Use COM2 as MODBUS Interface -> yes

### Home Assistant Dashboard

I've created dedicated cards for the heating system. Feel free to checkout them out: https://github.com/GyroGearl00se/lovelace-froeling-card

### ESPHome

I divided the yaml file into several sections and added them to the main file as a remote package to provide a better overview.  
Just flash the "froeling.yaml" to your ESP32. (Edit API, OTA & WiFi according to your device/environment)

As the setup of the heating unit can vary a lot for everyone remove the reference to the *.yaml files for the components you don't need before flashing.

```
packages:
  remote_package:
    url: https://github.com/GyroGearl00se/ESPHome-Froeling-Lambdatronic_3200
    ref: main
    files: [
      austragung.yaml,
      boiler_01.yaml,
      heizkreis01.yaml,
      heizkreis02.yaml,
      kessel.yaml,
      puffer_01.yaml,
      zirkulationspumpe.yaml,
      solarthermie.yaml,
      ]

```

### 3D Printed case

I've designed a housing for a Wemos ESP32 & RS232 Adapter. The front section is a bit fragile - But other than that. It works quite well. At the bottom, you'll find round inlays where you could fit in some 3x10mm magnets to attach the case easily to the housing of your heating unit.

![3D_Print_Case_mounted.jpg](docs/3D_Print_Case_mounted.jpg)
(Picture without lid to give you a better idea)

![3D_Print_Case.gif animation](docs/3D_Print_Case.gif)
![3D_Print_Case.png](docs/3D_Print_Case.png)
![3D_Print_Case_slicer.png](docs/3D_Print_Case_slicer.png)

### Contribution
  
Wrong/Missing data? Feel free to create an issue or fork this repository and create a pull request with your fix.
