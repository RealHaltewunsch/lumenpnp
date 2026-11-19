# Compact-500 Bring-up

**Stand 19.11.2026 · Nächster Test: Sekundäres Fiduzial 2,6 mm unter der primären Ebene neu ausmessen.**

Die Checkliste hält den jeweils nächsten Test, die Abnahmekriterien und die
bestätigten Ergebnisse fest. Ein Test ist abgeschlossen, sobald das Ergebnis
am Gerät oder im OpenPnP-Protokoll bestätigt wurde.

## Bestätigter Stand

- [x] Hardware-Homing und Achsrichtungen geprüft. Gemessener Verfahrbereich X 0–333 mm, Y 0–387 mm und Z 0–56 mm.
- [x] A und B drehen bei positiver Bewegung von oben gesehen gegen den Uhrzeigersinn. Die physische Winkelgenauigkeit wurde bisher nicht vermessen.
- [ ] J15-Gehäuselüfter am Gerät prüfen. Am 19.11.2026 geflasht und bytegenau verifiziert (131728 Bytes). Drei Sekunden Vollgas, anschließend 50 % PWM bei 250 Hz. TMC-Statusprüfung alle 500 ms. Bei aktueller Übertemperaturwarnung oder Übertemperatur eines Treibers 100 %, zurück auf 50 % sobald alle Treiber gültig ohne Warnung antworten. Umschaltung bei echter Übertemperatur steht zur Prüfung aus.
- [x] Beide Ringlichter funktionieren. Beide Vakuumkanäle melden im Ruhezustand 255 über `M3427`.
- [x] OpenPnP unter Windows kommuniziert über COM3 mit dem STM32 bei 115200 Baud, 8N1 und aktivem DTR. Nach dem Homing meldete der STM32 X=0, Y=0 und Z=24,5 mm. Nach der N2-Änderung meldete OpenPnP erneut erfolgreiches Homing.
- [x] Windows-Profil mit XY-Parkziel X=302,328 mm und Y=311,916 mm. N2 verwendet `Z_N2 = 63 mm − Z_Marlin`. Die Fahrt zum neuen Parkziel steht noch aus.

## 1. Bewegung in OpenPnP bestätigen

- [x] Z nach der mechanischen Änderung am Endschalter neu vermessen. Der Bediener bestätigte 63 mm maximalen Verfahrweg und eine gemeinsame sichere Höhe von 30 mm. Firmware am 19.11.2026 geflasht und 127488 Bytes per Readback verifiziert. Nach Anheben der Softwaregrenze erneut geflasht und 127488 Bytes verifiziert. Der finale Build meldet per M211 Z 0–63,0 mm. Homing-Vorschübe halbiert. Die aktive OpenPnP-Konfiguration und die Repo-Vorlage wurden auf Z 0–63 mm, Safe Z 30 mm und Home 30 mm abgeglichen. N2 verwendet 63 mm minus die physische Z-Position.

  - Nach dem Flash Endschalter-Homing ausgeführt. STM32 meldete X=0, Y=0, Z=24,5 mm. Anschließend Z=65,0 mm bei 5 mm/s angefahren. M400 bestätigte Bewegungsende, M114 meldete Z=65,0 mm (2600 Schritte). Bediener meldete nach Wiederholung, dass 65,0 mm zu weit sind. Nach erneutem Homing mit 5 mm/s zur vorgesehenen Mitte gefahren. STM32 meldete Z=31,50 mm (1260 Schritte). Danach den finalen 63-mm-Build mit 30 mm nach Homing geflasht und bytegenau verifiziert.

- [x] **1a** Z-Jogging mit N1 in OpenPnP bestätigt. Die Düse fuhr bei 0,1 mm Schrittweite von 24,5 auf 24,6 mm und wieder auf 24,5 mm zurück.
- **1b** Separater N2-Jogtest auf Wunsch übersprungen. Der Bediener bestätigt, dass N2 funktioniert.
- [x] Endschalter- und visuelles Homing mit dem 63-mm-Build am 19.11.2026 bestätigt. OpenPnP meldete um 12:36:51 Homed=true; der Status um 12:37:31 bestätigte X=167,270 mm, Y=195,830 mm und Z=30,000 mm am STM32 sowie Z=30,000 mm in der N1-Anzeige.
- [ ] **1c** Gemeinsame Z-Parkhöhe mit dem aktualisierten Profil prüfen. Erwartet werden N1=30 mm und N2=33 mm in OpenPnP sowie 30 mm am STM32.
  - Die vorherigen Parkwerte wurden am 19.11.2026 mit N1 (physisch 26,5 mm) und N2 (physisch 36,5 mm, Anzeige 19,5 mm) gemessen.
