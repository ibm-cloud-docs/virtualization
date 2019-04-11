---
copyright:
  years: 2014, 2018
lastupdated: "2018-08-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Preguntas más frecuentes: Hyper-V
{: #faqs-hyper-v}

## ¿Qué es Hyper-V?
{: #what-is-hyper-v-}

Hyper-V es un sistema de virtualización para Windows 2008 Server Datacenter Edition que permite que un servidor físico individual pueda alojar varias máquinas virtuales, ejecutando cada una de ellas su propio sistema operativo.

## ¿Cuáles son los requisitos para ejecutar Hyper-V?
{: #what-are-the-requirements-to-run-hyper-v-}

Hyper-V se debe instalar en Windows 2008 Server de 64 bits Datacenter Edition. Todo el hardware de {{site.data.keyword.BluSoftlayer}} cumple los requisitos del sistema de Hyper-V. Microsoft recomienda al menos 2 GB de RAM para el servidor.

## ¿Está disponible Hyper-V en cualquier otra versión de 2008?
{: #is-hyper-v-available-on-any-other-versions-of-2008-}

{{site.data.keyword.BluSoftlayer_notm}} ofrece Hyper-V únicamente en Datacenter Edition.

## ¿Es posible ejecutar Hyper-V en Windows 2003?
{: #can-hyper-v-run-on-windows-2003}

Hyper-V se ha desarrollado para Windows 2008 y no se puede ejecutar en Windows 2003.

## ¿Qué sistemas operativos se pueden instalar en una máquina virtual?
{: #what-operating-systems-can-be-installed-on-a-virtual-machine}

{{site.data.keyword.BluSoftlayer_notm}} admite los sistemas operativos en Hyper-V:

* Todas las versiones de Windows 2003 y 2008 Server
* Distribuciones Linux CentOS, Fedora y Ubuntu

## ¿Cuántas máquinas virtuales puede ejecutar un servidor?
{: #how-many-virtual-machines-can-a-server-run-}

El número de máquinas virtuales que puede ejecutar un servidor varía en función de los procesadores, RAM y espacio de disco duro del servidor.

## ¿Se puede personalizar la RAM de cada máquina virtual?
{: #is-the-ram-customizable-on-each-virtual-machine-}

Sí. Puede utilizar Hyper-V para personalizar los recursos del sistema de cada máquina virtual, incluyendo la memoria.

## ¿Cuánta RAM necesita una máquina virtual?
{: #how-much-ram-does-a-virtual-machine-need-}

Las necesidades de RAM varían en función de los requisitos de la máquina virtual. Compruebe los requisitos de sistema del sistema operativo de invitado. La cantidad de memoria proporcionada para una máquina virtual se puede cambiar en cualquier momento.

## ¿Puede una máquina virtual acceder a más de un procesador?
{: #can-a-virtual-machine-access-to-more-than-one-processor-}

Puede otorgar a una máquina virtual acceso a varios procesadores con cualquier máquina virtual Windows. No obstante, las máquinas virtuales Linux están limitadas a un único procesador.

## ¿Pueden cambiar los tamaños de disco duro después de la creación de una máquina virtual?
{: #can-hard-disk-sizes-change-after-a-virtual-machine-is-created-}

Sí.

## ¿Es necesario que cada sistema operativo virtual tenga una licencia?
{: #does-each-virtual-operating-system-need-to-have-a-license-}

Las máquinas virtuales Windows 2003 y 2008 tienen licencia a través de {{site.data.keyword.BluSoftlayer_notm}}. Las máquinas virtuales Linux tienen licencia gratuita y no requieren ninguna acción.

## ¿Tendrá las máquinas virtuales acceso a la red privada?
{: #will-the-virtual-machines-have-access-to-the-private-network-}

Sí. Las máquinas virtuales se pueden conectar tanto a una red pública como a una red privada.

## ¿Qué ventajas existen al proporcionar a las máquinas virtuales acceso a una red privada?
{: #what-advantages-are-there-to-providing-private-network-access-to-virtual-machines-}

Proporcionar a las máquinas virtuales acceso a red privada permite que puedan comunicarse entre ellas. El acceso a red privada también permite que las máquinas virtuales se puedan comunicar con otros sistemas internos como NAS e iSCSI y otros servidores que tenga con
{{site.data.keyword.BluSoftlayer_notm}}.

## ¿Pueden las máquinas virtuales utilizar el bloque de IP secundario que se incluye con el servidor?
{: #can-virtual-machines-use-the-secondary-ip-block-that-came-with-the-server-}

No. La IP secundaria que se proporciona con el servidor está dirigida específicamente a trabajar en el servidor físico, y no en una máquina virtual. Necesitará bloques de IP portátiles para conectar su máquina virtual a la red.

## ¿Qué son las direcciones IP portátiles?
{: #what-are-portable-ip-addresses-}

Para obtener información sobre las direcciones IP, consulte
[Iniciación a las subredes e IP](/docs/infrastructure/subnets?topic=subnets-getting-started-with-subnets-and-ips).

## ¿Cómo solicito Hyper-V?
{: #how-do-i-order-hyper-v-}

Puede instalar Hyper-V en un servidor existente emitiendo una recarga de SO y cambiando el sistema operativo a "Windows 2008 Server Datacenter Edition con Hyper-V". Para suministrar un nuevo servidor con Hyper-V, solicite un nuevo servidor y seleccione "Windows 2008 Server Datacenter Edition con Hyper-V" como sistema operativo.

## ¿Viene preinstalado Hyper-V con Windows 2008 Server Datacenter Edition?
{: #is-hyper-v-preinstalled-with-windows-2008-server-datacenter-edition-}

Hyper-V no está preinstalado en los servidores Windows 2008. Si desea instalar Hyper-V en su servidor Windows 2008, necesitará emitir una recarga de SO y seleccionar "Windows 2008 Server Datacenter Edition con Hyper-V" como sistema operativo.

## Se ha realizado la recarga de SO para Hyper-V. ¿Qué viene a continuación?
{: #the-os-reload-for-hyper-v-is-done-what-s-next-}

Es necesario configurar Hyper-V. Para obtener más información sobre cómo configurar Hyper-V, consulte
[Configuración de Hyper-V](/docs/infrastructure/virtualization?topic=Virtualization-setting-up-hyper-v).

## No se inicia una máquina virtual. "No se puede inicializar el sistema remoto de la máquina: Error:'Error no especificado'" ¿Cómo resuelvo este problema?
{: #a-virtual-machine-does-not-start-cannot-initialize-machine-remoting-system-error-unspecified-error-how-do-i-resolve-this-issue-}

Para resolver este problema, detenga el servicio Hyper-V y reinícielo. Normalmente, este problema se produce cuando se intenta iniciar Hyper-V mientras está en curso la activación del servidor. El reinicio del servicio vuelve a activar la máquina virtual después de que finalice la activación.
