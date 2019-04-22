---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Visión general del servidor nativo de Parallels Server 4
{: #overview-of-parallels-server-4-bare-metal}

Parallels Server Bare Metal es una solución de virtualización que proporciona virtualización de hardware junto con la virtualización de software de Virtuozzo, ofreciendo tecnología tanto para máquinas virtuales como para contenedores*.
{:shortdesc}

**Interfaz de línea de mandatos**

Junto con los mandatos habituales de Virtuozzo, hay otros mandatos disponibles: `pctl`, `pmigrate`, `pstat` y `prl_disk_tool`.

**Migración**

Ahora puede migrar contenedores de Virtuozzo a máquinas virtuales, servidores físicos a contenedores de Virtuozzo y servidores físicos a máquinas virtuales para permitir la consolidación de los servicios. Esta funcionalidad permite también migrar contenedores o máquinas virtuales entre servidores nativos, convertir máquinas virtuales que se crearon en entornos virtualizados distintos (V2V) y cambiar SID de Windows al clonar o desplegar una máquina virtual de Windows a partir de una plantilla.

**Uso de direcciones IP y VLAN**

Puede asignar máquinas virtuales a una IP desde el servidor físico utilizando las herramientas de Parallels que se encuentran en la máquina virtual. Además, PSBM permite que se puedan crear conmutadores virtuales y VLAN dentro de las máquinas virtuales para asegurar mejor el tráfico de red dentro de la máquina virtual.

**Ejecución de mandatos**

Puede ejecutar mandatos en bruto desde el servidor físico directamente dentro de los contenedores de Virtuozzo y, ahora, dentro de las máquinas virtuales (cuando se hayan instalado las herramientas de Parallels dentro de la máquina virtual), incluyendo restablecimientos de contraseñas de usuario.

**Contabilidad de procesos**

Puede utilizar Parallels Server Bare Metal para aumentar y reducir la prioridad de los recursos asignados (CPU, prioridad de E/S de disco) a una máquina virtual rápidamente.

**Copia de seguridad**

Parallels Server Bare Metal proporciona la función de realizar copias de seguridad y restaurar máquinas virtuales y contenedores en el servidor nativo local o en un servidor nativo remoto para incluir copias de seguridad completas e incrementales.

**Contabilidad de red**

Puede utilizar Parallels Server Bare Metal para visualizar y localizar de manera sencilla máquinas virtuales o contenedores según el rendimiento de red histórico y actual.

**Red**

Parallels Server Bare Metal utiliza direcciones IP portátiles, mientras que Virtuozzo utiliza direcciones IP portátiles o estáticas (en función de la configuración).

\* {{site.data.keyword.cloud}} solo ofrece licencia para máquinas virtuales de hardware en Parallels Server 4 Bare Metal, a menos que se indique lo contrario en el formulario de pedido.
_VM_ = máquina virtual. _V_ = VPS o contenedor.
