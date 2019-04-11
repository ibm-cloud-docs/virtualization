---
copyright:
  years: 2014, 2018
lastupdated: "2018-08-14"

subcollection: virtualization

keywords: Citrix XenServer
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Adición de una unidad de disco duro a Citrix XenServer
{: #adding-a-hard-disk-drive-to-citrix-xenserver}

La adición de una nueva unidad de disco duro en XenServer es distinta al proceso tradicional de Linux. Para XenServer, debe crear un contenedor denominado "repositorio de almacenamiento" para definir un destino de almacenamiento concreto (como un disco duro). Este contenedor es donde se almacenan las imágenes de disco virtual (VDI) de las máquinas virtuales. Una VDI es un espacio de almacenamiento abstracto que actúa como disco duro en las máquinas virtuales.

El repositorio de almacenamiento de Xen admite unidades IDE, SATA, SCSI y SAS cuando está conectado de forma local, además de iSCSI, NFS, SAS y canal de fibra para el almacenamiento remoto.

## Creación de un repositorio de almacenamiento en XenServer
{: #creating-a-storage-repository-in-a-xenserver}

1. Acceda mediante SSH al servidor XenServer como root.

2. Busque el ID de disco del nuevo dispositivo utilizando el mandato siguiente:

       # cat /proc/partitions

  El mandato muestra una lista de todas las unidades de disco duro y particiones. Busque su nuevo disco local, que probablemente sea "sdx" (lo más probable es que sea sdb) o '/cciss/c0d1p0'. Utilice el mandato siguiente para mostrar una lista de los ID de disco de todas las particiones y discos duros presentes en el servidor.

       # ll /dev/disk/by-id

  Busque el ID de disco del disco "sdx" o 'cciss/c0d1'. Necesita el formato "scsi-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" o "cciss-xxxxxxxxxxxxxxxxxxxxxxxxxx".

3. Busque el 'host-uuid' de un servidor XenServer utilizando el mandato siguiente:

        #xe host-list

  El uuid (RO) es el 'host-uuid' que necesita.

4. Cree un repositorio de almacenamiento (SR):

  > **Nota:** el mandato [sr-create ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](http://support.citrix.com/article/CTX121313){: new_window} es para añadir una nueva unidad de disco duro. La creación de una nueva unidad de disco duro es un proceso destructivo que particiona y formatea la unidad, y se pierden todos los datos de la unidad. Si desea volver a introducir una unidad que tiene datos existentes, utilice
[sr-introduce ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](http://support.citrix.com/article/CTX121896){: new_window}.

  `# xe sr-create content-type=user device-config:device=/dev/disk/by-id/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`<br/>
  \- O bien -<br/>
  `# xe sr-create content-type=user device-config:device=/dev/disk/by-id/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`<br/>
  \- O bien -<br/>
  `# xe sr-create content-type=user device-config:device=/dev/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`

## Verificación del repositorio de almacenamiento en XenCenter
{: #verifying-the-storage-repository-in-xencenter}

Puede verificar el repositorio de almacenamiento desde XenCenter siguiendo estos pasos:

1. Conéctese a XenCenter.

2. Acceda a **Almacenamiento** para encontrar los detalles de todos los repositorios de almacenamiento. Puede ver el almacenamiento 'Local Storage2' en la lista que muestra que el disco se ha añadido. Ahora puede empezar a crear VDI en él.
