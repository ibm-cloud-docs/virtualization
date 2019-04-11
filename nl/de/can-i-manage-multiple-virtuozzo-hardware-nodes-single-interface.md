---
copyright:
  years: 2014, 2018
lastupdated: "2018-08-15"

subcollection: virtualization

keywords: Virtuozzo
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Mehrere Virtuozzo-Hardwareknoten über einzelne Schnittstelle verwalten
{: #managing-multiple-virtuozzo-hardware-nodes-through-a-single-interface}

Die Verwaltung mehrerer Knoten erfolgt über die PIM-Schnittstelle (PIM = Parallels Infrastructure Manager). Sie verwenden PIM, um einen Master-Server mit mehreren Slaves zu implementieren. Ein Slave wird nur von einem einzigen Master verwaltet. Er kann nicht Mitglied alternativer Gruppen sein, die von einem separaten Master verwaltet werden.

Führen Sie die folgenden Schritte aus, um mehrere Hardwareknoten zu verwalten:

1. Rufen Sie **Infrastruktur** auf.
2. Klicken Sie auf der zweiten Registerkarte auf **Hardwareknoten > Neuer Hardwareknoten**.
3. Klicken Sie auf **Registrieren**. 

Sie können jetzt sowohl den Master als auch den Slave über dieselbe PIM-Schnittstelle verwalten.
