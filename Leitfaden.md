# Ein Add-on in den NVDA Store bringen  
**Von der lokalen Entwicklung bis zur Einreichung bei NV Access**

Diese Anleitung führt Schritt für Schritt durch den gesamten Prozess:  
Vom Einrichten der Entwicklungsumgebung über das Erstellen eines GitHub‑Repositories bis hin zur finalen Einreichung des Add-ons für den NVDA Store.

## 1. Voraussetzungen installieren

Für die Add-on‑Verwaltung werden einige Werkzeuge benötigt:

- **Python** als Entwicklungsumgebung 
- **Git** für Versionsverwaltung  
- **GitHub-cli** um mit GitHub zu interagieren 

Diese Komponenten bilden die Grundlage, um Add-ons lokal zu testen und zu bauen.

Wir installieren die python-Version 3.13, um für NVDA 2026 und höher gerüstet zu sein. 

Zur Installation nutzen wir den Paketierungsmanager winget, der auf jedem Windows-11-Rechner vorhanden sein sollte. 

Geben Sie zur Installation in der Eingabeaufforderung diese Befehle ein:

```cmd
winget install Python.Python.3.13
winget install git.git 
winget install github.cli
```

Die programme werden im Hintergrund "silent" mit Standdardeinstellungen installiert. Möchten Sie diese Programme lieber benutzerdefiniert installieren, tauschen Sie "install" mit "download", dann werden die pakete in Ihren Download-Ordner geladen und können von dort aus installiert werden. Ein Neustart empfiehlt sich.

## 2. GitHub-Konto erstellen

Falls noch nicht vorhanden, erstellen Sie ein kostenloses Konto auf GitHub. Ein GitHub‑Konto ist notwendig, um das Add-on später öffentlich bereitzustellen und bei NV Access einzureichen.

Auf GitHub werden Git-Projekte gehostet, zur Verfügung gestellt, um öffentlich gesehen zu werden und damit man an Projekten gemeinsam arbeiten kann.

