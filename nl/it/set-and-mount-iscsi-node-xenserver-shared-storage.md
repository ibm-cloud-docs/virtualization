---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-16"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Configurazione e montaggio di un nodo iSCSI in XenServer Shared Storage
{: #setting-up-and-mounting-an-iscsi-node-in-xenserver-shared-storage}

Shared Storage è un repository di archiviazione (SR, Storage Repository) che è qualsiasi forma di archiviazione che può essere utilizzata da XenServer. Puoi scegliere da due categorie di SR: condiviso e non condiviso. Un SR condiviso consente a più nodi di condividere un'ubicazione di archiviazione dallo stesso pool. {{site.data.keyword.cloud}} supporta due opzioni di SR, ossia NFS e LVMoiSCSI (LVM su iSCSI).

Devi installare un server NFS e gestirlo da un altro sistema cliente oppure utilizzando un server {{site.data.keyword.cloud_notm}} QuantaStor. Attieniti alla seguente procedura per montare il repository NFS in XenCenter:

1. Apri la console XenCenter.
2. Fai clic su **New Storage**.
* Nella nuova finestra di dialogo, seleziona **Virtual disk storage > NFS**.
* Assegna un nome appropriato al nuovo SR.
* Immetti il percorso del server in **Share Name**
* Fai clic su **Scan**

Questo processo esegue la scansione della condivisione NFS per rilevare eventuali SR precedenti.

Puoi utilizzare {{site.data.keyword.blockstoragefull}} per LVMoiSCSI. iSCSI può essere da un server di archiviazione gestito dal cliente o un'offerta {{site.data.keyword.blockstoragefull}}. Per il Block Storage Performance e Redundant, richiama l'IQN dal {{site.data.keyword.slportal}} andando a **Storage > Block Storage > Select Lun Name**. Per montare il nodo iSCSI tramite XenCenter, attieniti alla seguente procedura:

1. Apri la console XenCenter.
2. Vai a **General > Properties** e imposta l'IQN.
3. Fai clic su **New Storage**
4. Nella nuova finestra di dialogo, vai a **Virtual disk storage > Software iSCSI**.
5. Immetti un nome appropriato per il nuovo SR.
6. Immetti il nome host o l'IP della destinazione iSCSI e mantieni la porta sul valore predefinito 3260.
7. Seleziona **Use CHAP**.
8. Immetti il nome utente e la password per la LUN.
9. Fai clic su **Scan Target Host**.
* Seleziona un'opzione **Target IQNs**.
* Seleziona un'opzione **Target  Luns**.
10. Dopo che i campi IQN e Lun sono stati compilati, fai clic su **Finish**. Viene eseguita una scansione della destinazione per rilevare la presenza degli SR precedenti. Se esiste un SR, il programma di installazione ti chiede se desideri creare un nuovo SR oppure collegarlo all'SR precedente.

Se i server si trovano in un pool insieme, il repository iSCSI viene condiviso automaticamente.

Per informazioni sulla configurazione di iSCSI per i percorsi multipli, vedi [Configuring iSCSI Multipath ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus1000/sw/5_x/sys_mgmt_config/b_Cisco_N1KV_VMware_Sys_Mgmt_Config_5x/b_Cisco_N1KV_VMware_Sys_Mgmt_Config_5x_chapter_01110.html?dtid=osscdc000283){: new_window}
