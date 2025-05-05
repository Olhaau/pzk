---
title: AIDE - LinuxWiki.org - Linux Wiki und Freie Software
---

# AIDE - LinuxWiki.org - Linux Wiki und Freie Software

url:: https://linuxwiki.de/AIDE
up: [[sources]]

#source

AIDE (Advanced Intrusion Detection Environment) ist ein hostbasiertes [IntrusionDetectionSystem](https://linuxwiki.de/IntrusionDetectionSystem).

Es überprüft die Integrität der Dateien über Checksummen und meldet Änderungen.

Homepage: <http://www.cs.tut.fi/~rammer/aide.html>

Lizenz: [GPL](https://linuxwiki.de/GPL)

# Tipps & Tricks

# Mini-Howto

### AIDE konfigurieren

Als erstes die Pfade und Dateien die AIDE überprüfen soll in */etc/aide.conf* anpassen. Erläuterungen der übrigen Parameter bekommt man mit man aide.conf

### Datenbank initialisieren

Beim ersten Start muss die Datenbank mit den Prüfsummen erstellt werden.

```
# aide --init
```

Die Datenbank wurde in */var/lib/aide/aide.db.new* gespeichert. */var/lib/aide/aide.db.new* muss jetzt in */var/lib/aide/aide.db* umbenannt werden, sofern das nicht in der Konfiguration geändert wurde.

```
# mv /var/lib/aide/aide.db.new /var/lib/aide/aide.db
```

### Datenbank und AIDE sichern

Nun sollte man */usr/bin/aide* , */etc/aide.conf* und */var/lib/aide.db* auf einem sicheren Medium zb. CD-RW speichern. Will man AIDE auch von CD starten kann man den Pfad zur *aide.db* auf *./aide.db* in der Konfigurationsdatei ändern.

### Integrität prüfen

```
# aide --check
```

überprüft AIDE die Dateien auf System mit der Datenbank und meldet Änderungen auf die Konsole.

Mit -c kann man den Pfad zur *aide.conf* angeben um zb die Konfiguration von CD zu lesen.

```
# cd /cdrom
# aide -c ./aide.conf --check
```

## Änderungen in die Datenbank übernehmen

```
# aide --update
```

In */var/lib/aide/aide.db.new* wurde die aktualisierte Datenbank gespeichert. Die muss wieder umbenannt und gesichert werden.

```
# mv /var/lib/aide/aide.db.new /var/lib/aide/aide.db
```