- [ ] **1d** XY-Parken bei reduziertem Tempo prüfen. Die Maschine erreicht das gespeicherte Ziel ohne Softlimit-Fehler oder Timeout. Falls ein `M400`-Timeout wiederkehrt, vor weiteren Fahrten den auslösenden Bewegungsbefehl und die STM32-Antwort protokollieren.

## 2. Kameras und Fiduziale einrichten

- [x] Kameragestützte XY-Kalibrierung in der Windows-Konfiguration vom 19.11.2026, 13:30:21, verifiziert. Gespeicherte Umkehrspielkorrektur: X=0,034875 mm und Y=0,056250 mm.
- [ ] Neues sekundäres Datum-Board testen. Der Bediener hat die Marke auf einer um 2,6 mm tieferen Ebene als die primäre Marke platziert. Frühere sekundäre Messwerte gehören zum vorherigen Aufbau. XY, Kameramaßstab und Z-Höhe der neuen Ebene erneut erfassen.

- [x] Ringlichter in OpenPnP einzeln getestet. Der Bediener bestätigte, dass Top (M150 S1) und Bottom (M150 S0) jeweils separat leuchten.
- [ ] Anti-Glare bei echten Kameraaufnahmen prüfen.
- [ ] Top- und Bottom-Kamera dem richtigen Windows-Gerät zuordnen. Livebild, Orientierung, Fokus, Licht und Bildstabilität prüfen.
- Aktives Profil am 19.11.2026 geprüft. Das Homing-Fiduzial ist nominal bei X=218,107 mm und Y=197,304 mm eingetragen. Visuelles Homing steht auf `None`. Die Kamera-Maßstäbe sind Profilwerte, eine Messung an dieser Maschine steht aus.
- [x] Homing-Fiduzial im aktiven Windows-Profil auf X=167,270 mm und Y=195,830 mm gesetzt. Quelle ist die mit Top zentrierte und per STM32 bestätigte Statusabfrage vom 19.11.2026 um 10:59:20. Visuelles Homing ist weiterhin deaktiviert.
- [x] Primäres Fiduzial mit der Top-Kamera manuell zentriert. OpenPnP und STM32 meldeten am 19.11.2026 um 10:41:42 X=167,240 mm und Y=195,850 mm. Das ist die gemessene Ausgangsposition für den Kalibrierassistenten.
- [x] Units per Pixel für Top manuell kalibriert und gespeichert. Die aktive Windows-Konfiguration enthält seit 10:56:43 für X und Y jeweils 0,005 mm/Pixel. Die Erfassung im Assistenten steht noch aus.
- [x] Top-Kameramaßstab und Bildorientierung am 19.11.2026 um 11:16:07 mit 1-mm-Fahrten geprüft. Im verarbeiteten Bild (1933 × 1101 px) wanderte das Fiduzial bei X+ 19,875 px nach links und bei Y+ 19,625 px nach unten. Daraus ergeben sich X=0,05031 und Y=0,05096 mm/Pixel. Der erkannte Kreis hatte in allen drei Bildern 22 px Durchmesser.
- [x] Der Bediener bestätigt dieselbe physische Marke als primäres Kalibrier- und Homing-Fiduzial. Beide XY-Positionen sind im Windows-Profil auf X=167,270 mm und Y=195,830 mm gesetzt. Assistent und visuelles Homing stehen noch aus.
- Der Bediener schloss den primären Assistenten mit Feature Diameter 20 px und Erkennungswert 4,91 ab. Der gesicherte Assistentenstand enthält X=0,19073 und Y=0,05002 mm/Pixel, eine primäre Position von X=167,79450 mm und Y=195,83625 mm und 3,81451 mm berechneten Durchmesser. Im aktiven Profil wurde der Top-Maßstab auf X=Y=0,05 mm/Pixel zurückgesetzt. Position und Durchmesser des Assistenten vor der Aktivierung des visuellen Homings abgleichen.
- [ ] Primäres und sekundäres Kalibrierfiduzial in OpenPnP erfassen. Kameramaßstab und tatsächliche Position der Bottom-Kamera ermitteln. In `Issues & Solutions` die Punkte unter `Fundamental` in der angebotenen Reihenfolge abarbeiten.
- [x] Erster kombinierter Homing-Lauf am 19.11.2026 erfolgreich. OpenPnP protokollierte die Fiduzialsuche und meldete um 11:26:05 Homed=true. Der STM32 meldete danach X=167,270 mm, Y=195,830 mm und Z=24,500 mm.
- [x] Zweiter visueller Homing-Lauf am 19.11.2026 um 11:28:28 erfolgreich. Das Protokoll bestätigte die Fiduzialsuche und Homed=true. Das Top-Livebild zeigte die primäre Marke mittig im Fadenkreuz.
- [x] Maßstab auf der sekundären Ebene am 19.11.2026 um 12:00:20 durch je 1 mm XY-Fahrt geprüft. X+ verschob die Marke 16,750 px nach links, Y+ 17,125 px nach unten. Das ergibt X=0,05970 und Y=0,05839 mm/Pixel. Die Orientierung passt. Der Kreis hatte in allen drei Bildern 20 px Durchmesser.
- [ ] Sekundäres Kalibrierfiduzial im Assistenten erfassen. Der Bediener hat ein zweites Datum-Board auf einer um 8,4 mm tieferen Ebene platziert und die Top-Kamera darüber zentriert. Die gespeicherte sekundäre XY-Position steht noch auf 0/0. Vor der Übernahme die gemeldete Bilddrehung prüfen, danach die Z-Höhe mit N1 erfassen.
- Im aktiven Profil sind beide Fiduziale auf X=167,270 mm und Y=195,830 mm abgeglichen und der primäre Durchmesser ist auf 1,0 mm korrigiert. `ResetToFiducialLocation` ist aktiv.

