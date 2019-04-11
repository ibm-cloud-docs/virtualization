---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Explicación de los valores de contador de User Bean / UBC
{: #understanding-ubc-user-beancounter-values}

Utilice la modalidad SLM si no se entiende UBC. SLM es más sencilla y proporciona el mismo tipo de control.

|Nombre|Tipo|Descripción|
|---|---|---|
|*Parámetros principales*|||
|`numproc`|*sistema*|Número de procesos y hebras|
|`numtcpsock`|*sistema*|Número de sockets TCP|
|`numothersock`|*sistema*|Número de sockets que no sean TCP|
|`vmguardpages`|*sistema*|Garantía de asignación de memoria|
|`cpuunits`|*cpu*|Potencia de CPU|
|`diskspace`|*disco*|Cuota de espacio de disco|
|`rate`|*red*|Ancho de banda de red|
|`ratebound`|*red*|Indica si el ancho de banda de red está limitado|
|*Parámetros adicionales*|||
|`kmemsize`|*sistema*|Tamaño de la memoria de kernel no intercambiable asignada para procesos de este contenedor|
|`tcpsndbuf`|*sistema*|Tamaño total de almacenamiento intermedio de envío de TCP|
|`tcprcvbuf`|*sistema*|Tamaño total de almacenamiento intermedio de recepción de TCP|
|`othersockbuf`|*sistema*|Tamaño total de almacenamiento intermedio de socket de dominio UNIX, UDP y otro almacenamiento intermedio de envío de protocolos de datagrama|
|`dgramrcvbuf`|*sistema*|Almacenamiento intermedio de recepción de UDP y otros protocolos de datagrama|
|`oomguardpages`|*sistema*|Cantidad de memoria garantizada en caso de que la memoria se reserve en exceso (si se agota la memoria se anula la garantía)|
|`privvmpages`|*sistema*|Límite de asignación de memoria|
|`lockedpages`|*sistema*|Páginas de proceso que no se permite intercambiar (páginas bloqueadas por
[mlock(2) ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](http://linux.die.net/man/2/mlock))|
|`shmpages`|*sistema*|Tamaño total de memoria compartida (comunicación entre procesos o IPC, correlaciones anónimas compartidas y objetos tmpfs) en página|
|`physpages`|*sistema*|Número total de páginas de RAM utilizadas por procesos|
|`numfile`|*sistema*|Número de archivos abiertos|
|`numflock`|*sistema*|Número de bloqueos de archivo|
|`numpty`|*sistema*|Número de pseudoterminales|
|`numsiginfo`|*sistema*|Número de estructuras siginfo|
|`dcachesize`|*sistema*|Tamaño total de estructuras dentry e inode bloqueadas en memoria|
|`numiptent`|*sistema*|Número de entradas NETFILTER (filtrado de paquetes IP)|
|`cpulimit`|*cpu*|Límite de consumo de CPU|
|`diskinodes`|*disco*|Cuota de inode (objeto del sistema de archivos) de disco|
|`quotatime`|*disco*|Periodo de gracia de cuota de disco|
|`quotaugidlimit`|*disco*|Límite del número de entradas de contabilidad de uid y gid|
<caption>Tabla 1. Descripciones de parámetros SLM</caption>

Puede acceder a VzLinuxUBCMgmt.pdf o visualizarlo en HTML, dentro de PIM en el servidor accediendo a:
**Gestión > Soporte > Descargas > Gestión de recursos UBC: Guía del administrador**

Para obtener más información sobre UBC, consulte [Contenedores OpenVZ Virtuozzo, Categoría: UBC ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](http://wiki.openvz.org/Category:UBC).
**Nota:** no todo en OpenVZ es lo mismo que en Virtuozzo.
