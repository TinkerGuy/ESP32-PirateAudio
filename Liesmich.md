# ESP32-PirateAudio
Benötigte Dateien zum Bau eines sehr kompakten Logitech Media Servers Players.  

## Warum diese Platine entwickelt wurde:

Ich habe vor einiger Zeit einen Player mit Raspberry Pi Zero und [Pirate Audio Amp HAT](https://shop.pimoroni.com/products/pirate-audio-3w-stereo-amp) gebaut.
Diese kompakte Elektronik fand in einem 3D-gedruckten Gehäuse Platz.
Als dann von Phillipe eine Squeezelite Firmware für den ESP32 Mikrocontroller von Esspressif entwickelt und veröffentlicht wurde, kam die Idee bei mir auf, meinen Player auf den ESP32 umzubauen. Da der Platz in dem Gehäuse maßgeschneidert für den Raspberry Pi Zero ist,
mußte eine Platine in genau diesem Format her. Das Pirate Audio Board wurde speziell für den Raspberry Pi entwickelt, daher mußte auch mein ESP32 Board den 40 Pin Raspberry Pi Header bekommen. Auf dem Pirate Board sind zwei MAX9857 I2S Amps (2x 3W), ein SPI Display mit ST7789 Controller sowie vier Taster verbaut.
Welche Verbindungen nötig sind, um alle Funktionen weiter nutzen zu können, habe ich [dieser Seite](https://de.pinout.xyz/pinout/pirate_audio_3w_amp#) entnommen.
Leider ist dort ein Fehler enthalten, dessen Suche mich einige Zeit gekostet hat. Und zwar wird das Display nicht über einen Spannungsregler von der 5V Spannungsversorgung gespeist,
sondern es bekommt seine 3,3V Spannung direkt vom Raspberry über Pin17. Und zwar nur über Pin17 und nicht über Pin1.
Mein erster Platinenentwurf hat diese Verbindung nicht gehabt. Ich habe zwar 3,3V an Pin1 verbunden, aber eben nicht an Pin17 weitergeführt.
Daher mußte ich diese Brücke nachträglich einlöten. Ich habe den Platinenentwurf inzwischen geändert, jetzt liegt sowohl an Pin1 als auch an Pin17 3,3V an.
Nun funktioniert das Pirate Board einhunderprozentig an meinem ESP32 Board.

## Was kann man damit machen:

Mit dieser Platine in Kombination mit Phillipes Squeezelite-ESP32 Firmware und dem Pirate Audio Amp HAT lässt sich ein sehr kompakter Player für den Logitech-Media-Server (LMS) bauen. Es werden zusätzlich nur noch ein 5V/2A Netzteil sowie ein Paar Lautsprecher benötigt.
Da das Pirate Audio Board die Standard I2S-Schnittstelle des Raspberry Pi verwendet, sollte es auch möglich sein, einen Hifiberry Miniamp zu verwenden, falls man kein Display und keine Tasten benötigt werden.
Einen "großen" Hifiberry Amp kann man nicht ohne Änderungen nutzen, weil dieser zusätzlich die I2C-Schnittstelle (Pin3 und Pin5) benötigt. Diese sind auf meinem Board nicht verdrahtet.

## Platine anfertigen und SMD-Bauteile auflöten lassen:

Das Board wurde mit EasyEDA entworfen. Die Projektdatei befindet sich im Ordner EasyEDA und darf für private Zwecke verwendet und verändert werden.
Mit den hier bereitgestellten Fertigungs-Dateien (Gerber Files) sowie den Dateien BOM.csv (Bauteileliste) und Pick&Place.csv (Bauteilpositionen) kann man bei einem PCB Dienstleister
diese Platine fertigen lassen [z.B. jlcpcb.com](https://jlcpcb.com).
Die Gerber Files müssen als .zip Datei an den Dienstleister übermittelt werden. Diese enthalten alle Daten, um die Platine fertigen zu können. Die meisten mir bekannten Anbieter haben eine Mindestbestellmenge von 5 Stück. Es empfielt sich, die SMD-Bauteile gleich von dem Dienstleister mit auflöten zu lassen. Dafür sind die beiden .csv Dateien nötig.
Die BOM.csv enthält die Bauteileliste. Beim Bestellvorgang ist darauf zu achten, daß die Bauteile durch vorrätige Standard-Parts des Dienstleisters ersetzt werden. Die elektrischen Werte sind dabei unbedingt beizubehalten. Das ESP32 WROVER Modul kann sowohl in der 8MB PSRAM als auch in der 16MB PSRAM Version verwendet werden. Auch die einfach ESP32-WROVER-B bezeichnete Version funktioniert.
Alle Module mit der Bezeichnung WROOM funktionieren nicht, da diese keinen externen PSRAM verbaut haben.
Hinweis: Die durchgesteckten Bauteile (Pin Header, 5V Power Buchse, etc.) lötet man, wenn die Möglichkeit besteht, besser selber. Diese Teile werden auch vom Dienstleister von Hand gelötet, was etwas teuerer ist. Diese Aufgabe bekommt man, ein paar Vorkenntnisse im Löten vorausgesetzt, ganz gut selber hin.

## Board-Layout:

Die folgende Tabelle zeigt die Verbindungen zwischen ESP32 und dem 40 Pin Header:

|   | ESP32-WROVER-B  | 40 Pin Header  |
| :------------: | :------------: | :------------: |
| 5V  | NC  | Pin 2  |
| 3,3V  |  Pin 2 (VCC) |  Pin 1, 17 |
| GND  | Pin 1, 15, 38  | Pin 6, 9, 14, 20, 25, 30, 34, 39  |
| I2S (Clock)  | Pin 16 (GPIO 13)  | Pin 12  |
| I2S (Data)  | Pin 11(GPIO 26)  | Pin 40  |
| I2S (WordSelect)  | Pin 12 (GPIO 27)  | Pin 35  |
| Amp Enable  | Pin 13 (GPIO 14) | Pin 22  |
| SPI (Clock)  | Pin 16 (GPIO 13)  | Pin 23 |
| SPI (Data)  | Pin 36 (GPIO 22)  | Pin 19  |
| SPI (DC)  | Pin 33 (GPIO 21)  | Pin 21  |
| SPI (CS)  | Pin 9 (GPIO 33)  | Pin 24  |
| Backlight  | Pin 8 (GPIO 32)  | >Pin 33  |
| Button A  | Pin 24 (GPIO 2)  | Pin 29  |
| Button B  | Pin 26 (GPIO 4)  | Pin 31  |
| Button X  | Pin 29 (GPIO 5)  | Pin 36  |
| Button Y (old)  | Pin 30 (GPIO 18)  | Pin 18  |
| Button Y (new)  | Pin 23 (GPIO 15)  | Pin 38  |
| 10 kOhm Pullup | Pin EN  | NC  |
| Jumper to GND  | Pin 25 (GPIO 0)  | NC  |

## Firmware flashen und in Betrieb nehmen:

Bevor man das Board konfigurieren und in Betrieb nehmen kann, muß die Firmware von Phillipe einmalig geflasht werden. Weitere Updates können OTA über die Weboberfläche durchgeführt werden.
Die aktuelle Firmware (momentan diese: squeezelite-esp32-master-cmake-I2S-4MFlash-16-1.698.zip) sowie eine Anleitung zum Flashen befindet sich [hier](https://github.com/sle118/squeezelite-esp32 "Link").
Mein ESP32 Board besitzt keinen USB-to-serial Chip, daher ist ein externes Modul zum Flashen notwendig. Ich kann [dieses Modul](https://de.aliexpress.com/item/32828640989.html?albpd=de32828640989&acnt=708-803-3821&aff_platform=aaf&albpg=1240648134658&netw=u&albcp=9599365821&sk=UneMJZVf&trgt=1240648134658&terminal_id=cb90a984c6704024b9d10f47dab3cb43&tmLog=new_Detail&needSmbHouyi=false&albbt=Google_7_shopping&src=google&crea=de32828640989&aff_fcid=0df8fe743af744ffa314b38fcb0b7f42-1625766660782-06276-UneMJZVf&gclid=EAIaIQobChMIrIf4uITU8QIVqhJ7Ch2_jA0NEAQYCyABEgLKgfD_BwE&albag=101872837187&aff_fsk=UneMJZVf&albch=shopping&albagn=888888&isSmbAutoCall=false&aff_trace_key=0df8fe743af744ffa314b38fcb0b7f42-1625766660782-06276-UneMJZVf&device=c&gclsrc=aw.ds) empfehlen.
Um das ESP32 Modul in den Programmiermodus zu versetzen, muss der Jumper geschlossen werden.

Achtung: Die 5V Spannungsversorgung darf zum Programmieren nicht angeschlossen sein. Das USB-to-Serial Modul muß unbedingt auf 3,3V eingestellt werden! Eine zu hohe Spannung zerstört das ESP32 Modul!

Die Verkabelung der seriellen Schnittstelle und die Einstellung des ESP32 Flash-Tools finden sich im Verzeichnis Flash.
Wenn die Firmware erfolgreich geflasht wurde, wird das Flash-Tool beendet, die Verkabelung der seriellen Schnittstelle getrennt und der Jumper wieder geöffnet.

Danach wird das Pirate Board aufgesteckt und die 5V Versorgungsspannung angeschlossen.

## Konfiguration:

Wie man das Board mit dem wLAN verbindet, kann man ebenfalls auf der GitHub-Seite von Phillipe bzw. [sle118](https://github.com/sle118/squeezelite-esp32) erfahren.
Damit das Board voll funktionsfähig ist, sind folgende Einstellungen vorzunehmen:

* dac_config </br>
`model=I2S,bck=13,ws=27,do=26`

* spi_config </br>
`data=21,clk=23,dc=22,host=1`

* display_config </br>
`SPI,width=240,height=240,cs=33,back=32,speed=32000000,driver=ST7789`

* set_GPIO </br>
`14=amp`

* actrls_config </br>
`button`

* button </br>
`[{"gpio":4, "type": "BUTTON_LOW", "pull":true, "normal":{"pressed": "ACTRLS_VOLDOWN"}}, {"gpio":2, "type": "BUTTON_LOW", "pull":true, "normal":{"pressed": "ACTRLS_VOLUP"}},{"gpio":5, "type": "BUTTON_LOW", "pull":true, "long_press": 500, "normal":{"pressed": "ACTRLS_PLAY"}, "longpress":{"pressed": "BCTRLS_RIGHT"}},{"gpio":18, "type": "BUTTON_LOW", "pull":true, "long_press":500, "normal":{"pressed": "BCTRLS_DOWN"}, "longpress":{"pressed": "BCTRLS_LEFT"}}]`

**Hinweis**: Bei neueren Pirate Boards liegt Taster Y auf einem anderen GPIO, hier ist “gpio“:18 durch “gpio“:15 zu ersetzten.

In den LMS-Einstellungen muss für die Funktion des Displays noch das ESP32 Plugin installiert werden und nach eigenen Vorlieben konfiguriert werden. Auch hierführ empfehle ich die Anleitung von Phillipe.

## Haftungsausschluß:

Ich empfehle die Umsetzung diese Projektes nur, wenn entprechende Vorkenntnisse vorhanden sind.
Unsachgemäßer Einsatz kann zu Schäden führen. Für eventuell entstehende Schäden an Mensch und Technik übernehme ich keine Haftung.
Das Board ist für eine Versorgungsspannung von 5V konzipiert. Falsch dimensionierte Netzteile können die Elektronik zerstören.
Das ESP32 Modul darf ausschließlich mit 3,3V Spannung versorgt werden.
Die Versorgungsspannung von 5V wird von einem Spannungsregler auf 3,3V heruntergeregelt. Beim Anschluss des USB-to-Seriell Moduls ist darauf zu achten, daß max. 3,3 V Spannung an das ESP32 Modul angelegt werden. Der Spannungsregler ist in diesem Fall außer Funktion.
Für den Inhalt der oben verlinkten Seiten übernehme ich keine Verantwortung.
