---



copyright:
  years: 2014, 2018
lastupdated: "2018-05-18"


---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Setting up Hyper-V

The process of setting up Hyper-V includes the initial configuration of Hyper-V, the creation of a virtual machine, and the network configuration on the new virtual machine. The following guidelines explain these processes. 

## Using Hyper-V Manager

The Hyper-V Manager is the management console that you use to operate Hyper-V. From the Hyper-V Manager you can create, start, stop, delete, and configure all of your virtual machines. The management console is also where you set the default location to store your virtual hard disks and the default location to store your virtual machine configuration files.

You can find Hyper-V Manager under **Administrative Tools** in the Windows Control Panel. On a new installation of Windows 2008, you can find **Administrative Tools** under Programs in the Start Menu. After you open the Hyper-V Manager program, a new window appears on the screen. Take a moment to familiarize yourself with this screen. All configurations and use of your virtual machines are done here.

## Configuring Hyper-V

The first configuration to check is the storage location for your virtual hard disks. From the Hyper-V Manager window, on the left side you see **Hyper-V Manager** and the server name. Click your server name and you see the information that is available to the Hyper-V service on your server. Now, the middle of the screen is empty since shows that no virtual machines are installed. On the right side of the screen, you see a link for **Hyper-V Settings**. Clicking the link brings up the basic settings for Hyper-V.

The primary setting that you need to look at is **Virtual Hard Disks**. On the right side, you see the default location stores your virtual hard disks. The default location is:

`C:\Users\Public\Documents\Hyper-V\Virtual Hard Disks`

When you create a virtual machine, you can specify a specific place if you do not want to store that specific virtual hard disk in the default location. For example, you want to store your virtual hard disks on a second, larger drive (Drive D in this example) and you want those virtual drives to be stored in the **VirtualMachine** folder, enter `D:\VirtualMachine` into the field.

****

You can also manually browse through folders to select the location you want to use. After you select a location, click **OK**.

## Configuring network devices

Before you configure virtual network devices, determine which network adapters are connected to the private and public networks. From the start menu, click **Network Connections**. You see two network devices here. Make sure that you note that your devices are named **PrivateNetwork** and **PublicNetwork**. Right-click **PrivateNetwork** and select **Properties**. You see **Connect using:**, which shows the device name for the private network adapter. Write down this name as you need it when you create your virtual private network device.

### Configuring a private network device

Now that the default location to store your virtual hard disks is configured, you need to configure the virtual network devices that the virtual machines use. To open the Virtual Network Manager, click **Virtual Network Manager** on the right side of the Hyper-V Manager window that is labeled.

The first time that you open this window, you see Virtual Networks on the left with the only listed option, **New Virtual Network**. On the right side, you have three options: 
* External 
* Internal 
* Private. 
Make sure that **External** is selected and click **Add**. 

Continue entering information into the remaining fields: 
* **Name** The name that is associated with this network device. For this example, for the example, use _Private_ to indicate that this device is connected to the IBM Cloud private network. 
* **Connection type** Select the device that corresponds to your private network. This name is the device name that you found earlier in the **Network Connections** window. After you select the correct device, click **OK**. You are presented with a warning that your network connection might be lost, click **Yes** to continue.

### Configuring a public network device

Your Private Network device is now installed. Repeat the preceding steps for the Public network. For this example, use the name **Public** for the device name. Under connection type, select the other network device that was not used in the Private network setup. After you create both the public and private network devices, click **OK**.

## Obtaining installation media

Now that the virtual network devices are created, you need to obtain the installation media. The installation media that is used in this example is a CD/DVD image file. IBM Cloud supports the following Operating Systems for Hyper-V:
* Windows 2008
* Windows 2003 
* CentOS
* Fedora
* Ubuntu. 
**Note:** If you already have the installation media, go to **Creating a Virtual Machine**.

The installation media for [CentOS ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://centos.org){: new_window}, [Fedora ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://fedoraproject.org/){: new_window}, and [Ubuntu ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://www.ubuntu.com/){: new_window} are available from their respective websites. All three OSs are freely licensed and no further action needs to be taken on the part of the user.

## Creating a virtual machine

Now that you have the installation media, you can create a virtual machine from the Hyper-V Manager. Click **New > Virtual Machine** to start the **New Virtual Machine Wizard**.

On the first screen, click **Next** and enter a name for the new virtual machine. This name can be changed at a future date. You can change the location of the virtual hard disk. The default value that you previously set is used unless you specify a different location. After you enter the information, click **Next**.

Now, you need to allocate memory to the server. Generally, you provide at least the minimum recommended memory as specified by the operating system. After you enter the amount of memory to allocate, click **Next**. 

Next, set the first network adapter. If a Linux Operating System is being installed on the virtual machine, select **Not Connected**. If Windows is being installed, select **Private**, then click **Next**.

You can change the following settings for the virtual disk: file name, the storage location, and size. Make any applicable changed and click **Next**. 

The installation media needs to be set. Select **Install an operating system from a boot CD/DVD-ROM > Image file (.iso)** to set the location of the CD/DVD image file for your installation media. Click **Next**, view the summary and click **Finish** to close the window.

Lastly, you need to create the virtual network adapters. The new virtual machine is now listed under the **Virtual Machines** section of the Hyper-v Manager. Right-click the new virtual machine click **Settings**. 

### Configuring Virtual Network Adapter for Windows Operating System

A list of hardware is now available for the virtual machine. In the list, you see **Network Adapter** and **Private**. If you see **Not Connected**, click the Network Adapter, select **Private**, and click **Apply**. 

Next, click **Add Hardware > Network Adapter > Add** to add the Network Adapter to the hardware.

On the right pane, select **Public** from the drop-down and click **OK**.

### Configuring Virtual Network Adapter for Linux Operating System

A list of hardware is now available for the virtual machine. Click **Network Adapter > Remove** and select **Add Hardware**. The list on the right changes. 
From the list, select **Legacy Network Adapter**. **Note:** This option is different from network adapter that is not used on a Linux virtual machine. 
Click **Add** to add this legacy network adapter to the hardware list. The new legacy network adapter is automatically selected. 
Select **Private** from the drop-down list and click **Apply**. 
Repeat the preceding steps to add the **Legacy Network Adapter**. However, select **Public**. 
After the new legacy network is added, click **OK**.

## Installing a guest Operating System

The virtual machine is now ready to be started. Right-click the virtual machine and select **Connect**. The virtual machine console opens. From the **Action** menu, select **Start**. The new virtual machine starts by using the installation media that you selected. Now, you run through the operating system installation. During the OS installation process, it is recommended that you configure the public network settings. The public network is the second network interface. When the OS installation is complete, you have a working virtual machine. If a public network is configured, you can access the virtual machine remotely.

The final step in the installation process is configuring the private network. If the virtual machine is not connected to the private network, the installation process is complete. For more information about configuring the private network, see [Setting up a virtual machine network](/docs/infrastructure/virtualization/virtual-machine-network-setup.html).
