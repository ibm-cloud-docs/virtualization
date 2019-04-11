---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Descrizione dei valori UBC (User Beancounter)
{: #understanding-ubc-user-beancounter-values}

Se UBC non è chiaro, usa la modalità SLM. SLM è più facile e fornisce lo stesso tipo di controllo.

|Nome|Tipo|Descrizione|
|---|---|---|
|*Parametri principali*|||
|`numproc`|*sistema*|Numero di processi e thread|
|`numtcpsock`|*sistema*|Numero di socket TCP|
|`numothersock`|*sistema*|Numero di socket diversi da TCP|
|`vmguardpages`|*sistema*|Garanzia di assegnazione di memoria|
|`cpuunits`|*cpu*|Potenza della CPU|
|`diskspace`|*disco*|Quota di spazio su disco|
|`rate`|*rete*|Larghezza di banda della rete|
|`ratebound`|*rete*|Indica se la larghezza di banda di rete è limitata|
|*Parametri aggiuntivi*|||
|`kmemsize`|*sistema*|Dimensione della memoria kernel di cui non è possibile eseguire lo swap che è assegnata per i processi in questo contenitore|
|`tcpsndbuf`|*sistema*|Dimensione totale dei buffer di invio TCP|
|`tcprcvbuf`|*sistema*|Dimensione totale dei buffer di ricezione TCP|
|`othersockbuf`|*sistema*|Dimensione totale dei buffer di socket di dominio UNIX, UDP e altri buffer di invio di protocollo datagramma|
|`dgramrcvbuf`|*sistema*|Buffer di ricezione di UDP e altri protocolli datagramma|
|`oomguardpages`|*sistema*|La quantità di memoria garantita in caso di sovraprenotazione della memoria (una terminazione dei processi verrà avviata automaticamente se si verifica una condizione di memoria esaurita)|
|`privvmpages`|*sistema*|Limite di assegnazione di memoria|
|`lockedpages`|*sistema*|Swapping delle pagine di processo non consentito (pagine bloccate da [mlock(2) ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](http://linux.die.net/man/2/mlock))|
|`shmpages`|*sistema*|Dimensione totale della memoria condivisa (comunicazioni tra processi o IPC, associazioni anonime condivise e oggetti tmpfs) nelle pagine|
|`physpages`|*sistema*|Numero totale di pagine RAM utilizzate dai processi|
|`numfile`|*sistema*|Numero di file aperti|
|`numflock`|*sistema*|Numero di blocchi di file|
|`numpty`|*sistema*|Numero di pseudo-terminali|
|`numsiginfo`|*sistema*|Numero di strutture siginfo|
|`dcachesize`|*sistema*|Dimensione totale delle strutture dentry e inode bloccate in memoria|
|`numiptent`|*sistema*|Numero di voci NETFILTER (filtro pacchetti IP)|
|`cpulimit`|*cpu*|Limite sul consumo della CPU|
|`diskinodes`|*disco*|Quota di inode di disco (oggetto filesystem)|
|`quotatime`|*disco*|Periodo di tolleranza della quota disco|
|`quotaugidlimit`|*disco*|Limite sul numero di voci di account uid e gid|
<caption>Tabella 1. Descrizioni dei parametri SLM</caption>

Puoi accedere a VzLinuxUBCMgmt.pdf o visualizzarlo in HTML, all'interno di PIM sul tuo server andando a **Management > Support > Downloads > Management of UBC Resources Administrator's Guide**

Per ulteriori informazioni su UBC, vedi [OpenVZ Virtuozzo Containers, Category: UBC ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](http://wiki.openvz.org/Category:UBC).
**Nota:** non tutto in OpenVZ è lo stesso di Virtuozzo.
