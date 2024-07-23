---

copyright:
  years: 2014, 2024
lastupdated: "2024-07-23"

subcollection: virtualization

---

{{site.data.keyword.attribute-definition-list}}

# Setting up a hypervisor
{: #setting-up-a-hypervisor}

Use the following information to set up a hypervisor.
{: shortdesc}

## Before you begin
{: #before-you-begin-hyperv}

* Go to your console's device menu. For more information, see [Navigating to devices](/docs/virtual-servers?topic=virtual-servers-navigating-devices).
* Make sure that you have any necessary account permissions and device access. Only the account owner, or a user with the **Manage Users** classic infrastructure permission, can adjust the permissions.

For more information about permissions, see [Managing classic infrastructure access](/docs/account?topic=account-mngclassicinfra) and [Managing device access](/docs/virtual-servers?topic=virtual-servers-managing-device-access).

## Setting up a hypervisor
{: #setting-up-hypervisor}

Use the following steps to set up a hypervisor.

1. Connect to the private network through the [secure VPN](https://www.ibm.com/products/vpn-access){: external} to access your hypervisor. The {{site.data.keyword.cloud}} hypervisor providers include XenServer, VMware, and Hyper-V. Each provider has management consoles that are accessed differently. For more information about accessing and working in a management console, see the following links:

   * [XenServer](https://support.citrix.com/s/){: external}
   * [VMware](https://www.broadcom.com/support/vmware-services){: external}
   * [Hyper-V](https://learn.microsoft.com/en-us/windows/deployment/){: external}

2. Obtain portable IPs for your virtual machines.

   * VMs require portable IP addresses. Blocks of public and private portable IPs can be ordered through the {{site.data.keyword.slportal}}. For more information about allocating IP addresses, see [Getting started with subnets and IPs](/docs/subnets?topic=subnets-getting-started).
   {: note}

3. Establish routing for VMs on the Private Network. VMs need the following specifications to route to other VMs over the private network:

   * Portable private IPs
   * Static route that relates to the `10.0.0.0/8` network range

For more information about the VM routing process, see [Setting up a virtual machine network](/docs/virtualization?topic=virtualization-setting-up-a-virtual-machine-network).

Machines that are on the same VLAN can communicate after you set up the VM network. [Enable VLAN spanning](/docs/vlans?topic=vlans-vlan-spanning) if machines that are on different VLANs must communicate.

## Access and securely store ISOs
{: #access-and-securely-store-isos}

VMs that are on the {{site.data.keyword.cloud_notm}} network can run preconfigured or custom ISOs. They can also access the internal mirrors site, which is available exclusively to {{site.data.keyword.cloud_notm}} users and provides popular configurations of the most commonly used operating systems. If you need a special version or configuration for a specific operating system, see the OS vendor's website.

If you run a custom ISO on your VM, upload your ISO to a secure location so it can be retrieved if a device fails. Many users choose to store custom ISOs on a device's local system by using SSH or RDP. Alternately, space is offered through storage services that have various features.
