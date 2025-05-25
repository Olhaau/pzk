---
title: Remote Desktop Session Host (RDSH)
---

# Remote Desktop Session Host (RDSH)

url:: https://www.computerweekly.com/de/definition/Remote-Desktop-Session-Host-RDSH
up: [[sources]]

#source

* 
* 

## Was ist der Remote Desktop Session Host (RDSH)?

Remote Desktop Session Host (Remotedesktop-Sitzungshost, RDHS) ist eine Rolle in den Remote Desktop Services (Remotedesktopdiensten, RDS). RDHS kann sitzungsbasierte Windows-Anwendungen und -Desktops hosten, die für Benutzer aus der Ferne freigegeben werden können. Benutzer können darauf über einen Webclient in einem unterstützten Browser oder über einen Remotedesktop-Client zugreifen, der auf Windows-, macOS, iOS- und Android-Geräten läuft. Um remote auf Anwendungen oder Desktops zugreifen zu können, müssen die Benutzer über eine Netzwerkverbindung verfügen.

RDHS kann für zahlreiche Zwecke verwendet werden, beispielsweise für die Installation von Anwendungen auf einem Desktop an einem entfernten Standort. Das kann zum Beispiel in Unternehmen nützlich sein, die ihre Mitarbeiter bei der Installation einer benötigten Anwendung unterstützen müssen, während sie an einem anderen Ort arbeiten.

Zu den Vorteilen der Bereitstellung von [Windows](https://www.computerweekly.com/de/definition/Microsoft-Windows "Microsoft Windows")-Desktops oder -Anwendungen über Remote Desktop Session Host anstelle der Installation von Anwendungen auf den Geräten der Mitarbeiter gehören schnellere Update-Rollouts und die Möglichkeit für Endbenutzer, aus der Ferne auf Unternehmensanwendungen und Desktops zuzugreifen, und zwar mit Geräten, die diese Anwendungen oder Windows normalerweise nicht unterstützen.

## So funktioniert RDSH

Mehrere Endbenutzer können sich über einen [Remote Desktop Connection Broker](https://www.computerweekly.com/de/definition/Remote-Desktop-Connection-Broker "Remote Desktop Connection Broker") oder [Microsoft Azure](https://www.computerweekly.com/de/definition/Microsoft-Azure "Microsoft Azure") mit dem RDSH-[Server](https://www.computerweekly.com/de/definition/Server "Server") verbinden, um auf sitzungsbasierte Desktops, veröffentlichte Anwendungen und Serverressourcen zuzugreifen. Mehrere RDSH-Server können in einer Sitzungssammlung gruppiert werden und entweder [Azure](https://www.computerweekly.com/de/definition/Microsoft-Azure "Microsoft Azure")-Anwendungen oder sitzungsbasierte Desktops veröffentlichen, aber nicht beides von derselben Sammlung aus. Die Möglichkeit, RDSH-Server in Sammlungen zu gruppieren, war neu in Windows Server 2012 RDS. Die Funktion ist nützlich für den Lastausgleich von Verbindungen zwischen verbundenen Servern.

RDSH verwendet ein Webportal, um Windows-Desktops und -Anwendungen auf Clientgeräten zu veröffentlichen. Darüber hinaus können Benutzer selektiv Desktops oder Anwendungen für bestimmte Benutzer und Gruppen veröffentlichen. Damit RDSH ordnungsgemäß funktioniert, benötigt es [Internet Information Services](https://www.computerweekly.com/de/ratgeber/Microsoft-IIS-Tipps-und-Tools-fuer-den-sicheren-Betrieb "Microsoft IIS: Tipps und Tools für den sicheren Betrieb") (IIS) und eine sichere [HTTPS](https://www.computerweekly.com/de/definition/HTTPS-Hypertext-Transfer-Protocol-Secure "HTTPS (Hypertext Transfer Protocol Secure)")-Verbindung. Die HTTPS-Verbindung dient dazu, einen verschlüsselten Kommunikationskanal für den RD-[Webserver](https://www.computerweekly.com/de/definition/Webserver "Webserver") und die Clients bereitzustellen. Server und Clients müssen außerdem über übereinstimmende digitale Zertifikate verfügen, die mit dem Fully Qualified Domain Name (FQDN) übereinstimmen sollten, der für den Zugriff auf den RD-Web-Zugang verwendet wird.

Jede Remote-Sitzung auf einem RDSH-Server benötigt eine [Client Access License](https://www.computerweekly.com/de/definition/Windows-Server-Client-Access-License-CAL "Windows Server Client Access License (CAL)") (CAL). Die Lizenzierungsrolle in RDS verwaltet diese CALs, so dass es nicht mehr aktive Sitzungen als Lizenzen gibt. Standard-Windows-Server-Lizenzen enthalten die grundlegenden Anforderungen für die Einrichtung und Nutzung von RDSH und Lizenzierungsdiensten.
![Microsoft Remotedesktopservices](https://www.computerweekly.com/rms/German/Microsoft-Remotedesktopservices-deutsch_mobile.png) Abbildung 1: Dieses Bild zeigt, wo RDSH in Microsoft RDS passt. Diese Definition wurde zuletzt im Juli 2024 aktualisiert

#### Erfahren Sie mehr über Server- und Desktop-Virtualisierung

