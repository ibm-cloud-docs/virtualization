---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Setting up a virtual machine network
{: #setting-up-a-virtual-machine-network}

Network configuration on your new virtual machine is done in a few steps. You need a separate portable IP block for both the public and private network. It is assumed that you are using a virtualization offering that requires secondary on VLAN broadcast domain and not routed to VLAN (without network id/gateway/broadcast) subnet. If you do not plan to use the Private network on your virtual machine, you need only the Public subnet. Portable IP blocks can be ordered directly through the portal at [Sales->Add IP Addresses ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://manage.softlayer.com/Sales/orderAdditionalServices/subnet){: new_window} or from the [Public Network IP Manager ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://manage.softlayer.com/PublicNetwork/ipManager){: new_window}.

After you obtain your IP blocks, you can configure networking on the virtual machine. If the virtual machine is not installed, you can configure your public network block through the Operating System installation process. This process is the quickest method to configure your public network. All network configuration steps assume that the first network interface is the private network and the second network interface is the public network.

Configuring your network is different for each OS. You can find detailed information on configuring your network for the following Operating Systems. **Note:** Operating Systems that are grouped have the same network configuration.

* Windows 2008 Server Core
* Windows 2003 Standard and Enterprise
* Windows 2008 Web, Standard, Enterprise, and Datacenter
* RedHat, Fedora and, CentOS
* Ubuntu and Debian

Network configuration requires the following information for both your Public and Private IP blocks. The information can be found in the portal for Public IP blocks under [Public Network -> IP Manager ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://manage.softlayer.com/PublicNetwork/ipManager){: new_window} and Private IP blocks under [Private Network -> IP Manager ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://manage.softlayer.com/PrivateNetwork/ipManager){: new_window}.

    -IP Address
    -Gateway
    -Subnet Mask
    --Subnet conversion
    ---/29 - 255.255.255.248
    ---/28 - 255.255.255.240
    ---/27 - 255.255.255.224
    ---/26 - 255.255.255.192
    ---/25 - 255.255.255.128
    ---/24 - 255.255.255.0

## Windows 2008 Server Core
{: #windows-2008-server-core}

Windows 2008 Server Core edition does not provide a graphical interface to configure the system network. The configuration needs to be done manually by using the command prompt. You need to run the following commands. The IP addresses that are used in this example need to be replaced with the IP addresses from your IP Blocks. These commands work with older versions of Windows Server that include the [netsh ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://support.microsoft.com/kb/242468){: new_window} command.

**Example public IP block – 192.0.2.0/29**

* IP address – 192.0.2.2
* Gateway – 192.0.2.1
* Subnet Mask – 255.255.255.248

**Example private IP block – 10.0.0.0/29**

* IP address – 10.0.0.2
* Gateway – 10.0.0.1
* Subnet Mask – 255.255.255.248

**Public Network**

This command creates the public network to connect your server to the internet.

* *Netsh interface ip set address name=”Local Area Connection 2” static 192.0.2.2 255.255.255.248 192.0.2.1*

These commands create the DNS entries. If you are not using the private network, you need to replace these IP addresses with working DNS servers

* *Netsh interface ip add dns “Local Area Connection 2” 10.0.80.11*
* *Netsh interface ip add dns “Local Area Connection 2” 10.0.80.12*

**Private Network**

The following two commands configure the private network and create the persistent route for the private network. The persistent route provides your private network access to the entire private network. Access includes the DNS servers in the public network configuration. **Note:** No gateway is assigned to the private network.

* `Netsh interface IP set address name=”Local Area Connection” static 10.0.0.2 255.255.255.248`
* `Route add 10.0.0.0 mask 255.0.0.0 10.0.0.1 -p`

## Windows 2003 Standard and Enterprise
{: #windows-2003-standard-and-enterprise}

You can use Windows 2003 to configure networking by using either the command line or the graphical user interface. If want to use the command line, see the configuration instructions for Windows 2008 Server Core. These instructions also work for Windows 2003. The instructions for using the graphical interface are provided here. The IP addresses that are used in this example need to be replaced with the IP addresses from your IP Blocks.

**Example private IP block – 10.0.0.0/29**

* IP address – 10.0.0.2
* Gateway – 10.0.0.1
* Subnet Mask – 255.255.255.248

### Opening your network configurations
{: #opening-your-network-configurations}

1. Go to **Start Menu > Settings > Control Panel**
2. Open **Network Connections**
   **Note:** If you installed two network adaptors, you see “Local Area Connection” and “Local Area Connection 2”

**Public Network**

Use the following instructions to configure the public network through the windows graphical interface.

1. Right-click on **Local Area Connection > Properties** and select **Internet Protocol (TCP/IP) > Properties**.
2. Go to **General > Use the following IP address:**
3. Enter your public IP address, subnet mask, and gateway.
4. Select **Use the following DNS server addresses:**. If you are configuring the private network, use the following DNS Servers. If you are not configuring the private network, you need to provide DNS servers.
    * Preferred DNS server: 10.0.80.11
    * Alternate DNS server: 10.0.80.12
5. Click **OK**

**Private Network**

1. Right-click **Local Area Connection 2 > Properties**, select **Internet Protocol (TCP/IP) > Properties**.
2. Go to **General > Use the following IP address:** and enter your private IP address and subnet mask. Leave the gateway field empty and click **OK**
3. Click **OK** again to close the network configuration window.

To provide access to the entire private network that includes the DNS servers, you need to add a custom route to the server. Following these steps to provide private network access:

1. Go to **Start > Run** and enter *cmd* and press **OK**.
2. Run the following command: **Note:** Make sure that you replace the gateway address (10.0.0.1) with your private IP block gateway.<br/>
*“route add 10.0.0.0 mask 255.0.0.0 10.0.0.1 –p”*

## Windows 2008 Web, Standard, Enterprise, and Datacenter
{: #windows-2008-web-standard-enterprise-and-datacenter}

If you have Windows 2008, you configure networking by using either the command prompt or the graphical user interface. If you intend to use the command prompt, refer to the configuration instructions for Windows 2008 Server Core. These instructions work for all 2008 servers. The instructions for using the graphical interface are provided here. The IP addresses that are used in this example need to be replaced with the IP addresses from your IP Blocks.

**Example private IP block – 10.0.0.0/29**

* IP address – 10.0.0.2
* Gateway – 10.0.0.1
* Subnet Mask – 255.255.255.248

**Open your network configurations**

1. Go to **Start Menu > Settings > Control Panel**
2. Open **Network and Sharing Center** and click **Manage network connections**
* If you installed two network adapters, you see “Local Area Connection” and “Local Area Connection 2”.

**Public Network**

The following instructions provide detailed steps on configuring the public network through the windows graphical interface.

1. Right-click **Local Area Connection > Properties**.
2. Select **Internet Protocol Version 4 (TCP/IPv4) Properties**.
3. Go to **General > Use the following IP address:** and enter your public IP address, subnet mask, and gateway.
4. Select **Use the following DNS server addresses:**. If you are configuring the private network, use the following DNS Servers. If you are not configuring the private network, you need to provide DNS servers
    * Preferred DNS server: 10.0.80.11
    * Alternate DNS server: 10.0.80.12
5. Click **OK**

**Private Network**

1. Right-click on **Local Area Connection 2 > Properties** and select **Internet Protocol Version 4 (TCP/IPv4) > Properties**.
2. Go to **General > Use the following IP address:**, enter your private IP address and subnet mask, and click **OK**.
3. Click **OK** again to close the network configuration window.

To provide access to the entire private network which includes the DNS servers, you need to add a custom route to the server.

1. Go to **Start > Run** and enter “cmd” and click **OK**
2. Run the following command: **Note:** Replace the gateway address (_10.0.0.1_) with your private IP block gateway.
  * `route add 10.0.0.0 mask 255.0.0.0 10.0.0.1 –p`

## RedHat, Fedora, and CentOS
{: #redhat-fedora-and-centos}

Configuring the network on RedHat, Fedora and CentOS is done by manually editing configuration files. To manually edit the files, you need to log in to the virtual machine.

**Example public IP block – 192.0.2.0/29**

* IP address – 192.0.2.2
* Gateway – 192.0.2.1
* Subnet Mask – 255.255.255.248

**Example private IP block – 10.0.0.0/29**

* IP address – 10.0.0.2
* Gateway – 10.0.0.1
* Subnet Mask – 255.255.255.248

**Public Network**

The public network settings are contained in the following file. You need to edit this file with the information that is provided. Replace the example IP addresses with the IP addresses form your public IP block. If the file does not exist, create the file. If it does exist, replace all data that is in the file with the following information:
* /etc/sysconfig/network-scripts/ ifcfg-eth1
      DEVICE=eth1
      BOOTPROTO=static
      BROADCAST=192.0.2.7
      IPADDR=192.0.2.2
      NETMASK=255.255.255.248
      NETWORK=192.0.2.0

      GATEWAY=192.0.2.1
      ONBOOT=yes

**Private Network**

The private network settings are in the following file. You need to edit this file with the information that is provided. Replace the example IP addresses with the correct IP addresses from your Private IP block. If the file does not exist, create the file. If the file does exist, replace all data in the file with the following information: **Note:** The private network *WILL NOT* have a default route, so the *GATEWAY* is not defined.

* /etc/sysconfig/network-scripts/ ifcfg-eth0
      DEVICE=eth0
      BOOTPROTO=static
      IPADDR=10.0.0.2
      NETMASK=255.255.255.248
      ONBOOT=yes

Finally, a new route is required to provide private access to the entire private network to include the DNS server, which is done by editing the following file. This fill does not exist, so it needs to be created. **NOTE:** Replace "10.0.0.1" in the example with your private subnet IP gateway.

* /etc/sysconfig/network-scripts/route-eth0
      10.0.0.0/8 via 10.0.0.1

**DNS Configuration**

The primary and secondary DNS configurations are contained in a separate file. You need to edit the file with the following information. If this virtual machine does not have access to the private network, you need to replace the server IPs with the IP addresses of the DNS servers you want to use.

* /etc/resolv.conf
      nameserver 10.0.80.11
      nameserver 10.0.80.12

For these changes to take effect, you need to restart networking on the server. You can restart networking by running the following command:

* `service network restart`

## Ubuntu and Debian
{: #ubuntu-and-debian}

You configure Ubuntu and Debian networks through a single configuration file. You need to edit this configuration file with the following information. To edit this file, you need to have root access to the server. The base installation of Ubuntu does not provide a root login. However, the user that was created in the installation process can access sudo. You need to use the sudo command to edit the file if you are running Ubuntu.

**Example public IP block – 192.0.2.0/29**

* IP address – 192.0.2.2
* Gateway – 192.0.2.1
* Subnet Mask – 255.255.255.248

**Example private IP block – 10.0.0.0/29**

·IP address – 10.0.0.2
·Gateway – 10.0.0.1
·Subnet Mask – 255.255.255.248

**Public and Private Network**

Edit the following file with any text editor and replace example IP addresses with the IPs that are in your Public and Private IP blocks.

* /etc/network/interfaces
      auto lo
      iface lo inet loopback

      auto eth1
      iface eth1 inet static
      address 192.0.2.2
      netmask 255.255.255.248
      gateway 192.0.2.1

      auto eth0
      iface eth0 inet static
      address 10.0.0.2
      netmask 255.255.255.248

      #Static route for backend service network
      up route add -net 10.0.0.0/8 gw 10.0.0.1

**DNS Configuration**

The primary and secondary DNS configuration is contained in a separate file. You need to edit this configuration file with the following information. If this virtual machine does not have access to the private network, you need to replace the server IPs with the IP addresses of the DNS servers that you want to use.

* /etc/resolv.conf
      nameserver 10.0.80.11
      nameserver 10.0.80.12

For these changes to take effect, you want to restart networking on the server. You can restart the network by running the following command:

* */etc/init.d/network restart*

**Note:** 192.0.2.0/24 is used as Public IP Documentation per RFC1166 and RFC5737. You do not use these IP addresses on your servers.
