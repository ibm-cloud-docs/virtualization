---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization

keywords: ESXi, VMware
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Abilitazione dell'accesso pubblico a VMware ESXi
{: #enabling-public-access-to-vmware-esxi}

Per impostazione predefinita, l'host ESXi è installato con la console di servizio solo sulla rete privata. impedendo così il traffico pubblico verso e dalla rete pubblica sull'host ESXi.

Per iniziare, devi stabilire una connessione al server utilizzando un VMware vSphere Client sull'interfaccia privata per il server.

Devi disporre delle informazioni sull'IP pubblico 'primario' del server per completare la configurazione. Le informazioni relative all'IP pubblico del server sono disponibili nel [{{site.data.keyword.slportal_full}} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://control.softlayer.com/){: new_window}.
{:tip}

Dopo che hai eseguito l'accesso all'host ESXi, attieniti alla seguente procedura per abilitare l'interfaccia pubblica:

1. Potresti dover eseguire l'accesso al server su SSH utilizzando l'IP privato per correggere l'instradamento.
* Esegui `esxcfg-route -l` per determinare gli instradamenti correnti
* Esegui `esxcfg-route -a 10.0.0.0/8 [l'IP gateway privato del tuo server]`
* Esegui `esxcfg-route [il gateway pubblico del tuo server]` per assicurarti che il gateway pubblico sia quello predefinito

Esegui l'accesso a vSphere e attieniti alla seguente procedura:

1. Fai clic sulla scheda Configuration e fai clic su **Networking**.
2. Per vSwitch1, fai clic su **Properties > Add**
3. Scegli **VM Kernel** e fai clic su **Next**
* Le impostazioni possono essere lasciate invariate. Puoi rietichettare la rete come "VMKernelPublic" per scopi di identificazione.
4. Immetti le informazioni sull'IP pubblico del server.
5. Per il gateway predefinito del kernel di VM, fai clic su **Edit**, immetti l'IP gateway pubblico e fai clic su **OK**.
* Fai clic su **OK**. **Nota:** una disconnessione è normale e prevista.*

Puoi ora accedere al server utilizzando sia gli indirizzi IP pubblici che quelli privati.

L'abilitazione dell'accesso IP pubblico a ESXi contiene dei rischi per la sicurezza intrinseci. Assicurati di eseguire le operazioni appropriate per limitare l'accesso a ESXi solo agli utenti che ne hanno bisogno.
{:tip}
