---
copyright:
  years: 2014, 2023
lastupdated: "2023-04-08"

subcollection: virtualization

keywords: ESXi, VMware

---

{{site.data.keyword.attribute-definition-list}}

# Enabling public access to VMware ESXi
{: #enabling-public-access-to-vmware-esxi}

By default, the ESXi Host is installed with the service console only on the private network. Thus preventing public traffic to and from the public network on the ESXi Host.

To get started, you need to connect to the server by using a VMware vSphere Client on the private interface for the server.

You need to have the ‘Primary’ public IP information of the server to complete the setup.
{: tip}

After you log in to the ESXi Host, use the following steps to enable the public interface.

1. You might need to log in to the server over SSH by using the Private IP to correct the routing.

   * Run `esxcfg-route -l` to determine the current routes
   * Run `esxcfg-route -a 10.0.0.0/8 [your servers private gateway ip]`
   * Run `esxcfg-route [your servers public gateway]` to make sure that the public gateway is the default

1. Log in to vSphere.
1. Click the Configuration tab and click **Networking**.
1. For vSwitch1, click **Properties > Add**
1. Choose **VM Kernel** and click **Next**
   * The settings can be left as they are. You can relabel the network to "VMKernelPublic" for identification purposes.
1. Enter the server Public IP information.
1. For the VM Kernel default gateway, click **Edit**, enter the public gateway IP, and click **OK**. A disconnection is normal and expected.

You can now access the server by using both the Public and Private IP addresses.

Enabling Public IP access to ESXi contains inherent security risks. Make sure that you take the appropriate steps to restrict access to ESXi to only those users who need it.
{: important}
