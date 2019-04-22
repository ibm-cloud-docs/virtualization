---
copyright:
  years: 2014, 2018
lastupdated: "2018-06-22"

subcollection: virtualization

keywords: disaster recovery
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:table: .aria-labeledby="caption"}

# Recuperación del entorno de VMware vSphere
{: #recovering-your-vmware-vsphere-environment}

La copia de seguridad de datos es actualmente el método principal para mantener la seguridad y la integridad de los datos. En un mundo perfecto e ideal, no haría falta utilizarla.  Desafortunadamente, la realidad es que ocurren accidentes y desastres.  En el caso de que sea así, resulta esencial minimizar el tiempo de inactividad del sistema y conseguir que vuelva a estar activo y en ejecución lo antes posible.

En el pasado, almacenar copias de seguridad en cintas o almacenamientos de alto rendimiento era una solución factible. Hoy en día, las empresas buscan disminuir los costes operativos y de capital asociados al almacenamiento físico local aumentándolo o bien sustituyéndolo por el almacenamiento de objetos. Sin embargo, como las copias de seguridad ya no se ubican en almacenamientos locales con el almacenamiento de objetos, realizar una restauración ya no es cuestión solo de ubicar la copia de seguridad necesaria y realizar una restauración. Por suerte, hay una solución híbrida que lleva a cabo la mayor parte del trabajo preliminar relativo a la restauración de las copias de seguridad guardadas en el almacenamiento de objetos. La solución consta de

* {{site.data.keyword.cos_full}}
* Pasarela de almacenamiento en la nube AltaVault de NetApp (anteriormente conocida como Riverbed SteelStore)
* Software Veeam Backup & Replication

Si se produce una catástrofe y el entorno principal pasa a estar fuera de línea, se puede crear un sitio de recuperación secundario dentro de {{site.data.keyword.cloud}} para recuperarlo. Puede desplegar el software Veeam Backup & Replication y un dispositivo de almacenamiento integrado en la nube de AltaVault para comunicarse con {{site.data.keyword.cos_full_notm}} para acceder a copias de seguridad anteriores del entorno principal. El software se conecta con el dispositivo de almacenamiento para restaurar las copias de seguridad en una nueva ubicación, volviendo a poner en línea el entorno principal y evitando cualquier pérdida adicional de tiempo de actividad.

Las direcciones IP locales se conservan al restaurar las máquinas virtuales (VM) en el sitio de recuperación de {{site.data.keyword.cloud_notm}}. Como resultado, es importante cambiar las direcciones IP tras una recuperación exitosa o diseñar una infraestructura BYOIP. Para recibir asistencia sobre alguno de los dos casos, póngase en contacto con su representante de IBM.

El procedimiento siguiente se centra en el uso coordinado de un dispositivo de pasarela de almacenamiento en la nube AltaVault de NetApp, la infraestructura de {{site.data.keyword.cloud_notm}} y Veeam Backup & Replication para restaurar por completo un entorno de VMware vSphere que haya fallado. El software presupone que se ha realizado al menos una copia de seguridad del entorno utilizando las tecnologías anteriores, y que reside en {{site.data.keyword.cos_full_notm}} en el momento de la recuperación.

El “dispositivo AltaVault” se identifica de dos maneras distintas. La primera es “dispositivo AltaVault local”, que hace referencia el dispositivo AltaVault local original que ha fallado y desde el cual se recuperan y/o restauran las copias de seguridad. La segunda es “dispositivo AltaVault de {{site.data.keyword.cloud_notm}}”, que hace referencia al dispositivo AltaVault que se utiliza para recuperar copias de seguridad desde el dispositivo AltaVault local que ha fallado. El dispositivo AltaVault de {{site.data.keyword.cloud_notm}} se despliega en un entorno de vSphere utilizando un servidor de programas de utilidad.

## Introducción a una solución híbrida
{: #introducing-a-hybrid-solution}

Veeam Backup & Replication habilita una solución híbrida para incluir el dispositivo de almacenamiento integrado en la nube AltaVault de NetApp y {{site.data.keyword.cos_full_notm}}. La preocupación principal es la creación, mantenimiento y restauración a partir de copias de seguridad de entornos virtuales. El dispositivo de almacenamiento integrado en la nube AltaVault de NetApp es una solución de software para integrar a la perfección un entorno local con nubes privadas o públicas. Cuando se utilizan de forma conjunta, Veeam Backup & Replication crea copias de seguridad que el dispositivo de almacenamiento integrado en la nube AltaVault almacena en el almacenamiento local y replica simultáneamente en {{site.data.keyword.cos_full_notm}}. Además, el modelo de precios de paso según uso de {{site.data.keyword.cloud}} y la integración completa con la red de entrega de contenido (CDN) ofrecen la posibilidad de almacenar y distribuir datos en 24 nodos distribuidos en diversas zonas geográficas.

### IBM Cloud Object Storage
{: #ibm-cloud-object-storage}

Para acceder a los archivos de datos de los que se ha hecho copia de seguridad en {{site.data.keyword.cos_full_notm}}, puede solicitar los archivos de datos de las maneras siguientes:

* El dispositivo AltaVault que ya se encuentra en el entorno local
* Un dispositivo AltaVault secundario que se encuentra dentro de {{site.data.keyword.cloud_notm}}

Siga este procedimiento para desplegar un dispositivo AltaVault secundario dentro de {{site.data.keyword.cloud_notm}} para recuperar un dispositivo AltaVault local primario que haya fallado.

El entorno de {{site.data.keyword.cloud_notm}} consta de un host ESXi individual con almacenamiento local para alojar y ejecutar el dispositivo AltaVault secundario. La infraestructura es representante de la arquitectura básica de un solo sitio con un único host ESXi gestionada por un servidor vCenter dentro de una instancia de servidor virtual (VSI) de {{site.data.keyword.cloud_notm}}.

Si tiene una infraestructura más compleja que requiera almacenamiento compartido y/o soporte para estas características

* vSphere High Availability (HA)
* vSphere Distributed Resource Scheduler (DRS)
* vSphere vMotion

#### Requisitos previos
{: #prerequisites-ibm-cloud-object-storage}

Compruebe que se cumplen los requisitos previos siguientes antes de continuar: **Nota:** para este ejemplo, se ha utilizado una versión de prueba de un dispositivo AltaVault AVA-v8 con AltaVault Cloud-Integrated Storage 4.1.

* Asegúrese de que el entorno existente esté formado por un host ESXi individual gestionado por un servidor vCenter dentro de una VSI de {{site.data.keyword.cloud_notm}}.
* Conocimiento de la terminología de VMware vSphere y sobre la administración de entornos vSphere ESXi. Este conocimiento incluye, entre otros aspectos, el uso del cliente web de vSphere, el cliente de vSphere y la asignación de recursos de hardware para incluir redes y almacenamiento.

#### Solicitar dos redes privadas portátiles
{: #order-two-portable-private-networks}

AltaVault necesita que las interfaces de red estén en redes diferentes dentro del entorno. Realice los pasos siguientes para solicitar dos redes privadas portátiles a través del {{site.data.keyword.slportal_full}}.

1. Acceda al [{{site.data.keyword.slportal_full}} ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://control.softlayer.com/){: new_window} utilizando sus credenciales exclusivas.
2. Pulse **Cuenta > Realizar un pedido**.
3. Seleccione la sección **Red** y pulse **Subredes/IP > Pedir**.
4. En el menú desplegable, seleccione **Privada portátil**. La selección de Privada portátil activa una opción para seleccionar el número de direcciones IP privadas portátiles que se deben mostrar. **Nota:** {{site.data.keyword.cloud_notm}} reserva automáticamente al menos tres direcciones IP del bloque de direcciones para cada red privada portátil. Las direcciones reservadas son para la dirección de red, la dirección de pasarela y la dirección de difusión. Estas direcciones deben vincularse directamente a la LAN virtual (VLAN). <!--If your IBM Cloud pod supports Hot Standby Ready Protocol (HSRP), then the number of usable IP addresses is reduced. For instance, if you select **4 Portable Private IP Addresses** will yield only one usable IP address or zero if the pod supports HSRP.--> Para obtener más información sobre las direcciones IP portátiles, consulte [Iniciación a las subredes e IP](/docs/infrastructure/subnets?topic=subnets-getting-started-subnets-ips#getting-started-subnets-ips).
5. Pulse **Continuar** después de seleccionar el número de direcciones IP privadas portátiles.
6. Seleccione la VLAN donde se van direccionar las direcciones IP privadas portátiles y pulse **Continuar**.
7. Complete la información de contacto necesaria y pulse **Realizar pedido**.
8. Repita el proceso de pedido para obtener la segunda red privada portátil necesaria.
9. Pulse **Red > Gestión de IP > Subredes** en la página {{site.data.keyword.slportal}} para ver las redes privadas portátiles y las direcciones IP después de su asignación. Se recomienda que realice un seguimiento de las asignaciones de direcciones IP. Utilice la sección **Notas** de la página **Subredes** de cada dirección IP para anotar la máquina a la que está asignada.

#### Configuración de vSphere
{: #configuring-vsphere}

Es necesario configurar vSphere para reflejar la adición de redes privadas portátiles y asegurarse de que estén lógicamente separadas.

1. Vaya al entorno de vSphere y cree un grupo de puertos de máquina virtual para reflejar la adición de uno de los bloques de IP privados (dos en total).

El grupo de puertos de máquina virtual está etiquetado como **Red compartida**. Se utiliza un bloque de IP portátil para las máquinas virtuales que estén ubicadas en el grupo de puertos de máquina virtual predeterminado (principal). El otro se utiliza para la transferencia de datos entre un dispositivo AltaVault y un punto de montaje Common Internet File System (CIFS)/Server Message Block (SMB).

## Pasarela de almacenamiento en la nube AltaVault
{: #altavault-cloud-storage-gateway}

Puede utilizar AltaVault para integrar su entorno local con la nube sin necesidad de escribir scripts ni aplicación utilizando las API REST para {{site.data.keyword.cos_full_notm}}. AltaVault expone un punto de montaje CIFS/SMB o Network File System (NFS) en el frontal y se conecta de forma segura con la interfaz de {{site.data.keyword.cos_full_notm}} en el sistema de fondo. A continuación puede montar o hacer referencia a los puntos de montaje y copiar datos en entornos de recuperación/restauración desde la nube de forma segura. <!-- , as shown in Figure 1.-->

<!-- ![Figure 1](images/veeam_restore_fig2.png)
<br/>`Figure 1: AltaVault end-to-end flow with Veeam and IBM Cloud`-->

### Despliegue de AltaVault en una configuración de recuperación tras desastre
{: #deploying-altavault-in-a-disaster-recovery-configuration}

Siga estos pasos para desplegar AltaVault como una solución de recuperación tras desastre con {{site.data.keyword.cos_full_notm}}.

#### Antes de empezar
{: #before-you-get-started-deploying-altavault-in-a-disaster-recovery-configuration}

Compruebe que se cumplan los requisitos previos siguientes antes de continuar:

* Obtenga una copia del dispositivo virtual AltaVault y asegúrese de que se encuentra en el servidor de programas de utilidad. Se trata de un archivo individual con una extensión de archivo OVA. Póngase en contacto con el representante de NetApp para el dispositivo, o descargue una versión de prueba de 90 días del [Sitio web de AltaVault de NetApp ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://www.netapp.com/us/products/protection-software/altavault/){: new_window}.
* Debe disponer de un entorno local vSphere ESXi 5.5 con los requisitos mínimos de CPU, memoria y espacio de disco para el dispositivo AltaVault. Si utiliza la versión de prueba, aquí están los requisitos de recursos: cuatro CPU virtuales (vCPU), 24 GB de memoria y hasta 8 TB de espacio de disco.
* Debe disponer de dos controladores de interfaz de red (NIC) de 10 Gbps dentro del entorno de vSphere. Un NIC se utiliza para el acceso a datos y el otro se utiliza para la réplica de datos en {{site.data.keyword.cos_full_notm}}.
* Debe disponer de dos redes privadas portátiles que correspondan a los dos NIC definidos dentro del entorno de vSphere. La red de réplica no se puede asignar a la misma red que la red de acceso a datos, lo cual puede crear un bucle de direccionamiento.
* Debe disponer de credenciales de {{site.data.keyword.cos_full_notm}}. Estas credenciales incluyen un nombre de usuario de {{site.data.keyword.cloud_notm}}, un nombre de usuario de {{site.data.keyword.cos_full_notm}} y la clave de API asociada con el nombre de usuario de {{site.data.keyword.cloud_notm}}.
* Suspenda la réplica de datos en el dispositivo AltaVault local o desconéctelo del contenedor/grupo que se utiliza para acceder al archivo de copia de seguridad en {{site.data.keyword.cloud_notm}} si el entorno local permanece intacto.
  * Utilice uno de los dos métodos siguientes para suspender o detener la réplica:
    * Apague el dispositivo AltaVault local.
    * Vaya a la consola de gestión web del dispositivo AltaVault y pulse **Almacenamiento > Configuración de la nube > Réplica**. Seleccione **Suspender réplica**.
    * Obtenga una copia del archivo de configuración del dispositivo AltaVault local o haga que sea accesible a través de un URL. El archivo de configuración es un archivo .tar.
  * Vaya a la consola de gestión web del dispositivo AltaVault y pulse **Configurar > Asistente de configuración > Exportar configuración > Exportar configuración**. Guarde el archivo de configuración (archivo .tar) y transfiéralo al servidor de programas de utilidad o haga que sea accesible a través de un URL.
    * Conocimiento de la terminología de VMware vSphere y sobre la administración de entornos vSphere ESXi. Este conocimiento incluye, entre otros aspectos, el uso del cliente web de vSphere, el cliente de vSphere y la asignación de recursos de hardware como redes y almacenamiento.

### Despliegue del OVA de AltaVault
{: #deploying-altavault-ova-disaster}

Despliegue el OVA de AltaVault en el entorno de vSphere utilizando el servidor de programas de utilidad después de que se hayan cumplido todos los requisitos previos. Las instrucciones para el despliegue del OVA pueden encontrarse en la [Guía de instalación y servicio de NetApp AltaVault ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}. La versión de prueba de AltaVault viene configurada con cuatro CPU virtuales, 6 GB de memoria y un disco primario de 150 GB. Utilice la Guía de instalación y servicio para realizar los pasos siguientes:

1. Aumente la cantidad de memoria a 24 GB.
2. Añada un dispositivo de almacenamiento de disco secundario que tenga un tamaño menor o igual a 8 TB para almacenar datos de copia de seguridad deduplicados.
3. Asigne distintas redes privadas portátiles al dispositivo AltaVault después de que se modifiquen las configuraciones de memoria y disco.

Se asignan las siguientes funciones de interfaz a los NIC:

* **Primaria**: utilizado para la gestión del dispositivo AltaVault y la réplica de datos en la nube. Tiene asignado el grupo de puertos **Red primaria** en el entorno de ejemplo.
* **e0a**: una interfaz opcional utilizada para replicar datos del dispositivo AltaVault en la nube.
* **e0b**: una interfaz utilizada para exportar el punto de montaje para la compartición SMB/CIFS o NFS. Tiene asignado el grupo de puertos **Red compartida** en el entorno de ejemplo.
* **e0c**: una interfaz opcional utilizada para exportar el punto de montaje para la compartición SMB/CIFS o NFS.

En la configuración de ejemplo de este procedimiento, el dispositivo AltaVault utiliza la interfaz **Primaria** como interfaz de réplica en la nube y la interfaz **e0b** para exportar un punto de montaje CIFS/SMB. **Nota:** no puede utilizar tanto una compartición CIFS/SMB como una compartición NFS para acceder a los mismos datos. El protocolo utilizado debe ser el mismo que el protocolo que se haya utilizado con el dispositivo AltaVault local para replicar datos en la nube.

<!-- ![Figure 2](images/veeam_restore_fig3.png)
<br/>`Figure 2: IBM Cloud setup with VMware`-->

Para obtener más información sobre el despliegue del dispositivo AltaVault y la configuración de los valores de máquina virtual, consulte [Guía de instalación y servicio de NetApp AltaVault ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}.

### Configuración del dispositivo AltaVault
{: #configuring-the-altavault-appliance}

1. Puede encender la máquina virtual AltaVault una vez que se haya configurado. La máquina virtual puede tardar cierto tiempo en iniciarse, ya que el dispositivo AltaVault está formateando el disco de almacenamiento auxiliar.

2. Inicie sesión en la consola de AltaVault utilizando **admin** como **Nombre de usuario** y **password** como **Contraseña** una vez que el dispositivo haya finalizado el proceso de arranque. Puede cambiar estas credenciales después de completar la configuración inicial. Consulte la Tabla 1: Valores de la configuración inicial de AltaVault

3. Después de iniciar sesión en el dispositivo, se le preguntará si desea utilizar el asistente para la configuración. Especifique *y*.
4. Utilice la información de la Tabla 1 después de entrar en el asistente.
5. Pulse **Intro** para guardar los cambios.

|Pregunta|Respuesta|
|---|---|
|Paso 1: ¿Contraseña de administrador?|Especifique una nueva contraseña de administrador (no puede ser **password**).|
|Paso 2: ¿Nombre de host?|Especifique el nombre de host adecuado.|
|Paso 3: ¿Utilizar DHCP en interfaz primaria?|Especifique **n** o **no**.|
|Paso 4: ¿Dirección IP primaria?|Especifique la dirección IP de red primaria. En la configuración de ejemplo, esta es la red utilizada para la interfaz de gestión (por ejemplo, 10.120.108.132).|
|Paso 5: ¿Máscara de red?|Introduzca la máscara de red (ej. 255.255.255.192).|
|Paso 6: ¿Pasarela predeterminada?|Especifique la pasarela predeterminada (ej., 10.120.108.129).|
|Paso 7: ¿Servidor DNS primario?|Introduzca el servidor de sistema de nombres de dominio (DNS) primario de su entorno.|
|Paso 8: ¿Nombre de dominio?|Escriba el nombre de dominio del entorno (ej., testenv.org).|
{: caption="Tabla 1. Valores de la configuración inicial de AltaVault" caption-side="top"}

### Configuración de AltaVault para IBM Cloud Object Storage
{: #configuring-altavault-for-ibm-cloud-object-storage}

El dispositivo AltaVault se debe conectar al servicio {{site.data.keyword.cos_full_notm}} después de su configuración. En la configuración local, se utiliza el DNS público para establecer la conexión con el servicio Object Storage. En esta configuración, se utiliza el nombre de DNS interno en su lugar.

1. Abra un navegador web y escriba la dirección IP del dispositivo AltaVault.
2. Inicie sesión en la consola con las credenciales de administración. Tras el inicio de sesión inicial, aparecerá al **Panel de control del asistente**.
3. Seleccione **Valores del sistema**, compruebe que todos los valores de red sean correctos y ajuste el huso horario para que refleje el huso horario de su entorno.
4. Seleccione **Siguiente > Guardar y aplicar > Salir**. Volverá al **Panel de control del asistente** y se cerrará la sesión actual de su navegador.
5. Vuelva a iniciar sesión en el dispositivo AltaVault, pulse **Valores > Asistente de configuración > Importar configuración** y especifique la ubicación del archivo de configuración del dispositivo AltaVault local.
6. Seleccione **Importar solo datos compartidos**, lo cual importa únicamente los valores compartidos de manera común, como los valores de la nube o de correo electrónico.
7. Especifique la frase de contraseña para la clave de cifrado que haya especificado durante la creación del dispositivo AltaVault local, si se ha establecido una, en el recuadro de texto **Frase de contraseña de clave**.
8. Pulse **Importar configuración > Salir**.

<!--The AltaVault appliance web console displays a message indicating a successful import. If the import was not successful, verify that the configuration file is in the correct format and that the passphrase is correct before trying again.-->

Ha importado la configuración del dispositivo AltaVault local en el dispositivo AltaVault de {{site.data.keyword.cloud_notm}}.

Realice los pasos siguientes para modificar los valores de la nube del dispositivo AltaVault de {{site.data.keywrod.cloud_notm}} para proporcionarle acceso al servicio {{site.data.keyword.cos_full_notm}} a través de la red privada.

1. En AltaVault, vaya a **Almacenamiento > Configuración de la nube > Nube** y modifique el nombre de host para que refleje la dirección privada del servicio {{site.data.keyword.cos_full_notm}}.
  * La sintaxis del nombre de la red privada es <ubicación>.objectstorage.service.networklayer.com, donde *<ubicación>* hace referencia al nombre del centro de datos abreviado (ejemplo: mel01 para el centro de datos Melbourne 01).
2. Pulse **Aplicar**. El dispositivo AltaVault intenta conectar con el servicio {{site.data.keyword.cos_full_notm}}. Si falla este intento de conexión, compruebe que los valores del proveedor de nube sean correctos antes de volver a intentar establecer la conexión.

Después de conectarse a {{site.data.keyword.cos_full_notm}}, el dispositivo necesita pasar a la modalidad de recuperación para sincronizar el contenido de metadatos de los datos de copia de seguridad originales de {{site.data.keyword.cos_full_notm}}.

Lleve a cabo los pasos siguientes para poner el dispositivo AltaVault en modalidad de recuperación.

El dispositivo AltaVault sincroniza los datos de {{site.data.keyword.cos_full_notm}}. **Nota:** el tiempo que tardará en completarse la sincronización depende del tamaño y el número de copias de seguridad que se vayan a restaurar.

1. Vaya al dispositivo AltaVault, inicie sesión y especifique los mandatos que se indican en la Figura 3:<br/>![Figura 3](images/veeam_restore_fig5.png)<br/>`Figura 5: Mandatos de la modalidad de recuperación`
2. Vuelva a la consola web del dispositivo AltaVault una vez que finalice el proceso de sincronización.
3. Verifique que el **servicio de optimización del almacenamiento** se está **ejecutando** y que su **estado** es **ready**. Pueden transcurrir unos minutos hasta que el **Estado** cambie a **ready**.

### Configurar el punto de montaje CIFS/SMB en AltaVault
{: #configure-the-cifs-smb-mount-point-in-altavault}

Después de que se haya establecido la conexión interna con {{site.data.keyword.cos_full_notm}}, configure la interfaz **e0b** para acceder a un punto de montaje CIFS/SMB.

1. En la consola web de AltaVault, vaya a **Valores > Interfaces de datos**.
2. Expanda la interfaz **e0b** y seleccione **Habilitar interfaz de datos**, si es necesario.
3. Especifique la dirección IP, la máscara de subred y la pasarela que desee utilizar para montar la compartición CIFS/SMB. Asegúrese de utilizar una subred distinta a la que se ha utilizado para la interfaz primaria.
4. Deje el valor predeterminado de **MTU** en **1500 bytes**.
  * Aunque la unidad de transmisión máxima (MTU) predeterminada es 1500, puede cambiarla a 9000 si utiliza tramas de gran tamaño. Es necesario que el host de ESXi y la infraestructura física tengan soporte para tramas de gran tamaño. De forma predeterminada, {{site.data.keyword.cloud_notm}} admite un tamaño de MTU de 9000 bytes sin que sean necesarios cambios de configuración.
5. Pulse **Aplicar**.

El dispositivo AltaVault tiene ahora la configuración mínima para permitir comunicaciones entre el propio dispositivo, {{site.data.keyword.cos_full_notm}} y Veeam Backup & Replication.

## Veeam Backup & Replication
{: #veeam-backup-replication-recover}

El software Veeam Backup & Replication proporciona funciones completas de copia de seguridad, réplica y recuperación para las máquinas virtuales y sus datos. Veeam Backup & Replication se puede integrar completamente con un dispositivo de pasarela de nube AltaVault, ofreciendo una experiencia de copia de seguridad y recuperación sin fisuras.

### Desplegar Veeam Backup & Replication
{: #deploy-veeam-backup-replication}

En el ejemplo se utiliza una versión de prueba de Veeam Backup & Replication Versión 8.

#### *Requisitos previos*
{: #prerequisites-deploy-veeam-backup-replication}

Antes de continuar con el despliegue, asegúrese de que se cumplen los requisitos previos siguientes:

* Debe disponer de un dispositivo AltaVault existente configurado para su uso con IBM Cloud Object Storage y Veeam Backup & Replication.
* Obtenga una copia de Veeam Backup & Replication para entornos de VMware, que es un solo archivo ejecutable. Póngase en contacto con el representante de Veeam para obtener una copia o descargar una [versión de prueba de 30 días ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}.
* Obtenga un archivo de licencia para su uso con Veeam Backup & Replication. En la mayoría de los casos, este archivo se enviará por correo electrónico a la dirección que haya utilizado para descargar Veeam Backup & Replication. Si no ha recibido este archivo, póngase en contacto con su representante de Veeam.<br/><br/>El archivo de licencia se utiliza para activar la funcionalidad completa de Veeam Backup & Replication. Si no se proporciona este archivo durante la instalación del programa, todas las características y funciones se revertirán a las de la versión de prueba de 30 días.
* Debe disponer de una instancia de servidor virtual (VSI) suministrada en IBM Cloud con las especificaciones de la Tabla 2. **Nota:** el sistema operativo instalado debe ser una versión de 64 bits.

||Mínimo|Recomendado|
|---|---|---|
|**SO**|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>Windows Server 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|<ul><li>Windows Servers 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>WIndows 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|
|**Número de núcleos o vCPU**|2|4|
|**Memoria**|4 GB de RAM base más 500 MB para cada trabajo de copia de seguridad y restauración simultáneo.|16 GB de RAM base más 4 GB para cada trabajo de copia de seguridad y restauración simultáneo.|
|**Espacio de disco**|2 GB para la instalación del producto, 10 GB por cada 100 máquinas virtuales para la carpeta del catálogo del sistema invitado (datos persistentes).|2 GB para la instalación del producto, 10 GB por cada 100 máquinas virtuales para la carpeta del catálogo del sistema invitado (datos persistentes).|
|**Red**|LAN de 1 Gbps para la copia de seguridad y la réplica in situ, WAN de 1 Mbps para la copia de seguridad y réplica fuera del sitio.|LAN de 1 Gbps para la copia de seguridad y la réplica in situ, WAN de 1 Mbps para la copia de seguridad y réplica fuera del sitio.|
{: caption="Tabla 2. Requisitos del sistema para la VSI de {{site.data.keyword.cloud_notm}}" caption-side="top"}

Suministre la VSI dentro del mismo centro de datos que el dispositivo AltaVault y el entorno de vSphere. La VSI debe tener acceso al dispositivo AltaVault y a vCenter.

### Instalación de Veeam Backup & Replication
{: #installing-veeam-backup-replication-disaster}

Utilice los pasos siguientes para instalar Veeam Backup & Replication en la VSI de {{site.data.keyword.cloud_notm}} después de que se hayan cumplido todos los requisitos previos.

1. Realice una doble pulsación en el ejecutable del programa y pulse **Veeam Backup & Replication – Instalar**. Se abrirá el asistente de configuración.
* Pulse **Siguiente** y seleccione **Acepto los términos del acuerdo de licencia**.
* Pulse **Siguiente** y especifique la ubicación del archivo de licencia que se obtiene bajo Desplegar Veeam Backup & Replication.
* Pulse **Siguiente** y seleccione qué componentes de Veeam Backup & Replication desea instalar y la ubicación de la instalación en la pantalla **Características del programa**. **Nota:** **Veeam Backup & Replication** y **Veeam Backup Catalog** son componentes necesarios.
* Pulse **Siguiente**.<br/>El asistente de configuración realiza una serie de comprobaciones para asegurarse de que están instaladas las infraestructuras de programa necesarias y los componentes que les dan soporte. Si falta algún componente, el asistente de configuración le ofrecerá instalarlo. <!--Click **Install** if this is the case.-->
2. Verifique que todos los componentes **superan** las comprobaciones de sistema y pulse **Siguiente**.
3. Seleccione la **Cuenta de servicio (usuario)** en la que desea que se ejecute el servicio Veeam Backup. La cuenta de servicio predeterminada es la **cuenta LOCAL SYSTEM**. Pulse **Siguiente**.
4. Seleccione la **Instancia de SQL Server** que desee utilizar para crear y almacenar las bases de datos de Veeam Backup & Replication. Para obtener más información, póngase en contacto con el administrador de la base de datos. Pulse **Siguiente**.
5. Especifique el **Puerto de servicio de catálogo** (9393) y el **Puerto de servicio de Veeam Backup** (9392). Pulse **Siguiente**.
6. Seleccione los directorios donde desea almacenar el catálogo del sistema de archivos invitado (datos persistentes) y la memoria caché de escritura de vPower NFS (datos no persistentes). Pulse **Siguiente**.
7. Compruebe que todos los parámetros y valores sean correctos y pulse **Instalar**. Una vez que finalice la instalación, pulse **Finalizar**.

### Configuración de Veeam Backup & Replication
{: #configuring-veeam-backup-replication}

Después de instalar Veeam Backup & Replication, estará listo para conectarse con el dispositivo AltaVault.

1. Inicie Veeam Backup & Replication.
2. En la parte inferior izquierda de la pantalla, pulse **Infraestructura de copia de seguridad**.
3. En la ventana **Infraestructura de copia de seguridad**, pulse **Servidores gestionados > Servidores gestionados**.
4. En el menú de cinta superior, pulse **Añadir servidor** y realice una doble pulsación sobre **VMware vSphere**.
5. Especifique la dirección IP privada del servidor vSphere y pulse **Siguiente**.
6. Especifique las credenciales de cuenta de una cuenta local con privilegios de administrador en el servidor vSphere que haya especificado anteriormente. **Nota:** el nombre de usuario de la cuenta debe tener el formato DOMINIO\USUARIO para cuentas de dominio, o el formato HOST\USUARIO para cuentas locales. Para añadir una cuenta, pulse **Añadir** y especifique el nombre de usuario y la contraseña adecuados. No cambie el puerto de servicios web de VMware predeterminado a menos que se lo indique el administrador de red. <!-- otherwise during [Veeam Backup & Replication installation](#_Installing_Veeam_Backup).--> Pulse **Siguiente**.
  * Veeam Backup & Replication intenta establecer la conexión con el servidor vSphere. Si el intento de conexión falla, compruebe que la cuenta existe y que tiene privilegios de administrador en el servidor vSphere antes de volver a intentarlo.
7. Pulse **Finalizar**.
8. Compruebe que el servidor vSphere se haya añadido correctamente pulsando **Servidores gestionados > VMware vSphere**.

### Adición de un repositorio de copia de seguridad a Veeam Backup & Replication
{: #adding-a-backup-repository-to-veeam-backup-replication-disaster}

Siga estos pasos para crear un nuevo repositorio de copia de seguridad para almacenar copias de seguridad restauradas en el dispositivo AltaVault. **Nota:** se utiliza una compartición CIFS/SMB para el ejemplo, ya que el servidor de copia de seguridad de Veeam Backup & Replication debe estar alojado en una máquina que ejecute Windows. El protocolo de compartición utilizado para restaurar copias de seguridad en el dispositivo AltaVault de {{site.data.keyword.cloud_notm}} debe ser idéntico al protocolo de compartición utilizado para esas mismas copias de seguridad en el dispositivo AltaVault local; se utiliza una compartición CIFS/SMB.

1. En la esquina inferior izquierda, pulse **Infraestructura de copia de seguridad > Repositorios de copia de seguridad**.
2. En el menú de cinta superior, pulse **Añadir repositorio**.
3. Proporcione un **Nombre** exclusivo para el repositorio, así como una **Descripción** adecuada. Pulse **Siguiente**.
4. Seleccione el tipo de compartición que corresponda al tipo de compartición que se ha utilizado en el dispositivo AltaVault local. Ejemplo: si el tipo de compartición utilizado en el dispositivo AltaVault local es CIFS/SMB, seleccione **Carpeta compartida**.
5. Especifique la ubicación donde desee que se restauren las copias de seguridad de la compartición CIFS/SMB en el dispositivo AltaVault. La compartición tiene el mismo nombre que la compartición a través de la cual se han procesado las copias de seguridad en el dispositivo AltaVault local. Por ejemplo, si se ha utilizado una compartición denominada `cifs_test1` para las copias de seguridad en el dispositivo AltaVault local, especifique la ubicación de la compartición `cifs_test1` en el dispositivo AltaVault de {{site.data.keyword.cloud_notm}}.
  * Abra un navegador web y escriba la dirección IP del dispositivo AltaVault para determinar la ubicación.
  * Vaya a **Almacenamiento > CIFS** y anote la **Vía de acceso de compartición** de la compartición. **Nota:** esta NO es la misma que la vía de acceso local de la compartición. El formato de la vía de acceso de compartición es `\\<AltaVault appliance hostname>\<share name>`. Sustituya el nombre de host del dispositivo AltaVault en la vía de acceso de compartición por la dirección IP de la interfaz de red e0b (el punto de montaje de la compartición) del dispositivo AltaVault.<br/>
   * Pulse **Valores > Interfaces de datos** en la ventana de gestión del dispositivo AltaVault para encontrar la dirección IP de la interfaz **e0b**. Para obtener más información sobre cómo configurar montajes IFS/SMB, consulte Configurar un punto de montaje CIFS/SMB en AltaVault.
   * La vía de acceso de compartición especificada en Veeam Backup & Replication sería `\\10.120.108.133\cifs_test1`, no `\\restore-appliance\cifs_test1`.
6. Vuelva a Veeam Backup & Replication, especifique la vía de acceso de compartición del punto de montaje en el campo **Carpeta compartida** y pulse **Siguiente**. Veeam Backup & Replication intenta establecer una conexión con el punto de montaje. Si la conexión falla, vuelva atrás y compruebe que los valores de red del dispositivo AltaVault sean correctos antes de intentarlo de nuevo.
7. Especifique un valor para **Limitar el máximo de tareas simultáneas** al número de recursos disponibles en la pantalla **Nuevo repositorio de copia seguridad**. Este valor es el número máximo de tareas que puede enviar un proxy de copia de seguridad a la compartición seleccionada. El número predeterminado de tareas simultáneas es cuatro. AltaVault recomienda que comience con cinco tareas simultáneas y que aumente o disminuya este valor en la medida en que los recursos lo permitan.
8. Pulse **Siguiente**.
9. Especifique los valores opcionales de vPower NFS en la pantalla **vPower NFS**. Si se deja sin marcar el recuadro de selección **Habilitar el servidor vPower NFS**, Veeam Backup & Replication utilizará vPower para la recuperación y la verificación de la recuperación. Pulse **Siguiente**.
10. Seleccione **Importar copias de seguridad existentes automáticamente**, a menos que utilice la selección manual de copias de seguridad de máquina virtual.
11. Compruebe que todos los valores sean correctos y pulse **Siguiente**.
* Pulse **Finalizar** para salir del asistente.
12. Pulse **Sí** para continuar.

Veeam Backup & Replication está configurado y puede empezar a restaurar copias de seguridad.

### Restauración del entorno
{: #restoring-the-environment}

Siga estos pasos para restaurar completamente una restauración de máquina virtual.

1. En la parte inferior izquierda de la pantalla, pulse **Backup & Replication**.
2. En el menú de cinta superior, pulse **Restaurar**.
3. Seleccione **Máquina virtual completa (incluyendo registro)**. **Nota:** no realice ninguna selección bajo **Restaurar a partir de réplica**. Pulse **Siguiente**.
4. Pulse **Añadir máquina virtual > Desde copia de seguridad**. Verá las máquinas virtuales que haya replicado anteriormente en {{site.data.keyword.cos_full_notm}} a través del dispositivo AltaVault local y la compartición CIFS/SMB seleccionada. Pulse **Añadir > Siguiente**.
5. Restaure las máquinas virtuales en una nueva ubicación <!--[because the original location of the VM(s) failed]--> seleccionando **Restaurar en una ubicación nueva o con valores distintos**. Pulse **Siguiente**.
6. En la pantalla **Host**, seleccione un host de destino para cada máquina virtual que vaya a restaurar. Seleccione una máquina virtual y, a continuación, pulse **Host**.
7. Seleccione el host de destino en **Seleccionar host** y pulse **Aceptar**.<br/>Veeam Backup & Replication intenta establecer contacto con los hosts de destino para asegurarse de que están en línea y listos para recibir datos. Si este intento falla, verifique que todos los valores de red sean correctos antes de volver a intentarlo. Una vez finalizada la comprobación, pulse **Siguiente**.
8. Seleccione una **Agrupación de recursos** opcional para cada máquina virtual. Pulse **Siguiente**.
9. Seleccione un almacén de datos de destino y un tipo de disco para cada máquina virtual que vaya a restaurar. Pulse **Siguiente**.<br/>Veeam Backup & Replication intentará validar los almacenes de datos de destino. Si la validación falla, compruebe que el almacén de datos tiene suficiente capacidad para las máquinas virtuales que se van a restaurar y verifique todos los valores de red antes de volver a intentar la conexión.
10. Seleccione una carpeta de destino en los almacenes de datos de destino para cada máquina virtual que desee restaurar. Pulse **Siguiente**.
11. Especifique conexiones de red y valores para cada máquina virtual. Pulse **Siguiente**.
12. Especifique un **Motivo de restauración** opcional proporcionando un motivo por el cual va a realizar la operación de restauración. Pulse **Siguiente**.<br/>Veeam Backup & Replication intentará validar las máquinas virtuales que va a restaurar. Espere a que esta validación finalice correctamente antes de continuar.
13. Verifique las operaciones de restauración y los valores de la ventana **Backup & Replication** y pulse **Finalizar**. Veeam Backup & Replication comenzará automáticamente la restauración de las máquinas virtuales seleccionadas.

## Pasos siguientes
{: #next-steps-recovering-your-vmware-vsphere-environment}

<!--The recent rise in Object Storage can be heavily attributed to enterprises' desire to reduce or even eliminate the operational and capital expenditures associated with comparatively expensive on-premises storage. While Object Storage is a highly cost-effective solution, it introduces a new problem of how to access stored files. In the case of stored data backups, the issue is how to use them to recover an environment that has failed for whatever reason.-->

<!--This problem is handily resolved by Veeam Backup & Replication, along with a NetApp AltaVault Cloud Gateway appliance, and {{site.data.keyword.cos_full_notm}}. When used together, they create a seamless backup and recovery experience that can be tailored to fit a wide variety of environmental requirements and constraints. By following the steps outlined in this procedure, enterprises can quickly realize the benefits of cloud-based storage, while reducing the complexities involved in shifting from on-premises storage to Object Storage using the three aforementioned technologies.-->

Para obtener más información sobre los componentes de esta solución híbrida, consulte:

* [Sitio web de NetApp AltaVault ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://www.netapp.com/us/products/protection-software/altavault/){: new_window}
* [Sitio web de Veeam Backup & Replication ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}
* [Sitio web de IBM Cloud Object Storage ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://www.ibm.com/cloud/storage){: new_window}

El *servidor de programas de utilidad* hace referencia a un servidor que contiene el cliente VMware vSphere que se utiliza para acceder a un entorno privado y a través del cual se despliega el dispositivo AltaVault utilizado para las operaciones de recuperación de datos.
