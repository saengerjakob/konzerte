# GitHub-Synchronisierung einrichten

## Website-Dateien veröffentlichen

Für Excel-Import, Installation und Offline-Nutzung müssen jetzt **alle Dateien aus dem Ordner `outputs`** in deinem GitHub-Pages-Repository liegen. Dazu gehören insbesondere:

- `index.html`
- `xlsx.full.min.js`
- `manifest.webmanifest`
- `sw.js`
- `icon-192.png`, `icon-512.png`, `icon-192.svg` und `icon-512.svg`

Die Dateien müssen im selben Ordner wie die `index.html` liegen. Nach dem Hochladen GitHub Pages kurz neu laden. Die App kann anschließend über den eingeblendeten Button **App installieren** oder über das Browsermenü zum Startbildschirm hinzugefügt werden. Der Offline-Modus steht nach dem ersten vollständigen Online-Aufruf bereit.

Der sichtbare Import und Export verwendet Excel-Dateien (`.xlsx`). Die GitHub-Synchronisierung nutzt intern weiterhin `concert-data.json`; diese Datei bitte nicht manuell bearbeiten.

Die Konzertseite speichert ihre gemeinsamen Daten in einer separaten Datei namens `concert-data.json`. Für die sicherste und übersichtlichste Trennung empfiehlt sich dafür ein eigenes **privates** GitHub-Repository.

## 1. Privates Daten-Repository erstellen

1. Auf GitHub oben rechts **New repository** wählen.
2. Zum Beispiel den Namen `meine-konzertdaten` vergeben.
3. **Private** auswählen.
4. **Add a README file** aktivieren, damit der Branch `main` sofort existiert.
5. Repository erstellen.

## 2. Eingeschränkten Token erzeugen

1. GitHub öffnen: **Settings → Developer settings → Personal access tokens → Fine-grained tokens**.
2. **Generate new token** wählen.
3. Eine sinnvolle Laufzeit festlegen.
4. Unter **Repository access** nur das eben erstellte Daten-Repository auswählen.
5. Unter **Repository permissions** ausschließlich **Contents: Read and write** erlauben. `Metadata: Read` wird automatisch ergänzt.
6. Token erzeugen und sofort sicher kopieren. GitHub zeigt ihn später nicht erneut vollständig an.

Der Token darf niemals in `index.html`, einem Git-Commit oder einem Screenshot landen. Die Website speichert ihn ausschließlich im lokalen Browserspeicher des jeweiligen Geräts.

## 3. Erstes Gerät verbinden

Zuerst das Gerät verwenden, auf dem deine vollständige aktuelle Konzertliste liegt.

1. Die veröffentlichte Konzertseite öffnen.
2. **Einstellungen → GitHub-Synchronisierung** öffnen.
3. GitHub-Benutzernamen eintragen.
4. Als Repository beispielsweise `meine-konzertdaten` eintragen.
5. Branch `main` und Datei `concert-data.json` unverändert lassen.
6. Den Fine-grained Token einfügen.
7. **Verbinden und synchronisieren** wählen.

Existiert die Datendatei noch nicht, wird sie automatisch mit der aktuellen lokalen Liste erstellt.

## 4. Weitere Geräte verbinden

Auf Handy und weiteren Computern dieselben Angaben und denselben Token eintragen. Beim Verbinden wird die bestehende `concert-data.json` geladen. Danach gilt:

- Änderungen werden nach dem Speichern automatisch zu GitHub übertragen.
- Beim Öffnen der Seite, beim Zurückkehren in den Tab und ungefähr einmal pro Minute wird der aktuelle Stand geladen.
- Die lokale Speicherung bleibt als Offline-Kopie erhalten.
- Über **Aktuelle Liste hochladen** und **Cloud-Daten laden** kann der Abgleich jederzeit manuell ausgelöst werden.

## Sicherheit und Konflikte

- Verwende ein separates privates Repository und einen Token, der ausschließlich darauf zugreifen darf.
- Bei einem verlorenen oder versehentlich veröffentlichten Token diesen sofort in GitHub widerrufen und einen neuen erzeugen.
- Bearbeitest du dieselbe Liste exakt gleichzeitig auf zwei Geräten, kann GitHub einen Konflikt melden. Dann zuerst **Cloud-Daten laden**, die Änderung erneut vornehmen und speichern.
- Vor der ersten Einrichtung empfiehlt sich zusätzlich ein JSON-Export der Konzertliste.
