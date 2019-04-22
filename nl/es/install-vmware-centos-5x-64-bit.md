---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Instalación de VMware en CentOS 5.x (64 bits)
{: #installing-vmware-on-centos-5-x-64-bit-}

Antes de comenzar, inicie sesión en el servidor con el usuario root.

## Preparación de la instalación de VMware
{: #preparing-to-install-vmware}

1. Descargue el [instalador de VMware
![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://vmware.com/download/server/){: new_window}. Pulse el enlace de descarga, acepte el EULA y descargue el archivo LinuxTarball (VMware-server-1.0.3-44356.tar.gz en este ejemplo):

* `# wget –O vmware-server.tar.gz https://download3.vmware.com/software/vmserver/VMware-server-1.0.3-44356.tar.gz`

2. Tras descargar el software, necesitará obtener una clave de licencia (que es gratuita en la versión gratuita de VMWare Server). Para registrarse, consulte
[VMware ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://register.vmware.com/content/registration.html){: new_window}.

3. Extraiga el archivo .tar:

* `# tar -xzvf vmware-server.tar.gz`

## Antes de empezar la instalación
{: #before-you-begin-installation}

Antes de empezar la instalación, revise los requisitos previos siguientes:

```
# yum update
# yum install libXtst.i386
# yum install libXrender.i386
# yum install xinetd
```

## Instalación de VMWare Server
{: #installing-vmware-server}

1. Vaya al directorio:

* `# cd vmware-server-distrib/`

2. Ejecute el script de instalación de VMware:

* `# ./vmware-install.pl`

3. La instalación presenta preguntas básicas y desea saber qué directorios crear para instalar en ellos determinados componentes de VMware. A partir de aquí, acepte los valores predeterminados. Acepte el acuerdo de licencia para continuar con la instalación.

4. Es posible que se le formule esta pregunta: "Ninguno de los módulos vmmon precompilados para VMware Server es adecuado para el kernel en ejecución. Desea que este programa intente compilar el módulo vmmon para su sistema (necesita un compilador C instalado en el sistema)?"
* Responda **sí** a esta pregunta (que es el valor predeterminado).

## Configuración de la red de VMware
{: #setting-up-vmware-networking}

Se le realizarán las preguntas siguientes:

1. "¿Desea conectar en red sus máquinas virtuales?"
* Responda **sí**. Necesita crear una configuración de red para el dispositivo de red público, de manera que pueda acceder a Internet en sus máquinas virtuales.

2. "Su sistema tiene varias interfaces Ethernet disponibles: eth0, eth1. ¿Cuál desea utilizar como puente para vmnet0?"
* Recuerde, la manera en que todos los servidores se configuran y ejecutan en {{site.data.keyword.BluSoftlayer}}, con la red pública en eth1 y la red privada en eth0. En VMware, el dispositivo de puente predeterminado para vmnet0 es eth0. En lugar de pulsar Intro, escriba eth1.

## Puente de la red privada
{: #bridging-the-private-network}

Se le realizará la pregunta siguiente, que puede responder con **sí** o **no**:
*"¿Desea configurar otra red con puente?"

Si tiene pensado ejecutar servicios u otras aplicaciones en la red privada, responda "sí" a esta pregunta (a menos que sepa que no van a utilizar la red privada), y se creará un puente de red para la red privada. Tras pulsar Intro, utilizará automáticamente eth0 como interfaz, ya que esta es la única que queda disponible (debido a que solo tiene dos tarjetas de red en el servidor).

## Otros valores de red
{: #other-networking-settings}

Se le formularán algunas otras preguntas acerca de la configuración de red del servidor de VMware. Responda siguiendo las recomendaciones siguientes:

* *"¿Desea poder utilizar redes NAT en las máquinas virtuales?"*

- Responda "sí"

* *"¿Desea que este programa realice un sondeo para ver si existe una subred privada sin utilizar?"*

- Responda "sí"

- Después de que finalice este proceso, asegúrese de no configurar ninguna otra red NAT.

* *"¿Desea poder utilizar redes solo de host en las máquinas virtuales?"*

- Responda "sí"

* *"¿Desea que este programa realice un sondeo para ver si existe una subred privada sin utilizar?"*

- Responda "sí"

- Después de que finalice este proceso, asegúrese de no configurar ninguna otra red solo de host.

## Especificación del puerto de escucha
{: #specifying-listening-port}

La siguiente pregunta que se le formulará es en qué puerto desea que esté a la escucha VMware Server. Puede dejar el puerto predeterminado como 904.

## Almacenamiento de las máquinas virtuales
{: #storing-the-virtual-machines}

La siguiente pregunta que el instalador realizará es *"¿En qué directorio desea conservar los archivos de máquina virtual?"*. El lugar predeterminado es /var/lib/vmware/virtual machines. Asegúrese de colocar las máquinas virtuales en un lugar donde tenga bastante espacio de disco, como una matriz RAID redundante o un disco duro secundario de gran tamaño. Asegúrese siempre de disponer de espacio suficiente para una máquina virtual. En este caso, podría utilizar un punto de montaje /data/vm que se monte en un disco grande.

## Suministro del número de serie para VMware
{: #providing-the-serial-number-for-vmware}

La parte final de la instalación requiere que inserte la clave de licencia de VMware y un número de serie. Si aún no tiene una clave de licencia, consulte el [Sitio de VMware ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://register.vmware.com/content/registration.html){: new_window}. Si tiene el número de serie para este servidor VMware, insértelo en el indicador y pulse Intro.

Ahora, verá algo parecido a la sentencia siguiente:

    "La configuración de la compilación de VMware Server 1.0.3-44356 para Linux para este kernel en ejecución se ha completado satisfactoriamente".

VMware está ahora configurado en el servidor. Ahora debe descargar la consola VMware Server Console, que es el cliente de GUI para el servidor VMware, para configurar e instalar máquinas virtuales.

## Descarga de VMware Server Console
{: #downloading-vmware-server-console}

VMware Server Console es la aplicación cliente para VMware Server. Utilice la consola para gestionar el servidor VMware en el que puede crear, configurar e instalar máquinas virtuales. Para instalar esta aplicación, descargue el paquete de cliente de Windows de VMware Server desde [Descargas de VMware ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://vmware.com/download/server/){: new_window}. Este paquete es el archivo .zip. Después de la descarga, extraiga el paquete e instale el archivo VMware-console-1.0.3-x. Cuando finalice la instalación, habrá terminado de instalar VMware Server Console y estará listo para configurar VMware Server.

## Inicio de sesión en VMware Console
{: #logging-in-to-the-vmware-console}

Abra la consola VMware Server Console desde el sistema en el que la haya instalado. Cuando se cargue, aparecerá una pantalla "Cambiar host" (inicio de sesión). VMware Server utiliza el nombre de usuario y la contraseña del sistema Linux para autenticar usuarios, por lo que necesita utilizar los nombres de usuario (root en concreto) para iniciar sesión en VMware. Utilice las credenciales siguientes:

* **Nombre de host:** dirección IP más puerto (por ejemplo, 67.228.160.201:904)<br />
* **Nombre de usuario:** root<br />
* **Contraseña:** password (utilice la contraseña real de root del sistema)

## Configuración de reglas de cortafuegos (IPTables)
{: #configuring-the-firewall-rules-iptables-}

Si tiene problemas para conectarse al servidor VMware y no es un problema de autenticación (si obtiene un error de nombre de usuario y contraseña, entonces tiene un usuario o una contraseña incorrectos), es posible que el cortafuegos esté impidiéndole que se conecte a VMware Server. Como solución, intente añadir la regla IPTable siguiente en /etc/sysconfig/iptablesfile. **Nota:** asegúrese de que el convenio de denominación sigue la configuración del servidor:

- `# -A FWALL-INPUT -p tcp -m tcp -s 0/0 --dport 904 -j ACCEPT`
