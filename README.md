# Chancenplaner 2026 – Online-App

Ziel: dieselbe App über eine feste URL auf Mac, Samsung und anderen Geräten nutzen.

## Aktiver Stand
- Live-URL: `https://crossnexushq.github.io/chancenplaner/`
- Hosting: GitHub Pages
- Daten + Login: Supabase
- GitHub-Repo: `git@github-crossnexus:CrossNexusHQ/chancenplaner.git`
- Alte URL (Rückfallebene, läuft vorerst weiter):
  `https://t22k7jz5vb-ship-it.github.io/chancenplaner-online/`

Hinweis zum Pushen: Der SSH-Host heisst `github-crossnexus`, nicht `github.com` —
das steuert über `~/.ssh/config`, welches der beiden GitHub-Konten genutzt wird.
Das Projekt liegt auf einer SMB-NAS-Freigabe, dort stürzt `git push` mit einem
Bus error ab (Git kann seine Packdatei über SMB nicht per mmap lesen). Bis das
Projekt auf einer internen SSD liegt, deshalb so pushen:

```bash
cp -R /Volumes/2_Projekte/H_Chancenplaner/online_app /tmp/cp_push
cd /tmp/cp_push && git push origin main
git ls-remote origin main   # unbedingt pruefen: der Push scheitert sonst lautlos
```

## Design
Die App folgt dem **CrossNexus Brand System v1.1**
(`/Volumes/2_Projekte/H_Chancenplaner/crossnexus_brand_system.md`).

- Schrift: Arial (Fallback `Liberation Sans` für Android)
- Farben nur über die CSS-Variablen in `:root` — Dark Mode ist Standard,
  der Light Mode folgt der Geräteeinstellung über `prefers-color-scheme`
- Violett, Orange und Gold sind je Modus getönt, weil die reinen Markenwerte
  auf dem jeweiligen Grund unter 4.5:1 Kontrast fallen würden. Alle 18
  Text-auf-Grund-Kombinationen liegen geprüft darüber.
- Farben nie aus dem JavaScript setzen — Inline-Styles hebeln den Light Mode
  aus. Zustände über CSS-Klassen schalten (`is-error`, `is-active`).

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
