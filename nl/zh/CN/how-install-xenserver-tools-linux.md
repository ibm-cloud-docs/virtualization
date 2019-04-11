---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# 使用 Linux 时安装 XenServer Tools
{: #installing-xenserver-tools-when-using-linux}

请执行以下步骤在使用 CentOS、Debian 和 RedHat 操作系统的虚拟机上安装 XenServer Tools。

这些步骤专门针对的是客户管理的 XenServer，而不是 {{site.data.keyword.cloud}} 提供的 VSI。
{:tip}

1. 打开 XenCenter 并建立与 XenServer 的连接。请参阅未设置 XenServer Tools 的新虚拟机的示例。您可以看到对于**“虚拟化状态：”**，软件显示“工具未安装”。

2. 右键单击要安装 XenServer Tools 的虚拟机的名称，并单击**安装 XenServer Tools**。这将显示一条警告，声明“安装 xenServer Tools 时，将弹出虚拟机 CD 驱动器中当前的任何 CD 或 DVD。”

3. 单击**安装 XenServer Tools** 以继续。

在虚拟机的 XenCenter 的“控制台”选项卡上，您会看到装入到 DVD 驱动器的 CD 映像“xs-tools.iso”。控制台还会为您提供系统中的设备名。

4. 可以通过控制台或通过与系统的 SSH 会话来执行以下步骤。遵循此示例时，安装将使用 SSH。**注：**驱动器设备名由 XenCenter 提供。

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

        对此虚拟机进行以下更改：
        * 要安装或升级的包：
        - kernel-xen-2.6.18-53.1.13.el5.xs4.1.0.24.x86_64.rpm
        - xe-guest-utilities-4.1.0-257.x86_64.rpm

        Continue? [y/n] y

        Preparing...########################################### [100%]
        1:xe-guest-utilities##########################################[100%]
        Preparing...########################################### [100%]
        package kernel-xen-2.6.18-92.el5 (which is newer than kernel-xen-2.6.18-53.1.13.el5.xs4.1.0.24) is already installed

        Reboot this virtual machine.
        [root@ns0 Linux]#

如果要复制并粘贴到 shell 中，以下行将执行所有步骤（如果为您分配了相同的设备名 /dev/xvdd）。

        mkdir /mnt/xs-tools
        mount /dev/xvdd /mnt/xs-tools
        cd /mnt/xs-tools/Linux/
        bash install.sh

在某些情况下，Xen Tools ISO 会安装到 /dev/cdrom。

        mkdir /mnt/xs-tools
        mount /dev/cdrom /mnt/xs-tools
        cd /mnt/xs-tools/Linux
        ./install.sh

5. 通过命令行重新启动系统，您可以看到 XenServer Tools 已安装。

6. 现在，XenCenter 对虚拟机中的操作系统具有更多控制权，并且您可以在 XenCenter 的**性能**选项卡中查看先前不可用的计数器。
