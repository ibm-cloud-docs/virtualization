---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-16"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# iSCSI-Knoten in XenServer Shared Storage einrichten und anhängen
{: #setting-up-and-mounting-an-iscsi-node-in-xenserver-shared-storage}

Shared Storage ist ein Speicherrepository (SR), das jedes Format aufweisen kann, das von XenServer benutzt werden kann. Sie können zwischen zwei Kategorien von Speicherrepositorys auswählen: gemeinsam genutzte und nicht gemeinsam genutzte. Ein gemeinsam genutztes Speicherrepository ermöglicht mehreren Knoten die gemeinsame Nutzung einer Speicherposition über denselben Pool. {{site.data.keyword.cloud}} unterstützt zwei Optionen für SRs: NFS und LVMoiSCSI (LVM over iSCSI).

Sie müssen einen NFS-Server installieren und ihn über ein anderes Kundensystem oder mithilfe eines {{site.data.keyword.cloud_notm}} QuantaStor-Servers verwalten. Führen Sie die folgenden Schritte aus, um das NFS-Repository in XenCenter anzuhängen:

1. Öffnen Sie die XenCenter-Konsole.
2. Klicken Sie auf **Neuer Speicher**.
* Wählen Sie im neuen Dialogfenster **Virtueller Plattenspeicher > NFS** aus.
* Geben Sie einen geeigneten Namen für das neue SR an.
* Geben Sie unter **Freigabename** den Serverpfad an.
* Klicken Sie auf **Scan**.

Dieser Prozess durchsucht die NFS-Freigabe nach allen vorherigen SRs.

Sie können den {{site.data.keyword.blockstoragefull}} für LVMoiSCSI verwenden. Die iSCSI-Komponente kann von einem vom Kunden verwalteten Speicherserver oder aus einem {{site.data.keyword.blockstoragefull}}-Angebot stammen. Rufen Sie für Performance and Redundant Block Storage den IQN aus dem {{site.data.keyword.slportal}} ab. Dazu müssen Sie die Optionen **Speicher > Block Storage > LUN-Name auswählen** verwenden. Führen Sie die folgenden Schritte aus, um den iSCSI-Knoten über XenCenter anzuhängen:

1. Öffnen Sie die XenCenter-Konsole.
2. Rufen Sie **Allgemein > Eigenschaften** auf und legen Sie den IQN fest.
3. Klicken Sie auf **Neuer Speicher**.
4. Rufen Sie im neuen Dialogfenster **Virtueller Plattenspeicher > Software iSCSI** auf.
5. Geben Sie einen geeigneten Namen für das neue SR an.
6. Geben Sie den Hostnamen oder die IP des iSCSI-Ziels ein und behalten Sie den Standardport '3260' bei.
7. Wählen Sie **CHAP verwenden** aus.
8. Geben Sie den Benutzernamen und das Kennwort für die LUN ein.
9. Klicken Sie auf **Zielhost scannen**.
* Wählen Sie die Option **Ziel-IQNs** aus.
* Wählen Sie die Option **Ziel-LUNs** aus.
10. Nachdem die Felder für IQN und LUN ausgefüllt wurden, klicken Sie auf **Fertigstellen**. Das Ziel wird auf vorherige SRs überprüft. Wenn ein SR vorhanden ist, dann fragt das Installationsprogramm an, ob ein neues SR erstellt oder eine Verbindung zum vorherigen SR hergestellt werden soll.

Wenn sich die Server in einem Pool befinden, dann wird das iSCSI-Repository automatisch gemeinsam genutzt.

Informationen zum Konfigurieren von iSCSI für das Multipathing finden Sie im Abschnitt zum [Konfigurieren von iSCSI Multipath ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus1000/sw/5_x/sys_mgmt_config/b_Cisco_N1KV_VMware_Sys_Mgmt_Config_5x/b_Cisco_N1KV_VMware_Sys_Mgmt_Config_5x_chapter_01110.html?dtid=osscdc000283){: new_window}.
