---
copyright:
  years: 2014, 2018
lastupdated: "2018-08-16"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Was ist Virtuozzo?
{: #what-is-virtuozzo-}

Virtuozzo ist eine containerbasierte Virtualisierungslösung, die die gemeinsame Nutzung von Hardware mithilfe einer Abstraktionsebene ermöglicht. Virtuozzo erstellt Container, die auch als VEs oder VPSs bezeichnet werden und einen Server simulieren. Der Container arbeitet und reagiert hierbei überwiegend wie ein Standalone-Server. Der Container ist von anderen Containern getrennt, die sich auf demselben physischen Server befinden. Dies bedeutet, dass sie nicht auf die Dateien, IPC-Ressourcen oder Speichereinheiten anderer Container zugreifen können. Das Netz kann zur gemeinsamen Nutzung durch mehrere Container konfiguriert oder isoliert werden.
{:shortdesc}

Virtuozzo ist kein Hypervisor und auch keine Softwareschnittstelle zu einem Hypervisor. Das Produkt arbeitet mit einer Kombination aus Kernel- und Benutzeradressbereich in einem proprietären Kernel. Diese Konfiguration ermöglicht Containern die Übernahme von Ressourcen im Bedarfsfall, während die Ressourcen auf dem Hostknoten verfügbar sind.

Weitere Informationen finden Sie in [Virtuozzo ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://virtuozzo.com/){: new_window}.
