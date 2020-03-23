---
copyright:
  years: 2014, 2020
lastupdated: "2020-03-23"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Getting started with XenCenter
{: #getting-started-with-xencenter}

## Before you begin
{: #before-you-begin-xencenter}

Before you begin, review the following prerequisites:

- New ranges of portable IP addresses (public and private) that are routed as "Secondary on VLAN". This example solution to the setup of a new VM requires that you have usable IPs available on the Private Network. You can order subnets from [{{site.data.keyword.slportal_full}} ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://control.softlayer.com/network/subnets/order).
- Ability to connect to the {{site.data.keyword.cloud}} Private Network through the VPN. For more information about VPN access, see [Enable access to the {{site.data.keyword.cloud_notm}} infrastructure private network](/docs/customer-portal?topic=customer-portal-getting-started#enable-private-network).
- Citrix XenCenter is installed to your local system. <!-- . http://downloads.service.softlayer.com/citrix/xen/-->

**Note:** Your server is pre-configured with various of templates to help you get off and running with XenServer quickly.

## Creating a virtual machine with XenCenter
{: #creating-a-virtual-machine-with-xencenter}

Use the following steps to help you create a virtual machine with XenCenter.

1. Make sure that you are connected to the {{site.data.keyword.cloud_notm}} Private Network through the VPN. Establish a connection through SSL VPN or PPTP.
2. Open XenCenter and click **"Add your XenServer"**.
3. Enter your server host name, your user name, and your password. You need to use your server's Private IP address (looks like 10.x.x.x), the user name `root`, then your server's root password. This information is available in the {{site.data.keyword.slportal}} by going to your [Devices ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://control.softlayer.com/devices){: new_window} then clicking the name of your XenServer. Click **Connect**.
4. If you receive a Free License activation screen, complete this information now.
5. Right-click your {{site.data.keyword.cloud_notm}} name and select **New VM...**.<!--You can now create your first Virtual Machine. Create a CentOS virtual machine with a disk of 10 GB and have both Public and Private Networks functioning-->
6. Select the operating system that you want to use for the new virtual machine and then click **Next**. **Note:** Some templates require that you provide your own media.<!--Because you are using CentOS, you can use {{site.data.keyword.BluSoftlayer_notm}} private mirrors for CentOS to get our installation going.Select a version of CentOS and then click **Next**.-->
7. Enter an appropriate name and description for the new virtual machine.
8. Enter the location of the guest operating system installation media and click **Next**. <!-- In the example, {{site.data.keyword.BluSoftlayer_notm}} a CentOS mirror is used as installation media. Provide the Install URL of: http://mirrors.service.softlayer.com/centos/5/os/x86_64 and click **Next**.
  *A trailing ‘/’ at the end of the URL can sometimes break the installation.*
  *This mirror is available only on the {{site.data.keyword.BluSoftlayer_notm}} Private Network. The full mirror's contents are  available here: http://mirrors.service.softlayer.com/.-->
9. Select the number of CPUs and amount of memory allocation. (You are building a streamlined system and you do not need too much RAM. So, 512 MB is more than enough.). Click **Next**.
10. Select your virtual disks to allocate disk space to your VM.<!--Remember that this is like adding hard disks, it is not like partitioning your system. Partitioning is done during the installation of the OS.--> The default size is 8 GB for this template. This amount is more than enough for the server that you are building. *Optional: You can make the disk larger to suit your needs by highlighting the disk and clicking **Edit...**.* Click **Next**.
11. Add or remove virtual network interfaces for the virtual machine. The defaults are acceptable, unless you do not want your system to be able to communicate on the Private Network. Click **Next**.
12. The configuration is complete. Leave "Start VM automatically" selected and click **Finish**. The installation begins. You return to the main screen. You can see the new VM listed on the left.
13. Select your new VM and click the **Console** tab. <!--You can now see that your system is booted into the CentOS installer awaiting your input.-->
14. <!--All of the parameters of a CentOS installation are outside of the scope of this article and will need to be customized by your System Administrator, but this article will provide some specific pieces of information that you need to complete the installation. Select your language to get started. The CentOS installer will then ask you for assistance in configuring the Networking Devices in the system.--> Select **eth0 - xen Virtual Ethernet** and click **OK**.
  <!--![14](images/14.png)-->
15. <!--In the pre-requisite notes, we made sure that we already had a set of Portable IP Addresses routed as "Secondary on VLAN" ready for this installation.--> Make sure that you have the information ready to configure TCP/IP. You need to manually configure the interface with IPV4 support and disable IPV6 support.
  <!--[15](images/15.png)-->
16. Confirm that you are aware of how to use your Private IP Subnet (10.17.37.240/29) and click **OK**<!-- to go to the CentOS installer-->. You and your system administrator can install the guest operating system according to your company guidelines. For more informatin about static and portable IP blocks, see [Getting started with subnets and IPs](/docs/subnets?topic=subnets-getting-started).

You have created a new VM.
