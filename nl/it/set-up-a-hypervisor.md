---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Configurazione di un hypervisor
{: #setting-up-a-hypervisor}

Utilizza la seguente procedura per configurare un hypervisor.

1. Accedi al [{{site.data.keyword.slportal_full}} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://control.softlayer.com/){: new_window} utilizzando le tue credenziali univoche.
2. Dal menu **Devices**, seleziona **Device List** e trova il tuo hypervisor.
3. Stabilisci una connessione alla rete privata tramite la [VPN sicura ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://www.softlayer.com/vpn-access){: new_window} per accedere al tuo hypervisor.

**Nota:** i provider di hypervisor {{site.data.keyword.cloud}} includono XenServer, VMware e Hyper-V. Ogni provider ha delle console di gestione uniche a cui si accede in modo diverso. Per ulteriori informazioni sull'accesso e l'esecuzione di operazioni in una console di gestione, vedi i seguenti link:

   * [XenServer ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://support.citrix.com/en/products/xenserver){: new_window}
   * [VMware ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://www.vmware.com/support/vsphere-hypervisor.html){: new_window}
   * [Hyper-V ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://technet.microsoft.com/en-us/windowsserver/dd448604){: new_window}

4. Ottieni gli IP portatili per le tue macchine virtuali.
    * Le VM richiedono indirizzi IP portatili. Puoi ordinare blocchi di IP portatili pubblici e privati tramite il {{site.data.keyword.slportal}}.
    * Per ulteriori informazioni sull'assegnazione di indirizzi IP, vedi [Introduzione alle sottoreti e agli IP](/docs/infrastructure/subnets?topic=subnets-getting-started-subnets-ips#getting-started-subnets-ips).

5. Stabilisci l'instradamento per le VM sulla rete privata. Le VM hanno bisogno delle seguenti specifiche per eseguire l'instradamento ad altre VM sulla rete privata:
    * IP privati portatili
    * Instradamento statico che si correla all'intervallo di rete 10.0.0.0/8

Per ulteriori informazioni sul processo di instradamento delle VM, vedi [Configurazione di una rete di macchine virtuali](/docs/infrastructure/virtualization?topic=Virtualization-setting-up-a-virtual-machine-network). Le macchine che si trovano sulla stessa VLAN possono comunicare dopo che hai configurato la rete di macchine virtuali (VM, Virtual Machine). [Abilita lo spanning della VLAN](/docs/infrastructure/vlans?topic=vlans-vlan-spanning) se le macchine che si trovano su VLAN differenti devono comunicare.

## Accesso e archiviazione protetta delle ISO
{: #access-and-securely-store-isos}

Le VM sulla rete {{site.data.keyword.cloud_notm}} possono eseguire delle ISO preconfigurate o personalizzate. Le VM sulla rete {{site.data.keyword.cloud_notm}} possono accedere al sito dei mirror interno, che è disponibile esclusivamente agli utenti {{site.data.keyword.cloud_notm}} e fornisce tipiche configurazioni dei sistemi operativi utilizzati più comunemente. Se ti serve una versione o una configurazione speciali per uno specifico sistema operativo, vedi il sito web del fornitore del sistema operativo.

Se esegui una ISO personalizzata sulla tua VM, carica la tua ISO in un'ubicazione sicura in modo da consentirne il richiamo se si verifica un malfunzionamento del dispositivo. Molti utenti scelgono di archiviare le ISO personalizzate nel sistema locale di un dispositivo utilizzando SSH o RDP. In alternativa, viene offerto dello spazio mediante i servizi di archiviazione che presentano diverse funzioni.