## 3. Düsen kalibrieren

- [x] Primäre Z-Referenz mit N1 und danach N2 auf derselben Marke eingemessen. Der Bediener hat an beiden Düsen je einen 14er-Tip montiert. Das aktive Profil kennt N14 als Tiptyp. Die montierten Tips sind im Windows-Profil als N14 links (N1) und N14 rechts (N2) eingetragen und jeweils als geladen markiert. Das sekundäre Datum-Board wartet auf die gedruckte Unterlage.

- OpenPnP protokollierte am 19.11.2026 die neu erfassten primären Düsenoffsets. N1: X=−24,600 mm, Y=−63,100 mm, Z=0,000 mm. N2: X=22,700 mm, Y=−63,500 mm, Z=−2,900 mm. In der um 12:54:01 gespeicherten Windows-Konfiguration verifiziert. Primäres Fiduzial: X=167,270 mm, Y=195,830 mm, Z=5,700 mm.
- [ ] Die tatsächlich montierten Düsentips N1 und N2 zuordnen. XY-Düsenoffsets, Kontakt- bzw. Platzierhöhe und sichere Z-Höhen ermitteln.
- [ ] Linkes Rack für N1 nach Kamera- und Düsenoffset-Kalibrierung einmessen. Tiptyp und Rackplatz notieren. Die vier Wechselwegpunkte mit N1 aufnehmen, beginnend mit `Last Location` vor dem Einschub gemäß [Opulo-Anleitung](https://docs.opulo.io/misc/auto-toolchanger/).
- [ ] Rechtes Rack für N2 nach dem linken Rack einmessen. Für jeden physischen Tip den Rackplatz und die kompatible Düse eintragen. Automatischen Wechsel erst nach der Prüfung der Wechselwege aktivieren.
- [ ] Düsenrundlauf mit der Bottom-Kamera kalibrieren. Drehung und Zentrierung an einem Testbauteil bestätigen.

## 4. Vakuum und Pick-Erkennung prüfen

- [ ] Pumpe, Ventil und Sensor je Düse aus OpenPnP testen. Den Sensorwert bei offener Düse, aufgenommenem Bauteil und nach dem Ablegen erfassen.
- [ ] Pick- und Verlustschwellen aus den gemessenen Werten festlegen. Erfolgreichen Pick, absichtlich leeren Pick und Freigabe getrennt prüfen.

## 5. Feeder und erster Bestückungstest

- [ ] Einen Feeder oder ein Testtray einrichten. Pickposition, Höhe und wiederholtes Bereitstellen eines gut sichtbaren Testbauteils prüfen.
- [ ] Testplatine und Bestückungsdaten einrichten. Platinenfiduziale erkennen lassen und die ersten Positionen ohne Bauteil anfahren.
- [ ] Mit niedriger Geschwindigkeit einzelne Bauteile aufnehmen, per Bottom Vision ausrichten und platzieren. Position, Drehung und Wiederholbarkeit prüfen. Erst nach erfolgreichem Test auf eigene Jobs übergehen.
- [ ] Funktionsfähige OpenPnP-Konfiguration sichern und die bestätigten Einstellungen im Repository dokumentieren.
