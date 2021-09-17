---

copyright:
years: 2014, 2021
lastupdated: "2021-09-17"

subcollection: virtualization

keywords: vsphere, Veeam
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:external: target="_blank" .external}
{:table: .aria-labeledby="caption"}

# Backing up your VMware vSphere environment by using Veeam
{: #backing-up-your-vmware-vsphere-environment-by-using-veeam}

You can back up your VMware vSphere environment by using a hybrid solution that consists of the following services:

* {{site.data.keyword.cloud}} Object Storage Service
* NetApp AltaVault Cloud Storage Gateway
* Veeam Backup and Replication software

Veeam Backup and Replication enables a hybrid solution that includes NetApp AltaVault cloud-integrated storage appliance and {{site.data.keyword.cos_full}}. Software creates, maintains, and restores virtual environments from backups. When used along with a NetApp AltaVault cloud-integrated storage appliance, you create backups that are stored locally (on-premises). The backup is also simultaneously replicated to {{site.data.keyword.cos_full_notm}}. With this hybrid solution, two copies of a backup are made, but only one of them exists locally.

## AltaVault Cloud-Integrated Storage Gateway
{: #altavault-cloud-integrated-storage-gateway}

You can use AltaVault Cloud Storage Gateway to integrate your on-premises environment with the cloud without having to write scripts or applications by using REST APIs for {{site.data.keyword.cos_full_notm}}. You can mount or point to the mount points and begin copying data into the cloud securely.

### Deploying AltaVault on-premises
{: #deploying-altavault-on-premises}

Follow these steps to deploy AltaVault as an on-premises backup solution to {{site.data.keyword.cos_full_notm}}.

You can purchase AltaVault as either a physical or a virtual appliance. The deployment of the trial-version VMware vSphere ESXi-based AltaVault virtual appliance is covered in this procedure.
{: tip}

<a name="prerequisites"></a>
#### Prerequisites

Verify that the following prerequisites are met:

* A copy of AltaVault Virtual Appliance. It is a single file with an OVA file extension. Contact your NetApp representative for the appliance, or download a 90-day trial version from the [NetApp AltaVault website](http://www.netapp.com){: external}.
* An existing on-premises vSphere ESXi 5.5 environment with the minimum CPU, memory, and disk space requirements available for the AltaVault appliance. If you use the trial version, these requirements are four virtual CPUs (vCPUs), 24 GB of memory, and up to 8 TB of disk space.
* Two 10-Gbps network interface controllers (NICs) available within the vSphere environment. One NIC is used for data input and the other is used for data replication to {{site.data.keyword.cos_full_notm}}.
* Two networks that correspond to the two NICs (VLANs) that are defined within the vSphere environment. The replication network cannot be assigned to the same network as the data input network, doing so can create a routing loop.
* A set of {{site.data.keyword.cos_full_notm}} credentials. These credentials include an {{site.data.keyword.cloud_notm}} user name, {{site.data.keyword.cos_full_notm}} user name, and the API key that is associated with the {{site.data.keyword.cloud_notm}} user name.
* Knowledge of VMware Sphere terminology and administering vSphere ESXi environments. This knowledge includes, but is not limited to, use of the vSphere web client, vSphere client, and assignment of hardware resources that include networking and storage.

### Deploying AltaVault OVA
{: #deploying-altavault-ova}

You can deploy the AltaVault OVA to the vSphere environment after all of the prerequisites are met. Instructions for OVA deployment can be found in the [NetApp AltaVault Installation and Service Guide](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: external}.

1. Edit the AltaVault virtual machine (VM) after the deployment of the OVA is complete.
2. Change the allocated memory to match the version of AltaVault that is in the edit window. If you are using the trial version, assign 24 GB of memory and add one disk that is less than or equal to 8 TB. **Note:** This secondary disk storage device is used to store deduplicated backup data.
* Make sure that you assign different networks (VLANs) to the AltaVault appliance after the memory and disk configurations are modified.

The NICs are assigned the following interface functions:

* Primary. Used as the management interface.
* e0a. An interface that is used to replicate data from the AltaVault appliance to the cloud.
* e0b. An interface that is used to export the mount point for the SMB/CIFS or NFS share.
* e0c. An optional interface that is used to export the mount point for the SMB/CIFS or NFS share.

In this example configuration, the AltaVault appliance uses the **e0a** interface as the replicate-to-cloud interface and the **e0b** interface to export a CIFS/SMB mount point. **Note:** A CIFS/SMB share and an NFS share cannot both be used to access the same data. In other words, if data is placed in a CIFS/SMB share, it cannot be accessed via an NFS share and vice versa.

For more information about the deploying the AltaVault appliance and configuration of the VM settings for the appliance, see [NetApp AltaVault Installation and Service Guide](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: external}.

### Initial configuration of the AltaVault appliance <!--initial configuration?-->
{: #initial-configuration-of-the-altavault-appliance-initial-configuration}

You can power on the AltaVault VM after it is configured with the appropriate hardware. **Note:** It takes some time for the AltaVault VM to initially start as the AltaVault appliance is formatting the secondary metadata cache disk.

1. After the appliance completes the start process, log in to the AltaVault console. Use ``*admin`` as the **user name**, and ``password`` as the **Password**. You can change these credentials after the initial configuration completes.
2. After you log in, you are asked whether you want to use the wizard for initial configuration. Enter **y** and press **Enter** to save your changes.

Use the information in Table 1 after the wizard opens.

|Question|Answer|
|---|---|
|Step 1: Admin Password?|Enter a new admin password (it cannot be "password")|
|Step 2: Host name?|Enter the host name that you want to use|
|Step 3: Use DHCP on primary interface?|Enter **n**|
|Step 4: Primary IP address?|Enter the primary network IP address. In the example configuration, the primary IP address is the network that is used for cloud replication and appliance management (192.168.50.15)|
|Step 5: Netmask?|Enter the netmask (255.255.255.0)|
|Step 6: Default gateway?|Enter the default gateway (192.168.50.1)|
|Step 7: Primary DNS server?|Enter the primary Domain Name System (DNS) server in your environment|
|Step 8: Domain name?|Enter the domain name of your environment (testenv.org)|
{: caption="Table 1. AltaVault initial configuration values" caption-side="top"}

### Configuring AltaVault for Object Storage
{: #configuring-altavault-for-object-storage}

Use the following steps to configure the appliance to connect to {{site.data.keyword.cos_full_notm}} service.

1. Open a web browser and enter the IP address of the AltaVault appliance primary interface (see the previous step).
2. Log in to the console with the admin credentials. Upon first login, the Wizard Dashboard is displayed.
3. Select **System Settings** and verify that the information is correct on the next screen and adjust the time zone to reflect the time zone of your environment.
4. Click **Next > Save and Apply > Exit**. You are returned to the Wizard Dashboard.
5. Select **Cloud Settings** and click **Provider**. Choose **IBM Cloud Object Storage**.
6. Select an appropriate **Object Storage Region**. **Note:** Not all regions are displayed (such as Melbourne). However, the host name of the {{site.data.keyword.cos_short}} service is modified by using the **Host name** field. For example, if you want to use Melbourne as the region, you can select **San Jose 1** from the **Region** drop-down menu and modify the **Host name** field to **mel01.objectstorage.softlayer.net**.
7. Enter your {{site.data.keyword.cos_full_notm}} credentials in the **User name** field. The format of the user name must be `object_storage_username:IBM_Cloud_username`. For example: **ABC-DE123456-7:user**. You can find your Object Storage user name under **Storage > Object Storage**.
8. Enter a **Bucket Name** to store the data. The bucket name is the container name where you want to store the data in {{site.data.keyword.cos_full_notm}}.
   * Don't modify the default port unless otherwise directed to by your network administrator. The **Enable Archiving** field defaults to **No** and click **Next**.
9. Enter the **License Request Token**, if necessary, and click **Next**.
10. Enter an **Encryption Key**. You can allow AltaVault to generate a new encryption key or enter an existing key that you want to use to encrypt and decrypt the data. Click **Next**.
11. Verify that all your settings are correct and then click **Finish and Apply**. AltaVault now attempts to contact the {{site.data.keyword.cos_full_notm}} service by using the inputs and settings that are in the Cloud Settings Wizard. If the connection fails, review your settings and make sure that you have the appropriate access to the service.
12. Click **Exit** after a connection is established to return to the Wizard Dashboard and click **Exit Wizard** to return to the AltaVault appliance status page.
13. Verify that the *Storage Optimization Service* is running and that the status is ready. **Note:** It might take few minutes for the status to change to *Ready*.

The AltaVault appliance is configured to communicate with the {{site.data.keyword.cos_full_notm}} service.

### Configuring the CIFS/SMB mount point in AltaVault
{: #configuring-the-cifs-smb-mount-point-in-altavault}

The **e0b** interface needs to be configured to create a CIFS/SMB mount point. Use the following steps to configure **e0b**.

1. Go to **Settings > Data Interfaces** and expand the **e0b** interface. Select **Enable Data Interface**, and enter the **IP address, Subnet Mask,** and **Gateway** that you use to mount the CIFS/SMB share.
2. Leave the default **MTU** value of **1500 bytes**.<br/><br/>Although the default maximum transmission unit (MTU) is set to 1,500, you can change it to 9,000 if you use jumbo frames. **Note:** Your ESXi host and physical infrastructure is required to support jumbo frames. By default, {{site.data.keyword.cloud_notm}} already supports an MTU size of 9,000 bytes with no configuration changes needed.
3. Click **Apply**. The mount point is ready for configuration.
4. Select **Storage > CIFS > Add CIFS Share** and enter a unique name.
5. Click the **Pin Share** drop-down menu and select **Yes**. **Note:** Veeam Backup and Replication backups can fail to an unpinned share.
6. Enter a unique path for the share in the **Path** field. It is preferable to use the share name as the path, that is, if the share name is `cifs_share0, enter /cifs_share0` as the path.
7. Clear **Allow Everyone Access** if security is not an issue. It is preferable to whitelist the clients that use the CIFS/SMB share. Otherwise, leave **Allow Everyone Access** selected if security is an issue and click **Add Share**.
8. Click **Add CIFS User** to create accounts for authorized users and complete the **User name** and **Password** fields.
9. Expand the new CIFS share and click **Add a user or group** to add the authorized user accounts.
10. Go to **Global CIFS Settings** and click the **Listening Interface** drop-down menu and select **e0b** and click **Apply**.

The AltaVault appliance is configured to allow communications between itself, {{site.data.keyword.cos_full_notm}}, and the computer that is running Veeam Backup and Replication. It is recommended that you export the configuration of the AltaVault appliance to expedite future deployments, if necessary.

To export your AltaVault appliance configuration, follow these steps.

1. Click **Settings > Setup Wizard** to access the Wizard Dashboard in the web management console of the on-premises AltaVault appliance.
2. Click **Export Configuration** and click **Export Configuration**.
3. Save the configuration file (a tarball/.tar) in a safe location.

## Veeam Backup and Replication
{: #veeam-backup-replication-backup}

Veeam Backup and Replication software provides complete backup, replication, and recovery capabilities for VMs and their data. The backup can fully integrate with an AltaVault Cloud Gateway Appliance.

### Provisioning Veeam on a new server
{: #provisioning-veeam-on-a-new-server}

You can order Veeam when you provision a new virtual server instance or bare metal server. Use the following information when provisioning.
   * Veeam is only available with monthly billing
   * Veeam is only available with a Windows Operating System

To add Veeam:
   * In the System Addons section, under OS-Specific Addons, click the Veeam tab and select one of the Veeam options.
   * In the System Addons section, under CDP Addon, select any additional Veeam options to add. <br><br>**Note**: If you select *Veeam Backup and Replication 9.5 Update 3*, you are required to select at least one option in the CDP Addon list.

### Ordering Veeam on an existing server
{: #ordering-veeam-on-an-existing-server}

You can add Veeam to an existing server with the [OS reload](/docs/bare-metal?topic=bare-metal-reloading-the-os) procedure. The server must be running a Windows OS and it must be set up for monthly billing.

To add Veeam:
1. In the System Addons section, under OS-Specific Addons, click the Veeam tab and select one of the Veeam options.
2. In the System Addons section, under CDP Addon, select any additional Veeam options to add. <br><br>**Note**: If you select *Veeam Backup and Replication 9.5 Update 3*, you are required to select at least one option in the CDP Addon list.

### Deploying Veeam Backup and Replication
{: #deploying-veeam-backup-replication}

A trial version of Veeam Backup and Replication Version 8 is used in the example.

#### Prerequisites
{: #prerequisites-deploying-veeam-backup-replication}

Before you proceed with deployment, verify that the following prerequisites are satisfied:

* Have an existing AltaVault appliance that is configured for use with {{site.data.keyword.cos_full_notm}} and Veeam Backup and Replication.
* Obtain a copy of Veeam Backup and Replication for VMware environments, which is a single executable file. Contact your Veeam representative for a copy or download a [30-day trial version](http://www.veeam.com/vm-backup-recovery-replication-software.html){: external}.
* Obtain a license file to use with Veeam Backup and Replication. In most cases, this file is emailed to the email address that is used to download Veeam Backup and Replication. If you did not receive this file, contact your Veeam representative.<br/><br/>The license file is used to activate all of the Veeam Backup and Replication functions. If this file is not supplied during program installation, all features and functions revert to the 30-day trial version.
* Have an existing backup server, which can either be onsite or off-site, with the specifications that are found in Table 2. The installed operating system must be a 64-bit version.

||Minimum|Recommended|
|---|---|---|
|**OS**|A supported Windows operating system|
|**# of cores or vCPUs**|2|4|
|**Memory**|4 GB-base RAM plus 500 MB for each concurrent backup job.|16 GB-base RAM plus 4 GB for each concurrent backup job.|
|**Disk space**|2 GB for product installation; 10 GB per 100 VMs for guest file system catalog data (persistant data).|2 GB for product installation; 10 GB for 100 VMs for guest file system catalog (persistant data).|
|**Network**|1-Gbps LAN for onsite backup and replication; 1-Mbps WAN for off-site backup and replication.|1-Gbps LAN for onsite backup and replication; 1-Mbps WAN for off-site backups and replication.|
{: caption="Table 2. System requirements for Veeam Backup and Replication backup server" caption-side="top"}

#### Installing Veeam Backup and Replication
{: #installing-veeam-backup-replication}

Use the following steps to install Veeam Backup and Replication to the backup server after all of the prerequisites are met.

1. To start the setup wizard, double-click the Veeam executable file and click **Veeam Backup and Replication – Install**.
2. Click **Next** and accept the terms in the license agreement.
3. Click **Next** and click **Install** for **Veeam Backup and Replication**.
4. Enter the location of the license file that was previously obtained and click **Next**.
5. Select the Veeam Backup and Replication components that you want to install and provide the installation location on the **Veeam Backup and Replication Setup** screen. **Veeam Backup and Replication and Veeam Backup catalog** are required components. Click **Next**. The setup wizard runs a series of checks to make sure that all required program frameworks and supporting components are installed. If any are missing, the setup wizard offers to install them automatically. If you need to install missing frameworks or components, click **Install**.
6. Verify that all components **Passed** the systems check and click **Next**.
7. Select the **Service (user) Account** where the Veeam Backup Service runs. **Note:** The default service account is the *LOCAL SYSTEM account*. Click **Next**.
8. Select the **SQL Server Instance** that is used to create and store Veeam Backup and Replication databases.<!--Contact your database administrator for more information, if necessary. --> Click **Next**.
9. Enter the **Catalog service port** and **Veeam Backup service port** (the default ports are **9393** and **9392**).<!--Contact your network administrator for more information, if necessary.--> Click **Next**.
10. Select the directories where the guest file system catalog (persistent data) and vPower NFS write cache (non-persistent data) are stored. Click **Next**.
11. Verify that all settings and values are correct and click **Install** to start the installation. After installation is complete, click **Finish**.

### Configuring Veeam Backup and Replication for Backups
{: #configuring-veeam-backup-replication-for-backups}

After you install Veeam Backup and Replication, you are ready to connect it to the vSphere ESXi host that contains the AltaVault virtual appliance.

1. Start Veeam Backup and Replication.
2. On the lower-left side of the screen, click **Backup Infrastructure > Managed Servers**.
3. Click **Add Server** and double-click **VMware vSphere**.
4. Enter the **DNS name or IP address** and **Description** of the vCenter server and click **Next**.
5. Enter the **Credentials** of a local account that has administrator privileges on the vSphere server. **Note:** The account user name must be in **DOMAIN\USER** format for domain accounts, or **HOST\USER** format for local accounts. To add an account, click **Add** and enter the account user name and password.<br/>Do not change the **Default VMware web services port** during Veeam Backup and Replication installation unless your network administrator tells you otherwise.
6. Click **Next**. Veeam Backup and Replication connects to the VMware vSphere server. If the connection attempt fails, check that the account exists and has administrator privileges on the VMware vSphere server before you try to connect again.
7. Click **Finish** on the **Summary** window and verify that the vSphere server was added successfully by clicking **Managed Servers > VMware vSphere**.

### Adding a Backup Repository to Veeam Backup and Replication
{: #adding-a-backup-repository-to-veeam-backup-replication}

By default, Veeam Backup and Replication creates a local backup repository on the `C:\` drive of the Veeam Backup and Replication backup server during program installation.

Use the following steps to create a backup repository to store all backups on the AltaVault appliance. If you want to use the default backup repository, skip the following steps.

1. In the lower left of the **Backup Infrastructure** screen, click **Backup Infrastructure > Backup Repositories > Add Repository**.
2. Enter a unique repository name in the **Name** field. Optionally, you can provide a **Description**. Click **Next**.
3. Select **Shared folder**. The CIFS/SMB share that you created is the share that this backup repository uses. Click **Next**.
4. Specify the location of the CIFS/SMB share on the AltaVault appliance. To determine the location, open a web browser and enter the IP address of the AltaVault appliance. Go to **Storage > CIFS** and note the **Share Path** of the share. **Note:** This is NOT the same as the local path of the share.<br/><br/>The share path format is `\\<AltaVault appliance hostname>\<share name>`. Replace the AltaVault appliance host name in the Share Path with the IP address of the **e0b** network interface (the mount point of the share) of the AltaVault appliance.<br/><br/>To find the IP address of the **e0b** interface, click **Settings > Data Interfaces** in the AltaVault appliance management window. The share path that is specified in Veeam Backup and Replication is `\\192.168.50.16\cifs_test2`.
5. Return to Veeam Backup and Replication, enter the share path of the mount point in the **Shared folder** field and click **Next**. Veeam Backup and Replication attempts to establish a connection with the mount point. If the connection attempt fails, go back and verify that the network settings for the AltaVault appliance are correct before you try again.
6. Enter a value to limit maximum concurrent tasks to the number of resources that are available. This value is the maximum number of tasks a backup proxy can send to the selected share. The default number of concurrent tasks is 4. AltaVault suggests that you start with five concurrent tasks and increasing or decreasing this value as resources allow. This value can be adjusted after the backup repository is created.
7. Click **Next**.
8. If needed, specify the **vPower NFS settings**. If the **Enable vPower NFS server** selection is left clear, then Veeam Backup and Replication uses vPower for recovery and recovery verification. Click **Next**.
9. On the **Review** screen, confirm that all your settings are correct and click **Next**.
10. Click **Finish** to exit the wizard. You can now begin backing up your data.

### Backing up the Environment
{: #backing-up-the-environment}

Follow these steps to create a backup of a complete virtual environment.

1. From the **Backup Infrastructure** screen, click **Backup and Replication**.
2. In the **Backup and Replication** window, click **Jobs > Backup Job**.
3. Enter a unique name in the **Name** field. Optionally, you can enter a **Description**. Click **Next**.
4. Select which VMs that you want to back up by clicking **Add Objects** and click through the tree structure to select the VMs. Click **Add** after you select the appropriate VMs.<br/>If only specific parts of the VMs are to be backed up (the start disk), click **Exclusions** and specify the parts. Otherwise, click **Next**.
5. Select the backup repository that you created by using the **Backup repository** drop-down menu.

**Note:**For optimal performance, make sure that you change the data deduplication and compression settings. Use the following steps to optimize performance.

1. Click **Advanced**, select the **Storage** tab, and clear **Enable inline data duplication**. Performance is improved because the AltaVault appliance performs block-level deduplication of the Veeam Backup and Replication backups that pass through it.
2. Select **None** under the **Compression level** drop-down menu and select **LAN target** under the **Storage optimization** drop-down menu.<br/>**Note:** If the network location of the CIFS/SMB share is congested, leaving **Enable inline data deduplication** checked might alleviate network performance issues, but at the cost of smaller data deduplication ratios experienced on the AltaVault appliance.
3. Click **Next**.
4. If you want application-aware processing and or guest file system indexing, select the appropriate check box. Set the **Guest OS credentials** of the guest OS of the VMs that is being backed up, if necessary. Click **Next**.
5. Select the **Run the job automatically** check box if backups run regularly and set the intervals that you want. Otherwise, click **Create** and **Finish**.

#### Starting a manual backup
{: starting-a-manual-backup}
To manually start a backup, right-click on the backup job and select **Start**. Alternatively, select **Active Full** if you want a new backup.

**Note:** Veeam Backup and Replication can restore virtual environments from a backup. For more information about restoring virtual environments, see the [Veeam Backup and Replication](https://www.veeam.com/vm-backup-recovery-replication-software.html){: external} website.
