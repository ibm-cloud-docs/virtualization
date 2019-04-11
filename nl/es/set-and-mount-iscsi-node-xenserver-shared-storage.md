---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-16"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Configuración y montaje de un nodo iSCSI en XenServer Shared Storage
{: #setting-up-and-mounting-an-iscsi-node-in-xenserver-shared-storage}

Shared Storage es un repositorio de almacenamiento (SR) que es cualquier forma de almacenamiento que puede utilizar XenServer. Puede elegir entre dos categorías de SR: compartido y no compartido. Un SR compartido permite que varios nodos puedan compartir una ubicación de almacenamiento de la misma agrupación. {{site.data.keyword.cloud}} admite dos opciones de SR: NFS y LVMoiSCSI (LVM sobre iSCSI).

Debe instalar un servidor NFS y gestionarlo desde otro sistema de cliente o utilizando un servidor
{{site.data.keyword.cloud_notm}} QuantaStor. Siga estos pasos para montar el repositorio NFS en XenCenter:

1. Abra la consola de XenCenter.
2. Pulse **Nuevo almacenamiento**.
* En el nuevo recuadro de diálogo, seleccione **Almacenamiento de disco virtual > NFS**.
* Proporcione un nombre adecuado al nuevo SR.
* Especifique server:/path en **Nombre de compartición**
* Pulse **Explorar**

Este proceso explora la compartición NFS para ver si hay SR anteriores.

Puede utilizar {{site.data.keyword.blockstoragefull}} para LVMoiSCSI. El iSCSI puede ser de un servidor de almacenamiento gestionado por el cliente o una oferta de {{site.data.keyword.blockstoragefull}}. Por rendimiento y para el almacenamiento de bloque redundante, recupere el IQN del {{site.data.keyword.slportal}} accediendo a **Almacenamiento > Almacenamiento de bloque > Seleccionar nombre de LUN**. Para montar el nodo iSCSI a través de XenCenter, siga estos pasos:

1. Abra la consola de XenCenter.
2. Vaya a **General > Propiedades** y establezca el IQN.
3. Pulse **Nuevo almacenamiento**
4. En el nuevo recuadro de diálogo, vaya a **Almacenamiento de disco virtual > iSCSI de software**.
5. Especifique un nombre adecuado para el nuevo SR.
6. Especifique el nombre de host o la IP del destino iSCSI y deje el puerto con el valor predeterminado de 3260.
7. Seleccione **Utilizar CHAP**.
8. Especifique el nombre de usuario y la contraseña del LUN.
9. Pulse **Explorar host de destino**.
* Seleccione una opción de **IQN de destino**.
* Seleccione una opción de **LUN de destino**.
10. Después de rellenar los campos de IQN y LUN, pulse **Finalizar**. Se explora el destino para comprobar si existen SR anteriores. Si ya existe un SR, el instalador preguntará si desea crear un nuevo SR o conectarlo al SR anterior.

Si los servidores están juntos en una agrupación, el repositorio iSCSI se comparte automáticamente.

Para obtener información sobre la configuración de iSCSI para varias vías, consulte
[Configuración de varias vías de iSCSI ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus1000/sw/5_x/sys_mgmt_config/b_Cisco_N1KV_VMware_Sys_Mgmt_Config_5x/b_Cisco_N1KV_VMware_Sys_Mgmt_Config_5x_chapter_01110.html?dtid=osscdc000283){: new_window}
