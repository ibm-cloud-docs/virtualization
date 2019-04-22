---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Überblick zu Parallels Server 4 Bare Metal Server
{: #overview-of-parallels-server-4-bare-metal}

Parallels Server Bare Metal ist eine Virtualisierungslösung, die die Hardwarevirtualisierung zusammen mit der Softwarevirtualisierung von Virtuozzo bereitstellt. Hierbei wird die erforderliche Technologie für virtuelle Maschinen und Container bereitgestellt*.
{:shortdesc}

**Befehlszeilenschnittstelle**

Zusammen mit den üblichen Virtuozzo-Befehlen werden weitere Befehle bereitgestellt: `pctl`, `pmigrate`, `pstat` und  `prl_disk_tool`.

**Migration**

Sie können Virtuozzo-Container jetzt auf virtuelle Maschinen, physische Server auf Virtuozzo-Container und physische Server auf virtuelle Maschinen migrieren, um die Konsolidierung von Services zu ermöglichen. Diese Funktionalität erlaubt außerdem die Migration von Containern oder VMs zwischen Bare Metal Server-Systemen, die Konvertierung von VMs, die in unterschiedlichen virtualisierten Umgebungen (V2V) erstellt wurden und die Änderung von Windows-SIDs beim Klonen oder Bereitstellen einer Windows-VM auf Basis einer Vorlage.

**Verwendung von IP-Adressen und VLANs**

Sie können VMs einer IP über den physischen Server zuordnen, indem Sie Parallels Tools verwenden, die in der VM verfügbar sind. Darüber hinaus ermöglicht PSBM die Erstellung virtueller Switches und VLANs in den VMs, um den VM-internen Netzdatenverkehr wirkungsvoller zu sichern.

**Befehle ausführen**

Sie können unaufbereitete Befehle über den physischer Server direkt in den Virtuozzo-Containern ausführen und nun auch in VMs (wenn die Parallels Tools auf der VM installiert sind). Dies schließt auch das Zurücksetzen von Benutzerkennwörtern ein.

**Prozessabrechnung**

Sie können Parallels Server Bare Metal verwenden, um die Priorität von Ressourcen, die einer VM zugeordnet sind (Priorität von CPU und Platten-E/A), schnell zu erhöhen oder zu reduzieren.

**Sicherung**

Parallels Server Bare Metal bietet die Funktionalität zum Sichern und Wiederherstellen von VMs und Containern entweder auf dem lokalen Bare Metal Server-System oder auf einem fernen Bare Metal Server-System. Hierbei können Gesamtsicherungen und Teilsicherungen erstellt werden.

**Netzabrechnung**

Sie können Parallels Server Bare Metal verwenden, um VMs oder Container auf Basis des aktuellen und protokollierten Netzdurchsatzes einfach anzuzeigen und zu lokalisieren.

**Netz**

Parallels Server Bare Metal verwendet portierbare IP-Adressen, während Virtuozzo (abhängig von der Konfiguration) entweder portierbare oder statische IP-Adressen verwendet.

\* {{site.data.keyword.cloud}} lizenziert nur virtuelle Maschine für Hardware unter Parallels Server 4 Bare Metal, es sei denn, auf dem Bestellschein werden anderen Angaben gemacht.
_VM_ = virtuelle Maschine. _V_ = VPS oder Container.
