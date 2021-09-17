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
{:faq: data-hd-content-type='faq'}

# FAQS: General
{: #faqs-general}

## Why isn't my server listed in the Virtual Servers list?
{: #why-isn-t-my-server-listed-in-the-virtual-servers-list-in-the-customer-portal}
{: faq}

If you have **XenServer**, **Hyper-V** or **Virtuozzo** on your server, but it is not listed in the [{{site.data.keyword.virtualmachinesshort}} ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://cloud.ibm.com/classic/devices){: new_window} list, then you need to open a [support case ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://cloud.ibm.com/unifiedsupport/cases/add){: new_window}.

This request is filled during normal United States CST business hours.
{: tip}

## If clients are a service provider, can they use their own VSPP licensing for hosts that they rent in IBM Cloud?
{: #if-clients-are-a-service-provider-can-they-use-their-own-vspp-licensing-for-hosts-that-they-rent-in-ibm-cloud}
{: faq}

No. You can either use {{site.data.keyword.cloud}} VSPP or BYOL for socket-based licensing. You cannot use your own VSPP agreement.

## Can I use the secondary IP block that came with the server for virtual machines?
{: #can-i-use-the-secondary-ip-block-that-came-with-the-server-for-virtual-machines}
{: faq}

No. The secondary IP that is provided with your server is routed to work on the physical server and not on a virtual machine. You need to have Portable IP blocks to connect your virtual machine to the network.
