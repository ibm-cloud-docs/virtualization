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

# Preguntas más frecuentes: General
{: #faqs-general}

## ¿Por qué no aparece mi servidor en la lista de servidores virtuales en el Portal de clientes?
{: #why-isn-t-my-server-listed-in-the-virtual-servers-list-in-the-customer-portal-}

Si tiene **XenServer**, **Hyper-V** o **Virtuozzo** en el servidor, pero no aparece en la lista [{{site.data.keyword.virtualmachinesshort}} ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://manage.softlayer.com/Virtual/live){: new_window}, dentro de la sección [Hardware ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://manage.softlayer.com/Hardware/configuration){: new_window} del
[Portal ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://manage.softlayer.com/){: new_window}, necesitará abrir una [incidencia de soporte estándar ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://manage.softlayer.com/Support/addTicket){: new_window} con el departamento de soporte para que se pueda añadir a la lista.

Esta solicitud se rellena durante horas laborables normales CST de EE.UU.
{:tip}

## Si los clientes son un proveedor de servicios, ¿pueden utilizar su propia licencia VSPP para los hosts que alquilan en IBM Cloud?
{: #if-clients-are-a-service-provider-can-they-use-their-own-vspp-licensing-for-hosts-that-they-rent-in-ibm-cloud-}

No. Puede utilizar BYOL o VSPP de {{site.data.keyword.cloud}} para una licencia basada en socket. No puede utilizar su propio acuerdo VSPP.

## ¿Puedo utilizar el bloque de IP secundaria que se proporciona con el servidor para las máquinas virtuales?
{: #can-i-use-the-secondary-ip-block-that-came-with-the-server-for-virtual-machines-}

No. La IP secundaria que se proporciona con el servidor está dirigida a trabajar en el servidor físico y no en una máquina virtual. Necesitará tener bloques de IP portátiles para conectar su máquina virtual a la red.
