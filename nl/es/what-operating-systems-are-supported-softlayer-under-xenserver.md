---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-24"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:table: .aria-labeledby="caption"}

# Sistemas operativos admitidos por IBM Cloud con XenServer
{: #operating-systems-supported-by-ibm-cloud-under-xenserver}

Los sistemas operativos siguientes se pueden enlazar a {{site.data.keyword.slapi_full}} mientras se ejecuta XenServer:

- CentOS 6.x, 7.x
- CloudLinux 6.x
- CoreOS Stable
- RedHat Enterprise Linux 6.x, 7.x
- Debian 7.x a 9.x
- Ubuntu 14.04 LTS a 16.04 LTS
- Microsoft Windows Server 2012, 2012 R2, 2016

XenServer admite software más allá de la lista anterior, que es posible que no se enlace con
{{site.data.keyword.slapi_short}}. Cualquier sistema operativo de invitado que se instale mediante las opciones
*“Otros soportes de instalación”* en XenCenter, no será compatible con
{{site.data.keyword.BluSoftlayer_notm}} y podría no funcionar en el entorno XenServer.
