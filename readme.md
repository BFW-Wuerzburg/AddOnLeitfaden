# Ein Add-on in den NVDA Store bringen  
**Von der lokalen Entwicklung bis zur Einreichung bei NV Access**

Diese Anleitung ist für fortgeschrittene Anwender und führt Schritt für Schritt durch den gesamten Prozess: 

Vom Einrichten der Entwicklungsumgebung über das Erstellen eines GitHub‑Repositories bis hin zur finalen Einreichung des Add-ons für den NVDA Store.

---

## Inhaltsverzeichnis

- [1. Voraussetzungen](#1-Voraussetzungen)
- [1.1 programme installieren](#1-1-Programme-installieren)  
- [1.2 GitHub-Konto erstellen und verbinden](#1-2-github-konto-erstellen-und-verbinden)  
- [1.3 Git-Identität konfigurieren](#1-3-git-identität-konfigurieren)  
- [1.4 Python-Module und Gettext](1-4-python-Module-und-gettext)
- [2. Lokales Repository anlegen](#2-lokales-repository-anlegen)  
  - [Git-Repository initialisieren und bearbeiten](#git-repository-initialisieren-und-bearbeiten)  
  - [Wichtige Git-Befehle im Überblick](#wichtige-git-befehle-im-überblick)  
- [3. Repository auf GitHub hochladen](#3-repository-auf-github-hochladen)  
  - [Neues GitHub-Repository erstellen](#neues-github-repository-erstellen)  
  - [Wichtige GitHub-CLI-Befehle im Überblick](#wichtige-github-cli-befehle-im-überblick)  
- [4. NVDA-Add-on-Vorlage klonen](#4-nvda-add-on-vorlage-klonen)  
- [5. Die Vorlage mit Add-on-Code befüllen](#5-die-vorlage-mit-add-on-code-befüllen)  
- [6. Ein lokales Add-on erstellen](#6-ein-lokales-add-on-erstellen)  
- [7. Das Add-on einreichen](#7-das-add-on-einreichen)  
- [8. Zusätzliche Tipps und Links](#8zusätzliche-tipps-und-links)

---

## 1. Voraussetzungen

In diesem Kapitel schaffen wir die Voraussetzungen. Wir installieren Programme, richten GitHub und git ein und fügen Module für python hinzu, die uns später helfen werden.

All diese Aufgaben müssen nur einmal gemacht werden.

--- 

### 1.1. Programme installieren

Für unser Vorhaben werden folgende Werkzeuge empfohlen:

- **Python** als Entwicklungsumgebung  
- **Git** für Versionsverwaltung  
- **GitHub CLI** zur Interaktion mit GitHub  
- **Poedit und gettext-Tools** für die Übersetzung 
- **Microsoft Visual Studio Code ** Zur bearbeitung von Code, ist nicht zwingend notwendig

Wir installieren Python **3.13**, um für NVDA 2026 und höher gerüstet zu sein.

Unter Windows 11 nutzen wir den Paketmanager **winget** zum einfachen Installieren. Verwenden sie gerne diese Befehle von der eingabeaufforderung aus:

```cmd
winget install Python.Python.3.13
winget install git.git
winget install github.cli
winget install VaclavSlavik.Poedit
winget install Microsoft.VisualStudioCode
```

**Was passiert hier?**

- `winget install ...` lädt das jeweilige Programm aus dem offiziellen Repository und installiert es mit Standardeinstellungen.  
- Die Installation läuft bis auf die Sicherheitsabfragen ohne weitere Rückfragen („silent“).  
- Wenn Sie mehr Kontrolle möchten, können Sie `install` durch `download` ersetzen und die Installationsdateien manuell starten.

Ein Rechner-Neustart nach der Installation ist empfehlenswert.

---

### 1.2 GitHub-Konto erstellen und verbinden

GitHub ist die Plattform (hoster), auf der Sie Ihr Add-on‑Projekt veröffentlichen und mit anderen teilen.

1. Öffnen Sie:  
   [https://github.com/signup?source=login](https://github.com/signup?source=login)
2. Folgen Sie den Schritten zur Kontoerstellung.  
3. Merken Sie sich Ihren Benutzernamen und Ihr Passwort – beides wird  in der GitHub‑CLI benötigt.

Moderne Browser können die englische Seite automatisch ins Deutsche übersetzen.

#### GitHub anmelden

Öffnen Sie die Eingabeaufforderung und geben Sie ein:

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

### 1.3 Git-Identität konfigurieren

Git speichert bei jedem Commit, **wer** ihn erstellt hat. Dazu braucht Git Ihren Namen und Ihre E‑Mail‑Adresse.

Öffnen Sie die Eingabeaufforderung und geben Sie ein:

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

### 1.4 Python-Module und Gettext

Wir müssen einige Module unter python installieren, um lokale NVDA-Addon-Pakete zu erstellen. 

Geben Sie diese befehle in einer Eingabeaufforderung ein:

```cmd
python -m pip install --upgrade pip
python -m pip install markdown
python -m pip install scons
```

Nachdem das Python-Installer-Paket pip aktualisiert wurde und zwei Module installiert wurden, können wir jetzt noch die Gettext-Tools auf pfad setzen.

Die Gettext-Tols werden benötigt, um die Lokalisierungstrings aus den py-Dateien in eine po-atei zu extrahieren und mehr.

Es gibt mehrere möglichkeiten, an diese tools zu gelangen. Durch die Installation von Poedit sind diese schon auf Ihrem Rechner. Es muss nur noch der pfad zu den Tools in die Umgebungsvariable path aufgenommen werden.

Wenn Sie  Poedit mit den Standard-Einstellungen installiert haben, ist der gesuchte Pfad dieser:
"C:\Program Files\Poedit\GettextTools\bin"

Startmenü öffnen, „Umgebungsvariablen“ tippen → „Systemumgebungsvariablen bearbeiten“

Dort bei path den Pfad hinzufügen.

Evtl. zum Testen in der eingabeaufforderung xgettext.exe eingeben. Die Datei sollte gefunden werden, egal wo Sie sich befinden.

--- 

## 2. Lokales Repository anlegen

Wir erstellen ein erstes Test‑Repository (Projekt), um Git kennenzulernen.

1. Legen Sie einen Ordner für Ihre GitHub‑Projekte an, z. B. `D:\MyGitHub`.  
2. Erstellen Sie darin den Ordner `TestRepo`.  
3. Öffnen Sie den Ordner `TestRepo` im Explorer.  
4. Drücken Sie **F4**, geben Sie `cmd` ein und bestätigen Sie.  
5. Sie befinden sich nun in der Eingabeaufforderung:  
   `D:\MyGitHub\TestRepo>`

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
3. `git add .` ausführen  
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

## 3. Repository auf GitHub hochladen

Bis jetzt existiert Ihr Projekt nur lokal.  
Nun verbinden wir es mit GitHub, damit es online verfügbar ist.

### Neues GitHub-Repository erstellen

Im Ordner `D:\MyGitHub\TestRepo` folgendes in der Eingabeaufforderung eingeben:

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

## 4. NVDA-Add-on-Vorlage klonen

NV Access stellt eine offizielle Add-on‑Vorlage (template) bereit, die:

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
- `git log` zeigt die Geschichte der Vorlage.

Lesen Sie unbedingt die `readme.md` im Template – dort wird die Struktur des Add-ons erklärt.

Optional: NVDA‑Quellcode klonen:

```cmd
git clone https://github.com/nvaccess/nvda
```

So laden Sie sich die kompletten Quellen von NVDA herunter.

---

## 5. Die Vorlage mit Add-on-Code befüllen

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
		description=_("Announce current line number from the status bar"),
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

Speichern Sie diesen Code in die Textdatei notepad.py im Format UTF8.

Erstellen Sie einen ordner mit dem Namen "MyFirstAppAddon". 

Nun aus dem ordner "addontemplate" folgendes nach "MyFirstAppAddon"  kopieren:

- site_scons
- .gitattributes
- .gitignore
- buildVars
- manifest.ini
- manifest-translated.ini
- readme.md- sconstruct
- style

Sie sind bereits im Ordner "MyFirstAppAddon". Legen Sie dort den Ordner "addon" an. Dort wiederum den Ordner "appModules" anlegen, weil wir ein Addon für eine Anwendung erstellen wollen. Also kopieren wir jetzt auch in diesen Ordner die notepad.py hinein.

Jetzt öffnen wir die Datei: d:\MyGitHub\MyFirstAppAddon\buildVars.py

Ändern Sie in der Datei `buildVars.py` die Variable `addoninfo` mit den Informationen Ihres Add-ons:
- Name
- Zusammenfassung
- Was ist neu (darf Markdown sein)
- Beschreibung
- Version
- Autor 
- URL 
- Quell-URL 
- Lizenz
- Lizenz-URL

als Beispiel für name verwenden wir hier "myNotepad". 

Stellen Sie außerdem sicher, dass Sie die Pfade in den anderen Variablen in dieser Datei sorgfältig festlegen. Wichtig ist hier die Zeile, 
```python
pythonSources: list[str] = []
```
Geben Sie hier die pfade zu Ihren py-Dateien an. Dies wird benötigt für die Erstellung der Lokalisierungsdateien. Das könnte so ausehen:
```python
pythonSources: list[str] = ["addon/appModules/*.py", "addon/globalPlugins/*.py"]
```

Wenn Sie sich eine funktionierende buildVars.py ansehen möchten, dann klonen Sie einfach ein bestehndes Addon. Die URL dazu finden Sie im NVDA Store.

Die readme.md ist in englisch zu verfassen und sollte Ihr Handbuch zu Ihren addon beinhalten.

---

## 6. Ein lokales Add-on erstellen

### 6.1 Lokalisierung

Wir kümmern uns nun um die Übersetzungen, wobei englisch als Vorlage gilt, weil wir das so im quellcode und in der Buildvars.ini festgelegt haben.

wechseln Sie in Ihrem Addon-ordner in die Eingabeaufforderung und führen Sie aus:

```batch
scons pot 
```

es wird nun eine myNotepad.pot angelegt. Das ist eine Vorlage (template) für eine po-Datei. 

Starten Sie nun den am Anfang installierten Poedit, wählen Sie im Datei-Menü "Neu aus POT-/PO-Datei", selektieren Sie die myNotepad.pot, legen Sie "Deutsch" als Sprache fest und übersetzen Sie die 8 einträge. Dabei ist darauf zu achten, dass die Zeichenketten in {Geschweiften Klammern} nicht zu übersetzen sind.

Mit Strg+s die Datei als nvda.po an diesen ort speichern: D:\GitHub\MyFirstAddon\addon\locale\de\LC_MESSAGES

### 6.2 Handbuch 

Ihr englisches Handbuch befindet sich in der readme.md direkt im Ordner myFirstAddon. Kopieren Sie dieses nach D:\GitHub\MyFirstAddon\addon\doc\de und übersetzen Sie es nach Deutsch.

### 6.3 Das Addon bauen 

wechseln Sie in Ihrem Addon-ordner in die Eingabeaufforderung und führen Sie aus:

```batch
scons
```

Sie erhalten nun eine Datei mit einem Namen wie myNotepad-2026.03.16.nvda-addon. Diese Datei können Sie nun mit einem Doppelklick im Explorer ausführen und sollten dann Ihr erstes Addon erfolgreich gebaut und installiert haben.

Herzlichen Glückwunsch!

beachten sie, das scons aus der nvda.po eine nvda.mo generiert hat, aus der readme.md eine readme.html erstellt wurde, das eine englische und eine deutsche manifest.ini generiert auftaucht und eine style.css für das layout kopiert wurde.

### 6.4 Das Addon modifizieren

Sie werden sicher im Laufe der zeit Ihre py-Dateien anpassen und ggf. neue zu lokalisierende Strings einbauen. Diese müssen dann auch in die jeweiligen vorhandenen nvda-po-Dateien Einzug halten.

Es gibt mehrere Möglichkeiten, um dies zu realisieren:

1. Händisch 

Öffnen Sie die nvda.po mit poedit. Im Menü "Übersetzung" den Punkt Aus POT-Datei aktualisieren" auswählen. Die aktualisierte myNotepad.pot selektieren. Der Rest ist sprechend.

2. Mit einer batch-Datei 

Wenn Sie viele NVDA-po-Dateien haben, dann ist das recht aufwendig. Daher bedienen wir uns dieser batch-Datei:

```cmd
scons pot 
For /R addon\locale\ %%f In (*.po) Do msgmerge -U --backup=none %%f myNotepad.pot
```

Wenn Sie nicht möchten, dass diese Batch-Datei in Ihr repo auf GitHub aufgenommen wird, dann fügen Sie den Batch-Dateinamen in die datei .gitignore hinzu.

## 7. Das Add-on einreichen

Sie haben Ihr Addon lokal erstellt und ausführlich getestet.

Jetzht muss alles noch auf GitHub hochgeladen und ein release eingepflegt werden, damit wir das Addon für den NVDA Store bei NV Access zur Einreichung anmelden können.

Erst mal müssen wir einmalig unseren ordner als git ordner initialisieren:

```cmd
git init 
```

Wir erstellen und verbinden einmalig das GitHub repo:

```cmd
gh repo create myNotepad --public --source=.
git remote add origin https://github.com/BFW-Wuerzburg/myNotepad 
```

Jetzt fügen wir alle Dateien hinzu und erstellen  ein commit:

```cmd
git add . 
git commit -m "First initialization"
```

Und jetzt einfach nur noch alles online stellen.

```cmd
git push 
```

Diese Befehle kann man auch in eine Batch-Datei schreiben.

Jetzt ist unser Repo für alle sichtbar auf GitHub. Was fehlt, ist unser Addon als Herunterladbare Version auf gitHub, auf das der Store als Download zugreifen kann. Wir müssen also noch ein release wie folgt erstellen:

```cmd
gh release create 2026.03.16 myNotepad-2026.03.16.nvda-addon --title "myNotepad 2026.03.16" --notes "First Release"
```

Noch zwei Befehle, die interessant sein könnten. Einmal die releases anzeigen und dann ein release löschen:

```cmd
gh release list
gh release delete 2026.03.16
```

So und jetzt müssen wir unser release einfach nur noch zum Einreichen in den NVDA Store anmelden.

Auf dieser Website finden Sie [das Formullar zum Einreichen](https://github.com/nvaccess/addon-datastore/issues/new?assignees=nvaccess&labels=autoSubmissionFromIssue&projects=&template=registerAddon.yml&title=%5BSubmit+add-on%5D%3A+).

Wenn Sie die Felder alle ausgefüllt und das Formular abgesendet haben, wird ein issue auf GitHub erstellt und jemand von Einreichungsteam wird sich darum kümmern Dies kann beim erstmaligen einreichen etwas dauern, weil genauer hingesehen wird. Wenn Sie erst als vertrauenswürdig eingestuft sind, dauert die Freigabe nur einige Minuten in der Regel. Auch wenn das issue geschlossen ist, wird Ihr Addon noch nicht gleich im NVDA Store auftauchen, das dauert in der Regel noch einige Stunden.

---

## 8. Zusätzliche Tipps und Links

- [Entwickler-Leitfaden](https://download.nvaccess.org/documentation/developerGuide.html)
- [entwickler-Wiki](https://github.com/nvdaaddons/DevGuide/wiki/NVDA-Add-on-Development-Guide)
