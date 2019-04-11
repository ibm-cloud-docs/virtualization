---
copyright:
  years: 2014, 2018
lastupdated: "2018-02-05"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Aggiornamento dell'applicazione Veeam Backup and Replication
{: #updating-veeam-backup-and-replication-application}

## Panoramica
{: #overview}

Utilizza la seguente procedura per aggiornare l'applicazione Veeam Backup and Replication che è in esecuzione sul tuo server quando sono disponibili dei nuovi aggiornamenti al prodotto.

### Prerequisiti
{: #prerequisites-updating-veeam-backup-and-replication-application}

* Un account Veeam gratuito. Puoi registrarti nella pagina [Registration di Veeam ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://www.veeam.com/signin.html).
* Internet Explorer, Chrome, Firefox o Safari.

### Controllo dell'eventuale presenza di aggiornamenti
{: #checking-for-updates}

L'applicazione Veeam Backup and Replication include un programma di controllo degli aggiornamenti. Per assicurarti di essere informato degli aggiornamenti, vai all'icona di menu nell'angolo superiore sinistro e fai clic su **General Options** > **Notifications**. Assicurati che l'opzione **Check for product and hypervisor updates periodically** sia selezionata.

### Aggiornamento di Veeam
{: #updating-veeam}

Per iniziare l'aggiornamento, attieniti alla seguente procedura:
1. Apri la vista **Backup Infrastructure**.
2. Nel riquadro di inventario, vai a **Managed servers node** > **Missing Updates**.
3. Fai clic sul link di aggiornamento.

Alcune cose da notare prima di iniziare l'aggiornamento:

* Assicurati che l'esecuzione corrente per tutti i lavori esistenti sia completa. Se qualche lavoro non è riuscito, rieseguilo.
* Assicurati che non ci sia alcun lavoro in esecuzione, comprese le sessioni di ripristino, le sessioni Instant VM Recovery e i lavori SureBackup. Ti consigliamo di non arrestare i lavori in esecuzione.
* Disabilita temporaneamente eventuali lavori di copia di backup e periodici per evitarne l'avvio durante l'upgrade.

### Passi successivi
{: #next-steps-updating-veeam-backup-and-replication-application}

Puoi ora estrarre l'aggiornamento scaricato e fare doppio clic sul programma di installazione aggiornamenti Veeam. L'aggiornamento viene installato sia sul server Veeam locale che sugli hypervisor su cui si trova l'agent Veeam. Una volta completato il processo, avvia Veeam Backup and Replication e riabilita i lavori di backup.
