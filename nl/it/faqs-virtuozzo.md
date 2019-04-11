---



copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# FAQ: Virtuozzo
{: #faqs-virtuozzo}

## Ho bisogno di indirizzi IP statici o portatili per Virtuozzo?
{: #do-i-need-static-or-portable-ip-addresses-for-virtuozzo-}

* Indirizzo IP statico: utilizza un indirizzo IP statico se hai solo un singolo nodo hardware (per ogni VLAN o in totale)
* Indirizzo IP portatile:
    * Routed to VLAN: utilizza questo metodo se hai più nodi host all'interno della stessa VLAN. Puoi anche utilizzare questo metodo per migrare un contenitore a un nodo hardware differente all'interno della stessa VLAN senza modificare l'IP del contenitore.
    * Secondary on VLAN: questo metodo funziona ma non hai bisogno di un gateway secondario. Perdi 3 degli indirizzi IP a causa di ID host, gateway e broadcast.

I costi per ogni IP possono variare in base al tipo di instradamento. I prezzi sono mostrati nel portale dopo che hai selezionato il server per cui stai ordinando gli indirizzi IP. L'instradamento può essere modificato in qualsiasi momento aprendo un ticket di supporto.
{:tip}

## Posso migrare un Virtuozzo 3 Containers a un nodo hardware Virtuozzo Containers 4 senza tempi di inattività?
{: #can-i-migrate-a-virtuozzo-3-container-to-a-virtuozzo-container-4-hardware-node-with-no-downtime-}

**Linux:**

Sì. Per impostazione predefinita, l'esecuzione di Virtuozzo Containers per Linux 4 avviene in modalità di compatibilità. Se Virtuozzo Containers non è in esecuzione in modalità di compatibilità, puoi eseguirne l'avvio utilizzando il seguente comando sul nodo hardware tramite SSH.

`/usr/sbin/vzagent_compat_ctl start`

**Windows (non supportato)**

Con Virtuozzo Containers 4 per Windows, hai un tempo di inattività durante la transizione (tra i 20 secondi e fino a 5 minuti) a causa della mancata disponibilità di un'opzione online da passare.

Se utilizzi Virtuozzo 3.0 per Windows, devi eseguire l'upgrade del nodo hardware ad almeno Virtuozzo 3.5.1 per Windows.

Virtuozzo Containers 4 per Windows, per impostazione predefinita, viene eseguito in modalità di compatibilità.

Il ripristino di backup da 3.5.1 su un nodo hardware Virtuozzo Containers 4 può anche essere eseguito copiando il backup completo nel nodo di destinazione, eseguendo `vzbackupsync.exe` e ripristinando il backup normalmente.

Assicurati che sul nuovo nodo hardware siano installati gli stessi template.
{:tip}

## I contenitori sono isolati l'uno dall'altro e dall'host?
{: #are-containers-isolated-from-each-other-and-the-host-}

Ai tuoi contenitori non è consentito caricare LKM ed eseguire `chroot(“../../../”)` dal loro ambiente e non condividono risorse IPC (insiemi di semafori e segmenti di memoria condivisi).

Non hai alcuna preoccupazione in materia di sicurezza relativamente al modo in cui Virtuozzo gestisce le risorse e isola i processi. Con una configurazione di rete con bridge, tutti i contenitori nella stessa VLAN (sullo stesso nodo hardware) possono vedere il rispettivo traffico. L'utilizzo della modalità instradata è caldamente consigliato.

## Perché non riesco a memorizzare in cache i template di sistema operativo RedHat e SUSE in Virtuozzo?
{: #why-can-t-i-cache-redhat-and-suse-os-templates-in-virtuozzo-}

Puoi riscontrare dei problemi quando memorizzi in cache i template di sistema operativo RedHat e SUSE, se utilizzi Virtuozzo. Questi template di sistema operativo richiedono delle licenze supplementari che non sono fornite da {{site.data.keyword.cloud_notm}}. Se ricevi qualcuno di questi errori, devi avere dei tuoi account con i rispettivi fornitori e configurare le variabili corrispondenti in /etc/vztt/url.map (/etc/vztt/vztt.conf in Virtuozzo 3).

||||
|---|---|---|
|DATA/ORA|Click here to open/close the operation details.Update OS Template Cache|**Failed**|
|DATA/ORA|Operation update with the Env(s) "virtuozzo00.softlayer.local" and package(s) .redhat-as4-x86_64 is started||
|DATA/ORA|Operation update with the Env(s) "virtuozzo00.softlayer.local" is finished with errors: Can not update packages: exec failed: Error: URL $RH_SERVER/download/mirrors/redhat-as4 contents undefined variable You can define this variable in /etc/vztt/url.map. .|**Failed**|
|DATA/ORA|Click here to open/close the operation details.Update OS Template Cache|**Failed**|
|DATA/ORA|Operation update with the Env(s) "virtuozzo00.softlayer.local" and package(s) .redhat-el5-x86_64 is started||
|DATA/ORA|Operation update with the Env(s) "virtuozzo00.softlayer.local" is finished with errors: Can not update packages: exec failed: Error: URL $RH_SERVER/download/mirrors/redhat-el5 contents undefined variable You can define this variable in /etc/vztt/url.map. See Virtuozzo Installation Guide for more details. .|**Failed**|
|DATA/ORA|Click here to open/close the operation details.Update OS Template Cache|**Failed**|
|DATA/ORA|Operation update with the Env(s) "virtuozzo00.softlayer.local" and package(s) .sles-10-x86_64 is started||
|DATA/ORA|Operation update with the Env(s) "virtuozzo00.softlayer.local" is finished with errors: Can not update packages: exec failed: Error: URL $SLES_SERVER/download/mirrors/suse-es10 contents undefined variable You can define this variable in /etc/vztt/url.map. See Virtuozzo Installation Guide for more details. .|**Failed**|
|DATA/ORA|Click here to open/close the operation details.Update OS Template Cache|**Failed**|
|DATA/ORA|Operation update with the Env(s) "virtuozzo01.softlayer.local" and package(s) .redhat-as4-x86_64 is started||
|DATA/ORA|Operation update with the Env(s) "virtuozzo01.softlayer.local" is finished with errors: Can not update packages: exec failed: Error: URL $RH_SERVER/download/mirrors/redhat-as4 contents undefined variable You can define this variable in /etc/vztt/url.map. See Virtuozzo Installation Guide for more details. .|**Failed**|
|DATA/ORA|Click here to open/close the operation details.Update OS Template Cache|**Failed**|
|DATA/ORA|Operation update with the Env(s) "virtuozzo01.softlayer.local" and package(s) .redhat-el5-x86_64 is started||
|DATA/ORA|Operation update with the Env(s) "virtuozzo01.softlayer.local" is finished with errors: Can not update packages: exec failed: Error: URL $RH_SERVER/download/mirrors/redhat-el5 contents undefined variable You can define this variable in /etc/vztt/url.map. .|**Failed**|
|DATA/ORA|Click here to open/close the operation details.Update OS Template Cache          Failed<br/><br/>Oct 29, 2008 01:16:56 PM     Operation update with the Env(s) "virtuozzo01.softlayer.local" and package(s) .sles-10-x86_64 is started||
|DATA/ORA|Operation update with the Env(s) "virtuozzo01.softlayer.local" is finished with errors: Can not update packages: exec failed: Error: URL $SLES_SERVER/download/mirrors/suse-es10 contents undefined variable .|**Failed**|
|DATA/ORA|Completing the process|**Failed**|

## Perché posso eseguire il ping del mio ServiceVE ma non posso eseguire l'accesso mediante SSH (PIM/PMC)?
{: #why-can-i-ping-my-serviceve-but-cannot-log-in-to-via-ssh-pim-pmc-}

Assicurati che il nodo host non abbia indirizzi IP assegnati diversi dagli indirizzi IP elencati nel Portale (1 IP pubblico IP e 1 IP privato [che rientrano nell'intervallo 10.0.0.0/8]).

Se hai dei file di intervallo configurati sul nodo host, devi rimuoverli e riavviare la rete.

    [root@virtuozzo ~]# mv –vi /etc/sysconfig/network-scripts/eth[0-9]-range[0-9]* ~/.sl-orig-configs/

    [root@virtuozzo ~]# /sbin/service network restart


`vzcp` non è avviato nel Service VE. Avvia `vzcp` nel Service VE con il nodo hardware.

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp status

    vzcp is stopped

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp start

    Starting vzcpd: [  OK  ]

    Starting vzcp: [  OK  ]

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp status

    vzcp (pid 3775 3774 3771) is running...

    [root@virtuozzo ~]#
