---
copyright:
  years: 2014, 2018
lastupdated: "2018-02-14"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Iniciación a VMware ESXi
{: #getting-started-with-vmware-esxi}

Después de que se suministre el servidor ESX, el primer paso para acceder al servidor es conectarse a la VPN de red privada de
{{site.data.keyword.cloud}}. De forma predeterminada, todos los servidores VMware desplegados por
{{site.data.keyword.cloud_notm}} tienen únicamente la interfaz de red privada o el vínculo que se configura con una dirección IP. Este despliegue es una medida de seguridad adicional que se establece para limitar la exposición del nuevo servidor ESX a Internet público. Como resultado de esta seguridad añadida, el servidor solo está a la escucha en una dirección IP 10.x.x.x privada de
{{site.data.keyword.cloud_notm}}, que solo es accesible a través de la VPN de
{{site.data.keyword.cloud_notm}} o de otro de los hosts existentes de {{site.data.keyword.cloud_notm}} con acceso a la misma VLAN privada en la que reside el nuevo servidor VMware.

Para obtener más información sobre la VPN de {{site.data.keyword.cloud_notm}}, consulte
[Iniciación a las redes privadas virtuales (VPN)](/docs/infrastructure/iaas-vpn?topic=VPN-getting-started-with-virtual-private-networking-vpn-).

## Acceso al host ESX
{: #accessing-your-esx-host}

Después de conectarse a la VPN de {{site.data.keyword.cloud_notm}}, puede comunicarse con el nuevo servidor VMware. ESX se gestiona mediante un cliente vSphere. Puede recuperarlo del nuevo servidor VMware especificando la dirección IP de la red privada en un navegador web y pulsando "Descargar cliente vSphere" en la página resultante.

Después de que se descargue el cliente vSphere y de que lo instale en la estación de trabajo local, puede iniciar la aplicación y especificar la dirección privada del servidor y las credenciales de inicio de sesión. Las credenciales de inicio de sesión se pueden ver en la página Dispositivos del {{site.data.keyword.slportal_full}} tras pulsar sobre el separador Contraseñas.

1. Especifique la dirección IP privada del servidor, el usuario root y la contraseña en los campos correspondientes del cliente vSphere y pulse **Iniciar sesión** para conectarse.
2. Ahora que está conectado al servidor, acceda al nuevo host ESX seleccionando **Inventario**
3. El host ESX aparecerá como un nodo disponible para la configuración en la página resultante. Desde aquí, tendrá diversas maneras de desplegar una máquina virtual. Un método es cargar un ISO de instalación del sistema operativo que prefiera en el almacén de datos local del servidor. Después de que se haya cargado el ISO, puede seleccionar el ISO como medio de instalación al crear una máquina virtual.  

## Carga de un ISO
{: #uploading-an-iso}

Realice los pasos siguientes para cargar un ISO en el almacén de datos del servidor.

1. Resalte el servidor (representado por un icono de servidor y su dirección IP privada) en el panel de la izquierda, y seleccione el separador **Configuración** en el panel de la derecha.
2. Seleccione **Hardware > Almacenamiento**. Asegúrese de que la **Vista** está seleccionada en
**Almacenes de datos**.
3. Pulse el botón derecho del ratón sobre el almacén de datos adecuado y pulse **Examinar almacén de datos**.
4. La página resultante tiene un gestor de archivos para examinar, cargar archivos en el almacén de datos y descargar archivos del mismo.  
  * Para cargar un ISO, pulse el icono de volumen (con la flecha apuntando hacia arriba) y seleccione **Cargar archivo**.
5. Seleccione el archivo ISO del sistema de archivos local que desee cargar en el almacén de datos y pulse **Abrir**.
6. El ISO que haya elegido está ahora en el almacén de datos.
7. Ahora que el medio de instalación está en el servidor VMWare, puede continuar con la
[creación de la máquina virtual](/docs/infrastructure/vmware?topic=VMware-creating-a-vmware-esx-virtual-machine).
