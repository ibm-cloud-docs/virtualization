---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-23"
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Installing Windows Server virtualization on Windows 2008

<!--Windows 2008 64-bit edition comes with the option to install Windows next generation virtualization application codenamed Veridian. By default the application will not be a selectable option when Adding and Removing Roles from Windows 2008.

Note: At this time, this is only available in full installation of 64-bit editions of Windows 2008. 32-bit versions and Server-core installations do not support this.-->

## Before you begin

Before you begin, review the following prerequisites.

### Hardware

* Hardware-assisted virtualization technology enabled in the BIOS
* Intel VT
* AMD-V
* Data Execution Prevention enabled in the BIOS
* Intel Execute Disable (XD)
* AMD No Execute (NX)

### Software

To enable the **Role** option for Windows Server virtualization a few patches must be installed.
1. Open a browser window and browse to %sysdir%\Windows\wsv, usually C:\Windows\wsv. Two files will be located in that folder:
    * Windows6.0-KB939853-x64.msu
    * Windows6.0-KB939854-x64.msu
2. These patches can be installed in any order. Install both patches and then restart the system.

## Installation 

1. After the system restarts, you need to add the **Role** to the system. 
2. After you add the role, the **Create Virtual Networks** dialog box appears.
**Note:** Network connectivity is lost briefly.
3. Select **Local Area Connection**, your private network adapter. Click **Continue**. The installation continues and requires you to restart. 
4. After the systems restarts, log in to the system via the Public connection. **Note:** You must log in with the same login credential that you used to install this connection. You will lose network connectivity to the interface. If not, you might receive the following error:
    * *Attempt to configure Windows Server Virtualization failed with error code 0x80078000.*
To resolve the error, go to **Start > Programs > Administrative Tools > Windows Virtualization Management** in the management console for Windows virtualization. 
5. In the right pane, click on the appropriate server. 
6. Then in the action pane, click **Virtual Network Manager**. You now see **Virtual Network Switch Management**.
7. In the left pane, click the network switch under **Add New** network switch.
8. Rename the network switch to private. 
9. Select Physical network adapter for **Connection** and select the first network adapter. All network protocols are now unbound from the private network interface.
10. **IMPORTANT** To reestablish network connectivity to private, you need to configure the new switch device, **NOT** the private interface. Go to **Start > Settings > Network Connections**. You see a new device that is called **Local Area Connection 2**.
11. Right-click this connection and go to **Properties**. 
12. Select **ipv4** and its properties. You need to configure this device with the private network interface IP address, netmask, and DNS servers. 
13. Click **OK > Close**. This configuration reenables networking on the private side. You can verify that the network is private by pinging the private IP.

RDP to the private IP to setup the public network.

### Adding a public switch 
Adding a public switch follows the same process as adding the private switch. 
1. Go back to **Virtual Network Switch Management** and select **Add new network switch**. 
2. Select **External** as the network switch type and click **Add**. 
3. Rename the switch to **Public** and select **Physical network adapter**. 
4. Select the second network adapter and click **OK**. This configuration causes the public port not to respond to the network. Configure the new public switch interface just like you did with the private one with the proper settings.
