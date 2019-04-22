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

# Verbindung zu XenServer über öffentliche Schnittstelle herstellen
{: #connecting-to-xenserver-over-a-public-interface}

Standardmäßig wird die {{site.data.keyword.cloud}}-Installation von XenServer so konfiguriert, dass die Schnittstelle für das private Netz für alle Managementverbindungen verwendet wird. Wenn im privaten Netz Verbindungsprobleme auftreten, dann öffnen Sie ein Support-Ticket.

Die Änderungen dieser Konfiguration stellt ein Sicherheitsrisiko dar und ist nicht zu empfehlen.
{:tip}

Wenn Sie XenServer anders konfigurieren wollen, um die öffentliche Schnittstelle zu verwenden, dann führen Sie die folgenden Befehle als Root über den Host (keine Pool-Hosts!) aus.

1. `xe pif-list`

2. Notieren Sie die UUID (Universally Unique Identifier) der Schnittstelle, die Sie aktivieren möchten (meist 'eth1' oder 'bond1').

3. Führen Sie den folgenden Befehl aus:

        `xe host-management-reconfigure pif-uuid=`

Diese Änderungen werden sofort und ohne einen Neustart wirksam.
