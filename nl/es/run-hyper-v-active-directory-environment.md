---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-10"

subcollection: virtualization

keywords: hypervisor
---
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:table: .aria-labeledby="caption"}

# Ejecución de Hyper-V en un entorno de Active Directory
{: #running-hyper-v-in-an-active-directory-environment}

<!--Running Hyper-V in an Active Directory environment is the best implementation of Hyper-V. Microsoft has truly shined with the ability to remotely manage a server.You can remotely manage the Hyper-V server allows for it to be installed on a Core installation of Windows freeing up those valuable resources from the system for use within the VM’s. When this is combined with an Active Directory Domain Controller
You manage all your Hyper-V servers from a single Hyper-V Manager that runs on any 2008 or Vista computer that is connected to the Domain.--> <!--Vista?? MS doesn't support Vista.-->

## Antes de empezar
{: #before-you-begin-running-hyper-v-in-an-active-directory-environment}

Antes de empezar a ejecutar Hyper-V en un entorno de Active Directory, necesitará lo siguiente:

* Un servidor de Windows 2008 Datacenter de 64 bits que ejecute Hyper-V (instalación completa o básica del SO).
* Un grupo global en el dominio utilizado para gestionar Hyper-V.
* Acceso de administrador de dominio para permitir cambios en el servidor Hyper-V desde el sistema desde el que esté trabajando.

## Configuración del servidor Hyper-V
{: #configuring-the-hyper-v-server}

1. Abra una conexión de gestión con el servidor Hyper-V.

2. Añada el grupo **Hyper-V** al grupo **Usuarios COM distribuidos**.

3. Añada el grupo **Hyper-V** a los espacios de nombres **CIMV2 y Virtualización**.

4. Añada el grupo **Hyper-V** al almacén de autorización para Hyper-V en el servidor Hyper-V.

5. Proporcione los permisos de grupo de **Hyper-V** al directorio Hyper-V en el servidor Hyper-V.

## Configuración de la conexión de gestión remota
{: #setting-up-remote-management-connection}

Asegúrese de haber iniciado sesión en un sistema del dominio con privilegios de administrador del dominio.

1. Vaya a **Panel de control > Herramientas administrativas > Administración de equipos**.

2. En el menú Acción, seleccione **Conectar con otro equipo**.

3. Especifique el nombre o la dirección IP del servidor y pulse **Aceptar**.

## Adición del grupo Usuarios COM distribuidos al servidor Hyper-V
{: #adding-distributed-com-users-group-to-the-hyper-v-server}

En primer lugar necesita añadir el grupo Hyper-V al grupo Usuarios COM distribuidos en el servidor Hyper-V.

1. Vaya a **Herramientas del sistema > Usuarios y grupos locales > Grupos > Usuarios COM distribuidos > Añadir a grupo**.

2. Pulse **Añadir**, especifique el nombre de grupo para el grupo Hyper-V y pulse **Aceptar**.

## Cómo otorgar acceso remoto al servidor para los espacios de nombres CIMV2 y Virtualización
{: #granting-remote-access-to-the-server-for-cimv2-and-virtualization-namespaces}

Puede actualizar los permisos para el acceso remoto al servidor por parte de Virtualización y CIMV2.

1. Desde la ventana Administración del sistema, seleccione **Servicios y aplicaciones > Control WMI**.

2. Pulse el botón derecho del ratón y vaya a **Propiedades > Seguridad > Raíz > CIMV2 > Seguridad**.

3. Añada el grupo Hyper-V y, a continuación, selecciónelo y pulse **Avanzado**.

4. Asegúrese de que el nuevo grupo esté seleccionado y pulse **Editar**.

5. Cambie **Aplicar a:** a **El espacio de nombres y todos los subespacios de nombres**.

6. Para **Habilitar cuenta y habilitación remota**, asegúrese de que **Permitir** esté seleccionado.

7. Seleccione **Aplicar estos permisos a objetos y/o contenedores solo dentro de este contenedor** y pulse
**Aceptar**.

8. Repita estos pasos para Virtualización.

## Actualización del almacén de autorización
{: #updating-authorization-store}

El almacén de autorización para Hyper-V es el componente final que proporciona el grupo de dominio a Hyper-V.

1. Abra el Administrador de autorización ejecutando el mandato `azman.msc` desde el menú de ejecución o un indicador de mandatos.

2. En el menú Acción, seleccione **Abrir almacén de autorización**.

3. Asegúrese de que **XML** esté seleccionado. Ahora, necesitará acceder de forma remota a
`InitalStore.xml` en el servidor Hyper-V. Utilice la vía de acceso siguiente:

`\HOSTNAME\c$\ProgramData\Microsoft\Windows\Hyper-V\InitialStore.xml`

4. Vaya a **Servicios Hyper-V > Asignaciones de rol > Administrador**.

5. En el menú **Acción**, seleccione **Asignar usuarios y grupos > Desde Windows y Active Directory**.

6. Añada el grupo Hyper-V.

## Cómo otorgar permisos de carpeta
{: #granting-folder-permissions}

Ahora que el grupo Hyper-V tiene permisos completos para gestionar Hyper-V de forma remota, necesitará otorgar permisos para escribir en la carpeta `C:\Users\Public\Documents\Hyper-V`.

1. Abra Mi PC y vaya a la dirección siguiente:

`\HOSTNAME\c$\Users\Public\Documents`

2. Vaya a **Hyper-V > Propiedades > Seguridad**

3. Añada el grupo Hyper-V y asegúrese de que tiene la posibilidad de leer, escribir y ejecutar archivos desde dicho directorio. En general, es más sencillo asignar **Control total**.

## Finalización de la configuración
{: #finalizing-configuration}

Todos los cambios de configuración se han completado. Para finalizar la configuración, necesitará reiniciar el servidor Hyper-V. Después de que el servidor vuelva a estar en línea, conéctese a él desde el gestor de Hyper-V local. Ahora tiene acceso completo para gestionar todas las máquinas virtuales y el servicio Hyper-V.
