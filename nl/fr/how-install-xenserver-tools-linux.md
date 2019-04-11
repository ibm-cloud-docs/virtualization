---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Installation de XenServer Tools lors de l'utilisation de Linux
{: #installing-xenserver-tools-when-using-linux}

Pour installer XenServer Tools sur vos machines virtuelles qui utilisent les systèmes d'exploitation CentOS, Debian et RedHat, procédez comme suit :

Ces étapes concernent spécifiquement les serveurs XenServer gérés par les clients, et non les instances de serveur virtuel fournies par {{site.data.keyword.cloud}}.
{:tip}

1. Ouvrez XenCenter et établissez une connexion avec votre serveur XenServer. Reportez-vous à l'exemple illustrant une nouvelle machine virtuelle pour laquelle XenServer Tools n'est pas configuré. Comme vous pouvez le constater, la zone **"Virtualization State:"**, indique "Tools not installed".

2. Cliquez avec le bouton droit de la souris sur le nom de la machine virtuelle sur laquelle vous souhaitez installer XenServer Tools, puis cliquez sur **Install XenServer Tools**. Le message d'avertissement suivant s'affiche : "Any CD or DVD currently in the virtual machine's CD drive ejected when xenServer Tools are installed." 

3. Cliquez sur **Install XenServer Tools** pour continuer. 

Sur l'onglet de la console de XenCenter pour votre machine virtuelle, vous voyez qu'une image de CD de "xs-tools.iso" est chargée dans l'unité de DVD. Le nom de l'unité dans le système est également indiqué. 

4. Vous pouvez exécuter les étapes suivantes via la console ou en ouvrant une session SSH avec le système. Dans le cadre de cet exemple, l'installation utilise SSH. **Remarque :** Le nom d'unité est fourni par XenCenter.

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

Vous pouvez effectuer un copier-coller dans votre interpréteur de commandes (les lignes suivantes effectuent toutes les étapes), si le même nom d'unité /dev/xvdd vous est affecté.

        mkdir /mnt/xs-tools
        mount /dev/xvdd /mnt/xs-tools
        cd /mnt/xs-tools/Linux/
        bash install.sh

Dans certains cas, l'image ISO de Xen Tools es montée dans /dev/cdrom.

        mkdir /mnt/xs-tools
        mount /dev/cdrom /mnt/xs-tools
        cd /mnt/xs-tools/Linux
        ./install.sh

5. Après avoir redémarré votre système à partir de la ligne de commande, vous pourrez constater que XenServer Tools est installé. 

6. A présent, XenCenter a davantage de contrôle sur le système d'exploitation dans la machine virtuelle et vous pouvez visualiser les compteurs non disponibles précédents dans l'onglet **Performance** de XenCenter.
