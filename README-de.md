[Click here for the english version](README.md)

Dies ist eine Neuimplementierung der Inkscape-Generator-Erweiterung von Aurélio
A. Heckert, zu finden unter <http://wiki.colivre.net/Aurium/InkscapeGenerator>

Ich habe diese Neuimplementierung aus zwei Gründen vorgenommen:

* Die ursprüngliche Erweiterung kann nur mit einigem Aufwand unter Windows
genutzt werden, weil sie als Bash-Script geschrieben ist und Befehle wie `head`
oder `sed` verwendet. Diese neue Implementierung ist in Python geschrieben und
verwendet nur Standardfunktionen von Python und Windows.
* Die ursprüngliche Erweiterung hat bei Schriftzeichen wie `,` oder `"`
Probleme mit dem Parsen von CSV-Dateien.

# Installation

## Windows

Kopieren Sie `generator.inx` und `generator.py` in das Verzeichnis
`C:\Programme\Inkscape\share\extensions` (globale Installation 64 bit), in das
Verzeichnis `C:\Programme (x86)\Inkscape\share\extensions` (globale Installation
32 bit) oder in das Verzeichnis
`C:\Users\<Username>\AppData\Roaming\inkscape\extension` (Installation für
einzelnen Nutzer).

Diese Erweiterung benötigt Python 2.7, welches ab Version 0.92.1 im
Inkscape-Installer enthalten ist. Python 3 kann ebenfalls verwendet werden,
dieses ist ab Version 1.0 im Inkscape-Installer enthalten.

Die 64bit-Windows-Version von Inkscape 0.92.1 hat einen Bug, der dazu führt,
dass sich Inkscape bei Benutzung dieser Erweiterung aufhängt
([#1659172](https://bugs.launchpad.net/inkscape/+bug/1659172)). Wenn Sie
von diesem Problem betroffen sind, verwenden Sie stattdessen die 32bit-Version
oder eine neuere Version (0.92.2 oder später).

Folgendes ist unter Windows **NICHT** verfügbar:

* Fortschrittsbalken und Abbrechen-Schaltfäche während des Render-Vorgangs
* Ausgabe im JPEG-Format

Wenn Sie die JPEG-Ausgabe benötigen, können Sie ImageMagick installieren und die
Funktion `Png_to_jpg` in der Datei `generator.py` wie folgt anpassen (ersetzen
Sie `Path\to\convert.exe` mit dem richtigen Pfad von `convert.exe`):

```python
def Png_to_jpg(pngfile, jpgfile):
    Call_or_die(
        [
            'Path\to\convert.exe',
            'PNG:' + pngfile,
            'JPG:' + jpgfile
        ],
        'ImageMagick Converting Error')
```

## GNU/Linux

Kopieren Sie `generator.inx` und `generator.py` in das Verzeichnis
`/usr/share/inkscape/extensions`
(globale Installation) oder in das Verzeichnis
`/home/<username>/.config/inkscape/extensions/`
(Installation für einzelnen Nutzer).

Folgende Software wird benötigt:

* Python 2.7 oder Python 3
* Zenity (für besser Benutzer-Interaktion, grundsätzlich funktioniert die
Erweiterung auch ohne Zenity)
* Convert (Aus der ImageMagick-Suite, für den Export ins JPEG-Format)

# Inkompatible Änderungen


Einige Details in der Benutzung dieser Erweiterung unterscheiden sich von der
Benutzung der ursprünglichen Erweiterung:

* In der Bash-basierten Erweiterung mussten bestimmte Zeichen in der CSV-Datei
maskiert werden. Zum Beispiel musste `\\\\&amp;` notiert werden, um das
Zeichen `&` zu erhalten. In dieser Erweiterung muss `&`
(wenn die Berücksichtigung von Sonderzeichen aktiviert ist) oder `&amp;`
(wenn die Berücksichtigung von Sonderzeichen nicht aktiviert ist) notiert
werden.

* In der Bash-basierten Erweiterung wurden die Zeichen `[`, `]`, `   `,
`$`, `'` und `"` durch einen Unterstrich ersetzt, wenn sie in einem
Spaltennamen auftraten. Zum Beispiel musste für eine Spalte mit dem Namen
"erster Name" der Platzhalter `%VAR_erster_Name%` verwendet werden. Mit dieser
Erweiterung findet keine solche Ersetzung statt. Es muss der Platzhalter
`%VAR_erster Name%` verwendet werden.

* Diese Änderungen ist nur von Bedeutung, wenn `generator.sh` nicht als
Inkscape-Erweiterung genutzt, sondern direkt aufgerufen wurde: Im Bash-Script
hatten einige Parameter einen Bindestrich im Namen (z.B. `--data-file`).
In diesem Python-Script werden alle Parameter ohne Bindestrich geschrieben
(z.B. `--datafile`).
