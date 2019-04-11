---

copyright:
years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: vsphere, Veeam
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}
{:table: .aria-labeledby="caption"}

# Backup del tuo ambiente VMware vSphere utilizzando Veeam
{: #backing-up-your-vmware-vsphere-environment-by-using-veeam}

Puoi eseguire un backup del tuo ambiente VMware vSphere utilizzando una soluzione ibrida formata dai seguenti servizi:

* Servizio {{site.data.keyword.cloud}} Object Storage
* NetApp AltaVault Cloud Storage Gateway
* Software Veeam Backup & Replication

Veeam Backup & Replication abilita una soluzione ibrida che include l'appliance di archiviazione integrata nel cloud NetApp AltaVault e {{site.data.keyword.cos_full}}. Il software crea, gestisce e ripristina gli ambienti virtuali dai backup. Quando viene utilizzato insieme a un'appliance di archiviazione integrata nel cloud NetApp AltaVault, crei dei backup che vengono archiviati in loco. Il backup viene anche simultaneamente replicato in {{site.data.keyword.cos_full_notm}}. Con questa soluzione ibrida, vengono creare due copie di un backup ma solo una di esse esiste localmente.

## AltaVault Cloud Storage Gateway
{: #altavault-cloud-integrated-storage-gateway}

Puoi utilizzare AltaVault Cloud Storage Gateway per integrare il tuo ambiente in loco con il cloud senza dover scrivere script o applicazioni utilizzando le API REST per {{site.data.keyword.cos_full_notm}}. Puoi montare o puntare ai punti di montaggio e iniziare a copiare i dati nel cloud in modo protetto.

### Distribuzione di AltaVault in loco
{: #deploying-altavault-on-premises}

Attieniti alla seguente procedura per distribuire AltaVault come una soluzione di backup in loco per {{site.data.keyword.cos_full_notm}}.

Puoi acquistare AltaVault come un'appliance fisica o virtuale. Questa procedura copre la distribuzione della versione di prova dell'appliance virtuale AltaVault basata su ESXi VMware vSpere.
{:tip}

<a name="prerequisites"></a>
#### Prerequisiti

Verifica che siano soddisfatti i seguenti prerequisiti:

* Una copia dell'appliance virtuale AltaVault. È un singolo file con un'estensione file OVA. Contatta il tuo rappresentante NetApp per l'appliance oppure scarica una versione di prova di 90 giorni dal [sito web di NetApp AltaVault ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](http://www.netapp.com/us/products/protection-software/altavault/){: new_window}.
* Un ambiente vSphere ESXi 5.5 in loco esistente con i requisiti minimi di CPU, memoria e spazio su disco disponibili per l'appliance AltaVault. Se utilizzi la versione di prova, questi requisiti sono: quattro vCPU (virtual CPU), 24 GB di memoria e fino a 8 TB di spazio su disco.
* Due NIC (network interface controller) da 10 Gbps disponibili nell'ambiente vSphere. Un NIC viene utilizzato per l'input dei dati e altro viene utilizzato per la replica dei dati a {{site.data.keyword.cos_full_notm}}.
* Due reti che corrispondono ai due NIC (VLAN) che sono definiti nell'ambiente vSphere. La rete di replica non può essere assegnata alla stessa rete della rete di input di dati; farlo può creare un loop di instradamento.
* Un insieme di credenziali {{site.data.keyword.cos_full_notm}}. Queste credenziali includono un nome utente {{site.data.keyword.cloud_notm}}, un nome utente {{site.data.keyword.cos_full_notm}} e la chiave API associata al nome utente {{site.data.keyword.cloud_notm}}.
* Conoscenza della tecnologia VMware Sphere e amministrazione degli ambienti vSphere ESXi. Questa conoscenza include, tra l'altro, l'uso del client web vSphere e l'assegnazione di risorse hardware che includono rete e archiviazione.

### Distribuzione di AltaVault OVA
{: #deploying-altavault-ova}

Puoi distribuire AltaVault OVA all'ambiente vSphere dopo che sono stati soddisfatti tutti i prerequisiti. Le istruzioni per la distribuzione di OVA sono disponibili nel manuale [NetApp AltaVault Installation and Service Guide ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}. 

1. Modifica la macchina virtuale (VM, Virtual Machine) AltaVault dopo che è stata completata la distribuzione di OVA.
2. Modifica la memoria assegnata in modo che corrisponda alla versione di AltaVault che si trova nella finestra di modifica. Se stai usando la versione di prova, assegna 24 GB di memoria e aggiungi un disco di dimensioni inferiori o pari a 8 TB. **Nota:** questo dispositivo di archiviazione disco secondario viene utilizzato per archiviare i dati di backup deduplicati.
* Assicurati di assegnare delle reti (VLAN) differenti all'appliance AltaVault dopo che sono state modificate le configurazioni di memoria e disco.

I NIC sono assegnati alle seguenti funzioni di interfaccia:

* Primario. Utilizzato come interfaccia di gestione.
* e0a. Un'interfaccia che viene utilizzata per replicare i dati dall'appliance AltaVault al cloud.
* e0b. Un'interfaccia utilizzata per esportare il punto di montaggio per la condivisione SMB/CIFS o NFS.
* e0c. Un'interfaccia facoltativa utilizzata per esportare il punto di montaggio per la condivisione SMB/CIFS o NFS.

In questa configurazione di esempio, l'appliance AltaVault utilizza l'interfaccia **e0a** come interfaccia di replica al cloud e l'interfaccia **e0b** per esportare un punto di montaggio CIFS/SMB. **Nota:** non si possono utilizzare sia una condivisione CIFS/SMB che una condivisione NFS per accedere agli stessi dati. In altre parole, se i dati vengono inseriti in una condivisione CIFS/SMB, non è possibile accedervi mediante una condivisione NFS e viceversa.

Per ulteriori informazioni sulla distribuzione dell'appliance AltaVault e la configurazione delle impostazioni di VM per l'appliance, vedi il manuale [NetApp AltaVault Installation and Service Guide ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}.

### Configurazione iniziale dell'appliance AltaVault.<!--initial configuration?-->
{: #initial-configuration-of-the-altavault-appliance-initial-configuration-}

Puoi accendere la VM AltaVault dopo che è stata configurata con l'hardware appropriato. **Nota:** ci può volere del tempo per l'avvio iniziale della VM AltaVault poiché l'appliance AltaVault sta formattando il disco della cache dei metadati secondario.

1. Dopo che l'appliance ha completato il processo di avvio, esegui l'accesso alla console AltaVault. Utilizza ``*admin`` come nome utente (**user name**) e ``password`` come **Password**. Puoi modificare queste credenziali dopo il completamento della configurazione iniziale.
2. Dopo che hai eseguito l'accesso, ti viene richiesto se desideri utilizzare la procedura guidata per la configurazione iniziale. Immetti **y** e premi **Invio** per salvare le tue modifiche.

Dopo l'apertura della procedura guidata, utilizza le informazioni nella Tabella 1.

|Domanda|Risposta|
|---|---|
|Step 1: Admin Password?|Immetti una nuova password di amministratore (non può essere "password").|
|Step 2: Host name?|Immetti il nome host che desideri utilizzare|
|Step 3: Use DHCP on primary interface?|Immetti **n**|
|Step 4: Primary IP address?|Immetti l'indirizzo IP di rete primario. Nella configurazione di esempio, l'indirizzo IP primario è la rete utilizzata per la gestione dell'appliance e della replica cloud (192.168.50.15)|
|Step 5: Netmask?|Immetti la maschera di rete (255.255.255.0)|
|Step 6: Default gateway?|Immetti il gateway predefinito (192.168.50.1)|
|Step 7: Primary DNS server?|Immetti il server DNS (Domain Name System) primario nel tuo ambiente|
|Step 8: Domain name?|Immetti il nome dominio del tuo ambiente (testenv.org)|
{: caption="Tabella 1. Valori di configurazione iniziali di AltaVault" caption-side="top"}

### Configurazione di AltaVault per Object Storage
{: #configuring-altavault-for-object-storage}

Utilizza la seguente procedura per configurare l'appliance per la connessione al servizio {{site.data.keyword.cos_full_notm}}.

1. Apri un browser web e immette l'indirizzo IP dell'interfaccia primaria dell'appliance AltaVault (vedi il passo precedente).
2. Accedi alla console con le credenziali di amministratore. Al primo accesso, viene visualizzato il Wizard Dashboard.
3. Seleziona **System Settings**, nella schermata successiva verifica che le informazioni siano corrette e regola il fuso orario per riflettere quello del tuo ambiente.
4. Fai clic su **Next > Save and Apply > Exit**. Vieni riportato al Wizard Dashboard.
5. Seleziona **Cloud Settings** e fai clic su **Provider**. Scegli **IBM Cloud Object Storage**.
6. Seleziona una regione Object Storage (**Object Storage Region**) appropriata. **Nota:** non tutte le regioni vengono visualizzate (ad esempio Melbourne). Tuttavia, il nome host del servizio {{site.data.keyword.cos_short}} viene modificato utilizzando il campo **Host name**. Ad esempio, se desideri utilizzare Melbourne come regione, puoi selezionare **San Jose 1** dal menu a discesa **Region** e modificare il campo **Host name** in **mel01.objectstorage.softlayer.net**.
7. Immetti le tue credenziali {{site.data.keyword.cos_full_notm}} nel campo **User name**. Il formato del nome utente deve essere `nomeutente_object_storage:nomeutente_IBM_Cloud`. Ad esempio: **ABC-DE123456-7:user**. Puoi trovare il tuo nome utente Object Storage in **Storage > Object Storage** nel [{{site.data.keyword.slportal_full}} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://control.softlayer.com/){: new_window}.
8. Immetti un nome bucket (**Bucket Name**) per archiviare i dati. Il nome bucket è il nome del contenitore in cui desideri archiviare i dati in {{site.data.keyword.cos_full_notm}}.
* Non modificare la porta predefinita se non su esplicita indicazione del tuo amministratore di rete. Il campo **Enable Archiving** viene impostato automaticamente su **No**; fai quindi clic su **Next**.
9. Immetti **License Request Token**, se necessario, e fai clic su **Next**.
10. Immetti una chiave di crittografia (**Encryption Key**). Puoi consentire a AltaVault di generare una nuova chiave di crittografia oppure immettere una chiave esistente che desideri utilizzare per crittografare e decrittografare i dati. Fai clic su **Next**.
11. Verifica che tutte le tue impostazioni siano corrette e fai quindi clic su **Finish and Apply**. AltaVault ora prova a contattare il servizio {{site.data.keyword.cos_full_notm}} utilizzando gli input e le impostazioni presenti nel Cloud Settings Wizard. Se la connessione non riesce, riesamina le tue impostazioni e accertati di disporre dell'accesso appropriato al servizio.
12. Fai clic su **Exit** dopo che è stata stabilita una connessione per ritornare al Wizard Dashboard e fai clic su **Exit Wizard** per ritornare alla pagina dello stato dell'appliance AltaVault.
13. Verifica che il servizio di ottimizzazione dell'archiviazione (*Storage Optimization Service*) sia in esecuzione e che lo stato indichi una condizione di pronto (ready).**Nota:** potrebbe volerci qualche minuto perché lo stato cambi in *Ready*.

L'appliance AltaVault è configurata per comunicare con il servizio {{site.data.keyword.cos_full_notm}}.

### Configurazione del punto di montaggio CIFS/SMB in AltaVault
{: #configuring-the-cifs-smb-mount-point-in-altavault}

L'interfaccia **e0b** deve essere configurata per creare un punto di montaggio CIFS/SMB. Utilizza la seguente procedura per configurare **e0b**.

1. Vai a **Settings > Data Interfaces** ed espandi l'interfaccia **e0b**. Seleziona **Enable Data Interface** e immetti l'indirizzo IP, la maschera di sottorete (**IP address, Subnet Mask**) e il **Gateway** che utilizzi per montare la condivisione CIFS/SMB.
2. Lascia il valore **MTU** predefinito di **1500 bytes**.<br/><br/>Anche se la MTU (maximum transmission unit) predefinita è impostata su 1.500, puoi modificarla in 9.000 se utilizzi i frame jumbo. **Nota:** il tuo host ESXi e la tua infrastruttura fisica devono supportare i frame jumbo. Per impostazione predefinita, {{site.data.keyword.cloud_notm}} già supporta una dimensione di MTU di 9.000 byte senza che occorra alcuna modifica della configurazione.
3. Fai clic su **Apply**. Il punto di montaggio è pronto per la configurazione.
4. Seleziona **Storage > CIFS > Add CIFS Share** e immetti un nome univoco.
5. Fai clic sul menu a discesa **Pin Share** e seleziona **Yes**. **Nota:** i backup di Veeam Backup & Replication possono non riuscire su una condivisione non localmente accessibile.
6. Immetti un percorso univoco per la condivisione nel campo **Path**. È preferibile utilizzare il nome della condivisione come percorso; cioè, se il nome della condivisione è `cifs_share0, immettere /cifs_share0` come percorso.
7. Deseleziona **Allow Everyone Access** se la sicurezza non è un problema. È preferibile inserire in whitelist i client che utilizzano la condivisione CIFS/SMB. Altrimenti, lascia **Allow Everyone Access** selezionato se la sicurezza è un problema e fai clic su **Add Share**.
8. Fai clic su **Add CIFS User** per creare gli account per gli utenti autorizzati e completa i campi **User name** e **Password**.
9. Espandi la nuova condivisione CIFS e fai clic su **Add a user or group** per aggiungere gli account utente autorizzati.
10. Vai a **Global CIFS Settings**, fai clic sul menu a discesa **Listening Interface**, seleziona **e0b** e fai clic su **Apply**.

L'appliance AltaVault è configurata per consentire le comunicazioni tra essa, {{site.data.keyword.cos_full_notm}} e il computer che sta eseguendo Veeam Backup & Replication. Ti consigliamo di esportare la configurazione dell'appliance AltaVault per velocizzare le future distribuzioni, se necessario.

Per esportare la configurazione della tua appliance AltaVault, attieniti alla seguente procedura.

1. Fai clic su **Settings > Setup Wizard** per accedere al Wizard Dashboard nella console di gestione web dell'appliance AltaVault in loco.
2. Fai clic su **Export Configuration** e fai clic su **Export Configuration**.
3. Salva il file di configurazione (un tarball/.tar) in un'ubicazione sicura.

## Veeam Backup & Replication
{: #veeam-backup-replication-backup}

Il software Veeam Backup & Replication fornisce delle funzionalità di backup, replica e ripristino complete per le VM e i loro dati. Il backup può integrarsi completamente con un'appliance AltaVault Cloud Gateway.

### Provisioning di Veeam su un nuovo server
{: #provisioning-veeam-on-a-new-server}

Puoi ordinare Veeam quando esegui il provisioning di una nuova VSI o di un nuovo server bare metal. Quando esegui il provisioning, utilizza le seguenti informazioni.
  * Veeam è disponibile solo con la fatturazione mensile
  * Veeam è disponibile solo con un sistema operativo Windows

Per aggiungere Veeam:
  * Nella sezione System Addons, sotto OS-Specific Addons, fai clic sulla scheda Veeam e seleziona una delle opzioni Veeam.
  * Nella sezione System Addons, sotto CDP, seleziona qualsiasi opzione Veeam supplementare da aggiungere. <br><br>**Nota**: se selezioni *Veeam Backup and Replication 9.5 Update 3*, devi selezionare almeno una opzione nell'elenco CDP Addon.

### Ordinazione di Veeam su un server esistente
{: #ordering-veeam-on-an-existing-server}

Puoi aggiungere Veeam a un server esistente con la procedura [OS reload](/docs/infrastructure/software?topic=software-reloading-the-os). Il server deve eseguire un sistema operativo Windows e deve essere configurato per la fatturazione mensile.

Per aggiungere Veeam:
1. Nella sezione System Addons, sotto OS-Specific Addons, fai clic sulla scheda Veeam e seleziona una delle opzioni Veeam.
2. Nella sezione System Addons, sotto CDP, seleziona qualsiasi opzione Veeam supplementare da aggiungere. <br><br>**Nota**: se selezioni *Veeam Backup and Replication 9.5 Update 3*, devi selezionare almeno una opzione nell'elenco CDP Addon.

### Distribuzione di Veeam Backup & Replication
{: #deploying-veeam-backup-replication}

Nell'esempio viene utilizzata una versione di prova di Veeam Backup & Replication versione 8.

#### Prerequisiti
{: #prerequisites-deploying-veeam-backup-replication}

Prima di procedere con la distribuzione, verifica che siano soddisfatti i seguenti prerequisiti:

* Disponi di un'appliance AltaVault esistente configurata per l'utilizzo con {{site.data.keyword.cos_full_notm}} e Veeam Backup & Replication.
* Ottieni una copia di Veeam Backup & Replication per gli ambienti VMware, che è un singolo file eseguibile. Contatta il tuo rappresentante Veeam per una copia oppure scarica una [versione di prova di 30 giorni![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](http://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}.
* Ottieni un file di licenza da utilizzare con Veeam Backup & Replication. Nella maggior parte dei casi, questo file viene inviato per email all'indirizzo email utilizzato per scaricare Veeam Backup & Replication. Se non hai ricevuto questo file, contatta il rappresentante di Veeam.<br/><br/>Il file di licenza viene utilizzato per attivare tutte le funzioni di Veeam Backup & Replication, Se questo file non viene fornito durante l'installazione del programma, tutte le funzioni e tutta la funzionalità sono ripristinate a quelle della versione di prova di 30 giorni.
* Disponi di un server di backup esistente, che può essere in loco o non in loco, con le specifiche disponibili nella Tabella 2. Il sistema operativo installato deve essere una versione a 64 bit.

||Minimo|Consigliato|
|---|---|---|
|**Sistema operativo**|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>Windows Server 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>Windows Server 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|
|**# di core o vCPU**|2|4|
|**Memoria**|RAM di base di 4 GB più 500 MB per ogni lavoro di backup simultaneo.|RAM di base di 16 GB più 4 GB per ogni lavoro di backup simultaneo.|
|**Spazio su disco**|2 GB per l'installazione del prodotto; 10 GB per ogni 100 VM per i dati del catalogo del file system guest (dati persistenti).|2 GB per l'installazione del prodotto; 10 GB per 100 VM per il catalogo del file system guest (dati persistenti).|
|**Rete**|LAN da 1-Gbps per backup e replica in sito; VLAN da 1-Mbps per backup e replica non in sito. |LAN da 1-Gbps per backup e replica in sito; WAN da 1-Mbps per backup e replica non in sito.|
{: caption="Tabella 2. Requisiti di sistema per il server di backup Veeam Backup & Replication" caption-side="top"}

#### Installazione di Veeam Backup & Replication
{: #installing-veeam-backup-replication}

Attieniti alla seguente procedura per installare Veeam Backup & Replication sul server di backup dopo che sono stati soddisfatti tutti i prerequisiti.

1. Per avviare la procedura guidata di configurazione, fai doppio clic sul file eseguibile di Veeam e fai clic su **Veeam Backup & Replication – Install**.
2. Fai clic su **Next** e accetta i termini nell'accordo di licenza.
3. Fai clic su **Next** e fai clic su **Install** per **Veeam Backup & Replication**.
4. Immetti l'ubicazione del file di licenza che avevi ottenuto in precedenza e fai clic su **Next**.
5. Seleziona i componenti Veeam Backup & Replication che desideri installare e fornisci l'ubicazione di installazione nella schermata **Veeam Backup & Replication Setup**. **Veeam Backup & Replication e Veeam Backup Catalog** sono componenti obbligatori. Fai clic su **Next**. La procedura guidata di configurazione esegue una serie di controlli per accertarsi che tutti i framework di programmi e i componenti di supporto richiesti siano installati. Se ne manca qualcuno, la procedura guidata di configurazione si offre di eseguirne l'installazione automaticamente. Se devi installare dei framework o dei componenti mancanti, fai clic su **Install**.
6. Verifica che tutti i componenti abbiano superato (**Passed**) il controllo del sistema e fai clic su **Next**.
7. Seleziona l'account (utente) del servizio (**Service (user) Account**) dove viene eseguito il servizio di backup Veeam. **Nota:** l'account di servizio predefinito è l'account di sistema locale (*LOCAL SYSTEM account*). Fai clic su **Next**.
8. Seleziona la **SQL Server Instance** utilizzata per creare e archiviare i database Veeam Backup & Replication.<!--Contact your database administrator for more information, if necessary. --> Fai clic su **Next**.
9. Immetti la porta del servizio di catalogo (**Catalog service port**) e la porta del servizio di backup Veeam (**Veeam Backup service port**) (le porte predefinite sono **9393** e **9392**).<!--Contact your network administrator for more information, if necessary.--> Fai clic su **Next**.
10. Seleziona le directory dove sono archiviati il catalogo del file system guest (dati persistenti) e la cache di scrittura vPower NFS (dati non persistenti). Fai clic su **Next**.
11. Verifica che tutte le impostazioni e tutti i valori siano corretti e fai clic su **Install** per avviare l'installazione. Una volta completata l'installazione, fai clic su **Finish**.

### Configurazione di Veeam Backup & Replication for Backups
{: #configuring-veeam-backup-replication-for-backups}

Dopo che hai installato Veeam Backup & Replication, sei pronto a connetterlo all'host ESXi vSphere che contiene l'appliance virtuale AltaVault.

1. Avvia Veeam Backup & Replication.
2. Nella parte inferiore sinistra dello schermo, fai clic su **Backup Infrastructure > Managed Servers**.
3. Fai clic su **Add Server** e fai doppio clic su **VMware vSphere**.
4. Immetti il nome DNS o l'indirizzo IP (**DNS name or IP address**) e la descrizione (**Description**) del server vCenter e fai clic su **Next**.
5. Immetti le credenziali (**Credentials**) di un account locale che dispone di privilegi di amministratore sul server vSphere. **Nota:** il nome utente dell'account deve essere in formato **DOMINIO\UTENTE** per gli account di dominio oppure in formato **HOST\UTENTE** per gli account locali. Per aggiungere un account, fai clic su **Add** e immetti il nome utente e la password dell'account. <br/>Non modificare la porta dei servizi web di VMware predefinita (**Default VMware web services port**) durante l'installazione di Veeam Backup & Replication se non dietro indicazione esplicita del tuo amministratore di rete.
6. Fai clic su **Next**. Veeam Backup & Replication si connette al server VMware vSphere. Se il tentativo di connessione non riesce, controlla che l'account esista e che disponga di privilegi di amministratore sul server VMware vSphere prima di provare nuovamente a stabilire una connessione.
7. Fai clic su **Finish** nella finestra **Summary** e verifica che il server vSphere sia stato aggiunto correttamente facendo clic su **Managed Servers > VMware vSphere**.

### Aggiunta di un repository di backup a Veeam Backup & Replication
{: #adding-a-backup-repository-to-veeam-backup-replication}

Per impostazione predefinita, Veeam Backup & Replication crea un repository di backup locale sull'unità C:\ del server di backup Veeam Backup & Replication durante l'installazione del programma.

Utilizza la seguente procedura per creare un repository di backup per archiviare tutti i backup sull'appliance AltaVault. Se desideri utilizzare il repository di backup predefinito, ignora i seguenti passi.

1. Nella parte inferiore sinistra della schermata **Backup Infrastructure**, fai clic su **Backup Infrastructure > Backup Repositories > Add Repository**.
2. Immetti un nome repository univoco nel campo **Name**. Facoltativamente, puoi fornire una descrizione (**Description**). Fai clic su **Next**.
3. Seleziona **Shared folder**. La condivisione CIFS/SMB che hai creato è la condivisione utilizzata da questo repository di backup. Fai clic su **Next**.
4. Specifica l'ubicazione della condivisione CIFS/SMB sull'appliance AltaVault. Per determinare l'ubicazione, apri un browser web e immetti l'indirizzo IP dell'appliance AltaVault. Vai a **Storage > CIFS** e prendi nota del percorso di condivisione (**Share Path**) della condivisione. **Nota:** questo NON è uguale al percorso locale della condivisione. <br/><br/>Il formato del percorso di condivisione è `\\<AltaVault appliance hostname>\<share name>`. Sostituisci il nome host dell'appliance AltaVault nel percorso di condivisione con l'indirizzo IP dell'interfaccia di rete **e0b** (il punto di montaggio della condivisione) dell'appliance AltaVault.<br/><br/>Per trovare l'indirizzo IP dell'interfaccia **e0b**, fai clic su **Settings > Data Interfaces** nella finestra di gestione dell'appliance AltaVault. Il percorso di condivisione specificato in Veeam Backup & Replication è `\\192.168.50.16\cifs_test2`.
5. Ritorna a Veeam Backup & Replication, immetti il percorso di condivisione del punto di montaggio nel campo **Shared folder** e fai clic su **Next**. Veeam Backup & Replication prova a stabilire una connessione con il punto di montaggio. Se il tentativo di connessione non riesce, torna indietro e verifica che le impostazioni di rete per l'appliance AltaVault siano corrette prima di riprovare.
6. Immetti un valore per limitare le attività simultanee massime al numero di risorse disponibili. Questo valore è il numero massimo di attività che un proxy di backup può inviare alla condivisione selezionata. Il numero predefinito di attività simultanee è 4. AltaVault consiglia di iniziare con cinque attività simultanee e di aumentare o diminuire questo valore come consentito dalle risorse. Questo valore può essere regolato dopo che il repository di backup è stato creato.
7. Fai clic su **Next**.
8. Se necessario, specifica le impostazioni NFS vPower (**vPower NFS settings**). Se la selezione **Enable vPower NFS server** viene lasciata non selezionata, Veeam Backup & Replication utilizza vPower per il recupero e la verifica del recupero. Fai clic su **Next**.
9. Nella schermata **Review**, conferma che tutte le tue impostazioni siano corrette e fai clic su **Next**.
10. Fai clic su **Finish** per uscire dalla procedura guidata. Puoi ora iniziare ad eseguire il backup dei tuoi dati.

### Backup dell'ambiente
{: #backing-up-the-environment}

Attieniti alla seguente procedura per creare un backup di un ambiente virtuale completo.

1. Dalla schermata **Backup Infrastructure**, fai clic su **Backup & Replication**.
2. Nella finestra **Backup & Replication**, fai clic su **Jobs > Backup Job**.
3. Immetti un nome univoco nel campo **Name**. Facoltativamente, puoi immettere una descrizione (**Description**). Fai clic su **Next**.
4. Seleziona le VM di cui desideri eseguire il backup facendo clic su **Add Objects** e, mediante clic del mouse all'interno della struttura ad albero, seleziona le VM. Fai clic su **Add** dopo aver selezionato le VM appropriate.<br/>Se deve essere eseguito il backup solo di specifiche parti delle VM (il disco di avvio), fai clic su **Exclusions** e specifica le parti. Altrimenti, fai clic su **Next**.
5. Seleziona il repository di backup che hai creato utilizzando il menu a discesa **Backup repository**.

**Nota:**per prestazioni ottimali, assicurati di modificare le impostazioni di deduplicazione e compressione dei dati. Per ottimizzare le prestazioni, attieniti alla seguente procedura.

1. Fai clic su **Advanced**, seleziona la scheda **Storage** e deseleziona **Enable inline data duplication**. Le prestazioni migliorano perché l'appliance AltaVault esegue una deduplicazione a livello di blocco dei backup di Veeam Backup & Replication che lo attraversano.
2. Seleziona **None** nel menu a discesa **Compression level** e seleziona **LAN target** nel menu a discesa **Storage optimization**. <br/>**Nota:** se l'ubicazione di rete della condivisione CIFS/SMB è congestionata, lasciare l'opzione **Enable inline data deduplication** selezionata riduce i problemi di prestazioni della rete ma a costo di rapporti di deduplicazione dei dati minori riscontrati sull'appliance AltaVault.
3. Fai clic su **Next**.
4. Se desideri un'elaborazione sensibile alle applicazioni e/o un'indicizzazione del file system guest, seleziona la casella di spunta appropriata. Imposta le **Guest OS credentials** [il nome utente e la password del sistema operativo guest delle VM oggetto del backup], se necessario. Fai clic su **Next**.
5. Seleziona la casella di spunta **Run the job automatically** se i backup vengono eseguiti regolarmente e imposta gli intervalli che desideri. Altrimenti, fai clic su **Create** e **Finish**.

#### Avvio di un backup manuale
{: starting-a-manual-backup}
Per avviare manualmente un backup, fai clic con il pulsante destro del mouse sul lavoro di backup e seleziona **Start**. In alternativa, seleziona **Active Full**se desideri un nuovo backup.

**Nota:** Veeam Backup & Replication può ripristinare gli ambienti virtuali da un backup. Per ulteriori informazioni sul ripristino di ambienti virtuali, vedi il sito web di [Veeam Backup & Replication ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}.
