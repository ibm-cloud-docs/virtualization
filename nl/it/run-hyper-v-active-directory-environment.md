---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-10"

subcollection: virtualization

keywords: hypervisor
---
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:table: .aria-labeledby="caption"}

# Esecuzione di Hyper-V in un ambiente Active Directory
{: #running-hyper-v-in-an-active-directory-environment}

<!--Running Hyper-V in an Active Directory environment is the best implementation of Hyper-V. Microsoft has truly shined with the ability to remotely manage a server.You can remotely manage the Hyper-V server allows for it to be installed on a Core installation of Windows freeing up those valuable resources from the system for use within the VM’s. When this is combined with an Active Directory Domain Controller
You manage all your Hyper-V servers from a single Hyper-V Manager that runs on any 2008 or Vista computer that is connected to the Domain.--> <!--Vista?? MS doesn't support Vista.-->

## Prima di iniziare
{: #before-you-begin-running-hyper-v-in-an-active-directory-environment}

Prima di iniziare a eseguire Hyper-V in un ambiente Active Directory, hai bisogno di quanto segue:

* Un server Windows 2008 Datacenter a 64 bit che esegue Hyper-V (installazione completa o dei soli componenti di base del sistema operativo).
* Un gruppo locale sul dominio utilizzato per gestire Hyper-V.
* L'accesso di amministratore al dominio per abilitare le modifiche sul server Hyper-V dal computer dal quale stai lavorando.

## Configurazione del server Hyper-V
{: #configuring-the-hyper-v-server}

1. Apri una connessione di gestione al server Hyper-V.

2. Aggiungi il gruppo **Hyper-V** al gruppo **Distributed COM Users**.

3. Aggiungi il gruppo **Hyper-V** agli spazi dei nomi **CIMV2 e Virtualization**.

4. Aggiungi il gruppo **Hyper-V** all'Archivio autorizzazioni per Hyper-V sul server Hyper-V.

5. Fornisci le autorizzazioni del gruppo **Hyper-V** alla directory Hyper-V sul server Hyper-V.

## Configurazione della connessione di gestione remota
{: #setting-up-remote-management-connection}

Assicurati di avere eseguito l'accesso in un computer sul dominio con i privilegi di amministratore del dominio.

1. Vai a **Pannello di controllo > Strumenti di amministrazione > Gestione computer**.

2. Nel menu Azioni, seleziona **Connetti a un altro computer**.

3. Immetti il nome server o l'IP e fai clic su **OK**.

## Aggiunta del gruppo Distributed COM Users al server Hyper-V
{: #adding-distributed-com-users-group-to-the-hyper-v-server}

Devi prima aggiungere il gruppo Hyper-V al gruppo Distributed COM Users sul server Hyper-V.

1. Vai a **Utilità di sistema > Utenti e gruppi locali > Gruppi > Distributed COM Users > Aggiungi al gruppo**.

2. Fai clic su **Aggiungi**, immetti il nome del gruppo per il gruppo Hyper-V e fai clic su **OK**.

## Concessione dell'accesso remoto al server per gli spazi dei nomi CIMV2 e Virtualization
{: #granting-remote-access-to-the-server-for-cimv2-and-virtualization-namespaces}

Puoi aggiornare le autorizzazioni per l'accesso remoto al server per Virtualization e CIMV2.

1. Dalla finestra Gestione computer, seleziona **Servizi e applicazioni > Controllo WMI**.

2. Fai clic con il pulsante destro del mouse e vai a **Proprietà > Sicurezza > Root > CIMV2 > Sicurezza**.

3. Aggiungi il gruppo Hyper-V, quindi selezionalo e fai clic su **Avanzate**.

4. Assicurati che il nuovo gruppo sia selezionato e fai clic su **Modifica**.

5. Modifica **Applica a:** in **Questi spazio e sottospazi dei nomi**.

6. Per **Abilita account e Abilita remoto**, assicurati che sia selezionato **Consenti**.

7. Seleziona **Applica queste autorizzazioni solo a oggetti e/o contenitori in questo contenitore** e fai clic su **OK**.

8. Ripeti questi passi per Virtualization.

## Aggiornamento dell'archivio autorizzazioni
{: #updating-authorization-store}

L'archivio autorizzazioni per Hyper-V è il componente finale che dà il gruppo di dominio a Hyper-V.

1. Apri Gestione autorizzazioni eseguendo il comando `azman.msc` dal menu Esegui o da un prompt di comandi.

2. Dal menu Azione, seleziona **Apri archivio autorizzazioni**.

3. Assicurati che **XML** sia selezionato. Ora devi accedere in remoto a `InitalStore.xml` sul server Hyper-V. Utilizza il seguente percorso:

`\HOSTNAME\c$\ProgramData\Microsoft\Windows\Hyper-V\InitialStore.xml`

4. Vai a **Servizi Hyper-V > Assegnazioni di ruolo > Amministratore**.

5. Dal **menu Azione**, seleziona **Assegna utenti e gruppi > Da Windows e Active Directory**.

6. Aggiungi il gruppo Hyper-V.

## Concessione delle autorizzazioni cartella
{: #granting-folder-permissions}

Ora che il gruppo Hyper-V dispone di autorizzazioni complete alla gestione in remoto di Hyper-V, devi concedere le autorizzazioni per scrivere nella cartella `C:\Users\Public\Documents\Hyper-V`.

1. Apri Risorse del computer e vai al seguente indirizzo:

`\HOSTNAME\c$\Users\Public\Documents`

2. Vai a **Hyper-V > Proprietà > Sicurezza**

3. Aggiungi il gruppo Hyper-V e assicurati che possa leggere, scrivere ed eseguire file da tale directory. In generale, è più facile assegnare un **Controllo completo**.

## Finalizzazione della configurazione
{: #finalizing-configuration}

Tutte le modifiche della configurazione sono state completate. Per finalizzare la configurazione, devi riavviare il server Hyper-V. Dopo che il server sarà di nuovo online, stabilisci una connessione ad esso dalla tua Console di gestione Hyper-V locale. Ora hai il pieno accesso per gestire tutte le VM e il servizio Hyper-V.
