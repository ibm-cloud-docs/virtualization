---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-11"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Abilitazione dell'accesso pubblico a VMware
{: #enabling-public-access-to-vmware}

Per impostazione predefinita, l'host ESX viene installato con la console di servizio solo sulla rete privata, il che significa che il traffico pubblico verso e dalla rete pubblica non è consentito sull'host ESX..

Per iniziare, devi stabilire una connessione al server utilizzando un VMware vSphere Client sull'interfaccia privata del server attenendoti alla seguente procedura:

1. Fai clic su **ESX Host > scheda Configuration > Networking > Add Networking**
2. Seleziona Service Console.
3. Seleziona un vSwitch da utilizzare. In questo caso, si tratta delle interfacce esterna/pubblica; seleziona pertanto **vSwitch1** (vmnic1 e vmnic3).
4. Rinomina la Service Console in qualcosa facilmente riconoscibile (ad esempio "Service Console pubblica").
5. Immetti l'indirizzo IP esterno primario assegnato al server con la maschera di sottorete corretta.
6. Fai clic su **Edit** e aggiungi il gateway predefinito che è assegnato al server.
