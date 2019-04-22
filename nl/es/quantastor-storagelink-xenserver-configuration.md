---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: QuantaStor
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Instalación de QuantaStor StorageLink Xenserver
{: #installing-quantastor-storagelink-xenserver}

## Instalación del adaptador QuantaStor Adapter para Citrix StorageLink
{: #installing-the-quantastor-adapter-for-citrix-storagelink}

1. Asegúrese de que su instalación de XenServer tenga la licencia adecuada para StorageLink.

Únicamente los clientes que se suscriban a las licencias "Enterprise" o "Platinum" a través de Citrix pueden utilizar la característica StorageLink. Los clientes que tengan la licencia "Advanced" de Citrix XenServer no podrán acceder a la característica StorageLink.
{:tip}

2. Detenga el servicio StorageLink especificando el mandato siguiente en un indicador de mandatos de administrador. O bien, puede detener el servicio StorageLink desde el Panel de servicios en XenServer.

        C:\> net stop storagelink

3. Descargue y ejecute el adaptador StorageLink disponible desde [OSNexus ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://www.osnexus.com/trynow/){: new_window}.
4. Después de que el servicio se detenga, puede instalar el adaptador QuantaStor StorageLink ejecutando el mandato siguiente:

        osn_quantastor_csladapter.exe

5. Añada este bloque de XML al final de la configuración de StorageLink en:
  * `C:\Archivos de programa\Citrix\StorageLink\Server\scsi_device_id_config.xml`
  * `OSNEXUS`
  * `QUANTASTOR1`
  * Al comienzo de este archivo verá un número de versión similar a _2.0.0.4._ Aumente el número de versión, por ejemplo, a 2.0.0.5. El número de versión indica a StorageLink que necesita actualizar los sistemas XenServer con estas nuevas reglas de identificación de dispositivos.
6. Reinicie el servicio Citrix StorageLink:

        C:\> net start storagelink

## Verificación de que StorageLink se ha cargado correctamente
{: #verifying-that-storagelink-loaded-properly}

1. Inicie sesión en Citrix StorageLink Manager, seleccione el árbol de "Administración" de la izquierda y busque OS NEXUS QuantaStor en la lista [QuantaStor ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://svn.osnexus.com/mediawiki/images/thumb/c/c8/Storagelink_admin.png/640px-Storagelink_admin.png){: new_window}.
2. Añada las credenciales del sistema de almacenamiento para que StorageLink pueda configurar y asignar volúmenes de almacenamiento de forma dinámica para el sistema de almacenamiento QuantaStor.

## Instalación de los valores de configuración multivía de XenServer
{: #installing-the-xenserver-multipath-configuration-settings}

XenServer utiliza el controlador multivía del correlacionador de dispositivos de Linux (`dmmp`) para habilitar el soporte para varias vías de acceso de hardware. El controlador `dmmp` utiliza un archivo de configuración denominado
`multipath.conf` que contiene la modalidad multivía y las reglas de migración tras error para cada proveedor.

QuantaStor tiene algunas reglas especiales que se deben añadir al archivo `multipath.conf`. Edite el archivo
`/etc/multipath-enabled.conf` que se encuentra en cada uno de los nodos `dom0` de XenServer. En dicho archivo, añada la stanza siguiente después de la última sección device { }:

    device {
          vendor                  "OSNEXUS"
          product                 "QUANTASTOR"
          getuid_callout          "/sbin/scsi_id -g -u -s /block/%n"
          path_selector           "round-robin 0"
          path_grouping_policy    multibus
          failback                immediate
          rr_weight               uniform
          rr_min_io               100
          path_checker            tur
    }
