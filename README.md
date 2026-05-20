# Home-Automation-IoT

Nach meinem letzten Umzug wollte ich Smart-Home-Geräte in meiner Wohnung installieren. Da ich wegen Privacy-Bedenken nicht auf kommerzielle Systeme wie Google Home Kit setzen wollte, habe ich mich in der Recherchephase für eine lokale Home Assistant¹ Instanz entschieden. Dieses System ist vollständig Open-Source und bietet von Anfang an Protokolle für eine native Hardware-Integration vieler Hersteller von Smart-Home Geräten. Und durch die Tinkerer-Philosophie des Projekts lässt sich Home Assistant mit eigenen oder Community-Scripts vollständig nach den eigenen Vorstellungen erweitern und umbauen. 

---

## Infrastruktur & Setup

Um Home Assistant lokal aufzusetzen, brauchte ich passende Hardware. Ich habe über einen Einplatinencomputer nachgedacht, mich aber letztendlich für eine virtuelle Maschine auf einem Home-Server entschieden. Dafür habe ich mich nach einem günstigen wiederaufgearbeiteten Thin-Client umgeschaut, den ich im Anschluss mit einem Hypervisor aufgesetzt habe. Dort hoste ich die VM mit dem eigenständigen, Open-Source Betriebssystem Home Assistant OS². So kann ich Home Assistant in vollem Umfang nutzen und zusätzlich noch weitere Projekte mit der selben Hardware angehen.

Meine ersten Smart-Home-Geräte wären WLAN gesteuerte RGBW-Glühbirnen. Mittlerweile nutze ich aber fast ausschließlich ein lokales Zigbee-Mesh für die Integration in mein Smart-Home. Zigbee ist ein drahtloser Funkstandard, speziell für die Heimautomatisierung. Ein spezieller, an die VM durchgereichter USB-Dongle kann dieses Funksignal senden und empfangen. Die Home Assistant Integration **Zigbee2MQTT**³ fungiert als Übersetzer in der Kommunikation zwischen Home Assistant und dem Mesh-Netzwerk. Zigbee ist energieeffizient und stabilisiert sich mit jedem Gerät, das ins Netzwerk integriert wird, weiter. Aber vor allem ist Zigbee vollständig lokal und nicht mit dem Internet verbunden. Anders als bei Internet-of-Things (IoT) Devices, hat ein Zigbee-Gerät nicht einmal die **Möglichkeit**, außerhalb des Mesh-Netzwerks zu kommunizieren. Das schützt meine Privatsphäre und minimiert das Risiko von Angriffen auf mein Heimnetz.

## DIY-Projekt 1: Automatisierte Filament-Dryboxes (Abgeschlossen)

Filament für 3D-Drucker wird spröde, wenn es über einen längeren Zeitraum einer zu hohen Luftfeuchtigkeit ausgesetzt ist. Da ich mit meinem 3D-Drucker viel Dekoration produziere, habe ich aber viele farbige Filamentspulen, die ich nur selten benutze. Um die Lebensdauer des Materials zu verlängern, habe ich Stauraum gebraucht, in dem ich die innere Luftfeuchtigkeit überwachen und sicherstellen kann, dass sie einen konstant niedrigen Wert behält.

Fertige komerzielle Lösungen existieren zwar, sind allerdings teuer und haben meistens keine integrierte Option, die Luftfeuchtigkeit zu überwachen. Bei meiner weiteren Recherche bin ich dann auf eine Sammlung an 3D-Modellen⁴ gestoßen, mit denen sich die 22 Liter Version der Ikea Samla Kiste zu einer Drybox für vier Filamentspulen umbauen lässt. Also habe ich ein paar der Kisten bestellt, die Teile gedruckt und später zusammengebaut. Ich habe außerdem noch eine Rolle Dichtungsband besorgt, um den Deckel bestmöglich abzudichten. Zur Überwachung der Luftfeuchtigkeit, habe ich mir günstige Zigbee-Hygrometer von Tuya bestellt.

### Logik

Home Assistant stellt ein integriertes GUI für das Erstellen von Automationen mit "Wenn..., Und wenn..., Dann..." Logik zur Verfügung. Dafür definiert man beliebige Entitäten als Auslöser, Bedingung (optional) und Aktion.

Das Hygrometer in der Drybox ist als Auslöser definiert. Über mein Zigbee-Mesh meldet es konstant die aktuell gemessene Luftfeuchtigkeit an meine Home Assistant Instanz. Liegt dieser Wert über einen Zeitraum von 15 Minuten ununterbrochen bei mehr als 35%, wird die Automation ausgelöst.

Weiter habe ich eine Home Assistant Integration für den Dienst **CallMeBot**⁵ installiert. Dieser stellt APIs zum Versenden von Nachrichten zur Verfügung. Die Integration nutze ich als Aktionsentität. Wird die Automation ausgelöst, sendet CallMeBot eine WhatsApp Nachricht an meine Telefonnummer.

Die fertige Automationslogik sieht also so aus:
- **Wenn** die Luftfeuchtigkeit im inneren der Drybox über einen Zeitraum von 15 Minuten höher ist als 35%,
- **Dann** weist CallMeBot mich per WhatsApp darauf hin, neues Silica-Gel in den Behälter der Drybox zu füllen.

Die selbe Logik verwende ich auch, um darauf aufmerksam gemacht zu werden, wenn die Batterie des Hygrometers gewechselt werden muss:
- **Wenn** die Batterie des Hygrometers auf 5% fällt,
- **Dann** weist CallMeBot mich per WhatsApp darauf hin, die Batterie des Hygrometers auszuwechseln.

