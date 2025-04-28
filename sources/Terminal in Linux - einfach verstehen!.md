---
title: Terminal in Linux - einfach verstehen!
keywords: [source]
---

# Terminal in Linux - einfach verstehen!

url:: https://it-learner.de/terminal-in-linux/
up: [[sources]]

Dieser Artikel ist der zweite von 10 Teilen zum Thema [**Linux Terminal in 10 Teilen**.](https://it-learner.de/das-linux-terminal-in-10-teilen/)Hierbei geht es um die Thematiken: Ausgeben der Shell, Bash Hilfe und History sowie  Aliases.

**[» Zur Übersicht der Artikelserie «](https://it-learner.de/?p=3024&preview=true)**

## Überblick Linux Terminal

Das wohl wichtigste Instrument im Linux System ist das Terminal mit der entsprechenden Shell. Eine Shell ist eigentlich nichts anderes als ein Programm, das als Kommandozeileninterpreter für den Dialog und die Interaktion mit dem Benutzer zuständig ist. Im Linux System ist das meist die **bash** . Die Shell stellt an einem Terminal eine Kommandozeile zur Verfügung. Sämtliche Konfigurationen kann man anhand des Linux Terminals vornehmen. Sei es nur Daten zu erstellen oder **[komplexe Scripte](https://it-learner.de/linux-bash-script-erstellen/)** zu schreiben.

Das Terminal wird über Start oder Ausführen geöffnet. Im Anschluss daran erhält man die folgende Ausgabe.
[![Das Linux Terminal](https://it-learner.de/wp-content/uploads/2015/05/Linux-Terminal-1.png "Terminal in Linux - Hilfe und History 1")](https://it-learner.de/wp-content/uploads/2015/05/Linux-Terminal-1.png) Das Linux Terminal

Klären wir zunächst die Bedeutung der Ausgabe: Angemeldet ist der Benutzer **test** am PC **Vorlage** **(test@Vorlage)** . Aktuell befindet man sich im **home (/home/test)** [**Verzeichnis**](https://it-learner.de/terminal-in-linux-verzeichnisstruktur-teil-4/) des Benutzers test (~) und der Benutzer hat Standardrechte (**$** ). Hätte der Benutzer Admin Recht, so würde das „**#**“ Zeichen angezeigt. Wie man sieht kann man hier schon eine ganz Menge hier ablesen.

## Help und Eigenschaften der Bash

Hilfe erhält man in der Shell immer wenn man **help,** oder **help Befehl** eintippt. Allerdings bietet das [Linux System auch ein integriertes Handbuch](https://it-learner.de/linux-das-umfassende-handbuch/) (Manual) für die verschiedenen Befehle. Mit dem Befehl man Befehl, kann man sich zu jedem Befehl den Manual Auszug ansehen.  Die Bash bietet einige wichtige Eigenschaften u.a. gehören hierzu folgenden:

* **Eingabekomplettierung mit der Tabulator Taste**
* **History: Anzeige der zuletzt eingegebenen Befehle**
* **Ein- und Ausgabeumleitungen bei Programmen (Pipe)**
* **Umfangreiche Scriptesprache**

## Bash History

Die Bash speichert die letzten eingegebenen Kommandos  mit deren Parametern in einer History Datei. **(~/.bash_history)** Mit der Pfeiltaste nach oben kann man sich die letzten eingetippten Kommandos anzeigen lassen. Dies ist vor allem bei sehr langen Eingaben ziemlich nützlich. Die Anzahl der Kommandos, welche gespeichert werden wird in der **HISTSIZE** Variable gespeichert. Diese Variable kann man sich folgendermaßen ausgeben lassen:

```
echo Variable HISTSIZE=$HISTSIZE
```

Löschen kann man diese Liste mit dem Parameter **`-c`**. Wichtig ist das vor allem für den root und für den Fall, dass man Kommandos mit Passwörter in der Shell aufruft. Die Passwörter werden dann nämlich ebenfalls in der History gespeichert.

## Alias-Kommandos

Alias sind im Grunde genommen nur Abkürzungen für Befehle. Gesetzt werden diese mit **`Alias`** und gelöscht mit **`unalias`.**
[![Linux Alias in der Bash](https://it-learner.de/wp-content/uploads/2015/05/Linux-Bash-Alias.png "Terminal in Linux - Hilfe und History 2")](https://it-learner.de/wp-content/uploads/2015/05/Linux-Bash-Alias.png) Linux Alias in der Bash

Wie man der obigen Ausgabe entnehmen kann, so ist z.B. `la` eine Abkürzung für den Befehl **`ls -A`.**

## Fazit

Das Linux Terminal, ist für den IT-Systemadministrator ein unverzichtbares Werkzeug und prinzipiell auch nicht sehr schwer zu verstehen. Man muss sich nur trauen damit zu arbeiten. Leider gibt es nach wie vor viele Hemmschwellen.

**[»](https://it-learner.de/?p=3024&preview=true) [Teil 1](https://it-learner.de/warum-sollte-man-die-linux-shell-verwenden/) [«](https://it-learner.de/?p=3024&preview=true)          [» Zur Übersicht der Artikelserie «](https://it-learner.de/?p=3024&preview=true)          [»](https://it-learner.de/?p=3024&preview=true) [Teil 3](https://it-learner.de/terminal-in-linux-teil-2/) [«](https://it-learner.de/?p=3024&preview=true)**  
![ab137e46b3464c3bbd0d1ef3e3f9b740](https://vg05.met.vgwort.de/na/ab137e46b3464c3bbd0d1ef3e3f9b740 "Terminal in Linux - Hilfe und History 3")  
![Der Blogbetreiber und Autor: Markus Elsberger](http://it-learner.de/wp-content/uploads/2012/11/Autor-Markus-767x1024.png)  

## Über den Autor

Mein Name ist Markus Elsberger und ich beschäftige mich mit der Administration von Windows und Linux-Systemen sowie mit diversen Themen bzgl. Netzwerktechnik. In meiner Lehrtätigkeit erstelle ich verschiedene Szenarien und teste auch verschiedene Konfigurationen, welche ich in diesem Blog festhalten möchte. -> **[Gern kannst du mich mit einem Kaffee unterstützen!](https://ko-fi.com/itlearnerde)**  

---

**Kostenlose Befehlsreferenzen und E-Books**   
*(Netzwerkanalyse, Benutzerverwaltung, Festplattenkonfiguration, u. v. m.)*

---

