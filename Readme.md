# Hausaufgabe Docker 

### Antworten: 

#### Frage 1: 
Das "Works on my machine"-Problem

Wenn ich nur meinen Code-Ordner verschicke, muss jeder Kollege sicherstellen, dass sein System exakt die gleiche Umgebung hat (richtige Version von Nginx, gleiche Konfiguration usw.).
Mit dem Docker-Container ist das egal: das Image enthält alles, was nötig ist. Jeder kann es identisch starten – unabhängig vom Betriebssystem.

#### Frage 2:
Dockerfile als "Infrastructure as Code"

Das Dockerfile ist wie eine Bauanleitung: es beschreibt exakt, welche Basis (Nginx) genutzt wird, welche Dateien hineinkommen und welcher Port geöffnet wird.
Damit ist die Infrastruktur in Code beschrieben, versionierbar und reproduzierbar – genauso wie Software.

#### Frage 3: 
Trennung von Code und Abhängigkeiten

Bei einfachen HTML-Dateien ist das weniger wichtig, aber bei Node.js oder Python gilt:
Es ist besser, die Abhängigkeiten (npm install oder pip install) im Dockerfile zu installieren, statt node_modules oder virtuelle Umgebungen ins Image zu kopieren.
Warum?

- kleinere Images

- keine unnötigen lokalen Dateien

Abhängigkeiten werden sauber und reproduzierbar direkt im Container installiert.

#### Frage 4:

Ports

- EXPOSE 80 im Dockerfile: dokumentiert, dass der Container auf Port 80 lauscht (innen).

- docker run -p 8080:80: veröffentlicht Port 80 des Containers nach außen auf Port 8080 des Hosts.

       → Ich erreiche die Seite über http://localhost:8080, obwohl Nginx im Container auf Port 80 läuft.


