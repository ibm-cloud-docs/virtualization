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

# Conexión a XenServer a través de una interfaz pública
{: #connecting-to-xenserver-over-a-public-interface}

De forma predeterminada, la instalación de {{site.data.keyword.BluSoftlayer}} de XenServer está configurada para utilizar la interfaz de red privada para todas las conexiones de gestión. Si tiene problemas de conexión en la red privada, abra una incidencia de soporte.

El cambio de esta configuración supone un riesgo de seguridad y no está recomendado.
{:tip}

Si desea reconfigurar XenServer para utilizar la interfaz pública, ejecute los mandatos siguientes como root desde el host (el host no puede ser estar agrupado).

1. `xe pif-list`

2. Anote el uuid de la interfaz que desea habilitar (lo más probable es que sea eth1 o bond1).

3. Ejecute el mandato siguiente:

        `xe host-management-reconfigure pif-uuid=`

Estos cambios entran en vigor inmediatamente y no requieren un reinicio.
