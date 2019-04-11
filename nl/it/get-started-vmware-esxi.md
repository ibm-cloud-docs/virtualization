---
copyright:
  years: 2014, 2018
lastupdated: "2018-02-14"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Introduzione a VMware ESXi
{: #getting-started-with-vmware-esxi}

Dopo che è stato eseguito il provisioning del tuo server ESX, il primo passo per accedere a esso consiste nello stabilire una connessione alla VPN della rete privata {{site.data.keyword.cloud}}. Per impostazione predefinita, tutti i server VMware distribuiti a {{site.data.keyword.cloud_notm}} hanno solo l'interfaccia di rete privata o l'associazione configurate con un indirizzo IP. Questa distribuzione è una misura di sicurezza supplementare implementata per limitare l'esposizione del tuo nuovo server ESX all'internet pubblico. Come risultato di questa sicurezza aggiunta, il server è in ascolto solo sull'indirizzo IP 10.x.x.x {{site.data.keyword.cloud_notm}} privato, che è accessibile solo tramite la VPN {{site.data.keyword.cloud_notm}} o un altro dei tuoi host {{site.data.keyword.cloud_notm}} esistenti con accesso alla stessa VLAN privata sui cui risiede il nuovo server VMware.

Per ulteriori informazioni sulla VPN {{site.data.keyword.cloud_notm}}, vedi [Introduzione alla VPN (Virtual Private Networking)](/docs/infrastructure/iaas-vpn?topic=VPN-getting-started-with-virtual-private-networking-vpn-).

## Accesso al tuo host ESX
{: #accessing-your-esx-host}

Dopo che hai stabilito una connessione alla VPN {{site.data.keyword.cloud_notm}}, puoi comunicare con il tuo nuovo server VMware. ESX è gestito da un client vSphere. Puoi eseguire download dal tuo nuovo server VMware immettendo l'indirizzo IP di rete privato un browser web e facendo clic sul link "Download vSphere Client" nella pagina risultante.

Dopo che il client vSphere è stato scaricato e dopo che lo hai installato sulla tua workstation locale, puoi avviare l'applicazione e immettere il tuo indirizzo privato e le tue credenziali di accesso del server. Le tue credenziali di accesso sono visualizzate nella pagina Device nel {{site.data.keyword.slportal_full}} dopo che hai fatto clic sulla scheda Password.

1. Immetti l'indirizzo IP privato del server, l'utente root e la password nei campi appropriati del clienti vSphere e fai clic su **Login** per stabilire la connessione.
2. Ora che sei connesso al server, vai al tuo nuovo host ESX selezionando **Inventory**
3. Il tuo host ESX viene mostrato come un nodo disponibile per la configurazione nella pagina risultante. Da qui, disponi di diversi modi per distribuire una macchina virtuale. Un metodo consiste nel caricare una ISO di installazione del tuo sistema operativo preferito nell'archivio dati locale del server. Dopo che l'ISO è stata caricata, puoi selezionarla come supporto di installazione quando viene creata una VM.  

## Caricamento di una ISO
{: #uploading-an-iso}

Completa la seguente procedura per caricare una ISO sull'archivio dati del server.

1. Evidenzia il server (rappresentato da un icona di server e dal suo indirizzo IP privato) nel riquadro di sinistra e seleziona la scheda **Configuration** nel riquadro di destra.
2. Seleziona **Hardware > Storage**. Assicurati che **View** sia impostata su **Datastores**.
3. Fai clic con il pulsante destro del mouse sull'archivio dati appropriato e fai clic su **browse datastore**.
4. La pagina risultante ha un gestore file per sfogliare e caricare file sull'archivio dati e per scaricarli da esso.  
  * Per caricare una ISO, fai clic sull'icona del volume (con la freccia che punta verso l'alto) e seleziona **Upload File**.
5. Seleziona il file ISO sul tuo filesystem locale che desideri caricare nell'archivio dati e fai clic su **Open**.
6. L'ISO di tua scelta si trova ora nell'archivio dati.
7. Ora che il tuo supporto di installazione si trova sul server VMware, puoi procedere con la [creazione della tua macchina virtuale](/docs/infrastructure/vmware?topic=VMware-creating-a-vmware-esx-virtual-machine).
