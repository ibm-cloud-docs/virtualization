---



copyright:
  years: 2014, 2018
lastupdated: "2018-08-16"

subcollection: virtualization


---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Configurazione di Hyper-V
{: #setting-up-hyper-v}

La procedura di configurazione di Hyper-V include la configurazione iniziale di Hyper-V, la creazione di una macchina virtuale e la configurazione di rete sulla nuova macchina virtuale. Le seguenti linee guida spiegano queste procedure.

## Utilizzo della Console di gestione di Hyper-V
{: #using-hyper-v-manager}

La Console di gestione di Hyper-V è la console di gestione di cui ti servi per utilizzare Hyper-V. Dalla Console di gestione di Hyper-V puoi creare, avviare, arrestare, eliminare e configurare tutte le tue macchine virtuali. La console di gestione è anche dove configuri l'ubicazione predefinita per archiviare i tuoi dischi rigidi virtuali e l'ubicazione predefinita per archiviare i tuoi file di configurazione di macchina virtuale.

Puoi trovare la Console di gestione di Hyper-V in **Strumenti di amministrazione** nel Pannello di controllo di Windows. In una nuova installazione di Windows 2008, puoi trovare gli **Strumenti di amministrazione** in Programmi nel menu Start. Dopo che hai aperto il programma Console di gestione di Hyper-V, viene visualizzata una nuova finestra a schermo. Dedica qualche minuto ad acquisire dimestichezza con questa schermata. Tutte le configurazioni e tutto l'utilizzo delle tue macchine virtuali viene eseguito qui.

## Configurazione di Hyper-V
{: #configuring-hyper-v}

La prima configurazione da controllare è l'ubicazione dell'archiviazione per i tuoi dischi rigidi virtuali. Dalla finestra Console di gestione di Hyper-V, sul lato sinistro vedi **Console di gestione di Hyper-V** e il nome del server. Fai clic sul tuo nome del server per visualizzare le informazioni disponibili per il servizio Hyper-V sul tuo server. Ora la parte centrale della schermata è vuota poiché mostra che non è installata alcuna macchina virtuale. Sul lato destro della schermata, vedi un link per **Impostazioni Hyper-V**. Facendo clic sul link richiamerai le impostazioni di base per Hyper-V.

L'impostazione primaria che devi osservare è **Dischi rigidi virtuali**. Sul lato destro, vedi l'ubicazione predefinita dove sono archiviati i tuoi dischi rigidi virtuali. L'ubicazione predefinita è:

`C:\Users\Public\Documents\Hyper-V\Virtual Hard Disks`

Quando crei una macchina virtuale, puoi specificare un'ubicazione specifica se non desideri archiviare quello specifico disco rigido virtuale nell'ubicazione predefinita. Ad esempio, se vuoi archiviare i tuoi dischi rigidi virtuali in una seconda unità più grande (l'unità D, ad esempio) e vuoi che tali unità virtuali siano archiviate nella cartella **VirtualMachine**m immetti `D:\VirtualMachine` nel campo.

****

Puoi anche sfogliare manualmente le cartelle per selezionare l'ubicazione che desideri utilizzare. Dopo che hai selezionato un'ubicazione, fai clic su **OK**.

## Configurazione dei dispositivi di rete
{: #configuring-network-devices}

Prima di configurare i dispositivi di rete virtuale, determina quali adattatori di rete sono connessi alle reti private e pubbliche. Dal menu Start, fai clic su **Connessioni di rete**. Qui vedi due dispositivi di rete. Assicurati di notare che i tuoi dispositivi sono denominati **PrivateNetwork** e **PublicNetwork**. Fai clic con il pulsante destro del mouse su **PrivateNetwork** e seleziona **Proprietà**. Vedi **Connetti usando:**, che mostra il nome dispositivo per l'adattatore di rete privato. Annota questo nome poiché ti servirà quando creerai il tuo dispositivo di rete privata virtuale.

### Configurazione di un dispositivo di rete privata
{: #configuring-a-private-network-device}

Ora che l'ubicazione predefinita per archiviare i tuoi dischi rigidi virtuali è configurata, devi configurare i dispositivi di rete virtuale utilizzati dalle macchine virtuali. Per aprire Gestione reti virtuali, fai clic su **Gestione reti virtuali** sul lato destro della finestra Console di gestione di Hyper-V etichettata.

La prima volta che apri questa finestra, vedi Reti virtuali sulla sinistra con la sola opzione elencata **Nuova rete virtuale**. Sul lato destro, hai tre opzioni:
* Esterna
* Interna
* Privata.
Assicurati che sia selezionata l'opzione **Esterna** e fai clic su **Aggiungi**.

Continua immettendo le informazioni nei restanti campi:
* **Nome** Il nome associato a questo dispositivo di rete. Per questo esempio utilizziamo _Privata_ per indicare che questo dispositivo è connesso alla rete privata IBM Cloud.
* **Tipo di connessione** Seleziona il dispositivo che corrisponde alla tua rete privata. Questo nome è il nome dispositivo che hai trovato in precedenza nella finestra **Connessioni di rete**. Dopo che hai selezionato il dispositivo corretto, fai clic su **OK**. Ti viene presentata un'avvertenza che la tua connessione di rete potrebbe interrompersi; fai clic su **Sì** per continuare.

### Configurazione di un dispositivo di rete pubblica
{: #configuring-a-public-network-device}

Il tuo dispositivo di rete privata è ora installato. Ripeti i passi precedenti per la rete pubblica. Per questo esempio, usa il nome **Pubblica** per il nome dispositivo. Nel tipo di connessione, seleziona l'altro dispositivo di rete che non era stato utilizzato nella configurazione della rete privata. Dopo che hai creato sia il dispositivo di rete pubblica che quello di rete privata, fai clic su **OK**.

## Ottenimento del supporto di installazione
{: #obtaining-installation-media}

Ora che i dispositivi di rete virtuale sono stati creati, devi ottenere il supporto di installazione. Il supporto di installazione che viene utilizzato in questo esempio è un file immagine CD/DVD. IBM Cloud supporta i seguenti sistemi operativi per Hyper-V:
* Windows 2008
* Windows 2003
* CentOS
* Fedora
* Ubuntu.
**Nota:** se già disponi del supporto di installazione, vai a **Creazione di una macchina virtuale**.

I supporti di installazione per [CentOS ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://centos.org){: new_window}, [Fedora ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://fedoraproject.org/){: new_window} e [Ubuntu ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://www.ubuntu.com/){: new_window} sono disponibili dai loro rispettivi siti web. Tutti e tre i sistemi operativi sono concessi in licenza gratuitamente e l'utente non deve eseguire altre operazioni.

## Creazione di una macchina virtuale
{: #creating-a-virtual-machine}

Ora che disponi del supporto di installazione, puoi creare una macchina virtuale dalla Console di gestione di Hyper-V. Fai clic su **Nuovo > Macchina virtuale** per avviare la **Creazione guidata macchina virtuale**).

Nella prima schermata, fai clic su **Avanti** e immetti un nome per la nuova macchina virtuale. Questo nome può essere modificato in un secondo momento. Puoi modificare l'ubicazione del disco rigido virtuale. Viene utilizzato il valore predefinito che hai impostato precedentemente, a meno che non specifichi un'ubicazione differente. Dopo che hai immesso le informazioni, fai clic su **Avanti**.

Devi ora assegnare la memoria al server. In generale, fornisci almeno la memoria minima consigliata come specificato dal sistema operativo. Dopo che hai immesso la quantità di memoria da assegnare, fai clic su **Avanti**.

Imposta quindi il primo adattatore di rete. Se è in corso l'installazione di un sistema operativo Linux sulla macchina virtuale, seleziona **Non connesso**. Se è in fase di installazione Windows, seleziona **Privata** e fai quindi clic su **Avanti**.

Puoi modificare le seguenti impostazioni per il disco virtuale: il nome file, l'ubicazione dell'archiviazione e la dimensione. Apporta le modifiche applicabili e fai clic su **Avanti**.

Il supporto di installazione deve essere impostato. Seleziona **Installa un sistema operativo da un CD/DVD-ROM di avvio > File immagine (.iso)** per impostare l'ubicazione del file immagine CD/DVD per il tuo supporto di installazione. Fai clic su **Avanti**, visualizza il riepilogo e fai clic su **Fine** per chiudere la finestra.

Per finire, devi creare gli adattatori di rete virtuale. La nuova macchina virtuale è ora elencata nella sezione **Macchine virtuali** di Hyper-v Manager. Fai clic con il pulsante destro del mouse sulla nuova macchina virtuale e fai clic su **Impostazioni**.

### Configurazione dell'adattatore di rete virtuale per il sistema operativo Windows
{: #configuring-virtual-network-adapter-for-windows-operating-system}

Un elenco di hardware è ora disponibile per la macchina virtuale. Nell'elenco, vedi **Scheda di rete** e **Privata**. Se vedi **Non connesso**, fai clic sulla scheda (adattatore) di rete, seleziona **Privata** e fai clic su **Applica**.

Fai quindi clic su **Aggiungi hardware > Scheda di rete > Aggiungi** per aggiungere la scheda (adattatore) di rete all'hardware.

Nel riquadro di destra, seleziona **Pubblica** dall'elenco a discesa e fai clic su **OK**.

### Configurazione dell'adattatore di rete virtuale per il sistema operativo Linux
{: #configuring-virtual-network-adapter-for-linux-operating-system}

Un elenco di hardware è ora disponibile per la macchina virtuale. Fai clic su **Scheda di rete > Rimuovi** e seleziona **Aggiungi hardware**. L'elenco sulla destra cambia.
Dall'elenco, seleziona **Scheda di rete legacy**. **Nota:** questa opzione è diversa dall'adattatore di rete che non è utilizzato su una macchina virtuale Linux.
Fai clic su **Aggiungi** per aggiungere questo adattatore di rete legacy all'elenco hardware. Il nuovo adattatore di rete legacy viene selezionato automaticamente.
Seleziona **Privata** dall'elenco a discesa e fai clic su **Applica**.
Ripeti i passi precedenti per aggiungere la scheda (adattatore) di rete legacy (**Scheda di rete legacy**). Tuttavia, seleziona **Pubblica**.
Dopo che la rete legacy è stata aggiunta, fai clic su **OK**.

## Installazione di un sistema operativo guest
{: #installing-a-guest-operating-system}

La macchina virtuale è ora pronta per essere avviata. Fai clic con il pulsante destro del mouse sulla macchina virtuale e seleziona **Connetti**. La console della macchina virtuale viene aperta. Dal menu **Azione**, seleziona **Start**. La nuova macchina virtuale viene avviata utilizzando il supporto di installazione da te selezionato. Procedi ora con l'installazione del sistema operativo. Durante la procedura di installazione del sistema operativo, ti consigliamo di configurare le impostazioni di rete pubblica. La rete pubblica è la seconda interfaccia di rete. Una volta completata l'installazione del sistema operativo, disponi di una macchina virtuale funzionante. Se è configurata una rete pubblica, puoi accedere alla macchina virtuale in remoto.

Il passo finale della procedura di installazione è la configurazione della rete privata. Se la macchina virtuale non è connessa alla rete privata, la procedura di installazione è completa. Per ulteriori informazioni sulla configurazione della rete privata, vedi [Configurazione di una rete di macchine virtuali](/docs/infrastructure/virtualization?topic=Virtualization-setting-up-a-virtual-machine-network).
