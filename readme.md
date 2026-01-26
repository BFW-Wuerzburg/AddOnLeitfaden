# Ein Add-on in den NVDA Store bringen  
**Von der lokalen Entwicklung bis zur Einreichung bei NV Access**

Diese Anleitung ist für fortgeschrittene Anwender und führt Schritt für Schritt durch den gesamten Prozess: 

Vom Einrichten der Entwicklungsumgebung über das Erstellen eines GitHub‑Repositories bis hin zur finalen Einreichung des Add-ons für den NVDA Store.

---

## Inhaltsverzeichnis

- [1. Voraussetzungen installieren](#1-voraussetzungen-installieren)  
- [2. GitHub-Konto erstellen](#2-github-konto-erstellen)  
- [3. Lokales Repository anlegen](#3-lokales-repository-anlegen)  
  - [Git-Identität konfigurieren](#git-identität-konfigurieren)  
  - [Git-Repository initialisieren und bearbeiten](#git-repository-initialisieren-und-bearbeiten)  
  - [Wichtige Git-Befehle im Überblick](#wichtige-git-befehle-im-überblick)  
- [4. Repository auf GitHub hochladen](#4-repository-auf-github-hochladen)  
  - [GitHub CLI anmelden](#github-cli-anmelden)  
  - [Neues GitHub-Repository erstellen](#neues-github-repository-erstellen)  
  - [Wichtige GitHub-CLI-Befehle im Überblick](#wichtige-github-cli-befehle-im-überblick)  
- [5. NVDA-Add-on-Vorlage lokal einrichten](#5-nvda-add-on-vorlage-lokal-einrichten)  
- [6. Die Vorlage mit Add-on-Code befüllen](#6-die-vorlage-mit-add-on-code-befüllen)  
- [7. Ein lokales Add-on erstellen](#7-ein-lokales-add-on-erstellen)  
- [8. Das Add-on einreichen](#8-das-add-on-einreichen)  
- [Zusätzliche Tipps und Links](#zusätzliche-tipps-und-links)

---

## 1. Voraussetzungen installieren

Für die Add-on‑Einreichung werden folgende Werkzeuge benötigt:

- **Python** als Entwicklungsumgebung  
- **Git** für Versionsverwaltung  
- **GitHub CLI** zur Interaktion mit GitHub  

Wir installieren Python **3.13**, um für NVDA 2026 und höher gerüstet zu sein.

Unter Windows 11 nutzen wir den Paketmanager **winget** zum einfachen Installieren:

```cmd
winget install Python.Python.3.13
winget install git.git
winget install github.cli
```

**Was passiert hier?**

- `winget install ...` lädt das jeweilige Programm aus dem offiziellen Repository und installiert es mit Standardeinstellungen.  
- Die Installation läuft ohne weitere Rückfragen („silent“).  
- Wenn Sie mehr Kontrolle möchten, können Sie `install` durch `download` ersetzen und die Installationsdateien manuell starten.

Ein Rechner-Neustart nach der Installation ist empfehlenswert.

---

## 2. GitHub-Konto erstellen

GitHub ist die Plattform, auf der Sie Ihr Add-on‑Projekt veröffentlichen und mit anderen teilen.

1. Öffnen Sie:  
   `https://github.com/signup?source=login`  
2. Folgen Sie den Schritten zur Kontoerstellung.  
3. Merken Sie sich Ihren Benutzernamen und Ihr Passwort – beides wird später in der GitHub‑CLI benötigt.

Moderne Browser können die englische Seite automatisch ins Deutsche übersetzen.

---

## 3. Lokales Repository anlegen

Wir erstellen ein erstes Test‑Repository, um Git kennenzulernen.

1. Legen Sie einen Ordner für Ihre GitHub‑Projekte an, z. B. `D:\MyGitHub`.  
2. Erstellen Sie darin den Ordner `TestRepo`.  
3. Öffnen Sie den Ordner im Explorer.  
4. Drücken Sie **F4**, geben Sie `cmd` ein und bestätigen Sie.  
5. Sie befinden sich nun in der Eingabeaufforderung:  
   `D:\MyGitHub\TestRepo>`

### Git-Identität konfigurieren

Git speichert bei jedem Commit, **wer** ihn erstellt hat. Dazu braucht Git Ihren Namen und Ihre E‑Mail‑Adresse.

```cmd
git config --global user.name "Ihr Name"
git config --global user.email "ihre@mailadresse.de"
```

- `--global` bedeutet: Diese Einstellungen gelten für alle Git‑Projekte auf diesem Rechner.  
- `user.name` und `user.email` erscheinen später in der Commit‑Historie.

Prüfen können Sie das mit:

```cmd
git config --global --list
```

Dieser Befehl zeigt alle globalen Einstellungen an, darunter auch `user.name` und `user.email`.

---

### Git-Repository initialisieren und bearbeiten

#### Repository initialisieren

```cmd
git init
```

**Was macht `git init`?**

- Es legt im aktuellen Ordner ein verstecktes Verzeichnis `.git` an.  
- Ab diesem Moment betrachtet Git diesen Ordner als „Projekt“ und kann Änderungen an Dateien verfolgen.  
- Der Ordnerinhalt selbst ändert sich nicht sichtbar – nur Git „merkt sich“ intern den Zustand.

#### Status prüfen

```cmd
git status
```

**Was zeigt `git status`?**

- Auf welchem Branch Sie sich befinden (standardmäßig `main`).  
- Welche Dateien neu sind (*untracked*).  
- Welche Dateien geändert wurden.  
- Welche Dateien bereits für den nächsten Commit vorgemerkt sind (*staged*).

`git status` ist der wichtigste „Kontrollblick“ zwischendurch.

#### Erste Datei erstellen

Erstellen Sie im Ordner `TestRepo` die Datei `readme.md` im UTF‑8‑Format mit folgendem Inhalt:

```md
# Mein erstes Repo

Hier die Dokumentation zu meinem ersten GitHub-Projekt.

Die Dokumentation ist in **Markdown** geschrieben.  
Wer nicht weiß, was Markdown ist, findet Informationen auf dieser [Markdown-Seite](https://markdown.de/).
```

Wenn Sie jetzt erneut `git status` ausführen, sehen Sie:

- `readme.md` wird als **untracked file** angezeigt.  
  Das bedeutet: Git kennt die Datei, verfolgt sie aber noch nicht.

---

### Wichtige Git-Befehle im Überblick

#### `git add` – Dateien für den nächsten Commit vormerken

```cmd
git add readme.md
```

oder:

```cmd
git add .
```

**Was macht `git add`?**

- `git add` nimmt Änderungen in den sogenannten **Staging-Bereich** (Index) auf.  
- Nur Dateien im Staging-Bereich werden beim nächsten Commit berücksichtigt.  
- `git add readme.md` fügt nur diese eine Datei hinzu.  
- `git add .` fügt alle neuen und geänderten Dateien im aktuellen Ordner hinzu.

Typischer Ablauf:

1. Datei bearbeiten oder neu erstellen  
2. `git status` prüfen  
3. `git add ...` ausführen  
4. Wieder `git status` prüfen – die Datei sollte nun als „staged“ erscheinen

---

#### `git commit` – aktuellen Zustand speichern

```cmd
git commit -m "Add readme"
```

**Was macht `git commit`?**

- Erstellt einen Speicherpunkt, auf den ggf. später zurückgesprungen werden kann.
- Alle Dateien, die vorher mit `git add` in den Staging‑Bereich gelegt wurden, werden in diesem Commit gespeichert.  
- Die Option `-m "Nachricht"` fügt eine kurze Beschreibung hinzu, z. B. was geändert wurde.

Gute Commit‑Nachrichten sind kurz und aussagekräftig, z. B.:

- `"Initial commit"`  
- `"Add readme with basic description"`  
- `"Implement line announcement in Notepad add-on"`

Nach einem Commit zeigt `git status` meist:

```text
nothing to commit, working tree clean
```

Das bedeutet: Es gibt keine ungespeicherten Änderungen mehr.

Git commit speichert dabei nicht jedes Mal das komplette Projekt neu, sondern nur die Änderungen in der git History – sehr effizient.

---

#### `git log` – die Historie ansehen

```cmd
git log
```

**Was zeigt `git log`?**

- Alle bisherigen Commits in chronologischer Reihenfolge (neueste zuerst).  
- Zu jedem Commit:
  - eine eindeutige ID (Hash)  
  - den Autor  
  - Datum und Uhrzeit  
  - die Commit‑Nachricht  

Kurzform:

```cmd
git log --oneline
```

- Jeder Commit wird in einer Zeile angezeigt.  
- Praktisch, um schnell einen Überblick zu bekommen.

---

#### `git help` – eingebaute Hilfe

```cmd
git help -a
```

- Listet alle verfügbaren Git‑Befehle auf.

```cmd
git --help
```

- Zeigt eine kompakte Übersicht der wichtigsten Befehle.

```cmd
git help commit
```

- Öffnet die Dokumentation zu einem bestimmten Befehl (hier: `commit`).  
- Das funktioniert auch mit `git help add`, `git help log` usw.

---

## 4. Repository auf GitHub hochladen

Bis jetzt existiert Ihr Projekt nur lokal.  
Nun verbinden wir es mit GitHub, damit es online verfügbar ist.

### GitHub anmelden

```cmd
gh auth login
```

**Was macht `gh auth login`?**

- Startet den Anmeldeprozess für die GitHub‑CLI.  
- Verknüpft Ihr lokales System mit Ihrem GitHub‑Konto.  
- Danach kann `gh` in Ihrem Namen Repositories erstellen, Issues anlegen, Pull Requests öffnen usw.

Typischer Ablauf in der CLI:

- **What account do you want to log into?** → `GitHub.com`  
- **What is your preferred protocol for Git operations?** → `HTTPS`  
- **Authenticate Git with your GitHub credentials?** → `Yes`  
- **How would you like to authenticate GitHub CLI?** → `Login with a web browser`  

Dann:

- Ein Code wird angezeigt (z. B. `ABCD-1234`).  
- Sie drücken **Enter**, der Browser öffnet sich.  
- Sie geben den Code ein und bestätigen.  
- Sie wählen Ihr Konto aus und erlauben den Zugriff.

Nach erfolgreicher Anmeldung:

```text
✓ Logged in as NVDAde
```

---

### Neues GitHub-Repository erstellen

Im Ordner `D:\MyGitHub\TestRepo`:

```cmd
gh repo create TestRepo --public --source=. --remote=origin --push
```

**Was passiert hier im Detail?**

- `gh repo create TestRepo`  
  → Erstellt ein neues Repository mit dem Namen `TestRepo` auf GitHub.  

- `--public`  
  → Das Repository ist öffentlich sichtbar. (Alternativ: `--private`.)  

- `--source=.`  
  → Das aktuelle Verzeichnis (`.`) wird als Inhalt des neuen Repositories verwendet.  

- `--remote=origin`  
  → Git richtet automatisch ein Remote namens `origin` ein, das auf das neue GitHub‑Repository zeigt.  

- `--push`  
  → Der aktuelle Stand (alle bisherigen Commits) wird sofort zu GitHub hochgeladen.

Nach wenigen Sekunden ist das Repository online, z. B.:

```text
https://github.com/NVDAde/TestRepo
```

---

### Wichtige GitHub-CLI-Befehle im Überblick

#### `gh repo list` – Repositories anzeigen

```cmd
gh repo list
```

**Was macht `gh repo list`?**

- Zeigt alle Repositories des aktuell angemeldeten GitHub‑Kontos an.

Mit Benutzername:

```cmd
gh repo list nvaccess
gh repo list nvdaes 
gh repo list bfw-wuerzburg
```

- Zeigt alle öffentlichen Repositories eines anderen Accounts an.  
- Praktisch, um sich Projekte von NV Access, Community‑Organisationen oder Firmen anzusehen.

---

#### `git push` – Änderungen zu GitHub hochladen

```cmd
git push
```

**Was macht `git push`?**

- Überträgt alle lokalen Commits, die noch nicht auf GitHub sind, zum Remote‑Repository (meist `origin`).  
- Erst nach einem `git push` sind Ihre Änderungen online sichtbar.

Typischer Workflow nach Änderungen:

```cmd
git add .
git commit -m "Beschreibung der Änderung"
git push
```

---

## 5. NVDA-Add-on-Vorlage lokal einrichten

NV Access stellt eine offizielle Add-on‑Vorlage bereit, die:

- die Grundstruktur eines Add-ons enthält,  
- Skripte zum Bauen und Testen mitbringt,  
- und als guter Ausgangspunkt für eigene Erweiterungen dient.

Wechseln Sie in Ihren allgemeinen GitHub‑Ordner, z. B.:

```cmd
cd D:\MyGitHub
```

Dann klonen Sie die Vorlage:

```cmd
git clone https://github.com/nvaccess/addontemplate
```

**Was macht `git clone`?**

- Lädt das komplette Repository von GitHub herunter.  
- Legt einen neuen Ordner `addontemplate` an.  
- Dieser Ordner ist bereits ein vollständiges Git‑Repository (inkl. `.git`).

Sie können nun eingeben:

```cmd
cd addontemplate
git status
git log
```

- `git status` zeigt den aktuellen Zustand (meist „clean“).  
- `git log` zeigt die Historie der Vorlage.

Lesen Sie unbedingt die `readme.md` im Template – dort wird die Struktur des Add-ons erklärt.

Optional: NVDA‑Quellcode klonen:

```cmd
git clone https://github.com/nvaccess/nvda
```

So laden Sie sich die kompletten Quellen von NVDA herunter.

---

## 6. Die Vorlage mit Add-on-Code befüllen

Jetzt beginnt die eigentliche Entwicklung.

Beispielidee:  
Ein Add-on für den Windows‑Editor (Notepad), das per **Strg+Umschalt+Z** die aktuelle Zeilennummer ansagt, wenn sich der Fokus im Schreibbereich befindet. Hier der entsprechende Python-Code:

```python
"""NVDA App-Modul für Windows Notepad.

Dieses Modul erweitert NVDA um spezielle Funktionen für den Windows-Editor (notepad.exe).
Es ermöglicht das Ansagen der aktuellen Zeilennummer aus der Statusleiste mittels
Tastenkombination Strg+Umschalt+Z.

Optimiert für: %windir%\\system32\\notepad.exe
Autor: Rainer Brell <nvda@bfw-wuerzburg.de>
Lizenz: GNU General Public License
Code ist pyright und ruff konform 
"""

from typing import Optional
import re
import addonHandler
import api
import appModuleHandler
import ui
from controlTypes import Role
from scriptHandler import script

# Initialisiert das Übersetzungssystem für mehrsprachige Unterstützung
addonHandler.initTranslation()

class AppModule(appModuleHandler.AppModule):
	"""App-Modul für Windows Notepad (notepad.exe).

	Stellt Funktionalität bereit, um Zeilennummern aus der Statusleiste anzusagen.
	Diese Klasse wird automatisch von NVDA geladen, wenn notepad.exe im Fokus ist.
	"""

	# Translators: Name of the category for the key assignment dialog
	scriptCategory = _("Notepad - Windows Editor")

	def __init__(self, *args, **kwargs) -> None:
		"""Initialisiert das App-Modul.

		Wird von NVDA aufgerufen, wenn das App-Modul geladen wird.
		Ruft die Initialisierung der Basisklasse auf.

		Args:
			*args: Positionsargumente für die Basisklasse
			**kwargs: Schlüsselwortargumente für die Basisklasse
		"""
		super().__init__(*args, **kwargs)

	def terminate(self) -> None:
		"""Beendet das App-Modul ordnungsgemäß.

		Wird von NVDA aufgerufen, wenn das App-Modul entladen wird
		(z.B. wenn Notepad geschlossen wird). Räumt Ressourcen auf.
		"""
		super().terminate()

	@script(
		# Translators: Description for the function to display the line number
		description=_("Aktuelle Zeilennummer aus der Statusleiste ansagen"),
		gesture="kb:control+shift+z",  # Tastenkombination: Strg+Umschalt+Z
		speakOnDemand=True,  # Sprrechen, wenn "Sprachmodus bei Bedarf" steht 
	)
	def script_current_line_number(self, gesture) -> None:  # noqa: ARG002
		"""Sagt die aktuelle Zeilennummer aus der Statusleiste an.

		Diese Funktion wird ausgeführt, wenn der Benutzer Strg+Umschalt+Z drückt.
		Sie sucht nach der Statusleiste im Notepad-Fenster und extrahiert die
		Zeilennummer aus dem Statusleistentext.

		Args:
			gesture: Das Tastatur-Gesture-Objekt (wird hier nicht verwendet,
					aber von NVDA erwartet)
		"""
		# das aktuelle Vordergrundfenster (das Notepad-Fenster)
		fg = api.getForegroundObject()

		# Versuche, die Statusleiste zu finden (sie ist das letzte Kind-Element)
		statusbar: Optional[api.TextInfo] = fg.simpleLastChild

		# Prüfe, ob eine Statusleiste gefunden wurde und ob es wirklich eine ist
		if statusbar and statusbar.role == Role.STATUSBAR:
			# Hole den Text aus der Statusleiste
			# (z.B. "Zeile 15, Spalte 3" oder "Ln 15, Col 3")
			text = api.getStatusBarText(statusbar)

			# Suche nach der ersten Zahl im String mittels regulärem Ausdruck
			# \d+ bedeutet: eine oder mehrere Ziffern
			match = re.search(r"\d+", text)

			if match:
				# Extrahiere die gefundene Zahl (die Zeilennummer)
				line = match.group()

				# Translators: Message for the current line number
				# {line} wird durch die tatsächliche Zeilennummer ersetzt
				ui.message(_("line {line}").format(line=line))
			else:
				# Translators: Error message if no line number was found
				ui.message(_("No line number found."))
		else:
			# Wenn keine Statusleiste gefunden wurde
			# Translators: Error message if there is no status bar
			ui.message(_("No status bar found."))
```

---

## 7. Ein lokales Add-on erstellen

Dieser Abschnitt ist als nächster Schritt gedacht.

---

## 8. Das Add-on einreichen

Wenn das Add-on stabil läuft, folgt die Einreichung bei NV Access:

> Hier folgt noch eine Schritt‑für‑Schritt‑Anleitung zur Einreichung im NVDA Store.

---

## Zusätzliche Tipps und Links

- [Entwickler-Leitfaden](https://download.nvaccess.org/documentation/developerGuide.html)
- [entwickler-Wiki](https://github.com/nvdaaddons/DevGuide/wiki/NVDA-Add-on-Development-Guide)
