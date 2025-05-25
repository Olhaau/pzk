---
title: WebSocket – Wikipedia
---

# WebSocket – Wikipedia

url:: https://de.m.wikipedia.org/wiki/WebSocket
up: [[sources]]

#source

# WebSocket

auf TCP basierendes Netzwerkprotokoll  
Das **WebSocket-Protokoll** ist ein auf [TCP](https://de.m.wikipedia.org/wiki/Transmission_Control_Protocol "Transmission Control Protocol") basierendes [Netzwerkprotokoll](https://de.m.wikipedia.org/wiki/Netzwerkprotokoll "Netzwerkprotokoll"), das entworfen wurde, um eine [bidirektionale](https://de.m.wikipedia.org/wiki/Bidirektional "Bidirektional") Verbindung zwischen einem [Webbrowser](https://de.m.wikipedia.org/wiki/Webbrowser "Webbrowser") und einem WebSocket-Server bzw. einem [Webserver](https://de.m.wikipedia.org/wiki/Webserver "Webserver"), der auch WebSockets unterstützt, herzustellen.
Während bei einer reinen [HTTP](https://de.m.wikipedia.org/wiki/Hypertext_Transfer_Protocol "Hypertext Transfer Protocol")-Verbindung jede Aktion des Servers eine vorhergehende Anfrage des Clients erfordert, reicht es beim WebSocket-Protokoll, wenn der Client die Verbindung öffnet. Der Server kann dann diese offene Verbindung aktiv verwenden und neue Informationen an den Client ausliefern, ohne auf eine neue Verbindung des Clients zu warten. Eine vom Server initiierte Übertragung ist bei einer reinen HTTP-Verbindung nur durch verzögertes Antworten auf eine vom Client initiierte Anfrage möglich ([long Polling](https://de.m.wikipedia.org/wiki/Long_Polling "Long Polling")). Seit HTTP/2 kann der Server auch Push-Meldungen absetzen. Zudem entfallen bei WebSockets die durch den HTTP-Header verursachten zusätzlichen Daten, die bei jeder Anfrage einige hundert Bytes umfassen können. Technisch betrachtet startet bei WebSocket, wie bei HTTP, der Client eine Anfrage, mit dem Unterschied, dass nach der Übertragung der Daten zum Verbindungsaufbau die zugrundeliegende [TCP](https://de.m.wikipedia.org/wiki/Transmission_Control_Protocol "Transmission Control Protocol")-Verbindung bestehen bleibt und asynchrone Übertragungen in beide Richtungen ermöglicht.
Die WebSocket-Protokoll-Spezifikation definiert zwei neue [URI](https://de.m.wikipedia.org/wiki/Uniform_Resource_Identifier "Uniform Resource Identifier")-Schemata, *ws:* für unverschlüsselte, und *wss:* für verschlüsselte Verbindungen.
Zu Beginn jeder Verbindung führen Server und Client einen sogenannten Handshake durch. Dieser ähnelt dem HTTP-Header und ist vollständig abwärtskompatibel zu diesem, was die Nutzung des Standard-HTTP-Ports „80“ zugleich für normale HTTP-Kommunikation als auch für die Websocket-Nutzung ermöglicht. Der Handshake beinhaltet außerdem weitere Informationen (z. B. die verwendete Protokollversion).

Im Folgenden ist ein Beispielhandshake des siebzehnten Protokollentwurfs (*draft-ietf-hybi-thewebsocketprotocol-17* ) dargestellt und erläutert:^[[2]](#cite_note-2)^

Achtung: Ältere Protokollentwürfe können sehr stark von dieser Version des Protokolls abweichen, da es aufgrund von Sicherheitsbedenken umgestaltet wurde. Dies betrifft insbesondere die Entwürfe vor *draft-ietf-hybi-thewebsocketprotocol-04*.  

```
 GET /chat HTTP/1.1
 Host: server.example.com
 Upgrade: websocket
 Connection: Upgrade
 Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
 Origin: http://example.com
 Sec-WebSocket-Protocol: chat, superchat
 Sec-WebSocket-Version: 13
```

Wie auch im [HTTP](https://de.m.wikipedia.org/wiki/Hypertext_Transfer_Protocol "Hypertext Transfer Protocol") gibt der Client an, auf welche Ressource (hier: */chat* ) und auf welchen Host (hier: *server.example.com* ) er zugreifen möchte. Außerdem fordert der Client ein Upgrade auf das Websocket-Protokoll. Der zufällig generierte „Sec-WebSocket-Key“ dient zur Überprüfung, ob der Server die Anfrage tatsächlich gelesen und verstanden hat (*siehe Abschnitt [Antwort des Servers](#Antwort_des_Servers)*). Unter „Sec-WebSocket-Protocol“ hat der Client die Möglichkeit, auf das Websocket-Protokoll aufbauende Protokolle anzugeben, die die Clientanwendung unterstützt (hier: ein Chat-Protokoll). Selbsterklärend sollte unter „Sec-WebSocket-Version“ die verwendete Protokollversion angegeben werden.

Auf die obige Beispielanfrage könnte ein Websocket-Server beispielsweise wie folgt antworten:  

```
 HTTP/1.1 101 Switching Protocols
 Upgrade: websocket
 Connection: Upgrade
 Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
 Sec-WebSocket-Protocol: chat
```

Durch den [HTTP-Statuscode](https://de.m.wikipedia.org/wiki/HTTP-Statuscode "HTTP-Statuscode") 101 und die folgenden zwei Zeilen erklärt der Server, dass er mit dem Wechsel des Protokolls einverstanden ist. Von diesem Zeitpunkt an verwenden beide ein binäres Protokoll, das nicht mehr HTTP-kompatibel ist. Es ist in Pakete aufgeteilt, die als Binär oder Text, als Ping oder Pong oder als Ende der Verbindung markiert sind und die Länge des Inhalts angeben.^[[3]](#cite_note-3)^

Der zurückgesendete Schlüssel unter „Sec-WebSocket-Accept“ dient der Verifikation, dass der Server die Anfrage des Clients gelesen hat. Er wird wie folgt erstellt: An den oben erwähnten, [Base64](https://de.m.wikipedia.org/wiki/Base64 "Base64")-kodierten String, den der Client sendet („Sec-WebSocket-Key“) wird der [Globally Unique Identifier](https://de.m.wikipedia.org/wiki/Globally_Unique_Identifier "Globally Unique Identifier") `258EAFA5-E914-47DA-95CA-C5AB0DC85B11` angehängt. Daraufhin wird ein [SHA1-Hash](https://de.m.wikipedia.org/wiki/Secure_Hash_Algorithm "Secure Hash Algorithm") des entstandenen Schlüssels erstellt und Base64-kodiert. Anzumerken ist hierbei, dass der ursprünglich empfangene Schlüssel zwar Base64-kodiert ist, jedoch zu keinem Zeitpunkt dekodiert wird.

In diesem Beispiel gibt der Server zusätzlich an, dass er das angeforderte Protokoll „chat“ kennt („Sec-WebSocket-Protocol“).
WebSocket Client in [JavaScript](https://de.m.wikipedia.org/wiki/JavaScript "JavaScript")  

```
var socket = new WebSocket(urlToWebsocketServer);

// callback-Funktion wird gerufen, wenn die Verbindung erfolgreich aufgebaut werden konnte
socket.onopen = function () {
    console.log("Verbindung wurde erfolgreich aufgebaut");
};

// callback-Funktion wird gerufen, wenn eine neue Websocket-Nachricht eintrifft
socket.onmessage = function (messageEvent) {
    console.log(messageEvent.data);
};

// callback-Funktion wird gerufen, wenn ein Fehler auftritt
socket.onerror = function (errorEvent) {
    console.log("Error! Die Verbindung wurde unerwartet geschlossen");
};

socket.onclose = function (closeEvent) {
    console.log('Die Verbindung wurde geschlossen --- Code: ' + closeEvent.code + ' --- Grund: ' + closeEvent.reason);
};
```

Die Spezifikation erlaubt die Erweiterung des Websocket-Protokolls durch definierte „Extensions“. Diese werden zwischen Client und Server ausgehandelt. Beispiele:

* [WebSocket Per-frame Compression](https://tools.ietf.org/html/draft-tyoshino-hybi-websocket-perframe-deflate-06)
* [A Multiplexing Extension for WebSockets](https://tools.ietf.org/html/draft-tamplin-hybi-google-mux-03)
* [The MessageBroker WebSocket Subprotocol](https://tools.ietf.org/html/draft-hapner-hybi-messagebroker-subprotocol-01)
* Auf [WebKit](https://de.m.wikipedia.org/wiki/WebKit "WebKit") basierende Browser (z. B. [Google Chrome](https://de.m.wikipedia.org/wiki/Google_Chrome "Google Chrome"), [Apple Safari](https://de.m.wikipedia.org/wiki/Apple_Safari "Apple Safari"))
* [Opera](https://de.m.wikipedia.org/wiki/Opera_(Browser) "Opera (Browser)"), ab Version 10.70
* [Microsoft Edge](https://de.m.wikipedia.org/wiki/Microsoft_Edge "Microsoft Edge"), ab erster Version
* [Mozilla Firefox](https://de.m.wikipedia.org/wiki/Mozilla_Firefox "Mozilla Firefox"), ab Version 4^[[4]](#cite_note-4)^
* [Internet Explorer](https://de.m.wikipedia.org/wiki/Internet_Explorer "Internet Explorer"), ab Version 10.0
* Peter Leo Gorski, Luigi Lo Iacono, Hoai Viet Nguyen: *WebSockets – Moderne HTML5-Echtzeitanwendungen entwickeln* , Hanser, München und Wien 2015, [ISBN 978-3-446-44371-6](https://de.m.wikipedia.org/wiki/Spezial:ISBN-Suche/9783446443716)
* *[RFC](https://de.m.wikipedia.org/wiki/Request_for_Comments "Request for Comments"): [6455](https://datatracker.ietf.org/doc/html/rfc6455 "rfc:6455")* – *The WebSocket Protocol Version 13*. 2011 (englisch).
* [The WebSocket API](https://websockets.spec.whatwg.org/) – WHATWG Entwurf für die API-Spezifikation
* [HTML5 Server-Push Technologies, Part 2.](https://community.oracle.com/docs/DOC-982926) community.oracle.com; Einführung in WebSockets
* [*WebSocket: Annäherung an Echtzeit im Web*.](https://www.heise.de/developer/artikel/WebSocket-Annaeherung-an-Echtzeit-im-Web-1260189.html) heise Developer
1. [↑](#cite_ref-1) *[RFC](https://de.m.wikipedia.org/wiki/Request_for_Comments "Request for Comments"): [6455](https://datatracker.ietf.org/doc/html/rfc6455 "rfc:6455")* – *The WebSocket Protocol Version 13*. 2011 (englisch).
2. [↑](#cite_ref-2) Der vorliegende Beispielhandshake entstammt der [siebzehnten Version des *WebSocket Protocol Draft der IETF*](https://tools.ietf.org/html/draft-ietf-hybi-thewebsocketprotocol-17)
3. [↑](#cite_ref-3) *[RFC](https://de.m.wikipedia.org/wiki/Request_for_Comments "Request for Comments"): [6455](https://datatracker.ietf.org/doc/html/rfc6455#section-5.2 "rfc:6455")* – *The WebSocket Protocol Version 13* . 2011, Abschnitt 5.2: *Base Framing Protocoll*. (englisch).
4. [↑](#cite_ref-4) [developer.mozilla.org](https://developer.mozilla.org/de/docs/WebSockets)