Öffnen Sie die Seite [https://github.com/signup?source=login](https://github.com/signup?source=login) und folgen Sie den Anweisungen. GitHub.com ist nur in Englisch verfügbar. Sie können Ihren Browser anweisen, englischsprachige Seiten nach Deutsch zu übersetzen. 

## 3. Lokales Repository anlegen

Im nächsten Schritt wird ein lokales Git-Repository (Projekt) zum Testen erstellt, um sich mit dem Versionierungssystem Git ein wenig vertraut zu machen:

1. Legen Sie sich einen allgemeinen GitHub-Ordner für Ihre Projekte an. Zum Beispiel d:\MyGitHub 
2. Dort erstellen Sie den Ordner TestRepo
3. Wechseln Sie mit dem Windows-Explorer in diesen Ordner.
4. Drücken Sie F4, geben Sie "cmd" gefolgt von return ein.
5. Sie sollten sich nun in der Eingabeaufforderung am Prompt d:\MyGitHub\TestRepo> befinden.

Wir arbeiten jetzt mit dem CLI (Kommandozeileninterpreter) von git, der auch mit git aufgerufen wird. Es gibt natürlich auch eine GUI, jedoch hat die CLI einige Vorteile, so zum Beispiel die MMöglichkeit, mehrere befehle in einer Batch-Datei auszuführen.

### Git‑Identität konfigurieren

Wenn Sie Git auf einem neuen Rechner eingerichtet haben, fehlt in der Regel noch Ihre persönliche Identität. Git benötigt **Name und E‑Mail‑Adresse**, um korrekt zu arbeiten.

Führen Sie einmalig folgende Befehle aus:

```cmd
git config --global user.name "Ihr Name"
git config --global user.email "ihre@mailadresse.de"
```

#### Einstellungen prüfen

```cmd
git config --global --list
```

Damit sehen Sie, ob `user.name` und `user.email` korrekt gesetzt sind.

### Git‑Repository initialisieren und bearbeiten 

#### Tipp für die Eingabeaufforderung
Mit Umschalt+Steuerung+a können Sie den gesamten Text, der von der Eingabeaufforderung angezeigt wird, markieren und dann mit Umschalt+Steuerung+c diesen in die Zwischenablage kopieren.

```
git init
```

Erstellt/initialisiert das Repository. Der ordner bleibt ohne offensichtlichen Inhalt. Eine Meldung wird angezeigt.

```
git status
```

Gibt auskunft über den aktuellen Status. Interessant bei der Ausgabe ist die Zeile "On branch main". Sie zeigt an, dass wir uns auf dem branch (zweig) main befinden und damit arbeiten. Wir können auch einen zweiten zweig öffnen und darin arbeiten, um diesen dann später evtl. mit dem main-branch zu mergen (verschmelzen). Darauf wollen wir an dieser Stelle nicht weiter eingehen.

Erstellen Sie nun in dem ordner TestRepo die Textdatei  readme.md im Format UTF8 mit folgendem Inhalt:

```
# Mein erstes Repo 

Hier die Dokumentation zu meinem ersten GitHub-Projekt.

Die Dokumentation ist in **Markdown** geschrieben und wer nicht weiß, was das ist und was Markdown so alles kann, mag sich vielleicht auf dieser [Markdown-Seite](https://markdown.de/) umsehen.
```

Sobald Sie Ihr Repository mit `git init` erstellt **und** die Datei `readme.md` hineinkopiert haben, beginnt der eigentliche Git‑Workflow. Der Ablauf ist immer:

1. **Dateien zum Index hinzufügen**  
2. **Commit erstellen**  

#### 1. Status prüfen
```cmd
git status
```
Damit sehen Sie, dass `readme.md` als *untracked* (nicht verfolgt*) angezeigt wird.

#### 2. Datei zum Index hinzufügen
```cmd
git add readme.md
```
Oder alle neuen Dateien:
```cmd
git add .
```

An dieser Stelle gerne mit `git status` nachsehen, was sich geändert hat.

####  3. Commit erstellen
```cmd
git commit -m "Add readme"
```
Damit speichern Sie die Datei dauerhaft im lokalen Repository mit der Message (Nachricht) "add readme". Es wird quasi ein Schnappschuss vom Istzustand gemacht. Mit `git status` gibt es jetzt die Info, dass alles OK ist.

#### 5. nachverfolgen
```cmd
git log 
```
zeigt an, was sich in unserem Projekt getan hat. Mit 
```cmd
git log --oneline
```
erhält man eine kompakte anzeige.

##### Hilfe zu git 

Das waren nur erst mal die wichtigsten Befehle, wir werden noch mehr kennen lernen. Sie wollen sich schon jetzt mehr ansehen? Hier ein wenig Hilfe zur Selbsthilfe:

```cmd
git help -a
```
Dieser Befehl listet alle verfügbaren Git‑Kommandos auf 

```cmd
git --help
```
Das zeigt eine kompakte Liste der wichtigsten Kommandos.

```cmd
git help commit
```
Wenn Sie Details zu einem einzelnen Befehl möchten. Hier zu commit, das geht natürlich auch mit add oder init. Dieser Hilfetext wird gleich im Browser angezeigt.

Damit ist das erste lokale Projekt erfolgreich erstellt.

## 4. Repository auf GitHub hochladen

Um das lokale Repository online verfügbar zu machen:

### Voraussetzungen
- Git installiert  
- GitHub CLI (gh) installiert  
- GitHub‑Konto vorhanden (Benutzername: **NVDAde**)  
- Lokales Git‑Repository existiert (z. B. ein Ordner mit `git init` initialisiert)

#### Teil 1: GitHub CLI mit Ihrem eigenen Konto verbinden

##### GitHub CLI anmelden
Öffnen Sie eine Eingabeaufforderung und führen Sie aus:

```cmd
gh auth login
```

##### Auswahl treffen
Die CLI stellt Ihnen nun Fragen. Wählen Sie jeweils Folgendes:

- **What account do you want to log into?**  
  → *GitHub.com*
- **What is your preferred protocol for Git operations?**  
  → *HTTPS*
- **Authenticate Git with your GitHub credentials?**  
  → *Yes*
- **How would you like to authenticate GitHub CLI?**  
  → *Login with a web browser*

##### Browser‑Login
- Die CLI zeigt Ihnen einen Code an (z. B. `ABCD-1234`).  
- Drücken Sie **return**, der Browser öffnet sich.  
- Geben Sie den Code ein und bestätigen Sie die Anmeldung.  
- Wählen Sie Ihr Konto **NVDAde** aus und erlauben Sie den Zugriff.

Nach erfolgreicher Anmeldung zeigt die CLI:

```
✓ Logged in as NVDAde
```

Damit ist die Verbindung zu GitHub hergestellt. Dieser Vorgang muss nur einmal durchlaufen werden.

#### Teil 2: Neues GitHub‑Repository erstellen

Mit der GitHub‑CLI können Sie das Repository direkt online anlegen. Wir befinden uns in der Eingabeaufforderung `d:\myGitHub\TestRepo`:

```cmd
gh repo create TestRepo --public --source=. --remote=origin --push
```

Bedeutung der Parameter:

- **`TestRepo`**
  → Name des neuen Repositories auf GitHub
- **`--public`**
  → Repository wird öffentlich (alternativ: `--private`)
- **`--source=.`**
  → aktuelles Verzeichnis als Repository verwenden
- **`--remote=origin`**
  → Remote „origin“ automatisch setzen
- **`--push`**
  → lokalen Stand sofort hochladen

Nach wenigen Sekunden ist das Repository online verfügbar unter:

```
https://github.com/NVDAde/TestRepo
```

#### Ergebnis
- GitHub CLI ist mit Ihrem Konto **NVDAde** verbunden  
- Ihr lokales Projekt wurde erfolgreich als **TestRepo** auf GitHub veröffentlicht  
- Das Remote ist korrekt gesetzt und zukünftige Änderungen können Sie einfach so hochladen:

```cmd
git add .
git commit -m "Beschreibung"
git push
```

Erweitern Sie gerne ihre readme.md und bringen Sie die Änderungen auf GitHub. Das geht nun ganz einfach, wenn alles einmal eingerichtet ist.

## 5. NVDA‑Add-on‑Vorlage lokal einrichten

NV Access stellt eine offizielle Vorlage (Template) auf GitHubbereit, die die Grundstruktur und Werkzeuge eines Add-ons enthält:

Bevor wir uns nun das Template holen und kennen lernen, schauen wir uns noch ein wenig die GitHub cli an: 

```cmd
gh repo list 
```

zeigt unsere eigenen Repos auf, die, die wir vorher mit `git auth login` verknüpft haben. Möchten wir uns Projekte von anderen ansehen, so geht dies wie folgt:

```cmd
gh repo list nvdaes
gh repo list nvdade
gh repo list bfw-wuerzburg 
gh repo list microsoft
gh repo list nvaccess 
```

Wir geben also einfach nur den Namen hinter den "list" an. 

Hugo 

- Vorlage klonen oder herunterladen  
- Dateien in das eigene Projekt übernehmen  
- Aufbau der Vorlage verstehen:
  - `addon/` – Python‑Code  
  - `locale/` – Übersetzungen  
  - `manifest.ini` – Metadaten  
  - Build‑Skripte (`scons`, `buildVars.py`)

Diese Struktur ist Voraussetzung für die spätere Einreichung.

## 6. Die Vorlage mit Add-on‑Code befüllen

Jetzt wird das eigentliche Add-on entwickelt:

- Python‑Module im Ordner `addon/` erstellen oder anpassen  
- Globale Plugins, App‑Module oder Treiber hinzufügen  
- Gesten und Befehle definieren  
- Dokumentation und Lokalisierung ergänzen

Wichtig ist eine klare Struktur und saubere Trennung der Komponenten.

##  7. Ein lokales Add-on erstellen

Zum Testen wird das Add-on lokal gebaut:

- Build‑Prozess starten (z. B. `scons`)  
- Die erzeugte `.nvda-addon`‑Datei installieren  
- Add-on in NVDA aktivieren  
- Logdateien prüfen und Fehler beheben

Dieser Schritt stellt sicher, dass das Add-on stabil und kompatibel ist.

##  8. Das Add-on einreichen

Wenn das Add-on fertig ist:

- Die Anforderungen von NV Access prüfen  
- Repository auf GitHub aktuell halten  
- Pull Request im offiziellen NVDA‑Add-on‑Repository erstellen  
- Auf Rückmeldungen des NV Access Teams reagieren

Nach erfolgreicher Prüfung erscheint das Add-on im NVDA Store.

## Zusätzliche Tipps und Links 

- ... 