---
copyright:
  years: 2014, 2019
lastupdated: "2019-01-15"

subcollection: virtualization

keywords: vmware, ESXi
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:table: .aria-labeledby="caption"}

# Arquitectura avanzada de referencia de un solo sitio de VMware
{: #advanced-single-site-vmware-reference-architecture}

Utilice los pasos siguientes para crear un entorno vSphere avanzado de un solo sitio. Los pasos siguientes definen un despliegue de arquitectura de referencia de {{site.data.keyword.cloud}} utilizando los métodos recomendados de VMware.

La arquitectura de referencia está pensada para suministrar un entorno que utiliza almacenamiento compartido, alta disponibilidad (HA) de VMware y el planificador de recursos distribuidos (DRS) de vSphere, así como un cortafuegos o una pasarela de {{site.data.keyword.cloud_notm}}. La arquitectura de referencia de VMware de un solo sitio es adecuada para la mayoría de los despliegues de producción que se pueden escalar como dictados de carga de trabajo y pueden sustituir una implementación local o ampliarse en un caso de ejemplo de TI híbrido.

## Visión general del entorno
{: #environment-overview}

El entorno de VMware representativo avanzado que se describe, consta de un centro de datos que gestiona dos clústeres separados: de gestión y de capacidad. La configuración se puede establecer utilizando un solo clúster de cuatro nodos en función de los requisitos. El clúster de gestión contiene las máquinas virtuales (VM) siguientes que se utilizan para gestionar la infraestructura:

* Dispositivo VMware vCenter Server 5.5 o 6.0
* Microsoft Windows 2012 R2 Standard con roles de Active Directory y de Sistema de nombres de dominio (DNS)

El clúster de capacidad contiene los recursos y la infraestructura necesarios para crear y ejecutar máquinas virtuales. En cuanto a las redes, el entorno consta de tres VLAN privadas internas y de una única VLAN pública que se utiliza para la comunicación externa. En la tabla 1 se especifican los tipos de VLAN y los nombres de VLAN que se utilizan en todo el entorno.

|Tipo de VLAN|Nombre de VLAN|Descripción|                                                                             |
|---|---|---|
|Privada primaria| Gestión| Asignada para gestionar y acceder a los hosts ESXi físicos y los servidores virtuales.|
|Privada primaria| Almacenamiento| Asignada para gestionar y acceder al almacenamiento compartido conectado a cada host ESXi.|
|Privada primaria| Acceso a máquina virtual | Asignada a máquinas virtuales que se ejecutan en cada host ESXi.|
|Primaria pública | Pública | Asignada a máquinas virtuales u otros dispositivos que requieren acceso desde la red pública.|
<caption>Tabla 1. VLAN primarias</caption>

Para el almacenamiento compartido, puede utilizar OS Nexus QuantaStor, un servidor de almacenamiento compartido de un solo arrendatario, o los servicios compartidos {{site.data.keyword.cloud_notm}} Endurance o Performance. En cualquier caso, el dispositivo de almacenamiento compartido se utiliza para almacenar las máquinas virtuales tanto en el clúster de gestión como en el de capacidad. Para obtener más información sobre las opciones de almacenamiento, consulte [Soluciones de almacenamiento de {{site.data.keyword.cloud_notm}} ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://www.ibm.com/cloud/storage){: new_window}.

El entorno de almacenamiento está configurado para dar soporte a volúmenes NFS.

## Paso 1 Solicitud de las VLAN privada y pública primarias
{: #step-1-ordering-primary-public-and-private-vlans}

**Nota:** las solicitudes de VLAN están sujetas a revisión y aprobación. No hay condiciones ni requisitos específicos impuestos que indiquen que una solicitud de VLAN se apruebe automáticamente. Las solicitudes de VLAN se pueden denegar por diversos motivos.

Se crean cuatro VLAN en este paso: una para gestión, una para almacenamiento, una para las máquinas virtuales y una para el acceso público. Se recomienda crear estas cuatro VLAN antes de solicitar los servidores. La solicitud de las VLAN en primer lugar garantiza que los servidores se ubiquen en las VLAN correctas y en el centro de datos adecuado.

El entorno consta de cinco hosts ESXi (dos para el clúster de gestión y tres para el clúster de capacidad) y un servidor virtual. La VLAN de gestión privada consta de una subred con 16 direcciones IP para darle soporte. La VLAN privada primaria para almacenamiento y tráfico de máquina virtual consta de ocho direcciones, ya que el entorno contiene un único servidor de almacenamiento y las máquinas virtuales utilizan una subred portátil. Si es necesario un rango de subred mayor para la red de gestión, ajuste el rango calculando el número de hosts ESXi y multiplicándolo por 2. Además, asegúrese de especificar el centro de datos para el que se crean estas VLAN.

Tras iniciar sesión en {{site.data.keyword.cloud_notm}}, abra una incidencia de soporte para las VLAN de gestión y de máquina virtual seleccionando **Soporte > Añadir incidencia**. Complete los campos con la información de la **Tabla 2:**.

|Campo|Valor|
|---|---|
|Asunto|Pregunta de red privada|
|Título|Solicitar VLAN|
|Detalles|Suministrar tres VLAN privadas primarias y una VLAN pública primaria. Asociar el esquema de direccionamiento siguiente a cada VLAN: <br/><ul><li>Asociar 1x18 (16 direcciones) para la VLAN privada primaria</li><li>Asociar 2x29 (ocho direcciones) para la VLAN privada primaria</li><li>Asociar 1x29 (ocho direcciones) para la VLAN pública primaria</li></ul>|
|¿Por qué necesito estas VLAN adicionales?|Para colocar hosts, almacenamiento y máquinas virtuales en una red distinta en un entorno VMware.
|¿Necesito VLAN pública y privada?|Privada y pública|
|¿Cuántas VLAN necesito?|Privadas = 3, Públicas = 1|
|¿Cuántas direcciones IP necesito?|8 - 16 para cada VLAN|
|¿Detrás de qué direccionador necesito que estén las VLAN?|No importa que las VLAN estén todas en el mismo pod.|
|¿En qué pod deben estar las VLAN?|Todas van en el mismo pod de <NOMBRE DE CENTRO DE DATOS>.|
<caption>Tabla 2. Información de incidencia de soporte</caption>

Una vez que se han suministrado las VLAN, tome nota de los números de VLAN, el rango de subred y la pasarela, y asígnelos a las redes lógicas de vSphere. Puede utilizar la hoja de trabajo del Apéndice A: Hoja de trabajo de VLAN para registrar la VLAN y la información asociada. Por ejemplo:

|Tipo de VLAN|Número de VLAN|Rango de IP|Pasarela|Finalidad|
|---|---|---|---|---|
|Privada primaria|1101|10.X.Y.Z/28|10.X.Y.1|Gestión|
|Privada primaria|1102|10.A.B.C/29|10.A.B.1|Almacenamiento|
|Privada primaria|1103|10.Q.R.S/29|10.Q.R.1|Máquinas virtuales|
|Primaria pública|2101|75.S.T.U/29|75.S.T.1|Acceso público|
<caption>Tabla 3. Ejemplo de VLAN primaria</caption>

**Nota:** no continúe con el paso siguiente hasta que se suministren las VLAN.

## Paso 2 Solicitud de direcciones IP privadas portátiles
{: #step-2-ordering-portable-private-ip-addresses}

En el Paso 2, se ha realizado una solicitud para crear subredes privadas portátiles para las máquinas virtuales de gestión, el acceso al almacenamiento de kernel de máquina y las máquinas virtuales del clúster de capacidad. Es necesario determinar cuántas direcciones necesita para cada subred de VLAN. En el entorno representativo, solicite las direcciones siguientes:

* VLAN de gestión - 1x8 direcciones /29 – Una dirección para el dispositivo vCenter; una dirección para DNS y Active Directory.
* VLAN de almacenamiento - 1x16 direcciones /28 – Cree dos subredes en la misma VLAN para almacenamiento y dos puertos de kernel de máquina virtual en cada host ESXi utilizando distintas subredes para acceder a los dispositivos almacenamiento compartido.
* VLAN de máquina virtual - 1x32 direcciones /27 – Estas direcciones se utilizan para asignar direcciones IP a las máquinas virtuales en el clúster de capacidad.

Al solicitar una cantidad, asegúrese de tener en cuenta cuántas direcciones IP se necesitarán en los próximos 30 días y en 6 meses. También es importante tener en cuenta que {{site.data.keyword.cloud_notm}} reserva de tres a cuatro direcciones IP por cada bloque de subred portátil. Como resultado, la solicitud de cuatro redes de direcciones IP deja una dirección IP, o cero si el pod tiene soporte para el protocolo de direccionador de espera activa.

Utilice los pasos siguientes para solicitar un bloque de direcciones IP portátiles para cada VLAN para cada subred que desee crear:

1. Abra una ventana de navegador e inicie sesión en {{site.data.keyword.cloud_notm}}.
2. Seleccione **Cuenta > Realizar un pedido**.
3. En la ventana emergente, vaya a **Red > Subredes / IP > Solicitar**.
4. En el menú desplegable, seleccione **Privada portátil**.
5. Seleccione **XX direcciones IP privadas portátiles** y pulse **Continuar**. **Nota:** _XX_ especifica el número de direcciones IP.
6. Seleccione la VLAN con la que asociar el bloque de direcciones IP y pulse **Continuar**.
7. Termine de rellenar la información en pantalla y pulse **Continuar**.

La creación de direcciones IP es bastante rápida y se visualiza seleccionando **Subredes** en **Red > Gestión de IP**. Puede registrar estas direcciones IP en la hoja de trabajo que se encuentra en el Apéndice A: Hoja de trabajo de VLAN.

## Paso 3 Solicitud de un servidor virtual
{: #step-3-ordering-a-virtual-server}

Se suministra un servidor virtual Windows 2012 R2 Standard para su uso como servidor de programas de utilidad para ISO y proporcionar acceso al entorno en este paso.

1. Abra una ventana de navegador e inicie sesión en {{site.data.keyword.cloud_notm}}.
2. Seleccione **Cuenta > Realizar un pedido**.
3. Vaya a **Servidor virtual > Por hora o mensual**.
4. Seleccione el centro de datos adecuado (donde se hayan creado las VLAN) para suministrar el servidor virtual y defina las especificaciones siguientes para cada opción:
  * Instancia de cálculo - 1 x 2,0 GHz núcleos
  * RAM: 4 GB
  * Sistema operativo: Windows Server 2012 R2 Standard Edition (64 bits)
  * Primer disco: 100 GB (SAN)
  * Velocidad de puerto de enlace ascendente: enlaces ascendentes de red pública y privada de 1 Gbps
5. Pulse **Continuar con el pedido** y seleccione las VLAN de fondo y frontal en la pantalla **Resumen de pedido y facturación**. **Nota:** la selección de las VLAN es importante para que el programa de utilidad se pueda colocar en el pod correcto dentro del centro de datos. En el entorno de ejemplo, la VLAN de fondo es la VLAN de gestión (1101) y la VLAN frontal es la VLAN pública (2101)
6. Especifique un nombre de host y un nombre de dominio para el servidor y pulse **Realizar pedido**.

## Paso 4 Solicitud de hosts ESXi y de una pasarela o cortafuegos
{: #step-4-ordering-esxi-hosts-and-gateway-firewall}

Debe solicitar los hosts ESXi, así como la pasarela Brocade vRouter (Vyatta) y el dispositivo de cortafuegos mientras se suministra el servidor virtual. Solicite todos estos servidores al mismo tiempo para que se coloquen en el mismo pod al mismo tiempo. La solicitud de hardware en momentos distintos puede provocar que los hosts y los cortafuegos estén en pods independientes dentro de un centro de datos de {{site.data.keyword.cloud_notm}}.

### Hosts ESXi
{: #esxi-hosts}

En cada host ESXi solicitado para el entorno, el sistema operativo es VMware ESXi 5.5. Si desea utilizar licencias de vSphere de {{site.data.keyword.cloud_notm}}, se efectúan cargos mensuales según el uso.

Otra opción es instalar ESXi utilizando su propio ISO. Puede encontrar instrucciones para este proceso en [Instalación de VMware vSphere ESXi a través de la consola remota y el soporte virtual](/docs/infrastructure/vmware?topic=VMware-installing-vsphere-esxi#installing-vsphere-esxi). Si desea instalar ESXi utilizando su propio ISO, asegúrese de seleccionar **Sin sistema operativo** como sistema operativo de los hosts de gestión y capacidad durante el proceso de solicitud.

**Nota:** esta implementación requiere una licencia de Enterprise Plus para utilizar conmutadores virtuales distribuidos de vSphere. Si la licencia no es válida para Enterprise Plus, deberá utilizar la licencia de programa de proveedor de servicios de VMware (VSPP) proporcionada por {{site.data.keyword.cloud_notm}}.

### Solicitud de hosts de gestión
{: #ordering-management-hosts}

Utilice los pasos siguientes para solicitar los servidores de host de gestión.

1. Abra una ventana de navegador e inicie sesión en {{site.data.keyword.cloud_notm}}.
2. Seleccione **Cuenta > Realizar un pedido**.
3. Seleccione **Servidores nativos > Mensual**.
4. Elija un servidor adecuado que se ajuste a los requisitos del clúster de gestión en la pantalla de la lista de servidores. **Nota:** como mínimo, ESXi 5.5 requiere un procesador de doble núcleo, 4 GB de RAM y un controlador Ethernet de 1 Gb.
5. Seleccione el centro de datos adecuado (donde se hayan creado las VLAN) para suministrar los servidores ESXi y defina las especificaciones siguientes para cada opción:
  * Cantidad: 2
  * RAM: 32 GB
  * Sistema operativo: VMware ESXi 5.5 ('Sin sistema operativo' si utiliza [Instalación de VMware vSphere ESXi a través de la consola remota y el soporte virtual](/docs/infrastructure/vmware?topic=VMware-installing-vsphere-esxi#installing-vsphere-esxi))
  * Discos duros: discos 1 y 2: 500 GB SATA en RAID 1
  * Ancho de banda público: solo red privada> 0 GB de ancho de banda
  * Velocidades de puerto de enlace ascendente: enlaces ascendentes a 10 Gbps de redes privadas redundantes
6. Pulse **Continuar su pedido**.
7. Pulse **Añadir servidor** para empezar a añadir hosts ESXi para el clúster de capacidad al pedido.

### Solicitud de hosts de capacidad
{: #ordering-capacity-hosts}

1. Elija un servidor adecuado que se ajuste a los requisitos de los hosts del clúster de capacidad en la pantalla de la lista de servidores. **Nota:** como mínimo, ESXi 5.5 requiere un procesador de doble núcleo, 4 GB de RAM y un controlador Ethernet de 1 GB.
2. Seleccione el centro de datos adecuado (donde se hayan creado las VLAN) para suministrar los servidores ESXi y defina las especificaciones siguientes para cada opción:
  * Cantidad: 3
  * RAM: 128 GB
  * Sistema operativo: VMware ESXi 5.5 ('Sin sistema operativo' si utiliza [Instalación de VMware vSphere ESXi a través de la consola remota y el soporte virtual](/docs/infrastructure/vmware?topic=VMware-installing-vsphere-esxi#installing-vsphere-esxi))
  * Disco duro: discos 1 y 2: 500 GB SATA en RAID 1
  * Ancho de banda público: solo red privada > 0 GB de ancho de banda
  * Velocidades de puerto de enlace ascendente: enlaces ascendentes a 10 Gbps de redes privadas redundantes
3. Pulse **Continuar su pedido**.

### Finalización de la configuración
{: #completing-configuration}

Ahora, tiene hosts ESXi en el carro de la compra. Para que los dispositivos se suministren correctamente, es necesario asignar la VLAN pública (si procede), la VLAN privada, el nombre de host y el dominio a los dispositivos.

1. Asigne las VLAN siguientes y cree nombres de host para los dispositivos.

* Hosts ESXi – VLAN de fondo: (1101)
* VLAN de fondo: (1101)
* VLAN frontal: (2101)

2. Seleccione el método de pago, acepte los términos y condiciones y pulse **Finalizar su pedido**. **Importante:** no continúe con el paso 5 hasta que se suministren los servidores y sean accesibles a través de VPN o del servidor virtual que haya solicitado en el paso anterior.

## Paso 5 Conexión troncal de VLAN en conmutadores BCS
{: #step-5-trunking-vlans-on-bcs-switches}

De forma predeterminada, los puertos se colocan en los conmutadores de fondo del cliente (es decir, el conmutador de red privada en un pod, como un conmutador de fondo de cliente (BCS)) en modalidad de acceso. Como resultado, debe realizar una conexión troncal con los puertos que están asociados a los hosts ESXi de manera que los hosts puedan acceder al almacenamiento y que las máquinas virtuales se puedan comunicar en la red privada.

Para poder abrir la incidencia para realizar la conexión troncal de las VLAN, debe determinar primero qué interfaces de red están en la red privada. Para determinar la interfaz, vaya a los **Detalles de dispositivo** de cada servidor ESXi y acceda a **Red > Privada**. En este entorno, `eth0` y `eth2` son los adaptadores de red privada.

Además de la conexión troncal de las VLAN para los hosts ESXi, también debe desvincular las NIC para los hosts de gestión y capacidad. Debe desvincular las NIC debido a que LACP (Link Aggregation Control Protocol) no es compatible con varias vías iSCSI por software.

Para realizar la conexión troncal de las VLAN y desvincular las NIC, necesita abrir una incidencia siguiendo estos pasos:

1. Abra una ventana de navegador e inicie sesión en {{site.data.keyword.cloud_notm}}.
2. Seleccione **Soporte, Añadir incidencia**.
3. Especifique la información siguiente:
  * Asunto: Pregunta de red privada
  * Título: Conexión troncal de VLAN y desvincular NIC
  * Detalles: Conexión troncal de las VLAN `<Management VLAN>`, `<Storage VLAN>` y `<VM VLAN>` en el par de NIC eth0 y eth2 para los hosts siguientes [listar cada host ESXi]. Además, desvincular (eliminar LACP) las NIC privadas (eth0 y eth2) en los servidores siguientes: [listar cada host ESXi]
4. Pulse **Añadir incidencia**.

Asegúrese de cambiar las VLAN designadas en <> por sus VLAN reales

## Paso 6 Configuración de la red de los hosts de gestión
{: #step-6-configuring-management-host-networking}

Después de que se suministren los servidores y de que se haya establecido la conexión troncal de las VLAN, debe configurar la red para los hosts del clúster de gestión. Para esta configuración, utilice conmutadores estándar de vSphere para el clúster de gestión. Excepto para los grupos de puertos de vMotion y de tolerancia a errores, utilice direcciones IP portátiles para configurar los grupos de puertos de kernel de máquina virtual. **Nota:** puede utilizar su propio esquema de IP para vMotion y para tolerancia a errores, ya que no es necesario direccionar el tráfico. No obstante, todos los hosts deben estar en la misma subred que los demás hosts del clúster para poder utilizar las funciones de vMotion y de tolerancia a errores. Si es necesario direccionar la subred, se recomienda utilizar direcciones IP portátiles de {{site.data.keyword.cloud_notm}}.

Para configurar los grupos de puertos, es necesario instalar el cliente vSphere en el servidor virtual de programas de utilidad o la estación de trabajo que accede a los hosts a través de la VPN de gestión de {{site.data.keyword.cloud_notm}}.

1. Abra una ventana de navegador e inicie sesión en {{site.data.keyword.cloud_notm}}.
2. Tras conectar la VPN de {{site.data.keyword.cloud_notm}} o el servidor de programas de utilidad, inicie el cliente vSphere.
3. Especifique la dirección IP, el nombre de usuario y la contraseña de uno de los hosts ESXi de gestión. (Puede encontrar la contraseña de root del host ESXi seleccionando **Detalles de dispositivo > Contraseñas**.
4. Vaya a **Configuraciones > Redes** y cree o modifique los grupos de puertos siguientes en el conmutador estándar de vSphere (muy probablemente vSwitch0).

Realice estos pasos para cada host del clúster de gestión.

### Propiedades de vSwitch0
{: #vswitch0-properties}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabla 4. Propiedades de vSwitch0</caption>
<tbody>
<tr><th>Adaptador de red</th><th>vmnicX y vmnicY</th></tr>
<tr><td>Equilibrio de carga</td><td>Direccionador basado en el ID de puerto virtual de origen</td></tr>
<tr><td>Adaptadores activos</td><td>vmnicX y vmnicY</td></tr>
</tbody>
</table>

### Editar el grupo de puertos de máquina virtual existente
{: #edit-existing-virtual-machine-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabla 5. Grupo de puertos vmPG-Management</caption>
<tbody>
<tr><th>Etiqueta de red</th><th>*vmPG-Management*</th></tr>
</tbody>
</table>

### Editar el grupo de puertos de kernel de máquina virtual existente
{: #edit-existing-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabla 6. Grupo de puertos vmkPG-Management</caption>
<tbody>
<tr><th>Etiqueta de red</th><th>*vmkPG-Management*</th></tr>
</tbody>
</table>

### Añadir el grupo de puertos de kernel de máquina virtual de vMotion
{: #add-vmotion-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabla 7. Grupo de puertos de vMotion</caption>
<tbody>
<tr><th>Tipo de conexión</th><th>VMKernel</th></tr>
<tr><td>Etiqueta de red</td><td>*vmkPG-vMotion*</td></tr>
<tr><td>ID de VLAN</td><td>*&lt;VLAN de almacenamiento&gt; (1102)*</td></tr>
<tr><td>Tráfico de vMotion</td><td>Habilitado</td></tr>
<tr><td>Dirección IP</td><td>*172.16.10.X/24*<br/><br/>*Dirección definida por el usuario que puede ser una subred distinta. Asegúrese de que las demás direcciones de vMotion en cada host estén en la misma subred.*</td></tr>
<tr><td>Máscara de subred</td><td>255.255.255.0</td></tr>
</tbody>
</table>

### Añadir el grupo de puertos de kernel de máquina virtual de tolerancia a errores
{: #add-fault-tolerance-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabla 8. Grupo de puertos de FT (tolerancia a errores)</caption>
<tbody>
<tr><th>Tipo de conexión</th><th>Kernel de máquina virtual</th></tr>
<tr><td>Etiqueta de red</td><td>*vmkPG-FT*</td></tr>
<tr><td>ID de VLAN</td><td>*&lt;VLAN de almacenamiento&gt; (1102)*</td></tr>
<tr><td>Registro de tolerancia a errores</td><td>Habilitado</td></tr>
<tr><td>Dirección IP</td><td>*172.16.20.X/24*<br/><br/>*Dirección definida por el usuario que puede ser una subred distinta si es necesario. Asegúrese de que las demás direcciones de FT en cada host están en la misma subred.*</td></tr>
<tr><td>Máscara de red</td><td>255.255.255.0</td></tr>
</tbody>
</table>

### Añadir el grupo de puertos de kernel de máquina virtual de almacenamiento
{: #add-storage-vm-kernel-port-group}

Actualice la sección **Notas** de cada dirección IP portátil con el nombre del host y el puerto de kernel de máquina virtual asignados. La sección **Notas** puede encontrarse accediendo al {{site.data.keyword.slportal_full}} y seleccionando **Red > Gestión de IP > Subredes > [Subred]**.

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabla 9. Grupo de puertos de almacenamiento</caption>
<tbody>
<tr><th>Tipo de conexión</th><th>Kernel de máquina virtual</th></tr>
<tr><td>Etiqueta de red</td><td>*vmkPG-Storage*</td></tr>
<tr><td>ID de VLAN</td><td>*&lt;VLAN de almacenamiento&gt; (1102)*</td></tr>
<tr><td>Dirección IP</td><td>*Dirección privada portátil*<br/><br/>*Una dirección IP seleccionada entre las direcciones privadas portátiles vinculadas a la VLAN de almacenamiento.*</td></tr>
<tr><td>Máscara de red</td><td>*Máscara de subred asociada al rango de IP*</td></tr>
</tbody>
</table>

### Añadir grupo de puertos de direcciones públicas
{: #add-public-address-port-group}

Actualice la sección **Notas** de cada dirección IP portátil con el nombre del host y el puerto de kernel de máquina virtual asignados. La sección **Notas** puede encontrarse accediendo al {{site.data.keyword.slportal}} y seleccionando **Red > Gestión de IP > Subredes > [Subred]**.

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabla 10. Grupo de puertos público</caption>
<tbody>
<tr><th>Tipo de conexión</th><th>Máquina virtual</th></tr>
<tr><td>Etiqueta de red</td><td>vmPG-Public</td></tr>
<tr><td>ID de VLAN</td><td>&lt;VLAN pública primaria&gt; (por ejemplo, 2101)</td></tr>
</tbody>
</table>

## Paso 7 Descargar o actualizar imágenes ISO y el dispositivo virtual vCenter
{: #step-7-download-or-upload-iso-images-and-vcenter-virtual-appliance}

Ahora, el entorno está preparado para desplegar el dispositivo virtual VMware vCenter e instalar una máquina virtual para DNS, Active Directory o BIND. No obstante, antes del despliegue, necesita descargar las imágenes. Para descargar las imágenes, realice una conexión de escritorio remota con el servidor virtual suministrado anteriormente y descargue las imágenes adecuadas en el servidor virtual del entorno:

* Active Directory / Windows DNS: imagen ISO de Windows Server 2008R2/Windows Server 2012 (su soporte con licencia)
* Linux BIND: [Imagen de instalación de CentOS ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://isoredirect.centos.org/centos/6/isos/x86_64/){: new_window}
* [Dispositivo virtual vCenter ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://my.vmware.com/web/vmware/info/slug/datacenter_cloud_infrastructure/vmware_vsphere/5_5){: new_window} (requiere una suscripción válida a VMware)

Tras descargar el ISO, debe cargar la imagen en el almacén de datos de un host de gestión para que se pueda adjuntar a una máquina virtual. Utilice el cliente vSphere para conectarse a un host de gestión y cree un directorio ISO en el almacén de datos local.

## Paso 8 Despliegue de DNS
{: #step-8-deploying-dns}

Necesita desplegar una máquina virtual (en un host de gestión) e instalar servicios DNS. Utilice el cliente vSphere tradicional para crear una máquina virtual en el host ESXi de gestión donde se encuentre el ISO de Windows o Linux. Conecte el ISO adecuado (Windows o CentOS) para desplegar un servidor DNS en la máquina virtual. Asegúrese de asociar la máquina virtual con el grupo de puertos de máquina virtual (vmpg) o grupo de puertos de gestión que se haya creado en un paso anterior.

Una vez que se haya instalado la máquina virtual, asigne una dirección IP y una pasarela predeterminada del grupo de subred privado portátil. Si utiliza la hoja de trabajo de VLAN del Apéndice A, la subred de máquina virtual de gestión. Actualice la sección **Notas** de cada dirección IP portátil con el nombre de la máquina virtual asignada. La sección **Notas** puede encontrarse accediendo al {{site.data.keyword.slportal}} y seleccionando **Red > Gestión de IP > Subredes > [Subred]**.

Aunque está más allá del ámbito de este documento detallar los pasos necesarios para habilitar DNS, se proporcionan las siguientes directrices:

1. Establezca **Reenvío de DNS** en los hosts DNS locales de service.softlayer.com:
  * `rs1.service.softlayer.com 10.0.80.11`
  * `rs2.service.softlayer.com 10.0.80.12`
2. Tras configurar DNS, cree una zona DNS local (dal06.mycompany.local) y una zona de búsqueda inversa para todas las subredes primarias y portátiles que se suministran.
3. Añada un registro de A HOST para cada dirección IP de gestión del host (vmk0 en vmkPG-management).
4. Añada un registro de A HOST de la subred privada portátil vinculada a la VLAN de gestión para el dispositivo virtual vCenter.
5. Actualice la sección **Notas** de la subred de IP portátil que haya asignado a vCenter.

Para obtener más información, consulte los enlaces siguientes:
* [DNS de Windows y Active Directory ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://social.technet.microsoft.com/wiki/contents/articles/12370.step-by-step-guide-for-setting-up-a-windows-server-2012-domain-controller.aspx){: new_window}.
<!--* [CentOS BIND ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.centos.org/docs/2/rhl-rg-en-7.2/s1-bind-configuration.html){: new_window}.-->

## Paso 9 Despliegue y configuración del dispositivo virtual vCenter
{: #step-9-deploying-and-configuring-vcenter-virtual-appliance}

Ahora que DNS está configurado, puede desplegar y configurar el dispositivo del servidor vCenter. Para desplegar el dispositivo, siga estos pasos:

1. Conéctese mediante una conexión remota de escritorio al servidor virtual y abra el cliente vSphere.
2. Conéctese al host de gestión y seleccione **Archivo, Desplegar plantilla OVF**.
3. Siga los pasos del asistente para completar el despliegue.

<!--For more information about OVF Template deployment, see [VMware documentation ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://pubs.vmware.com/vsphere-55/index.jsp?topic=%2Fcom.vmware.vsphere.hostclient.doc%2FGUID-6C847F77-8CB2-4187-BD7F-E7D3D5BD897B.html&resultof=%22thick%22%20%22thin%22%20){: new_window}.-->

Debido a que no hay ningún servidor DHCP (Protocolo de configuración dinámica de hosts) disponible para asignar al dispositivo virtual vCenter una dirección IP tras el encendido, debe utilizar la consola de root para configurar el dispositivo. **Nota:** aparecerá un mensaje `NO NETWORKING DETECTED` (sin conexión de red detectada) en la consola del dispositivo virtual vCenter.

Siga estos pasos para configurar el dispositivo:

1. Inicie sesión en la consola con el **Nombre de usuario** 'root' y la **Contraseña** 'vmware'.
2. Ejecute `/opt/vmware/share/vami/vami_config_net` y siga las indicaciones del asistente de texto para configurar las propiedades de IP, subred y DNS. Recuerde utilizar la dirección IP del servidor DNS o BIND que se ha creado en **Paso 8: Desplegar DNS**.
3. Guarde los valores de red, salga de la consola y abra un navegador en el servidor virtual.
4. Vaya a la dirección IP que ha proporcionado en el dispositivo virtual vCenter (VCVA) con el puerto 5480 añadido<!-- (https://:5480)-->.
5. Acepte el EULA en el asistente, responda a la pregunta del **Programa de mejora de la experiencia de cliente** y seleccione **Configurar opciones, Establecer configuración personalizada**.
6. Pulse **Siguiente** y especifique los valores siguientes:
<table border="1" cellpadding="0" cellspacing="0"><caption>Tabla 11. Asistente de configuración de VCVA</caption><tbody><tr><th>Menú del asistente</th><th>Opción</th><th>Valor</th></tr><tr><td>Valores de base de datos</td><td>Tipo de base de datos</td><td>incorporado</td></tr><tr><td>Valores de SSO</td><td>Tipo de despliegue de SSO</td><td>incorporado</td></tr><tr><td>Valores de SSO</td><td>Nueva contraseña de administrador *(para administrator@vsphere.local)*</td><td>&lt;Especifique una contraseña&gt;</td></tr><tr><td>Valores de SSO</td><td>Vuelva a escribir la contraseña</td><td>&lt;Especifique la misma contraseña que ha utilizado anteriormente&gt;</td></tr><tr><td>Sincronización de hora</td><td>Sincronización NTP</td><td>servertime.service.softlayer.com</td></tr></tbody></table>
7. Pulse **Iniciar**. Se configura el VCVA.
8. Cambie la contraseña de root utilizando las opciones de **Administrador**.
9. Cierre la sesión de la página web de configuración del VCVA.
10. Vaya al cliente web de vSphere especificando la dirección IP del VCVA con el sufijo `9443/vsphere-client`<!-- (https://:9443/vsphere-client)-->.
11. Inicie sesión como root y seleccione **Administración, Licencias**.
12. Especifique su licencia de VMware vCenter.

## Paso 10 Crear un conmutador virtual distribuido y clústeres de vCenter
{ #step-10-create-vcenter-clusters-and-distributed-virtual-switch}

Ahora que se ha configurado VCVA y que tiene licencia, puede crear el centro de datos y las construcciones de clúster y conmutadores virtuales distribuidos para el clúster de capacidad.

### Creación del centro de datos y los clústeres
{: #creating-data-center-and-clusters}

1. En el cliente vSphere, vaya a la pantalla **Inicio**.
2. Seleccione **Iniciación** y pulse **pulse aquí**.
3. Pulse **Crear centro de datos**.
4. Especifique un nombre de centro de datos (en el entorno de ejemplo es el centro de datos Toronto 01. Utilice `Toronto 01` como nombre de centro de datos).
5. Una vez que se haya creado el centro de datos, pulse **Crear un clúster** en la página **Iniciación**.
6. Denomine el primer clúster como `Gestión`. Deje las demás opciones sin seleccionar y pulse **Aceptar**.
7. Cree otro clúster cuando haya terminado utilizando el mismo proceso que para el clúster de gestión.

Ahora puede añadir los hosts de gestión y de capacidad en los clústeres de gestión y de capacidad, utilizando **Añadir un host**. Asegúrese de utilizar el nombre de dominio completo (FQDN) del servidor en lugar de la dirección IP, de manera que se utilice DNS al añadir cada host.

Tras añadir los hosts ESXi a vCenter, es posible que aparezcan un par de mensajes de aviso acerca de la habilitación del shell y de SSH en cada host ESXi. Para suprimir estos avisos, pulse **Suprimir aviso > Sí** en la ventana emergente. Si el enlace Suprimir avisos no está presente, siga estos pasos:

1. Vaya al separador **Gestionar** del host ESXi.
2. Seleccione **Valores** y pulse **Valores del sistema avanzados**.
3. Busque la clave **UserVars.SupressShellWarning** y cambie el valor a **1**.
4. Pulse **Aceptar**.

Después de añadir los hosts de gestión y de capacidad a sus respectivos clústeres, acceda a cada host y configure DNS y NTP. Para configurar DNS, siga estos pasos:

1. Pulse sobre un host y seleccione **Gestionar > Redes**.
2. Seleccione la pila de sistema predeterminada (**Configuración TCP/IP**) y pulse sobre el icono de lápiz.
3. Especifique la dirección IP del servidor DNS que haya creado anteriormente, así como el nombre de host y de dominio.

Para los valores de NTP:

1. Vaya a **Gestionar, Valores, Configuración de hora**.
* Especifique `servertime.service.softlayer.com` como servidor NTP.
* Establezca la **Política de inicio del servicio NTP** en `Iniciar y detener con el host`.

***Creación de un conmutador virtual distribuido para los hosts de capacidad***

1. Utilice el cliente web de vSphere para acceder a **Redes** y pulse el botón derecho del ratón sobre el nombre del centro de datos.
2. Seleccione **Nuevo conmutador distribuido**.
3. Defina un nombre para el conmutador distribuido y pulse **Siguiente**.
4. Seleccione la versión adecuada del conmutador distribuido y pulse **Siguiente**.
5. En la pantalla **Editar valores**, especifique `2` como número de enlaces ascendentes y borre la selección de **Crear un grupo de puertos predeterminado**.
6. Pulse **Siguiente > Finalizar** y cree el conmutador virtual distribuido.

***Cree grupos de puertos para el conmutador virtual distribuido***

Ahora que el conmutador virtual distribuido está presente, debe crear grupos de puertos para vMotion, tolerancia a errores, máquinas virtuales y almacenamiento.

***Creación del grupo de puertos de gestión dvpg-Private-VM***

1. Vaya a la sección Redes utilizando el cliente web de vSphere.
2. Pulse el botón derecho del ratón sobre el conmutador virtual distribuido (DVS).
3. Pulse **Nuevo grupo de puertos distribuidos…** y especifique la información de la tabla 14 para el primer grupo de puertos.
4. Deje los valores predeterminados para las opciones que no se especifiquen en la tabla.

| Menú de nuevo grupo de puertos distribuido | Campo | Valor |
| --- | --- | --- |
| Nombre y ubicación | Nombre | dvpg-Private-VM Management |
| Configurar valores | Avanzado | Marcar Personalizar configuración de políticas predeterminadas |
| Configurar políticas (agrupación y migración tras error)| Equilibrio de carga| Direccionamiento basado en carga de NIC física |
| Configurar políticas (agrupación y migración tras error)| Orden de migración tras error| Enlaces ascendentes activos: Enlace ascendente 1 y Enlace ascendente 2 |
<caption>Tabla 12. Grupo de puertos de gestión de máquina virtual de DVS</caption>

Tras crear el primer grupo de puertos, cree los grupos de puertos restantes con las opciones de configuración siguientes (de la Tabla 15 a la Tabla 19).

***Crear el grupo de puertos dvpg-Private-vMotion***

|Menú de nuevo grupo de puertos distribuido|Campo|Valor|
|---|---|---|
|Nombre y ubicación|Nombre|dvpg-Private-vMotion|
|Configurar valores|Tipo de VLAN|VLAN|
|Configurar valores|ID de VLAN|&lt;VLAN de almacenamiento&gt;|
|Configurar valores|Avanzado|Marcar Personalizar configuración de políticas predeterminadas|
|Configurar políticas (agrupación y migración tras error)|Equilibrio de carga|Direccionamiento basado en carga de NIC física|
|Configurar políticas (agrupación y migración tras error)|Orden de migración tras error|Enlaces ascendentes activos: Enlace ascendente 1 y Enlace ascendente 2|
<caption>Tabla 13. Grupo de puertos de vMotion de DVS</caption>

***Crear el grupo de puertos dvpg-Private-Fault Tolerance***

|Menú de nuevo grupo de puertos distribuido|Campo|Valor|
|---|---|---|
|Nombre y ubicación|Nombre|dvpg-Private-Fault Tolerance|
|Configurar valores|Tipo de VLAN|VLAN|
|Configurar valores|ID de VLAN|&lt;VLAN de almacenamiento&gt;|
|Configurar valores|Avanzado|Marcar Personalizar configuración de políticas predeterminadas|
|Configurar políticas (agrupación y migración tras error)|Equilibrio de carga|Direccionamiento basado en carga de NIC física|
|Configurar políticas (agrupación y migración tras error)|Orden de migración tras error|Enlaces ascendentes activos: Enlace ascendente 1 y Enlace ascendente 2|
<caption>Tabla 14. Grupo de puertos de FT de DVS</caption>

***Crear el grupo de puertos dvpg-Private-VM Access***

|Menú de nuevo grupo de puertos distribuido|Campo|Valor|
|---|---|---|
|Nombre y ubicación|Nombre|dvpg-Private-VM Access|
|Configurar valores|Tipo de VLAN|VLAN|
|Configurar valores|ID de VLAN|&lt;VLAN de máquina virtual&gt;|
|Configurar valores|Avanzado|Marcar Personalizar configuración de políticas predeterminadas|
|Configurar políticas (agrupación y migración tras error)|Equilibrio de carga|Direccionamiento basado en carga de NIC física|
|Configurar políticas (agrupación y migración tras error)|Orden de migración tras error|Enlaces ascendentes activos: Enlace ascendente 1 y Enlace ascendente 2|
<caption>Tabla 15. Grupo de puertos de acceso a máquina virtual de DVS</caption>

***Crear dvpg-Private-Storage***

|Menú de nuevo grupo de puertos distribuido|Campo|Valor|
|---|---|---|
|Nombre y ubicación|Nombre|dvpg-Private-Storage Path A|
|Configurar valores|Tipo de VLAN|VLAN|
|Configurar valores|ID de VLAN|&lt;VLAN de almacenamiento&gt;|
|Configurar valores|Avanzado|Marcar Personalizar configuración de políticas predeterminadas|
|Configurar políticas (agrupación y migración tras error)|Equilibrio de carga|Direccionamiento basado en carga de NIC física|
|Configurar políticas (agrupación y migración tras error)|Orden de migración tras error|Enlaces ascendentes activos: Enlace ascendente 1 y Enlace ascendente 2|
<caption>Tabla 16. Grupo de puertos de almacenamiento de DVS</caption>

***Crear dvpg-Primary-Public***

|Menú de grupo de puertos distribuido|Campo|Valor|
|Nombre y ubicación|Nombre|dvpg-Private-Storage|
|Configurar valores|Tipo de VLAN|VLAN
|Configurar valores|ID de VLAN|&lt;VLAN pública primaria&gt;|
|Configurar valores|Avanzado|Marcar Personalizar configuración de políticas predeterminadas|
|Configurar políticas (agrupación y migración tras error)|Equilibrio de carga|Direccionamiento basado en carga de NIC física|
|Configurar políticas (agrupación y migración tras error)|Orden de migración tras error|Enlaces ascendentes activos: Enlace ascendente 1 y Enlace ascendente 2|
<caption>Tabla 17. Grupo de puertos de vía B de almacenamiento de DVS</caption>

## Paso 11 Migración de hosts ESXi del clúster de capacidad al conmutador virtual distribuido
{: #step-11-migrating-esxi-hosts-in-capacity-cluster-to-distributed-virtual-switch}

Ahora que se han añadido hosts de capacidad al clúster de capacidad, puede migrar la configuración de conmutador virtual estándar al conmutador virtual distribuido que ha creado en *Paso 10: Crear un conmutador virtual distribuido y clústeres de vCenter*. La migración se realiza para un host y, más adelante, se aplica un perfil de host para configurar el resto del clúster.

Antes de comenzar a añadir adaptadores de VMkernel, asigne las vmnics a los enlaces ascendentes en el conmutador virtual distribuido.

1. Pulse **Listas de inventario de vCenter, Conmutadores distribuidos**.
2. Seleccione el conmutador distribuido adecuado para los hosts de capacidad.
3. Pulse **Añadir y gestionar hosts** en la página **Iniciación**.
4. Utilice los valores siguientes para añadir enlaces ascendentes y migrar el VMkernel existente que están asociados con la gestión del host.
  - <table border="1" cellpadding="0" cellspacing="0"><caption>Tabla 18. DVS - Añadir hosts</caption><tbody><tr><th>Menú</th><th>Campo</th><th>Valor</th></tr><tr><td>Seleccionar tarea</td><td>Seleccionar tareas</td><td>Añadir hosts</td></tr><tr><td>Seleccionar hosts</td><td>Pulse **Nuevos hosts**</td><td>Pulse el host de capacidad</td></tr><tr><td>Seleccionar tareas de adaptador de red</td><td>Seleccionar tareas de adaptador de red</td><td>Seleccionar Gestionar adaptadores físicos y Gestionar adaptadores de VMkernel</td></tr></tbody></table>
5. Seleccione una de las vmnics privadas y pulse **Gestionar adaptadores de red físicos > Asignar enlace ascendente**.
6. Seleccione `uplink1` en la pantalla emergente y pulse **Aceptar**.
7. Repita los pasos para la otra vmnic privada y asígnela a `uplink2`.
8. Pulse **Siguiente**, resalte el adaptador de VMkernel vmk0 y pulse **Asignar grupo de puertos**.
9. Seleccione **dvpg-Private-VM Management** en la ventana emergente y pulse **Aceptar**.
10. Pulse **Siguiente** dos veces y, a continuación, pulse **Finalizar** para completar la migración al conmutador virtual distribuido. **Nota:** es posible que pierda brevemente la conectividad de red con el host. La conexión se volverá a establecer rápidamente.

Tras migrar el adaptador vmk0 al conmutador virtual distribuido, puede añadir kernels de máquina virtual a cada grupo de puertos del DVS.

11. Pulse **Gestionar > Redes** en el host dentro de vCenter.
12. Vaya a **Adaptadores de kernel de máquina virtual > Añadir red de host** y añada los adaptadores de kernel de máquina virtual que se encuentran en las tablas 19 y 21. Para añadir estos adaptadores, migre al menú "Adaptadores de kernel de máquina virtual" que se encuentra en el separador **Gestionar > Redes** del host dentro de vCenter. A continuación, pulse el icono "Añadir red de host" y añada los adaptadores de kernel de máquina virtual siguientes.

***Añadir vmk1 para vMotion***

|Menú|Campo|Valor|
|---|---|---|
|Seleccionar tipo de conexión|Seleccionar tipo de conexión|Adaptador de red de VMKernel|
|Seleccionar dispositivo de destino|Seleccionar un grupo de puertos distribuido existente|dvpg-Private-vMotion|
|Seleccionar tareas de adaptador de red|Seleccionar tareas de adaptador de red|Seleccionar **Gestionar adaptadores físicos** y **Gestionar adaptadores de kernel de máquina virtual**|
|Propiedades de puerto|Habilitar servicios|Marcar Tráfico de vMotion|
|Valores de IPv4|Dirección IPv4|*172.16.10.X/24*<br/><br/>*Esta es una dirección definida por el usuario y puede ser una subred distinta si es necesario. Asegúrese de que las demás direcciones de vMotion que están en cada host estén en la misma subred.*|
|Valores de IPv4|Máscara de subred|255.255.255.0|
<caption>Tabla 19. Red de hosts vMotion</caption>

***Añadir vmk2 para tolerancia a errores***

|Menú|Campo|Valor|
|---|---|---|
|Seleccionar tipo de conexión|Seleccionar tipo de conexión|Adaptador de red de VMKernel|
|Seleccionar dispositivo de destino|Seleccionar un grupo de puertos distribuido existente|dvpg-Private-Fault Tolerance|
|Seleccionar tareas de adaptador de red|Seleccionar tareas de adaptador de red|Seleccionar **Gestionar adaptadores físicos** y **Gestionar adaptadores de VMKernel**|
|Propiedades de puerto|Habilitar servicios|Marcar Registro de tolerancia a errores|
|Valores de IPv4|Dirección IPv4|*172.16.20.X/24*<br/><br/>*Esta es una dirección definida por el usuario y puede ser una subred distinta si es necesario. Asegúrese de que las demás direcciones de FT que están en cada host estén en la misma subred.*|
|Valores de IPv4|Máscara de subred|255.255.255.0|
<caption>Tabla 20. Red de hosts de tolerancia a errores</caption>

***Añadir vmk3 para almacenamiento***

|Menú|Campo|Valor|
|---|---|---|
|Seleccionar tipo de conexión|Seleccionar tipo de conexión|Adaptador de red de VMkernel|
|Seleccionar dispositivo de destino|Seleccionar un grupo de puertos distribuido existente|dvpg-Private-Storage|
|Seleccionar tareas de adaptador de red|Seleccionar tareas de adaptador de red|Seleccionar Gestionar adaptadores físicos y Gestionar adaptadores de VMkernel|
|Valores de IPv4|Dirección IPv4|*Dirección privada portátil*<br/><br/>*Esta dirección IP se selecciona entre las direcciones privadas portátiles vinculadas a la VLAN de almacenamiento. Esta dirección debe estar en una subred distinta que la vía B de almacenamiento.*|
|Valores de IPv4|Máscara de subred|Máscara de subred asociada al rango de IP|
<caption>Tabla 21. Red de hosts de almacenamiento</caption>

***Creación de un perfil de host***

Para crear un perfil de host, debe capturarlo del host de capacidad individual que ha configurado anteriormente.

1. Vaya a la pantalla de inicio del cliente web de vSphere y pulse el icono **Perfiles de host**.
2. Pulse el signo más (+) verde, **Extraer perfil de un host** y seleccione el host de capacidad configurado anteriormente en la ventana emergente.
3. Pulse **Siguiente**.
4. Proporcione al perfil de host (perfil de host Capacity01) un nombre adecuado, especifique una descripción y pulse **Siguiente**.
5. Revise los valores y pulse **Finalizar**.

Tras crear el perfil de host, debe modificarlo para que no solicite direcciones MAC cuando el perfil se aplique al resto de los hosts del clúster de capacidad.

1. Pulse el botón derecho del ratón sobre el perfil de host que ha creado y seleccione **Editar valores > Editar perfil de host, NIC virtual de host**.
2. En el panel de la derecha, cambie **Determinar cómo se deciden las direcciones MAC de vmknic** a **El usuario debe elegir explícitamente la opción de política**.
3. Pulse **Siguiente** y, a continuación, pulse **Finalizar**.

***Conexión del perfil de host al clúster de capacidad***

Ahora que ha creado un perfil de host, debe conectar el perfil de host al clúster. Los clústeres se conectan de modo que se puedan aplicar a los hosts de capacidad.

1. Vaya a la vista **Host y clústeres** del cliente web de vSphere.
2. Entre en la modalidad de mantenimiento en cada host de clúster. **Nota:** los perfiles solo se pueden aplicar a los hosts que estén en modalidad de mantenimiento.
3. Pulse el botón derecho del ratón sobre el clúster de capacidad y seleccione **Conectar perfil de host** en el menú emergente.
4. Seleccione el perfil de host que ha creado y pulse **Siguiente**.
5. Especifique la información adecuada en la pantalla **Personalizar host** y pulse **Finalizar**.
6. Quite los hosts de la modalidad de mantenimiento (modalidad de no mantenimiento).

## Paso 12 Solicitar, configurar y conectar almacenamiento compartido
{: #step-12-order-configure-and-attach-shared-storage}

Antes de continuar, es imperativo que solicite, configure y conecte almacenamiento compartido para su uso con los clústeres de gestión y de capacidad, así como de los hosts dentro del entorno. Si desea utilizar la solución de almacenamiento compartido de varios arrendatarios de {{site.data.keyword.cloud_notm}} (File Storage), consulte [Guía de arquitectura para IBM File Storage para {{site.data.keyword.cloud_notm}} con VMware](/docs/infrastructure/FileStorage?topic=FileStorage-architectureguide).

## Paso 13 Habilitación de HA/DRS y svMotion vCVA
{: #step-13-enabling-ha-drs-and-svmotion-vcva}

***Habilitación de HA/DRS en los clústeres de gestión y capacidad***

Ahora que se ha configurado el almacenamiento compartido, debe habilitar la alta disponibilidad (HA) y DRS para proporcionar una protección adicional y funciones de equilibrio de carga a las máquinas virtuales en el clúster de gestión.

1. Vaya al cliente web de vSphere.
* Seleccione Gestionar, Valores del clúster de gestión.
* Seleccione vSphere DRS y pulse Editar. Asegúrese de que estén seleccionados los valores siguientes: **Activar vSphere DRS**, Nivel de automatización - **Completamente automatizado**, valor del control deslizante de umbral de migración - por la mitad, automatización de máquina virtual - **Habilitar niveles de automatización de máquina virtual individual**, Gestión de alimentación - **Desactivado**.  
* En la pantalla de valores de HA de vSphere, asegúrese de que estén seleccionados los valores siguientes: **Activar vSphere HA**, **Supervisión de host**, Prioridad de reinicio de máquina virtual - **Media**, Respuesta de aislamiento de host - **Dejar encendido**.  
***Almacenamiento vMotion del dispositivo virtual vCenter***

Ahora que está configurado el almacenamiento en el clúster de gestión y se ha habilitado HA y DRS, debe configurar el dispositivo virtual vCenter para el almacenamiento compartido.

1. Pulse el botón derecho del ratón sobre el dispositivo adecuado y seleccione **Migrar** en el menú emergente.
2. Seleccione **Cambiar almacén de datos** y pulse **Siguiente**.
3. Seleccione el volumen iSCSI que haya montado anteriormente en el clúster de gestión y pulse **Siguiente**.
4. Revise las selecciones y pulse **Siguiente**.

El entorno avanzado de VMware de un solo sitio se ha completado.

## Resumen
{: #summary}

Ahora tiene un entorno VMware en ejecución en un centro de datos de IBM Cloud. El entorno VMware puede ejecutar cargas de trabajo de producción y complementar un despliegue de IBM Cloud local. El entorno proporciona métodos recomendados de VMware y permite características como VMware DRS, HA, DRS de almacenamiento y redundancia de redes. Puede ampliar esta implementación de arquitectura de referencia con más hosts de capacidad o de gestión y más almacenamiento.

Para obtener más información acerca de VMware, consulte [Desplegar](/docs/infrastructure/vmware?topic=VMware-using-cookbooks-for-vmware-deployments) y [Preguntas más frecuentes de VMware](/docs/infrastructure/vmware?topic=VMware-faqs-vmware)

## Apéndice A: Hoja de trabajo de VLAN
{: #appendix-a-vlan-worksheet}

|Tipo de VLAN|Número de VLAN|Rango de IP|Pasarela|Finalidad|
|---|---|---|---|---|
|Privada primaria||||Gestión|
|Privada primaria||||Almacenamiento|
|Privada primaria||||Máquinas virtuales|
|Primaria pública||||Acceso público|
|Privada portátil||||Máquinas virtuales de gestión|
|Privada portátil||||Almacenamiento|
|Privada portátil||||Máquinas virtuales|
|vMotion|||N/D||
|Tolerancia a errores|||N/D|||
<caption>Hoja de trabajo de VLAN</caption>
