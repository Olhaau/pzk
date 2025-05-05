---
title: wget › Wiki › ubuntuusers.de
---

# wget › Wiki › ubuntuusers.de

url:: https://wiki.ubuntuusers.de/wget/
up: [[sources]]

#source

### Dieser Artikel wurde für die folgenden Ubuntu-Versionen getestet:

Dieser Artikel ist größtenteils für alle Ubuntu-Versionen gültig.  

### Zum Verständnis dieses Artikels sind folgende Seiten hilfreich:

[![Wiki/Icons/terminal.png](https://media-cdn.ubuntu-de.org/wiki/thumbnails/8/87/873df3d4149ab35efbc7b1f3654ad92a9b4e579ai48x.png)](https://media-cdn.ubuntu-de.org/wiki/attachments/10/28/terminal.png) [wget](http://www.gnu.org/software/wget/) 🇬🇧 ist ein Programm, mit dem man direkt aus einem Terminal Dateien von FTP-, HTTP- oder HTTPS-Servern herunterladen kann. Das Programm ist sehr praktisch, wenn man in einem Shellscript Daten von Servern holen will, aber es ist auch ein sehr guter Downloadmanager. Downloads können zu einem späteren Zeitpunkt an der abgebrochenen Stelle fortgeführt werden.

## Installation[¶](#Installation)

Das Programm ist normalerweise in der Standardinstallation enthalten, kann ansonsten aber über das gleichnamige Paket installiert werden ^[[1]](#source-1)^:

* **wget**

Befehl zum Installieren der Pakete:  

```
sudo apt-get install wget 
```

## Aufruf[¶](#Aufruf)

Aus einem Terminal heraus ^[[2]](#source-2)^ kann man wget aufrufen:

Im Folgenden sind einige Optionen aufgelistet. Alle Optionen findet man in der [manpage](https://wiki.ubuntuusers.de/man/) von wget.

### Log- und Eingabe-Optionen[¶](#Log-und-Eingabe-Optionen)

|----------------------------------|-------------------------------------------------------------|
| Option                           | Beschreibung                                                |
| `-i DATEI`,`--input-file=DATEI ` | Liest URLs aus einer Text- oder HTML-Datei aus.             |
| `-nv`,`--no-verbose`             | Gibt nur die wichtigsten Informationen aus.                 |
| `-q`, `--quiet`                  | Verhindert dass wget Informationen auf der Konsole ausgibt. |
| `-v`, `--verbose`                | Vollständige Ausgabe aller Informationen. (Standard)        |

### Download-Optionen[¶](#Download-Optionen)

|---------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Option                                | Beschreibung                                                                                                                                                                                                                                                                                                   |
| `-t X`, `--tries=X`                   | Anzahl der Versuche X, die wget unternehmen soll eine Datei herunterzuladen.                                                                                                                                                                                                                                   |
| `-c`, `--continue`                    | Versucht einen teilweise durchgeführten Download fortzusetzen.                                                                                                                                                                                                                                                 |
| `-T X`                                | Zeitüberschreitung nach X Sekunden. Gilt für DNS-Abfrage, Verbindungsaufbau und letzten Datenempfang vom laufenden Download. Standard: 900.                                                                                                                                                                    |
| `-w X`                                | Bei mehreren Downloads, X Sekunden warten bis der Nächste angefordert wird. Entlastet den Server; Kann eine Verbannung wegen zu vieler Anfragen verhindern.                                                                                                                                                    |
| `--random-wait`                       | Zufällige Wartezeit um Verbannung zu verhindern. Der mit der Option *-w* gesetzte Zeitraum wird mit einem zufälligen Multiplikator von 0.5 bis 1.5 abgewandelt.                                                                                                                                                |
| `--waitretry=X`                       | Nach fehlgeschlagenem Download X Sekunden abwarten bis zum nächsten Versuch. Entlastet Server.                                                                                                                                                                                                                 |
| `-N`                                  | Lädt nur dann die Datei herunter, wenn die Datei neuer ist als eine Datei gleichen Namens auf dem Heimrechner.                                                                                                                                                                                                 |
| `-O DATEI`, `--output-document=DATEI` | Schreibe in `DATEI`. Diese Option kann nicht mit der Option `-N` zusammen verwendet werden. Gibt man `-O "-"` an, so schreibt wget den Inhalt der heruntergeladenen Datei direkt auf die Standardausgabe (stdout) des Terminals. Letzteres ist v.a. zum Auslesen von kurzen (Konfigurations-)Dateien sinnvoll. |
| `--user=BENUTZER`                     | Gibt einen Benutzernamen für HTTP- bzw. FTP-Downloads an.                                                                                                                                                                                                                                                      |
| `--password=PASSWORT`                 | Gibt ein Passwort für HTTP- bzw. FTP-Downloads an.                                                                                                                                                                                                                                                             |

### Verzeichnis-Optionen[¶](#Verzeichnis-Optionen)

|-----------------------------|------------------------------------------------------------------------------------------------------------|
| Option                      | Beschreibung                                                                                               |
| `-P VERZEICHNIS`            | Erlaubt die Angabe eines Verzeichnisses, wo die heruntergeladene(n) Datei(en) gespeichert werden soll(en). |
| `-nd`, `--no-directories`   | Falls Dateien rekursiv heruntergeladen werden, so werden sie lokal in einem Verzeichnis gespeichert.       |
| `-x`, `--force-directories` | Das Gegenteil von `-nd`. Es wird eine Verzeichnisstruktur erstellt.                                        |

### Optionen für den rekursiven Download[¶](#Optionen-fuer-den-rekursiven-Download)

|-----------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Option                            | Beschreibung                                                                                                                                               |
| `-r`, `--recursive`               | Aktiviert den rekursiven Download von Daten.                                                                                                               |
| `-l TIEFE`, `--level=<TIEFE>`     | Anzahl der Schritte TIEFE, die wget weiterverfolgen soll                                                                                                   |
| `-k`, `--convert-links`           | Konvertiere nach dem Download externe Links zu internen, so dass ein HTML-Dokument Offline betrachtet werden kann.                                         |
| `-np`, `--no-parent`              | Stellt sicher, dass nur Dateien unterhalb der angegebenen Hierarchieebene heruntergeladen werden. Links zu übergeordneten Verzeichnissen werden ignoriert. |
| `-A ACCLIST`, `--accept ACCLIST`  | Mit Kommata getrennte Liste an Datei-Erweiterungen, die heruntergeladen werden sollen.                                                                     |
| `-R REJLIST`, `--reject REJLIST`  | Mit Kommata getrennte Liste an Datei-Erweiterungen, die nicht heruntergeladen werden sollen.                                                               |
| `-D DOMAINS`, `--domains=DOMAINS` | Mit Kommata getrennte Liste an Domains, deren Links verfolgt werden sollen.                                                                                |
| `--exclude-domains LISTE`         | Eine Liste an Domains, die nicht beachtet werden sollen.                                                                                                   |
| `-e robots=off`                   | Ignoriere die **robots.txt**, damit alle Dateien heruntergeladen werden können.                                                                            |

### Beispiele[¶](#Beispiele)

#### Einfacher Dateidownload[¶](#Einfacher-Dateidownload)

Grundsätzlich kann man eine Datei mit dieser Syntax in das aktuelle Verzeichnis laden:  

```
wget http://example.com/folder/file 
```

#### Bilderdownload[¶](#Bilderdownload)

Will man eine Seite mit allen Bildern herunterladen, kann der Befehl so lauten:  

```
wget -r -l 3 -np -p --user-agent="Mozilla/5.0 (X11; U; Linux i686; de; rv:1.9b5) Gecko/2008050509 Firefox/3.0b5" http://example.com/dir/ 
```

`-p` gibt vor, den Inhalt der Seite herunterzuladen, wobei rekursiv (`-r`) bis zu einer Tiefe von 3 (`-l 3` oder `--level=3`) vorgegangen wird, übergeordnete Verzeichnisse werden ignoriert (`-np`). Außerdem gibt wget vor, als Mozilla unterwegs zu sein (`--user-agent`), da viele Seiten wget blockieren.

Zudem ist es möglich nur spezielle Dateitypen zu bekommen. Bei diesem Aufruf werden rekursiv die Dokumente nach ***.jpg** und ***.png** durchsucht und abgespeichert:  

```
wget -r -A jpg,png http://example.com/dir/  
```

Und mit diesem Befehl werden alle Dateien außer ***.jpg** und ***.png** heruntergeladen:  

```
wget -r -R jpg,png http://example.com/dir/  
```

#### Dateidownload mit Wiederholungsfunktion[¶](#Dateidownload-mit-Wiederholungsfunktion)

Was ist, wenn die Internetverbindung abbricht? Dann kann man den Download auch auf eine begrenzte oder unbegrenzte Anzahl von Wiederholungen schalten:  

```
wget -t X http://example.com/FOLDER/FILE 
```

Das `X` steht wie für eine Zahl entsprechend der Anzahl der gewünschten Wiederholungen. `0` steht für unendlich.

#### Download abbrechen[¶](#Download-abbrechen)

Mit Strg + C kann man einen aktiven Download abbrechen.

#### Download fortsetzen[¶](#Download-fortsetzen)

Hat man einen Download manuell abgebrochen oder ist er aus einem anderen Grund gescheitert, so kann man einen unvollständigen Download mit  

```
wget -c http://example.com/FOLDER/FILE 
```

fortsetzen. Nun beginnt er dort, wo er aufgehört hat.

#### Referer ändern[¶](#Referer-aendern)

Manche Downloadserver erlauben einen Download nur, wenn der Benutzer von einer bestimmten Adresse kommt. Diese ist immer die, von der normalerweise der Download startet. wget kann diesen Wert übernehmen:  

```
wget --referer=http://von.dieser.seite.com/me/ich http://example.com/FOLDER/FILE 
```

#### Geschwindigkeit begrenzen[¶](#Geschwindigkeit-begrenzen)

Wenn man bei einem Download gleichzeitig noch schnell surfen will, ist es praktisch, die Downloadrate zu begrenzen. In diesem Beispiel wird sie auf 40 kB/s begrenzt:  

```
wget --limit-rate=40000 http://example.com/FOLDER/FILE 
```

#### Im Hintergrund herunterladen[¶](#Im-Hintergrund-herunterladen)

Bei großen Dateien bietet es sich an, den Download im Hintergrund zu starten. wget läuft dann eigenständig im Hintergrund und schreibt im Download-Verzeichnis eine log-Datei:  

```
wget -b http://example.com/FOLEDER/FILE 
```

#### Websites offline speichern[¶](#Websites-offline-speichern)

Das ist sehr einfach und geht mit:  

```
wget -r -k -E -l 8 http://example.com 
```

Die Option `-r` sorgt dafür, dass rekursiv bis zu einer Tiefe von 8 (`-l 8`) vorgegangen wird, `-k` konvertiert die Links, so dass sie lokal funktionieren und `-E` erzwingt die Endung **.html** für alle Dateien (ist dann nützlich, wenn .**php** - oder .**asp**-Seiten geladen werden.

Hinweis auf eine alternative Möglichkeit: [httrack](http://www.httrack.com/) 🇬🇧

#### Tarballs automatisch entpacken[¶](#Tarballs-automatisch-entpacken)

Um **.tar.gz**-Archive direkt zu entpacken ohne sie zwischen zu speichern, kann man folgenden Befehl nutzen:  

```
wget -q -O - http://example.com/FOLDER/FILE.tar.gz | tar xvzf - 
```

Die Option `-q` sorgt dafür, dass keine Informationen ausgegeben werden, die `tar` nicht verarbeiten könnte. Mit `-O -` wird wget angewiesen die Datei direkt auf die Standardausgabe STDOUT zu schreiben. Mittels `|` wird die Standardausgabe STDOUT auf die Standardeingabe STDIN für das nächste Programm (in diesem Fall `tar`) umgeleitet. Nun wird die Datei ganz normal entpackt, wobei `x` für extrahieren, `v` für verbose (also viele Infos ausgeben), `z` für dekomprimieren steht. Mit `f -` wird die Datei von der Standardeingabe STDIN (die in diesem Fall ja von wget kommt) gelesen.

Nach diesem Befehl befinden sich nur die entpackten Dateien und Verzeichnisse, nicht jedoch die eigentliche **.tar.gz**-Datei im aktuellen Verzeichnis.

#### Zeitsteuerung von Downloads[¶](#Zeitsteuerung-von-Downloads)

Um Downloads zum Beispiel um 13:40 Uhr zu beginnen, hilft folgendes Skript:  

|---------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ``` 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 33 34 35 36 37 ``` | ``` #!/bin/sh cd ~/Downloads echo "\033[49;1;34mFolgende Datei soll heruntergeladen werden:" echo "\033[49;1;32m" echo "$1" | awk -F / '{print $NF}' echo echo "\033[49;1;35mWann soll der Download starten? (Format: hhmmss)" echo "\033[0m" read u if [ $u = "next" ] then ps -o etime,pid --no-headers -C dl | awk '{print $1,$2}' | sort -r | awk '{print $2}' > ~/.log$$ var=`head -n 1 ~/.log$$` if [ $$ -ne $var ] then echo "Nach dem letzten Download wird dieser Download gestartet." fi while [ $$ -ne $var ] do sleep 1 ps -o etime,pid --no-headers -C dl | awk '{print $1,$2}' | sort -r | awk '{print $2}' > ~/.log$$ var=`head -n 1 ~/.log$$` done rm ~/.log$$ elif [ $u = "now" -o $u = "jetzt" -o $u = "Jetzt" ] then echo "Der Download startet jetzt!" else while [ $(date +%H%M%S) -ne $u ] do sleep 1 done fi wget -c $1 echo "Der Download ist beendet." ``` |

Der Name des Skripts sollte dl heißen, ansonsten muss man im Skript dl durch den Namen ersetzen. Das Skript ausführbar machen und am besten in einem Pfad speichern der in $PATH enthalten ist. Dann kann der Download wie folgt gestartet werden:  

```
dl http://example.com/folder/file 
```

Daraufhin erscheint die Frage, wann der Download starten soll. Gültige Eingaben sind *"Jetzt"* , *"jetzt"* , *"now"* für sofort starten oder `134000` für 13:40 Uhr oder `121314` für 12:13 Uhr und 14 Sekunden. Ebenfalls möglich ist *next* . Damit startet er direkt nach den bisher gestarteten Downloads. So kann man z.B. einen Download sofort starten, und bei 5 weiteren *next* eingeben. Dadurch werden alle der Reihe nach abgearbeitet.

## Links[¶](#Links)

* [Manpage](https://manpages.ubuntu.com/cgi-bin/search.py?lr=lang_en&q=wget) 🇬🇧

* [cURL](https://wiki.ubuntuusers.de/cURL/) - wenn wget nicht mehr ausreicht

* [HTTPie](https://wiki.ubuntuusers.de/HTTPie/) - Alternative zu cURL und wget

* [The Ultimate Wget Download Guide With 15 Awesome Examples](http://www.thegeekstuff.com/2009/09/the-ultimate-wget-download-guide-with-15-awesome-examples/) – The Geek Stuff 🇬🇧 - Blogbeitrag, 09/2009

* [Shell/Anwendungen](https://wiki.ubuntuusers.de/Shell/Anwendungen/) Übersichtsseite

