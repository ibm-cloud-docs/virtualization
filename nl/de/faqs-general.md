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

# Häufig gestellte Fragen: Allgemein
{: #faqs-general}

## Warum wird mein Server nicht in der Virtual Server-Liste im Kundenportal aufgelistet?
{: #why-isn-t-my-server-listed-in-the-virtual-servers-list-in-the-customer-portal-}

Wenn auf Ihrem Server **XenServer**, **Hyper-V** oder **Virtuozzo** installiert ist, er jedoch nicht in der Liste für [{{site.data.keyword.virtualmachinesshort}} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://manage.softlayer.com/Virtual/live){: new_window} im Abschnitt [Hardware ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://manage.softlayer.com/Hardware/configuration){: new_window} des [Portals ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://manage.softlayer.com/){: new_window} aufgelistet wird, müssen Sie ein [Standard-Support-Ticket ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://manage.softlayer.com/Support/addTicket){: new_window} bei der Supportabteilung öffnen, damit er zu Ihrer Liste hinzugefügt werden kann.

Diese Anforderung wird während der normalen CST-Geschäftszeiten der Vereinigten Staaten erfüllt.
{:tip}

## Können Clients, bei denen es sich um Service-Provider handelt, eigene VSPP-Lizenzen für Hosts verwenden, die sie bei IBM Cloud mieten?
{: #if-clients-are-a-service-provider-can-they-use-their-own-vspp-licensing-for-hosts-that-they-rent-in-ibm-cloud-}

Nein. Sie können entweder {{site.data.keyword.cloud}} VSPP oder BYOL für die socketbasierte Lizenzierung verwenden. Eigene VSPP-Vereinbarungen können nicht verwendet werden.

## Kann ich den sekundären IP-Block verwenden, der zum Lieferumfang des Servers für virtuelle Maschinen gehört?
{: #can-i-use-the-secondary-ip-block-that-came-with-the-server-for-virtual-machines-}

Nein. Die sekundäre IP, die mit Ihrem Server bereitgestellt wird, ist für das Arbeiten auf dem physischen Server und nicht auf einer virtuellen Maschine vorgesehen. Sie benötigen portierbare IP-Blöcke, um Ihre virtuelle Maschine mit dem Netz zu verbinden.
