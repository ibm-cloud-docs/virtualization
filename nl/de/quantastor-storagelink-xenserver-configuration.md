---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: QuantaStor
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# QuantaStor StorageLink Xenserver installieren
{: #installing-quantastor-storagelink-xenserver}

## QuantaStor-Adapter für Citrix StorageLink installieren
{: #installing-the-quantastor-adapter-for-citrix-storagelink}

1. Vergewissern Sie sich, dass Ihre XenServer-Installation für StorageLink korrekt lizenziert wurde.

Nur Kunden, die die Enterprise- oder Platinum-Lizenz über Citrix abonniert haben, können die StorageLink-Funktion verwenden. Kunden, die mit der erweiterten Citrix XenServer-Lizenz arbeiten, können hingegen nicht auf die StorageLink-Funktion zugreifen.
{:tip}

2. Stoppen Sie den StorageLink-Service, indem Sie an der Eingabeaufforderung des Administrators folgenden Befehl eingeben. Alternativ hierzu können Sie den StorageLink-Service auch über die Wartungskonsole in XenServer stoppen.

        C:\> net stop storagelink

3. Laden Sie den StorageLink-Adapter von [OSNexus ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://www.osnexus.com/trynow/){: new_window} herunter und führen Sie ihn aus.
4. Nachdem der Service gestoppt wurde, können Sie den QuantaStor StorageLink-Adapter installieren, indem Sie den folgenden Befehl ausführen:

        osn_quantastor_csladapter.exe

5. Fügen Sie diesen XML-Block am Ende der StorageLink-Konfiguration im folgenden Verzeichnis hinzu:
  * `C:\Program Files\Citrix\StorageLink\Server\scsi_device_id_config.xml`
  * `OSNEXUS`
  * `QUANTASTOR1`
  * Am Anfang dieser Datei finden Sie die Versionsnummer, z. B. _2.0.0.4._ Erhöhen Sie die Versionsnummer, z. B. auf 2.0.0.5. Die Versionsnummer zeigt StorageLink an, dass für die XenServer-Systeme mit diesen neuen Einheitenidentifikationsregeln ein Upgrade durchgeführt werden muss.
6. Führen Sie für den Citrix StorageLink-Service einen Neustart durch:

        C:\> net start storagelink

## Korrektes Laden von StorageLink überprüfen
{: #verifying-that-storagelink-loaded-properly}

1. Melden Sie sich bei Citrix StorageLink Manager an, wählen Sie die Baumstruktur für die Verwaltung auf der linken Seite aus und suchen Sie in der Liste für [QuantaStor ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://svn.osnexus.com/mediawiki/images/thumb/c/c8/Storagelink_admin.png/640px-Storagelink_admin.png){: new_window} nach dem Eintrag für OS NEXUS QuantaStor.
2. Fügen Sie die Berechtigungsnachweise für das Speichersystem hinzu, sodass StorageLink Speicherdatenträger für das QuantaStor-Speichersystem dynamisch konfigurieren und zuordnen kann.

## XenServer-Multipath-Konfigurationseinstellungen installieren
{: #installing-the-xenserver-multipath-configuration-settings}

XenServer verwendet den Linux-Device-Mapper-Multipath-Treiber (`dmmp`), um die Unterstützung für das Hardware-Multipathing zu aktivieren. Der Treiber `dmmp` verwendet eine Konfigurationsdatei mit dem Namen `multipath.conf`, die die Regeln für den Multipath-Modus und das Failover für jeden Hersteller enthält.

QuantaStor verfügt über einige spezielle Regeln, die zur Datei `multipath.conf` hinzugefügt werden müssen. Bearbeiten Sie die datei `/etc/multipath-enabled.conf`, die auf allen XenServer-Knoten mit dem Namen `dom0` vorhanden ist. In dieser Datei müssen Sie die folgende Zeilengruppe nach dem Abschnitt { } der letzten Einheit hinzufügen:

    device {
          vendor                  "OSNEXUS"
          product                 "QUANTASTOR"
          getuid_callout          "/sbin/scsi_id -g -u -s /block/%n"
          path_selector           "round-robin 0"
          path_grouping_policy    multibus
          failback                immediate
          rr_weight               uniform
          rr_min_io               100
          path_checker            tur
    }
