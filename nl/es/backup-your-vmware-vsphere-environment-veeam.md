---

copyright:
years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: vsphere, Veeam
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}
{:table: .aria-labeledby="caption"}

# Copia de seguridad del entorno de VMware vSphere utilizando Veeam
{: #backing-up-your-vmware-vsphere-environment-by-using-veeam}

Puede realizar una copia de seguridad de su entorno de VMware vSphere utilizando una solución híbrida compuesta por los servicios siguientes:

* Servicio {{site.data.keyword.cloud}} Object Storage
* Pasarela de almacenamiento en la nube AltaVault de NetApp
* Software Veeam Backup & Replication

Veeam Backup & Replication habilita una solución híbrida que incluye el dispositivo de almacenamiento integrado en la nube AltaVault de NetApp y {{site.data.keyword.cos_full}}. El software crea, mantiene y restaura entornos virtuales a partir de las copias de seguridad. Cuando se utiliza junto con un dispositivo de almacenamiento integrado en la nube de NetApp AltaVault, se crean copias de seguridad que se almacenan localmente (en el entorno local). La copia de seguridad también se replica de forma simultánea en {{site.data.keyword.cos_full_notm}}. Con esta solución híbrida, se realizan dos copias de una copia de seguridad, pero solo una de ellas existe localmente.

## Pasarela de almacenamiento integrado en la nube AltaVault
{: #altavault-cloud-integrated-storage-gateway}

Puede utilizar la pasarela de almacenamiento en la nube AltaVault para integrar su entorno local con la nube sin necesidad de escribir scripts ni aplicación utilizando las API REST para {{site.data.keyword.cos_full_notm}}. Puede montar o apuntar a los puntos de montaje y empezar a copiar datos en la nube de forma segura.

### Despliegue de AltaVault en el entorno local
{: #deploying-altavault-on-premises}

Siga estos pasos para desplegar AltaVault como una solución de copia de seguridad local en {{site.data.keyword.cos_full_notm}}.

Puede adquirir AltaVault como un dispositivo físico o virtual. El despliegue de la versión de prueba del dispositivo virtual AltaVault basado en VMware vSphere ESXi se cubre en este procedimiento.
{:tip}

<a name="prerequisites"></a>
#### Requisitos previos

Compruebe que se cumplan los requisitos previos siguientes:

* Una copia del dispositivo virtual AltaVault. Se trata de un archivo individual con una extensión de archivo OVA. Póngase en contacto con el representante de NetApp para el dispositivo, o descargue una versión de prueba de 90 días del [Sitio web de AltaVault de NetApp ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](http://www.netapp.com/us/products/protection-software/altavault/){: new_window}.
* Un entorno local vSphere ESXi 5.5 existente con los requisitos mínimos de CPU, memoria y espacio de disco para el dispositivo AltaVault. Si utiliza la versión de prueba, los requisitos son cuatro CPU virtuales (vCPU), 24 GB de memoria y hasta 8 TB de espacio de disco.
* Dos controladores de interfaz de red (NIC) de 10 Gbps dentro del entorno de vSphere. Un NIC se utiliza para la entrada de datos y el otro se utiliza para la réplica de datos en {{site.data.keyword.cos_full_notm}}.
* Dos redes privadas portátiles que correspondan a los dos NIC (VLAN) definidos dentro del entorno de vSphere. La red de réplica no se puede asignar a la misma red que la red de entrada de datos, lo cual puede crear un bucle de direccionamiento.
* Un conjunto de credenciales de {{site.data.keyword.cos_full_notm}}. Estas credenciales incluyen un nombre de usuario de {{site.data.keyword.cloud_notm}}, un nombre de usuario de {{site.data.keyword.cos_full_notm}} y la clave de API asociada con el nombre de usuario de {{site.data.keyword.cloud_notm}}.
* Conocimiento de la terminología de VMware vSphere y sobre la administración de entornos vSphere ESXi. Este conocimiento incluye, entre otros aspectos, el uso del cliente web de vSphere, el cliente de vSphere y la asignación de recursos de hardware, que incluyen redes y almacenamiento.

### Despliegue del OVA de AltaVault
{: #deploying-altavault-ova}

Puede desplegar el OVA de AltaVault en el entorno de vSphere después de que se hayan cumplido todos los requisitos previos. Las instrucciones para el despliegue del OVA pueden encontrarse en la [Guía de instalación y servicio de NetApp AltaVault ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}.

1. Edite la máquina virtual (VM) AltaVault después de que finalice el despliegue del OVA.
2. Cambie la memoria asignada para que coincida con la versión de AltaVault que se encuentra en la ventana de edición. Si está utilizando la versión de prueba, asigne 24 GB de memoria y añada un disco que sea inferior o igual a 8 TB. **Nota:** este dispositivo de almacenamiento de disco secundario se utiliza para almacenar datos de copia de seguridad deduplicados.
* Asegúrese de asignar distintas redes (VLAN) al dispositivo AltaVault después de modificar las configuraciones de memoria y disco.

Se asignan las siguientes funciones de interfaz a los NIC:

* Primaria. Se utiliza como interfaz de gestión.
* e0a. Una interfaz utilizada para replicar datos del dispositivo AltaVault en la nube.
* e0b. Una interfaz utilizada para exportar el punto de montaje para la compartición SMB/CIFS o NFS.
* e0c. Una interfaz opcional utilizada para exportar el punto de montaje para la compartición SMB/CIFS o NFS.

En esta configuración de ejemplo, el dispositivo AltaVault utiliza la interfaz **e0a** como interfaz de réplica en la nube y la interfaz **e0b** para exportar un punto de montaje CIFS/SMB. **Nota:** no puede utilizar tanto una compartición CIFS/SMB como una compartición NFS para acceder a los mismos datos. En otras palabras, si los datos se colocan en una compartición CIFS/SMB, no se puede acceder a ellos a través de una compartición NFS y viceversa.

Para obtener más información sobre el despliegue del dispositivo AltaVault y la configuración de los valores de máquina virtual del dispositivo, consulte [Guía de instalación y servicio de NetApp AltaVault ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}.

### Configuración inicial del dispositivo AltaVault <!--initial configuration?-->
{: #initial-configuration-of-the-altavault-appliance-initial-configuration-}

Puede encender la máquina virtual AltaVault una vez que se haya configurado con el hardware adecuado. **Nota:** inicialmente, la máquina virtual AltaVault tarda algún tiempo en iniciarse, ya que el dispositivo AltaVault está formateando el disco de memoria caché de metadatos secundario.

1. Una vez que el dispositivo haya completado el proceso de inicio, inicie la sesión en la consola de AltaVault. Utilice ``*admin`` como **Nombre de usuario** y ``password`` como **Contraseña**. Puede cambiar estas credenciales cuando haya finalizado la configuración inicial.
2. Después de iniciar sesión, se le preguntará si desea utilizar el asistente para la configuración inicial. Especifique **y** y pulse **Intro** para guardar sus cambios.

Utilice la información de la Tabla 1 después de que se abra el asistente.

|Pregunta|Respuesta|
|---|---|
|Paso 1: ¿Contraseña de administrador?|Especifique una nueva contraseña de administrador (no puede ser "password")|
|Paso 2: ¿Nombre de host?|Especifique el nombre de host que desee utilizar|
|Paso 3: ¿Utilizar DHCP en interfaz primaria?|Especifique **n**|
|Paso 4: ¿Dirección IP primaria?|Especifique la dirección IP de red primaria. En la configuración de ejemplo, la dirección IP primaria es la red que se utiliza para la réplica de nube y la gestión de dispositivos (192.168.50.15)|
|Paso 5: ¿Máscara de red?|Especifique la máscara de red (255.255.255.0)|
|Paso 6: ¿Pasarela predeterminada?|Especifique la pasarela predeterminada (192.168.50.1)|
|Paso 7: ¿Servidor DNS primario?|Introduzca el servidor de sistema de nombres de dominio (DNS) primario de su entorno|
|Paso 8: ¿Nombre de dominio?|Escriba el nombre de dominio del entorno (testenv.org)|
{: caption="Tabla 1. Valores de la configuración inicial de AltaVault" caption-side="top"}

### Configuración de AltaVault para Object Storage
{: #configuring-altavault-for-object-storage}

Utilice los pasos siguientes para configurar el dispositivo para conectarse al servicio {{site.data.keyword.cos_full_notm}}.

1. Abra un navegador web y escriba la dirección IP de la interfaz primaria del dispositivo AltaVault (consulte el paso anterior).
2. Inicie sesión en la consola con las credenciales de administración. Tras el primer inicio de sesión, aparecerá el panel de control del asistente.
3. Seleccione **Valores del sistema**, compruebe que la información sean correcta en la pantalla siguiente y ajuste el huso horario para que refleje el huso horario de su entorno.
4. Pulse **Siguiente > Guardar y aplicar > Salir**. Volverá al panel de control del asistente.
5. Seleccione **Configuración de la nube** y pulse **Proveedor**. Elija **IBM Cloud Object Storage**.
6. Seleccione la **Región de Object Storage** adecuada. **Nota:** no se muestran todas las regiones (como Melbourne). No obstante, el nombre de host del servicio {{site.data.keyword.cos_short}} se modifica utilizando el campo **Nombre de host**. Por ejemplo, si desea utilizar Melbourne como región, puede seleccionar **San Jose 1** en el menú desplegable **Región** y modificar el campo **Nombre de host** a **mel01.objectstorage.softlayer.net**.
7. Especifique las credenciales de {{site.data.keyword.cos_full_notm}} en el campo **Nombre de usuario**. El formato del nombre de usuario debe ser `object_storage_username:IBM_Cloud_username`. Por ejemplo: **ABC-DE123456-7:user**. Puede encontrar su nombre de usuario de Object Storage en **Almacenamiento > Object Storage** en el [{{site.data.keyword.slportal_full}} ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://control.softlayer.com/){: new_window}.
8. Especifique un **Nombre de grupo** para almacenar los datos. El nombre de grupo es el nombre del contenedor donde desea almacenar los datos en {{site.data.keyword.cos_full_notm}}.
* No modifique el puerto predeterminado a menos que el administrador de la red le indique lo contrario. El campo **Habilitar archivado** tiene como valor predeterminado **No**; pulse **Siguiente**.
9. Especifique la **Señal de solicitud de licencia**, si es necesario, y pulse **Siguiente**.
10. Especifique una **Clave de cifrado**. Puede permitir que AltaVault genere una nueva clave de cifrado o especificar una clave existente que desee utilizar para cifrar y descifrar los datos. Pulse **Siguiente**.
11. Verifique que todos los valores sean correctos y, a continuación, pulse **Finalizar y aplicar**. AltaVault intenta ahora ponerse en contacto con el servicio {{site.data.keyword.cos_full_notm}} utilizando los valores de entrada y los parámetros del asistente de configuración de la nube. Si la conexión falla, revise los valores y asegúrese de tener el acceso adecuado al servicio.
12. Pulse **Salir** después de que se establezca una conexión para volver al panel de control del asistente y pulse **Salir del asistente** para volver a la página de estado del dispositivo AltaVault.
13. Verifique que el *Servicio de optimización de almacenamiento* esté en ejecución y de que su estado sea ready (preparado). **Nota:** es posible que el estado tarde algunos minutos en cambiar a *Ready*.

El dispositivo AltaVault está configurado para comunicarse con el servicio {{site.data.keyword.cos_full_notm}}.

### Configuración del punto de montaje CIFS/SMB en AltaVault
{: #configuring-the-cifs-smb-mount-point-in-altavault}

Es necesario configurar la interfaz **e0b** para crear un punto de montaje CIFS/SMB. Utilice los pasos siguientes para configurar **e0b**.

1. Vaya a **Valores > Interfaces de datos** y expanda la interfaz **e0b**. Marque **Habilitar interfaz de datos** y especifique la **dirección IP, la máscara de subred,** y la **pasarela** que desee utilizar para montar la compartición CIFS/SMB.
2. Deje el valor predeterminado de **MTU** en **1500 bytes**.<br/><br/>Aunque la unidad de transmisión máxima (MTU) predeterminada se establezca en 1500, puede cambiarla a 9000 si utiliza tramas de gran tamaño. **Nota:** es necesario que el host de ESXi y la infraestructura física tengan soporte para tramas de gran tamaño. De forma predeterminada, {{site.data.keyword.cloud_notm}} ya admite un tamaño de MTU de 9000 bytes sin que sean necesarios cambios de configuración.
3. Pulse **Aplicar**. El punto de montaje está preparado para la configuración.
4. Seleccione **Almacenamiento > CIFS > Añadir compartición CIFS** y especifique un nombre exclusivo.
5. Pulse el menú desplegable **Anclar compartición** y seleccione **Sí**. **Nota:** las copias de seguridad de Veeam Backup & Replication pueden fallar en una compartición sin anclar.
6. Especifique una vía de acceso exclusiva para la compartición en el campo **Vía de acceso**. Es preferible utilizar el nombre de compartición como vía de acceso, es decir, si el nombre de compartición es `cifs_share0, especifique /cifs_share0` como vía de acceso.
7. Deseleccione **Permitir el acceso a todos los usuarios** si la seguridad no es un problema. Es preferible incluir en la lista blanca a los clientes que utilicen la compartición CIFS/SMB. De lo contrario, deje **Permitir el acceso a todos los usuarios** seleccionado si la seguridad es importante y pulse **Añadir compartición**.
8. Pulse **Añadir usuario de CIFS** para crear cuentas para usuarios autorizados y complete los campos **Nombre de usuario** y **Contraseña**.
9. Expanda la nueva compartición CIFS y pulse **Añadir un usuario o grupo** para añadir las cuentas de usuario autorizadas.
10. Vaya a **Configuración global de CIFS**, pulse sobre el menú desplegable **Interfaz de escucha**, seleccione **e0b** y pulse **Aplicar**.

El dispositivo AltaVault está configurado para permitir comunicaciones entre el propio dispositivo, {{site.data.keyword.cos_full_notm}} y el sistema que ejecuta Veeam Backup & Replication. Es recomendable que exporte la configuración del dispositivo AltaVault para acelerar futuros despliegues, si es necesario.

Para exportar la configuración del dispositivo AltaVault, siga estos pasos.

1. Pulse **Valores > Asistente de configuración** para acceder al panel de control del asistente en la consola de gestión web del dispositivo AltaVault local.
2. Pulse **Exportar configuración** y pulse **Exportar configuración**.
3. Guarde el archivo de configuración (tarball/.tar) en una ubicación segura.

## Veeam Backup & Replication
{: #veeam-backup-replication-backup}

El software Veeam Backup & Replication proporciona funciones completas de copia de seguridad, réplica y recuperación para las máquinas virtuales y sus datos. La copia de seguridad se puede integrar completamente con un dispositivo de pasarela en la nube AltaVault.

### Suministro de Veeam en un nuevo servidor
{: #provisioning-veeam-on-a-new-server}

Puede solicitar Veeam cuando suministre una nueva VSI o un servidor nativo. Utilice la información siguiente durante el suministro.
  * Veeam solo está disponible con facturación mensual
  * Veeam solo está disponible con un sistema operativo Windows

Para añadir Veeam:
  * En la sección Complementos del sistema, en Complementos específicos del sistema operativo, pulse sobre el separador Veeam y seleccione una de las opciones de Veeam.
  * En la sección Complementos del sistema, bajo Complemento CDP, seleccione cualquier opción adicional de Veeam que desee añadir. <br><br>**Nota**: si selecciona *Veeam Backup and Replication 9.5 Update 3*, tendrá que seleccionar al menos una opción en la lista Complemento CDP.

### Solicitud de Veeam en un servidor existente
{: #ordering-veeam-on-an-existing-server}

Puede añadir Veeam a un servidor existente con el procedimiento de [recarga del sistema operativo](/docs/infrastructure/software?topic=software-reloading-the-os). El servidor debe ejecutar un SO Windows y debe estar configurado para la facturación mensual.

Para añadir Veeam:
1. En la sección Complementos del sistema, en Complementos específicos del sistema operativo, pulse sobre el separador Veeam y seleccione una de las opciones de Veeam.
2. En la sección Complementos del sistema, bajo Complemento CDP, seleccione cualquier opción adicional de Veeam que desee añadir. <br><br>**Nota**: si selecciona *Veeam Backup and Replication 9.5 Update 3*, tendrá que seleccionar al menos una opción en la lista Complemento CDP.

### Despliegue de Veeam Backup & Replication
{: #deploying-veeam-backup-replication}

En el ejemplo se utiliza una versión de prueba de Veeam Backup & Replication Versión 8.

#### Requisitos previos
{: #prerequisites-deploying-veeam-backup-replication}

Antes de continuar con el despliegue, compruebe que se cumplen los requisitos previos siguientes:

* Debe disponer de un dispositivo AltaVault existente configurado para su uso con {{site.data.keyword.cos_full_notm}} y Veeam Backup & Replication.
* Obtenga una copia de Veeam Backup & Replication para entornos de VMware, que es un solo archivo ejecutable. Póngase en contacto con el representante de Veeam para obtener una copia o descargar una [versión de prueba de 30 días ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](http://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}.
* Obtenga un archivo de licencia para su uso con Veeam Backup & Replication. En la mayoría de los casos, este archivo se enviará por correo electrónico a la dirección que haya utilizado para descargar Veeam Backup & Replication. Si no ha recibido este archivo, póngase en contacto con su representante de Veeam.<br/><br/>El archivo de licencia se utiliza para activar todas las funciones de Veeam Backup & Replication. Si no se proporciona este archivo durante la instalación del programa, todas las características y funciones se revertirán a las de la versión de prueba de 30 días.
* Debe disponer de un servidor de copia de seguridad existente, que puede ser interno o externo, con las especificaciones de la Tabla 2. El sistema operativo instalado debe ser una versión de 64 bits.

||Mínimo|Recomendado|
|---|---|---|
|**SO**|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>Windows Server 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>Windows Server 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|
|**Número de núcleos o vCPU**|2|4|
|**Memoria**|4 GB de RAM base más 500 MB para cada trabajo de copia de seguridad simultáneo.|16 GB de RAM base más 4 GB para cada trabajo de copia de seguridad simultáneo.|
|**Espacio de disco**|2 GB para la instalación del producto; 10 GB por cada 100 máquinas virtuales para los datos del catálogo del sistema invitado (datos persistentes).|2 GB para la instalación del producto; 10 GB por cada 100 máquinas virtuales para el catálogo del sistema invitado (datos persistentes).|
|**Red**|LAN de 1 Gbps para la copia de seguridad y la réplica in situ; WAN de 1 Mbps para la copia de seguridad y réplica fuera del sitio.|LAN de 1 Gbps para la copia de seguridad y la réplica in situ; WAN de 1 Mbps para copias de seguridad y réplica fuera del sitio.|
{: caption="Tabla 2. Requisitos del sistema para el servidor de copia de seguridad de Veeam Backup & Replication" caption-side="top"}

#### Instalación de Veeam Backup & Replication
{: #installing-veeam-backup-replication}

Utilice los pasos siguientes para instalar Veeam Backup & Replication en el servidor de copia de seguridad después de que se hayan cumplido todos los requisitos previos.

1. Para iniciar el asistente de configuración, realice una doble pulsación sobre el archivo ejecutable de Veeam y pulse **Veeam Backup & Replication – Instalar**.
2. Pulse **Siguiente** y acepte los términos del acuerdo de licencia.
3. Pulse **Siguiente** y pulse **Instalar** para **Veeam Backup & Replication**.
4. Especifique la ubicación del archivo de licencia que se ha obtenido anteriormente y pulse **Siguiente**.
5. Seleccione los componentes de Veeam Backup & Replication que desee instalar y proporcione la ubicación de instalación en la pantalla **Configuración de Veeam Backup & Replication**. **Veeam Backup & Replication y el catálogo de Veeam Backup** son componentes necesarios. Pulse **Siguiente**. El asistente de configuración realiza una serie de comprobaciones para asegurarse de que están instaladas las infraestructuras de programa necesarias y los componentes que les dan soporte. Si falta alguno, el asistente de configuración le ofrecerá instalarlos automáticamente. Si necesita instalar infraestructuras o componentes que falten, pulse **Instalar**.
6. Verifique que todos los componentes **superan** las comprobaciones de sistema y pulse **Siguiente**.
7. Seleccione la **Cuenta de servicio (usuario)** donde se ejecute el servicio Veeam Backup. **Nota:** la cuenta de servicio predeterminada es la *cuenta LOCAL SYSTEM*. Pulse **Siguiente**.
8. Seleccione la **Instancia de SQL Server** que se utilice para crear y almacenar las bases de datos de Veeam Backup & Replication. <!--Contact your database administrator for more information, if necessary. --> Pulse **Siguiente**.
9. Especifique el **Puerto de servicio de catálogo** y el **Puerto de servicio de Veeam Backup** (los puertos predeterminados son **9393** y **9392**).<!--Contact your network administrator for more information, if necessary.--> Pulse **Siguiente**.
10. Seleccione los directorios donde se almacenarán el catálogo del sistema de archivos invitado (datos persistentes) y la memoria caché de escritura de vPower NFS (datos no persistentes). Pulse **Siguiente**.
11. Compruebe que todos los parámetros y valores sean correctos y pulse **Instalar** para iniciar la instalación. Una vez que finalice la instalación, pulse **Finalizar**.

### Configuración de Veeam Backup & Replication para realizar copias de seguridad
{: #configuring-veeam-backup-replication-for-backups}

Tras instalar Veeam Backup & Replication, estará listo para conectarlo con el host ESXi de vSphere que contiene el dispositivo virtual AltaVault.

1. Inicie Veeam Backup & Replication.
2. En la parte inferior izquierda de la pantalla, pulse **Infraestructura de copia de seguridad > Servidores gestionados**.
3. Pulse **Añadir servidor** y realice una doble pulsación en **VMware vSphere**.
4. Especifique el **Nombre DNS o dirección IP** y la **Descripción** del servidor vCenter y pulse **Siguiente**.
5. Especifique las **Credenciales** de una cuenta local que tenga privilegios de administrador en el servidor vSphere. **Nota:** el nombre de usuario de la cuenta debe tener el formato **DOMAIN\USER** para cuentas de dominio, o el formato **HOST\USER** para cuentas locales. Para añadir una cuenta, pulse **Añadir** y especifique el nombre de usuario y la contraseña de la cuenta.<br/>No cambie el **Puerto de servicios web de VMware predeterminado** durante la instalación de Veeam Backup & Replication a menos que el administrador de red le indique lo contrario.
6. Pulse **Siguiente**. Veeam Backup & Replication se conecta al servidor VMware vSphere. Si el intento de conexión falla, compruebe que la cuenta existe y que tiene privilegios de administrador en el servidor VMware vSphere antes de volver a intentar la conexión.
7. Pulse **Finalizar** en la ventana **Resumen** y verifique que el servidor vSphere se haya añadido correctamente pulsando **Servidores gestionados > VMware vSphere**.

### Adición de un repositorio de copia de seguridad a Veeam Backup & Replication
{: #adding-a-backup-repository-to-veeam-backup-replication}

De forma predeterminada, Veeam Backup & Replication crea un repositorio de copia de seguridad local en la unidad C:\ del servidor de copia de seguridad de Veeam Backup & Replication durante la instalación del programa.

Utilice los pasos siguientes para crear un repositorio de copia seguridad para almacenar todas las copias de seguridad en el dispositivo AltaVault. Si desea utilizar el repositorio de copia seguridad predeterminado, omita los pasos siguientes.

1. En la parte inferior izquierda de la pantalla **Infraestructura de copia de seguridad**, pulse **Infraestructura de copia de seguridad > Repositorios de copia de seguridad > Añadir repositorio**.
2. Especifique un nombre de repositorio exclusivo en el campo **Nombre**. De manera opcional, puede proporcionar una **Descripción**. Pulse **Siguiente**.
3. Seleccione **Carpeta compartida**. La compartición CIFS/SMB que ha creado será la compartición que utilice este repositorio de copia seguridad. Pulse **Siguiente**.
4. Especifique la ubicación de la compartición CIFS/SMB en el dispositivo AltaVault. Para determinar la ubicación, abra un navegador web y escriba la dirección IP del dispositivo AltaVault. Vaya a **Almacenamiento > CIFS** y anote la **Vía de acceso de compartición** de la compartición. **Nota:** esta NO es la misma que la vía de acceso local de la compartición.<br/><br/>El formato de la vía de acceso de compartición es `\\<AltaVault appliance hostname>\<share name>`. Sustituya el nombre de host del dispositivo AltaVault en la vía de acceso de compartición por la dirección IP de la interfaz de red **e0b** (el punto de montaje de la compartición) del dispositivo AltaVault.<br/><br/>Para encontrar la dirección IP de la interfaz **e0b**, pulse **Valores > Interfaces de datos** en la ventana de gestión del dispositivo AltaVault. La vía de acceso de compartición especificada en Veeam Backup & Replication es `\\192.168.50.16\cifs_test2`.
5. Vuelva a Veeam Backup & Replication, especifique la vía de acceso de compartición del punto de montaje en el campo **Carpeta compartida** y pulse **Siguiente**. Veeam Backup & Replication intenta establecer una conexión con el punto de montaje. Si el intento de conexión falla, vuelva atrás y compruebe que los valores de red del dispositivo AltaVault sean correctos antes de intentarlo de nuevo.
6. Especifique un valor para limitar el máximo de tareas simultáneas al número de recursos que estén disponibles. Este valor es el número máximo de tareas que puede enviar un proxy de copia de seguridad a la compartición seleccionada. El número máximo de tareas simultáneas predeterminado es 4. AltaVault recomienda que comience con cinco tareas simultáneas y que aumente o disminuya este valor en la medida en que los recursos lo permitan. Este valor se puede ajustar después de que se cree el repositorio de copia de seguridad.
7. Pulse **Siguiente**.
8. Si es necesario, especifique los **Valores de vPower NFS**. Si se deja sin marcar el recuadro de selección **Habilitar el servidor vPower NFS**, Veeam Backup & Replication utilizará vPower para la recuperación y la verificación de la recuperación. Pulse **Siguiente**.
9. En la pantalla **Revisión**, confirme que todos los valores sean correctos y pulse **Siguiente**.
10. Pulse **Finalizar** para salir del asistente. Ahora puede empezar a realizar copias de seguridad de sus datos.

### Copia de seguridad del entorno
{: #backing-up-the-environment}

Siga estos pasos para crear una copia de seguridad de un entorno virtual completo.

1. En la pantalla **Infraestructura de copia de seguridad**, pulse **Backup & Replication**.
2. En la ventana **Backup & Replication**, pulse **Trabajos > Trabajo de copia de seguridad**.
3. Escriba un nombre exclusivo en el campo **Nombre**. De manera opcional, puede especificar una **Descripción**. Pulse **Siguiente**.
4. Seleccione las máquinas virtuales de las que desee realizar una copia de seguridad pulsando **Añadir objetos** y navegue por la estructura de árbol para seleccionar las máquinas virtuales. Pulse **Añadir** después de seleccionar las máquinas virtuales adecuadas.<br/>Si solo se va a realizar una copia de seguridad de algunas partes específicas de las máquinas virtuales (el disco de inicio), pulse **Exclusiones** y especifique las partes. De lo contrario, pulse **Siguiente**.
5. Seleccione el repositorio de copia seguridad que ha creado utilizando el menú desplegable **Repositorio de copia de seguridad**.

**Nota:** para obtener un rendimiento óptimo, asegúrese de cambiar los valores de deduplicación y compresión de datos. Utilice los pasos siguientes para optimizar el rendimiento.

1. Pulse **Avanzado**, seleccione el separador **Almacenamiento** y deseleccione **Habilitar duplicación de datos en línea**. El rendimiento mejorará debido a que el dispositivo AltaVault realiza la deduplicación a nivel de bloque de las copias de seguridad de Veeam Backup & Replication que pasan a través de él.
2. Seleccione **Ninguno** en el menú desplegable **Nivel de compresión** y seleccione **Destino de LAN** en el menú desplegable **Optimización de almacenamiento**.<br/>**Nota:** si la ubicación de red de la compartición CIFS/SMB está congestionada, dejar **Habilitar deduplicación de datos en línea** marcado podría mitigar los problemas de rendimiento de red, pero a costa de reducir los índices de deduplicación de datos experimentados en el dispositivo AltaVault.
3. Pulse **Siguiente**.
4. Si desea que el proceso y/o la indexación del sistema de archivos de invitado tenga en cuenta la aplicación, marque el recuadro de selección adecuado. Establezca las **Credenciales de SO de invitado** (el nombre de usuario y la contraseña del SO de invitado de las máquinas virtuales de las que se hace copia de seguridad), si es necesario. Pulse **Siguiente**.
5. Marque el recuadro de selección **Ejecutar el trabajo automáticamente** si las copias de seguridad se ejecutan de manera regular y establezca los intervalos que desee. De lo contrario, pulse **Crear** y **Finalizar**.

#### Inicio de una copia de seguridad manual
{: starting-a-manual-backup}
Para iniciar manualmente una copia de seguridad, pulse el botón derecho del ratón sobre el trabajo de copia de seguridad y seleccione **Iniciar**. Como alternativa, seleccione **Activar completa** si desea realizar una nueva copia de seguridad.

**Nota:** Veeam Backup & Replication puede restaurar entornos virtuales a partir de una copia de seguridad. Para obtener más información sobre la restauración de entornos virtuales, consulte el sitio web de [Veeam Backup & Replication ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}.
