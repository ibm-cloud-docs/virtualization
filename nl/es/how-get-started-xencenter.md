---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-24"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Iniciación a XenCenter
{: #getting-started-with-xencenter}

## Antes de empezar
{: #before-you-begin-xencenter}

Antes de empezar, revise los siguientes requisitos previos:

- Nuevos rangos de direcciones IP portátiles (públicas y privadas) que se direccionan como "Secundarias en VLAN". Esta solución de ejemplo para la configuración de una nueva máquina virtual requiere que disponga de IP utilizables disponibles en la red privada. Puede solicitar subredes desde
[{{site.data.keyword.slportal_full}} ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://control.softlayer.com/network/subnets/order).
- Posibilidad de conectarse a la red privada de {{site.data.keyword.cloud}} a través de la VPN. Para obtener más información sobre el acceso de VPN, consulte [Habilitar el acceso a la red privada de la infraestructura de {{site.data.keyword.cloud_notm}}](/docs/customer-portal?topic=customer-portal-getting-started#enable-private-network).
- Citrix XenCenter se instala en el sistema local. <!-- . http://downloads.service.softlayer.com/citrix/xen/-->

**Nota:** el servidor está preconfigurado con diversas plantillas para ayudarle a empezar a trabajar rápidamente con XenServer.

## Creación de una máquina virtual con XenCenter
{: #creating-a-virtual-machine-with-xencenter}

Utilice los pasos siguientes como ayuda para crear una máquina virtual con XenCenter.

1. Asegúrese de estar conectado a la red privada de {{site.data.keyword.cloud_notm}} a través de la VPN. Establezca una conexión a través de SSL VPN o PPTP.
2. Abra XenCenter y pulse **"Añadir XenServer"**.
3. Especifique el nombre de host de servidor, el nombre de usuario y la contraseña. Necesita utilizar la dirección IP privada del servidor (similar a 10.x.x.x), el nombre de usuario `root` y, a continuación, la contraseña de root del servidor. Esta información está disponible en el {{site.data.keyword.slportal}}, accediendo a [Dispositivos
![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://control.softlayer.com/devices){: new_window} y pulsando sobre el nombre del XenServer a continuación. Pulse Conectar.
4. Si aparece una pantalla de activación de licencia gratuita, complete esta información ahora.
5. Pulse el botón derecho del ratón sobre el nombre de {{site.data.keyword.cloud_notm}} y seleccione **Nueva máquina
virtual...**.<!--You can now create your first Virtual Machine. Create a CentOS virtual machine with a disk of 10 GB and have both Public and Private Networks functioning-->
6. Seleccione sistema operativo que desee utilizar para la nueva máquina virtual y, a continuación, pulse **Siguiente**. **Nota:** algunas plantillas requieren que proporcione su propio soporte.<!--Because you are using CentOS, you can use {{site.data.keyword.BluSoftlayer_notm}} private mirrors for CentOS to get our installation going.Select a version of CentOS and then click **Next**.-->
7. Especifique un nombre adecuado y una descripción para la nueva máquina virtual.
8. Especifique la ubicación del soporte de instalación del sistema operativo de invitado y pulse **Siguiente**. <!-- In the example, {{site.data.keyword.BluSoftlayer_notm}} a CentOS mirror is used as installation media. Provide the Install URL of: http://mirrors.service.softlayer.com/centos/5/os/x86_64 and click **Next**.
  *A trailing ‘/’ at the end of the URL can sometimes break the installation.*
  *This mirror is available only on the {{site.data.keyword.BluSoftlayer_notm}} Private Network. The full mirror's contents are  available here: http://mirrors.service.softlayer.com/.-->
9. Seleccione el número de CPU y la cantidad de asignación de memoria. (Va a crear un sistema optimizado y no necesita demasiada RAM. Por ello, 512 MB es más que suficiente).. Pulse **Siguiente**.
10. Seleccione los discos virtuales para asignar espacio de disco a la máquina virtual.<!--Remember that this is like adding hard disks, it is not like partitioning your system. Partitioning is done during the installation of the OS.--> El tamaño predeterminado es de 8 GB para esta plantilla. Esta cantidad es más que suficiente para el servidor que va a crear. *Opcional: puede ampliar el disco para que se ajuste a sus necesidades resaltando el disco y pulsando **Editar...**.* Pulse **Siguiente**.
11. Añada o elimine interfaces de red virtual para la máquina virtual. Los valores predeterminados son aceptables, a menos que no desee que el sistema pueda comunicarse en la red privada. El ejemplo requiere que deje ambas interfaces en el sistema, pero un administrador de Xen con experiencia puede eliminar una de ellas para sus propios fines. En dicho caso, puede resaltar y suprimir una interfaz si lo desea. Pulse **Siguiente**.
12. Se ha completado la configuración. Deje seleccionado "Iniciar máquina virtual automáticamente" y pulse
**Finalizar**. Se iniciará la instalación. Volverá a la pantalla principal. Puede ver la nueva máquina virtual listada en la parte izquierda.
13. Seleccione la nueva máquina virtual y pulse el separador **Consola**. <!--You can now see that your system is booted into the CentOS installer awaiting your input.-->
14. <!--All of the parameters of a CentOS installation are outside of the scope of this article and will need to be customized by your System Administrator, but this article will provide some specific pieces of information that you need to complete the installation. Select your language to get started. The CentOS installer will then ask you for assistance in configuring the Networking Devices in the system.--> Seleccione **eth0 - xen Virtual Ethernet** y pulse **Aceptar**.
  <!--![14](images/14.png)-->
15. <!--In the pre-requisite notes, we made sure that we already had a set of Portable IP Addresses routed as "Secondary on VLAN" ready for this installation.--> Asegúrese de que tiene la información preparada para configurar TCP/IP. Es necesario configurar manualmente la interfaz con soporte de IPV4 e inhabilitar el soporte de IPV6.
  <!--[15](images/15.png)-->
16. Confirme que sabe cómo utilizar la subred de IP privada (10.17.37.240/29) y pulse
**Aceptar**<!-- to go to the CentOS installer-->. Tanto usted como el administrador del sistema pueden instalar el sistema operativo de invitado en función de sus directrices. Para obtener más información sobre los bloques de IP estáticas y portátiles, consulte [Iniciación a las subredes e IP](/docs/infrastructure/subnets?topic=subnets-getting-started-with-subnets-and-ips).

Ha creado una nueva máquina virtual.
