---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Configuración de un hipervisor
{: #setting-up-a-hypervisor}

Utilice los pasos siguientes para configurar un hipervisor.

1. Inicie sesión en el [{{site.data.keyword.slportal_full}} ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://control.softlayer.com/){: new_window} utilizando sus credenciales exclusivas.
2. En el menú **Dispositivos**, seleccione **Lista de dispositivos** y busque el hipervisor.
3. Conéctese a la red privada a través de la [VPN segura
![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://www.softlayer.com/vpn-access){: new_window} para acceder al hipervisor.

**Nota:** los proveedores de hipervisores de {{site.data.keyword.cloud}} incluyen XenServer, VMware e Hyper-V. Cada proveedor tiene consolas de gestión exclusivas a las que se accede de manera diferente. Para obtener más información sobre cómo acceder y trabajar en una consola de gestión, consulte los enlaces siguientes:

   * [XenServer ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://support.citrix.com/en/products/xenserver){: new_window}
   * [VMware ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://www.vmware.com/support/vsphere-hypervisor.html){: new_window}
   * [Hyper-V ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://technet.microsoft.com/en-us/windowsserver/dd448604){: new_window}

4. Obtenga IP portátiles para sus máquinas virtuales.
    * Las máquinas virtuales requieren direcciones IP portátiles. Se pueden solicitar bloques de direcciones IP portátiles públicos y privados a través del {{site.data.keyword.slportal}}.
    * Para obtener más información sobre cómo asignar direcciones IP, consulte [Iniciación a las subredes e IP](/docs/infrastructure/subnets?topic=subnets-getting-started-subnets-ips#getting-started-subnets-ips).

5. Establezca el direccionamiento para las máquinas virtuales en la red privada. Las máquinas virtuales necesitan los parámetros siguientes para que se puedan direccionar a otras máquinas virtuales a través de la red privada:
    * IP privadas portátiles
    * Ruta estática correspondiente al rango de red 10.0.0.0/8

Para obtener más información sobre el proceso de direccionamiento de máquina virtual, consulte
[Configuración de la red de una máquina virtual](/docs/infrastructure/virtualization?topic=Virtualization-setting-up-a-virtual-machine-network). Las máquinas que estén en la misma VLAN pueden comunicarse después de configurar la red de máquinas virtuales. [Habilite la expansión de VLAN](/docs/infrastructure/vlans?topic=vlans-vlan-spanning) si las máquinas que están en VLAN distintas deben comunicarse.

## Acceder y almacenar ISO de forma segura
{: #access-and-securely-store-isos}

Las máquinas virtuales de la red de {{site.data.keyword.cloud_notm}} pueden ejecutar ISO preconfigurados o personalizados. Las máquinas virtuales de la red de {{site.data.keyword.cloud_notm}} pueden acceder al sitio de réplicas internas, que está disponible exclusivamente para usuarios de {{site.data.keyword.cloud_notm}} y que proporciona configuraciones populares de los sistemas operativos más utilizados. Si necesita una versión o una configuración especial para un sistema operativo específico, consulte el sitio web del proveedor del SO.

Si ejecuta un ISO personalizado en la máquina virtual, cargue el ISO en una ubicación segura para que se pueda recuperar si falla un dispositivo. Muchos usuarios optan por almacenar los ISO personalizados en el sistema local de un dispositivo utilizando SSH o RDP. Como alternativa, se ofrece espacio a través de servicios de almacenamiento que tienen diversas características.
