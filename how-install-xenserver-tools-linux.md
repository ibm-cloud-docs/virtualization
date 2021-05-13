---
copyright:
  years: 2014, 2018
lastupdated: "2019-09-24"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Installing XenServer tools with Linux
{: #installing-xenserver-tools-when-using-linux}

Follow these steps to install XenServer Tools on your virtual machines that use CentOS, Debian, and RedHat operating systems.

These steps are specifically for customer-managed XenServers, not {{site.data.keyword.cloud}} provided virtual server instances.
{:tip}

1. Open XenCenter and establish a connection to your XenServer. See the example of a new virtual machine that does not have XenServer Tools set up. You can see that for **"Virtualization State:"**, the software says, "Tools not installed".

2. Right-click on the name of the virtual machine that you want to install XenServer Tools and then click **Install XenServer Tools**. A warning appears stating that, "Any CD or DVD currently in the virtual machine's CD drive ejected when xenServer Tools are installed."

3. Click **Install XenServer Tools** to continue.

On the console tab of XenCenter for your virtual machine, you see a CD image of "xs-tools.iso" that is loaded into the DVD drive. It also provides you with the device name within the system.

4. You can do the following steps through either the console or through an SSH session to the system. By following this example, the installation uses SSH. **Note:** The drive device name is provided by XenCenter.

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

If you would like to copy and paste into your shell, the following lines perform all of the steps, if you are assigned the same device name of /dev/xvdd.

        mkdir /mnt/xs-tools
        mount /dev/xvdd /mnt/xs-tools
        cd /mnt/xs-tools/Linux/
        bash install.sh

In some cases, the Xen Tools ISO is mounted to /dev/cdrom.

        mkdir /mnt/xs-tools
        mount /dev/cdrom /mnt/xs-tools
        cd /mnt/xs-tools/Linux
        ./install.sh

5. Restart your system from the command line and you can see that XenServer Tools are installed.

6. Now XenCenter has more control of the operating system within the virtual machine, and you can view the previously unavailable counters in the **Performance** tab of XenCenter.
