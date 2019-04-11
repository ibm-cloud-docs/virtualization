---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-24"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Introduzione a XenCenter
{: #getting-started-with-xencenter}

## Prima di iniziare
{: #before-you-begin-xencenter}

Prima di iniziare, controlla i seguenti prerequisiti:

- I nuovi intervalli di indirizzi IP portatili (pubblici e privati) instradati come "Secondary on VLAN". Questa soluzione di esempio per la configurazione di una nuova VM richiede che tu disponga di IP utilizzabili disponibili nella rete privata. Puoi ordinare delle sottoreti dal [{{site.data.keyword.slportal_full}} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://control.softlayer.com/network/subnets/order).
- Possibilità di stabilire una connessione alla rete privata {{site.data.keyword.cloud}} tramite la VPN. Per ulteriori informazioni sull'accesso alla VPN, vedi il documento relativo all'[abilitazione dell'accesso alla rete privata dell'infrastruttura {{site.data.keyword.cloud_notm}}](/docs/customer-portal?topic=customer-portal-getting-started#enable-private-network).
- Citrix XenCenter è installato sul tuo sistema locale. <!-- . http://downloads.service.softlayer.com/citrix/xen/-->

**Nota:** il tuo server è preconfigurato con diversi template per aiutarti a iniziare a lavorare con XenServer rapidamente.

## Creazione di una macchina virtuale con XenCenter
{: #creating-a-virtual-machine-with-xencenter}

Utilizza la seguente procedura come ausilio nella creazione di una macchina virtuale con XenCenter.

1. Assicurati di essere connesso alla rete privata {{site.data.keyword.cloud_notm}} tramite la VPN. Stabilisci una connessione tramite SSL VPN o PPTP.
2. Apri XenCenter e fai clic su **"Add your XenServer"**.
3. Immetti il tuo nome host server, il tuo nome utente e la tua password. Devi utilizzare l'indirizzo IP privato del tuo server (simile a 10.x.x.x), il nome utente `root` e quindi la password root del tuo server. Queste informazioni sono disponibili nel {{site.data.keyword.slportal}} andando ai tuoi dispositivi ([Devices ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://control.softlayer.com/devices){: new_window}) e facendo quindi clic sul nome del tuo XenServer. Fai clic su **Connect**.
4. Se ricevi una schermata di attivazione di una licenza gratuita (Free License), completa queste informazioni ora.
5. Fai clic con il pulsante destro del mouse sul tuo nome {{site.data.keyword.cloud_notm}} e seleziona **New VM...**.<!--You can now create your first Virtual Machine. Create a CentOS virtual machine with a disk of 10 GB and have both Public and Private Networks functioning-->
6. Seleziona il sistema operativo che desideri utilizzare per la nuova macchina virtuale e fai quindi clic su **Next**. **Nota:** alcuni template richiedono che tu fornisca il tuo supporto.<!--Because you are using CentOS, you can use {{site.data.keyword.BluSoftlayer_notm}} private mirrors for CentOS to get our installation going.Select a version of CentOS and then click **Next**.-->
7. Immetti un nome e una descrizione appropriati per la nuova macchina virtuale.
8. Immetti la posizione del supporto di installazione del sistema operativo guest e fai clic su **Next**. <!-- In the example, {{site.data.keyword.BluSoftlayer_notm}} a CentOS mirror is used as installation media. Provide the Install URL of: http://mirrors.service.softlayer.com/centos/5/os/x86_64 and click **Next**.
  *A trailing ‘/’ at the end of the URL can sometimes break the installation.*
  *This mirror is available only on the {{site.data.keyword.BluSoftlayer_notm}} Private Network. The full mirror's contents are  available here: http://mirrors.service.softlayer.com/.-->
9. Seleziona il numero di CPU e la quantità di assegnazione di memoria. (Stai creando un sistema semplificato e non hai bisogno di troppa RAM. Pertanto, 512 MB sono più che sufficienti). . Fai clic su **Next**.
10. Seleziona i tuoi dischi virtuali per assegnare spazio su disco alla tua VM.<!--Remember that this is like adding hard disks, it is not like partitioning your system. Partitioning is done during the installation of the OS.--> La dimensione predefinita è 8 GB per questo template. Questa quantità è più che sufficiente per il server che stai creando. *Facoltativo: puoi aumentare le dimensioni del disco per soddisfare le tue esigenze evidenziando il disco e facendo clic su **Edit...**.* Fai clic su **Next**.
11. Aggiungi o rimuovi interfacce di rete virtuale per la macchina virtuale. I valori predefiniti sono accettabili, tranne nel caso in cui tu non desideri che il sistema possa comunicare sulla rete privata. L'esempio richiede che tu lasci entrambe le interfacce nel sistema ma un amministratore Xen esperto può rimuoverne una per i propri scopi. In tal caso, puoi evidenziare ed eliminare un'interfaccia, se lo desideri. Fai clic su **Next**.
12. La configurazione è stata completata. Lascia l'opzione "Start VM automatically" selezionata e fai clic su **Finish**. L'installazione viene avviata. Ritorni alla schermata principale. Poi vedere la nuova VM elencata a sinistra.
13. Seleziona la tua nuova VM e fai clic sulla scheda **Console**. <!--You can now see that your system is booted into the CentOS installer awaiting your input.-->
14. <!--All of the parameters of a CentOS installation are outside of the scope of this article and will need to be customized by your System Administrator, but this article will provide some specific pieces of information that you need to complete the installation. Select your language to get started. The CentOS installer will then ask you for assistance in configuring the Networking Devices in the system.--> Seleziona **eth0 - xen Virtual Ethernet** e fai clic su **OK**.
  <!--![14](images/14.png)-->
15. <!--In the pre-requisite notes, we made sure that we already had a set of Portable IP Addresses routed as "Secondary on VLAN" ready for this installation.--> Assicurati di avere pronte le informazioni per configurare TCP/IP. Devi configurare manualmente l'interfaccia con il supporto IPV4 e disabilitare il supporto IPV6.
  <!--[15](images/15.png)-->
16. Conferma di essere a conoscenza di come utilizzare la tua sottorete di IP privati (10.17.37.240/29) e fai clic su **OK**<!-- to go to the CentOS installer-->. Tu e il tuo amministratore di sistema potete installare il sistema operativo guest in base alle linee guida della vostra azienda. Per ulteriori informazioni sui blocchi di IP portatili e statici, vedi [Introduzione alle sottoreti e agli IP](/docs/infrastructure/subnets?topic=subnets-getting-started-with-subnets-and-ips).

Hai creato una nuova VM.
