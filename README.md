# Readwise Decider

**Readwise Decider** ist eine fokussierte Web-App zum schnellen Sichten und Klassifizieren von Readwise-Highlights.

Für jedes noch offene Highlight wird genau eine Entscheidung getroffen:

| Entscheidung | Readwise-Tag | Bedeutung |
| --- | --- | --- |
| Make Anki | `make-anki` | Später zu einer oder mehreren Anki-Karten verarbeiten |
| Make Atomic | `make-atomic` | Später zu einer oder mehreren Atomic Notes verdichten |
| Reflect | `reflect` | Für einen Reflexions- oder Schreibworkflow vormerken |
| Nothing | `nothing` | Bewusst geprüft und vorerst nicht weiterverarbeiten |

Die App erstellt selbst keine Karten, Notizen oder KI-Inhalte. Sie dient ausschließlich als schnelle Human-in-the-Loop-Entscheidungsoberfläche und speichert die gewählte Klassifikation direkt als Tag am Readwise-Highlight.

## App öffnen

[Readwise Decider auf GitHub Pages](https://kerimm93.github.io/readwise-decide/)

## Funktionen

- Vier klar definierte Workflow-Entscheidungen
- Bedienung per Maus, Touch oder Tastatur
- Standardmäßig 50 Highlights pro abschließbarer Runde
- Rundengröße wählbar: 25, 50, 100 oder eigener Wert bis 1.000
- Laufende Runden bleiben beim erneuten Öffnen erhalten
- Weitere Highlights werden bei Bedarf paginiert nachgeladen
- Bereits entschiedene Highlights werden aus neuen Runden ausgeschlossen
- Sofortiger Wechsel zur nächsten Karte, während Readwise im Hintergrund speichert
- Rückkehr des Highlights bei einem fehlgeschlagenen API-Request
- Markdown-Darstellung in Highlighttext und Readwise-Notiz
- Responsive Warm-Paper-Oberfläche mit mobiler Sticky-Aktionsleiste
- Als Progressive Web App auf Desktop und Mobilgeräten installierbar
- App-Shell nach einem erfolgreichen Online-Aufruf offline startbar

## Bedienung

1. App öffnen.
2. Über das Zahnrad den persönlichen Readwise-API-Token eintragen.
3. Highlights laden und eine Runde starten.
4. Jedes Highlight mit einem Button oder Tastenkürzel klassifizieren.
5. Nach Abschluss der Runde bewusst aufhören oder die nächste Runde starten.

### Tastaturkürzel

| Taste | Aktion |
| --- | --- |
| `1` | Make Anki |
| `2` | Make Atomic |
| `3` | Reflect |
| `0` | Nothing |
| `→` | Aktuelles Highlight überspringen |

`0` bedeutet nicht „ohne Tag“: Die App vergibt den Tag `nothing`. Dadurch gilt das Highlight als geprüft und wird in späteren Runden nicht erneut vorgelegt.

## Entscheidungslogik

Ein Highlight gilt als entschieden, sobald es mindestens einen dieser Workflow-Tags besitzt:

```text
make-anki
make-atomic
reflect
nothing
```

Andere bereits vorhandene Readwise-Tags bleiben erhalten. Die vier Tags beschreiben nur den nächsten Verarbeitungsschritt und sind keine thematischen Schlagwörter.

## Installation als App

### Chrome oder Edge auf dem Desktop

1. Die GitHub-Pages-Version öffnen.
2. In der Adressleiste auf das Installationssymbol klicken oder in den Einstellungen der App „App installieren“ wählen.
3. Installation bestätigen.

### Android

1. Die App in Chrome öffnen.
2. „App installieren“ beziehungsweise „Zum Startbildschirm hinzufügen“ wählen.

### iPhone oder iPad

1. Die App in Safari öffnen.
2. **Teilen → Zum Home-Bildschirm** wählen.
3. Mit „Hinzufügen“ bestätigen.

Im installierten Zustand startet Readwise Decider in einem eigenen App-Fenster ohne normale Browserleiste.

## Offline-Verhalten

Der Service Worker speichert die lokale App-Oberfläche und ihre statischen Dateien. Nach einem erfolgreichen Online-Aufruf kann die App daher auch ohne Verbindung geöffnet werden.

Das Abrufen von Highlights und das Speichern neuer Entscheidungen benötigen weiterhin eine Internetverbindung zu Readwise. Readwise-API-Anfragen werden nicht durch den Service Worker gecacht oder als erfolgreich simuliert.

## Datenschutz und lokale Daten

- Der Readwise-Token wird nur im lokalen Speicher des verwendeten Browserprofils abgelegt.
- Der Token wird direkt an Readwise gesendet und nicht an einen eigenen Server übertragen.
- Auch die laufende Runde, Queue, Pagination und Einstellungen werden lokal im Browser gespeichert.
- Die App besitzt kein eigenes Backend und keine Benutzerkonten.
- GitHub Pages liefert lediglich die statischen App-Dateien aus.
- Workflow-Tags werden über Readwise geräteübergreifend sichtbar; der lokale Stand einer angefangenen Runde wird dagegen nicht zwischen Browsern synchronisiert.

Wer Zugriff auf das verwendete Browserprofil hat, kann grundsätzlich auch auf dort gespeicherte App-Daten zugreifen. Auf gemeinsam genutzten Geräten sollte der Token daher nicht dauerhaft gespeichert werden.

## Technische Struktur

Die App ist eine statische Vanilla-JavaScript-Anwendung ohne Framework, Build-System oder Laufzeitabhängigkeiten.

| Pfad | Aufgabe |
| --- | --- |
| `index.html` | Oberfläche, State, Readwise-API-Logik und Markdown-Rendering |
| `manifest.webmanifest` | PWA-Metadaten, Startpfad, Farben und Icons |
| `sw.js` | App-Shell-Cache und Offline-Fallback |
| `.github/workflows/deploy-pages.yml` | Rekonstruktion der Icons und Deployment auf GitHub Pages |
| `.pwa-icon-sources/*.b64` | Binärfreie Base64-Quellen der PNG-Icons |
| `.pwa-icon-sources/SHA256SUMS` | Prüfsummen zur Kontrolle der rekonstruierten Icons |

Beim GitHub-Pages-Deployment werden die PNG-Icons aus ihren Base64-Quellen in einem temporären `_site`-Verzeichnis rekonstruiert. Der Workflow prüft anschließend ihre SHA-256-Werte, PNG-Signaturen und Abmessungen, bevor das Pages-Artefakt veröffentlicht wird.

Der Service Worker verwendet:

- **Network First** für Navigationen und `index.html`, damit neue Deployments zuverlässig geladen werden
- **Cache First** für lokale statische Dateien
- ausschließlich erfolgreiche `GET`-Antworten derselben Origin
- keinen Cache für Readwise oder andere Cross-Origin-Requests

## Lokal ausführen

Für die normale App-Entwicklung genügt ein einfacher statischer HTTP-Server:

```bash
python3 -m http.server 8000
```

Anschließend:

```text
http://localhost:8000/
```

Für vollständige PWA-Tests müssen zusätzlich die PNG-Icons aus `.pwa-icon-sources/` rekonstruiert werden. Der GitHub-Actions-Workflow enthält dafür den verbindlichen Deploymentablauf.

## Deployment

Das Repository wird über GitHub Actions auf GitHub Pages veröffentlicht.

Einmalige Repository-Einstellung:

```text
Settings → Pages → Build and deployment → Source → GitHub Actions
```

Danach startet jeder Push auf `main` automatisch ein neues Deployment. Der Workflow kann außerdem manuell über `workflow_dispatch` ausgeführt werden.

## Projektprinzipien

- lokal und statisch statt Backend
- eine fokussierte Hauptaufgabe statt Dashboard
- keine automatische oder KI-basierte Klassifikation
- keine Veränderung des Highlighttexts oder der Readwise-Notiz
- keine Löschung vorhandener thematischer Tags
- keine versteckte Offline-Synchronisation
- bestehende lokale Daten und Storage-Keys bei Updates erhalten

## Hinweis

Readwise Decider ist ein persönliches, unabhängiges Projekt und kein offizielles Produkt von Readwise.
