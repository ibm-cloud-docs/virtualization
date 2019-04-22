---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Installazione di VMware su CentOS 5.x (64-bit)
{: #installing-vmware-on-centos-5-x-64-bit-}

Prima di iniziare, esegui l'accesso al server con l'utente root.

## Preparazione all'installazione di VMware
{: #preparing-to-install-vmware}

1. Scarica il [programma di installazione VMware![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://vmware.com/download/server/){: new_window}. Fai clic sul link di download, accetta la EULA e scarica il LinuxTarball (VMware-server-1.0.3-44356.tar.gz in questo esempio).

* `# wget –O vmware-server.tar.gz https://download3.vmware.com/software/vmserver/VMware-server-1.0.3-44356.tar.gz`

2. Dopo che hai scaricato il software, dovrai ottenere una chiave di licenza (che è gratuita nella versione gratuita di VMware Server). Per eseguire la registrazione, vedi [VMware ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://register.vmware.com/content/registration.html){: new_window}.

3. Estrai il file .tar:

* `# tar -xzvf vmware-server.tar.gz`

## Prima di iniziare l'installazione
{: #before-you-begin-installation}

Prima di iniziare l'installazione, controlla i seguenti prerequisiti:

```
# yum update
# yum install libXtst.i386
# yum install libXrender.i386
# yum install xinetd
```

## Installazione di VMware Server
{: #installing-vmware-server}

1. Vai alla directory:

* `# cd vmware-server-distrib/`

2. Esegui lo script di installazione VMware:

* `# ./vmware-install.pl`

3. L'installazione presenta delle domande di base e vuole sapere quali directory creare e in cui installare alcune parti di VMware. Da qui, accetta i valori predefiniti. Accetta l'accordo di licenza per procedere con l'installazione.

4. Ti potrebbe essere fatta questa domanda: “None of the pre-built vmmon modules for VMware Server is suitable for your running kernel. Do you want this program to try to build the vmmon module for your system (you need a C compiler installed on your system)?”
* Rispondi **yes** a questa domanda (che è il valore predefinito).

## Configurazione della rete VMware
{: #setting-up-vmware-networking}

Ti vengono presentate le seguenti domande:

1. "Do you want to network your virtual machines?"
* Rispondi **yes**. Devi creare una configurazione di rete per il tuo dispositivo di rete pubblica in modo da poter accedere a internet sulle tue macchine virtuali.

2. “Your computer has multiple ethernet interfaces available: eth0, eth1. Which one do you want to bridge to vmnet0?”
* Tutti i server {{site.data.keyword.BluSoftlayer}} sono configurati con la rete pubblica in esecuzione su eth1 e la rete privata in esecuzione su eth0. In VMware, il dispositivo bridge predefinito per vmnet0 è eth0. Invece di premere Invio, immetti eth1.

## Bridging della rete privata
{: #bridging-the-private-network}

Ti viene presentata la seguente domanda, a cui puoi rispondere con **yes** o **no**:
*“Do you want to configure another bridged network?”

Se intendi eseguire i servizi o altre applicazioni sulla tua rete privata, procedi rispondendo “yes” a questa domanda (a meno che tu non sappia che non utilizzerai la rete privata) e verrà creato un bridge di rete alla tua rete privata. Dopo che avrai premuto Invio, utilizzerà automaticamente eth0 come interfaccia poiché è l'unica rimasta disponibile (poiché hai solo due schede di rete nel server).

## Altre impostazioni di rete
{: #other-networking-settings}

Ti viene presentata qualche altra domanda relativa alla configurazione di rete del VMware Server. Procedi con le seguenti raccomandazioni:

* *“Do you want to be able to use NAT networking in your virtual machines?”*

- Procedi con “yes”

* *“Do you want this program to probe for an unused private subnet?”*

- Procedi con “yes”

- Una volta completato questo processo, assicurati di non configurare un'altra rete NAT.

* *“Do you want to be able to use host-only networking in your virtual machines?”*

- Procedi con “yes”

* *“Do you want this program to probe for an unused private subnet?”*

- Procedi con “yes”

- Una volta completato questo processo, assicurati di non configurare un'altra rete solo host.

## Specifica della porta di ascolto
{: #specifying-listening-port}

La prossima domanda ti chiede di indicare la porta sulla quale desideri che sia in ascolto il VMware Server. Puoi lasciare la porta predefinita come 904.

## Archiviazione delle macchine virtuali
{: #storing-the-virtual-machines}

La domanda successiva presentata dal programma di installazione è *“In which directory do you want to keep your virtual machine files?”*. L'ubicazione predefinita è /var/lib/vmware/virtual machines. Assicurati di collocare le macchine virtuali in un'ubicazione dove disponi di molto spazio su disco, come un array RAID ridondante o un disco rigido secondario di grandi dimensioni. Assicurati sempre di disporre di spazio a sufficienza per una macchina virtuale. In questo caso, puoi utilizzare un punto di montaggio /data/vm che è montato su un disco di grandi dimensioni.

## Fornitura del numero di serie per VMware
{: #providing-the-serial-number-for-vmware}

La parte finale dell'installazione richiede che tu inserisca una chiave di licenza e un numero di serie di VMware. Se non disponi ancora di una chiave di licenza, consulta il [sito di VMware ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://register.vmware.com/content/registration.html){: new_window}. Se hai un numero di serie per questo VMware Server, inseriscilo nel prompt e premi quindi Invio.

Ora vedi qualcosa di simile al seguente messaggio:

    “The configuration of VMware Server 1.0.3 build-44356 for Linux for this running kernel completed successfully.”

VMware è ora configurato sul tuo server. Devi ora scaricare la VMware Server Console, che è il client GUI per il tuo VMware Server, configurare e installare le macchine virtuali.

## Download della VMware Server Console
{: #downloading-vmware-server-console}

La VMware Server Console è l'applicazione client per VMware Server. Utilizzi la console per gestire il VMware Server in cui puoi creare, configurare e installare le macchine virtuali. Per installare questa applicazione, scarica il pacchetto client VMware Server Windows da [VMware downloads ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://vmware.com/download/server/){: new_window}. Il pacchetto è il file .zip. Una volta completato il download del pacchetto, estrailo e installa il file VMware-console-1.0.3-x. Al termine dell'operazione, avrai completato l'installazione della VMware Server Console e sarai pronto a configurare il tuo VMware Server.

## Accesso alla VMware Console
{: #logging-in-to-the-vmware-console}

Apri la VMware Server Console dal computer su cui l'hai installata. Dopo il suo caricamento, ti viene presentato un prompt con una schermata “Switch Host” (login). VMware Server utilizza il nome utente e la password del sistema Linux per autenticare gli utenti; devi pertanto utilizzare i nomi utente (root in particolare) per eseguire l'accesso a VMware. Utilizza le seguenti credenziali:

* **Nome host:** indirizzo IP più porta (ad esempio 67.228.160.201:904)<br />
* **Nome utente:** root<br />
* **Password:** password (utilizza la password root effettiva del sistema)

## Configurazione delle regole del firewall (IPTable)
{: #configuring-the-firewall-rules-iptables-}

Se hai problemi a stabilire una connessione al VMware Server e non si tratta di un problema di autenticazione (se ottieni un errore di nome utente e password significa che hai un utente o una password errati), è possibile che il tuo firewall ti stia impedendo di stabilire una connessione al VMware Server. Per risolvere il problema, prova ad aggiungere la seguente regola IPTable nel tuo /etc/sysconfig/iptablesfile. **Nota:** assicurati che la convenzione di denominazione rispetti la tua configurazione del server:

- `# -A FWALL-INPUT -p tcp -m tcp -s 0/0 --dport 904 -j ACCEPT`
