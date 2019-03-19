---
copyright:
  years: 2014, 2019
lastupdated: "2019-01-15"

subcollection: virtualization

keywords: vmware, ESXi
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:table: .aria-labeledby="caption"}

# Advanced single-site VMware reference architecture
{: #advanced-single-site-vmware-reference-architecture}

Use the following steps to create an advanced single-site vSphere environment. The following steps define a {{site.data.keyword.cloud}} reference architecture deployment by using VMware suggested best practices.

The reference architecture is intended for provisioning an environment that uses shared storage, VMware High Availability (HA) and vSphere Distributed Resource Scheduler (DRS), and an {{site.data.keyword.cloud_notm}} gateway or firewall. Advanced single-site VMware reference architecture is suitable for most production deployments that can be scaled as workload dictates and can replace an on-premises implementation or extend into a hybrid IT scenario.

## Environment overview
{: #environment-overview}

The advanced representative VMware environment that is outlined, consists of one data center that manages two separate clusters: management and capacity. The configuration can be set up by using a single four node cluster that depending on requirements. The management cluster contains the following virtual machines (VMs) that are used for managing the infrastructure:

* VMware vCenter Server 5.5 or 6.0 Appliance
* Microsoft Windows 2012 R2 Standard with Active Directory and Domain Name System (DNS) roles

The capacity cluster contains the resources and infrastructure that are needed to create and run VMs. For networking, the environment consists of three private, internal VLANs, and a single, public VLAN that is used for external communication. Table 1 specifies the VLAN types and VLAN names are used throughout the environment.

|VLAN Type|VLAN Name|Description|                                                                             |
|---|---|---|
|Primary Private| Management| Assigned to manage and access the physical ESXi hosts and virtual servers.|
|Primary Private| Storage| Assigned to manage and access the shared storage that is attached to each ESXi host.|
|Primary Private| VM Access | Assigned to virtual machines that run on each ESXi host.|
|Primary Public | Public | Assigned to virtual machines or other devices that require access from the public network.|
<caption>Table 1. Primary VLANs</caption>

For shared storage, you can use OS Nexus QuantaStor, a single-tenant shared storage server, or {{site.data.keyword.cloud_notm}} Endurance or Performance storage services. In either case, the shared storage device is used to store the VMs on both the management and capacity clusters. For more information about storage options, see [{{site.data.keyword.cloud_notm}} Storage solutions ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/cloud/storage){: new_window}.

The storage environment is configured to support NFS volumes.

## Step 1 Ordering primary public and private VLANs
{: #step-1-ordering-primary-public-and-private-vlans}

**Note:** VLAN orders are subject to review and approval. No specific conditions or requirements are in place that flags a VLAN order to be auto-approved. VLAN orders can be denied for various reasons.

Four VLANs are created in this step: one for management, one for storage, one for VMs, and one for public access. It is recommended that you create these four VLANs before you order your servers. Ordering the VLANs first makes sure that the servers are placed on the correct VLANs and in the correct data center.

The environment consists of five ESXi hosts (two for the management cluster and three for the capacity cluster) and one virtual server. The private management VLAN consists of a subnet with 16 IP addresses to support it. The primary private VLAN for storage and VM traffic consists of eight addresses since the environment contains a single storage server and VMs use a portable subnet. If a larger subnet range is needed for the management network, adjust the range by calculating the number of ESXi hosts and multiplying by 2. Also, make sure that you specify the data center for which these VLANs are created.

After you log in to {{site.data.keyword.cloud_notm}}, open a support ticket for the management and virtual machine VLANs by selecting **Support > Add Ticket**. Complete the fields with the information that is in **Table 2:**.

|Field|Value|
|---|---|
|Subject|Private Network Question|
|Title|Order VLANs|
|Details|Provision three primary private VLANSs and one primary public VLAN. Associate the following addressing scheme for each VLAN: <br/><ul><li>Associate 1x18 (16 addresses) for primary private VLAN</li><li>Associate 2x29 (eight addresses) for primary private VLAN</li><li>Associate 1x29 (eight addresses) primary public VLAN</li></ul>|
|Why do I need these additional VLANs?|To place hosts, storage, and VMs on a different network for a VMware environment.
|Do I need public and or private VLANs?|Private and Public|
|How many VLANs do I need?|Private = 3, Public = 1|
|How many IP addresses do I need?|8 - 16 for each VLAN|
|What router do I the VLANs need to be behind?|It doesn't matter that the VLANs are all in the same pod.|
|Which pod do the VLANs need to be in?|They all go in the same pod in <DATA CENTER NAME>.|
<caption>Table 2. Support ticket information</caption>

After the VLANs are provisioned, make note of the VLAN numbers, subnet range, and gateway, and assign them to logical vSphere networks. You can use the worksheet in Appendix A: VLAN worksheet to record the VLAN and the associated information. For example:

|VLAN Type|VLAN Number|IP Range|Gateway|Purpose|
|---|---|---|---|---|
|Primary Private|1101|10.X.Y.Z/28|10.X.Y.1|Management|
|Primary Private|1102|10.A.B.C/29|10.A.B.1|Storage|
|Primary Private|1103|10.Q.R.S/29|10.Q.R.1|Virtual machines|
|Primary Public|2101|75.S.T.U/29|75.S.T.1|Public access|
<caption>Table 3. Primary VLAN sample</caption>

**Note:** Do not continue to the next step until the VLANs are provisioned.

## Step 2 Ordering portable private IP addresses
{: #step-2-ordering-portable-private-ip-addresses}

In Step 2, a request is made to create portable private subnets for management VMs, VM kernel storage access, and VMs in the capacity cluster. You need to determine how many addresses you need for each VLAN subnet. In the representative environment, order the following addresses:

* Management VLAN - 1x8 addresses /29 – One address for vCenter Appliance; one address for DNS and Active Directory.
* Storage VLAN - 1x16 addresses /28 – Create two subnets on the same VLAN for storage and two VM kernel ports on each ESXi hosts by using different subnets to access the shared storage devices.
* VM VLAN - 1x32 addresses /27 – These addresses are used to assign IP addresses to VMs in the capacity cluster.

When you order an amount, make sure that you consider how many IP addresses are needed within the next 30 days and 6 months. It is also important to note that {{site.data.keyword.cloud_notm}} reserves three to four IP addresses per portable subnet block. As a result, ordering four IP address nets one IP address, or zero if the pod supports Hot Standby Router Protocol.

Use the following steps to order a block of portable IP addresses for each VLAN for each subnet that you want to create:

1. Open a browser window and log in to {{site.data.keyword.cloud_notm}}.
2. Select **Account > Place an Order**.
3. In the pop-up window, go to **Network > Subnets / IPs > Order**.
4. From drop-down menu, select **Portable Private**.
5. Select **XX Portable Private IP address** and click **Continue**. **Note:** _XX_ specifies the number of IP addresses.
6. Select the VLAN to associate with IP address block and click **Continue**.
7. Complete filling out the information on the screen and click **Continue**.

Creating IP addresses is fairly quick and is displayed by selecting **Subnets** from **Network > IP Management**. You can record these IP addresses in the worksheet that is found in Appendix A: VLAN worksheet.

## Step 3 Ordering a virtual server
{: #step-3-ordering-a-virtual-server}

A Windows 2012 R2 Standard virtual server is provisioned to use as a utility server for ISOs and provide access to the environment in this step.

1. Open a browser window and log in to {{site.data.keyword.cloud_notm}}.
2. Select **Account > Place an Order**.
3. Go to **Virtual Server > Hourly or Monthly**.
4. Select the appropriate data center (where the VLANs were created) to provision the virtual server and specify the following specifications for each option:
  * Computing instance - 1x2.0 GHz Cores
  * RAM: 4 GB
  * Operating System: Windows Server 2012 R2 Standard Edition (64-bit)
  * First Disk: 100 GB (SAN)
  * Uplink Port Speeds - 1 Gbps Public and Private Network Uplinks
5. Click **Continue Your Order** and select the backend and fronted VLANs on the **Order Summary and Billing** screen. **Note:** The selection of VLANs is important so the utility can be placed in the correct pod within the data center. For the example environment, the backend VLAN is the management VLAN (1101) and the front-end VLAN is the public VLAN (2101)
6. Enter a host name and domain name for the server and click **Place Order**.

## Step 4 Ordering ESXi hosts and gateway / firewall
{: #step-4-ordering-esxi-hosts-and-gateway-firewall}

You need to order the ESXi hosts and Brocade vRouter (Vyatta) gateway and firewall appliance while the virtual server is provisioned. Order all of these servers at the same time so they are placed in the same pod at the same time. Ordering hardware at separate times can cause hosts and firewalls to be in separate pods within an {{site.data.keyword.cloud_notm}} data center.

### ESXi Hosts
{: #esxi-hosts}

For each ESXi host that is ordered for the environment, VMware ESXi 5.5 is the operating system. If you want to use {{site.data.keyword.cloud_notm}} vSphere licenses, monthly charges are incurred that are based on usage.

Another option is to install ESXi by using your own ISO. Instructions for this process can be found in [Installing VMware vSphere ESXi via Remote Console and Virtual Media](/docs/infrastructure/vmware?topic=VMware-installing-vmware-vsphere-esxi-via-remote-console-and-virtual-media). If you want to install ESXi by using your own ISO, make sure to select **No Operating System** as the operating system for the management and capacity hosts during the ordering process.

**Note:** This implementation requires Enterprise Plus licensing to use vSphere Distributed Virtual Switches. If your license is not valid for Enterprise Plus, you need to use the {{site.data.keyword.cloud_notm}} provided VMware Service Provider Program (VSPP) license.

### Ordering management hosts
{: #ordering-management-hosts}

Use the following steps to order the management host servers.

1. Open a browser window and log in to {{site.data.keyword.cloud_notm}}.
2. Select **Account > Place an Order**.
3. Select **Bare Metal Servers > Monthly**.
4. Choose an appropriate server that meets the requirements for the management cluster on the server list screen. **Note:** At a minimum, ESXi 5.5 requires a single dual-core processor, 4 GB of RAM, and 1 Gb Ethernet controller.
5. Select the appropriate data center (where the VLANs were created) to provision the ESXi servers and specify the following specifications for each option:
  * Quantity: 2
  * RAM: 32 GB
  * Operating System: VMware ESXi 5.5 ('No Operating System' if you are using [Installing VMware vSphere ESXi via Remote Console and Virtual Media](/docs/infrastructure/vmware?topic=VMware-installing-vmware-vsphere-esxi-via-remote-console-and-virtual-media))
  * Hard disks: Disks 1 & 2: 500 GB SATA in RAID 1
  * Public Bandwidth: Private Network Only-> 0 GB bandwidth
  * Uplink Port Speeds: 10 Gbps Redundant Private Network Uplinks
6. Click **Continue Your Order**.
7. Click **Add Server** to begin adding ESXi hosts for the capacity cluster to the order.

### Ordering capacity hosts
{: #ordering-capacity-hosts}

1. Choose an appropriate server that meets the requirements for the capacity cluster hosts on the server list screen. **Note:** At a minimum, ESXi 5.5 requires a single dual-core processor, 4 GB of RAM, and one 1 GB Ethernet controller.
2. Select the appropriate data center (where the VLANs were created) to provision the ESXi servers and specify the following specifications for each option:
  * Quantity: 3
  * RAM: 128 GB
  * Operating System: VMware ESXi 5.5 ('No Operating System' if you are using [Installing VMware vSphere ESXi via Remote Console and Virtual Media](/docs/infrastructure/vmware?topic=VMware-installing-vmware-vsphere-esxi-via-remote-console-and-virtual-media))
  * Hard disk: Disks 1 & 2: 500 GB SATA in RAID 1
  * Public Bandwidth: Private Network Only > 0 GB bandwidth
  * Uplink Port Speeds: 10 Gbps Redundant Private Network Uplinks
3. Click **Continue Your Order**.

### Completing configuration
{: #completing-configuration}

Now, you have ESXi hosts in your shopping cart. For the devices to provision correctly, you need to assign the public (if applicable) VLAN, private VLAN, host name, and domain to the devices.

1. Assign the following VLANs and create host names for the devices:

* ESXi hosts – Backend VLAN: (1101)
* Backend VLAN: (1101)
* Frontend VLAN: (2101)

2. Select your payment method, agree to the terms and conditions, and click **Finalize Your Order**. **Important:** Do not proceed to Step 5 until the servers are provisioned and are accessible via VPN or virtual server that was ordered in the previous step.

## Step 5 Trunking VLANs on BCS Switches
{: #step-5-trunking-vlans-on-bcs-switches}

By default, ports are placed on the backend customer switches (that is, private network switch in a pod, such as a Backend Customer Switch (BCS)) in access mode. As a result, you need to trunk the ports that are attached to the ESXi hosts so the hosts can access storage and so the VMs can communicate on the private network.

Before you open the ticket to trunk the VLANs, you need to determine which network interfaces are on the private network. To determine the interface, go to the **Device Details** for each ESXi server and go to **Network > Private**. For this environment, `eth0` and `eth2` are the private network adapters.

In addition to trunking of the VLANs for the ESXi hosts, you must also unbond the NICs for the management and capacity hosts. You unbond the NICs because the Link Aggregation Control Protocol (LACP) is not compatible with software iSCSI multipathing.

To trunk the VLANs and unbond the NICs, you need to open a ticket by following these steps:

1. Open a browser window and log in to {{site.data.keyword.cloud_notm}}.
2. Select **Support, Add Ticket**.
3. Enter the following information:
  * Subject: Private network question
  * Title: Trunk VLANs and unbond NICs
  * Details: Trunk VLANs `<Management VLAN>`, `<Storage VLAN>`, and `<VM VLAN>` on eth0 and eth2 NIC pair for the following hosts [list each ESXi host]. Also, unbond (remove LACP) the private NICs (eth0 and eth2) on the following servers: [list each ESXi host]
4. Click **Add Ticket**.

Make sure to change the VLANs that are designated in the <> with your actual VLANs

## Step 6 Configuring management host networking
{: #step-6-configuring-management-host-networking}

After the servers are provisioned and the VLANs are trunked, you need to set up networking on the hosts in the management cluster. For this configuration, you use vSphere standard switches for the management cluster. Except for the vMotion and fault tolerance port groups, you use portable IP addresses to configure the VM kernel port groups. **Note:** You use your own IP scheme for vMotion and fault tolerance because the traffic does not need to be routed. However, all hosts need to be on the same subnet as other hosts in the cluster to use vMotion and fault tolerance capabilities. If the subnet needs to be routed, it is recommended that you use {{site.data.keyword.cloud_notm}} Portable IP addresses.

To configure the port groups, the vSphere client must be installed on the utility virtual server or the workstation that accesses the hosts via {{site.data.keyword.cloud_notm}} management VPN.

1. Open a browser window and log in to {{site.data.keyword.cloud_notm}}.
2. After you connect to the utility server or {{site.data.keyword.cloud_notm}} VPN, start the vSphere client.
3. Enter the IP address, user name, and password of one of the management ESXi hosts. (You can find the root password for the ESXi host by selecting **Device Details > Passwords**.
4. Go to **Configurations > Networking** and create or modify the following port groups on the vSphere standards switch (most likely vSwitch0).

Complete these steps for each host in the management cluster.

### vSwitch0 properties
{: #vswitch0-properties}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Table 4. vSwitch0 Properties</caption>
<tbody>
<tr><th>Network Adapter</th><th>vmnicX and vmnicY</th></tr>
<tr><td>Load Balancing</td><td>Router based on the originating virtual port ID</td></tr>
<tr><td>Active Adapters</td><td>vmnicX and vmnicY</td></tr>
</table>

### Edit existing virtual machine port group
{: #edit-existing-virtual-machine-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Table 5. vmPG-Management Port Group</caption>
<tbody>
<tr><th>Network Label</th><th>*vmPG-Management*</th></tr>
</table>

### Edit existing VM kernel port group
{: #edit-existing-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Table 6. vmkPG-Management Port Group</caption>
<tbody>
<tr><th>Network Label</th><th>*vmkPG-Management*</th></tr>
</table>

### Add vMotion VM kernel port group
{: #add-vmotion-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Table 7. vMotion Port Group</caption>
<tbody>
<tr><th>Connection Type</th><th>VMKernel</th></tr>
<tr><td>Network Label</td><td>*vmkPG-vMotion*</td></tr>
<tr><td>VLAN ID</td><td>*&lt;Storage VLAN&gt; (1102)*</td></tr>
<tr><td>vMotion Traffic</td><td>Enabled</td></tr>
<tr><td>IP Address</td><td>*172.16.10.X/24*<br/><br/>*User-defined address that can be a different subnet. Make sure the other vMotion addresses on each host is on the same subnet.*</td></tr>
<tr><td>Subnet Mask</td><td>255.255.255.0</td></tr>
</table>

### Add fault tolerance VM kernel port group
{: #add-fault-tolerance-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Table 8. FT Port Group</caption>
<tbody>
<tr><th>Connection Type</th><th>VM Kernel</th></tr>
<tr><td>Network Label</td><td>*vmkPG-FT*</td></tr>
<tr><td>VLAN ID</td><td>*&lt;Storage VLAN&gt; (1102)*</td></tr>
<tr><td>Fault Tolerance Logging</td><td>Enabled</td></tr>
<tr><td>IP Address</td><td>*172.16.20.X/24*<br/><br/>*User-defined address that can be a different subnet if needed. Make sure that the other FT addresses on each host are on the same subnet.*</td></tr>
<tr><td>Netmask</td><td>255.255.255.0</td></tr>
</table>

### Add storage VM kernel port group
{: #add-storage-vm-kernel-port-group}

Update the **Notes** section of each Portable IP address with the name of the host and VM kernel port assigned. The **Notes** section can be located by going to the {{site.data.keyword.slportal_full}} and selecting **Network > IP Management > Subnets > [Subnet]**.

<table border="1" cellpadding="0" cellspacing="0">
<caption>Table 9. Storage Port Group</caption>
<tbody>
<tr><th>Connection Type</th><th>VM Kernel</th></tr>
<tr><td>Network Label</td><td>*vmkPG-Storage*</td></tr>
<tr><td>VLAN ID</td><td>*&lt;Storage VLAN&gt; (1102)*</td></tr>
<tr><td>IP Address</td><td>*Portable Private Address*<br/><br/>*An IP address that is selected from the portable private addresses that are bound to the storage VLAN.*</td></tr>
<tr><td>Netmask</td><td>*Subnet Mask Associated with the IP Range*</td></tr>
</table>

### Add public address port group
{: #add-public-address-port-group}

Update the **Notes** section of each Portable IP address with the name of the host and VM kernel port assigned. The **Notes** section is in the {{site.data.keyword.slportal}} and by selecting **Network > IP Management > Subnets > [Subnet]**.

<table border="1" cellpadding="0" cellspacing="0">
<caption>Table 10. Public Port Group</caption>
<tbody>
<tr><th>Connection Type</th><th>Virtual Machine</th></tr>
<tr><td>Network Label</td><td>vmPG-Public</td></tr>
<tr><td>VLAN ID</td><td>&lt;Primary Public VLAN&gt; (for example 2101)</td></tr>
</table>

## Step 7 Download or upload ISO images and vCenter Virtual Appliance
{: #step-7-download-or-upload-iso-images-and-vcenter-virtual-appliance}

Now, the environment is ready to deploy the VMware vCenter Virtual Appliance and install a virtual machine for DNS, Active Directory, or BIND. However, before you deploy, you need to download the images. To do download the images, remote desktop to the virtual server previously provisioned and download the appropriate images on the virtual server for your environment:

* Active Directory / Windows DNS: Windows Server 2008R2/Windows Server 2012 ISO Image (Your licensed Media)
* Linux BIND: [CentOS Install Image ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://isoredirect.centos.org/centos/6/isos/x86_64/){: new_window}
* [vCenter Virtual Appliance ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://my.vmware.com/web/vmware/info/slug/datacenter_cloud_infrastructure/vmware_vsphere/5_5){: new_window} (requires a valid VMware subscription)

After you download the ISO, you need to upload the image to a management host data store so it can be attached to a virtual machine. Use the vSphere client to connect to a management host and create an ISO directory on the local data store.

## Step 8 Deploying DNS
{: #step-8-deploying-dns}

You need to deploy a VM (on a management host) and install DNS services. Use the traditional vSphere client to create a VM on the Management ESXi host where the Windows or Linux ISO is located. Connect the appropriate ISO (Windows or CentOS) to deploy a DNS server on the VM. Make sure to associate the VM to the VM port group (vmpg)-management port group that was created in a previous step.

After the VM is installed, assign an IP address and default gateway from the portable private subnet group. If you are using the VLAN worksheet in Appendix A, the Management VM subnet. Update the **Notes** section of each Portable IP address with the name of the VM assigned. The **Notes** section can be located by going to the {{site.data.keyword.slportal}} and selecting **Network > IP Management > Subnets > [Subnet]**.

Although it is beyond the scope of this document to detail the steps that are needed to enable DNS, the following guidance is provided:

1. Set **DNS Forwarding** to the service.softlayer.com local DNS hosts:
  * `rs1.service.softlayer.com 10.0.80.11`
  * `rs2.service.softlayer.com 10.0.80.12`
2. After you set up DNS, create a local DNS zone (dal06.mycompany.local) and a reverse lookup zone for all portable and primary subnets that are provisioned.
3. Add an A HOST records for each host’s Management IP address (vmk0 on vmkPG-management).
4. Add an A HOST record from the portable private subnet that is bound to the management VLAN for your vCenter Virtual Appliance.
5. Update the **Notes** section of the Portable IP Subnet that you assigned to vCenter.

For more information, see the following links:
* [Windows DNS and Active Directory ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://social.technet.microsoft.com/wiki/contents/articles/12370.step-by-step-guide-for-setting-up-a-windows-server-2012-domain-controller.aspx){: new_window}.
<!--* [CentOS BIND ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://www.centos.org/docs/2/rhl-rg-en-7.2/s1-bind-configuration.html){: new_window}.-->

## Step 9 Deploying and configuring vCenter Virtual Appliance
{: #step-9-deploying-and-configuring-vcenter-virtual-appliance}

Now that DNS is configured, you can deploy and configure the vCenter Server Appliance. To deploy the appliance, follow these steps:

1. Remote desktop to the virtual server and open the vSphere Client.
2. Connect to a management host and select **File, Deploy OVF Template**.
3. Follow the wizard to complete the deployment.

<!--For more information about OVF Template deployment, see [VMware documentation ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://pubs.vmware.com/vsphere-55/index.jsp?topic=%2Fcom.vmware.vsphere.hostclient.doc%2FGUID-6C847F77-8CB2-4187-BD7F-E7D3D5BD897B.html&resultof=%22thick%22%20%22thin%22%20){: new_window}.-->

Because no Dynamic Host Configuration Protocol (DHCP) server is available to assign the vCenter virtual appliance an IP address upon power-on, you must use the root console to configure the appliance. **Note:** A `NO NETWORKING DETECTED` message is displayed on the vCenter Virtual Appliance Console.

Follow these steps to configure the appliance:

1. Log in to the console with a **User name** of 'root' and a **Password** of 'vmware'.
2. Run `/opt/vmware/share/vami/vami_config_net` and follow the text wizard to configure the IP, subnet, and DNS properties. Remember to use the IP address of the DNS or BIND server that was created in **Step 8: Deploy DNS**.
3. Save the network settings, exit the console, and open a browser on the virtual server.
4. Go to the IP address that you gave to the vCenter virtual appliance (VCVA) appended with port 5480<!-- (https://:5480)-->.
5. Accept the EULA in the wizard, answer the **Customer Improvement Experience Program** question, and select **Configure Options, Set custom configuration**.
6. Click **Next** and enter the following values:
<table border="1" cellpadding="0" cellspacing="0"><caption>Table 11. VCVA Setup Wizard</caption><tbody><tr><th>Wizard Menu</th><th>Option</th><th>Value</th></tr><tr><td>Database Settings</td><td>Database Type</td><td>embedded</td></tr><tr><td>SSO Settings</td><td>SSO Deployment Type</td><td>embedded</td></tr><tr><td>SSO Settings</td><td>New administrator password *(for administrator@vsphere.local)*</td><td>&lt;Enter a password&gt;</td></tr><tr><td>SSO Settings</td><td>Retype the new password</td><td>&lt;Enter the same password that you use previously&gt;</td></tr><tr><td>Time synchronization</td><td>NTP synchronization</td><td>servertime.service.softlayer.com</td></tr></table>
7. Click **Start**. The VCVA is configured.
8. Change the root password by using the options under **Admin**.
9. Log out of the VCVA configuration web page.
10. Go to the vSphere Web Client by entering the IP address of the VCVA appended with `9443/vsphere-client`<!-- (https://:9443/vsphere-client)-->.
11. Log in to the root and select **Administration, Licenses**.
12. Enter your VMware vCenter license.

## Step 10 Create vCenter clusters and distributed virtual switch
{ #step-10-create-vcenter-clusters-and-distributed-virtual-switch}

Now that VCVA is configured and licensed, you can create the data center and cluster constructs and distributed virtual switches for the capacity cluster.

### Creating data center and clusters
{: #creating-data-center-and-clusters}

1. On the vSphere client, go to the **Home** screen.
2. Select **Getting Started** and click **click here**.
3. Click **Create Datacenter**.
4. Enter a data center name (The example environment is the Toronto 01 data center. Use `Toronto 01` as the data center name).
5. After the data center is created, click **Create a cluster** on the **Getting Started** page.
6. Name the first cluster `Management`. Leave all other options cleared and click **OK**.
7. Create another cluster when you are done by using the same process as the management cluster.

You can now add the management and capacity hosts to the Management and Capacity clusters, by using **Add a host**. Be sure to use the fully qualified domain name (FQDN) of the server rather than the IP address so that DNS is used when each host is added.

After you add the ESXi hosts to vCenter, you might notice a couple of warnings messages about the enablement of the shell and SSH on each ESXi host. To suppress these warnings, click **Suppress Warning > Yes** on the pop-up window. If the Suppress Warnings link is not present, follow these steps:

1. Go to the ESXi host **Manage** tab.
2. Select the **Settings** and click **Advanced System Settings**.
3. Find the **UserVars.SupressShellWarning** key and change the value to **1**.
4. Click **OK**.

After the management and capacity hosts are added to their respective clusters, go to each host and setup DNS and NTP. To set up DNS, follow these steps:

1. Click a host and select **Manage > Networking**.
2. Select the default system stack (**TCP/IP configuration**) and click the pencil icon.
3. Enter the IP address of the DNS server that you previously created and the host and domain name.

For NTP settings:

1. Go to **Manage, Settings, Time Configuration**.
* Enter `servertime.service.softlayer.com` as the NTP server.
* Set the **NTP Service Startup Policy** to `Start and stop with host`.

***Creating a distributed virtual switch for the capacity hosts***

1. Use the vSphere Web Client to go to **Networking** and right-click on the data center name.
2. Select **New Distributed Switch**.
3. Name the distributed switch and click **Next**.
4. Select the appropriate distributed switch version and click **Next**.
5. On the **Edit Settings** screen, enter `2` as the number of uplinks and clear the **Create a default port group** selection.
6. Click **Next > Finish** and create the distributed virtual switch.

***Create port groups for distributed virtual switch***

Now that the distributed virtual switch is present, you must create port groups for vMotion, fault tolerance, VMs, and storage.

***Creating dvpg-Private-VM Management Port Group***

1. Go to the Networking section by using the vSphere web client.
2. Right-click on the distributed virtual switch (DVS).
3. Click **New Distributed Port Group…** and enter the information in Table 14 for the first port group.
4. Leave the default values for any options that are not specified in the table.

| New Distributed Port Group Menu | Field | Value |
| --- | --- | --- |
| Name and Location | Name | dvpg-Private-VM Management |
| Configure Settings | Advanced | Check Customize default policies configuration |
| Configure Policies (Teaming and Failover)| Load Balancing| Route that is based on physical NIC load |
| Configure Policies (Teaming and Failover)| Failover Order| Active Uplinks: Uplink 1 & Uplink 2 |
<caption>Table 12. DVS VM Management Port Group</caption>

After you create the first port group, create the remaining port groups with the following configurations options (Table 15 to Table 19).

***Create dvpg-Private-vMotion Port Group***

|New Distributed Port Group Menu|Field|Value|
|---|---|---|
|Name and Location|Name|dvpg-Private-vMotion|
|Configure Settings|VLAN Type|VLAN|
|Configure Settings|VLAN ID|&lt;Storage VLAN&gt;|
|Configure Settings|Advanced|Check Customize default policies configuration|
|Configure Policies (Teaming and Failover)|Load Balancing|Route that is based on physical NIC load|
|Configure Policies (Teaming and Failover)|Failover Order|Active Uplinks: Uplink 1 & Uplink 2|
<caption>Table 13. DVS vMotion Port Group</caption>

***Create dvpg-Private-Fault Tolerance Port Group***

|New Distributed Port Group Menu|Field|Value|
|---|---|---|
|Name and Location|Name|dvpg-Private-Fault Tolerance|
|Configure Settings|VLAN Type|VLAN|
|Configure Settings|VLAN ID|&lt;Storage VLAN&gt;|
|Configure Settings|Advanced|Check Customize default policies configuration|
|Configure Policies (Teaming and Failover)|Load Balancing|Route that is based on physical NIC load|
|Configure Policies (Teaming and Failover)|Failover Order|Active Uplinks: Uplink 1 & Uplink 2|
<caption>Table 14. DVS FT Port Group</caption>

***Create dvpg-Private-VM Access Port Group***

|New Distributed Port Group Menu|Field|Value|
|---|---|---|
|Name and Location|Name|dvpg-Private-VM Access|
|Configure Settings|VLAN Type|VLAN|
|Configure Settings|VLAN ID|&lt;Virtual Machine VLAN&gt;|
|Configure Settings|Advanced|Check Customize default policies configuration|
|Configure Policies (Teaming and Failover)|Load Balancing|Route that is based on physical NIC load|
|Configure Policies (Teaming and Failover)|Failover Order|Active Uplinks: Uplink 1 & Uplink 2|
<caption>Table 15. DVS VM Access Port Group</caption>

***Create dvpg-Private-Storage***

|New Distributed Port Group Menu|Field|Value|
|---|---|---|
|Name and Location|Name|dvpg-Private-Storage Path A|
|Configure Settings|VLAN Type|VLAN|
|Configure Settings|VLAN ID|&lt;Storage VLAN&gt;|
|Configure Settings|Advanced|Check Customize default policies configuration|
|Configure Policies (Teaming and Failover)|Load Balancing|Route that is based on physical NIC load|
|Configure Policies (Teaming and Failover)|Failover Order|Active Uplinks: Uplink 1 & Uplink 2|
<caption>Table 16. DVS Storage Port Group</caption>

***Create dvpg-Primary-Public***

|New Distributed Port Group Menu|Field|Value|
|Name and Location|Name|dvpg-Private-Storage|
|Configure Settings|VLAN Type|VLAN
|Configure Settings|VLAN ID|&lt;Primary Public VLAN&gt;|
|Configure Settings|Advanced|Check Customize default policies configuration|
|Configure Policies (Teaming and Failover)|Load Balancing|Route that is based on physical NIC load|
|Configure Policies (Teaming and Failover)|Failover Order|Active Uplinks: Uplink 1 & Uplink 2|
<caption>Table 17. DVS Storage Path B Port Group</caption>

## Step 11 Migrating ESXi hosts in capacity cluster to distributed virtual switch
{: #step-11-migrating-esxi-hosts-in-capacity-cluster-to-distributed-virtual-switch}

Now that the capacity hosts are added to the capacity cluster, you can migrate the virtual standard switch configuration to the distributed virtual switch that you created in *Step 10: Create vCenter Clusters & Distributed Virtual Switch*. Migrating is done for one host and you apply a host profile later to configure the rest of the cluster.

Before you begin adding VMkernel adapters, assign the vmnics to the uplinks on the distributed virtual switch.

1. Click **vCenter Inventory Lists, Distributed Switches**.
2. Select the appropriate distributed switch for the capacity hosts.
3. Click **Add and manage hosts** on the **Getting Started** page.
4. Use the following settings to add uplinks and migrate the existing VMkernel that are associated with management of the host.
  - <table border="1" cellpadding="0" cellspacing="0"><caption>Table 18. DVS Add Hosts</caption><tbody><tr><th>Menu</th><th>Field</th><th>Value</th></tr><tr><td>Select Task</td><td>Select Tasks</td><td>Add Hosts</td></tr><tr><td>Select Hosts</td><td>Click **New Hosts**</td><td>Click the Capacity Host</td></tr><tr><td>Select network adapter tasks</td><td>Select network adapter tasks</td><td>Select Manage physical adapters and Manage VMkernel adapters</td></tr></table>
5. Select one of the private vmnics and click **Manage physical network adapters > Assign uplink**.
6. Select `uplink1` on the pop-up screen and click **OK**.
7. Repeat these steps for the other private vmnic and assign it to `uplink2`.
8. Click **Next**, highlight the vmk0 VMkernel adapter, and click **Assign port group**.
9. Select **dvpg-Private-VM Management** on the pop-up window and click **OK**.
10. Click **Next** twice and then click **Finish** to complete the migration to the distributed virtual switch. **Note:** You might briefly lose network connectivity to the host. The connection reestablishes quickly.

After you migrate the vmk0 adapter to the distributed virtual switch, you can add VM kernels to each port group in the DVS.

11. Click **Manage > Networking** on the host within vCenter.
12. Go to **VM Kernel adapters > Add host networking** and add the VM kernel adapters that are in Tables 19 and 21. To add these adapters, you migrate to the “VM Kernel adapters” menu that is in the **Manage > Networking** tab on the host within vCenter. Then, click the “Add host networking” icon and add the following VM Kernel adapters.

***Add vmk1 for vMotion***

|Menu|Field|Value|
|---|---|---|
|Select connection type|Select connection type|VMKernel Network Adapter|
|Select target device|Select an existing distributed port group|dvpg-Private-vMotion|
|Select network adapter tasks|Select network adapter tasks|Select **Manage physical adapters** and **Manage VM kernel adapters**|
|Port Properties|Enable Services|Check vMotion traffic|
|IPv4 Settings|IPv4 Address|*172.16.10.X/24*<br/><br/>*This is a user-defined address and can be a different subnet if needed. Make sure the other vMotion addresses that are on each host are on the same subnet.*|
|IPv4 Settings|Subnet Mask|255.255.255.0|
<caption>Table 19. Host Networking vMotion</caption>

***Add vmk2 for fault tolerance***

|Menu|Field|Value|
|---|---|---|
|Select connection type|Select connection type|VMKernel Network Adapter|
|Select target device|Select an existing distributed port group|dvpg-Private-Fault Tolerance|
|Select network adapter tasks|Select network adapter tasks|Select **Manage physical adapters** and **Manage VMKernel adapters**|
|Port Properties|Enable Services|Check Fault Tolerance Logging|
|IPv4 Settings|IPv4 Address|*172.16.20.X/24*<br/><br/>*This is a user-defined address and can be a different subnet if needed. Make sure that the other FT addresses that are on each host are on the same subnet.*|
|IPv4 Settings|Subnet Mask|255.255.255.0|
<caption>Table 20. Host Networking Fault Tolerance</caption>

***Add vmk3 for storage***

|Menu|Field|Value|
|---|---|---|
|Select connection type|Select connection type|VMkernel Network Adapter|
|Select target device|Select an existing distributed port group|dvpg-Private-Storage|
|Select network adapter tasks|Select network adapter tasks|Select Manage physical adapters and Manage VMkernel adapters|
|IPv4 Settings|IPv4 Address|*Portable Private Address*<br/><br/>*This IP address is selected from the portable private addresses that are bound to the storage VLAN. This address needs to be on a different subnet than Storage Path B.*|
|IPv4 Settings|Subnet Mask|Subnet Mask Associated with the IP Range|
<caption>Table 21. Host Networking Storage</caption>

***Creating a host profile***

To create a host profile, you must capture it from the single capacity host that you previously configured.

1. Go to the vSphere Web Client home screen click the **Host Profiles** icon.
2. Click the green plus (+) sign, **Extract profile from a host**, and select the previously configured capacity host on the pop-up window.
3. Click **Next**.
4. Give the host profile (Capacity01 Host Profile) an appropriate name, enter a description, and click **Next**.
5. Review the settings and click **Finish**.

After you create the host profile, you need to modify it so that it does not prompt for MAC addresses when the profile is applied to the rest of the hosts in the capacity cluster.

1. Right-click the host profile that you created and select **Edit Settings > Edit Host Profile, Host virtual NIC**.
2. In the right pane, change **Determine how MAC address for vmknic is decided** to **User must explicitly choose the policy option**.
3. Click **Next** and then click **Finish**.

***Attaching host profile to capacity cluster***

Now that you created a host profile, you need to attach the host profile to the cluster. Clusters are attached so that they can be applied to the capacity hosts.

1. Go to the **Host and Clusters** view in the vSphere Web Client.
2. Enter maintenance mode for each host in the cluster. **Note:** Profiles can be applied only to hosts that are in maintenance mode.
3. Right-click the capacity cluster and select **Attach Host Profile** from the pop-up menu.
4. Select the host profile that you created and click **Next**.
5. Enter the appropriate information on the **Customize host** screen and click **Finish**.
6. Remove the hosts from maintenance mode (non-maintenance mode).

## Step 12 Order, configure, and attach shared storage
{: #step-12-order-configure-and-attach-shared-storage}

Before you continue, it is imperative that you order, configure, and attach shared storage for use with the management and capacity clusters and hosts within the environment. If you want to use the {{site.data.keyword.cloud_notm}} multi-tenant shared storage solution (File Storage), see [Architecture Guide for IBM File Storage for {{site.data.keyword.cloud_notm}} with VMware](/docs/infrastructure/FileStorage?topic=FileStorage-architectureguide).

## Step 13 Enabling HA/DRS and svMotion vCVA
{: #step-13-enabling-ha-drs-and-svmotion-vcva}

***Enabling HA/DRS on management and capacity clusters***

Now that shared storage is set up, you need to enable HA and DRS to provide extra protection and load-balancing capabilities to the VMs on the management cluster.

1. Go to the vSphere web client.
* Select Manage, Settings for the management cluster.
* Select vSphere DRS and click Edit. Ensure that the following settings are selected: **Turn on vSphere DRS**, Automation Level - **Fully Automated**, Migration threshold slider setting - midway, virtual machine automation - **Enable individual virtual machine automation levels.**, Power Management - **Off**.  
* On the vSphere HA Settings screen, ensure that the following settings are selected: **Turn on vSphere HA**, **Host Monitoring**, VM restart priority - **Medium**, Host isolation response - **Leave powered on**.  
***Storage vMotion the vCenter Virtual Appliance***

Now that storage is set up on the management cluster and HA and DRS are enabled, you need to set the vCenter Virtual Appliance to shared storage.

1. Right-click the appropriate appliance and select **Migrate** from the pop-up menu.
2. Select **Change data store** and click **Next**.
3. Select the iSCSI volume that you previously mounted to the management cluster and click **Next**.
4. Review the selections and click **Next**.

The advanced single-site VMware environment is complete.

## Summary
{: #summary}

You now have a VMware environment that is running in an IBM Cloud data center. Your VMware environment can run production workloads and supplementing an on-premises IBM Cloud deployment. The environment enacts VMware best practices and enables features such as VMware DRS, HA, Storage DRS, and networking redundancy. You can extend this reference architecture implementation with greater capacity or management hosts and more storage.

For more information about VMware, see [Deploy VMware](/docs/infrastructure/vmware?topic=VMware-using-cookbooks-for-vmware-deployments) and [VMware FAQ](/docs/infrastructure/vmware?topic=VMware-faqs-vmware)

## Appendix A: VLAN Worksheet
{: #appendix-a-vlan-worksheet}

|VLAN Type|VLAN Number|IP Range|Gateway|Purpose|
|---|---|---|---|---|
|Primary Private||||Management|
|Primary Private||||Storage|
|Primary Private||||Virtual Machines|
|Primary Public||||Public Access|
|Portable Private||||Management VMs|
|Portable Private||||Storage|
|Portable Private||||Virtual Machines|
|vMotion|||N/A||
|Fault Tolerance|||N/A|||
<caption>VLAN Worksheet</caption>
