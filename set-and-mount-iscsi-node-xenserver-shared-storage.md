---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-16"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Setting up and mounting an iSCSI node in XenServer Shared Storage
{: #setting-up-and-mounting-an-iscsi-node-in-xenserver-shared-storage}

Shared Storage is a storage repository (SR) that is any form of storage that can be used by XenServer. You can chose from two categories of SRs: shared and non-shared. A shared SR allows multiple nodes to share a storage location from the same pool. {{site.data.keyword.cloud}} supports two options of SRs, NFS, and LVMoiSCSI (LVM over iSCSI).

You need to install an NFS server and manage it from another customer system or by using am {{site.data.keyword.cloud_notm}} QuantaStor server. Follow these steps to mount the NFS repository in XenCenter:

1. Open the XenCenter console.
2. Click **New Storage**.
* In the new dialog box, select **Virtual disk storage > NFS**.
* Give an appropriate name of the new SR.
* Enter the server:/path under **Share Name**
* Click **Scan**

This process scans the NFS share for any previous SRs.

You can use the {{site.data.keyword.blockstoragefull}} for LVMoiSCSI. The iSCSI can be from a customer manage storage server or an {{site.data.keyword.blockstoragefull}} offering. For Performance and Redundant Block Storage, retrieve the IQN from the {{site.data.keyword.slportal}} by going to **Storage > Block Storage > Select Lun Name**. To mount the iSCSI node via XenCenter, follow these steps:

1. Open the XenCenter console.
2. Go to **General > Properties** and set the IQN.
3. Click **New Storage**
4. In the new dialog box, go to **Virtual disk storage > Software iSCSI**.
5. Enter an appropriate name for the new SR.
6. Enter the hostname or IP of the iSCSI Target and keep the port at the default 3260.
7. Select **Use CHAP**.
8. Enter the username and password for the Lun.
9. Click **Scan Target Host**.
* Select a **Target IQNs** option.
* Select a **Target  Luns** option.
10. After the IQN and Lun fields populate, click **Finish**. The target is scanned to check for previous SRs. If an SR exists, the installer asks if you want to create a new SR or attach to the previous SR.

If the servers are in a pool together, the iSCSI repository is automatically shared.

For information about configuring iSCSI for multipath, see [Configuring iSCSI Multipath ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus1000/sw/4_0_4_s_v_1_3/system_management/configuration/guide/n1000v_system/n1000v_system_13iscsi.pdf){: new_window}
