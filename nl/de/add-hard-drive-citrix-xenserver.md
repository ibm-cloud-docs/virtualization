---
copyright:
  years: 2014, 2018
lastupdated: "2018-08-14"

subcollection: virtualization

keywords: Citrix XenServer
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Festplattenlaufwerk zu Citrix XenServer hinzufügen
{: #adding-a-hard-disk-drive-to-citrix-xenserver}

Das Hinzufügen eines neuen Festplattenlaufwerks in XenServer unterscheidet sich von dem herkömmlichen Linux-Prozess. Für XenServer müssen Sie einen Container erstellen, der als 'Speicherrepository' bezeichnet wird, um ein bestimmtes Speicherziel (z. B. eine Festplatte) zu definieren. In diesem Container werden die VDIs (Virtual Disk Images; virtuelle Plattenimages) von VMs gespeichert. Ein VDI ist ein abstrakter Speicherbereich, der als Festplatte für VMs fungiert.

Das Xen-Speicherrepository unterstützt bei lokaler Verbindung IDE-, SATA-, SCSI- und SAS-Laufwerke und für die ferne Speicherung iSCSI-, NFS-, SAS- und Fibre Channel-Einheiten.

## Speicherrepository in XenServer erstellen
{: #creating-a-storage-repository-in-a-xenserver}

1. Stellen Sie als Root eine SSH-Verbindung zu XenServer her.

2. Verwenden Sie den folgenden Befehl, um die Platten-ID der neuen Einheit zu suchen:

       # cat /proc/partitions

  Der Befehl listet alle HDD-Festplatten und entsprechenden Partitionen auf. Suchen Sie die neue lokale Festplatte. Diese ist normalerweise mit 'sdx' (am häufigsten 'sdb') oder mit '/cciss/c0d1p0' gekennzeichnet. Verwenden Sie den folgenden Befehl, um die Platten-IDs für alle Partitionen/HDD-Einheiten aufzulisten, die im Server vorhanden sind.

       # ll /dev/disk/by-id

  Suchen Sie die Platten-ID der 'sdx'- oder 'cciss/c0d1'-Platte. Sie benötigen das Format "scsi-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" oder "cciss-xxxxxxxxxxxxxxxxxxxxxxxxxx".

3. Verwenden Sie den folgenden Befehl, um die Angabe für 'host-uuid' auf einem XenServer-System zu suchen:

        #xe host-list

  Die UUID (RO) enthält die von Ihnen benötigte Angabe für 'host-uuid'.

4. Erstellen Sie ein Speicherrepository (SR):

  > **Hinweis:** Der Befehl [sr-create ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://support.citrix.com/article/CTX121313){: new_window} dient zum Hinzufügen eines neuen Festplattenlaufwerks. Die Erstellung eines neuen Festplattenlaufwerks ist ein destruktiver Prozess, bei dem das Laufwerk partitioniert und formatiert wird und alle Daten auf dem Laufwerk verloren gehen. Wenn Sie ein Laufwerk mit bereits vorhandenen Daten wiederverwenden wollen, dann verwenden Sie den Befehl [sr-introduce ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://support.citrix.com/article/CTX121896){: new_window}.

  `# xe sr-create content-type=user device-config:device=/dev/disk/by-id/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`<br/>
  \- Oder -<br/>
  `# xe sr-create content-type=user device-config:device=/dev/disk/by-id/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`<br/>
  \- Oder -<br/>
  `# xe sr-create content-type=user device-config:device=/dev/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`

## Speicherrepository in XenCenter überprüfen
{: #verifying-the-storage-repository-in-xencenter}

Sie können das Speicherrepository von XenCenter überprüfen, indem Sie die folgenden Schritte ausführen:

1. Stellen Sie eine Verbindung zu XenCenter her.

2. Rufen Sie die Option **Speicher** auf, um Details zu allen Speicherrepositorys zu ermitteln. Wenn in der Liste die Speichereinheit 'Local Storage2' angezeigt wird, dann wurde die Platte hinzugefügt. Jetzt können Sie mit der Erstellung von VDIs beginnen.
