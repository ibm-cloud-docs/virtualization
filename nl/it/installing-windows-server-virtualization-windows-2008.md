---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-23"

subcollection: virtualization

keywords: Windows 2008
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Installazione della virtualizzazione Windows Server su Windows 2008
{: #installing-windows-server-virtualization-on-windows-2008}

<!--Windows 2008 64-bit edition comes with the option to install Windows next generation virtualization application codenamed Veridian. By default the application will not be a selectable option when Adding and Removing Roles from Windows 2008.

Note: At this time, this is only available in full installation of 64-bit editions of Windows 2008. 32-bit versions and Server-core installations do not support this.-->

## Prima di iniziare
{: #before-you-begin-win-2008}

Prima di iniziare, controlla i seguenti prerequisiti.

### Hardware
{: #hardware}

* Tecnologia di virtualizzazione assistita dall'hardware abilitata nel BIOS
* Intel VT
* AMD-V
* Data Execution Prevention abilitato nel BIOS
* Intel Execute Disable (XD)
* AMD No Execute (NX)

### Software
{: #software}

Per abilitare l'opzione **Ruolo** per la virtualizzazione Windows Server, è necessario installare alcune patch.
1. Apri una finestra del browser e vai a %sysdir%\Windows\wsv, di norma C:\Windows\wsv. In quella cartella saranno presenti due file:
    * Windows6.0-KB939853-x64.msu
    * Windows6.0-KB939854-x64.msu
2. Queste patch possono essere installate in qualsiasi ordine. Installa entrambe le patch e riavvia quindi il sistema.

## Installazione
{: #installation}

1. Dopo che il tuo sistema sarà stato riavviato, devi aggiungere il ruolo (**Ruolo**) al sistema.
2. Dopo che hai aggiunto il ruolo, viene visualizzata la finestra di dialogo **Crea reti virtuali**.
**Nota:** la connettività di rete si interromperà per un breve periodo.
3. Seleziona **Connessione alla rete locale (LAN)**, il tuo adattatore di rete privato. Fai clic su **Continua**. L'installazione continua e ti richiede il riavvio.
4. Dopo che il sistema è stato riavviato, esegui l'accesso al sistema mediante la connessione pubblica. **Nota:** devi eseguire l'accesso con le stesse credenziali di accesso che hai utilizzato per installare questa connessione. Perderai la connettività di rete all'interfaccia. In caso contrario, potresti ricevere il seguente errore:
    * *Tentativo di configurazione Windows Server Virtualization non riuscito. Codice errore: 0x80078000.*
Per risolvere l'errore, vai a **Start > Programmi > Strumenti di amministrazione > Windows Virtualization Management** nella console di gestione per la virtualizzazione di Windows.
5. Nel riquadro a destra, fai clic sul server appropriato.
6. Quindi, nel riquadro dell'azione, fai clic su **Gestione reti virtuali**. Puoi ora vedere **Gestione commutatori di rete virtuali**.
7. Nel riquadro a sinistra, fai clic sul commutatore (switch) di rete sotto **Aggiungi nuovo**.
8. Rinomina il commutatore (switch) di rete come privato.
9. Seleziona Scheda di rete fisica per **Connessione** e seleziona la prima scheda (adattatore) di rete. Tutti i protocolli di rete sono ora svincolati dall'interfaccia di rete privata.
10. **IMPORTANTE** per ristabilire la connettività di rete su privata, devi configurare il nuovo dispositivo di commutazione, **NON** l'interfaccia privata. Vai a **Start > Impostazioni > Connessioni di rete**. Vedi un nuovo dispositivo denominato **Connessione alla rete locale 2**.
11. Fai clic con il pulsante destro del mouse su questa connessione e vai a **Proprietà**.
12. Seleziona **ipv4** e le relative proprietà. Devi configurare questo dispositivo con i server DNS, la maschera di rete e l'indirizzo IP dell'interfaccia di rete privata.
13. Fai clic su **OK > Chiudi**. Questa configurazione riabilita la rete sul lato privato. Puoi verificare che la rete sia privata eseguendo il ping dell'IP privato.

Esegui RDP all'IP privato per configurare la rete pubblica.

### Aggiunta di un commutatore (switch) pubblico
{: #adding-a-public-switch}

L'aggiunta di un commutatore (switch) pubblico segue la stessa procedura prevista per l'aggiunta di un commutatore (switch) privato.
1. Torna a **Gestione commutatori di rete virtuali** e seleziona **Aggiungi nuovo commutatore di rete**.
2. Seleziona **Esterno** come tipo di commutatore (switch) di rete e fai clic su **Aggiungi**.
3. Rinomina il commutatore (switch) come **Pubblico** e seleziona **Scheda di rete fisica**.
4. Seleziona la seconda scheda (adattatore) di rete e fai clic su **OK**. Questa configurazione fa sì che la porta pubblica non risponda alla rete. Configura la nuova interfaccia di commutatore (switch) pubblico proprio come hai fatto per quello privato con le impostazioni appropriate.
