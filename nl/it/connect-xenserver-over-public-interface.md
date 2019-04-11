---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: Xenserver
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Connessione a XenServer su un'interfaccia pubblica
{: #connecting-to-xenserver-over-a-public-interface}

Per impostazione predefinita, l'installazione {{site.data.keyword.BluSoftlayer}} di XenServer è configurata per utilizzare l'interfaccia di rete privata per tutte le connessioni di gestione. Se hai problemi di connessione sulla rete privata, apri un ticket di supporto.

L'apporto di modifiche a questa configurazione è un rischio per la sicurezza e non è consigliato.
{:tip}

Se desideri riconfigurare XenServer per utilizzare l'interfaccia pubblica, esegui i seguenti comandi come root dall'host (l'host non può essere in pool).

1. `xe pif-list`

2. Annota l'uuid dell'interfaccia che desideri abilitare (molto probabilmente eth1 o bond1).

3. Esegui il seguente comando:

        `xe host-management-reconfigure pif-uuid=`

Queste modifiche diventano immediatamente effettive e non richiedono un riavvio.
