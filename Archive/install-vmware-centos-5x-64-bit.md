---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Installing VMware on CentOS 5.x (64-bit)
{: #installing-vmware-on-centos-5-x-64-bit-}

Before you begin, log in to the server with the root user.

## Preparing to install VMware
{: #preparing-to-install-vmware}

1. Download the [VMware installer ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://vmware.com/download/server/){: new_window}. Click the download link, accept the EULA, and download the LinuxTarball (VMware-server-1.0.3-44356.tar.gz in this example):

* `# wget –O vmware-server.tar.gz https://download3.vmware.com/software/vmserver/VMware-server-1.0.3-44356.tar.gz`

2. After you download the software, you will need to get a license key (which is free in the free version of VMWare Server). To register, see [VMware ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://register.vmware.com/content/registration.html){: new_window}.

3. Extract the .tar file:

* `# tar -xzvf vmware-server.tar.gz`

## Before you begin installation
{: #before-you-begin-installation}

Before you begin the installation, review the following prerequisites:

```
# yum update
# yum install libXtst.i386
# yum install libXrender.i386
# yum install xinetd
```

## Installing VMWare Server
{: #installing-vmware-server}

1. Go to the directory:

* `# cd vmware-server-distrib/`

2. Run the VMware install script:

* `# ./vmware-install.pl`

3. The installation presents basic questions and wants to know what directories to create and install certain parts of VMware into. From here, accept the defaults. Accept the license agreement to proceed with the installation.

4. You might be asked this question: `None of the pre-built vmmon modules for VMware Server is suitable for your running kernel. Do you want this program to try to build the vmmon module for your system (you need a C compiler installed on your system)?`
* Answer `yes` to this question (which is the default).

## Setting up VMware networking
{: #setting-up-vmware-networking}

You are prompted with the following questions:

1. "Do you want to network your virtual machines?"

Answer **yes**. This response creates a network for your public network device so that you can access the internet on your virtual machines.

2. “Your computer has multiple ethernet interfaces available: eth0, eth1. Which one do you want to bridge to vmnet0?”
Type *eth1* and press Enter. All {{site.data.keyword.cloud}} servers are set up with the public network running on eth1 and the private network runing on eth0. In VMware, the default bridge device for vmnet0 is eth0.

## Bridging the private network
{: #bridging-the-private-network}

You are prompted with the following question, which can be answered as **yes** or **no**:
*“Do you want to configure another bridged network?”

If you plan on running services or other applications on your private network, then proceed with “yes” to this question (unless you know you aren't using the private network), and a network bridge is create to your private network. After you press enter, it will automatically use eth0 as the interface, as that is the only one left available (since you have only two network cards in the server).

## Other Networking Settings
{: #other-networking-settings}

You are presented with a few other questions about the network setup of VMware server. Proceed with the following recommendations:

* *“Do you want to be able to use NAT networking in your virtual machines?”*

- Proceed with “yes”

* *“Do you want this program to probe for an unused private subnet?”*

- Proceed with “yes”

- After this process completes, make sure that you don't configure another NAT network.

* *“Do you want to be able to use host-only networking in your virtual machines?”*

- Proceed with “yes”

* *“Do you want this program to probe for an unused private subnet?”*

- Proceed with “yes”

- After this process completes, make sure that you don't configure another host-only network.

## Specifying listening port
{: #specifying-listening-port}

The next question you are asked is what port you are wanting VMware Server to listen on. You can leave the default port as 904.

## Storing the virtual machines
{: #storing-the-virtual-machines}

The next question that the installer asks is *“In which directory do you want to keep your virtual machine files?”*. The default place is /var/lib/vmware/virtual machines. Make sure to place the virtual machines in a place where you have plenty of disk space, such as a redundant RAID array or a large secondary hard disk. Always make sure that you have enough room for a virtual machine. In this case, you could use a mount point /data/vm that is mounted to a large disk.

## Providing the serial number for VMware
{: #providing-the-serial-number-for-vmware}

The final part of the installation requires you to insert a VMware license key and serial number. If you don't have a license key yet, see the [VMware site ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://register.vmware.com/content/registration.html){: new_window}. If you have the serial number for this VMware Server, insert it into the prompt and then press enter.

Now, you see something similar to the following statement:

    “The configuration of VMware Server 1.0.3 build-44356 for Linux for this running kernel completed successfully.”

VMware is now set up on your server. You now need to download the VMware Server Console, which is the GUI client for your VMware Server, configure, and install virtual machines.

## Downloading VMware Server Console
{: #downloading-vmware-server-console}

The VMware Server Console is the client application for VMware Server. You use the console to manage the VMware Server in which you can create, configure, and install virtual machines. To install this application, download the VMware Server Windows client package from [VMware downloads ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://vmware.com/download/server/){: new_window}. This package is the .zip file. After it downloads, extract the package and install the VMware-console-1.0.3-x file. When the installation completes, you are done installing the VMware Server Console and you are ready to configure your VMware Server.

## Logging in to the VMware Console
{: #logging-in-to-the-vmware-console}

Open the VMware Server Console from the computer you installed it on. When it loads, you are prompted with a “Switch Host” (login) screen. VMware Server uses the Linux system username and password to authenticate users, so you need to use the user names (root in particular) to log in to VMware. Use the following credentials:

* **Hostname:** IP address plus port (for example 67.228.160.201:904)<br />
* **User name:** root<br />
* **Password:** password (use the real root password of the system)

## Configuring the firewall rules (IPTables)
{: #configuring-the-firewall-rules-iptables-}

If you have trouble connecting to the VMware server and it is not an authentication issue (if you get a username and password error then you have a bad user or password), then your firewall might be blocking you from connecting to the VMware Server. As a resolution, try adding the following IPTable rule into your /etc/sysconfig/iptablesfile. **Note:** Make sure that the naming convention follows your server configuration:

- `# -A FWALL-INPUT -p tcp -m tcp -s 0/0 --dport 904 -j ACCEPT`
