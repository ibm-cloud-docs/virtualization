---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Enabling public access to VMware ESXi

By default, the ESXi Host is installed with the service console only on the private network. Thus preventing public traffic to and from the public network on the ESXi Host.

To get started, you need to connect to the server by using a VMware vSphere Client on the private interface for the server.

You need to have the ‘Primary’ Public IP information of the server to complete the setup. Information regarding the Public IP of the server can be found in the [{{site.data.keyword.slportal_full}} ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://control.softlayer.com/){: new_window}. 
{:tip}

After you log in to the ESXi Host, follow these steps to enable the Public Interface:

1. You might need to log in to the server over SSH by using the Private IP to correct the routing.
* Run `esxcfg-route -l` to determine the current routes
* Run `esxcfg-route -a 10.0.0.0/8 [your servers private gateway ip]`
* Run `esxcfg-route [your servers public gateway]` to ensure that the public gateway is the default

Log in to vSphere and follow these steps:

1. Click the Configuration tab and click **Networking**.
2. For vSwitch1, click **Properties > Add** 
3. Choose **VM Kernel** and click **Next**
* The settings can be left as they are. You can relabel the network to "VMKernelPublic" for identification purposes.
4. Enter the server Public IP information.
5. For the VM Kernel default gateway, click **Edit**, enter the public gateway IP, and click **OK**. 
* Click **OK**. **Note:** A disconnection is normal and expected.*

You can now access the server by using both the Public and Private IP addresses.

Enabling Public IP access to ESXi contains inherent security risks. Make sure that you take the appropriate steps to restrict access to ESXi to only those users who need it.
{:tip}
