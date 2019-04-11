---



copyright:
  years: 2014, 2018
lastupdated: "2018-01-09"

subcollection: virtualization


---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Iniciación a Virtuozzo
{: #getting-started-with-virtuozzo}

## Acceso a Parallels Infrastructure Manager (PIM)
{: #accessing-the-parallels-infrastructure-manager-pim-}

1. Obtenga información desde el [{{site.data.keyword.slportal_full}} ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://control.softlayer.com/){: new_window} acerca del servidor nativo.
    1. En el menú **Dispositivos**, seleccione **Lista de dispositivos**.
    2. Pulse sobre su servidor.
    3. Localice la dirección IP pública y la contraseña.
5. Acceda a la dirección IP pública del servidor en su navegador preferido utilizando HTTPS. Utilice Firefox, Opera o Internet Explorer para gestionar Virtuozzo a través de PIM.

Se requiere JavaScript. Sin JavaScript, se le redireccionará a una página que no existe y no funcionará PIM.
{:tip}

6. Especifique su nombre de usuario y contraseña para iniciar sesión en PIM.

El nombre de usuario es "root" y la contraseña es la contraseña de root del servidor, tal como se indica en el
{{site.data.keyword.slportal}}.
{:tip}

## Instalación y almacenamiento en memoria caché de plantillas
{: #installing-and-caching-templates}

1. En la pantalla de PIM, seleccione **Gestión** > **Actualizaciones**.
2. Vaya al servidor que desee actualizar.
3. Pulse **Actualizaciones de plantilla**.
4. Seleccione las plantillas adicionales que desee instalar o actualizar.
5. Para seleccionar el servidor que desea actualizar, pulse **Plantillas** > **Plantilla de SO**.
6. Seleccione las plantillas de sistema operativo que desee utilizar en el servidor y almacenar en memoria caché. Normalmente, no es necesario almacenar en memoria caché todas las plantillas para empezar. Almacene en memoria caché únicamente las plantillas que desee utilizar. Estas plantillas ocupan una cantidad significativa de espacio en la partición `/vz`.
7. Si solo tiene un nodo, pulse **Memoria caché siguiente**.
8. Espere a que se descarguen e instalen los RPM de plantilla completos. Pulse **Detalles** para ver el estado del almacenamiento en memoria caché.

## Configuración del rango de red para contenedores
{: #setting-up-network-range-for-containers}

Mientras el servidor descarga y almacena en memoria caché las plantillas, puede configurar el rango de red para los contenedores.

1. Pulse **Seleccionar** > **Red**.
2. Seleccione **Nuevo rango de IP**.
3. Añada direcciones IP adicionales para los contenedores. Si no ha adquirido direcciones IP adicionales para los servidores virtuales, vaya al
[{{site.data.keyword.slportal}} ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://control.softlayer.com/){: new_window}. Seleccione **Red** > **Gestión de
IP** > **VLAN** > **Solicitar IP**.
4. Vaya al nodo de hardware y pulse **Contenedores** > **Nuevo contenedor**.
5. Complete la información para que se ajuste a sus necesidades. En este ejemplo, utilice slm.512MB como valor predeterminado.
6. Especifique el nombre de host como un nombre de dominio completo, añada el servidor DNS y añada un dominio de búsqueda si es necesario.
7. Revise los parámetros de recursos.

Tenga cuidado de no prohibir la capacidad del contenedor para procesar datos, su espacio de disco o la memoria añadiendo demasiados parámetros, lo que podría agotar los recursos del servidor. El agotamiento del servidor provoca que se ralenticen o detengan inesperadamente los procesos. Si no entiende estos valores, póngase en contacto con el administrador del sistema o una [empresa de gestión de terceros ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://developer.ibm.com/bluemix/){: new_window}.
{:tip}

8. Pulse **Validar** antes de pulsar **Siguiente**. La validación garantiza que los valores no entran en conflicto entre sí.
9. Seleccione las aplicaciones que desea instalar y pulse
**Siguiente**.
10. Revise la configuración y pulse **Crear**. A continuación, se crea un contenedor.
11. Ahora puede volver a **Contenedores** para ver los nuevos contenedores y obtener la dirección IP asignada automáticamente (de las IP que haya añadido anteriormente).
12. Ahora puede iniciar sesión en el servidor y utilizarlo.

        myname/myserver[0]~$ ssh 1.234.123.4
        Warning: Permanently added '1.234.123.4' (DSA) to the list of known hosts.
        root@1.234.123.4's password:
        [root@test01 ~]# ip a s
        1: lo: mtu 16436 qdisc noqueue
            link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
            inet 127.0.0.1/8 scope host lo
            inet6 ::1/128 scope host
              valid_lft forever preferred_lft forever
        3: venet0: mtu 1500 qdisc noqueue
            link/void
            inet 127.0.0.1/32 scope host venet0
            inet 1.234.123.4/32 brd 1.234.123.4 scope global venet0:0
        [root@test01 ~]# ps auxww
        USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
        root         1  0.0  0.1  10328   744 ?        Ss   13:23   0:00 init [3]
        root     20419  0.0  0.1  12580   632 ?        Sroot     21726  0.0  0.2  60532  1220 ?        Ss   13:23   0:00 /usr/sbin/sshd
        root     21737  0.0  0.1  22512   892 ?        Ss   13:23   0:00 xinetd -stayalive -pidfile /var/run/xinetd.pid
        root     21756  0.0  0.3  62732  2052 ?        Ss   13:23   0:00 sendmail: accepting connections
        smmsp    21764  0.0  0.3  57608  1752 ?        Ss   13:23   0:00 sendmail: Queue runner@01:00:00 for /var/spool/clientmqueue
        root     21776  0.0  2.0 283796 10728 ?        Ss   13:23   0:00 /usr/sbin/httpd
        root     21786  0.0  0.2  20832  1144 ?        Ss   13:23   0:00 crond
        root     21796  0.0  0.1  46648   800 ?        Ss   13:23   0:00 /usr/sbin/saslauthd -m /var/run/saslauthd -a pam -n 2
        apache   21797  0.0  1.1 283932  5788 ?        S    13:23   0:00 /usr/sbin/httpd
        root     21798  0.0  0.1  46648   544 ?        S    13:23   0:00 /usr/sbin/saslauthd -m /var/run/saslauthd -a pam -n 2
        root     23573  0.0  0.6  85876  3240 ?        Rs   13:45   0:00 sshd: root@pts/0
        root     23594  0.0  0.3  12044  1604 pts/0    Ss   13:45   0:00 -bash
        root     23626  0.0  0.1  10700   952 pts/0    R+   13:45   0:00 ps auxww

        [root@test01 ~]# df -h
        Filesystem            Size  Used Avail Use% Mounted on
        vzfs                  1.0 G   64 M  961 M   7% /
        none                 1004 M  4.0 K 1004 M   1% /dev
        [root@test01 ~]#
