---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-09"
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Setting up and mounting an iSCSI node in XenServer Shared Storage

Shared Storage is a storage repository (SR) that is any form of storage that can be used by XenServer. There are two categories of SRs: shared and non-shared. A shared SR allows multiple nodes to share a storage location from the same pool. {{site.data.keyword.cloud}} supports two options of SRs, NFS, and LVMoiSCSI (LVM over iSCSI).

You need to install an NFS server and manage it from another customer system or by using am {{site.data.keyword.cloud_notm}} QuantaStor server. Follow these steps to mount the NFS repository in XenCenter:

1. Open up the XenCenter console.
2. Click **New Storage**.
* In the new dialog box, select **Virtual disk storage > NFS**.
* Give an appropriate name of the new SR.
* Enter the server:/path under **Share Name**
* Click **Scan**

This process scans the NFS share for any previous SRs. 

You can use the {{site.data.keyword.cloud_notm}} iSCSI for LVMoiSCSI. The iSCSI can be either from a customer manage storage server of an {{site.data.keyword.cloud_notm}} iSCSI offering. For Performance and Redundant Block Storage, retrieve the IQN from the {{site.data.keyword.slportal}} by going to **Storage > Block Storage > Select Lun Name**. To mount the iSCSI node via XenCenter follow these steps:

1. Open up the XenCenter console.
2. Go to **General > Properties** and set the IQN. 
3. From the top of the console, click **New Storage** 
4. In the new dialog box, go to **Virtual disk storage > Software iSCSI**.
5. Enter an appropriate name for the new SR.
6. Enter the hostname or IP of the iSCSI Target and keep the port at the default 3260.
7. Select **Use CHAP**.
8. Enter the username and password for the LUN.
9. Click **Scan Target Host**.
* Select a **Target IQNs** option.
* Select a **Target  LUNs** option.
10. After the IQN and LUN fields populate, click **Finish**. This scans the target to check for previous SRs. If an SR exists, the installer asks if you want to create a new SR or attach to the previous SR.

If the servers are in a pool together, the iSCSI repository are automatically shared.
