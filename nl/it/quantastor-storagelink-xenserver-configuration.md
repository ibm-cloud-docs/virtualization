---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: QuantaStor
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Installazione di QuantaStor StorageLink Xenserver
{: #installing-quantastor-storagelink-xenserver}

## Installazione dell'adattatore QuantaStor per Citrix StorageLink
{: #installing-the-quantastor-adapter-for-citrix-storagelink}

1. Assicurati che la tua installazione di XenServer disponga dell'appropriata licenza per StorageLink.

Solo i clienti che sottoscrivono la licenza "Enterprise" o "Platinum" tramite Citrix possono utilizzare la funzione StorageLink. I clienti che hanno la licenza di Citrix XenServer "Advanced" non possono accedere alla funzione StorageLink.
{:tip}

2. Arresta il servizio StorageLink immettendo il seguente comando in un prompt dell'amministratore. In alternativa, puoi arrestare il servizio StorageLink dal pannello Services in XenServer.

        C:\> net stop storagelink

3. Scarica ed esegui l'adattatore StorageLink disponibile da [OSNexus ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](http://www.osnexus.com/trynow/){: new_window}.
4. Dopo l'arresto del servizio, puoi installare l'adattatore QuantaStor StorageLink eseguendo questo comando:

        osn_quantastor_csladapter.exe

5. Aggiungi questo blocco di XML alla fine della configurazione StorageLink in:
  * `C:\Program Files\Citrix\StorageLink\Server\scsi_device_id_config.xml`
  * `OSNEXUS`
  * `QUANTASTOR1`
  * All'inizio di questo file vedi un numero di versione che è simile a _2.0.0.4._ Aumenta il numero di versione, ad esempio: 2.0.0.5. Il numero di versione indica a StorageLink che deve eseguire un upgrade dei tuoi sistemi XenServer con queste nuove regole di identificazione dispositivo.
6. Riavvia il servizio Citrix StorageLink:

        C:\> net start storagelink

## Verifica del corretto caricamento di StorageLink
{: #verifying-that-storagelink-loaded-properly}

1. Esegui l'accesso a Citrix StorageLink Manager, seleziona la struttura ad albero "Administration" sulla sinistra e cerca OS NEXUS QuantaStor nell'elenco [QuantaStor ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](http://svn.osnexus.com/mediawiki/images/thumb/c/c8/Storagelink_admin.png/640px-Storagelink_admin.png){: new_window}.
2. Aggiungi le tue credenziali del sistema di archiviazione in modo che StorageLink possa configurare e assegnare dinamicamente i volumi di archiviazione per il tuo sistema di archiviazione QuantaStor.

## Installazione delle impostazioni di configurazione a più percorsi XenServer
{: #installing-the-xenserver-multipath-configuration-settings}

XenServer utilizza il driver a più percorsi device-mapper Linux (`dmmp`) per abilitare il supporto per i percorsi multipli dell'hardware. Il driver `dmmp` utilizza un file di configurazione denominato `multipath.conf` che contiene la modalità a percorsi multipli e le regole di failover per ciascun fornitore.

QuantaStor ha delle regole speciali che devono essere aggiunte al file `multipath.conf`. Modifica il file `/etc/multipath-enabled.conf` che si trova su ciascuno dei nodi `dom0`. In tale file, aggiungi la seguente stanza dopo l'ultima sezione device { }:

    device {
          vendor                  "OSNEXUS"
          product                 "QUANTASTOR"
          getuid_callout          "/sbin/scsi_id -g -u -s /block/%n"
          path_selector           "round-robin 0"
          path_grouping_policy    multibus
          failback                immediate
          rr_weight               uniform
          rr_min_io               100
          path_checker            tur
    }
