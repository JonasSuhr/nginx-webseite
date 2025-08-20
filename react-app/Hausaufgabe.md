# Reflexion – Multi-Stage Docker Build

## Multi-Stage Build

**1. Was ist der Hauptvorteil eines Multi-Stage Builds gegenüber einem einfachen Dockerfile?**  
Der Hauptvorteil ist, dass das finale Image **deutlich kleiner und schlanker** wird.  
Beim Multi-Stage Build wird der Code zuerst in einer „Builder“-Stage gebaut (mit allen nötigen Tools und Abhängigkeiten), aber nur das fertige Ergebnis (z. B. die `dist/` Dateien) in das finale Image übernommen.  
So landen keine unnötigen Dateien wie `node_modules`, Build-Tools oder Quellcode im endgültigen Container.  

---

**2. Warum ist der `node_modules` Ordner nicht im finalen Nginx Image enthalten, obwohl er für den Build notwendig war?**  
Der `node_modules` Ordner wird nur in der ersten Stage benötigt, um die Anwendung zu bauen.  
Durch den Multi-Stage Build wird im zweiten Schritt **nur das fertige Build-Ergebnis (statische Dateien)** übernommen – nicht aber die Entwicklungs-Abhängigkeiten.  
Das macht das finale Image kleiner, sicherer und schneller.  

---

**3. Wie wird das Docker-Layer-Caching genutzt, insbesondere beim `COPY package*.json` Schritt?**  
- Zuerst werden nur `package.json` und `package-lock.json` kopiert und `npm ci` ausgeführt.  
- Docker speichert diesen Schritt als **Layer** im Cache.  
- Solange sich die Abhängigkeiten nicht ändern, muss Docker bei einem neuen Build **nicht alle Dependencies neu installieren**, sondern kann den Cache verwenden.  
- Das spart sehr viel Zeit beim Bauen.  

---

## Rolle des Webservers und der Anwendung

**4. Was wird im finalen Image gespeichert?**  
Im finalen Image landen **nur die gebauten statischen Dateien** (z. B. HTML, CSS, JS im `dist/` Ordner).  
Unterschied zur ersten Stage:  
- Erste Stage enthält Quellcode, Build-Tools und Dependencies.  
- Finale Stage enthält nur das Ergebnis, das der Browser direkt laden kann.  

---

**5. Welche Rolle spielt Nginx in diesem Kontext?**  
Nginx ist der **Webserver**, der die statischen Dateien (HTML, CSS, JS) an den Browser ausliefert.  
Ohne Nginx gäbe es keinen Server, der auf Anfragen wie `http://localhost:8080` antwortet.  
Bei Single Page Applications (SPAs) wie React ist oft eine zusätzliche Nginx-Konfiguration nötig, damit **alle Routen auf `index.html` zeigen** (z. B. `/about`, `/contact`), weil sonst 404-Fehler entstehen könnten.  

---

**6. Warum wird der Entwicklungsmodus (`npm run dev`) nicht für den Produktivbetrieb genutzt?**  
- `npm run dev` startet einen **Entwicklungsserver**, der langsamer ist und viele Debug-Funktionen hat.  
- Für den Produktivbetrieb braucht man das optimierte Build (`npm run build`), das schneller lädt, kompakt ist und von Nginx effizient ausgeliefert wird.  

---

## Containerisierung und Betrieb

**7. Hauptvorteile der Containerisierung mit Multi-Stage Build im Vergleich zu lokalem Build:**  
- **Portabilität:** Der Container läuft überall gleich, egal ob Windows, Linux oder Mac.  
- **Reproduzierbarkeit:** Jeder Build liefert exakt dasselbe Ergebnis – keine Unterschiede durch lokale Umgebungen.  
- **Isolation:** Der Container ist vom Host-System getrennt und beeinflusst keine lokalen Installationen.  

---

**8. Funktion des HEALTHCHECK und Bedeutung für Orchestrierung:**  
- Der `HEALTHCHECK` prüft regelmäßig, ob der Container korrekt läuft (z. B. ob Nginx erreichbar ist).  
- Wenn der Check fehlschlägt, wird der Container von Tools wie Docker Swarm oder Kubernetes automatisch neugestartet oder ersetzt.  
- Das erhöht die **Zuverlässigkeit und Stabilität** im Betrieb.  

---

## Vergleich .gitignore vs .dockerignore

**9. Unterschied zwischen `.gitignore` und `.dockerignore`:**  
- **`.gitignore`**: Steuert, welche Dateien **nicht ins Git-Repository** aufgenommen werden (z. B. `node_modules/`, `.env`).  
- **`.dockerignore`**: Steuert, welche Dateien **nicht ins Docker-Build** kopiert werden (z. B. `node_modules/`, `.git`).  
- Kurz gesagt: `.gitignore` betrifft den **Git-Verlauf**, `.dockerignore` betrifft den **Docker Build-Kontext**.  

---
