---
title: Pretty Good Privacy – Wikipedia
---

# Pretty Good Privacy – Wikipedia

url:: https://de.m.wikipedia.org/wiki/Pretty_Good_Privacy
up: [[sources]]

#source

# Pretty Good Privacy

Programm zur Verschlüsselung und zum Unterschreiben von Daten  
**Pretty Good Privacy** (**PGP** ; [englisch](https://de.m.wikipedia.org/wiki/Englische_Sprache "Englische Sprache") für „ziemlich gute Privatsphäre“) ist ein von [Phil Zimmermann](https://de.m.wikipedia.org/wiki/Phil_Zimmermann "Phil Zimmermann") entwickeltes [Programm](https://de.m.wikipedia.org/wiki/Computerprogramm "Computerprogramm") zur [Verschlüsselung](https://de.m.wikipedia.org/wiki/Verschl%C3%BCsselung "Verschlüsselung") und zum [Unterschreiben](https://de.m.wikipedia.org/wiki/Elektronische_Signatur "Elektronische Signatur") von Daten.
[](https://de.m.wikipedia.org/wiki/Datei:PGP_diagram_de.svg) Funktionsprinzip von PGP (Nur Verschlüsselung, ohne Signierung)

PGP benutzt ein sogenanntes [Public-Key-Verfahren](https://de.m.wikipedia.org/wiki/Asymmetrisches_Kryptosystem "Asymmetrisches Kryptosystem"), in dem es ein eindeutig zugeordnetes [Schlüsselpaar](https://de.m.wikipedia.org/wiki/Schl%C3%BCssel_(Kryptologie) "Schlüssel (Kryptologie)") gibt:

Genutzt wird ein [öffentlicher Schlüssel](https://de.m.wikipedia.org/wiki/Schl%C3%BCssel_(Kryptologie)#Schl%C3%BCssel_bei_asymmetrischen_Verfahren "Schlüssel (Kryptologie)"), mit dem jeder Daten für den Empfänger verschlüsseln und dessen Signaturen prüfen kann, und ein privater [geheimer Schlüssel](https://de.m.wikipedia.org/wiki/Schl%C3%BCssel_(Kryptologie) "Schlüssel (Kryptologie)"), den nur der Empfänger besitzt und der normalerweise durch ein [Passwort](https://de.m.wikipedia.org/wiki/Passwort "Passwort") geschützt ist. Nachrichten an einen Empfänger werden mit dessen öffentlichem Schlüssel verschlüsselt und können dann ausschließlich mittels seines privaten Schlüssels entschlüsselt werden. Diese Verfahren werden auch [asymmetrische Verfahren](https://de.m.wikipedia.org/wiki/Asymmetrisches_Kryptosystem "Asymmetrisches Kryptosystem") genannt, da Sender und Empfänger zwei unterschiedliche Schlüssel verwenden.

Die erste Version wurde im Jahr 1991 geschrieben und verwendete einen [RSA](https://de.m.wikipedia.org/wiki/RSA-Kryptosystem "RSA-Kryptosystem")-[Algorithmus](https://de.m.wikipedia.org/wiki/Algorithmus "Algorithmus") zur Verschlüsselung der Daten. Spätere Versionen benutzten den [Elgamal-Algorithmus](https://de.m.wikipedia.org/wiki/Elgamal-Verschl%C3%BCsselungsverfahren "Elgamal-Verschlüsselungsverfahren").

Bei PGP wird aber nicht die ganze Nachricht asymmetrisch verschlüsselt, denn dies wäre viel zu rechenintensiv und es wäre nicht praktikabel, dieselbe Nachricht an mehrere Empfänger zu schicken. Stattdessen wird die eigentliche Nachricht [symmetrisch](https://de.m.wikipedia.org/wiki/Symmetrisches_Kryptosystem "Symmetrisches Kryptosystem") und nur der verwendete Schlüssel asymmetrisch verschlüsselt ([Hybride Verschlüsselung](https://de.m.wikipedia.org/wiki/Hybride_Verschl%C3%BCsselung "Hybride Verschlüsselung")). Dazu wird jedes Mal ein symmetrischer Schlüssel (session key) zufällig erzeugt.

Dieser symmetrische Schlüssel wird dann z. B. per RSA- oder Elgamal-Kryptosystem mit dem öffentlichen Schlüssel des Empfängers verschlüsselt und der Nachricht hinzugefügt. Dadurch ist es möglich, eine Nachricht für mehrere Empfänger gleichzeitig zu verschlüsseln. Eine für mehrere Empfänger verschlüsselte Nachricht sieht dann folgendermaßen aus:

|-----------------------------------------------------------------------------------------------------------------------|
| asymmetrisch für Empfänger 1 verschlüsselter Schlüssel der Nachricht                                                  |
| <math xmlns="http://www.w3.org/1998/Math/MathML" alttext="{\displaystyle \vdots }"> ⋮ {\displaystyle \vdots } </math> |
| asymmetrisch für Empfänger *n* verschlüsselter Schlüssel der Nachricht                                                |
| symmetrisch verschlüsselte Nachricht                                                                                  |

PGP basiert dabei auf dem sogenannten [Web of Trust](https://de.m.wikipedia.org/wiki/Web_of_Trust "Web of Trust"), bei dem es keine zentrale Zertifizierungsinstanz gibt, sondern Vertrauen von den Benutzern selbst verwaltet wird.

Da PGP darauf ausgelegt ist, Nachrichten dauerhaft entschlüsseln zu können, wird, falls es einem Angreifer gelingt, einen privaten Schlüssel zu erlangen, die gesamte Kommunikationshistorie dieses Schlüssels [kompromittiert](https://de.m.wikipedia.org/wiki/Kompromittierung_(Kryptologie) "Kompromittierung (Kryptologie)"). Für [Instant Messaging](https://de.m.wikipedia.org/wiki/Instant_Messaging "Instant Messaging") wurde als Alternative zu PGP [Off-the-Record Messaging](https://de.m.wikipedia.org/wiki/Off-the-Record_Messaging "Off-the-Record Messaging") (OTR) entwickelt; dabei bleibt auch bei späterer Kompromittierung des privaten Schlüssels die verschlüsselte Kommunikation unlesbar für den Angreifer (allerdings auch für den legitimen Schlüsselbesitzer).
[Phil Zimmermann](https://de.m.wikipedia.org/wiki/Phil_Zimmermann "Phil Zimmermann") schrieb die erste Version 1991. Sein Ziel war es, dass alle Bürger und insbesondere [Bürgerbewegungen](https://de.m.wikipedia.org/wiki/B%C3%BCrgerbewegung "Bürgerbewegung") auch vor dem Zugriff durch Geheimdienste sicher verschlüsselte Nachrichten austauschen können (starke Verschlüsselung).^[[1]](#cite_note-singh-1)^

PGP durfte in seinen Anfangsjahren nicht lizenzfrei aus den USA exportiert werden, da es, ähnlich wie Waffen, unter das US-Exportgesetz fiel. Danach unterlagen Kryptosysteme mit Schlüsseln von mehr als 40 Bit Länge für die symmetrische Verschlüsselung besonderen Exportbestimmungen. Die ersten PGP-Versionen verwendeten den [IDEA](https://de.m.wikipedia.org/wiki/International_Data_Encryption_Algorithm "International Data Encryption Algorithm") mit 128 Bit Schlüssellänge. Ende der 1990er Jahre liberalisierten die USA diese Gesetze.^[[1]](#cite_note-singh-1)^

Um die [Exportbeschränkung](https://de.m.wikipedia.org/wiki/Exportbeschr%C3%A4nkung "Exportbeschränkung") zu umgehen, wurde der vollständige [Quellcode](https://de.m.wikipedia.org/wiki/Quelltext "Quelltext") 1995 in dem Buch „PGP Source Code and Internals“ von Phil Zimmermann veröffentlicht. Als Buch konnte die Software [legal](https://de.m.wikipedia.org/wiki/1._Zusatzartikel_zur_Verfassung_der_Vereinigten_Staaten "1. Zusatzartikel zur Verfassung der Vereinigten Staaten") aus den USA exportiert werden. Es wurde von über 60 Freiwilligen per Hand abgetippt. Aus dem abgetippten Programmcode wurde dann eine international verfügbare Version von PGP (PGPi) [kompiliert](https://de.m.wikipedia.org/wiki/Compiler "Compiler").^[[1]](#cite_note-singh-1)^

Die Firma *PGP Corporation* entstand aus einer Fusion vom Team um Phil Zimmermann mit ViaCrypt, an die Phil Zimmermann einige kommerzielle Rechte verkauft und die RSA direkt lizenziert hatte. Sie stellte bis Version 8 mit *PGP Freeware* ein eigenständiges Produkt für nichtkommerzielle Anwender bereit. Ab Version 9 gibt es stattdessen nur noch die Testversion von *PGP Desktop Professional 9* . Für 30 Tage kann sie uneingeschränkt benutzt werden. Nach Ablauf der Frist werden Funktionsumfang und Nutzungsrechte auf einen Umfang reduziert, der etwa dem ehemaligen *PGP Freeware* entspricht. Ver- und Entschlüsselung von E-Mails sind auch nach Ablauf der Testphase möglich, aber nur für nichtkommerzielle Zwecke zulässig.

PGP wurde 1997 von NAI ([McAfee](https://de.m.wikipedia.org/wiki/McAfee "McAfee")) aufgekauft und dort in die eigene Produktlinie integriert. Weil der Quelltext von PGP durch McAfee zeitweilig nicht offengelegt wurde und Features implementiert wurden, welche die automatische Verschlüsselung an einen weiteren Empfänger ermöglichten (Additional Decryption Key ADK), ist PGP in dieser Zeit stark in die Kritik geraten. 2002 hat McAfee die Marke PGP aufgegeben und an eine Gruppe ehemaliger Mitarbeiter von PGP rund um Phil Zimmermann verkauft. Diese neu gegründete PGP Corporation hat alle Rechte an PGP von McAfee zurückgekauft und von Beginn an alle Quelltexte wieder offengelegt.

Die neue PGP ist heute in vielen Ländern vertreten, darunter auch in Deutschland, wo die PGP Corporation im Jahre 2005 die deutsche Glück & Kanja Technology AG^[[2]](#cite_note-2)^ übernommen hat und damit die heute in Offenbach am Main sitzende PGP Deutschland AG gründete. Fünf Jahre später hat PGP dann auch die deutsche TC Trustcenter^[[3]](#cite_note-3)^ in Hamburg gekauft und ist damit auch als zertifiziertes Trustcenter für Zertifikate nach [deutschem Signaturgesetz](https://de.m.wikipedia.org/wiki/Signaturgesetz_(Deutschland) "Signaturgesetz (Deutschland)") (für *qualifizierte Signaturen*) am Markt vertreten.

Nicht zuletzt wegen der intransparenten Situation in der Zeit, in der PGP im Eigentum der McAfee war, wurde bis 1998 der [OpenPGP](https://de.m.wikipedia.org/wiki/OpenPGP "OpenPGP")-Standard entwickelt.^[[4]](#cite_note-4)^ Das unter der [GNU-GPL](https://de.m.wikipedia.org/wiki/GNU_General_Public_License "GNU General Public License") stehende Programm [GnuPG](https://de.m.wikipedia.org/wiki/GNU_Privacy_Guard "GNU Privacy Guard") war die erste Implementierung von OpenPGP und wurde als freie Alternative zu PGP entwickelt. Es gibt mittlerweile viele Erweiterungen des OpenPGP-Standards, die über den Funktionsumfang von PGP hinausgehen, so dass der reibungslose Austausch von Daten und Schlüsseln nicht immer garantiert ist.

Die PGP Corporation wurde im Juni 2010 von [Symantec](https://de.m.wikipedia.org/wiki/NortonLifeLock "NortonLifeLock") für 300 Millionen US-Dollar^[[5]](#cite_note-5)^ übernommen.^[[6]](#cite_note-6)^
Man kann mit PGP wahlweise eine Nachricht nur signieren, nur verschlüsseln oder sowohl signieren als auch verschlüsseln. Die Signatur dient dazu, die Echtheit der Nachricht zu garantieren, also dass sie vom behaupteten [Absender](https://de.m.wikipedia.org/wiki/Absender "Absender") ist ([Authentizität](https://de.m.wikipedia.org/wiki/Authentizit%C3%A4t#Informatik "Authentizität")) und nach der Signierung nicht verändert wurde ([Integrität](https://de.m.wikipedia.org/wiki/Integrit%C3%A4t_(Informationssicherheit) "Integrität (Informationssicherheit)")). In der Praxis wird man Nachrichten, wenn man sie verschlüsselt, zumeist auch signieren.

Um später sichergehen zu können, dass eine empfangene Nachricht nicht manipuliert oder ersetzt wurde ([Integrität](https://de.m.wikipedia.org/wiki/Integrit%C3%A4t_(Informationssicherheit) "Integrität (Informationssicherheit)")) und außerdem vom behaupteten Absender stammt ([Authentizität](https://de.m.wikipedia.org/wiki/Authentizit%C3%A4t#Informatik "Authentizität")), muss der Sender eine [digitale Signatur](https://de.m.wikipedia.org/wiki/Digitale_Signatur "Digitale Signatur") für die Nachricht (oder Teile davon) erzeugen. Dabei wird auf die Klartextnachricht eine [kryptologische Hashfunktion](https://de.m.wikipedia.org/wiki/Kryptologische_Hashfunktion "Kryptologische Hashfunktion") angewandt (früher oft [SHA-1](https://de.m.wikipedia.org/wiki/Secure_Hash_Algorithm "Secure Hash Algorithm"), inzwischen aber veraltet und durch u. a. SHA-256 ersetzt). Dadurch entsteht ein [Message Digest](https://de.m.wikipedia.org/wiki/Message_Digest "Message Digest") (eindeutiger Fingerprint) der Nachricht, der wesentlich kürzer ist als die Nachricht selbst, was die Generierung der digitalen Signatur vereinfacht. Anschließend wird aus dem Message Digest unter Verwendung des privaten Schlüssels des Senders eine Signatur erzeugt.

Als zweiten Schritt (oder ersten, falls nicht signiert werden soll) kann der Sender nun die Verschlüsselung der Nachricht vornehmen. Hierbei werden die Klartextnachricht und die digitale Signatur aus dem ersten Schritt zu einem Datensatz zusammengefasst und komprimiert, zur Reduktion der Größe und Erschwerung der [Kryptoanalyse](https://de.m.wikipedia.org/wiki/Kryptoanalyse "Kryptoanalyse"). Diese komprimierten Daten werden nun mit dem zufällig generierten Session Key K~M~ symmetrisch zum [Geheimtext](https://de.m.wikipedia.org/wiki/Geheimtext "Geheimtext") verschlüsselt. Da es sich um einen zufällig erstellten Einmalschlüssel handelt, muss dieser dem Empfänger mitgeteilt werden. Dazu wird der Schlüssel K~M~ mit dem öffentlichen Schlüssel des Empfängers asymmetrisch verschlüsselt und der verschlüsselten Nachricht vorangestellt. Eine verschlüsselte Nachricht ist deshalb immer nur so sicher wie das schwächere Element aus dem asymmetrischen Schlüssel, für den verschlüsselt wird, und dem Session Key. Zum Schluss müssen die Bytes des Geheimtextes und des verschlüsselten Schlüssels noch E-Mail-freundlich gemacht werden. Dazu werden diese (genauso wie andere Binärdaten im E-Mail-Versand) mittels einer [Base64](https://de.m.wikipedia.org/wiki/Base64 "Base64")-Variante (Radix-64) in bestimmte druckbare [ASCII](https://de.m.wikipedia.org/wiki/American_Standard_Code_for_Information_Interchange "American Standard Code for Information Interchange")-Zeichen codiert. Nun kann die PGP-Nachricht an den Empfänger gesendet werden. Neben der Verwendung für E-Mails kann PGP auch für andere Nachrichtenkanäle verwendet werden, indem Dateien oder Text (dafür gibt es wegen der zwischen den Betriebssystemen uneinheitlichen Zeilenende-Codierung ein gesondertes Signaturverfahren) signiert und/oder verschlüsselt werden.  

#### Aussehen einer verschlüsselten Nachricht

[Bearbeiten](https://de.m.wikipedia.org/w/index.php?title=Pretty_Good_Privacy&action=edit&section=6 "Abschnitt bearbeiten: Aussehen einer verschlüsselten Nachricht")

Verschlüsselt man den Text

```
http://de.wikipedia.org/wiki/Pretty_Good_Privacy
```

dann sieht die codierte Nachricht so aus (im [Inline-PGP-Format](https://de.m.wikipedia.org/wiki/PGP/INLINE "PGP/INLINE"); bei einer [PGP/MIME](https://de.m.wikipedia.org/wiki/PGP/MIME "PGP/MIME")-E-Mail sähe es etwas anders aus):

```
-----BEGIN PGP MESSAGE-----
Version: GnuPG v2.0.16 (GNU/Linux)

hQEMA1PUVhZb8UnsAQf+KS9PNvkWYFONnoStveMc4KwvGT7WlRFv/ZACvdyFsKDO
icurhL57uh56KCof1m5drfftwjDQWgNyMy0cixqV/2WzeQgjZILE0Z1FDg7cgAbs
UZvy2hmaJf0dhHEUziALotfUMhoSeHeObxmomzb7vovJv5tWDtQ9W+p2tbQ4tiin
LAsJtwQhEVPNltootBteC0dTgOdISe6kfqUSoN3A22SiSUihmjxMPiiO6iZB8gBS
hhfiSPa4khNwODncRe2BjqW+YQHf7L6CfLjx2S1BCSr+KWLmUnVdWSUonhHPF9mI
E/q7t2uoBWg0iQgCjQubgYeqSUYN/xWpqAUX9O71zdKUAbVjjLVT0qTjNLLvms2H
s4BDzHEqKeuGuMAWFzyfuW+VNofTxtcHhzrdjPuYi7sRL3YNUvqUpcGeKGyTApW2
k/fd7U32av7Pq63NoKK2g3RFcyBUiSdNlNhW8TYS1NdMSMXNw1R9dWVgFmsLj2vs
Rv89ufRiPbNLDXcx7CkRrTf13q0miy1850d6k5nt8qUFrnh4xQ==
=z6Xk
-----END PGP MESSAGE-----
```

Zu Beginn muss der Empfänger die vorliegenden ASCII-Zeichen mit [Base64](https://de.m.wikipedia.org/wiki/Base64 "Base64") wieder decodieren, um an den Geheimtext und den verschlüsselten Session Key zu gelangen. Jetzt kann der Session Key durch den privaten Schlüssel des Empfängers entschlüsselt werden und damit gleich anschließend der Geheimtext wieder in die komprimierte Kombination aus Klartextnachricht und digitaler Signatur entschlüsselt werden. Um schließlich an die Nachricht zu gelangen, muss diese nur noch dekomprimiert werden.

Allerdings sollen auch noch die Authentizität des Senders und die Integrität der Nachricht sichergestellt werden. Dazu wird einerseits auf den Klartext die gleiche Hash-Funktion wie beim Sender angewandt, und andererseits die digitale Signatur mit dem öffentlichen Schlüssel des Senders dechiffriert. Wurde zum Erstellen der Signatur tatsächlich der zugehörige private Schlüssel verwendet, so stimmen die beiden Klartexte des Message Digests überein; und man kann davon ausgehen, dass die Nachricht nicht verändert wurde. Davon, dass sie auch von einem bestimmten Sender stammt, kann man ausgehen, wenn der signierende Schlüssel einem Absender sicher zugeordnet werden kann, was unabhängig von der Bearbeitung einzelner Nachrichten ist.
Ein möglicher Angriff ergibt sich aus dem Vorgehen, Nachrichten zuerst zu signieren und dann zu verschlüsseln.^[[7]](#cite_note-7)^ Der Empfänger kann die signierte Nachricht nach dem Entschlüsseln mit gefälschtem Absender an eine dritte Person weiterleiten. Falls der Adressat in der Nachricht nicht namentlich genannt ist, kann die gültige Signatur den Eindruck erwecken, sie sei direkt vom ursprünglichen Sender an diese dritte Person geschickt worden.

Daneben wurde kritisiert, dass die öffentlichen Schlüssel häufig auf Servern gespeichert werden, auf die jede Person Lese- und Schreibzugang hat. So ist es dazu gekommen, dass dort falsche Schlüssel abgelegt wurden. Ein Ansatz, um dies zu verhindern, ist die Spezifikation DANE/OPENPGPKEY von der [Internet Engineering Task Force](https://de.m.wikipedia.org/wiki/Internet_Engineering_Task_Force "Internet Engineering Task Force").^[[8]](#cite_note-8)^^[[9]](#cite_note-9)^
* [GNU Privacy Guard](https://de.m.wikipedia.org/wiki/GNU_Privacy_Guard "GNU Privacy Guard")
* [OpenPGP](https://de.m.wikipedia.org/wiki/OpenPGP "OpenPGP")
* [Hal Finney](https://de.m.wikipedia.org/wiki/Hal_Finney "Hal Finney")
* [PGP-Produktwebsite von Symantec](https://www.symantec.com/encryption) – Aktuelle, kommerzielle Versionen von PGP (englisch). Freie binäre Versionen werden nicht mehr zum Download angeboten
* [Offizielle GnuPG Website](https://www.gnupg.org/index.de.html)
* [The International PGP Home Page](http://www.pgpi.org/) (englisch)
* [Liste öffentlicher PGP-Schlüsselserver](https://www.rossde.com/PGP/pgp_keyserv.html) (englisch)
1. ↑ ^[a](#cite_ref-singh_1-0)^ ^[b](#cite_ref-singh_1-1)^ ^[c](#cite_ref-singh_1-2)^ Simon Singh: [Geheime Botschaften](https://de.m.wikipedia.org/wiki/Geheime_Botschaften "Geheime Botschaften"). [ISBN 3-423-33071-6](https://de.m.wikipedia.org/wiki/Spezial:ISBN-Suche/3423330716).
2. [↑](#cite_ref-2) Daniel Bachfeld: [*PGP kauft Verschlüsselungsspezialisten Glück & Kanja.*](https://www.heise.de/security/meldung/PGP-kauft-Verschluesselungsspezialisten-Glueck-Kanja-141072.html) In: *heise Security.* 7. März 2005, abgerufen am 30. August 2012.
3. [↑](#cite_ref-3) Christian Kirsch: [*PGP kauft deutsches Trustcenter.*](https://www.heise.de/newsticker/meldung/PGP-kauft-deutsches-Trustcenter-920111.html) In: *heise online.* 2. Februar 2010, abgerufen am 30. August 2012.
4. [↑](#cite_ref-4) *[RFC](https://de.m.wikipedia.org/wiki/Request_for_Comments "Request for Comments"): [2440](https://datatracker.ietf.org/doc/html/rfc2440 "rfc:2440")* – *OpenPGP Message Format* . November 1998 (englisch). *[RFC](https://de.m.wikipedia.org/wiki/Request_for_Comments "Request for Comments"): [4880](https://datatracker.ietf.org/doc/html/rfc4880 "rfc:4880")* – *OpenPGP Message Format*. November 2007 (überarbeitete Fassung, englisch).
5. [↑](#cite_ref-5) Peter-Michael Ziegler: [*Symantec kauft Verschlüsselungsspezialisten PGP und GuardianEdge.*](https://www.heise.de/newsticker/meldung/Symantec-kauft-Verschluesselungsspezialisten-PGP-und-GuardianEdge-990166.html) In: *heise online.* 29. April 2010, abgerufen am 30. August 2012.
6. [↑](#cite_ref-6) [*PGP.*](http://www.symantec.com/about/profile/development/acquisitions/detail.jsp?id=pgp) In: *About Symantec.* Symantec Corporation, abgerufen am 30. August 2012 (englisch).
7. [↑](#cite_ref-7) Donald T. Davis: Defective Sign & Encrypt in S/MIME, PKCS#7, MOSS, PEM, PGP, and XML. In: 2001 USENIX Annual Technical Conference. 2001 ([std.com](http://world.std.com/~dtd/sign_encrypt/sign_encrypt7.html)).
8. [↑](#cite_ref-8) Paul Wouters, [Red Hat](https://de.m.wikipedia.org/wiki/Red_Hat "Red Hat"): [*Using DANE to Associate OpenPGP public keys with email addresses.*](https://datatracker.ietf.org/doc/draft-ietf-dane-openpgpkey/) Network Working Group, [Internet Engineering Task Force](https://de.m.wikipedia.org/wiki/Internet_Engineering_Task_Force "Internet Engineering Task Force"), 19. Oktober 2015, abgerufen am 4. Dezember 2015 (englisch).
9. [↑](#cite_ref-9) Dusan Zivadinovic: [*Mail-Verschlüsselung: Mail.de bringt automatisierte PGP-Schlüsselverwaltung.*](https://heise.de/-2571867) In: *[heise online](https://de.m.wikipedia.org/wiki/Heise_online "Heise online").* 10. März 2015, abgerufen am 22. Januar 2017.