Die Automation habe ich für alle meine Dryboxen dupliziert. Die Kisten sind nummeriert und die Nachricht, die CallMeBot verschickt, enthält die Nummer der betroffenen Drybox. 

So stelle ich sicher, dass meine Filamentspulen immer unter idealen Umständen gelagert sind und kein sprödes Filament im Müll landet.

## DIY-Projekt 2: Sequenzielle Treppenbeleuchtung (Work in Progress)

Ich habe die schlechte Angewohnheit, nachts das Licht nicht anzumachen, wenn ich die Treppe hoch oder runter gehe. Bisher ist dabei noch nie etwas passiert, aber mir ist klar, dass die Gefahr zu stürzen im Dunkeln deutlich erhöht ist. Auf eine vollkommen vermeidbare Querschnittslähmung kann ich gerne verzichten, aber obwohl ich schon *lange* versuche, mir anzugewöhnen, Licht zu schaffen, bevor ich Treppen steige, ertappe ich mich regelmäßig auf halbem Weg dabei, dass ich *schon wieder* blind das Stockwerk wechsle. 

Neulich kam mir dann eine Idee, mit der ich dieses Problem aus der Welt schaffen kann und der Wohnung gleichzeitig ein stilvolles Upgrade gebe - Lichtstreifen unter den Stufen, die automatisch reagieren, wenn die Treppe nachts betreten wird. Nach Sonnenuntergang soll die Beleuchtung dazu mit einem laufrichtungsabhängigen sequenzeffekt angehen.

### Hardware

Insgesamt sind 12 Treppenstufen zu beleuchten. Dafür habe ich 10 Meter adressierbare (IC) COB Light Strips mit RGBW-Farbspektrum und und 24V Spannung, sowie passende Aluminiumprofile für die Hitzeableitung besorgt. Außerdem habe ich ein hochwertiges 150W Netzteil, einen Zigbee-Controller, um die LEDs zu adressieren und zwei Vibrationssensoren, die ebenfalls ins Mesh-Netzwerk eingepflegt werden.

### Plan und Logik

Die Lichtstreifen werden in 80cm-Stücke geteilt und in den Profilen zentriert an den Unterseiten der Stufen befestigt. Mit Litzenkabeln und WAGO-Klemmen verbinde ich die einzelnen Streifen zu einer Daisy-Chain und schließe den Controller und das Netzteil an. Auf der Unterseite der obersten und untersten Stufe platziere ich je einen Vibrationssensor. 

Die geplante Logik für den Sequenzeffekt startet mit der Vibrationserkennung, entweder an der oberen oder unteren Stufe. Der zuerst aktivierte Sensor bestimmt den Startpunkt, von dem aus der Controller die IC-Strips nacheinander in Gehrichtung ansteuert. 

## DIY-Projekt 3: Smart Mirror (In Planung)

Ich möchte einen Smarten Spiegel in das Schlafzimmer hängen, der ein zentrales Info-Display darstellt. Hinter einem Einwegspiegel zeigt ein Monitor dafür verschiedene UI-Elemente und Widgets an, die mit MagicMirror⁶ visualisiert werden.

### UI-Konzept

Damit man immer die Zeit im Blick hat, soll die aktuelle Uhrzeit statisch auf dem Display platziert sein. Daneben soll der Spiegel aber auch weitere nützliche UI-Elemente und Widgets anzeigen - Etwa die Wettervorhersage, morgends die heutige, abends für den Folgetag, Erinnerungen an Termine oder ein selbst gebautes Widget, das Informationen von Uptime Kuma⁷ pullt.

Um die Informationsdichte zu maximieren, ohne dass die Oberfläche reizüberflutend wirkt, habe ich eine animierte Rotation nicht statischer Widgets im Kopf.

### Überlegungen zur Hardware

Smart Mirrors, die ich bisher in den sozialen Medien gesehen habe, waren für meinen Geschmack immer sehr dick. Um meinen Spiegel so dünn wie möglich zu halten, überlege ich, das Monitorpanel auszubauen und ohne Gehäuse auf der Hinterseite des Einwegspiegels zu verkleben. Anstelle eines platzintensiven vollwertigen Einplatinencomputers, würde ich nur einen Raspberry Pi Zero 2W verbauen. In der Theorie wäre das fertige Produkt so nicht *viel* dicker, als ein normaler Rahmen für einen einfachen Wandspiegel.

Da einem Raspberry Pi Zero 2W aber definitiv die Leistung fehlt, um MagicMirror zu betreiben, Informationen abzurufen, Widgets zu animieren und gleichzeitig Bildsignale auszugeben, würde ich die Rechenleistung auf den Server outsourcen, der die Bildausgabe lokal an den Spiegel sendet. So müsste der Pi Zero nur noch das Signal abfangen und an den Monitor ausgeben.

---

# 📚 Referenzen

| Referenz | Tool / Projekt | Link |
| :--- | :--- | :--- |
| 1 | Home Assistant | [Homepage](https://www.home-assistant.io/) |
| 2 | Home Assistant OS | [Repository](https://github.com/home-assistant/operating-system) |
| 3 | Zigbee2MQTT | [Homepage](https://www.zigbee2mqtt.io/) |
| 4 | Ikea Samla - Drybox Sammlung | [Maker World](https://makerworld.com/de/models/195190-ikea-samla-22l-filament-storage-box-v2024#profileId-215691) |
| 5 | CallMeBot | [Homepage](https://www.callmebot.com/) |
| 6 | MagicMirror | [Homepage](https://magicmirror.builders/) |
| 7 | Uptime Kuma | [Homepage](https://uptimekuma.org/) |
