---
title: cURL › Wiki › ubuntuusers.de
---

# cURL › Wiki › ubuntuusers.de

url:: https://wiki.ubuntuusers.de/cURL/
up: [[sources]]

#source

### Dieser Artikel wurde für die folgenden Ubuntu-Versionen getestet:

* [Ubuntu 24.04](https://wiki.ubuntuusers.de/Noble_Numbat/) Noble Numbat

---

Du möchtest den Artikel für eine weitere Ubuntu-Version testen? Mitarbeit im Wiki ist immer willkommen! Dazu sind die Hinweise [zum Testen von Artikeln](https://wiki.ubuntuusers.de/Wiki/FAQ_-_h%C3%A4ufig_gestellte_Fragen/#Wikiartikel-testen) zu beachten.  

### Zum Verständnis dieses Artikels sind folgende Seiten hilfreich:

[![Wiki/Icons/terminal.png](https://media-cdn.ubuntu-de.org/wiki/thumbnails/8/87/873df3d4149ab35efbc7b1f3654ad92a9b4e579ai48x.png)](https://media-cdn.ubuntu-de.org/wiki/attachments/10/28/terminal.png)

[cURL](http://curl.haxx.se/) 🇬🇧 ist ein Programm, das es ermöglicht, ohne Benutzerinteraktion Dateien von oder zu einem Server zu übertragen. Neben HTTP unterstützt das Programm noch eine Vielzahl von weiteren Netzwerkprotokollen wie FTP, FTPS, HTTPS, GOPHER, TELNET, DICT, FILE und LDAP. Die Steuerung erfolgt über Kommandozeilenparameter, die beim Programmaufruf angegeben werden.

cURL nutzt für alle den Datentransfer betreffenden Funktionen die Bibliothek libcurl. Es wird häufig für die Arbeit mit [REST](https://de.wikipedia.org/wiki/Representational_State_Transfer)-ful Services verwendet, z.B. zur Entwicklung oder zum Debugging solcher Dienste.

## Installation[¶](#Installation)

Das Programm kann über das gleichnamige Paket installiert werden ^[[1]](#source-1)^:

* **curl**

Befehl zum Installieren der Pakete:  

```
sudo apt-get install curl 
```

## Verwendung[¶](#Verwendung)

Aus einem Terminal heraus ^[[2]](#source-2)^ kann man cURL folgendermaßen aufrufen:

### Optionen[¶](#Optionen)

|-----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Optionen von cURL                                                                                                                                                                                                                                                                                                    ||
| Log-Optionen                                                                                                                                                                                                                                                                                                         ||
| `-v`, `--verbose`                                         | Gibt umfangreiche Informationen zur Client-Server-Kommunikation aus.                                                                                                                                                                                      |
| `--trace DATEI`, `--trace-ascii DATEI`                    | Liefert noch detailliertere Informationen als `--verbose` und schreibt diese in die angegebene Datei.                                                                                                                                                     |
| Upload-Optionen                                                                                                                                                                                                                                                                                                      ||
| `-T DATEI URL`, `--upload-file DATEI URL`                 | Überträgt die Datei zum angegebenen Ziel.                                                                                                                                                                                                                 |
| Download-Optionen, Arbeit mit HTTP                                                                                                                                                                                                                                                                                   ||
| `-C`, `--continue-at OFFSET`                              | Fortsetzung bzw. Wiederaufnahme einer vorangegangenen Dateiübertragung an der angegebenen Stelle. `-C -` bewirkt, dass CURL automatisch anhand der gegebenen output/input-Dateien herausfindet, wo und wie die Übertragung wiederaufgenommen werden kann. |
| `-L`, `--location`                                        | Folgt Weiterleitungen (nur HTTP/HTTPS)                                                                                                                                                                                                                    |
| `-o DATEI`, `--output DATEI`                              | Schreibt den Inhalt der heruntergeladenen Datei in die angegebene DATEI anstatt auf die [Standardausgabe](https://wiki.ubuntuusers.de/Shell/Umleitungen/#Standardausgabe)                                                                                 |
| ` -r BEREICH`, `--range BEREICH`                          | Ruft nur die Bytes innerhalb des angegebenen BEREICHES ab. Wenn der Server dies nicht unterstützt, wird die gesamte Datei heruntergeladen.                                                                                                                |
| `-O`, `--remote-name`                                     | Schreibt den Inhalt der heruntergeladenen Datei in eine gleichnamige Datei.                                                                                                                                                                               |
| `-X COMMAND URL`, `--request COMMAND URL`                 | Führt den angegebenen HTTP-Command auf die angegebene URL aus.                                                                                                                                                                                            |
| Proxy-Optionen                                                                                                                                                                                                                                                                                                       ||
| `-x PROTOCOL://HOST:PORT`, `--proxy PROTOCOL://HOST:PORT` | Verwendet den angegebenen Proxy. Das Protokoll und der Port sind optional.                                                                                                                                                                                |
| `-U USER:PASSWORD`, `--proxy-user USER:PASSWORD`          | Verwendet den angegebenen Proxy User. Das Passwort ist optional.                                                                                                                                                                                          |
| weitere-Optionen                                                                                                                                                                                                                                                                                                     ||
| `-u USER:PASSWORD`                                        | übergibt Zugangsdaten                                                                                                                                                                                                                                     |

Alle Optionen - auch zu anderen Netzwerkprotokollen - findet man in der [manpage](https://wiki.ubuntuusers.de/man/) von cURL.

### Beispiele[¶](#Beispiele)

Weitere Beispiele findet man im [cURL Handbuch](https://curl.haxx.se/book.html) 🇬🇧.

#### Dateidownload[¶](#Dateidownload)

Mit cURL lassen sich auch Dateidownloads durchführen. Dies funktioniert sowohl mit HTTP- als auch mit FTP-Downloads. Der folgende Befehl holt ein Ubuntu-Image von einem FTP-Server und speichert es dann im aktuellen Verzeichnis:  

```
curl -O https://ftp.halifax.rwth-aachen.de/ubuntu-releases/24.04/ubuntu-24.04.1-desktop-amd64.iso 
```

Man erhält während des Downloads die folgende Ausgabe, die unter anderem Angeben zur Übertragung und Übertragungszeit enthält:

```
 % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  3 5915M    3  224M    0     0  5879k      0  0:17:10  0:00:39  0:16:31 5933k
```

Möchte man die Datei unter einem anderen Namen oder in einem anderen Verzeichnis speichern, dann ersetzt man die Option `-O` einfach durch die Option `--output /PFAD/ZUM/VERZEICHNIS/DATEINAME.XXX`. Gibt man keinen Pfad sondern nur einen Dateinamen an, so wird die Datei im aktuellen Verzeichnis gespeichert.

Würde der Download des vorgenannten Ubuntu-Images unter- bzw. abgebrochen werden, so könnte man ihn mit dem folgenden Befehl fortsetzen bzw. wiederaufnehmen:  

```
curl -C - -O https://ftp.halifax.rwth-aachen.de/ubuntu-releases/24.04/ubuntu-24.04.1-desktop-amd64.iso 
```

Durch die Option `-C -` wird die Übertragung automatisch an der abgebrochenen Stelle wiederaufgenommen werden.

Wird die Option `-O` oder `--output` weggelassen, dann wird der Inhalt der heruntergeladenen Datei auf die Standardausgabe (also in der Regel den Bildschirm) ausgegeben. Dies kann z.B. zum Betrachten kurzer Skripte oder Readme-Dateien sinnvoll sein. So würde der Befehl  

```
curl http://www.example.com/foo/readme.txt 
```

die Datei **readme.txt** auf den Bildschirm ausgeben. Würde dabei zusätzlich noch die Option `-L` gesetzt werden, so würde etwaigen Weiterleitungen gefolgt werden.

#### Dateiupload[¶](#Dateiupload)

Mit cURL lassen sich auch Dateiuploads durchführen. Dies funktioniert sowohl mit HTTP- als auch mit FTP-Servern. Der folgende Befehl schiebt eine Datei auf einen FTP-Server in das Verzeichnis foo:  

```
curl -T datei.name ftp://www.example.com/foo/ 
```

Wenn man Daten zur Authentifizierung mitgeben muss, nutzt man die Option `-u`:  

```
curl -T datei.name -u username:password ftp://www.example.com/foo/ 
```

#### ReSTful Services[¶](#ReSTful-Services)

Im ersten Beispiel wird ein HTTP-GET auf die Ressource `https://dummyjson.com/products/1` ausgeführt, die Ausgabe der zurückgelieferten Daten erfolgt nach stdout, also in der Regel den Bildschirm:  

```
curl -X GET https://dummyjson.com/products/1 
```

Im zweiten Beispiel wird ein POST-Request mit JSON Daten an einen Dummyservice gesendet:  

```
curl -H 'Content-Type: application/json' -d '{ "title":"foo","body":"bar", "id": 1}' -X POST http://httpbin.org/post 
```

Die Option `-H` definiert den Content-Type, also in welcher Form die Daten gesendet werden, und die Option `-d` enthält die eigentlichen Daten.

#### Wetterbericht abrufen[¶](#Wetterbericht-abrufen)

Die Webseite [wttr.in](http://wttr.in) 🇬🇧 ist so konzipiert, dass man mit cURL den dort angezeigten Wetterbericht als formatierten Datensatz für eine Ausgabe im [Terminal](https://wiki.ubuntuusers.de/Terminal/) herunterladen kann:

Sofern die automatische Erkennung des Standorts nicht funktioniert, kann der Ort für den Wetterbericht an die URL angehängt werden:

Inzwischen lassen sich auch die Mondphasen anzeigen:

## Links[¶](#Links)

### Intern[¶](#Intern)

* [HTTPie](https://wiki.ubuntuusers.de/HTTPie/) - Alternative zu cURL zum Erstellen von HTTP-Anfragen

* [Shell/Anwendungen](https://wiki.ubuntuusers.de/Shell/Anwendungen/) Übersichtsseite

* [FritzBox/Skripte](https://wiki.ubuntuusers.de/FritzBox/Skripte/) - Daten von der Fritzbox mittels cURL auslesen

*

  ### Extern[¶](#Extern)

* [15 Practical Linux cURL Command Examples (cURL Download Examples)](http://www.thegeekstuff.com/2012/04/curl-examples/) 🇬🇧 - Blogbeitrag, 04/2012

* [A Few cURL Tips for Daily Use](http://www.laktek.com/2012/03/12/curl-tips-for-daily-use/) 🇬🇧 - Blogbeitrag, 03/2012

