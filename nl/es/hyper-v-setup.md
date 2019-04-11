---



copyright:
  years: 2014, 2018
lastupdated: "2018-08-16"

subcollection: virtualization


---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Configuración de Hyper-V
{: #setting-up-hyper-v}

El proceso de configuración de Hyper-V incluye la configuración inicial de Hyper-V, la creación de una máquina virtual y la configuración de red en la nueva máquina virtual. Estos procesos se explican en las directrices siguientes.

## Utilización de Hyper-V Manager
{: #using-hyper-v-manager}

Hyper-V Manager es la consola de gestión que se utiliza para trabajar con Hyper-V. Desde Hyper-V Manager, puede crear, iniciar, detener, suprimir y configurar todas las máquinas virtuales. La consola de gestión es también el lugar donde se establece la ubicación predeterminada para almacenar los discos duros virtuales y la ubicación predeterminada para almacenar los archivos de configuración de máquina virtual.

Puede encontrar Hyper-V Manager en **Herramientas administrativas** en el Panel de control de Windows. En una nueva instalación de Windows 2008, puede encontrar **Herramientas administrativas** bajo Programas en el menú Inicio. Después de abrir el programa Hyper-V Manager, aparecerá una nueva ventana en la pantalla. Tómese un momento para familiarizarse con esta pantalla. Todas las configuraciones y el uso de las máquinas virtuales se llevan a cabo aquí.

## Configuración de Hyper-V
{: #configuring-hyper-v}

La primera configuración que se debe comprobar es la ubicación de almacenamiento de los discos duros virtuales. Desde la ventana de Hyper-V Manager, podrá ver **Hyper-V Manager** y el nombre del servidor en la parte izquierda. Pulse el nombre del servidor y verá la información que está disponible para el servicio Hyper-V en el servidor. Ahora, la parte central de la pantalla está vacía, indicando que no hay máquinas virtuales instaladas. En la parte derecha de la pantalla, verá un enlace a
**Valores de Hyper-V**. Al pulsar el enlace, aparecerán los valores básicos para Hyper-V.

El valor principal que debe ver es **Discos duros virtuales**. En el lado derecho, verá que la ubicación predeterminada almacena los discos duros virtuales. La ubicación predeterminada es:

`C:\Users\Public\Documents\Hyper-V\Virtual Hard Disks`

Al crear una máquina virtual, puede definir un lugar específico si no desea almacenar ese disco duro virtual concreto en la ubicación predeterminada. Por ejemplo, si desea almacenar los discos duros virtuales en una segunda unidad más grande (unidad D en este ejemplo) y desea que dichas unidades virtuales se almacenen en la carpeta **VirtualMachine**, especifique `D:\VirtualMachine` en el campo.

****

También puede explorar manualmente las carpetas para seleccionar la ubicación que desee utilizar. Tras seleccionar una ubicación, pulse
**Aceptar**.

## Configuración de dispositivos de red
{: #configuring-network-devices}

Antes de configurar los dispositivos de red virtuales, determine qué adaptadores de red están conectados a las redes privada y pública. En el menú Inicio, pulse **Conexiones de red**. Ahí verá dos dispositivos de red. Asegúrese de observar que los dispositivos se denominan
**PrivateNetwork** y **PublicNetwork**. Pulse el botón derecho del ratón sobre
**PrivateNetwork** y seleccione **Propiedades**. Verá **Conectar utilizando:**, que muestra el nombre de dispositivo del adaptador de red privada. Anote este nombre, ya que lo necesitará al crear el dispositivo de red privada virtual.

### Configuración de un dispositivo de red privada
{: #configuring-a-private-network-device}

Ahora que se ha configurado la ubicación predeterminada para almacenar los discos duros virtuales, es necesario configurar los dispositivos de red virtual que utilizan las máquinas virtuales. Para abrir el Gestor de redes virtuales, pulse **Gestor de redes virtuales** en el lado derecho de la ventana de Hyper-V Manager etiquetada.

La primera vez que abra esta ventana, verá Redes virtuales en la parte izquierda con
**Nueva red virtual** como única opción. En la parte derecha, tiene tres opciones:
* Externa
* Interna
* Privada.
Asegúrese de seleccionar **Externa** y pulse **Añadir**.

Siga especificando información en los campos restantes:
* **Nombre**: nombre asociado con este dispositivo de red. Para este ejemplo, utilice
_Private_ para indicar que este dispositivo está conectado a la red privada de IBM Cloud.
* **Tipo de conexión**: seleccione el dispositivo que corresponda a su red privada. Este nombre es el nombre del dispositivo que haya encontrado anteriormente en la ventana **Conexiones de red**. Tras seleccionar el dispositivo correcto, pulse
**Aceptar**. Aparecerá un aviso que indica que es posible que se pierda la conexión de red; pulse **Sí** para continuar.

### Configuración de un dispositivo de red pública
{: #configuring-a-public-network-device}

Ahora, el dispositivo de red privada está instalado. Repita los pasos anteriores para la red pública. Para este ejemplo, utilice el nombre
**Public** como nombre de dispositivo. En el tipo de conexión, seleccione el otro dispositivo de red que no se ha utilizado en la configuración de red privada. Después de crear los dispositivos de red pública y privada, pulse **Aceptar**.

## Obtención del soporte de instalación
{: #obtaining-installation-media}

Ahora que se han creado los dispositivos de red virtual, es necesario obtener el soporte de instalación. El soporte de instalación que se utiliza en este ejemplo es un archivo de imagen de CD/DVD. IBM Cloud tiene soporte para los sistemas operativos siguientes para Hyper-V:
* Windows 2008
* Windows 2003
* CentOS
* Fedora
* Ubuntu.
**Nota:** si ya tiene el soporte de instalación, vaya a **Creación de una máquina virtual**.

Los soportes de instalación para [CentOS ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](http://centos.org){: new_window}, [Fedora ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](http://fedoraproject.org/){: new_window} y
[Ubuntu ![Icono de enlaceexterno](../../icons/launch-glyph.svg "Icono de enlace externo")](http://www.ubuntu.com/){: new_window} están disponibles en sus respectivos sitios web. Los tres sistemas operativos tienen licencia gratuita y no es necesario que el usuario realice ninguna otra acción.


## Creación de una máquina virtual
{: #creating-a-virtual-machine}

Ahora que tiene el soporte de instalación, puede crear una máquina virtual desde Hyper-V Manager. Pulse **Nuevo > Máquina virtual** para iniciar el **Asistente de nueva máquina virtual**.

En la primera pantalla, pulse **Siguiente** y especifique un nombre para la nueva máquina virtual. Este nombre se puede modificar en una fecha posterior. Puede cambiar la ubicación del disco duro virtual. Se utilizará el valor predeterminado que haya establecido previamente a menos que especifique una ubicación distinta. Después de especificar la información, pulse **Siguiente**.

Ahora, deberá asignar memoria al servidor. Normalmente, debe proporcionar al menos la memoria mínima recomendada especificada por el sistema operativo. Tras especificar la cantidad de memoria a asignar, pulse **Siguiente**.

A continuación, establezca el primer adaptador de red. Si se va a instalar un sistema operativo Linux en la máquina virtual, seleccione
**No conectado**. Si se va a instalar Windows, seleccione **Privada** y, a continuación, pulse
**Siguiente**.

Puede cambiar los valores siguientes del disco virtual: nombre de archivo, ubicación de almacenamiento y tamaño. Realice los cambios aplicables y pulse **Siguiente**.

Es necesario establecer el soporte de instalación. Seleccione **Instalar un sistema operativo desde un CD/DVD-ROM de arranque > Archivo de imagen (.iso)** para establecer la ubicación del archivo de imagen de CD/DVD para el soporte de instalación. Pulse **Siguiente**, visualice el resumen y pulse **Finalizar** para cerrar la ventana.

Por último, necesitará crear los adaptadores de red virtuales. La nueva máquina virtual aparece ahora en la sección
**Máquinas virtuales** de Hyper-v Manager. Pulse el botón derecho del ratón sobre la nueva máquina virtual y pulse
**Valores**.

### Configuración del adaptador de red virtual para el sistema operativo Windows
{: #configuring-virtual-network-adapter-for-windows-operating-system}

Ahora hay una lista de hardware disponible para la máquina virtual. En la lista, verá **Adaptador de red** y **Privado**. Si ve **No conectado**, pulse sobre el adaptador de red, seleccione
**Privado** y pulse **Aplicar**.

A continuación, pulse **Añadir hardware > Adaptador de red > Añadir** para añadir el adaptador de red al hardware.

En el panel de la derecha, seleccione **Público** en la lista desplegable y pulse **Aceptar**.

### Configuración del adaptador de red virtual para el sistema operativo Linux
{: #configuring-virtual-network-adapter-for-linux-operating-system}

Ahora hay una lista de hardware disponible para la máquina virtual. Pulse **Adaptador de red > Eliminar** y seleccione
**Añadir hardware**. La lista de la derecha cambiará.
En la lista, seleccione **Adaptador de red heredado**. **Nota:** esta opción es distinta al adaptador de red que no se utiliza en una máquina virtual Linux.
Pulse **Añadir** para añadir este adaptador de red heredado a la lista de hardware. El nuevo adaptador de red heredado se selecciona de forma automática.
Seleccione **Privado** en la lista desplegable y pulse **Aplicar**.
Repita los pasos anteriores para añadir el **Adaptador de red heredado**. No obstante, seleccione **Público**.
Una vez que se haya añadido la nueva red heredada, pulse **Aceptar**.

## Instalación de un sistema operativo de invitado
{: #installing-a-guest-operating-system}

La máquina virtual está ahora lista para iniciarse. Pulse el botón derecho del ratón sobre la máquina virtual y seleccione
**Conectar**. Se abrirá la consola de la máquina virtual. En el menú **Acción**, seleccione
**Iniciar**. Se iniciará la nueva máquina virtual utilizando el soporte de instalación que haya seleccionado. Ahora, instale el sistema operativo. Durante el proceso de instalación del sistema operativo, se recomienda que configure los valores de la red pública. La red pública es la segunda interfaz de red. Cuando finalice la instalación del sistema operativo, tendrá una máquina virtual en funcionamiento. Si se configura una red pública, podrá acceder a la máquina virtual de forma remota.

El paso final del proceso de instalación es configurar la red privada. Si la máquina virtual no está conectada a la red privada, el proceso de instalación habrá finalizado. Para obtener más información sobre la configuración de la red privada, consulte
[Configuración de la red de una máquina virtual](/docs/infrastructure/virtualization?topic=Virtualization-setting-up-a-virtual-machine-network).
