---
copyright:
  years: 2014, 2020
lastupdated: "2020-03-18"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Setting up a hypervisor
{: #setting-up-a-hypervisor}

## Before you begin
* Navigate to your console's device menu. For more information, see [Navigating to devices](/docs/virtualization?topic=virtual-servers-navigating-devices).
* Ensure you have any necessary account permissions and device access. Only the account owner, or a user with the **Manage Users** classic infrastructure permission, can adjust the permissions.

For more information about permissions, see [Classic infrastructure permissions](/docs/account?topic=account-infrapermission#infrapermission) and [Managing device access](/docs/virtual-servers?topic=virtual-servers-managing-device-access).

Use the following steps to set up a hypervisor.

1. Connect to the Private Network through the [secure VPN ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.softlayer.com/vpn-access){: new_window} to access your hypervisor.
**Note:** The {{site.data.keyword.cloud}} hypervisor providers include XenServer, VMware, and Hyper-V. Each provider has unique management consoles that are accessed differently. For more information about accessing and working in a management console, see the following links:
   * [XenServer ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://support.citrix.com/en/products/xenserver){: new_window}
   * [VMware ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.vmware.com/support/vsphere-hypervisor.html){: new_window}
   * [Hyper-V ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://technet.microsoft.com/en-us/windowsserver/dd448604){: new_window}
2. Obtain portable IPs for your virtual machines.
    * VMs require portable IP addresses. Blocks of public and private portable IPs can be ordered through the {{site.data.keyword.slportal}}.
    * For more information about allocating IP addresses, see [Getting started with subnets and IPs](/docs/subnets?topic=subnets-getting-started).

3. Establish routing for VMs on the Private Network. VMs need the following specifications in order to route to other VMs over the Private Network:
    * Portable private IPs
    * Static route that relates to the 10.0.0.0/8 network range

For more information about the VM routing process, see [Setting up a virtual machine network](/docs/virtualization?topic=virtualization-setting-up-a-virtual-machine-network). Machines that are on the same VLAN can communicate after you set up the VM network. [Enable VLAN spanning](/docs/vlans?topic=vlans-vlan-spanning) if machines that are on different VLANs must communicate.

## Access and securely store ISOs
{: #access-and-securely-store-isos}

VMs on the {{site.data.keyword.cloud_notm}} network can run pre-configured or custom ISOs. VMs on the {{site.data.keyword.cloud_notm}} network can access the internal mirrors site, which is available exclusively to {{site.data.keyword.cloud_notm}} users and provides popular configurations of the most commonly used operating systems. If you need a special version or configuration for a specific operating system, see the OS vendor's website.

If you run a custom ISO on your VM, upload your ISO to a secure location so it can be retrieved if a device fails. Many users choose to store custom ISOs on a device's local system by using SSH or RDP. Alternately, space is offered through storage services that have various features.
