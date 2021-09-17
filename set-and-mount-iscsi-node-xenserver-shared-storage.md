---
copyright:
  years: 2014, 2021
lastupdated: "2021-09-17"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:external: target="_blank" .external}
{:pre: .pre}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:shortdesc: .shortdesc}
{:term: .term}

# Setting up and mounting Shared Storage
{: #xenserver-shared-storage}

Shared Storage is a storage repository (SR) that is any form of storage that can be used by XenServer. You can choose from two categories of SRs: shared and non-shared. A shared SR allows multiple nodes to share a storage location from the same pool. {{site.data.keyword.cloud}} supports two options of SRs, NFS, and LVMoiSCSI (LVM over iSCSI).
{: shortdesc}

## Setting up and mounting an NFS volume
{: #setting-up-and-mounting-an-NFS-volume-xenserver}

You need to install an NFS server and manage it from another customer system or by using an {{site.data.keyword.cloud_notm}} QuantaStor server. Use the following steps to mount the NFS repository in XenCenter:

1. Open the XenCenter console.
2. Click **New Storage**.
* In the new dialog box, select **Virtual disk storage** > **NFS**.
* Give an appropriate name of the new SR.
* Enter the server:/path under **Share Name**
* Click **Scan**

This process scans the NFS share for any previous SRs.

## Setting up and mounting iSCSI storage
{: #setting-up-and-mounting-an-iscsi-volume-xenserver}

The iSCSI LUN can be from a customer-managed storage server or an {{site.data.keyword.blockstoragefull}} volume. To mount the iSCSI node through XenCenter, complete the following steps.

The IQN and target IP addresses of a {{site.data.keyword.blockstorageshort}} volume can be obtained from the [{{site.data.keyword.cloud_notm}} console](https://{DomainName}/){: external}. 
{: tip}

1. Open the XenCenter console.
2. Go to **General > Properties** and set the IQN.
3. Click **New Storage**.
4. In the new dialog box, go to **Virtual disk storage > Software iSCSI**.
5. Enter an appropriate name for the new SR.
6. Enter the hostname or IP of the iSCSI Target and keep the port at the default 3260.
7. Select **Use CHAP**.
8. Enter the username and password for the Lun.
9. Click **Scan Target Host**.
   * Select a **Target IQNs** option.
   * Select a **Target LUNs** option.
10. After the IQN and Lun fields populate, click **Finish**. The target is scanned to check for previous SRs. If an SR exists, the installer asks if you want to create a new SR or attach to the previous SR.

If the servers are in a pool together, the iSCSI repository is automatically shared. 

It's imperative that your servers are properly configured for continuous service through all storage and networking events, planned or unplanned. Make sure that Multipath is set up correctly. For more information about configuring multipath, see
[Multipathing Overview for XenServer 6.2, 6.5, and 7.0](https://support.citrix.com/article/CTX118791){: external} and [Storage Multipathing for XenServer 7.2](https://docs.citrix.com/en-us/xencenter/7-1/storage-pools-multipathing.html){: external}.
{: important}
