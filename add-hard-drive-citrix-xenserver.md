---
copyright:
  years: 2014, 2018
lastupdated: "2018-08-14"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Adding a hard disk drive to Citrix XenServer
{: #adding-a-hard-disk-drive-to-citrix-xenserver}

Adding new hard disk drive in XenServer is different from the traditional Linux process. For XenServer, you need to create a container that is called a "storage repository" to define a particular storage target (such as a hard disk). This container is where virtual disk images (VDIs) of VMs are stored. A VDI is an abstracted storage space that acts as the hard disk for VMs.

Xen storage repository supports IDE, SATA, SCSI, and SAS drives when locally connected, apart from iSCSI, NFS, SAS, and Fibre Channel for remote storage.

## Creating a storage repository in a XenServer
{: #creating-a-storage-repository-in-a-xenserver}

1. SSH to the XenServer as root.

2. Find the disk ID of the new device by using the following command:

       # cat /proc/partitions

  The command lists of all the HDDs and partitions. Find your new local disk, which is probably “sdx” (most probably sdb) or '/cciss/c0d1p0'. Use the following command to list the disk IDs for all the partitions/HDDs present in the server.

       # ll /dev/disk/by-id

  Find the disk ID of the “sdx” or 'cciss/c0d1'disk. The “scsi-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx” or “cciss-xxxxxxxxxxxxxxxxxxxxxxxxxx” format is what you need.

3. Find the 'host-uuid' in a XenServer by using the following command:

        #xe host-list

  The uuid (RO) is the 'host-uuid' that you need.

4. Create a Storage Repository (SR):

  > **Note:** The [sr-create ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://support.citrix.com/article/CTX121313){: new_window} command is for adding a new hard disk drive. Creating a new hard disk drive is a destructive process that partitions and formats the drive, and any data on the drive is lost. If you want to reintroduce a drive that has existing data, use [sr-introduce ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://support.citrix.com/article/CTX121896){: new_window}.

  `# xe sr-create content-type=user device-config:device=/dev/disk/by-id/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`<br/>
  \- Or -<br/>
  `# xe sr-create content-type=user device-config:device=/dev/disk/by-id/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`<br/>
  \- Or -<br/>
  `# xe sr-create content-type=user device-config:device=/dev/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`

## Verifying the Storage Repository in XenCenter
{: #verifying-the-storage-repository-in-xencenter}

You can verify the Storage Repository from XenCenter by following these steps:

1. Connect to XenCenter.

2. Go to **Storage** to find the details of all storage repositories. You can see the storage 'Local Storage2' in the list that shows that the disk is added. Now you can start creating VDIs on it.
