---
copyright:
  years: 2014, 2019
lastupdated: "2019-01-15"

subcollection: virtualization

keywords: vmware, ESXi
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:table: .aria-labeledby="caption"}

# Architettura di riferimento VMware a singolo sito avanzata
{: #advanced-single-site-vmware-reference-architecture}

Utilizza la seguente procedura per creare un ambiente vSphere a singolo sito avanzato. La seguente procedura definisce una distribuzione di architettura di riferimento {{site.data.keyword.cloud}} utilizzando le prassi ottimali consigliate di VMware.

L'architettura di riferimento è concepita per il provisioning di un ambiente che utilizza l'archiviazione condivisa, VMware HA (High Availability) e vSphere DRS (Distributed Resource Scheduler) e un firewall o un gateway {{site.data.keyword.cloud_notm}}. L'architettura di riferimento VMware a singolo sito avanzata è adatta per la maggior parte delle distribuzioni di produzione e può essere ridimensionata come reso necessario dal carico di lavoro e può sostituire un'implementazione in loco o estendersi in uno scenario IT ibrido.

## Panoramica dell'ambiente
{: #environment-overview}

L'ambiente Vmware avanzato che viene descritto consiste in un singolo data center che gestisce due cluster separati: gestione e capacità. La configurazione può essere impostata utilizzando un singolo cluster a quattro nodi che dipende dai requisiti. Il cluster di gestione contiene le seguenti macchine virtuali (VM, Virtual Machine) che vengono utilizzate per gestire l'infrastruttura:

* Appliance VMware vCenter Server 5.5 o 6.0
* Microsoft Windows 2012 R2 Standard con ruoli Active Directory e DNS (Domain Name System)

Il cluster di capacità contiene le risorse e l'infrastruttura necessarie per creare ed eseguire le macchine virtuali. Per le reti, l'ambiente consiste in tre VLAN interne e private e una singola VLAN pubblica utilizzata per la comunicazione esterna. La Tabella 1 specifica i tipi di VLAN e i nomi di VLAN utilizzati nell'ambiente.

|Tipo di VLAN|Nome di VLAN|Descrizione|                                                                             |
|---|---|---|
|Privata primaria| Management| Assegnata per gestire e accedere ai server virtuali e host ESXi fisici.|
|Privata primaria| Storage| Assegnata per gestire e accedere all'archiviazione condivisa collegata a ciascun host ESXi.|
|Privata primaria| VM Access | Assegnata alle macchine virtuali che vengono eseguite su ciascun host ESXi.|
|Pubblica primaria| Public | Assegnata alle macchine virtuali o altri dispositivi che richiedono l'accesso dalla rete pubblica. |
<caption>Tabella 1. VLAN primarie</caption>

Per l'archiviazione condivisa, puoi utilizzare OS Nexus QuantaStor, un server di archiviazione condivisa a singolo tenant, oppure i servizi di archiviazione {{site.data.keyword.cloud_notm}} Endurance o Performance. In entrambi i casi, il dispositivo di archiviazione condivisa viene utilizzato per archiviare le VM su entrambi i cluster di gestione e capacità. Per ulteriori informazioni sulle opzioni di archiviazione, vedi [{{site.data.keyword.cloud_notm}} Storage solutions ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://www.ibm.com/cloud/storage){: new_window}.

L'ambiente di archiviazione è configurato per supportare i volumi NFS.

## Passo 1 Ordinazione di VLAN pubbliche e private primarie
{: #step-1-ordering-primary-public-and-private-vlans}

**Nota: ** gli ordini di VLAN sono soggetti a revisione e approvazione. Non sono presenti condizioni o requisiti specifici che contrassegnino un ordine VLAN perché venga approvato automaticamente. Gli ordini VLAN possono essere rifiutati per diversi motivi.

In questo passo vengono create quattro VLAN: una per la gestione, una per l'archiviazione, una per le VM e una per l'accesso pubblico. Ti consigliamo di creare queste quattro VLAN prima di ordinare i tuoi server. Ordinare prima le VLAN garantisce che i server siano posizionati sulle VLAN corrette e nel data center corretto.

L'ambiente consiste in cinque host ESXi (due per il cluster di gestione e tre per il cluster di capacità) e un singolo server virtuale. La VLAN di gestione privata consiste in una sottorete di 16 indirizzi IP per supportarla. La VLAN privata primaria per l'archiviazione e il traffico di VM consiste in otto indirizzi poiché l'ambiente contiene un singolo server di archiviazione e le VM utilizzano una sottorete portatile. Se è necessario un intervallo di sottoreti più grande per la rete di gestione, regola l'intervallo calcolando il numero di host ESXi e moltiplicando per 2. Assicurati inoltre di specificare il data center per cui vengono create queste VLAN.

Dopo che hai eseguito l'accesso a {{site.data.keyword.cloud_notm}}, apri un ticket di supporto per le VLAN di gestione e di VM selezionando **Support > Add Ticket**. Completa i campi con le informazioni presenti nella **Tabella 2:**.

|Campo|Valore|
|---|---|
|Oggetto |Domanda di rete privata|
|Titolo |Ordina le VLAN|
|Dettagli |Esegui il provisioning di tre VLAN private primarie e una VLAN pubblica primaria. Associa il seguente schema di indirizzamento per ciascuna VLAN:<br/><ul><li>Associa 1x18 (16 indirizzi) per la VLAN privata primaria</li><li>Associa 2x29 (otto indirizzi) per la VLAN privata primaria</li><li>Associa 1x29 (otto indirizzi) per la VLAN pubblica primaria</li></ul>|
|Perché ho bisogno di queste VLAN aggiuntive?|Per posizionare host, archiviazione e VM su una rete diversa per un ambiente VMware.
|Ho bisogno di VLAN pubbliche o private?|Private e pubbliche|
|Di quante VLAN ho bisogno?|Privata = 3, Pubblica = 1|
|Di quanti indirizzi IP ho bisogno?|8 - 16 per ciascuna VLAN|
|Dietro quale router devono trovarsi le VLAN?|Non importa che le VLAN siano tutte nello stesso pod.|
|In quale pod si devono trovare le VLAN?|Vanno tutte nello stesso pod in <NOME DATA CENTER>.|
<caption>Tabella 2. Informazioni sui ticket di supporto</caption>

Dopo che è stato eseguito il provisioning delle VLAN, annota i numeri di VLAN, l'intervallo di sottoreti e il gateway e assegnale alle reti vSphere logiche. Puoi utilizzare il foglio di lavoro in Appendice A: Foglio di lavoro della VLAN per registrare la VLAN e le informazioni associate. Ad esempio:

|Tipo di VLAN|Numero VLAN|Intervallo IP |Gateway|Scopo|
|---|---|---|---|---|
|Privata primaria|1101|10.X.Y.Z/28|10.X.Y.1| Gestione|
|Privata primaria|1102|10.A.B.C/29|10.A.B.1|Archiviazione|
|Privata primaria|1103|10.Q.R.S/29|10.Q.R.1|Macchine virtuali|
|Pubblica primaria|2101|75.S.T.U/29|75.S.T.1|Accesso pubblico|
<caption>Tabella 3. Esempio di VLAN primaria</caption>

**Nota:** continua al passo successivo solo dopo che è stato eseguito il provisioning delle VLAN.

## Passo 2 Ordinazione di indirizzi IP privati portatili
{: #step-2-ordering-portable-private-ip-addresses}

Nel Passo 2, viene fatta una richiesta per creare le sottoreti private portatili per le VM di gestione, l'accesso all'archiviazione del kernel di VM e le VM nel cluster di capacità. Devi determinare di quanti indirizzi hai bisogno per ciascuna sottorete VLAN. Nell'ambiente rappresentativo, ordina i seguenti indirizzi:

* VLAN di gestione - 1x8 indirizzi /29 – un indirizzo per l'appliance vCenter; un indirizzo per DNS e Active Directory.
* VLAN di archiviazione - 1x16 indirizzi /28 – crea due sottoreti sulla stessa VLAN per l'archiviazione e due porte di kernel VM su ciascun host ESXi utilizzando sottoreti diverse per accedere ai dispositivi di archiviazione condivisi.
* VLAN di macchine virtuali (VM) - 1x32 indirizzi /27 – questi indirizzi vengono utilizzati per assegnare gli indirizzi IP alle VM nel cluster di capacità.

Quando ordini una quantità, assicurati di valutare quanti indirizzi IP sono necessari entro i prossimi 30 giorni e 6 mesi. È anche importante notare che {{site.data.keyword.cloud_notm}} riserva tre o quattro indirizzi IP per ogni blocco di sottoreti portatili. Di conseguenza, ordinare quattro reti di indirizzi IP, un indirizzo IP o zero se il pod supporta HSRP (Hot Standby Router Protocol).

Utilizza la seguente procedura per ordinare un blocco di indirizzi IP portatili per ogni VLAN per ogni sottorete che desideri creare:

1. Apri una finestra del browser e accedi a {{site.data.keyword.cloud_notm}}.
2. Seleziona **Account > Place an Order**.
3. Nella finestra a comparsa, vai a **Network > Subnets / IPs > Order**.
4. Dal menu a discesa, seleziona **Portable Private**.
5. Seleziona **XX Portable Private IP address** e fai clic su **Continue**. **Nota:** _XX_ specifica il numero di indirizzi IP.
6. Seleziona la VLAN da associare al blocco di indirizzi IP e fai clic su **Continue**.
7. Finisci di compilare le informazioni a schermo e fai clic su **Continue**.

La creazione di indirizzi IP è abbastanza rapida e viene visualizzata selezionando **Subnets** da **Network > IP Management**. Puoi registrare questi indirizzi IP nel foglio di lavoro trovato nell'Appendice A: Foglio di lavoro della VLAN.

## Passo 3 Ordinazione di un server virtuale 
{: #step-3-ordering-a-virtual-server}

Viene eseguito il provisioning di un server virtuale Windows 2012 R2 Standard da utilizzare come un server di utilità per le ISO e fornire l'accesso all'ambiente in questo passo.

1. Apri una finestra del browser e accedi a {{site.data.keyword.cloud_notm}}.
2. Seleziona **Account > Place an Order**.
3. Vai a **Virtual Server > Hourly or Monthly**.
4. Seleziona il data center appropriato (dove sono state create le VLAN) per eseguire il provisioning del server virtuale e specificare le seguenti specifiche per ogni opzione:
  * Istanza di calcolo - Core 1x2.0 GHz
  * RAM: 4 GB
  * Sistema operativo: Windows Server 2012 R2 Standard Edition (64-bit)
  * Primo disco: 100 GB (SAN)
  * Velocità porta di uplink - 1 Gbps di uplink di rete privata e pubblica
5. Fai clic su **Continue Your Order** e seleziona le VLAN di backend e frontend sulla schermata **Order Summary and Billing**. **Nota:** la selezione delle VLAN è importante in modo che il programma di utilità sia inserito nel pod corretto all'interno del data center. Per l'ambiente di esempio, la VLAN di backend è la VLAN di gestione (1101) mentre la VLAN di frontend è la VLAN pubblica (2101)
6. Immetti un nome host e di dominio per il server e fai clic su **Place Order**.

## Passo 4 Ordinazione di host ESXi e gateway / firewall
{: #step-4-ordering-esxi-hosts-and-gateway-firewall}

Devi ordinare gli host ESXi e l'appliance gateway e firewall Brocade vRouter (Vyatta) durante il provisioning del server virtuale. Ordina tutti questi server insieme così che vengano inseriti nello stesso pod contemporaneamente. Ordinare l'hardware in momenti diversi può comportare che gli host e i firewall siano in pod separati all'interno di un data center {{site.data.keyword.cloud_notm}}.

### Host ESXi
{: #esxi-hosts}

Per ogni host ESXi che viene ordinato per l'ambiente, VMware ESXi 5.5 è il sistema operativo. Se vuoi utilizzare le licenze {{site.data.keyword.cloud_notm}} vSphere, ti verranno addebitate delle spese mensili basate sull'utilizzo.

Un'altra opzione è di installare ESXi utilizzando la tua ISO. Le istruzioni per questo processo possono essere trovate in [Installazione di VMware vSphere ESXi tramite la console remota e il supporto virtuale](/docs/infrastructure/vmware?topic=VMware-installing-vsphere-esxi#installing-vsphere-esxi). Se vuoi installare ESXi utilizzando la tua ISO, assicurati di selezionare **No Operating System** come sistema operativo per gli host di gestione e capacità durante il processo di ordinazione.

**Nota:** questa implementazione richiede la licenza vSphere Enterprise Plus per utilizzare gli switch virtuali distribuiti vSphere. Se la tua licenza non è valida per Enterprise Plus, devi utilizzare la licenza VMware Service Provider Program (VSPP) fornita da {{site.data.keyword.cloud_notm}}.

### Ordinazione degli host di gestione
{: #ordering-management-hosts}

Utilizza la seguente procedura per ordinare i server host di gestione.

1. Apri una finestra del browser e accedi a {{site.data.keyword.cloud_notm}}.
2. Seleziona **Account > Place an Order**.
3. Seleziona **Bare Metal Servers > Monthly**.
4. Scegli un server appropriato che soddisfa le tue esigenze per il cluster di gestione sulla schermata dell'elenco di server. **Nota:** come minimo, ESXi 5.5 richiede un unico processore dual-core, 4 GB di RAM e un controller ethernet di 1 Gb.
5. Seleziona il data center appropriato (dove sono state create le VLAN) per eseguire il provisioning dei server ESXi e specificare le seguenti specifiche per ogni opzione:
  * Quantità: 2
  * RAM: 32 GB
  * Sistema operativo: VMware ESXi 5.5 ('No Operating System' se stai utilizzando [Installazione di VMware vSphere ESXi tramite la console remota e il supporto virtuale](/docs/infrastructure/vmware?topic=VMware-installing-vsphere-esxi#installing-vsphere-esxi))
  * Dischi rigidi: dischi 1 e 2: 500 GB SATA in RAID 1
  * Larghezza di banda pubblica: solo rete privata-> larghezza di banda di 0 GB
  * Velocità porta di uplink: 10 Gbps di uplink di rete privata ridondante 
6. Fai clic su **Continue Your Order**.
7. Fai clic su **Add Server** per iniziare ad aggiungere gli host ESXi per il cluster di capacità all'ordine.

### Ordinazione degli host di capacità
{: #ordering-capacity-hosts}

1. Scegli un server appropriato che soddisfa le tue esigenze per gli host cluster di capacità sulla schermata dell'elenco di server. **Nota:** come minimo, ESXi 5.5 richiede un unico processore dual-core, 4 GB di RAM e un controller ethernet di 1 GB.
2. Seleziona il data center appropriato (dove sono state create le VLAN) per eseguire il provisioning dei server ESXi e specificare le seguenti specifiche per ogni opzione:
  * Quantità: 3
  * RAM: 128 GB
  * Sistema operativo: VMware ESXi 5.5 ('No Operating System' se stai utilizzando [Installazione di VMware vSphere ESXi tramite la console remota e il supporto virtuale](/docs/infrastructure/vmware?topic=VMware-installing-vsphere-esxi#installing-vsphere-esxi))
  * Dischi rigidi: dischi 1 e 2: 500 GB SATA in RAID 1
  * Larghezza di banda pubblica: solo rete privata > larghezza di banda di 0 GB
  * Velocità porta di uplink: 10 Gbps di uplink di rete privata ridondante 
3. Fai clic su **Continue Your Order**.

### Completamento della configurazione
{: #completing-configuration}

Ora, gli host ESXi sono nel tuo carrello degli acquisti. Per eseguire il provisioning correttamente dei dispositivi, devi assegnare la VLAN (se applicabile) pubblica, la VLAN privata, il nome host e il dominio ai dispositivi.

1. Assegna le seguenti VLAN e crea i nomi host per i dispositivi:

* Host ESXi – VLAN di backend: (1101)
* VLAN di backend: (1101)
* VLAN di frontend: (2101)

2. Seleziona il tuo metodo di pagamento, accetta i termini e le condizioni e fai clic su **Finalize Your Order**. **Importante:** non procedere al passo 5 finché non è possibile accedere ai server di cui viene eseguito il provisioning tramite la VPN o il server virtuale ordinato nel passo precedente.

## Passo 5 Trunking della VLAN sugli switch BCS
{: #step-5-trunking-vlans-on-bcs-switches}

Per impostazione predefinita, le porte sono inserite sugli switch del cliente di backend (ossia, lo switch di rete privato in un pod, ad esempio BCS (Backend Customer Switch)) nella modalità di accesso. Di conseguenza, devi eseguire il trunk delle porte collegate agli host ESXi in modo che gli host possano accedere all'archiviazione e le VM possano comunicare sulla rete privata.

Prima di aprire il ticket per eseguire il trunk delle VLAN, devi determinare quali interfacce di rete sono sulla rete privata. Per determinare l'interfaccia, vai a **Device Details** per ogni server ESXi e vai a **Network > Private**. Per questo ambiente, `eth0` e `eth2` sono gli adattatori della rete privata.

In aggiunta al trunking delle VLAN per gli host ESXi, devi anche annullare l'associazione ai NIC per gli host di gestione e capacità. Devi annullare l'associazione ai NIC perché Link Aggregation Control Protocol (LACP) non è compatibile con i percorsi multipli iSCSI software.

Per eseguire il trunk delle VLAN e annullare l'associazione ai NIC, devi aprire un ticket seguendo questa procedura:

1. Apri una finestra del browser e accedi a {{site.data.keyword.cloud_notm}}.
2. Seleziona **Support, Add Ticket**.
3. Immetti le seguenti informazioni: 
  * Oggetto: domanda rete privata
  * Titolo: trunking della VLAN e annullamento dell'associazione ai NIC
  * Dettagli: trunking della VLAN `<Management VLAN>`, `<Storage VLAN>` e `<VM VLAN>` su coppie NIC eth0 e eth2 per i seguenti host [elenca ogni host ESXi]. Inoltre, annulla l'associazione (rimuove LACP) dei NIC privati (eth0 e eth2) sui seguenti server: [elenca ogni host ESXi]
4. Fai clic su **Add Ticket**.

Assicurati di modificare le VLAN designate in <> con le VLAN reali

## Passo 6 Configurazione della rete host di gestione
{: #step-6-configuring-management-host-networking}

Dopo che è stato eseguito il provisioning dei server e il trunking delle VLAN, devi configurare la rete sugli host nel cluster di gestione. Per questa configurazione, utilizza gli switch standard vSphere per il cluster di gestione. Tranne che per i gruppi di porte con tolleranza di errore e vMotion, utilizza gli indirizzi IP portatili per configurare i gruppi di porte kernel della VM. **Nota:** utilizza il tuo schema IP per vMotion e la tolleranza di errore perché il traffico non deve essere instradato. Tuttavia, tutti gli host devono trovarsi sulla stessa sottorete degli altri host nel cluster per utilizzare le funzionalità vMotion e tolleranza di errore. Se la sottorete deve essere instradata, si consiglia di utilizzare gli indirizzi IP portatili {{site.data.keyword.cloud_notm}}.

Per configurare i gruppi di porte, il client vSphere deve essere installato sul server virtuale di utilità o sulla workstation che accede agli host tramite la VPN di gestione {{site.data.keyword.cloud_notm}}.

1. Apri una finestra del browser e accedi a {{site.data.keyword.cloud_notm}}.
2. Dopo aver eseguito la connessione al server di utilità o alla VPN {{site.data.keyword.cloud_notm}}, avvia il client vSphere.
3. Immetti l'indirizzo IP, il nome utente e la password di uno degli host ESXi di gestione. Puoi trovare la password root per l'host ESXi selezionando **Device Details > Passwords**.
4. Vai a **Configurations > Networking** e crea o modifica i seguenti gruppi di porte sullo switch standard vSphere (molto probabilmente vSwitch0).

Completa questa procedura per ogni host nel cluster di gestione.

### Proprietà vSwitch0
{: #vswitch0-properties}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabella 4. Proprietà vSwitch0</caption>
<tbody>
<tr><th>Adattatore di rete</th><th>vmnicX e vmnicY</th></tr>
<tr><td>Bilanciamento del carico</td><td>Router basato sull'ID della porta virtuale di origine</td></tr>
<tr><td>Adattatori attivi</td><td>vmnicX e vmnicY</td></tr>
</tbody>
</table>

### Modifica il gruppo di porte della macchina virtuale esistente
{: #edit-existing-virtual-machine-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabella 5. Gruppo di porte vmPG-Management</caption>
<tbody>
<tr><th>Etichetta rete</th><th>*vmPG-Management*</th></tr>
</tbody>
</table>

### Modifica il gruppo di porte kernel della VM esistente
{: #edit-existing-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabella 6. Gruppo di porte vmkPG-Management</caption>
<tbody>
<tr><th>Etichetta rete</th><th>*vmkPG-Management*</th></tr>
</tbody>
</table>

### Aggiungi il gruppo di porte kernel della VM vMotion
{: #add-vmotion-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabella 7. Gruppo di porte vMotion</caption>
<tbody>
<tr><th>Tipo di connessione </th><th>VMKernel</th></tr>
<tr><td>Etichetta rete</td><td>*vmkPG-vMotion*</td></tr>
<tr><td>ID VLAN</td><td>*&lt;VLAN di archiviazione&gt; (1102)*</td></tr>
<tr><td>Traffico vMotion </td><td>Abilitato</td></tr>
<tr><td>Indirizzo IP</td><td>*172.16.10.X/24*<br/><br/>*Indirizzo definito dall'utente che può essere una sottorete diversa. Assicurati che gli altri indirizzi vMotion su ogni host siano nella stessa sottorete.*</td></tr>
<tr><td>Maschera di sottorete</td><td>255.255.255.0</td></tr>
</tbody>
</table>

### Aggiungi il gruppo di porte kernel della VM della tolleranza di errore
{: #add-fault-tolerance-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabella 8. Gruppo di porte della tolleranza di errore (FT)</caption>
<tbody>
<tr><th>Tipo di connessione </th><th>Kernel VM</th></tr>
<tr><td>Etichetta rete</td><td>*vmkPG-FT*</td></tr>
<tr><td>ID VLAN</td><td>*&lt;VLAN di archiviazione&gt; (1102)*</td></tr>
<tr><td>Registrazione della tolleranza di errore</td><td>Abilitato</td></tr>
<tr><td>Indirizzo IP</td><td>*172.16.20.X/24*<br/><br/>*Indirizzo definito dall'utente che può essere una sottorete diversa, se necessario. Assicurati che gli altri indirizzi FT su ogni host siano sulla stessa sottorete.*</td></tr>
<tr><td>Maschera di rete</td><td>255.255.255.0</td></tr>
</tbody>
</table>

### Aggiungi il gruppo di porte kernel della VM di archiviazione
{: #add-storage-vm-kernel-port-group}

Aggiorna la sezione **Notes** di ogni indirizzo IP portatile con il nome dell'host e la porta kernel della VM assegnati. La sezione **Notes** può essere trovata andando a {{site.data.keyword.slportal_full}} e selezionando **Network > IP Management > Subnets > [Subnet]**.

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabella 9. Gruppo di porte di archiviazione</caption>
<tbody>
<tr><th>Tipo di connessione </th><th>Kernel VM</th></tr>
<tr><td>Etichetta rete</td><td>*vmkPG-Storage*</td></tr>
<tr><td>ID VLAN</td><td>*&lt;VLAN di archiviazione&gt; (1102)*</td></tr>
<tr><td>Indirizzo IP</td><td>*Indirizzo privato portatile*<br/><br/>*Un indirizzo IP selezionato dagli indirizzi privati portatili associati alla VLAN di archiviazione.*</td></tr>
<tr><td>Maschera di rete</td><td>*La maschera della sottorete associata all'intervallo di IP*</td></tr>
</tbody>
</table>

### Aggiungi il gruppo di porte di indirizzi pubblici
{: #add-public-address-port-group}

Aggiorna la sezione **Notes** di ogni indirizzo IP portatile con il nome dell'host e la porta kernel della VM assegnati. La sezione **Notes** si trova in {{site.data.keyword.slportal}} e selezionando **Network > IP Management > Subnets > [Subnet]**.

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabella 10. Gruppo di porte pubbliche</caption>
<tbody>
<tr><th>Tipo di connessione </th><th>Macchina virtuale </th></tr>
<tr><td>Etichetta rete</td><td>vmPG-Public</td></tr>
<tr><td>ID VLAN</td><td>&lt;VLAN pubblica primaria&gt; (ad esempio 2101)</td></tr>
</tbody>
</table>

## Passo 7 Scarica o carica le immagini ISO e vCenter Virtual Appliance
{: #step-7-download-or-upload-iso-images-and-vcenter-virtual-appliance}

Ora, l'ambiente è pronto per distribuire VMware vCenter Virtual Appliance e installare una macchina virtuale per DNS, Active Directory o BIND. Tuttavia, prima di distribuire, devi scaricare le immagini. Per scaricare le immagini, collegati tramite desktop remoto al server virtuale di cui è stato eseguito il provisioning in precedenza e scarica le immagini appropriate sul server virtuale per il tuo ambiente:

* Active Directory / DNS di Windows: immagine ISO Windows Server 2008R2/Windows Server 2012 (il tuo supporto su licenza)
* Linux BIND: [CentOS Install Image ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://isoredirect.centos.org/centos/6/isos/x86_64/){: new_window}
* [vCenter Virtual Appliance ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://my.vmware.com/web/vmware/info/slug/datacenter_cloud_infrastructure/vmware_vsphere/5_5){: new_window} (è necessaria una sottoscrizione VMware valida)

Dopo aver scaricato l'ISO, devi caricare l'immagine in un archivio dati host di gestione in modo da poterla collegare a una macchina virtuale. Utilizza il client vSphere per la connessione a un host di gestione e creare una directory ISO sull'archivio dati locale.

## Passo 8 Distribuzione DNS
{: #step-8-deploying-dns}

Devi distribuire una VM (su un host di gestione) e installare i servizi DNS. Utilizza il client vSphere tradizionale per creare una VM sull'host ESXi di gestione in cui si trova l'ISO Windows o Linux. Connetti l'ISO appropriata (Windows o CentOS) per distribuire un server DNS sulla VM. Assicurati di associare la VM al gruppo di porte della VM, (vmpg)-management, creato nel passo precedente.

Dopo aver installato la VM, assegna un indirizzo IP e un gateway predefinito dal gruppo di sottoreti private portatili. Se stai utilizzando il foglio di lavoro VLAN nell'appendice A, la sottorete della VM di gestione. Aggiorna la sezione **Notes** di ogni indirizzo IP portatile con il nome della VM assegnata. La sezione **Notes** può essere trovata andando a {{site.data.keyword.slportal}} e selezionando **Network > IP Management > Subnets > [Subnet]**.

Sebbene esuli dall'ambito di questo documento, per descrivere la procedura necessaria per abilitare DNS, vengono fornite le seguenti indicazioni:

1. Imposta **DNS Forwarding** sugli host DNS locali service.softlayer.com:
  * `rs1.service.softlayer.com 10.0.80.11`
  * `rs2.service.softlayer.com 10.0.80.12`
2. Dopo aver impostato il DNS, crea una zona DNS locale (dal06.mycompany.local) e una zona di ricerca inversa per tutte le sottoreti portatili e primarie di cui è stato eseguito il provisioning.
3. Aggiungi un record host A per ogni indirizzo IP di gestione dell'host (vmk0 su vmkPG-management).
4. Aggiungi un record host A dalla sottorete privata portatile associata alla VLAN di gestione per vCenter Virtual Appliance.
5. Aggiorna la sezione **Notes** della sottorete di IP portatili assegnata al vCenter.

Per ulteriori informazioni, vedi i seguenti link:
* [DNS di Windows e Active Directory ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://social.technet.microsoft.com/wiki/contents/articles/12370.step-by-step-guide-for-setting-up-a-windows-server-2012-domain-controller.aspx){: new_window}.
<!--* [CentOS BIND ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.centos.org/docs/2/rhl-rg-en-7.2/s1-bind-configuration.html){: new_window}.-->

## Passo 9 Distribuzione e configurazione di vCenter Virtual Appliance
{: #step-9-deploying-and-configuring-vcenter-virtual-appliance}

Ora che il DNS è configurato, puoi distribuire e configurare vCenter Server Appliance. Per distribuire l'appliance, segui questa procedura:

1. Collegati tramite desktop remoto al server virtuale e apri il client vSphere.
2. Esegui la connessione a un host di gestione e seleziona **File, Deploy OVF Template**.
3. Segui la procedura guidata per completare la distribuzione.

<!--For more information about OVF Template deployment, see [VMware documentation ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://pubs.vmware.com/vsphere-55/index.jsp?topic=%2Fcom.vmware.vsphere.hostclient.doc%2FGUID-6C847F77-8CB2-4187-BD7F-E7D3D5BD897B.html&resultof=%22thick%22%20%22thin%22%20){: new_window}.-->

Poiché non è disponibile alcun server DHCP (Dynamic Host Configuration Protocol) per assegnare a vCenter Virtual Appliance un indirizzo IP all'accensione, devi configurare la console root per configurare l'appliance. **Nota:** viene visualizzato un messaggio `NO NETWORKING DETECTED` sulla console di vCenter Virtual Appliance.

Segui questa procedura per configurare l'appliance:

1. Accedi alla console con un nome utente (**User name**) 'root' e una **Password** 'vmware'.
2. Esegui `/opt/vmware/share/vami/vami_config_net` e segui la procedura guidata di testo per configurare le proprietà IP, sottorete e DNS. Ricorda di utilizzare l'indirizzo IP del server DNS o BIND creato nel **Passo 8: Distribuzione DNS**.
3. Salva le impostazioni di rete, esci dalla console e apri un browser sul server virtuale.
4. Vai all'indirizzo IP che hai fornito al vCenter virtual appliance (VCVA) accodato alla porta 5480<!-- (https://:5480)-->.
5. Accetta l'EULA nella procedura guidata, rispondi alla domanda **Customer Improvement Experience Program** e seleziona **Configure Options, Set custom configuration**.
6. Fai clic su **Next** e immetti i seguenti valori:
<table border="1" cellpadding="0" cellspacing="0"><caption>Tabella 11. Procedura guidata di configurazione di VCVA</caption><tbody><tr><th>Menu procedura guidata</th><th>Opzione</th><th>Valore</th></tr><tr><td>Impostazioni database</td><td>Tipo di database </td><td>integrato </td></tr><tr><td>Impostazioni SSO</td><td>Tipo di distribuzione SSO</td><td>integrato </td></tr><tr><td>Impostazioni SSO</td><td>Nuova password amministratore *(per administrator@vsphere.local)*</td><td>&lt;Immetti una password&gt;</td></tr><tr><td>Impostazioni SSO</td><td>Ripeti la nuova password</td><td>&lt;Immetti la stessa password che hai utilizzato in precedenza&gt;</td></tr><tr><td>Sincronizzazione temporale</td><td>Sincronizzazione NTP</td><td>servertime.service.softlayer.com</td></tr></tbody></table>
7. Fai clic su **Start**. Il VCVA è configurato.
8. Modifica la password root utilizzando le opzioni in **Admin**.
9. Scollegati dalla pagina web di configurazione di VCVA.
10. Passa al client web vSphere immettendo l'indirizzo IP del VCVA accodato a `9443/vsphere-client`<!-- (https://:9443/vsphere-client)-->.
11. Accedi alla root e seleziona **Administration, Licenses**.
12. Immetti la tua licenza VMware vCenter.

## Passo 10 Crea i cluster vCenter e lo switch virtuale distribuito
{ #step-10-create-vcenter-clusters-and-distributed-virtual-switch}

Ora che il VCVA è configurato e con licenza, puoi creare i costrutti cluster e data center e gli switch virtuali distribuiti per il cluster di capacità.

### Creazione del data center e dei cluster
{: #creating-data-center-and-clusters}

1. Sul client vSphere, vai alla schermata **Home**.
2. Seleziona **Getting Started** e fai clic su **click here**.
3. Fai clic su **Create Datacenter**.
4. Immetti il nome del data center (l'ambiente di esempio è il data center Toronto 01. Utilizza `Toronto 01` come nome del data center).
5. Dopo aver creato il data center, fai clic su **Create a cluster** sulla pagina **Getting Started**.
6. Fornisci un nome al primo cluster `Management`. Lascia tutte le altre opzioni non selezionate e fai clic su **OK**.
7. Crea un altro cluster quando hai terminato utilizzando lo stesso processo del cluster di gestione.

Puoi ora aggiungere gli host di gestione e capacità ai cluster di gestione e capacità, utilizzando **Add a host**. Assicurati di utilizzare il nome di dominio completo (FQDN) del server invece dell'indirizzo IP in modo che sia utilizzato il DNS quando viene aggiunto ogni host.

Dopo aver aggiunto gli host ESXi al vCenter, potresti notare una coppia di messaggi di avvertenza riguardo l'abilitazione di shell e SSH su ogni host ESXi. Per eliminare queste avvertenze, fai clic su **Suppress Warning > Yes** sulla finestra a comparsa. Se il link Suppress Warnings non è presente, segui questa procedura:

1. Vai alla scheda **Manage** dell'host ESXi.
2. Seleziona **Settings** e fai clic su **Advanced System Settings**.
3. Trova la chiave **UserVars.SupressShellWarning** e modifica il valore con **1**.
4. Fai clic su **OK**.

Dopo che gli host di gestione e capacità sono stati aggiunti ai rispettivi cluster, vai in ogni host e configura DNS e NTP. Per configurare DNS, segui questa procedura:

1. Fai clic su un host e seleziona **Manage > Networking**.
2. Seleziona lo stack di sistema predefinito (**TCP/IP configuration**) e fai clic sull'icona matita.
3. Immetti l'indirizzo IP del server DNS che hai precedentemente creato e il nome dell'host e del dominio.

Per le impostazioni NTP:

1. Vai a **Manage, Settings, Time Configuration**.
* Immetti `servertime.service.softlayer.com` come server NTP.
* Imposta **NTP Service Startup Policy** su `Start and stop with host`.

***Creazione di uno switch virtuale distribuito per gli host di capacità***

1. Utilizza il client web vSphere per passare a **Networking** e fai clic con il tasto destro sul nome del data center.
2. Seleziona **New Distributed Switch**.
3. Fornisci un nome allo switch distribuito e fai clic su **Next**.
4. Seleziona la versione dello switch distribuito appropriata e fai clic su **Next**.
5. Sulla schermata **Edit Settings**, immetti `2` come numero di uplink e deseleziona l'opzione **Create a default port group**.
6. Fai clic su **Next > Finish** e crea lo switch virtuale distribuito.

***Crea i gruppi di porte per lo switch virtuale distribuito***

Ora che è presente lo switch virtuale distribuito, devi creare dei gruppi di porte per vMotion, tolleranza di errore, VM e archiviazione.

***Creazione del gruppo di porte dvpg-Private-VManagement***

1. Vai alla sezione Networking utilizzando il client web vSphere.
2. Fai clic con il tasto destro sullo switch virtuale distribuito (DVS).
3. Fai clic su **New Distributed Port Group…** e immetti le informazioni nella tabella 14 per il primo gruppo di porte.
4. Lascia i valori predefiniti per tutte le opzioni che non sono specificate nella tabella.

| Nuovo menu del gruppo di porte distribuito |Campo|Valore|
| --- | --- | --- |
| Nome e ubicazione| Nome | dvpg-Private-VM Management |
| Impostazioni di configurazione|Opzioni avanzate| Consulta la configurazione delle politiche predefinite personalizzate |
| Configura le politiche (Teaming e Failover)|Bilanciamento del carico| Rotta basata sul carico NIC fisico |
| Configura le politiche (Teaming e Failover)|Ordine di failover | Uplink attivi: Uplink 1 e Uplink 2 |
<caption>Tabella 12. Gruppo di porte di gestione della VM DVS</caption>

Dopo aver creato il primo gruppo di porte, crea i rimanenti gruppi di porte con le seguenti opzioni di configurazione (dalla tabella 15 alla 19).

***Crea il gruppo di porte dvpg-Private-vMotion***

| Nuovo menu del gruppo di porte distribuito |Campo|Valore|
|---|---|---|
| Nome e ubicazione| Nome |dvpg-Private-vMotion|
| Impostazioni di configurazione|Tipo di VLAN|VLAN|
| Impostazioni di configurazione|ID VLAN|&lt;VLAN di archiviazione&gt;|
| Impostazioni di configurazione|Opzioni avanzate| Consulta la configurazione delle politiche predefinite personalizzate |
| Configura le politiche (Teaming e Failover)|Bilanciamento del carico| Rotta basata sul carico NIC fisico |
| Configura le politiche (Teaming e Failover)|Ordine di failover | Uplink attivi: Uplink 1 e Uplink 2 |
<caption>Tabella 13. Gruppo di porte vMotion DVS</caption>

***Crea il gruppo di porte dvpg-Private-Fault Tolerance***

| Nuovo menu del gruppo di porte distribuito |Campo|Valore|
|---|---|---|
| Nome e ubicazione| Nome |dvpg-Private-Fault Tolerance|
| Impostazioni di configurazione|Tipo di VLAN|VLAN|
| Impostazioni di configurazione|ID VLAN|&lt;VLAN di archiviazione&gt;|
| Impostazioni di configurazione|Opzioni avanzate| Consulta la configurazione delle politiche predefinite personalizzate |
| Configura le politiche (Teaming e Failover)|Bilanciamento del carico| Rotta basata sul carico NIC fisico |
| Configura le politiche (Teaming e Failover)|Ordine di failover | Uplink attivi: Uplink 1 e Uplink 2 |
<caption>Tabella 14. Gruppo di porte FT DVS</caption>

***Crea il gruppo di porte dvpg-Private-VM Access***

| Nuovo menu del gruppo di porte distribuito |Campo|Valore|
|---|---|---|
| Nome e ubicazione| Nome |dvpg-Private-VM Access|
| Impostazioni di configurazione|Tipo di VLAN|VLAN|
| Impostazioni di configurazione|ID VLAN|&lt;VLAN macchina virtuale&gt;|
| Impostazioni di configurazione|Opzioni avanzate| Consulta la configurazione delle politiche predefinite personalizzate |
| Configura le politiche (Teaming e Failover)|Bilanciamento del carico| Rotta basata sul carico NIC fisico |
| Configura le politiche (Teaming e Failover)|Ordine di failover | Uplink attivi: Uplink 1 e Uplink 2 |
<caption>Tabella 15. Gruppo di porte di accesso della VM DVS</caption>

***Crea dvpg-Private-Storage***

| Nuovo menu del gruppo di porte distribuito |Campo|Valore|
|---|---|---|
| Nome e ubicazione| Nome |dvpg-Private-Storage Path A|
| Impostazioni di configurazione|Tipo di VLAN|VLAN|
| Impostazioni di configurazione|ID VLAN|&lt;VLAN di archiviazione&gt;|
| Impostazioni di configurazione|Opzioni avanzate| Consulta la configurazione delle politiche predefinite personalizzate |
| Configura le politiche (Teaming e Failover)|Bilanciamento del carico| Rotta basata sul carico NIC fisico |
| Configura le politiche (Teaming e Failover)|Ordine di failover | Uplink attivi: Uplink 1 e Uplink 2 |
<caption>Tabella 16. Gruppo di porte di archiviazione DVS</caption>

***Crea dvpg-Primary-Public***

|Nuovo menu del gruppo di porte distribuito|Campo|Valore|
|Nome e ubicazione|Nome|dvpg-Private-Storage|
|Impostazioni di configurazione|Tipo di VLAN|VLAN
|Impostazioni di configurazione|ID VLAN|&lt;VLAN pubblica primaria&gt;|
|Impostazioni di configurazione|Opzioni avanzate|Consulta la configurazione delle politiche predefinite personalizzate|
|Configura le politiche (Teaming e Failover)|Bilanciamento del carico|Rotta basata sul carico NIC fisico|
|Configura le politiche (Teaming e Failover)|Ordine di failover|Uplink attivi: Uplink 1 e Uplink 2|
<caption>Tabella 17. Gruppo di porte Path B di archiviazione DVS</caption>

## Passo 11 Migrazione degli host ESXi nel cluster di capacità allo switch virtuale distribuito
{: #step-11-migrating-esxi-hosts-in-capacity-cluster-to-distributed-virtual-switch}

Ora che gli host di capacità sono stati aggiunti al cluster di capacità, puoi migrare la configurazione dello switch standard virtuale allo switch virtuale distribuito che hai creato nel *Passo 10: Crea i cluster vCenter e lo switch virtuale distribuito*. La migrazione viene eseguita per un host e successivamente applichi un profilo host per configurare il resto del cluster.

Prima di iniziare ad aggiungere gli adattatori VMkernel, assegna le vmnic agli uplink sullo switch virtuale distribuito.

1. Fai clic su **vCenter Inventory Lists, Distributed Switches**.
2. Seleziona lo switch distribuito appropriato per gli host di capacità.
3. Fai clic su **Add and manage hosts** sulla pagina **Getting Started**.
4. Utilizza le seguenti impostazioni per aggiungere gli uplink e migrare il VMkernel esistente associato alla gestione dell'host.
  - <table border="1" cellpadding="0" cellspacing="0"><caption>Tabella 18. Host aggiunti a DVS</caption><tbody><tr><th>Menu</th><th>Campo</th><th>Valore</th></tr><tr><td>Seleziona attività</td><td>Seleziona attività</td><td>Aggiungi host</td></tr><tr><td>Seleziona host</td><td>Fai clic su **New Hosts**</td><td>Fai clic su Capacity Host</td></tr><tr><td>Seleziona le attività dell'adattatore di rete</td><td>Seleziona le attività dell'adattatore di rete</td><td>Seleziona gli adattatori fisici di gestione e gli adattatori VMkernel di gestione</td></tr></tbody></table>
5. Seleziona una delle vmnic private e fai clic su **Manage physical network adapters > Assign uplink**.
6. Seleziona `uplink1` sulla schermata a comparsa e fai clic su **OK**.
7. Ripeti questi passi per l'altra vmnic privata e assegnala a `uplink2`.
8. Fai clic su **Next**, evidenzia l'adattatore VMkernel vmk0 e fai clic su **Assign port group**.
9. Seleziona **dvpg-Private-VM Management** sulla finestra a comparsa e fai clic su **OK**.
10. Fai clic due volte su **Next** e poi su **Finish** per completare la migrazione allo switch virtuale distribuito. **Nota:** potresti perdere brevemente la connettività di rete all'host. La connessione viene ristabilita velocemente.

Dopo aver migrato l'adattatore vmk0 allo switch virtuale distribuito, puoi aggiungere dei kernel della VM ad ogni gruppo di porte nel DVS.

11. Fai clic su **Manage > Networking** sull'host all'interno del vCenter.
12. Vai a **VM Kernel adapters > Add host networking** e aggiungi gli adattatori kernel della VM nella tabella 19 e 21. Per aggiungere questi adattatori, esegui la migrazione dal menu “VM Kernel adapters” presente nella scheda **Manage > Networking** sull'host all'interno del vCenter. Successivamente, fai clic sull'icona “Add host networking” e aggiungi i seguenti adattatori kernel della VM.

***Aggiungi vmk1 per vMotion***

|Menu|Campo|Valore|
|---|---|---|
|Seleziona tipo di connessione|Seleziona tipo di connessione|Adattatore di rete VMKernel |
|Seleziona dispositivo di destinazione|Seleziona un gruppo di porte distribuito esistente|dvpg-Private-vMotion|
|Seleziona le attività dell'adattatore di rete|Seleziona le attività dell'adattatore di rete|Seleziona **Manage physical adapters** e **Manage VM kernel adapters**|
|Proprietà porta|Abilita servizi|Controlla il traffico vMotion|
|Impostazioni IPv4|Indirizzo IPv4|*172.16.10.X/24*<br/><br/>*Questo è un indirizzo definito dall'utente e può essere una sottorete diversa, se necessario. Assicurati che gli altri indirizzi vMotion su ogni host siano nella stessa sottorete.*|
|Impostazioni IPv4|Maschera di sottorete|255.255.255.0|
<caption>Tabella 19. vMotion rete host</caption>

***Aggiungi vmk2 per la tolleranza di errore***

|Menu|Campo|Valore|
|---|---|---|
|Seleziona tipo di connessione|Seleziona tipo di connessione|Adattatore di rete VMKernel |
|Seleziona dispositivo di destinazione|Seleziona un gruppo di porte distribuito esistente|dvpg-Private-Fault Tolerance|
|Seleziona le attività dell'adattatore di rete|Seleziona le attività dell'adattatore di rete|Seleziona **Manage physical adapters** e **Manage VMKernel adapters**|
|Proprietà porta|Abilita servizi|Controlla la registrazione della tolleranza di errore|
|Impostazioni IPv4|Indirizzo IPv4|*172.16.20.X/24*<br/><br/>*Questo è un indirizzo definito dall'utente e può essere una sottorete diversa, se necessario. Assicurati che gli altri indirizzi FT su ogni host siano sulla stessa sottorete.*|
|Impostazioni IPv4|Maschera di sottorete|255.255.255.0|
<caption>Tabella 20. Tolleranza di errore rete host</caption>

***Aggiungi vmk3 per l'archiviazione***

|Menu|Campo|Valore|
|---|---|---|
|Seleziona tipo di connessione|Seleziona tipo di connessione|Adattatore di rete VMkernel |
|Seleziona dispositivo di destinazione|Seleziona un gruppo di porte distribuito esistente|dvpg-Private-Storage|
|Seleziona le attività dell'adattatore di rete|Seleziona le attività dell'adattatore di rete|Seleziona gli adattatori fisici di gestione e gli adattatori VMkernel di gestione|
|Impostazioni IPv4|Indirizzo IPv4|*Indirizzo privato portatile*<br/><br/>*Questo è l'indirizzo IP selezionato dagli indirizzi privati portatili associati alla VLAN di archiviazione. Questo indirizzo deve essere su una sottorete diversa rispetto a Storage Path B.*|
|Impostazioni IPv4|Maschera di sottorete|La maschera della sottorete associata all'intervallo di IP|
<caption>Tabella 21. Archiviazione rete host</caption>

***Creazione di un profilo host***

Per creare un profilo host, devi acquisirlo da un singolo host di capacità che hai precedentemente configurato.

1. Vai alla schermata home del client web vSphere e fai clic sull'icona **Host Profiles**.
2. Fai clic sul segno verde più (+), **Extract profile from a host** e seleziona l'host di capacità precedentemente configurato sulla finestra a comparsa.
3. Fai clic su **Next**.
4. Fornisci al profilo host (Capacity01 Host Profile) un nome appropriato, immetti una descrizione e fai clic su **Next**.
5. Rivedi le impostazioni e fai clic su **Finish**.

Dopo aver creato il profilo host, devi modificarlo in modo che non richieda gli indirizzi MAC quando viene applicato il profilo al resto degli host nel cluster di capacità.

1. Fai clic con il tasto destro sul profilo host che hai creato e seleziona **Edit Settings > Edit Host Profile, Host virtual NIC**.
2. Nel pannello di destra, modifica **Determine how MAC address for vmknic is decided** con **User must explicitly choose the policy option**.
3. Fai clic su **Next** e quindi su **Finish**.

***Collegamento del profilo host al cluster di capacità***

Ora che hai creato un profilo host, devi collegarlo al cluster. I cluster vengono collegati in modo da poter essere applicati agli host di capacità.

1. Vai alla vista **Host and Clusters** nel client web vSphere.
2. Entra nella modalità di manutenzione per ogni host nel cluster. **Nota:** i profili possono essere applicati solo agli host che sono nella modalità di manutenzione.
3. Fai clic con il tasto destro sul cluster di capacità e seleziona **Attach Host Profile** dal menu a comparsa.
4. Seleziona il profilo host che hai creato e fai clic su **Next**.
5. Immetti le informazioni appropriate nella schermata **Customize host** e fai clic su **Finish**.
6. Rimuovi gli host dalla modalità di manutenzione (modalità non di manutenzione).

## Passo 12 Ordinazione, configurazione e collegamento dell'archiviazione condivisa
{: #step-12-order-configure-and-attach-shared-storage}

Prima di continuare, è imperativo ordinare, configurare e collegare l'archiviazione condivisa da utilizzare con i cluster e gli host di gestione e capacità all'interno dell'ambiente. Se vuoi utilizzare la soluzione di archiviazione condivisa multitenant {{site.data.keyword.cloud_notm}} (File Storage), vedi [Architecture Guide for IBM File Storage for {{site.data.keyword.cloud_notm}} with VMware](/docs/infrastructure/FileStorage?topic=FileStorage-architectureguide).

## Passo 13 Abilitazione HA/DRS e svMotion vCVA
{: #step-13-enabling-ha-drs-and-svmotion-vcva}

***Abilitazione HA/DRS su cluster di gestione e capacità***

Ora che l'archiviazione condivisa è configurata, devi abilitare HA e DRS per fornire ulteriori funzionalità di protezione e bilanciamento del carico alle VM sul cluster di gestione.

1. Vai al client web vSphere.
* Seleziona Manage, Settings per il cluster di gestione.
* Seleziona vSphere DRS e fai clic su Edit. Assicurati che siano selezionate le seguenti impostazioni: **Turn on vSphere DRS**, Automation Level - **Fully Automated**, Migration threshold slider setting - midway, virtual machine automation - **Enable individual virtual machine automation levels.**, Power Management - **Off**.  
* Sulla schermata vSphere HA Settings, assicurati che siano selezionate le seguenti impostazioni: **Turn on vSphere HA**, **Host Monitoring**, VM restart priority - **Medium**, Host isolation response - **Leave powered on**.  
***Storage vMotion del vCenter Virtual Appliance***

Ora che l'archiviazione è configurata sul cluster di gestione e HA e DRS sono abilitati, devi configurare il vCenter Virtual Appliance per l'archiviazione condivisa.

1. Fai clic con il tasto destro sull'appliance appropriata e seleziona **Migrate** dal menu a comparsa.
2. Seleziona **Change data store** e fai clic su **Next**.
3. Seleziona il volume iSCSI che hai precedentemente montato sul cluster di gestione e fai clic su **Next**.
4. Rivedi le selezioni e fai clic su **Next**.

L'ambiente VMware a singolo sito avanzato è completo.

## Riepilogo
{: #summary}

Ora hai un ambiente VMware in esecuzione in un data center IBM Cloud. Il tuo ambiente VMware può eseguire carichi di lavoro di produzione e l'integrazione di una distribuzione IBM Cloud in loco. L'ambiente applica le procedure consigliate VMware e abilita funzioni come VMware DRS, HA, DRS di archiviazione e ridondanza di rete. Puoi estendere questa implementazione dell'architettura di riferimento con host di capacità o gestione più grandi e ulteriore archiviazione.

Per ulteriori informazioni su VMware, vedi [Deploy VMware](/docs/infrastructure/vmware?topic=VMware-using-cookbooks-for-vmware-deployments) e [VMware FAQ](/docs/infrastructure/vmware?topic=VMware-faqs-vmware)

## Appendice A: foglio di lavoro VLAN 
{: #appendix-a-vlan-worksheet}

|Tipo di VLAN|Numero VLAN|Intervallo IP |Gateway|Scopo|
|---|---|---|---|---|
|Privata primaria|||| Gestione|
|Privata primaria||||Archiviazione|
|Privata primaria||||Macchine virtuali|
|Pubblica primaria||||Accesso pubblico|
|Privata portatile||||VM di gestione|
|Privata portatile||||Archiviazione|
|Privata portatile||||Macchine virtuali|
|vMotion|||N/D||
|Tolleranza di errore|||N/D|||
<caption>Foglio di lavoro VLAN</caption>
