---
copyright:
  years: 2014, 2018
lastupdated: "2018-08-14"

subcollection: virtualization

keywords: Citrix XenServer
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Aggiunta di un'unità disco rigido a Citrix XenServer
{: #adding-a-hard-disk-drive-to-citrix-xenserver}

L'aggiunta di una nuova unità disco rigido in XenServer è diversa dal tradizionale processo Linux. Per XenServer, devi creare un contenitore denominato "repository di archiviazione" per definire una specifica destinazione di archiviazione (come un disco rigido). Questo contenitore è dove sono archiviate le VDI (virtual disk image) delle VM. Una VDI è uno spazio di archiviazione che è stato astratto che funge da disco rigido per le VM.

Il repository di archiviazione Xen supporta le unità IDE, SATA, SCSI e SAS in caso di connessione in locale, oltre a iSCSI, NFS, SAS e Fibre Channel per l'archiviazione remota.

## Creazione di un repository di archiviazione in uno XenServer
{: #creating-a-storage-repository-in-a-xenserver}

1. Esegui SSH allo XenServer come root.

2. Trova l'ID disco del nuovo dispositivo utilizzando il seguente comando:

       # cat /proc/partitions

  Il comando elenca tutte le unità disco rigido (HDD, Hard Disk Drive) e tutte le partizioni. Trova il tuo nuovo disco locale, che è probabilmente “sdx” (molto probabilmente sdb) o '/cciss/c0d1p0'. Utilizza il seguente comando per elencare gli ID disco per tutte le partizioni/HDD presenti nel server.

       # ll /dev/disk/by-id

  Trova l'ID disco del disco “sdx” o 'cciss/c0d1'. Il formato “scsi-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx” o “cciss-xxxxxxxxxxxxxxxxxxxxxxxxxx” è quello di cui hai bisogno.

3. Trova l''host-uuid' in uno XenServer utilizzando il seguente comando:

        #xe host-list

  L'uuid (RO) è l''host-uuid' di cui hai bisogno.

4. Crea un repository di archiviazione (SR, Storage Repository):

  > **Nota:** il comando [sr-create ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://support.citrix.com/article/CTX121313){: new_window} serve ad aggiungere una nuova unità disco rigido. La creazione di una nuova unità disco rigido è un processo distruttivo che partiziona e formatta l'unità e tutti i dati sull'unità vanno perduti. Se desideri reintrodurre un'unità che ha dei dati esistenti, utilizza [sr-introduce ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://support.citrix.com/article/CTX121896){: new_window}.

  `# xe sr-create content-type=user device-config:device=/dev/disk/by-id/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`<br/>
  \- Oppure -<br/>
  `# xe sr-create content-type=user device-config:device=/dev/disk/by-id/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`<br/>
  \- Oppure -<br/>
  `# xe sr-create content-type=user device-config:device=/dev/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`

## Verifica del repository di archiviazione in XenCenter
{: #verifying-the-storage-repository-in-xencenter}

Puoi verificare il repository di archiviazione da XenCenter attenendoti alla seguente procedura:

1. Stabilisci una connessione a XenCenter.

2. Vai a **Storage** per trovare i dettagli di tutti i repository di archiviazione. Puoi vedere l'archiviazione 'Local Storage2' nell'elenco che mostra che il disco è stato aggiunto. Ora puoi iniziare a creare le VDI su di esso.
