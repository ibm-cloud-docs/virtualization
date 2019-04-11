---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Instalando o XenServer Tools ao usar o Linux
{: #installing-xenserver-tools-when-using-linux}

Siga estas etapas para instalar o XenServer Tools em suas máquinas virtuais que usam os sistemas operacionais CentOS, Debian e RedHat.

Essas etapas são especificamente para XenServers gerenciados pelo cliente, não para VSIs fornecidas pelo {{site.data.keyword.cloud}}.
{:tip}

1. Abra o XenCenter e estabeleça uma conexão com o XenServer. Consulte o exemplo de uma nova máquina virtual que não tem o XenServer Tools configurado. É possível ver isso para **"Estado de virtualização:"**, o software diz, "Ferramentas não instaladas".

2. Clique com o botão direito no nome da máquina virtual em que deseja instalar o XenServer Tools e, em seguida, clique em **Instalar XenServer Tools**. Aparecerá um aviso indicando que "Qualquer CD ou DVD atualmente na unidade de CD da máquina virtual deve ser ejetado quando o xenServer Tools for instalado".

3. Clique em **Instalar XenServer Tools** para continuar.

Na guia do console do XenCenter para a sua máquina virtual, há uma imagem de CD de "xs-tools.iso" que é carregada na unidade de DVD. Ela também fornece o nome do dispositivo dentro do sistema.

4. É possível executar as etapas a seguir por meio do console ou por meio de uma sessão SSH para o sistema. Ao seguir esse exemplo, a instalação usa SSH. **Nota:** o nome do dispositivo da unidade é fornecido pelo XenCenter.

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

        As mudanças a seguir são feitas para esta máquina virtual:
        * pacotes a serem instalados ou submetidos a upgrade:
        - kernel-xen-2.6.18-53.1.13.el5.xs4.1.0.24.x86_64.rpm
        - xe-guest-utilities-4.1.0-257.x86_64.rpm

        Continuar? [s/n] s

        Preparing...########################################### [100%]
        1:xe-guest-utilities##########################################[100%]
        Preparing...########################################### [100%]
        package kernel-xen-2.6.18-92.el5 (which is newer than kernel-xen-2.6.18-53.1.13.el5.xs4.1.0.24) is already installed

        Reinicialize essa máquina virtual.
        [root@ns0 Linux]#

Se quiser copiar e colar em seu shell, as linhas a seguir executarão todas as etapas se você tiver designado o mesmo nome de dispositivo de /dev/xvdd.

        mkdir /mnt/xs-tools
        mount /dev/xvdd /mnt/xs-tools
        cd /mnt/xs-tools/Linux/
        bash install.sh

Em alguns casos, o ISO do Xen Tools é montado em /dev/cdrom.

        mkdir /mnt/xs-tools
        mount /dev/cdrom /mnt/xs-tools
        cd /mnt/xs-tools/Linux
        ./install.sh

5. Reinicie seu sistema por meio da linha de comandos e veja que o XenServer Tools está instalado.

6. Agora o XenCenter tem mais controle do sistema operacional dentro da máquina virtual e é possível visualizar os contadores indisponíveis anteriormente na guia **Desempenho** do XenCenter.
