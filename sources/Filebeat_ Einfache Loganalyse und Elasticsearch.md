---
title: Filebeat: Einfache Loganalyse und Elasticsearch
---

# Filebeat: Einfache Loganalyse und Elasticsearch

url:: https://www.elastic.co/de/beats/filebeat
up: [[sources]]

#source

![](https://static-www.elastic.co/v3/assets/bltefdd0b53724fa2ce/bltfddc59cecbc2daf7/67e73ef2493d2c55b9ac5552/icon-filebeat-color.svg)

# Filebeat



## Leichtgewichtiger Shipper für Logdaten



Ob Sie Daten von Security-Geräten, aus der Cloud, aus Containern oder von Hosts oder aber OT-Daten erfassen – Filebeat hilft Ihnen dabei, Einfaches einfach zu halten, indem es Ihre Logdaten und Dateien unkompliziert und schnell weiterleitet und zentralisiert.

## Aggregieren, „tail -f“ und Suche



Wenn Sie nach dem Starten von Filebeat die Logs-Benutzeroberfläche öffnen, können Sie beobachten, wie Ihre Dateien direkt in Kibana „getailt“ werden. Über die Suchleiste lassen sich die Daten nach Dienst, App, Host, Rechenzentrum oder anderen Kriterien filtern, sodass Sie in den aggregierten Logs nach unerwarteten Ereignissen fahnden können.  
![Animated gif of Logs UI](https://static-www.elastic.co/v3/assets/bltefdd0b53724fa2ce/blt6f7988e6a847040c/5ca7ef626583cf3c4114a271/animated-gif-logs-ui-optimized.gif)  

## Robust und zuverlässig



In jeder Umgebung muss stets damit gerechnet werden, dass es an den Rändern zu Anwendungsausfällen kommt. Im Fall einer Unterbrechung merkt sich Filebeat, welche Logzeile zuletzt verarbeitet wurde, und setzt nach Wiederherstellung der Verbindung die Arbeit an der richtigen Stelle fort.

## Mit Filebeat bleiben einfache Dinge einfach



Filebeat [enthält standardmäßig Module](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-modules.html) für Observability- und Security-Datenquellen, die das Sammeln, Parsen und Visualisieren von Daten aus gängigen Logdatenformaten so weit vereinfachen, dass sich diese Schritte mit einem einzigen Befehl starten lassen. Dafür werden die automatischen Standardpfade des jeweiligen Betriebssystems mit den Elasticsearch-Ingest-Knoten-Pipeline-Definitionen sowie Kibana-Dashboards kombiniert. Hinzu kommt, dass einige Filebeat-Module mit vorkonfigurierten Machine-Learning-Jobs ausgestattet sind.

[Live-Demonstration von Filebeat-Modulen aufrufen](https://demo.elastic.co/app/kibana#/dashboard/19e7fae0-92a6-11e8-8fa2-3d5f811fbd0f)  
![](https://static-www.elastic.co/v3/assets/bltefdd0b53724fa2ce/blt43ba81f4b6aece56/5ca78745806ba2f746cf1c27/filebeat-modules-system.jpg)  

## Sowohl „Container-“ als auch „Cloud-ready“



Ganz gleich, ob Sie alles containerisieren oder in einer Cloud-Umgebung arbeiten – mit dem Elastic Stack ist es ganz einfach, Cloud-Umgebungen und [Container zu überwachen](https://www.elastic.co/de/what-is/kubernetes-monitoring). Mit Filebeat in einem Kubernetes-, Docker- oder Cloud-Deployment werden Sie stets mit allen Log-Streams versorgt, komplett mit Pod-, Container-, Knoten-, VM-, Host- und anderen Metadaten für das automatische Korrelieren. Und die Beats-Autodiscover-Features sorgen dafür, dass neue Container erkannt und mit den entsprechenden Filebeat-Modulen adaptiv überwacht werden.

## Schluss mit überlasteten Pipelines



Beim Senden von Daten an Logstash oder Elasticsearch kommt ein Protokoll zum Einsatz, das reagiert, wenn es in der Pipeline zu voll wird. Wenn Logstash mit der Verarbeitung der vorliegenden Daten nicht mehr hinterherkommt, wird Filebeat angewiesen, die Lesegeschwindigkeit zu drosseln. Sobald es wieder freie Kapazitäten gibt, kehrt Filebeat zur Normalgeschwindigkeit zurück und sendet weiter Daten.  
![](https://static-www.elastic.co/v3/assets/bltefdd0b53724fa2ce/bltcab325282e728dac/5c18b0141e9a48990b55d33e/filebeat-diagram.svg)  

## Elasticsearch oder Logstash fürs Speichern, Kibana fürs Visualisieren



Filebeat ist Bestandteil des Elastic Stack – die nahtlose Zusammenarbeit mit Logstash, Elasticsearch und Kibana ist also garantiert. Egal, ob Sie Ihre Logdaten und Dateien mit Logstash umwandeln oder anreichern, in Elasticsearch mit den Analyse-Tools herumspielen oder in Kibana Dashboards einrichten und teilen möchten: Mit Filebeat ist es ganz einfach, Daten dorthin zu senden, wo sie benötigt werden.
