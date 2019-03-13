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

# FAQS: General
{: #faqs-general}

## Why isn't my server listed in the Virtual Servers list in the Customer Portal?
{: #why-isn-t-my-server-listed-in-the-virtual-servers-list-in-the-customer-portal-}

If you have **XenServer**, **Hyper-V** or **Virtuozzo** on your server, but it is not listed in the [{{site.data.keyword.virtualmachinesshort}} ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://manage.softlayer.com/Virtual/live){: new_window} list, within the [Hardware ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://manage.softlayer.com/Hardware/configuration){: new_window} section of the [Portal ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://manage.softlayer.com/){: new_window}, then you need to open a [standard support ticket ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://manage.softlayer.com/Support/addTicket){: new_window} with the Support Department so it can be added to your list.

This request is filled during normal United States CST business hours.
{:tip}

## If clients are a service provider, can they use their own VSPP licensing for hosts that they rent in IBM Cloud?
{: #if-clients-are-a-service-provider-can-they-use-their-own-vspp-licensing-for-hosts-that-they-rent-in-ibm-cloud-}

No. You can either use {{site.data.keyword.cloud}} VSPP or BYOL for socket-based licensing. You cannot use your own VSPP agreement.

## Can I use the secondary IP block that came with the server for virtual machines?
{: #can-i-use-the-secondary-ip-block-that-came-with-the-server-for-virtual-machines-}

No. The secondary IP that is provided with your server is routed to work on the physical server and not on a virtual machine. You need to have Portable IP blocks to connect your virtual machine to the network.
