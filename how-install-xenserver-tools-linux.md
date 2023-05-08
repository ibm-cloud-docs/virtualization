---
copyright:
  years: 2014, 2023
lastupdated: "2023-04-08"

subcollection: virtualization
---

{{site.data.keyword.attribute-definition-list}}

# Installing XenServer tools with Linux
{: #installing-xenserver-tools-when-using-linux}

Use the followings steps to install XenServer Tools on your virtual machines that use CentOS, Debian, and Red Hat operating systems.
{: shortdesc}

These steps are specifically for customer-managed XenServers, not {{site.data.keyword.cloud}} provided virtual server instances.
{: important}

1. Open XenCenter and establish a connection to your XenServer. See the example of a new virtual machine that does not have XenServer Tools set up. You can see that for **"Virtualization State:"**, the software says, "Tools not installed".
2. Right-click on the name of the virtual machine that you want to install XenServer Tools and then click **Install XenServer Tools**. A warning appears stating that, "Any CD or DVD that is in the virtual machine's CD drive ejects when xenServer Tools are installed."
3. Click **Install XenServer Tools** to continue.

   On the console tab of XenCenter for your virtual machine, you see a CD image of "xs-tools.iso" that is loaded into the DVD drive. It also provides you with the device name within the system.

4. You can complete the following steps through either the console or through an SSH session to the system. By following this example, the installation uses SSH. The drive device name is provided by XenCenter.

   ```text
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
   ```
   {: codeblock}

   If you want to copy and paste into your shell, the following lines perform all of the steps, if you are assigned the same device name of /dev/xvdd.

   ```text
   mkdir /mnt/xs-tools
   mount /dev/xvdd /mnt/xs-tools
   cd /mnt/xs-tools/Linux/
   bash install.sh
   ```
   {: pre}

   In some cases, the Xen Tools ISO is mounted to /dev/cdrom.

   ```text
   mkdir /mnt/xs-tools
   mount /dev/cdrom /mnt/xs-tools
   cd /mnt/xs-tools/Linux
   ./install.sh
   ```
   {: pre}

5. Restart your system from the command line and you can see that XenServer Tools are installed.
6. Now XenCenter has more control of the operating system within the virtual machine, and you can view the previously unavailable counters in the **Performance** tab of XenCenter.
