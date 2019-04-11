---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization
---

# Guía de consulta rápida de Parallels Virtuozzo Containers 4.0
{: #parallels-virtuozzo-containers-4-0-quick-reference-guide}

Utilice esta información para encontrar programas de utilidad de contenedores de Virtuozzo y términos comunes.

## Programas de utilidad generales
{: #general-utilities}

Los programas de utilidad generales son para las tareas de mantenimiento cotidianas.

|Programas de utilidad generales||
|---|---|
|vzctl|Programa de utilidad para controlar contenedores.|
|vzlist|Programa de utilidad para ver una lista de contenedores existentes en el nodo con información adicional.|
|vzquota|Programa de utilidad para controlar cuotas de disco de contenedores de Virtuozzo.|
{: caption="Tabla 1. Programas de utilidad generales" caption-side="top"}

## Programas de utilidad de licencia
{: #licensing-utilities}

Los programas de utilidad de licencia se utilizan para instalar una nueva licencia, ver el estado de la licencia o generar la solicitud de una nueva licencia.

|Programas de utilidad de licencia||
|---|---|
|vzlicview|Programa de utilidad para visualizar el estado y los parámetros de la licencia de Virtuozzo.|
|vzlicload|Programa de utilidad para gestionar licencias de Virtuozzo en el nodo de hardware.|
|vzlicupdate|Programa de utilidad para activar la instalación de contenedores de Virtuozzo, actualizar las licencias de Virtuozzo instaladas en el nodo de hardware o transferir la licencia de Virtuozzo del nodo de origen al nodo de destino.|
{: caption="Tabla 2. Programas de utilidad de licencia" caption-side="top"}

## Programas de utilidad de migración de contenedores
{: #container-migration-utilities}

Las herramientas de migración de contenedores se utilizan para migrar contenedores entre nodos de hardware o dentro de un nodo de hardware.

|Programas de utilidad de migración de contenedores||
|---|---|
|vzmigrate|Programa de utilidad para migrar contenedores de un nodo de hardware a otro.|
|vzmlocal|Programa de utilidad para la clonación local o el traslado de los contenedores.|
|vzp2v|Programa de utilidad para migrar un servidor físico a un contenedor del nodo.|
|vzv2p|Programa de utilidad para migrar un contenedor a un servidor físico.|
{: caption="Tabla 3. Programas de utilidad de migración de contenedores" caption-side="top"}

## Programas de utilidad de copia de seguridad de contenedores
{: #container-backup-utilities}

Los programas de utilidad de copia de seguridad de contenedores se utilizan para realizar la copia de seguridad y restaurar las áreas privadas del contenedor, archivos de configuración, scripts de acción e información de cuotas.

|Programas de utilidad de copia de seguridad de contenedores||
|---|---|
|vzbackup|Programa de utilidad para realizar copias de seguridad de contenedores.|
|vzrestore|Programa de utilidad para restaurar los contenedores de copia de seguridad.|
|vzabackup|Programa de utilidad para realizar copias de seguridad de nodos de hardware y sus contenedores. Este programa de utilidad requiere el software Parallels Agent.|
|vzarestore|Programa de utilidad para restaurar nodos de hardware y contenedores de copia de seguridad. Este programa de utilidad requiere el software Parallels Agent.|
{: caption="Tabla 4. Programas de utilidad de copia de seguridad de contenedores" caption-side="top"}

## Programas de utilidad de gestión de plantillas
{: #template-management-utilities}

Las herramientas de gestión de plantillas se utilizan para crear plantillas y para mantener e instalar aplicaciones en un contenedor.

|Programas de utilidad de gestión de plantillas||
|---|---|
|vzpkg|Programa de utilidad para gestionar plantillas EZ de SO y de aplicación ya sea dentro de los contenedores o del propio nodo de hardware.|
|vzmktmpl|Programa de utilidad para crear plantillas EZ de SO y de aplicación.|
|vzveconvert|Programa de utilidad para convertir contenedores basados en plantillas estándar de Virtuozzo a contenedores basados en plantillas EZ.|
|vzpkgproxy|Programa de utilidad para crear servidores proxy de almacenamiento en memoria caché para manejar plantillas EZ de sistema operativo y de aplicación.|
|vzrhnproxy|Programa de utilidad para crear servidores proxy RHN para manejar los paquetes incluidos en las plantillas EZ de SO de RHEL 4 y RHEL 5.|
|vzpkgls|Programa de utilidad para obtener una lista de plantillas disponibles en el nodo de hardware y en los contenedores.|
|vzpkginfo|Programa de utilidad para obtener la información sobre cualquier plantilla instalada en el nodo de hardware.|
|vzpkgcreat| Crear un nuevo conjunto de paquetes a partir de archivos binarios RPM o DEB.|
|vzpkgadd|Programa de utilidad para añadir una plantilla a un contenedor.|
|vzpkglink|Programa de utilidad para sustituir archivos reales dentro de un contenedor por enlaces simbólicos a los archivos del nodo.|
|vzpkgrm|Programa de utilidad para eliminar una plantilla de un contenedor.|
|vzpkgcache|Actualizar un conjunto de archivados de contenedores preinstalados después de la instalación de una nueva plantilla.|
{: caption="Tabla 5. Programas de utilidad de gestión de plantillas" caption-side="top"}

## Herramientas complementarias
{: #supplementary-tools}

Las herramientas complementarias se utilizan para tareas variadas en el contexto del nodo de hardware y el contenedor.

|Herramientas complementarias||
|---|---|
|vzup2date|Programa de utilidad para actualizar el software y las plantillas de Virtuozzo.|
|vzup2date-mirror|Programa de utilidad para crear duplicados locales del repositorio oficial de Virtuozzo.|
|vzfsutil|Programa de utilidad para la optimización de VZFS y la comprobación de coherencia.|
|vzcache|Programa de utilidad para obtener espacio de disco adicional almacenando en memoria caché los archivos idénticos en contenedores diferentes.|
|vzsveinstall|Programa de utilidad para crear el contenedor de servicios en el nodo de hardware.|
|vzsveupgrade|Programa de utilidad para actualizar los paquetes dentro del contenedor de servicios.|
|vzps|Programa de utilidad que funciona como los programas de utilidad ps y top estándar, con funciones añadidas relacionadas con el contenedor.|
|vztop|Programa de utilidad que funciona como los programas de utilidad ps y top estándar, con funciones añadidas relacionadas con el contenedor.|
|vzsetxinetd|Programa de utilidad para conmutar algunos servicios entre la modalidad autónoma y la modalidad `xinetddependent`.|
|vzdqcheck|Imprimir el uso actual del espacio de archivos desde el punto de vista de la cuota.|
|vzdqdump|Programa de utilidad para volcar los límites de cuota de grupo o de usuario de contenedor y los tiempos de gracia del kernel o del archivo de cuotas o para cargarlos en un archivo de cuotas.|
|vzdqload|Programa de utilidad para volcar los límites de cuota de grupo o de usuario de contenedor y los tiempos de gracia del kernel o del archivo de cuotas o para cargarlos en un archivo de cuotas.|
|vznetstat|Programa de utilidad que imprime estadísticas del uso de tráfico de red por parte de los contenedores.|
|vzcpucheck|Programa de utilidad para comprobar el uso de CPU por parte de los contenedores.|
|vzmemcheck|Programa de utilidad para comprobar los parámetros de memoria actuales del nodo de hardware y del contenedor.|
|vzcalc|Programa de utilidad para calcular el uso de recursos por parte de un contenedor.|
|vzcheckovr|Programa de utilidad para comprobar el exceso de compromiso y la seguridad del sistema actual de los valores de control de recursos totales.|
|vzstat|Programa de utilidad para supervisar el consumo de recursos del contenedor y del nodo de hardware en tiempo real.|
|vzpid|Programa de utilidad que imprime el ID de contenedor al que pertenece el proceso.|
|vzsplit|Programa de utilidad para generar el ejemplo de archivo de configuración de contenedor, "dividiendo" el nodo de hardware en partes iguales.|
|vzcfgscale|Programa de utilidad para escalar la configuración del contenedor.|
|vzcfgvalidate|Programa de utilidad para validar la corrección del archivo de configuración del contenedor.|
|vzcfgconvert|Programa de utilidad para convertir los archivos de configuración del contenedor de Virtuozzo 2.0.2 al formato de Virtuozzo 2.5.x.|
|vzstatrep|Programa de utilidad para analizar los registros recopilados por vzlmond y para generar informes de estadísticas basados en estos registros (en formato de texto y en formato gráfico).|
|vzreport|Programa de utilidad para preparar un informe de problemas y enviarlo automáticamente al equipo de soporte de Parallels.|
|vzhwcalc|Programa de utilidad para explorar los recursos principales en cualquier servidor Linux y crear un archivo en el que se especifique esta información.|
|vzveconvert|Programa de utilidad para convertir los contenedores basados en plantillas de sistema operativo estándar de Virtuozzo a los basados en plantillas EZ.|
|vznetcfg|Programa de utilidad para gestionar dispositivos de red en el nodo de hardware.|
|vzmtemplate|Programa de utilidad para migrar el SO instalado y las plantillas de aplicación de un nodo de hardware a otro.|
{: caption="Tabla 6. Herramientas complementarias" caption-side="top"}

## Términos comunes
{: #common-terms}

|Términos comunes||
|---|---|
|Nodo de hardware|El nodo de hardware (o HN) es un sistema físico que alberga contenedores.|
|Contenedor|El contenedor es funcionalmente idéntico a un servidor autónomo aislado con sus propias direcciones IP, procesos, archivos, usuarios, su propia versión, sus propios archivos de configuración, sus propias aplicaciones, bibliotecas del sistema y otras funciones de servidor. Las contenedores comparten nodos de hardware y el mismo kernel de SO, pero están aislados entre sí. Cada contenedor tiene un ID numérico exclusivo a partir del 1.|
|Sistema operativo de host|El sistema operativo host (o host) es un sistema operativo que está instalado en el nodo de hardware. También se conoce como contenedor 0, en contraste con los contenedores reales, que tienen ID que comienzan por el 1.|
|Plantilla|La plantilla (o conjunto de paquetes) es un conjunto de archivos de aplicación originales que se vuelven a empaquetar para su montaje en el sistema de archivos de Virtuozzo (VZFS). Hay dos tipos de plantillas disponibles en Virtuozzo: plantillas de SO que se pueden utilizar para crear nuevos contenedores en sus bases y plantillas de aplicación que se pueden añadir a los contenedores después de su creación.|
|PIM|Parallels Infrastructure Manager (o PIM) es una herramienta de gestión basada en la web que está diseñada para administradores de hosts.|
|PMC|Parallels Management Console (o PMC) es una herramienta de gestión remota con una interfaz gráfica de usuario que está diseñada para administradores de hosts.|
|PPP|Parallels Power Panel es una herramienta de gestión basada en la web que está diseñada para propietarios de contenedores.|
{: caption="Tabla 7. Términos comunes" caption-side="top"}
