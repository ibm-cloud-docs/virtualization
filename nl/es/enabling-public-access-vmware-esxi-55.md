---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization

keywords: ESXi, VMware
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Habilitación del acceso público a VMware ESXi
{: #enabling-public-access-to-vmware-esxi}

De forma predeterminada, el host ESXi se instala con la consola de servicio únicamente en la red privada. Por lo tanto, se evita el tráfico público hacia y desde la red pública en el host ESXi.

Para empezar, necesitará conectarse al servidor utilizando un cliente VMware vSphere en la interfaz privada del servidor.

Necesite disponer de la información de IP pública 'primaria' del servidor para completar la configuración. La información relativa a la IP pública del servidor se puede encontrar en el [{{site.data.keyword.slportal_full}} ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://control.softlayer.com/){: new_window}.
{:tip}

Tras iniciar sesión en el host ESXi, siga estos pasos para habilitar la interfaz pública:

1. Es posible que necesite iniciar sesión en el servidor a través de SSH utilizando la IP privada para corregir el direccionamiento.
* Ejecute `esxcfg-route -l` para determinar las rutas actuales
* Ejecute `esxcfg-route -a 10.0.0.0/8 [ip de pasarela privada del servidor]`
* Ejecute `esxcfg-route [pasarela pública del servidor]` para asegurarse de que la pasarela pública sea la predeterminada

Inicie sesión en vSphere y siga estos pasos:

1. Pulse el separador Configuración y pulse **Redes**.
2. Para vSwitch1, pulse **Propiedades > Añadir**
3. Elija **Kernel de máquina virtual** y pulse **Siguiente**
* Los valores se pueden dejar tal como están. Puede volver a etiquetar la red como "VMKernelPublic" para facilitar la identificación.
4. Especifique la información de IP pública del servidor.
5. Para la pasarela predeterminada del kernel de máquina virtual, pulse **Editar**, especifique la IP de pasarela pública y pulse **Aceptar**.
* Pulse **Aceptar**. **Nota:** es normal y esperado que se produzca una desconexión.*

Ahora puede acceder al servidor utilizando tanto la dirección IP pública como la dirección IP privada.

La habilitación del acceso de IP pública a ESXi implica riesgos de seguridad inherentes. Asegúrese de realizar los pasos adecuados para restringir el acceso a ESXi únicamente a aquellos usuarios que lo necesiten.
{:tip}
