# 🏠 Home-Automation-IoT

Nach meinem letzten Umzug wollte ich Smart-Home-Geräte in meiner Wohnung installieren. Da ich aus Privacy-Bedenken nicht auf kommerzielle Systeme wie Google Home Kit setzen wollte, habe ich mich in der Recherchephase für eine lokale Home Assistant¹ Instanz entschieden. Dieses System ist vollständig Open-Source und bietet von Anfang an Protokolle für eine native Hardware-Integration vieler Hersteller von Smart-Home-Geräten. Durch die Tinkerer-Philosophie des Projekts lässt sich Home Assistant zudem mit eigenen oder Community-Scripts vollständig nach den eigenen Vorstellungen erweitern und umbauen.

---

## 🌐 Infrastruktur & Setup

Um Home Assistant lokal aufzusetzen, brauchte ich passende Hardware. Ich habe über einen Einplatinencomputer nachgedacht, mich aber letztendlich für eine virtuelle Maschine auf einem Home-Server entschieden. Dafür habe ich mich nach einem günstigen, wiederaufgearbeiteten Thin-Client umgeschaut, den ich im Anschluss mit einem Hypervisor aufgesetzt habe. Dort hoste ich die VM mit dem eigenständigen Open-Source-Betriebssystem Home Assistant OS². So kann ich Home Assistant in vollem Umfang nutzen und zusätzlich noch weitere Projekte auf derselben Hardware angehen.

Meine ersten Smart-Home-Geräte waren WLAN-gesteuerte RGBW-Glühbirnen. Mittlerweile nutze ich aber fast ausschließlich ein lokales Zigbee-Mesh für die Integration in mein Smart-Home. Zigbee ist ein drahtloser Funkstandard, speziell für die Heimautomatisierung. Ein spezieller, an die VM durchgereichter USB-Dongle kann dieses Funksignal senden und empfangen. Die Home Assistant Integration **Zigbee2MQTT**³ fungiert als Übersetzer in der Kommunikation zwischen Home Assistant und dem Mesh-Netzwerk. 

Zigbee ist energieeffizient und stabilisiert sich mit jedem Gerät, das ins Netzwerk integriert wird, weiter. Vor allem aber ist Zigbee vollständig lokal und nicht mit dem Internet verbunden. Anders als bei klassischen Internet-of-Things-Devices (IoT) hat ein Zigbee-Gerät nicht einmal die **Möglichkeit**, außerhalb des Mesh-Netzwerks zu kommunizieren. Das schützt meine Privatsphäre und minimiert das Risiko von Angriffen auf mein Heimnetz.

---

## 📦 DIY-Projekt 1: Automatisierte Filament-Dryboxes (Abgeschlossen)

Filament für 3D-Drucker wird spröde, wenn es über einen längeren Zeitraum einer zu hohen Luftfeuchtigkeit ausgesetzt ist. Da ich mit meinem 3D-Drucker viel Dekoration produziere, habe ich viele farbige Filamentspulen, die ich nur selten benutze. Um die Lebensdauer des Materials zu verlängern, benötigte ich eine Lagerung, in der ich die innere Luftfeuchtigkeit überwachen und sicherstellen kann, dass sie einen konstant niedrigen Wert behält.

Fertige kommerzielle Lösungen existieren zwar, sind allerdings teuer und haben meistens keine integrierte Option zur Feuchtigkeitsüberwachung. Bei meiner weiteren Recherche bin ich auf eine Sammlung von 3D-Modellen⁴ gestoßen, mit denen sich die 22-Liter-Version der Ikea Samla Kiste zu einer Drybox für vier Filamentspulen umbauen lässt. Also habe ich ein paar der Kisten bestellt, die Teile gedruckt und alles zusammengebaut. Zusätzlich habe ich noch eine Rolle Dichtungsband besorgt, um den Deckel bestmöglich abzudichten. Zur Überwachung der Luftfeuchtigkeit kamen günstige Zigbee-Hygrometer von Tuya zum Einsatz.

### Logik

Home Assistant stellt ein integriertes GUI für das Erstellen von Automationen mit einer „Wenn..., Und wenn..., Dann...“-Logik zur Verfügung. Dafür definiert man beliebige Entitäten als Auslöser, Bedingung (optional) und Aktion.

* **Auslöser:** Das Hygrometer in der Drybox meldet über das Zigbee-Mesh konstant die aktuell gemessene Luftfeuchtigkeit an meine Home Assistant Instanz. Liegt dieser Wert über einen Zeitraum von 15 Minuten ununterbrochen bei mehr als 35 %, wird die Automation ausgelöst.
* **Aktion:** Für den Versand von Benachrichtigungen nutze ich den Dienst **CallMeBot**⁵. Wird der Schwellenwert überschritten, sendet die API eine WhatsApp-Nachricht an meine Telefonnummer.

**Die fertige Automationslogik:**
> ⚡ **Wenn** die Luftfeuchtigkeit im Inneren der Drybox über einen Zeitraum von 15 Minuten höher ist als 35 %,
> 💬 **Dann** weist CallMeBot mich per WhatsApp darauf hin, neues Silica-Gel in den Behälter der Drybox zu füllen.

Dieselbe Logik verwende ich auch, um rechtzeitig über einen niedrigen Batteriestand informiert zu werden:
> ⚡ **Wenn** die Batterie des Hygrometers auf 5 % fällt,
> 💬 **Dann** weist CallMeBot mich per WhatsApp darauf hin, die Batterie auszuwechseln.

