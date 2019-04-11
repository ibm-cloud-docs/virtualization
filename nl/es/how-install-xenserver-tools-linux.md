---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Instalación de las herramientas de XenServer cuando se utiliza Linux
{: #installing-xenserver-tools-when-using-linux}

Siga estos pasos para instalar las herramientas de XenServer en las máquinas virtuales que utilizan los sistemas operativos CentOS, Debian y RedHat.

Estos pasos son específicos para los XenServers gestionados por el cliente, y no para las VSI proporcionadas por
{{site.data.keyword.cloud}}.
{:tip}

1. Abra XenCenter y establezca una conexión con XenServer. Consulte el ejemplo de una máquina virtual nueva que no tiene las herramientas de XenServer configuradas. Puede ver que, para **"Estado de virtualización:"**, el software indica "Herramientas no instaladas".

2. Pulse el botón derecho del ratón sobre el nombre de la máquina virtual en la que desee instalar las herramientas de XenServer y, a continuación, pulse **Instalar herramientas de XenServer**. Aparecerá un aviso que indica que "Cualquier CD o DVD que esté actualmente en la unidad de CD de la máquina virtual se expulsará cuando se instalen las herramientas de XenServer".

3. Pulse **Instalar herramientas de XenServer** para continuar.

En el separador de la consola de XenCenter para la máquina virtual, verá una imagen de CD "xs-tools.iso" cargada en la unidad de DVD. También proporciona el nombre de dispositivo dentro del sistema.

4. Puede realizar los pasos siguientes a través de la consola o de una sesión SSH en el sistema. En este ejemplo, la instalación utiliza SSH. **Nota:** XenCenter proporciona el nombre de dispositivo de la unidad.

        $ ssh 10.17.37.243
        root@10.17.37.243's password:
        Last login: Thu Sep 11 12:57:31 2008 from 10.0.80.84
        [root@ns0 ~]# cd /mnt
        [root@ns0 mnt]# ls
        [root@ns0 mnt]# mkdir xs-tools
        [root@ns0 mnt]# mount /dev/xvdd /mnt/xs-tools/
        mount: block device /dev/xvdd is write-protected, mounting read-only
        [root@ns0 mnt]# cd /mnt/xs-tools/Linux/
        [root@ns0 Linux]# bash install.sh
        Detected `CentOS release 5.2 (Final)' (centos version 5).

        The following changes are made to this virtual machine:
        * packages to be installed or upgraded:
        - kernel-xen-2.6.18-53.1.13.el5.xs4.1.0.24.x86_64.rpm
        - xe-guest-utilities-4.1.0-257.x86_64.rpm

        Continue? [y/n] y

        Preparing...########################################### [100%]
        1:xe-guest-utilities##########################################[100%]
        Preparing...########################################### [100%]
        package kernel-xen-2.6.18-92.el5 (which is newer than kernel-xen-2.6.18-53.1.13.el5.xs4.1.0.24) is already installed

        Reboot this virtual machine.
        [root@ns0 Linux]#

Si desea copiar y pegar en su shell, las líneas siguientes realizan todos los pasos, si tiene asignado el mismo nombre de dispositivo de /dev/xvdd.

        mkdir /mnt/xs-tools
        mount /dev/xvdd /mnt/xs-tools
        cd /mnt/xs-tools/Linux/
        bash install.sh

En algunos casos el ISO de las herramientas de Xen se monta en /dev/cdrom.

        mkdir /mnt/xs-tools
        mount /dev/cdrom /mnt/xs-tools
        cd /mnt/xs-tools/Linux
        ./install.sh

5. Reinicie el sistema desde la línea de mandatos y podrá ver que se han instalado las herramientas de XenServer.

6. Ahora, XenCenter tiene más control sobre el sistema operativo dentro de la máquina virtual, y puede ver los contadores que anteriormente no estaban disponibles en el separador **Rendimiento** de XenCenter.
