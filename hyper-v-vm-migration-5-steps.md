---
copyright:
  years: 2014, 2021
lastupdated: "2021-09-21"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Migrating a Hyper-V virtual machine
{: #migrating-a-hyper-v-virtual-machine}

## Before you begin
{: #before-you-begin-migrating-a-hyper-v-virtual-machine}

Before you begin, review the following prerequisites.

* A compatible server.
* A method of connecting from Source server to Destination Server.
* Source and destination server must reside on the same VLAN. If they do not reside on the same VLAN, reissuing an IP for the virtual machine is necessary (requires opening a support ticket).
* Virtual machines with working network uplinks and installed working/supported software.

## Why you might need to relocate your Hyper-V virtual machine
There are two common reasons that you might need to relocate your Hyper-V Virtual Machine:
* The VM is on hardware that is not functioning properly,
* The current host server is running low on resources.
In either event, the Hyper-V migration can be completed quickly if you have the previously mentioned requirements, complete the following steps.

1. Log in to the source server and open the Hyper-V Manager. Select the virtual machine that you want to migrate to the destination server.
2. Shut down the virtual machine that you want to migrate. Then select **Export** under the Server actions list enter the location of the export file.
3. Now, using RDP on the source server, you can log in to the destination server with the C: drive mounted to bring the file over.

   You are transferring the file via a resource mount by using RDP. You can choose the transfer method that is most comfortable to you for this process (Windows Sharing, Resource mount via RDP, FTP, and other transfer methods).
   {: tip}

4. Make sure that the file you are exporting is in the default location for Hyper-V virtual hard disks (VHD). The default location is `C:\Users\Public\Documents\Hyper-V\Virtual hard disks`. If you changed this default location and are not sure of what it was, you can select **Hyper-V Settings > Import Virtual Machine** to view the location.
5. After you locate the exported file and click **Import**, the virtual machine populates into your Hyper-V Manager with all configurations and files it had previously. The server is now online and working. If your server did not match the requirements and the destination server resides in a different VLAN, you need to Re-IP the virtual machine with a portable subnet that is routed as Secondary on VLAN.
