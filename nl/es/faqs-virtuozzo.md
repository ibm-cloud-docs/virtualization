---



copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Preguntas más frecuentes: Virtuozzo
{: #faqs-virtuozzo}

## ¿Necesito direcciones IP estáticas o portátiles para Virtuozzo?
{: #do-i-need-static-or-portable-ip-addresses-for-virtuozzo-}

* Dirección IP estática: utilice una dirección IP estática si solo tiene un nodo de hardware (por cada VLAN o en total)
* Dirección IP portátil:
    * Direccionada a VLAN: utilice este método si tiene varios nodos de host dentro de la misma VLAN. También puede utilizar este método para migrar un contenedor a un nodo de hardware distinto dentro de la misma VLAN sin cambiar la IP del contenedor.
    * Secundaria en VLAN: este método funciona, pero no necesita una pasarela secundaria. Se pierden 3 de las direcciones IP relativas a ID de host, pasarela y difusión.

El coste por IP puede cambiar en función del tipo de direccionamiento. Los precios se muestran en el portal después de seleccionar el servidor para el que va a solicitar las direcciones IP. El direccionamiento se puede cambiar en cualquier momento abriendo una incidencia de soporte.
{:tip}

## ¿Puedo migrar un contenedor de Virtuozzo 3 a un nodo de hardware de contenedor de Virtuozzo 4 sin tiempo de inactividad?
{: #can-i-migrate-a-virtuozzo-3-container-to-a-virtuozzo-container-4-hardware-node-with-no-downtime-}

**Linux:**

Sí. De forma predeterminada, los contenedores de Virtuozzo para Linux 4 se ejecutan en modalidad de compatibilidad. Si los contenedores de Virtuozzo no se ejecutan en modalidad de compatibilidad, puede iniciarla utilizando el mandato siguiente en el nodo de hardware a través de SSH.

`/usr/sbin/vzagent_compat_ctl start`

**Windows (sin soporte):**

Con los contenedores de Virtuozzo 4 en Windows, existe un tiempo de inactividad durante la transición (entre 20 segundos y 5 minutos) debido a que no hay una opción en línea que utilizar.

Si utiliza Virtuozzo 3.0 para Windows, necesita actualizar el nodo de hardware a Virtuozzo 3.5.1 para Windows como mínimo.

Los contenedores de Virtuozzo 4 para Windows, de forma predeterminada, se ejecutan en modalidad de compatibilidad.

La restauración de copias de seguridad de la versión 3.5.1 en un nodo de hardware de contenedores de Virtuozzo 4 también se puede llevar a cabo copiando la copia de seguridad completa en el nodo de destino, ejecutando `vzbackupsync.exe` y restaurando la copia de seguridad de la manera habitual.

Asegúrese de que se instalen las mismas plantillas en el nuevo nodo de hardware.
{:tip}

## ¿Están los contenedores aislados entre sí y del host?
{: #are-containers-isolated-from-each-other-and-the-host-}

Los contenedores tienen restringida la carga de LKM, realizando un `chroot(“../../../”)` fuera de su propio entorno, y no comparten recursos IPC (conjuntos de semáforos y segmentos de memoria compartida).

No hay problemas de seguridad en relación a la manera en que Virtuozzo gestiona recursos y aísla los procesos. Con una configurar de red de puente, todos los contenedores de la misma VLAN (en el mismo nodo de hardware) pueden ver el tráfico de los demás. Se recomienda encarecidamente el uso de la modalidad direccionada.

## ¿Por qué no puedo almacenar en memoria caché las plantillas de SO de RedHat y SUSE en Virtuozzo?
{: #why-can-t-i-cache-redhat-and-suse-os-templates-in-virtuozzo-}

Puede experimentar problemas al almacenar en memoria caché las plantillas de SO de RedHat y SUSE si utiliza Virtuozzo. Estas plantillas de SO requieren licencias adicionales, que {{site.data.keyword.cloud_notm}} no proporciona. Si obtiene alguno de los errores siguientes, deberá disponer de sus propias cuentas con los proveedores respectivos y configurar las variables correspondientes dentro de /etc/vztt/url.map (/etc/vztt/vztt.conf en Virtuozzo 3).

||||
|---|---|---|
|TIMESTAMP|Pulse aquí para abrir/cerrar la operación details.Update OS Template Cache|**Error**|
|TIMESTAMP|Se ha iniciado la actualización de operación con el entorno (o entornos) "virtuozzo00.softlayer.local" y el paquete (o paquetes) .redhat-as4-x86_64||
|TIMESTAMP|La actualización de operación con el entorno (o entornos) "virtuozzo00.softlayer.local" ha finalizado con errores: No se pueden actualizar los paquetes: exec ha fallado: Error: variable indefinida del contenido del URL $RH_SERVER/download/mirrors/redhat-as4 Puede definir esta variable en /etc/vztt/url.map. .|**Error**|
|TIMESTAMP|Pulse aquí para abrir/cerrar la operación details.Update OS Template Cache|**Error**|
|TIMESTAMP|Se ha iniciado la actualización de operación con el entorno (o entornos) "virtuozzo00.softlayer.local" y el paquete (o paquetes) .redhat-el5-x86_64||
|TIMESTAMP|La actualización de operación con el entorno (o entornos) "virtuozzo00.softlayer.local" ha finalizado con errores: No se pueden actualizar los paquetes: exec ha fallado: Error: variable indefinida del contenido del URL $RH_SERVER/download/mirrors/redhat-el5 Puede definir esta variable en /etc/vztt/url.map. Consulte la Guía de instalación de Virtuozzo para obtener más detalles. .|**Error**|
|TIMESTAMP|Pulse aquí para abrir/cerrar la operación details.Update OS Template Cache|**Error**|
|TIMESTAMP|Se ha iniciado la actualización de operación con el entorno (o entornos) "virtuozzo00.softlayer.local" y el paquete (o paquetes) .sles-10-x86_64||
|TIMESTAMP|La actualización de operación con el entorno (o entornos) "virtuozzo00.softlayer.local" ha finalizado con errores: No se pueden actualizar los paquetes: exec ha fallado: Error: variable indefinida del contenido del URL $SLES_SERVER/download/mirrors/suse-es10 Puede definir esta variable en /etc/vztt/url.map. Consulte la Guía de instalación de Virtuozzo para obtener más detalles. .|**Error**|
|TIMESTAMP|Pulse aquí para abrir/cerrar la operación details.Update OS Template Cache|**Error**|
|TIMESTAMP|Se ha iniciado la actualización de operación con el entorno (o entornos) "virtuozzo01.softlayer.local" y el paquete (o paquetes) .redhat-as4-x86_64||
|TIMESTAMP|La actualización de operación con el entorno (o entornos) "virtuozzo01.softlayer.local" ha finalizado con errores: No se pueden actualizar los paquetes: exec ha fallado: Error: variable indefinida del contenido del URL $RH_SERVER/download/mirrors/redhat-as4 Puede definir esta variable en /etc/vztt/url.map. Consulte la Guía de instalación de Virtuozzo para obtener más detalles. .|**Error**|
|TIMESTAMP|Pulse aquí para abrir/cerrar la operación details.Update OS Template Cache|**Error**|
|TIMESTAMP|Se ha iniciado la actualización de operación con el entorno (o entornos) "virtuozzo01.softlayer.local" y el paquete (o paquetes) .redhat-el5-x86_64||
|TIMESTAMP|La actualización de operación con el entorno (o entornos) "virtuozzo01.softlayer.local" ha finalizado con errores: No se pueden actualizar los paquetes: exec ha fallado: Error: variable indefinida del contenido del URL $RH_SERVER/download/mirrors/redhat-el5 Puede definir esta variable en /etc/vztt/url.map. .|**Error**|
|TIMESTAMP|Pulse aquí para abrir/cerrar la operación details.Update OS Template Cache          Error<br/><br/>29 de octubre de 2008 01:16:56 PM     Se ha iniciado la actualización de operación con el entorno (o entornos) "virtuozzo01.softlayer.local" y el paquete (o paquetes) .sles-10-x86_64||
|TIMESTAMP|La actualización de operación con el entorno (o entornos) "virtuozzo01.softlayer.local" ha finalizado con errores: No se pueden actualizar los paquetes: exec ha fallado: Error: variable indefinida del contenido del URL $SLES_SERVER/download/mirrors/suse-es10.|**Error**|
|TIMESTAMP|Completando el proceso|**Error**|

## ¿Por qué puedo hacer ping a ServiceVE pero no puedo iniciar sesión a través de SSH (PIM/PMC)?
{: #why-can-i-ping-my-serviceve-but-cannot-log-in-to-via-ssh-pim-pmc-}

Asegúrese de que el nodo de host no tenga ninguna dirección IP asignada que no sean las direcciones IP que aparecen en el portal (una IP pública y una IP privada, ubicada en 10.0.0.0/8).

Si tiene algún archivo de rango configurado en el nodo de host, deberá eliminarlo y reiniciar la red.

    [root@virtuozzo ~]# mv –vi /etc/sysconfig/network-scripts/eth[0-9]-range[0-9]* ~/.sl-orig-configs/

    [root@virtuozzo ~]# /sbin/service network restart


`vzcp` no se inicia dentro de Service VE. Inicie `vzcp` dentro de Service VE desde el nodo de hardware.

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp status

    vzcp is stopped

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp start

    Starting vzcpd: [  OK  ]

    Starting vzcp: [  OK  ]

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp status

    vzcp (pid 3775 3774 3771) is running...

    [root@virtuozzo ~]#
