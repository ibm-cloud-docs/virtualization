---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# 使用 Linux 時安裝 XenServer Tools
{: #installing-xenserver-tools-when-using-linux}

請遵循下列步驟，在使用 CentOS、Debian 及 RedHat 作業系統的虛擬機器上安裝 XenServer Tools。

這些步驟專用於客戶管理的 XenServer，而非 {{site.data.keyword.cloud}} 提供的 VSI。
{:tip}

1. 開啟 XenCenter 並建立與 XenServer 的連線。請參閱沒有設定 XenServer Tools 的新虛擬機器範例。您可以看到，對於**虛擬化狀態：**，軟體指出「未安裝工具」。

2. 用滑鼠右鍵按一下您要安裝 XenServer Tools 的虛擬機器名稱，然後按一下**安裝 XenServer Tools**。這時會出現警告，指出「在安裝 xenServer Tools 時，退出目前在虛擬機器光碟機中的任何 CD 或 DVD。」

3. 按一下**安裝 XenServer Tools** 以繼續。

在虛擬機器的 XenDesktop 主控台標籤上，您會看到已載入至 DVD 光碟機的 "xs-tools.iso" CD 映像檔。它也會提供系統內的裝置名稱。

4. 您可以透過主控台或透過系統的 SSH 階段作業執行下列步驟。安裝會遵循此範例使用 SSH。**附註：**磁碟機裝置名稱由 XenCenter 提供。

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

如果您想要複製並貼至 Shell，則在您獲指派相同的裝置名稱 /dev/xvdd 時，下列幾行會執行所有步驟。

        mkdir /mnt/xs-tools
        mount /dev/xvdd /mnt/xs-tools
        cd /mnt/xs-tools/Linux/
        bash install.sh

在某些情況下，Xen Tools ISO 會裝載至 /dev/cdrom。

        mkdir /mnt/xs-tools
        mount /dev/cdrom /mnt/xs-tools
        cd /mnt/xs-tools/Linux
        ./install.sh

5. 從指令行重新啟動系統，您可以看到已安裝 XenServer Tools。

6. 現在 XenCenter 更能控制虛擬機器內的作業系統，而且您可以在 XenCenter 的**效能**標籤中檢視先前無法使用的計數器。