Die Automation habe ich für alle meine Dryboxen dupliziert. Da die Kisten nummeriert sind, enthält die Nachricht von CallMeBot direkt die Nummer der betroffenen Box. So stelle ich sicher, dass meine Filamentspulen immer unter idealen Umständen gelagert sind und kein sprödes Filament im Müll landet.

---

## 🪜 DIY-Projekt 2: Sequenzielle Treppenbeleuchtung (Work in Progress)

Ich habe die schlechte Angewohnheit, nachts das Licht nicht anzumachen, wenn ich die Treppe hoch- oder runtergehe. Bisher ist dabei noch nie etwas passiert, aber mir ist klar, dass die Sturzgefahr im Dunkeln deutlich erhöht ist. Auf vollkommen vermeidbare Verletzungen kann ich gerne verzichten, deshalb versuche ich schon *lange*, mir anzugewöhnen, Licht zu schaffen, bevor ich Treppen steige. Trotzdem ertappe ich mich weiterhin regelmäßig auf halbem Weg dabei, dass ich *schon wieder* blind das Stockwerk wechsle.

Neulich kam mir eine Idee, mit der ich dieses Problem aus der Welt schaffen und der Wohnung gleichzeitig ein stilvolles Upgrade verpassen kann: Lichtstreifen unter den Stufen, die automatisch reagieren, wenn die Treppe nachts betreten wird. Nach Sonnenuntergang soll die Beleuchtung mit einem laufrichtungsabhängigen Sequenzeffekt angehen.

### Hardware

Insgesamt sind 12 Treppenstufen zu beleuchten. Dafür habe ich folgende Komponenten besorgt:
* **LED-Strips:** 10 Meter adressierbare (IC) COB Light Strips mit RGBW-Farbspektrum und 24V Spannung.
* **Profile:** Passende Aluminiumprofile für die Hitzeableitung.
* **Stromversorgung & Steuerung:** Ein hochwertiges 150W-Netzteil sowie einen Zigbee-Controller, um die LEDs einzeln zu adressieren.
* **Sensorik:** Zwei Vibrationssensoren, die ebenfalls in das Mesh-Netzwerk eingepflegt werden.

### Plan und Logik

Die Lichtstreifen werden in 80-cm-Stücke geteilt und in den Profilen zentriert an den Unterseiten der Stufen befestigt. Mit Litzenkabeln und WAGO-Klemmen verbinde ich die einzelnen Streifen zu einer Daisy-Chain und schließe den Controller sowie das Netzteil an. An der Unterseite der obersten und untersten Stufe wird je ein Vibrationssensor platziert.

Die geplante Logik für den Sequenzeffekt startet mit der Vibrationserkennung. Der zuerst aktivierte Sensor bestimmt den Startpunkt, von dem aus der Controller die IC-Strips nacheinander in Gehrichtung ansteuert.

---

## 🪞 DIY-Projekt 3: Smart Mirror (In Planung)

Ich möchte einen smarten Spiegel im Schlafzimmer aufhängen, der als zentrales Info-Display dient. Hinter einem Einwegspiegel zeigt ein Monitor dafür verschiedene UI-Elemente und Widgets an, die mit MagicMirror⁶ visualisiert werden.

### UI-Konzept

Damit man die Zeit immer im Blick hat, soll die aktuelle Uhrzeit statisch auf dem Display platziert sein. Daneben zeigt der Spiegel weitere nützliche Widgets an – etwa die Wettervorhersage (morgens für den aktuellen Tag, abends für den Folgetag), Terminerinnerungen oder ein selbst gebautes Widget, das Informationen von Uptime Kuma⁷ abruft.

Um die Informationsdichte zu maximieren, ohne dass die Oberfläche reizüberflutend wirkt, plane ich eine animierte Rotation der nicht-statischen Widgets.

### Überlegungen zur Hardware

Smart Mirrors, die ich bisher in den sozialen Medien gesehen habe, waren für meinen Geschmack immer sehr klobig. Um meinen Spiegel so dünn wie möglich zu halten, überlege ich, das Monitorpanel auszubauen und ohne Gehäuse direkt auf der Rückseite des Einwegspiegels zu verkleben. Anstelle eines platzintensiven, vollwertigen Einplatinencomputers möchte ich nur einen Raspberry Pi Zero 2W verbauen. In der Theorie wäre das fertige Produkt so kaum dicker als ein normaler Rahmen für einen einfachen Wandspiegel.

Da dem Raspberry Pi Zero 2W allerdings die Leistung fehlt, um MagicMirror zu betreiben, Informationen abzurufen, Widgets zu animieren und gleichzeitig Bildsignale auszugeben, werde ich die Rechenleistung auf den Server auslagern. Dieser sendet die Bildausgabe lokal an den Spiegel, sodass der Pi Zero das Signal nur noch abfangen und an den Monitor weiterleiten muss.

---

## 📚 Referenzen

| Referenz | Tool / Projekt | Link |
| :--- | :--- | :--- |
| 1 | Home Assistant | [Homepage](https://www.home-assistant.io/) |
| 2 | Home Assistant OS | [Repository](https://github.com/home-assistant/operating-system) |
| 3 | Zigbee2MQTT | [Homepage](https://www.zigbee2mqtt.io/) |
| 4 | Ikea Samla - Drybox Sammlung | [Maker World](https://makerworld.com/de/models/195190-ikea-samla-22l-filament-storage-box-v2024#profileId-215691) |
| 5 | CallMeBot | [Homepage](https://www.callmebot.com/) |
| 6 | MagicMirror | [Homepage](https://magicmirror.builders/) |
| 7 | Uptime Kuma | [Homepage](https://uptimekuma.org/) |
