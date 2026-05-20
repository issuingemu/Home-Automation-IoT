# Home-Automation-IoT

Nach meinem letzten Umzug wollte ich Smart-Home-Geräte in meiner Wohnung installieren. Da ich wegen Privacy-Bedenken nicht auf kommerzielle Systeme wie Google Home Kit setzen wollte, habe ich mich in der Recherchephase für eine lokale Home Assistant¹ Instanz entschieden. Dieses System ist vollständig Open-Source und bietet von Anfang an Protokolle für eine native Hardware-Integration vieler Hersteller von Smart-Home Geräten. Und durch die Tinkerer-Philosophie des Projekts lässt sich Home Assistant mit eigenen oder Community-Scripts vollständig nach den eigenen Vorstellungen erweitern und umbauen. 

---

## Infrastruktur & Setup

Um Home Assistant lokal aufzusetzen, brauchte ich passende Hardware. Ich habe über einen Einplatinencomputer nachgedacht, mich aber letztendlich für eine virtuelle Maschine auf einem Home-Server entschieden. Dafür habe ich mich nach einem günstigen wiederaufgearbeiteten Thin-Client umgeschaut, den ich im Anschluss mit einem Hypervisor aufgesetzt habe. Dort hoste ich die VM mit dem eigenständigen, Open-Source Betriebssystem Home Assistant OS². So kann ich Home Assistant in vollem Umfang nutzen und zusätzlich noch weitere Projekte mit der selben Hardware angehen.

Meine ersten Smart-Home-Geräte wären WLAN gesteuerte RGBW-Glühbirnen. Mittlerweile nutze ich aber fast ausschließlich ein lokales Zigbee-Mesh für die Integration in mein Smart-Home. Zigbee ist ein drahtloser Funkstandard, speziell für die Heimautomatisierung. Ein spezieller, an die VM durchgereichter USB-Dongle kann dieses Funksignal senden und empfangen. Die Home Assistant Integration **Zigbee2MQTT**³ fungiert als Übersetzer in der Kommunikation zwischen Home Assistant und dem Mesh-Netzwerk. Zigbee ist energieeffizient und stabilisiert sich mit jedem Gerät, das ins Netzwerk integriert wird, weiter. Aber vor allem ist Zigbee vollständig lokal und nicht mit dem Internet verbunden. Anders als bei Internet-of-Things (IoT) Devices, hat ein Zigbee-Gerät nicht einmal die **Möglichkeit**, außerhalb des Mesh-Netzwerks zu kommunizieren. Das schützt meine Privatsphäre und minimiert das Risiko von Angriffen auf mein Heimnetz.

## DIY-Projekt 1: Automatisierte Filament-Dryboxes (Abgeschlossen)

Filament für 3D-Drucker wird spröde, wenn es über einen längeren Zeitraum einer zu hohen Luftfeuchtigkeit ausgesetzt ist. Da ich mit meinem 3D-Drucker viel Dekoration produziere, habe ich aber viele farbige Filamentspulen, die ich nur selten benutze. Um die Lebensdauer des Materials zu verlängern, habe ich Boxen gebraucht, in denen ich die Luftfeuchtigkeit überwachen und sicherstellen kann, dass die innere Luftfeuchtigkeit auf einem konstant niedrigen Wert bleibt. 

### Plan

---

# 📚 Referenzen

| Referenz | Tool / Projekt | Link |
| :--- | :--- | :--- |
| 1 | Home Assistant | [Homepage](https://www.home-assistant.io/) |
| 2 | Home Assistant OS | [Repository](https://github.com/home-assistant/operating-system) |
| 3 | xxx | [xxx]() |
| 4 | xxx | [xxx]() |
| 5 | xxx | [xxx]() |
| 6 | xxx | [xxx]() |
| 7 | xxx | [xxx]() |
| 8 | xxx | [xxx]() |
| 9 | xxx | [xxx]() |
| 10 | xxx | [xxx]() |
| 11 | xxx | [xxx]() |
| 12 | xxx | [xxx]() |
| 13 | xxx | [xxx]() |
| 14 | xxx | [xxx]() |
| 15 | xxx | [xxx]() |
| 16 | xxx | [xxx]() |
| 17 | xxx | [xxx]() |
| 18 | xxx | [xxx]() |
