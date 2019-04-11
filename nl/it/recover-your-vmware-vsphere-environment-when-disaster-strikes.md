---
copyright:
  years: 2014, 2018
lastupdated: "2018-06-22"

subcollection: virtualization

keywords: disaster recovery
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:table: .aria-labeledby="caption"}

# Ripristino del tuo ambiente VMware vSphere
{: #recovering-your-vmware-vsphere-environment}

Il backup dei dati è attualmente il metodo principale per mantenere la sicurezza e l'integrità dei dati. In un mondo ideale e perfetto, il loro utilizzo non sarebbe mai necessario. Purtroppo, la realtà è che gli incidenti e i disastri accadono. Nel caso in cui ciò succeda, è di fondamentale importanza ridurre al minimo il tempo di inattività del sistema e tornare operativi il prima possibile.

In passato, l'archiviazione di backup su nastri o in archivi ad elevate prestazioni era una soluzione fattibile. Oggi, le aziende cercano di alleggerire i costi di capitale e operativi associati all'archiviazione in loco fisica aumentandola o anche sostituendola con Object Storage. Tuttavia, con Object Storage, poiché i backup non si trovano più nell'archiviazione in loco, l'esecuzione di ripristini da essi non significa più semplicemente individuare il backup necessario e utilizzarlo per il ripristino. Fortunatamente, esiste una soluzione ibrida che esegue gran parte del lavoro richiesto dal ripristino di backup archiviati su Object Storage. La soluzione consiste in

* {{site.data.keyword.cos_full}}
* NetApp AltaVault Cloud Storage Gateway da NetApp (in precedenza Riverbed SteelStore)
* Software Veeam Backup & Replication

Se si verifica un evento catastrofico che mette offline il tuo ambiente primario, è possibile creare un sito di ripristino secondario in {{site.data.keyword.cloud}} per ripristinarlo. Puoi distribuire il software Veeam Backup & Replication e un'appliance di archiviazione integrata nel cloud AltaVault per comunicare con {{site.data.keyword.cos_full_notm}} per accedere ai backup precedenti dell'ambiente primario. Il software si connette all'appliance di archiviazione per ripristinare i backup in una nuova ubicazione, portando nuovamente online l'ambiente primario ed evitando ulteriori perdite di tempo di attività.

Gli indirizzi IP in loco vengono conservati quando ripristini le macchine virtuali (VM, Virtual Machine) sul sito di ripristino {{site.data.keyword.cloud_notm}}. Di conseguenza, è importante modificare gli indirizzi IP dopo un ripristino eseguito correttamente oppure progettare un'infrastruttura BYOIP. Per assistenza con l'uno o con l'altro caso, contatta il tuo rappresentante IBM.

La seguente procedura si concentra sull'utilizzo coordinato di un'appliance NetApp AltaVault Cloud Storage, dell'infrastruttura {{site.data.keyword.cloud_notm}} e di Veeam Backup & Replication per ripristinare completamente un ambiente VMware vSphere per cui si è verificato un malfunzionamento. Il software presume che sia stato eseguito almeno un backup dell'ambiente utilizzando le tre tecnologie precedenti e si trova su {{site.data.keyword.cos_full_notm}} al momento del ripristino.

L'“appliance AltaVault” è identificata in due modi diversi. Il primo è “appliance AltaVault in loco”, che si riferisce all'appliance AltaVault in loco originale di cui si è verificato il malfunzionamento e da cui vengono recuperati o ripristinati i backup. Il secondo è “appliance {{site.data.keyword.cloud_notm}} AltaVault”, che fa riferimento all'appliance AltaVault utilizzata per recuperare i backup dall'appliance AltaVault in loco in errore. Per la distribuzione a un ambiente vSphere, l'appliance {{site.data.keyword.cloud_notm}} AltaVault utilizza un utility server.

## Introduzione di una soluzione ibrida
{: #introducing-a-hybrid-solution}

Veeam Backup & Replication abilita una soluzione ibrida, per includere l'appliance di archiviazione integrata nel cloud NetApp AltaVault e {{site.data.keyword.cos_full_notm}}. La preoccupazione principale è quella di creare, gestire e ripristinare da backup di ambienti virtuali. L'appliance di archiviazione integrata nel cloud NetApp AltaVault è una soluzione software per integrare senza soluzione di continuità un ambiente in loco con cloud privati o pubblici. Quando sono utilizzati insieme, Veeam Backup & Replication crea dei backup che l'appliance di archiviazione integrata nel cloud AltaVault memorizza in locale nell'archiviazione in sede e, simultaneamente, esegue una replica su {{site.data.keyword.cos_full_notm}}. Inoltre, il modello di prezzo di pagamento a consumo {{site.data.keyword.cloud}} e la piena integrazione con CDN (contente delivery network) consentono di archiviare e distribuire dati in 24 nodi geograficamente diversi.

### IBM Cloud Object Storage
{: #ibm-cloud-object-storage}

Per accedere ai file di dati di cui è stato eseguito il backup su {{site.data.keyword.cos_full_notm}}, i file di dati vengono richiesti mediante i seguenti modi:

* L'appliance AltaVault che già si trova in loco
* Una appliance AltaVault secondaria che si trova in {{site.data.keyword.cloud_notm}}

Attieniti a questa procedura per distribuire un'appliance AltaVault secondaria in {{site.data.keyword.cloud_notm}} per ripristinare un'appliance AltaVault in loco primaria di cui si verificato il malfunzionamento.

L'ambiente {{site.data.keyword.cloud_notm}} consiste in un singolo host ESXi con archiviazione locale per ospitare ed eseguire l'appliance AltaVault secondaria. L'infrastruttura è rappresentativa dell'architettura a sito singolo di base con un singolo ESXi gestito da un server vCenter in una VSI (Virtual Server Instance) {{site.data.keyword.cloud_notm}}.

Se hai un'infrastruttura più consistente che richiede l'archiviazione condivisa e/o il supporto per queste funzioni

* vSphere HA (High Availability )
* vSphere DRS (Distributed Resource Scheduler)
* vSphere vMotion

#### Prerequisiti
{: #prerequisites-ibm-cloud-object-storage}

Prima di procedere, verifica che siano soddisfatti i seguenti prerequisiti: **Nota:** per questo esempio, è stata utilizzata una versione di prova di un'appliance AltaVault AVA-v8 con AltaVault Cloud-Integrated Storage 4.1.

* Assicurati che l'ambiente esistente consista in un singolo host ESXi gestito da un server vCenter in una VSI {{site.data.keyword.cloud_notm}}.
* Conoscenza della tecnologia VMware Sphere e amministrazione degli ambienti vSphere ESXi. Questa conoscenza include, tra l'altro, l'uso del client web vSphere e l'assegnazione di risorse hardware per includere rete e archiviazione.

#### Ordina due reti private portatili
{: #order-two-portable-private-networks}

AltaVault richiede che le interfacce di rete si trovino su reti differenti all'interno dell'ambiente. Utilizza la seguente procedura per ordinare due reti private portatili mediante il {{site.data.keyword.slportal_full}}.

1. Accedi al [{{site.data.keyword.slportal_full}} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://control.softlayer.com/){: new_window} utilizzando credenziali univoche.
2. Fai clic su **Account > Place an Order**.
3. Seleziona la sezione **Network** e fai clic su **Subnets/IPs > Order**.
4. Dal menu a discesa, seleziona **Portable Private**. La selezione Portable Private attiva un'opzione per selezionare il numero di indirizzi IP privati portatili da visualizzare. **Nota:** {{site.data.keyword.cloud_notm}} riserva automaticamente almeno tre indirizzi IP nel blocco di indirizzi per ogni rete privata portatile. Gli indirizzi riservati sono per l'indirizzo di rete, l'indirizzo gateway e l'indirizzo di broadcast. Questi indirizzi devono essere associati direttamente alla VLAN (Virtual LAN).<!--If your IBM Cloud pod supports Hot Standby Ready Protocol (HSRP), then the number of usable IP addresses is reduced. For instance, if you select **4 Portable Private IP Addresses** will yield only one usable IP address or zero if the pod supports HSRP.--> Per ulteriori informazioni sugli indirizzi IP portatili, vedi [Introduzione alle sottoreti e agli IP](/docs/infrastructure/subnets?topic=subnets-getting-started-with-subnets-and-ips).
5. Fai clic su **Continue** dopo aver selezionato il numero di indirizzi IP privati portatili.
6. Seleziona la VLAN dove devono essere instradati gli indirizzi IP privati portatili e fai clic su **Continue**.
7. Completa le informazioni di contatto richieste e fai clic su **Place Order**.
8. Ripeti il processo di ordinazione per ottenere la seconda rete privata portatile richiesta.
9. Fai clic su **Network > IP Management > Subnets** dalla pagina del {{site.data.keyword.slportal}} per visualizzare le tue reti private portatili e i tuoi indirizzi IP dopo che ne è stata eseguita l'assegnazione. Ti consigliamo di tenere traccia delle tue assegnazioni di indirizzi IP. Utilizza la sezione **Notes** della pagina **Subnets** perché ogni indirizzo IP rifletta la macchina a cui è assegnato.

#### Configurazione di vSphere
{: #configuring-vsphere}

Devi configurare vSphere per riflettere l'aggiunta delle reti private portatili e assicurarti che siano logicamente separate.

1. Vai al tuo ambiente vSphere e crea un gruppo di porte di macchina virtuale per riflettere l'aggiunta di uno dei blocchi di IP portatili (due in totale).

Il gruppo di porte di macchina virtuale è etichettato come **Share Network**. Un blocco di IP portatili viene utilizzato per le VM posizionate sul gruppo di porte di macchina virtuale predefinito (primario). L'altro viene utilizzato per il trasferimento di dati tra un'appliance AltaVault e un punto di montaggio CIFS (Common Internet File System)/SMB (Server Message Block).

## AltaVault Cloud Storage Gateway
{: #altavault-cloud-storage-gateway}

Puoi utilizzare AltaVault per integrare il tuo ambiente in loco con il cloud senza dover scrivere script o applicazioni utilizzando le API REST per {{site.data.keyword.cos_full_notm}}. AltaVault espone un punto di montaggio CIFS/SMB o NFS (Network File System) sul front-end e stabilisce una connessione protetta all'interfaccia {{site.data.keyword.cos_full_notm}} sul back-end. Puoi quindi montare o puntare ai punti di montaggio e copiare i dati negli, e/o recuperare/ripristinare, ambienti dal cloud in modo protetto.<!-- , as shown in Figure 1.-->

<!-- ![Figure 1](images/veeam_restore_fig2.png)
<br/>`Figure 1: AltaVault end-to-end flow with Veeam and IBM Cloud`-->

### Distribuzione di AltaVault in una configurazione di ripristino di emergenza
{: #deploying-altavault-in-a-disaster-recovery-configuration}

Attieniti alla seguente procedura per distribuire AltaVault come una soluzione di ripristino di emergenza con {{site.data.keyword.cos_full_notm}}.

#### Prima di iniziare
{: #before-you-get-started-deploying-altavault-in-a-disaster-recovery-configuration}

Prima di procedere, verifica che siano soddisfatti i seguenti prerequisiti:

* Ottieni una copia dell'appliance virtuale AltaVault e assicurati che si trovi sull'utility server. È un singolo file con un'estensione file OVA. Contatta il tuo rappresentante NetApp per l'appliance oppure scarica una versione di prova di 90 giorni dal [sito web di NetApp AltaVault ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](http://www.netapp.com/us/products/protection-software/altavault/){: new_window}.
* Disponi di un ambiente vSphere ESXi 5.5 in loco esistente con i requisiti minimi di CPU, memoria e spazio su disco disponibili per l'appliance AltaVault. Se utilizzi la versione di prova, questi sono i requisiti di risorse: quattro vCPU (virtual CPU), 24 GB di memoria e fino a 8 TB di spazio su disco.
* Disponi di due NIC (network interface controller) da 10 Gbps disponibili nell'ambiente vSphere. Un NIC viene utilizzato per l'accesso ai dati e altro viene utilizzato per la replica dei dati a {{site.data.keyword.cos_full_notm}}.
* Disponi di due reti private portatili che corrispondono ai due NIC definiti nell'ambiente vSphere. La rete di replica non può essere assegnata alla stessa rete della rete di accesso ai dati, cosa che può creare un loop di instradamento.
* Disponi di credenziali {{site.data.keyword.cos_full_notm}}. Queste credenziali includono un nome utente {{site.data.keyword.cloud_notm}}, un nome utente {{site.data.keyword.cos_full_notm}} e la chiave API associata al nome utente {{site.data.keyword.cloud_notm}}.
* Sospendi la replica dei dati sull'appliance AltaVault in loco oppure disconnettila dal contenitore/bucket utilizzato per accedere all'archivio di backup in {{site.data.keyword.cloud_notm}} se l'ambiente in loco è ancora intatto.
  * Utilizza uno dei due metodi di seguito indicati per sospendere o arrestare la replica:
    * Disattiva l'appliance AltaVault in loco.
    * Vai alla console di gestione web dell'appliance AltaVault e fai clic su **Storage > Cloud Settings > Replication**. Seleziona **Suspend Replication**.
    * Ottieni una copia del file di configurazione dell'appliance AltaVault in loco oppure rendilo accessibile tramite un URL. Il file di configurazione è un file .tar.
  * Vai alla console di gestione web dell'appliance AltaVault e fai clic su **Configure > Setup Wizard > Export Configuration > Export Configuration**. Salva il file di configurazione (file .tar) e trasferiscilo all'utility server o rendilo accessibile tramite un URL.
    * Conoscenza della tecnologia VMware Sphere e amministrazione degli ambienti vSphere ESXi. Questa conoscenza include, tra l'altro, l'uso del client web vSphere e l'assegnazione di risorse hardware, quali la rete e l'archiviazione.

### Distribuzione di AltaVault OVA
{: #deploying-altavault-ova-disaster}

Distribuisci AltaVault OVA all'ambiente vSphere utilizzando l'utility server dopo che sono stati soddisfatti tutti i prerequisiti. Le istruzioni per la distribuzione di OVA sono disponibili nel manuale [NetApp AltaVault Installation and Service Guide ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}. La versione di prova di AltaVault viene fornita configurata con quattro CPU virtuali, 6 GB di memoria, e un disco primario da 150 GB. Utilizza il manuale Installation and Service Guide per eseguire questa procedura:

1. Aumentare la quantità di memoria a 24 GB.
2. Aggiungere un dispositivo di archiviazione disco secondario con una dimensione inferiore o uguale a 8 TB per archiviare i dati di backup deduplicati.
3. Assegnare reti private portatili differenti all'appliance AltaVault dopo che sono state modificate le configurazioni di memoria e disco.

I NIC sono assegnati alle seguenti funzioni di interfaccia:

* **Primary**: utilizzato per la gestione dell'appliance AltaVault e per la replica dei dati al cloud. Nell'ambiente di esempio, a esso è assegnato il gruppo di porte **Primary Network**.
* **e0a**: un'interfaccia facoltativa utilizzata per replicare i dati dall'appliance AltaVault al cloud.
* **e0b**: un'interfaccia utilizzata per esportare il punto di montaggio per la condivisione NFS o SMB/CIFS. Nell'ambiente di esempio, a esso è assegnato il gruppo di porte **Share Network**.
* **e0c**: un'interfaccia facoltativa utilizzata per esportare il punto di montaggio per la condivisione NFS o SMB/CIFS. 

Nella configurazione di esempio in questa procedura, l'appliance AltaVault utilizza l'interfaccia **Primary** come interfaccia di replica al cloud e l'interfaccia **e0b** per esportare un punto di montaggio CIFS/SMB. **Nota:** non puoi utilizzare sia una condivisione CIFS/SMB che una condivisione NFS per accedere agli stessi dati. Il protocollo utilizzato deve essere uguale al protocollo utilizzato con l'appliance AltaVault in loco per replicare i dati al cloud.

<!-- ![Figure 2](images/veeam_restore_fig3.png)
<br/>`Figure 2: IBM Cloud setup with VMware`-->

Per ulteriori informazioni sulla distribuzione dell'appliance AltaVault e la configurazione delle impostazioni di VM, vedi il manuale [NetApp AltaVault Installation and Service Guide ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}.

### Configurazione dell'appliance AltaVault
{: #configuring-the-altavault-appliance}

1. Puoi accendere la VM AltaVault dopo che è stata configurata. Ci può volere del tempo perché la VM venga avviata poiché l'appliance AltaVault sta formattando il disco di archiviazione ausiliario.

2. Esegui l'accesso alla console AltaVault utilizzando **admin** come **Username** e **password** come **Password** dopo che l'appliance ha completato il processo di avvio. Puoi modificare queste credenziali dopo aver completato la configurazione iniziale. Vedi la Tabella 1: Valori di configurazione iniziali di AltaVault

3. Dopo che hai eseguito l'accesso all'appliance, ti viene richiesto se desideri utilizzare la procedura guidata per la configurazione. Immetti *y*.
4. Quando ti trovi nella procedura guidata, utilizza le informazioni contenute nella Tabella 1.
5. Premi **Invio** per salvare le tue modifiche.

|Domanda|Risposta|
|---|---|
|Step 1: Admin Password?|Immetti una nuova password di amministratore (non può essere **password**).|
|Step 2: Hostname?|Immetti il nome host appropriato.|
|Step 3: Use DHCP on primary interface?|Immetti **n** oppure **no**.|
|Step 4: Primary IP Address?|Immetti l'indirizzo IP di rete primario. Nella configurazione di esempio, questa è la rete utilizzata per l'interfaccia di gestione (ad es.10.120.108.132).|
|Step 5: Netmask?|Immetti la maschera di rete (ad es. 255.255.255.192).|
|Step 6: Default gateway?|Immetti il gateway predefinito (ad es. 10.120.108.129).|
|Step 7: Primary DNS server?|Immetti il server DNS (Domain Name System) primario nel tuo ambiente.|
|Step 8: Domain name?|Immetti il nome dominio del tuo ambiente (ad es. testenv.org).|
{: caption="Tabella 1. Valori di configurazione iniziali di AltaVault" caption-side="top"}

### Configurazione di AltaVault per IBM Cloud Object Storage
{: #configuring-altavault-for-ibm-cloud-object-storage}

L'appliance AltaVault deve essere connessa al servizio {{site.data.keyword.cos_full_notm}} dopo essere stata configurata. Nella configurazione in loco, il DNS pubblico viene utilizzato per stabilire una connessione al servizio Object Storage. In questa configurazione, viene invece utilizzato il nome DNS interno.

1. Apri un browser web e immetti l'indirizzo IP dell'appliance AltaVault.
2. Accedi alla console con le credenziali di amministratore. Al momento dell'accesso iniziale, verrà visualizzato il **Wizard Dashboard**.
3. Seleziona **System Settings**, verifica che tutte le impostazioni di rete siano corrette e regola il uso orario per riflettere quello del tuo ambiente.
4. Seleziona **Next > Save and Apply > Exit**. Ritorni al **Wizard Dashboard** e vieni disconnesso dalla tua sessione del browser corrente.
5. Esegui nuovamente l'accesso all'appliance AltaVault, fai clic su **Settings > Setup Wizard > Import Configuration** e specifica l'ubicazione del file di configurazione dell'appliance AltaVault in loco.
6. Seleziona **Import Shared Data Only**, che importa solo le impostazioni condivise in comune, come ad esempio le impostazioni di cloud e email.
7. Immetti la passphrase per la chiave di crittografia specificata durante la creazione dell'appliance AltaVault in loco, se ne è stata impostata una, nella casella di testo **Key Passphrase**.
8. Fai clic su **Import Configuration > Exit**.

<!--The AltaVault appliance web console displays a message indicating a successful import. If the import was not successful, verify that the configuration file is in the correct format and that the passphrase is correct before trying again.-->

Hai importato la configurazione dell'appliance AltaVault in loco nell'appliance {{site.data.keyword.cloud_notm}} AltaVault.

Utilizza la seguente procedura per modificare le impostazioni cloud dell'appliance {{site.data.keywrod.cloud_notm}} AltaVault per darle accesso al servizio {{site.data.keyword.cos_full_notm}} mediante la rete privata.

1. In AltaVault, vai a **Storage > Cloud Settings > Cloud** e modifica il nome host per riflettere l'indirizzo privato del servizio {{site.data.keyword.cos_full_notm}}.
  * La sintassi del nome di rete privata è <ubicazione>.objectstorage.service.networklayer.com, dove *<ubicazione>* designa il nome di data center abbreviato (esempio: mel01 per il data center Melbourne 01).
2. Fai clic su **Apply**. L'appliance AltaVault prova a stabilire una connessione al servizio {{site.data.keyword.cos_full_notm}}. Se questo tentativo di connessione non riesce, verifica che le impostazioni del provider cloud siano corrette prima di ritentare la connessione.

Dopo che hai stabilito una connessione a {{site.data.keyword.cos_full_notm}}, l'appliance deve essere messa in modalità di recupero per la sincronizzazione del contenuto dei metadati dei dati di backup originali da {{site.data.keyword.cos_full_notm}}.

Utilizza la seguente procedura per mettere l'appliance AltaVault in modalità di recupero.

L'appliance AltaVault sincronizza i dati da {{site.data.keyword.cos_full_notm}}. **Nota:** il tempo necessario per completare la sincronizzazione dipende dalla dimensione e dal numero di backup di cui si sta eseguendo il ripristino.

1. Vai all'appliance AltaVault, esegui l'accesso e immetti i comandi mostrati nella Figura 3.<br/>![Figura 3](images/veeam_restore_fig5.png)<br/>`Figura 5: Comandi della modalità di recupero`
2. Ritorna alla console web dell'appliance AltaVault dopo che il processo di sincronizzazione è stato completato.
3. Verifica che **Storage Optimization Service** sia in esecuzione (**running**) e che il suo stato (**Status**) indichi che è pronto (**ready**). Potrebbe volerci qualche minuto perché lo stato (**Status**) cambi indicando una condizione di pronto (**ready**).

### Configura il punto di montaggio CIFS/SMB in AltaVault
{: #configure-the-cifs-smb-mount-point-in-altavault}

Dopo che la connessione interna a {{site.data.keyword.cos_full_notm}} è stata stabilita, configura l'interfaccia **e0b** per accedere a un punto di montaggio CIFS/SMB.

1. Nella console web AltaVault, vai a **Settings > Data Interfaces**.
2. Espandi l'interfaccia **e0b** e seleziona **Enable Data Interface**, se necessario.
3. Immetti l'indirizzo IP, la maschera di sottorete e il gateway che desideri utilizzare per montare la condivisione CIFS/SMB. Assicurati di utilizzare una sottorete diversa da quella utilizzata per l'interfaccia primaria.
4. Lascia il valore **MTU** predefinito di **1500 bytes**.
  * Anche se la MTU (maximum transmission unit) predefinita è 1.500, puoi modificarla in 9.000 se stai utilizzando i frame jumbo. Il tuo host ESXi e la tua infrastruttura fisica devono supportare i frame jumbo. Per impostazione predefinita, {{site.data.keyword.cloud_notm}} supporta una dimensione di MTU di 9.000 byte senza che occorra alcuna modifica della configurazione.
5. Fai clic su **Apply**. 

L'appliance AltaVault ha ora una configurazione minima che consente le comunicazioni tra essa, {{site.data.keyword.cos_full_notm}} e Veeam Backup & Replication.

## Veeam Backup & Replication
{: #veeam-backup-replication-recover}

Il software Veeam Backup & Replication fornisce delle funzionalità di backup, replica e ripristino complete per le VM e i loro dati. Veeam Backup & Replication può essere completamente integrato con un'appliance AltaVault Cloud Gateway, offrendo così un'esperienza di backup e ripristino senza soluzione di continuità.

### Distribuisci Veeam Backup & Replication
{: #deploy-veeam-backup-replication}

Nell'esempio viene utilizzata una versione di prova di Veeam Backup & Replication versione 8.

#### *Prerequisiti*
{: #prerequisites-deploy-veeam-backup-replication}

Prima di procedere con la distribuzione, assicurati che siano soddisfatti i seguenti prerequisiti:

* Disponi di un'appliance AltaVault esistente configurata per l'utilizzo con IBM Cloud Object Storage e Veeam Backup & Replication.
* Ottieni una copia di Veeam Backup & Replication per gli ambienti VMware, che è un singolo file eseguibile. Contatta il tuo rappresentante Veeam per una copia oppure scarica una [versione di prova di 30 giorni![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](http://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}.
* Ottieni un file di licenza per l'utilizzo con Veeam Backup & Replication. Nella maggior parte dei casi, questo file viene inviato per email all'indirizzo email che era stato utilizzato per scaricare Veeam Backup & Replication. Se non hai ricevuto questo file, contatta il rappresentante di Veeam. <br/><br/>Il file di licenza viene utilizzato per attivare la piena funzionalità di Veeam Backup & Replication, Se questo file non viene fornito durante l'installazione del programma, tutte le funzioni e tutta la funzionalità sono ripristinate a quelle della versione di prova di 30 giorni.
* Disponi di una VSI (virtual server instance) di cui è stato eseguito il provisioning in IBM Cloud con le specifiche di cui alla Tabella 2 **Nota:** il sistema operativo installato deve essere una versione a 64 bit.

||Minimo|Consigliato|
|---|---|---|
|**Sistema operativo**|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>Windows Server 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|<ul><li>Windows Servers 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>Windows 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|
|**# di core o vCPU**|2|4|
|**Memoria**|RAM di base di 4 GB più 500 MB per ogni lavoro di backup e ripristino simultaneo. |RAM di base di 16 GB più 4 GB per ogni lavoro di backup e ripristino simultaneo. |
|**Spazio su disco**|2 GB per l'installazione del prodotto, 10 GB per ogni 100 VM per la cartella del catalogo del sistema guest (dati persistenti). |2 GB per l'installazione del prodotto, 10 GB per ogni 100 VM per la cartella del catalogo del sistema guest (dati persistenti).|
|**Rete**|LAN da 1 per backup e replica in sito, VLAN da 1 Mbps per backup e replica non in sito. |LAN da 1 per backup e replica in sito, VLAN da 1 Mbps per backup e replica non in sito.|
{: caption="Tabella 2. Requisiti di sistema per la VSI {{site.data.keyword.cloud_notm}}" caption-side="top"}

Esegui il provisioning della VSI nello stesso data center dell'appliance AltaVault e dell'ambiente vSphere. La VSI deve avere accesso al vCenter e all'appliance AltaVault.

### Installazione di Veeam Backup & Replication
{: #installing-veeam-backup-replication-disaster}

Utilizza la seguente procedura per installare Veeam Backup & nella VSI {{site.data.keyword.cloud_notm}} dopo che sono stati soddisfatti tutti i prerequisiti.

1. Fai doppio clic sull'eseguibile di programma e fai clic su **Veeam Backup & Replication – Install**. Si apre la procedura guidata di configurazione.
* Fai clic su **Next** e seleziona **I accept the terms in the license agreement** .
* Fai clic su **Next** e specifica l'ubicazione del file di licenza ottenuto in Deploy Veeam Backup & Replication.
* Fai clic su **Next** e seleziona quali componenti di Veeam Backup & Replication desideri installare e la loro ubicazione di installazione nella schermata **Program Features**. **Nota:** **Veeam Backup & Replication** ed **Veeam Backup Catalog** sono componenti obbligatori.
* Fai clic su **Next**.<br/>La procedura guidata di configurazione esegue una serie di controlli per accertarsi che tutti i framework di programmi e i componenti di supporto richiesti siano installati. Se manca qualche componente, la procedura guidata di configurazione si offre di eseguirne l'installazione. <!--Click **Install** if this is the case.-->
2. Verifica che tutti i componenti abbiano superato (**Passed**) il controllo del sistema e fai clic su **Next**.
3. Seleziona l'account (utente) del servizio (**Service (user) Account**) che desideri venga eseguito dal servizio di backup Veeam. L'account di servizio predefinito è l'account di sistema locale (**LOCAL SYSTEM account**). Fai clic su **Next**.
4. Seleziona la **SQL Server Instance** che desideri utilizzare per creare e archiviare i database Veeam Backup & Replication. Per ulteriori informazioni, contatta il tuo amministratore del database. Fai clic su **Next**.
5. Immetti la porta del servizio di catalogo (**Catalog service port**) (9393) e la porta del servizio di backup Veeam (**Veeam Backup service port**) (9392). Fai clic su **Next**.
6. Seleziona le directory dove desideri archiviare il catalogo del file system guest (dati persistenti) e la cache di scrittura vPower NFS (dati non persistenti). Fai clic su **Next**.
7. Verifica che tutte le impostazioni e tutti i valori siano corretti e fai clic su **Install**. Una volta eseguita l'installazione, fai clic su **Finish**.

### Configurazione di Veeam Backup & Replication
{: #configuring-veeam-backup-replication}

Dopo che Veeam Backup & Replication è stato installato, sei pronto a stabilire una connessione all'appliance AltaVault.

1. Avvia Veeam Backup & Replication.
2. Nella parte inferiore sinistra dello schermo, fai clic su **Backup Infrastructure**.
3. Nella finestra **Backup Infrastructure**, fai clic su **Managed Servers > Managed servers**.
4. Dal menu barra multifunzione superiore, fai clic su **Add Server** e fai doppio clic su **VMware vSphere**.
5. Immetti l'indirizzo IP privato del server vSphere e fai clic su **Next**.
6. Immetti le credenziali dell'account di un account locale con privilegi di amministratore sul server vSphere specificato in precedenza. **Nota:** il nome utente dell'account deve essere in formato DOMINIO\UTENTE per gli account di dominio oppure in formato HOST\UTENTE per gli account locali. Per aggiungere un account, fai clic su **Add** e immetti il nome utente e la password appropriati. Non modificare la porta dei servizi web VMware predefinita se non dietro indicazione esplicita del tuo amministratore di rete.<!-- otherwise during [Veeam Backup & Replication installation](#_Installing_Veeam_Backup).--> Fai clic su **Next**.
  * Veeam Backup & Replication prova a stabilire una connessione al server vSphere. Se il tentativo di connessione non riesce, controlla che l'account esista e che disponga di privilegi di amministratore sul server vSphere prima di riprovare.
7. Fai clic su **Finish**.
8. Verifica che il server vSphere sia stato aggiunto correttamente facendo clic su **Managed Servers > VMware vSphere**.

### Aggiunta di un repository di backup a Veeam Backup & Replication
{: #adding-a-backup-repository-to-veeam-backup-replication-disaster}

Attieniti alla seguente procedura per creare un nuovo repository di backup per archiviare i backup ripristinati sull'appliance AltaVault. **Nota:** per l'esempio viene utilizzata una condizione CIFS/SMB perché il server di backup Veeam Backup & Replication deve essere ospitato su una macchina che sta eseguendo Windows. Il protocollo di condivisione utilizzato per ripristinare i backup nell'appliance {{site.data.keyword.cloud_notm}} AltaVault deve essere identico al protocollo di condivisione utilizzato per quegli stessi backup nell'appliance AltaVault in loco.

1. Nell'angolo inferiore sinistro, fai clic su **Backup Infrastructure > Backup Repositories**.
2. Dal menu barra multifunzione superiore, fai clic su **Add Repository**.
3. Dai al repository un nome (**Name**) univoco e una descrizione (**Description**) appropriata. Fai clic su **Next**.
4. Seleziona il tipo di condivisione che corrisponde a quello utilizzato nell'appliance AltaVault in loco. Esempio: se il tipo di condivisione utilizzato nell'appliance AltaVault in loco è CIFS/SMB, seleziona **Shared folder**.
5. Specifica l'ubicazione dove desideri che vengano ripristinati sull'appliance AltaVault i backup della condivisione CIFS/SMB. La condivisione ha lo stesso nome della condivisione tramite la quale erano stati elaborati i backup sull'appliance AltaVault in loco. Ad esempio, se la condivisione denominata `cifs_test1` viene utilizzata per i backup nell'appliance AltaVault in loco, specifica l'ubicazione della condivisione `cifs_test1` sull'appliance {{site.data.keyword.cloud_notm}} AltaVault.
  * Apri un browser web e immetti l'indirizzo IP dell'appliance Web per determinare l'ubicazione.
  * Vai a **Storage > CIFS** e prendi nota del percorso di condivisione (**Share Path**) della condivisione. **Nota:** questo NON è uguale al percorso locale della condivisione. Il formato del percorso di condivisione è `\\<AltaVault appliance hostname>\<share name>`. Sostituisci il nome host dell'appliance AltaVault nel percorso di condivisione con l'indirizzo IP dell'interfaccia di rete e0b (il punto di montaggio della condivisione) dell'appliance AltaVault.<br/>
   * Fai clic su **Settings > Data Interfaces** nella finestra di gestione dell'appliance AltaVault per trovare l'indirizzo IP dell'interfaccia **e0b**. Per ulteriori informazioni sulla configurazione dei montaggi IFS/SMB, vedi Configurazione del punto di montaggio CIFS/SMB in AltaVault.
   * Il percorso di condivisione specificato in Veeam Backup & Replication sarebbe `\\10.120.108.133\cifs_test1`, non `\\restore-appliance\cifs_test1`.
6. Ritorna a Veeam Backup & Replication, immetti il percorso di condivisione del punto di montaggio nel campo **Shared folder** e fai clic su **Next**. Veeam Backup & Replication prova a stabilire una connessione con il punto di montaggio. Se la connessione non riesce, torna indietro e verifica che le impostazioni di rete per l'appliance AltaVault siano corrette prima di riprovare.
7. Immetti un valore per limitare le attività simultanee massime (**Limit maximum concurrent**) al numero di risorse disponibili nella schermata **New Backup Repository**. Questo valore è il numero massimo di attività che un proxy di backup può inviare alla condivisione selezionata. Il numero predefinito di attività simultanee è quattro. AltaVault consiglia di iniziare con cinque attività simultanee e di aumentare o diminuire questo valore come consentito dalle risorse.
8. Fai clic su **Next**.
9. Specifica le impostazioni vPower NFS facoltative nella schermata **vPower NFS**. Se la casella di spunta **Enable vPower NFS server** viene lasciata non selezionata, Veeam Backup & Replication utilizza vPower per il recupero e la verifica del recupero. Fai clic su **Next**.
10. Seleziona **Import existing backups automatically**, a meno che non utilizzi la selezione manuale dei backup VM.
11. Verifica che tutte le impostazioni siano corrette e fai clic su **Next**.
* Fai clic su **Finish** per uscire dalla procedura guidata.
12. Fai clic su **Yes** per continuare.

Veeam Backup & Replication è configurato e può iniziare a ripristinare i backup.

### Ripristino dell'ambiente
{: #restoring-the-environment}

Attieniti alla seguente procedura per ripristinare completamente un ripristino VM.

1. Nella parte inferiore sinistra dello schermo, fai clic su **Backup & Replication**.
2. Dal menu barra multifunzione superiore, fai clic su **Restore**.
3. Seleziona **Entire VM (including registration)**. **Nota:** non eseguire alcuna selezione in **Restore from replica**. Fai clic su **Next**.
4. Fai clic su **Add VM > From Backup**. Vedi le VM che erano state precedentemente replicate su {{site.data.keyword.cos_full_notm}} mediante l'appliance AltaVault in loco e la condivisione CIFS/SMB selezionata. Fai clic su **Add > Next**.
5. Ripristina le VM in una nuova ubicazione <!--[because the original location of the VM(s) failed]--> selezionando **Restore to a new location, or with different settings**. Fai clic su **Next**.
6. Dalla schermata **Host**, seleziona un host di destinazione per ogni VM che stai ripristinando. Seleziona una VM e fai quindi clic su **Host**.
7. Seleziona l'host di destinazione da **Select Host** e fai clic su **OK**.<br/>Veeam Backup & Replication prova a contattare gli host di destinazione per accertarsi che siano online e pronti a ricevere dati. Se il tentativo non riesce, verifica che tutte le impostazioni di rete siano corrette prima di riprovare. Una volta completato il controllo, fai clic su **Next**.
8. Seleziona un pool di risorse (**Resource Pool**) facoltativo per ogni VM. Fai clic su **Next**.
9. Seleziona un tipo di disco e un archivio dati di destinazione per ogni VM che stai ripristinando. Fai clic su **Next**. <br/>Veeam Backup & Replication prova a convalidare gli archivi dati di destinazione. Se la convalida non riesce, controlla che l'archivio dati abbia una capacità sufficiente per le VM di cui stai eseguendo il ripristino e verifica che tutte le impostazioni di rete siano corrette prima di ritentare la connessione.
10. Seleziona una cartella di destinazione sugli archivi dati di destinazione per ogni VM che desideri ripristinare. Fai clic su **Next**.
11. Specifica le connessioni e le impostazioni di rete per ogni VM. Fai clic su **Next**.
12. Immetti un motivo del ripristino (**Restore reason**) facoltativo fornendo un motivo per cui stai eseguendo l'operazione di ripristino. Fai clic su **Next**.<br/>Veeam Backup & Replication prova a convalidare le VM che stai ripristinando. Prima di procedere, attendi che questa convalida termini con esito positivo.
13. Verifica le operazioni e le impostazioni di ripristino nella finestra **Backup & Replication** e fai clic su **Finish**. Veeam Backup & Replication inizia automaticamente a ripristinare le VM selezionate.

## Passi successivi
{: #next-steps-recovering-your-vmware-vsphere-environment}

<!--The recent rise in Object Storage can be heavily attributed to enterprises' desire to reduce or even eliminate the operational and capital expenditures associated with comparatively expensive on-premises storage. While Object Storage is a highly cost-effective solution, it introduces a new problem of how to access stored files. In the case of stored data backups, the issue is how to use them to recover an environment that has failed for whatever reason.-->

<!--This problem is handily resolved by Veeam Backup & Replication, along with a NetApp AltaVault Cloud Gateway appliance, and {{site.data.keyword.cos_full_notm}}. When used together, they create a seamless backup and recovery experience that can be tailored to fit a wide variety of environmental requirements and constraints. By following the steps outlined in this procedure, enterprises can quickly realize the benefits of cloud-based storage, while reducing the complexities involved in shifting from on-premises storage to Object Storage using the three aforementioned technologies.-->

Per ulteriori informazioni sui componenti di questa soluzione ibrida, vedi:

* [Sito web di NetApp AltaVault ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](http://www.netapp.com/us/products/protection-software/altavault/){: new_window}
* [Sito web di Veeam Backup & Replication ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}
* [Sito web di IBM Cloud Object Storage ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://www.ibm.com/cloud/storage){: new_window}

Per *utility server* si intende un server che contiene il VMware vSphere Client utilizzato per accedere a una rete privata e attraverso il quale viene distribuita l'appliance AltaVault utilizzata per le operazioni di recupero dei dati.
