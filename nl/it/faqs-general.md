---



copyright:
  years: 2014, 2018
lastupdated: "2018-01-04"

subcollection: virtualization


---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# FAQ: generale
{: #faqs-general}

## Perché il mio server non è elencato nell'elenco Virtual Server del Portale del Cliente?
{: #why-isn-t-my-server-listed-in-the-virtual-servers-list-in-the-customer-portal-}

Se hai **XenServer**, **Hyper-V** o **Virtuozzo** sul tuo server, ma non è elencato nell'elenco [{{site.data.keyword.virtualmachinesshort}} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://manage.softlayer.com/Virtual/live){: new_window}, nella sezione [Hardware ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://manage.softlayer.com/Hardware/configuration){: new_window} del [Portale ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://manage.softlayer.com/){: new_window}, devi aprire un [ticket di supporto standard ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://manage.softlayer.com/Support/addTicket){: new_window} presso il reparto di supporto in modo che possa essere aggiunto al tuo elenco.

Questa richiesta viene evasa durante le normali ore lavorative dell'Ora solare fuso centrale (CST, Central Standard Time) degli Stati Uniti.
{:tip}

## Se i clienti sono dei provider di servizi, possono utilizzare la loro concessione di licenze VSPP per gli host che affittano in IBM Cloud?
{: #if-clients-are-a-service-provider-can-they-use-their-own-vspp-licensing-for-hosts-that-they-rent-in-ibm-cloud-}

No. Puoi utilizzare {{site.data.keyword.cloud}} VSPP o BYOL per la concessione di licenze basata sul socket. Non puoi utilizzare il tuo accordo VSPP.

## Posso utilizzare il blocco di IP secondari fornito con il server per le macchine virtuali?
{: #can-i-use-the-secondary-ip-block-that-came-with-the-server-for-virtual-machines-}

No, l'IP secondario fornito con il tuo server è instradato per funzionare sul server fisico e non su una macchina virtuale. Devi disporre di blocchi di IP portatili per connettere la tua macchina virtuale alla rete.
