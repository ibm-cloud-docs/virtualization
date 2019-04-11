---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Configuración de la red de una máquina virtual
{: #setting-up-a-virtual-machine-network}

La configuración de red de su nueva máquina virtual se lleva a cabo en unos pocos pasos. Necesita un bloque de IP portátil independiente tanto para la red pública como para la privada. Se presupone que utiliza una oferta de virtualización que requiere un dominio de difusión de VLAN y que no se direcciona a una subred VLAN (sin ID de red/pasarela/difusión). Si no tiene pensado utilizar la red privada en la máquina virtual, solo necesita la subred pública. Los bloques de IP portátiles se pueden solicitar directamente a través del portal en
[Ventas->Añadir direcciones IP
![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://manage.softlayer.com/Sales/orderAdditionalServices/subnet){: new_window} o desde el
[Gestor de IP de red pública ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://manage.softlayer.com/PublicNetwork/ipManager){: new_window}.

Después de obtener los bloques de IP, puede configurar la red en la máquina virtual. Si la máquina virtual no está instalada, puede configurar el bloque de red pública a través del proceso de instalación del sistema operativo. Este proceso es el método más rápido para configurar la red pública. En todos los pasos de configuración de red se presupone que la primera interfaz de red es la red privada y la segunda interfaz de red es la red pública.

La configuración de la red es distinta para cada SO. Puede encontrar información detallada sobre cómo configurar la red para los sistemas operativos siguientes. **Nota:** los sistemas operativos que están agrupados tienen la misma configuración de red.

* Windows 2008 Server Core
* Windows 2003 Standard y Enterprise
* Windows 2008 Web, Standard, Enterprise y Datacenter
* RedHat, Fedora y CentOS
* Ubuntu y Debian

La configuración de red requiere la información siguiente tanto para los bloques de IP públicos como para los privados. La información sobre los bloques de IP públicos se puede encontrar en el portal en
[Red pública -> Gestor de IP
![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://manage.softlayer.com/PublicNetwork/ipManager){: new_window} y sobre los bloques de IP privados en [Red privada -> Gestor de IP
![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://manage.softlayer.com/PrivateNetwork/ipManager){: new_window}.

    -IP Address
    -Gateway
    -Subnet Mask
    --Subnet conversion
    ---/29 - 255.255.255.248
    ---/28 - 255.255.255.240
    ---/27 - 255.255.255.224
    ---/26 - 255.255.255.192
    ---/25 - 255.255.255.128
    ---/24 - 255.255.255.0

## Windows 2008 Server Core
{: #windows-2008-server-core}

La edición de Windows 2008 Server Core no proporciona una interfaz gráfica para configurar la red del sistema. Es necesario que la configuración se realice de manera manual utilizando el indicador de mandatos. Necesita ejecutar los mandatos siguientes. Las direcciones IP que se utilizan en este ejemplo deben sustituirse por las direcciones IP de sus bloques de IP. Estos mandatos funcionan con versiones anteriores de Windows Server que incluyan el mandato [netsh ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](http://support.microsoft.com/kb/242468){: new_window}.

**Ejemplo de bloque de IP público – 192.0.2.0/29**

* Dirección IP – 192.0.2.2
* Pasarela – 192.0.2.1
* Máscara de subred – 255.255.255.248

**Ejemplo de bloque de IP privado – 10.0.0.0/29**

* Dirección IP – 10.0.0.2
* Pasarela – 10.0.0.1
* Máscara de subred – 255.255.255.248

**Red pública**

Este mandato crea la red pública para conectar su servidor a Internet.

* *Netsh interface ip set address name=”Local Area Connection 2” static 192.0.2.2 255.255.255.248 192.0.2.1*

Estos mandatos crean las entradas de DNS. Si no utiliza la red privada, debe sustituir estas direcciones IP por servidores DNS en funcionamiento

* *Netsh interface ip add dns “Local Area Connection 2” 10.0.80.11*
* *Netsh interface ip add dns “Local Area Connection 2” 10.0.80.12*

**Red privada**

Los dos mandatos siguientes configuran la red privada y crean la ruta persistente para la red privada. La ruta persistente proporciona a su red privada acceso a toda la red privada. El acceso incluye los servidores DNS de la configuración de la red pública. **Nota:** no se asigna ninguna pasarela a la red privada.

* `Netsh interface IP set address name=”Local Area Connection” static 10.0.0.2 255.255.255.248`
* `Route add 10.0.0.0 mask 255.0.0.0 10.0.0.1 -p`

## Windows 2003 Standard y Enterprise
{: #windows-2003-standard-and-enterprise}

Puede utilizar Windows 2003 para configurar la red utilizando la línea de mandatos o la interfaz gráfica de usuario. Si desea utilizar la línea de mandatos, consulte las instrucciones de configuración para Windows 2008 Server Core. Estas instrucciones también funcionan para Windows 2003. Las instrucciones para utilizar la interfaz gráfica se proporcionan aquí. Las direcciones IP que se utilizan en este ejemplo deben sustituirse por las direcciones IP de sus bloques de IP.

**Ejemplo de bloque de IP privado – 10.0.0.0/29**

* Dirección IP – 10.0.0.2
* Pasarela – 10.0.0.1
* Máscara de subred – 255.255.255.248

### Apertura las configuraciones de red
{: #opening-your-network-configurations}

1. Vaya a **Menú Inicio > Configuración > Panel de control**
2. Abra **Conexiones de red**
   **Nota:** si ha instalado dos adaptadores de red, verá “Conexión de área local” y “Conexión de área local 2”

**Red pública**

Utilice las instrucciones siguientes para configurar la red pública a través de la interfaz gráfica de Windows.

1. Pulse el botón derecho del ratón sobre **Conexión de área local > Propiedades** y seleccione **Protocolo de Internet (TCP/IP) > Propiedades**.
2. Vaya a **General > Usar la siguiente dirección IP:**
3. Especifique la dirección IP pública, la máscara de subred y la pasarela.
4. Seleccione **Usar las siguientes direcciones de servidor DNS:**. Si va a configurar la red privada, utilice los servidores DNS siguientes. Si no va a configurar la red privada, necesitará proporcionar los servidores DNS.
    * Servidor DNS preferido: 10.0.80.11
    * Servidor DNS alternativo: 10.0.80.12
5. Pulse **Aceptar**

**Red privada**

1. Pulse el botón derecho del ratón sobre **Conexión de área local 2 > Propiedades**, seleccione
**Protocolo de Internet (TCP/IP) > Propiedades**.
2. Vaya a **General > Usar la siguiente dirección IP:** y especifique la dirección IP privada y la máscara de subred. Deje el campo de pasarela vacío y pulse **Aceptar**
3. Pulse **Aceptar** otra vez para cerrar la ventana de configuración de red.

Para proporcionar acceso a la red privada completa, que incluye los servidores DNS, debe añadir una ruta personalizada al servidor. Siga estos pasos para proporcionar acceso a la red privada:

1. Vaya a **Inicio > Ejecutar**, especifique *cmd* y pulse **Aceptar**.
2. Ejecute el mandato siguiente: **Nota:** asegúrese de sustituir la dirección de la pasarela (10.0.0.1) por la pasarela de su bloque de IP privado.<br/>
*“route add 10.0.0.0 mask 255.0.0.0 10.0.0.1 –p”*

## Windows 2008 Web, Standard, Enterprise y Datacenter
{: #windows-2008-web-standard-enterprise-and-datacenter}

Si tiene Windows 2008, configure la red utilizando el indicador de mandatos o la interfaz gráfica de usuario. Si tiene pensado utilizar el indicador de mandatos, consulte las instrucciones de configuración para Windows 2008 Server Core. Estas instrucciones funcionan para todos los servidores de Windows 2008. Las instrucciones para utilizar la interfaz gráfica se proporcionan aquí. Las direcciones IP que se utilizan en este ejemplo deben sustituirse por las direcciones IP de sus bloques de IP.

**Ejemplo de bloque de IP privado – 10.0.0.0/29**

* Dirección IP – 10.0.0.2
* Pasarela – 10.0.0.1
* Máscara de subred – 255.255.255.248

**Abrir las configuraciones de red**

1. Vaya a **Menú Inicio > Configuración > Panel de control**
2. Abra **Centro de redes y recursos compartidos** y pulse **Gestionar conexiones de red**
* Si ha instalado dos adaptadores de red, verá “Conexión de área local” y “Conexión de área local 2”.

**Red pública**

En las instrucciones siguientes se proporcionan los pasos detallados para configurar la red pública a través de la interfaz gráfica de Windows.

1. Pulse el botón derecho del ratón sobre **Conexión de área local > Propiedades**.
2. Seleccione **Propiedades de Protocolo de Internet versión 4 (TCP/IPv4)**.
3. Vaya a **General > Usar la siguiente dirección IP:** y especifique su dirección IP pública, la máscara de subred y la pasarela.
4. Seleccione **Usar las siguientes direcciones de servidor DNS:**. Si va a configurar la red privada, utilice los servidores DNS siguientes. Si no va a configurar la red privada, necesitará proporcionar los servidores DNS
    * Servidor DNS preferido: 10.0.80.11
    * Servidor DNS alternativo: 10.0.80.12
5. Pulse **Aceptar**

**Red privada**

1. Pulse el botón derecho del ratón sobre **Conexión de área local 2 > Propiedades** y seleccione
**Protocolo de Internet versión 4 (TCP/IPv4) > Propiedades**.
2. Vaya a **General > Usar la siguiente dirección IP:**, especifique su dirección IP privada y la máscara de subred, y pulse
**Aceptar**.
3. Pulse **Aceptar** otra vez para cerrar la ventana de configuración de red.

Para proporcionar acceso a la red privada completa, que incluye los servidores DNS, debe añadir una ruta personalizada al servidor.

1. Vaya a **Inicio > Ejecutar**, especifique “cmd” y pulse **Aceptar**
2. Ejecute el mandato siguiente: **Nota:** sustituya la dirección de pasarela (_10.0.0.1_) por la pasarela de su bloque de IP privado.
  * `route add 10.0.0.0 mask 255.0.0.0 10.0.0.1 –p`

## RedHat, Fedora y CentOS
{: #redhat-fedora-and-centos}

La configuración de la red en RedHat, Fedora y CentOS se realiza de manera manual editando los archivos de configuración. Para editar manualmente los archivos, necesita iniciar sesión en la máquina virtual.

**Ejemplo de bloque de IP público – 192.0.2.0/29**

* Dirección IP – 192.0.2.2
* Pasarela – 192.0.2.1
* Máscara de subred – 255.255.255.248

**Ejemplo de bloque de IP privado – 10.0.0.0/29**

* Dirección IP – 10.0.0.2
* Pasarela – 10.0.0.1
* Máscara de subred – 255.255.255.248

**Red pública**

Los valores de la red pública están incluidos en el archivo siguiente. Debe editar este archivo con la información que se proporciona. Sustituya las direcciones IP de ejemplo por las direcciones IP de su bloque de IP público. Si el archivo no existe, cree el archivo. Si ya existe, sustituya todos los datos del archivo por la información siguiente:
* /etc/sysconfig/network-scripts/ ifcfg-eth1
      DEVICE=eth1
      BOOTPROTO=static
      BROADCAST=192.0.2.7
      IPADDR=192.0.2.2
      NETMASK=255.255.255.248
      NETWORK=192.0.2.0

      GATEWAY=192.0.2.1
      ONBOOT=yes

**Red privada**

Los valores de la red privada están en el archivo siguiente. Debe editar este archivo con la información que se proporciona. Sustituya las direcciones IP de ejemplo por las direcciones IP correctas de su bloque de IP privado. Si el archivo no existe, cree el archivo. Si el archivo ya existe, sustituya todos los datos del archivo por la información siguiente: **Nota:** la red privada *NO* tendrá una ruta predeterminada, por lo que *GATEWAY* no está definido.

* /etc/sysconfig/network-scripts/ ifcfg-eth0
      DEVICE=eth0
      BOOTPROTO=static
      IPADDR=10.0.0.2
      NETMASK=255.255.255.248
      ONBOOT=yes

Finalmente, se necesita una nueva ruta para proporcionar acceso privado a toda la red privada para incluir el servidor DNS, lo cual se lleva a cabo editando el archivo siguiente. Este archivo no existe, por lo que es necesario crearlo. **NOTA:** sustituya "10.0.0.1" en el ejemplo por la pasarela de IP de su subred privada.

* /etc/sysconfig/network-scripts/route-eth0
      10.0.0.0/8 via 10.0.0.1

**Configuración de DNS**

Las configuraciones de DNS primario y secundario se incluyen en un archivo aparte. Debe editar el archivo con la información siguiente. Si esta máquina virtual no tiene acceso a la red privada, necesitará sustituir las direcciones IP del servidor por las direcciones IP de los servidores DNS que desee utilizar.

* /etc/resolv.conf
      nameserver 10.0.80.11
      nameserver 10.0.80.12

Para que estos cambios entren en vigor, deberá reiniciar la red en el servidor. Puede reiniciar la red ejecutando el mandato siguiente:

* `service network restart`

## Ubuntu y Debian
{: #ubuntu-and-debian}

Configure las redes de Ubuntu y Debian a través de un único archivo de configuración. Debe editar este archivo de configuración con la información siguiente. Para editar este archivo, necesitará tener acceso de usuario root al servidor. La instalación base de Ubuntu no proporciona un inicio de sesión como root. No obstante, el usuario que se haya creado en el proceso de instalación puede acceder a sudo. Debe utilizar el mandato sudo para editar el archivo si está ejecutando Ubuntu.

**Ejemplo de bloque de IP público – 192.0.2.0/29**

* Dirección IP – 192.0.2.2
* Pasarela – 192.0.2.1
* Máscara de subred – 255.255.255.248

**Ejemplo de bloque de IP privado – 10.0.0.0/29**

·Dirección IP – 10.0.0.2
·Pasarela – 10.0.0.1
·Máscara de subred – 255.255.255.248

**Red pública y privada**

Edite el archivo siguiente con cualquier editor de texto y sustituya las direcciones IP de ejemplo por las direcciones IP de sus bloques de IP público y privado.

* /etc/network/interfaces
      auto lo
      iface lo inet loopback

      auto eth1
      iface eth1 inet static
      address 192.0.2.2
      netmask 255.255.255.248
      gateway 192.0.2.1

      auto eth0
      iface eth0 inet static
      address 10.0.0.2
      netmask 255.255.255.248

      #Static route for backend service network
      up route add -net 10.0.0.0/8 gw 10.0.0.1

**Configuración de DNS**

Las configuraciones de DNS primario y secundario se incluyen en un archivo aparte. Debe editar este archivo de configuración con la información siguiente. Si esta máquina virtual no tiene acceso a la red privada, necesitará sustituir las direcciones IP del servidor por las direcciones IP de los servidores DNS que desee utilizar.

* /etc/resolv.conf
      nameserver 10.0.80.11
      nameserver 10.0.80.12

Para que estos cambios entren en vigor, deberá reiniciar la red en el servidor. Puede reiniciar la red ejecutando el mandato siguiente:

* */etc/init.d/network restart*

**Nota:** se utiliza 192.0.2.0/24 como Documentación de IP pública para RFC1166 y RFC5737. No utilice estas direcciones IP en sus servidores.
