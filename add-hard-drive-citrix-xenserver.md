---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-11"
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Adding a hard drive to Citrix XenServer

Adding new hard drive in XenServer is different from the traditional Linux process. For XenServer, you need to create a container that is called a "storage repository" to define a particular storage target (such as a hard disk), in which Virtual Disk Images (VDIs) of VMs are stored. A VDI is an abstracted storage space that acts as the hard disk for VMs.

Xen storage repository supports IDE, SATA, SCSI and SAS drives  when locally connected, apart from iSCSI, NFS, SAS and fiber channel in case of a remote storage.

## Creating an SR in a XenServer

1. SSH to the XenServer as root.

2. Find the disk ID of the new device using the following commands:

       # cat /proc/partitions

  You will see a list of all the HDDs and partitions. Find which device is your new local disk. This is probably “sdx” (most probably sdb) or “/cciss/c0d1p0”. The following command lists the disk IDs for all the partitions/HDDs present in the server.

       # ll /dev/disk/by-id

  Find the disk ID of the “sdx” or “cciss/c0d1”disk. The “scsi-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx” or “cciss-xxxxxxxxxxxxxxxxxxxxxxxxxx” format is what you need.

3. Find out the 'host-uuid' in the XenServer using the following command.

        #xe host-list

  The uuid (RO) is the 'host-uuid' you need.

4. Create a Storage Repository (SR):

  > **Note:** The [sr-create ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://support.citrix.com/article/CTX121313){: new_window} command is for adding a **new** hard drive. This is a destructive process that will partition and format the drive, and any data on the drive will be lost. If you wish to re-introduce a drive that contains existing data, you should use [sr-introduce ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://support.citrix.com/article/CTX121896){: new_window} instead.

  `# xe sr-create content-type=user device-config:device=/dev/disk/by-id/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`<br/>
  \- Or -<br/>
  `# xe sr-create content-type=user device-config:device=/dev/disk/by-id/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`<br/>
  \- Or -<br/>
  `# xe sr-create content-type=user device-config:device=/dev/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`

## Verifying the Storage Repository in XenCenter

You can verify the Storage Repository from XenCenter by using the following steps:

1. Connect to XenCenter.

2. Go to the **Storage** tab. You will find the details of all storage repositories here. You can see the storage 'Local Storage2' in the list. This signifies that the disk has been added successfully. Now you can start creating VDIs on it.
