---
copyright:
  years: 2014, 2018
lastupdated: "2018-06-22"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Migración de una máquina virtual Hyper-V
{: #migrating-a-hyper-v-virtual-machine}

## Antes de empezar
{: #before-you-begin-migrating-a-hyper-v-virtual-machine}

Antes de empezar, revise los siguientes requisitos previos.

* Un servidor compatible (Windows 2008 Server DC Edition con Hyper-V).
* Un método de conexión entre el servidor de origen y el servidor de destino.
* Los servidores de origen y de destino deben residir en la misma VLAN. Si no residen en la misma VLAN, será necesario volver a emitir una IP para la máquina virtual (requiere abrir una incidencia de soporte).
* Máquinas virtuales con enlaces de red activos y software instalado compatible o en funcionamiento.

## Por qué necesita reubicar la máquina virtual Hyper-V
Hay dos motivos comunes por los que podría necesitar reubicar la máquina virtual Hyper-V:
* La máquina virtual está en un hardware que no funciona correctamente.
* Se están agotando los recursos del servidor de host.
En cualquiera de los casos, la migración de Hyper-V se puede completar rápidamente si cumple los requisitos anteriormente citados, realizando los pasos siguientes.

1. Inicie sesión en el servidor de origen y abra Hyper-V Manager. Seleccione la máquina virtual que desea migrar al servidor de destino.
2. Concluya la máquina virtual que desee migrar. A continuación, seleccione **Exportar** en la lista de acciones de servidor y especifique la ubicación del archivo de exportación.
3. Ahora, utilizando RDP en el servidor de origen, puede iniciar sesión en el servidor de destino con la unidad C: montada para trasladar el archivo.

Va a transferir el archivo a través de un montaje de recursos utilizando RDP. Puede elegir el método de transferencia que le resulte más cómodo para este proceso (compartición de Windows, montaje de recursos a través de RDP, FTP, y otros métodos de transferencia).
{:tip}

4. Al exportar el archivo al servidor de destino, asegúrese de que se encuentra en la ubicación predeterminada para discos duros virtuales Hyper-V
(VHD) e impórtelo. La ubicación predeterminada es `C:\Users\Public\Documents\Hyper-V\Virtual hard disks`. Si ha cambiado esta ubicación predeterminada y no está seguro de cuál era, puede seleccionar **Valores de Hyper-V > Importar máquina virtual** para ver la ubicación.
5. Después de localizar el archivo exportado y pulsar **Importar**, la máquina virtual se llena en Hyper-V Manager con todas las configuraciones y archivos que tenía anteriormente. El servidor está ahora en línea y en funcionamiento. Si el servidor no cumplía los requisitos y el servidor de destino se encuentra en una VLAN distinta, necesitará volver a asignar la IP de la máquina virtual con una subred portátil (direccionada como secundaria en la VLAN).
