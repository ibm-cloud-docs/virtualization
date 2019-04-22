---
copyright:
  years: 2014, 2018
lastupdated: "2018-08-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# FAQ: Hyper-V
{: #faqs-hyper-v}

## Che cos'è Hyper-V?
{: #what-is-hyper-v-}

Hyper-V è un sistema di virtualizzazione per Windows 2008 Server Datacenter Edition che consente a un singolo server fisico di ospitare più macchine virtuali che eseguono tutte un loro sistema operativo.

## Quali sono i requisiti per eseguire Hyper-V?
{: #what-are-the-requirements-to-run-hyper-v-}

Hyper-V deve essere installato su Windows 2008 Server Datacenter Edition a 64 bit. Tutto l'hardware {{site.data.keyword.cloud}} soddisfa i requisiti di sistema di Hyper-V. Microsoft consiglia almeno 2 GB di RAM per il server.

## Hyper-V è disponibile su qualche altra versione di 2008?
{: #is-hyper-v-available-on-any-other-versions-of-2008-}

{{site.data.keyword.cloud_notm}} offre Hyper-V solo sulla Datacenter Edition.

## Hyper-V può essere eseguito su Windows 2003?
{: #can-hyper-v-run-on-windows-2003}

Hyper-V è stato sviluppato per Windows 2008 e non può essere eseguito su Windows 2003.

## Quali sistemi operativi possono essere installati su una macchina virtuale?
{: #what-operating-systems-can-be-installed-on-a-virtual-machine}

{{site.data.keyword.cloud_notm}} supporta i seguenti sistemi operativi su Hyper-V:

* Tutte le versioni di Windows 2003 e 2008 Server
* Distribuzioni CentOS, Fedora e Ubuntu Linux

## Quante macchine virtuali può eseguire un server?
{: #how-many-virtual-machines-can-a-server-run-}

Il numero di macchine virtuali che un server può eseguire varia a seconda dei processori server, della RAM e dello spazio su disco rigido.

## La RAM è personalizzabile su ciascuna macchina virtuale?
{: #is-the-ram-customizable-on-each-virtual-machine-}

Sì. Puoi utilizzare Hyper-V per personalizzare le risorse di sistema per ogni macchina virtuale, compresa la memoria.

## Di quanta RAM ha bisogno una macchina virtuale?
{: #how-much-ram-does-a-virtual-machine-need-}

Le esigenze di RAM variano in base ai requisiti per la macchina virtuale. Controlla i requisiti di sistema per il tuo sistema operativo guest. La quantità di memoria fornita a una macchina virtuale può essere modificata in qualsiasi momento.

## Una macchina virtuale può accedere a più di un processore?
{: #can-a-virtual-machine-access-to-more-than-one-processor-}

Puoi concedere a una macchina virtuale l'accesso a più processori con qualsiasi macchina virtuale Windows. Tuttavia, le macchine virtuali Linux sono limitate a un singolo processore.

## Possono essere modificate le dimensioni del disco rigido dopo che una macchina virtuale è stata creata?
{: #can-hard-disk-sizes-change-after-a-virtual-machine-is-created-}

Sì.

## Ogni sistema operativo virtuale ha bisogno di una licenza?
{: #does-each-virtual-operating-system-need-to-have-a-license-}

Le macchine virtuali Windows 2003 e 2008 sono concesse in licenza tramite {{site.data.keyword.cloud_notm}}. Le macchine virtuali Linux sono concesse in licenza gratuitamente e non richiedono alcuna azione.

## Le macchine virtuali avranno accesso alla rete privata?
{: #will-the-virtual-machines-have-access-to-the-private-network-}

Sì. Le macchine virtuali possono connettersi sia alla rete pubblica sia a quella privata.

## Quali vantaggi offre il fornire l'accesso alla rete privata alle macchine virtuali?
{: #what-advantages-are-there-to-providing-private-network-access-to-virtual-machines-}

Fornire alle macchine virtuali l'accesso alla rete privata consente alle macchine virtuali stesse di comunicare tra loro. L'accesso alla rete privata consente anche alle macchine virtuali di comunicare con altri sistemi interni quali NAS e iSCSI e qualsiasi altro server che hai con {{site.data.keyword.cloud_notm}}.

## Le macchine virtuali possono utilizzare il blocco di IP secondari fornito con il server?
{: #can-virtual-machines-use-the-secondary-ip-block-that-came-with-the-server-}

No. L'IP secondario fornito con il tuo server viene instradato specificamente per funzionare sul server fisico e non su una macchina virtuale. Hai bisogno di blocchi di IP portatili per connettere la tua macchina virtuale alla rete.

## Cosa sono gli indirizzi IP portatili?
{: #what-are-portable-ip-addresses-}

Per informazioni sugli indirizzi IP portatili, vedi [Introduzione alle sottoreti e agli IP](/docs/infrastructure/subnets?topic=subnets-getting-started-subnets-ips#getting-started-subnets-ips).

## Come ordino Hyper-V?
{: #how-do-i-order-hyper-v-}

Puoi installare Hyper-V su un server esistente immettendo un ricaricamento del sistema operativo e modificando il sistema operativo in “Windows 2008 Server Datacenter Edition with Hyper-V." Per eseguire il provisioning di un nuovo server con Hyper-V, ordina un nuovo server e seleziona “Windows 2008 Server Datacenter Edition with Hyper-V” come sistema operativo.

## Hyper-V è preinstallato con Windows 2008 Server Datacenter Edition?
{: #is-hyper-v-preinstalled-with-windows-2008-server-datacenter-edition-}

Hyper-V non è preinstallato sui server Windows 2008. Se desideri installare Hyper-V sul tuo server Windows 2008, devi immettere un ricaricamento del sistema operativo e selezionare “Windows 2008 Server Datacenter Edition with Hyper-V” come sistema operativo.

## Il ricaricamento del sistema operativo per Hyper-V è stato eseguito. Come procedo ora?
{: #the-os-reload-for-hyper-v-is-done-what-s-next-}

Hyper-V deve essere configurato. Per ulteriori informazioni sulla configurazione di Hyper-V, vedi [Configurazione di Hyper-V](/docs/infrastructure/virtualization?topic=Virtualization-setting-up-hyper-v).

## Una macchina virtuale non viene avviata. “Cannot initialize machine remoting system: Error:'Unspecified error'” Come risolvo questo problema?
{: #a-virtual-machine-does-not-start-cannot-initialize-machine-remoting-system-error-unspecified-error-how-do-i-resolve-this-issue-}

Per risolvere questo problema, arresta il servizio Hyper-V e riavvialo. Questo problema viene di solito causato da un tentativo di avvio di Hyper-V mentre è in corso l'attivazione del server. Il riavvio del servizio riattiva la macchina virtuale dopo che l'attivazione è stata completata.
