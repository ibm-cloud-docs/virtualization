---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization
---

# Guida di riferimento rapido a Parallels Virtuozzo Containers 4.0
{: #parallels-virtuozzo-containers-4-0-quick-reference-guide}

Utilizza queste informazioni per trovare i termini comuni e i programmi di utilità di contenitore Virtuozzo.

## Programmi di utilità generali
{: #general-utilities}

I programmi di utilità generali sono per le attività di manutenzione giornaliere.

|Programmi di utilità generali||
|---|---|
|vzctl|Programma di utilità per controllare i contenitori.|
|vzlist|Programma di utilità per visualizzare un elenco dei contenitori esistenti sul nodo con informazioni aggiuntive.|
|vzquota|Programma di utilità per controllare le quote di disco dei contenitori Virtuozzo.|
{: caption="Tabella 1. Programmi di utilità generali" caption-side="top"}

## Programmi di utilità di licenza
{: #licensing-utilities}

I programmi di utilità di licenza sono utilizzati per installare una nuova licenza, visualizzare lo stato della licenza o generare una richiesta di licenza per una nuova licenza.

|Programmi di utilità di licenza||
|---|---|
|vzlicview|Programma di utilità per visualizzare lo stato e i parametri delle licenze Virtuozzo.|
|vzlicload|Programma di utilità per gestire le licenze Virtuozzo sul nodo hardware.|
|vzlicupdate|Programma di utilità per attivare l'installazione dei contenitori Virtuozzo, aggiornare le licenze Virtuozzo installate sul nodo hardware o trasferire la licenza Virtuozzo dal nodo di origine al nodo di destinazione.|
{: caption="Tabella 2. Programmi di utilità di licenza" caption-side="top"}

## Programmi di utilità di migrazione di contenitori
{: #container-migration-utilities}

Gli strumenti di migrazione di contenitori sono utilizzi per eseguire la migrazione di contenitori tra i nodi hardware o all'interno di un singolo nodo hardware.

|Programmi di utilità di migrazione di contenitori||
|---|---|
|vzmigrate|Programma di utilità per eseguire la migrazione di contenitori da un nodo hardware a un altro.|
|vzmlocal|Programma di utilità per lo spostamento o la clonazione in locale dei contenitori.|
|vzp2v|Programma di utilità per eseguire la migrazione di un server fisico a un contenitore sul nodo.|
|vzv2p|Programma di utilità per eseguire la migrazione di un contenitore a un server fisico.|
{: caption="Tabella 3. Programmi di utilità di migrazione di contenitori" caption-side="top"}

## Programmi di utilità di backup di contenitori
{: #container-backup-utilities}

I programmi di utilità di backup di contenitori sono utilizzati per eseguire il backup e il ripristino delle informazioni sulle quote, degli script di azione, dei file di configurazione e delle aree private dei contenitori.

|Programmi di utilità di backup di contenitori||
|---|---|
|vzbackup|Programma di utilità per eseguire il backup di contenitori.|
|vzrestore|Programma di utilità per ripristinare i contenitori di cui era stato eseguito il backup.|
|vzabackup|Programma di utilità per eseguire il backup di nodi hardware e dei loro contenitori. Questo programma di utilità richiede il software Parallels Agent.|
|vzarestore|Programma di utilità per ripristinare contenitori e nodi hardware di cui era stato eseguito il backup. Questo programma di utilità richiede il software Parallels Agent.|
{: caption="Tabella 4. Programmi di utilità di backup di contenitori" caption-side="top"}

## Programmi di utilità di gestione di template
{: #template-management-utilities}

Gli strumenti di gestione di template sono utilizzati per creare template e per gestire e installare applicazioni in un contenitore.

|Programmi di utilità di gestione di template||
|---|---|
|vzpkg|Programma di utilità per gestire i template EZ di applicazione e sistema operativo all'interno dei tuoi contenitori oppure sul nodo hardware stesso.|
|vzmktmpl|Programma di utilità per creare template EZ di applicazione e sistema operativo.|
|vzveconvert|Programma di utilità per convertire i contenitori basati sui template standard Virtuozzo in contenitori basati su template EZ.|
|vzpkgproxy|Programma di utilità per creare i server proxy di memorizzazione in cache per gestire i template EZ di applicazione e sistema operativo.|
|vzrhnproxy|Programma di utilità per creare i server proxy RHN per gestire i pacchetti inclusi nei template EZ del sistema operativo RHEL 4 e RHEL 5.|
|vzpkgls|Programma di utilità per ottenere un elenco dei template disponibili sul nodo hardware e nei contenitori.|
|vzpkginfo|Programma di utilità per ottenere le informazioni su qualsiasi template installato sul nodo hardware.|
|vzpkgcreat| Crea un nuovo insieme di pacchetti dai file binari RPM o DEB.|
|vzpkgadd|Programma di utilità per aggiungere un template a un contenitore.|
|vzpkglink|Programma di utilità per sostituire i file effettivi all'interno di un contenitore con i collegamenti simbolici ai file sul nodo.|
|vzpkgrm|Programma di utilità per rimuovere un template da un contenitore.|
|vzpkgcache|Aggiorna una serie di archivi contenitore preinstallati dopo l'installazione del nuovo template.|
{: caption="Tabella 5. Programmi di utilità di gestione di template" caption-side="top"}

## Strumenti supplementari
{: #supplementary-tools}

Gli strumenti supplementari vengono utilizzati per attività varie nel contesto di nodi hardware e contenitori.

|Strumenti supplementari||
|---|---|
|vzup2date|Programma di utilità per aggiornare il tuo software e i tuoi template Virtuozzo.|
|vzup2date-mirror|Programma di utilità per creare dei mirror locali del repository ufficiarle di Virtuozzo.|
|vzfsutil|Programma di utilità per l'ottimizzazione e il controllo della congruenza di VZFS.|
|vzcache|Programma di utilità per ottenere dello spazio su disco supplementare memorizzando in cache i file identici in contenitori diversi.|
|vzsveinstall|Programma di utilità per creare il contenitore di servizio sul nodo hardware.|
|vzsveupgrade|Programma di utilità per aggiornare i pacchetti all'interno del contenitore di servizio.|
|vzps|Programma di utilità che funziona come i programmi di utilità ps e htop standard, con aggiunte le funzioni correlate ai contenitori.|
|vztop|Programma di utilità che funziona come i programmi di utilità ps e htop standard, con aggiunte le funzioni correlate ai contenitori.|
|vzsetxinetd|Programma di utilità per commutare alcuni servizi tra la modalità autonoma e la modalità `xinetddependent`.|
|vzdqcheck|Stampa l'utilizzo corrente dello spazio file dal punto di vista della quota.|
|vzdqdump|Programma di utilità per eseguire il dump dei periodi di tolleranza e dei limiti di quota di gruppi o utenti del contenitore dal kernel o dal file di quota oppure per caricarli in un file di quota.|
|vzdqload|Programma di utilità per eseguire il dump dei periodi di tolleranza e dei limiti di quota di gruppi o utenti del contenitore dal kernel o dal file di quota oppure per caricarli in un file di quota.|
|vznetstat|Programma di utilità che stampa le statistiche di utilizzo del traffico di rete dai contenitori.|
|vzcpucheck|Programma di utilità per controllare l'utilizzo della CPU dai contenitori.|
|vzmemcheck|Programma di utilità per controllare i parametri di memoria correnti di nodi hardware e contenitori.|
|vzcalc|Programma di utilità per calcolare l'utilizzo delle risorse da un contenitore.|
|vzcheckovr|Programma di utilità per controllare l'overcommit del sistema corrente e la sicurezza delle impostazioni di controllo delle risorse totali.|
|vzstat|Programma di utilità per monitorare l'utilizzo di risorse di contenitori e nodi hardware in tempo reale.|
|vzpid|Programma di utilità che stampa l'ID contenitore a cui appartiene il processo.|
|vzsplit|Programma di utilità per generare l'esempio di file di configurazione del contenitore, “suddividendo” il nodo hardware in parti uguali.|
|vzcfgscale|Programma di utilità per ridimensionare la configurazione del contenitore.|
|vzcfgvalidate|Programma di utilità per convalidare la correttezza del file di configurazione del contenitore.|
|vzcfgconvert|Programma di utilità per convertire i file di configurazione del contenitore Virtuozzo 2.0.2 in formato Virtuozzo 2.5.x.|
|vzstatrep|Programma di utilità per analizzare i log raccolti da vzlmond e per generare i report di statistiche basati su tali log (in formato testuale e grafico).|
|vzreport|Programma di utilità per redigere un report dei problemi e per inviarlo automaticamente al team di supporto Parallels.|
|vzhwcalc|Programma di utilità per eseguire la scansione delle risorse principali su qualsiasi server Linux e creare un file in cui vengono specificate tali informazioni.|
|vzveconvert|Programma di utilità per convertire i contenitori basati sui template di sistema operativo standard di Virtuozzo in quelli basati sui template EZ.|
|vznetcfg|Programma di utilità per gestire i dispositivi di rete sul nodo hardware.|
|vzmtemplate|Programma di utilità per eseguire la migrazione dei template di applicazione e sistema operativo installati da un nodo hardware a un altro.|
{: caption="Tabella 6. Strumenti supplementari" caption-side="top"}

## Termini comuni
{: #common-terms}

|Termini comuni||
|---|---|
|Nodo hardware|Un nodo hardware (o HN, Hardware Node) è un computer fisico che ospita i contenitori.|
|Contenitore|Un contenitore è funzionalmente identico a un server autonomo isolato con dei propri indirizzi IP, processi, file e utenti, una propria versione, dei propri file di configurazione e delle proprie applicazioni, librerie di sistema e altre funzioni server. I contenitori condividono i nodi hardware e lo stesso kernel del sistema operativo ma sono isolati l'uno dall'altro. Ogni contenitore ha un ID numerico univoco che inizia con 1.|
|Sistema operativo host|Un sistema operativo host (oppure host) è un sistema operativo installato sul nodo hardware. È indicato anche come contenitore 0, per distinguerlo dai contenitori effettivi che hanno degli ID che iniziano con 1.|
|Template|Template (o insieme di pacchetti) è un insieme di file applicazione originali che sono riassemblati per il montaggio su VZFS (Virtuozzo File System). In Virtuozzo sono disponibili due tipi di template: i template di sistema operativo, che possono essere utilizzati per creare nuovi contenitori sulla loro base, e i template di applicazione, che possono essere aggiunti ai contenitori dopo la loro creazione.|
|PIM|Parallels Infrastructure Manager (o PIM) è uno strumento di gestione basato sul web che è progettato per gli amministratori host.|
|PMC|Parallels Management Console (o PMC) è uno strumento di gestione remota con una GUI (graphical user interface) progettata per gli amministratori host.|
|PPP|Parallels Power Panel è uno strumento di gestione basato sul web che è progettato per i proprietari di contenitori.|
{: caption="Tabella 7. Termini comuni" caption-side="top"}
