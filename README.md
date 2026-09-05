# Chancenplaner 2026 – Online-App

Ziel: dieselbe App über eine feste URL auf Mac, Samsung und anderen Geräten nutzen.

## Aktiver Stand
- Live-URL: `https://t22k7jz5vb-ship-it.github.io/chancenplaner-online/`
- Hosting: GitHub Pages
- Daten + Login: Supabase
- GitHub-Repo: `git@github.com:t22k7jz5vb-ship-it/chancenplaner-online.git`

## Wichtige Dateien
- `index.html` – die App, einzige Quelldatei
- `config.js` – aktive Supabase-Konfiguration
- `config.example.js` – Vorlage für neue Konfigurationen
- `supabase_schema_v4_json.sql` – aktuelles Supabase-Schema
- `tests/` – Regressionstests
- `archive/` – lokale Snapshot-Ablage veröffentlichter Stände

## Supabase einrichten
1. Neues Supabase-Projekt anlegen
2. Im SQL Editor `supabase_schema_v4_json.sql` ausführen
3. Unter Authentication -> Sign In / Providers -> Email aktiv lassen,
   aber "Allow new users to sign up" abschalten (die App hat bewusst keinen
   Signup-Button mehr; weitere Konten legst du im Supabase-Dashboard an)
4. Unter Authentication -> URL Configuration die Live-URL als Site URL und
   als Redirect URL eintragen, damit der Passwort-Reset-Link funktioniert
5. Unter Project Settings -> API diese Werte kopieren:
   - Project URL
   - anon public key
6. `config.example.js` nach `config.js` kopieren und Platzhalter ersetzen

## Lokal prüfen
Einfacher Testserver:

```bash
cd /Volumes/2_Projekte/H_Chancenplaner/online_app
python3 -m http.server 8080
```

Dann öffnen:
- `http://localhost:8080`

## Testen
Regressionstests für den Arbeitsstand:

```bash
cd /Volumes/2_Projekte/H_Chancenplaner/online_app
python3 -m unittest discover -s tests -v
```

Zuletzt verifiziert am 2026-05-31:
- komplette Dev-Testsuite grün: `35/35`
- veröffentlichter Commit: `957b51c`
- Live-Stand zusätzlich per Hash-Abgleich für `index.html` und `config.js` geprüft

## Aktueller Stabilitätsstand
Der veröffentlichte Stand schützt Eingaben jetzt robuster bei Mobile- und Tab-Wechseln.

Enthaltene Absicherung:
- lokale Draft-Sicherung pro Nutzer
- getrennte Drafts für Strategie, Tageseintrag und Wocheneintrag
- Wiederherstellung der Drafts beim erneuten Laden
- Löschen lokaler Drafts nach erfolgreichem Save in Supabase
- zusätzliche Sicherung bei `beforeunload`, `pagehide` und `visibilitychange`

Wichtig:
- GitHub Pages hostet nur die App-Dateien
- die eigentlichen Nutzdaten liegen in Supabase
- andere Geräte sehen den aktuellen Stand nach Reload oder Neuöffnung

## Arbeitsregel
Die Projektregel liegt hier:
- `/Volumes/2_Projekte/H_Chancenplaner/WORKFLOW.md`

Kurz:
- Änderungen direkt in `index.html`
- testen
- committen und pushen
- veröffentlichten Stand zusätzlich in `archive/` sichern
- anschließend GitHub Pages prüfen

## Nutzung
- Mit E-Mail + Passwort einloggen
- Passwort vergessen? Button auf der Loginseite schickt einen Reset-Link
- Überall dieselbe URL
- Tages-, Wochen- und Jahresinhalte bleiben online verfügbar
