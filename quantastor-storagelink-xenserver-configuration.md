---
copyright:
  years: 2014, 2018
lastupdated: "2018-05-18"
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Installing QuantaStor StorageLink Xenserver 

## Installing the QuantaStor Adapter for Citrix StorageLink

1. Make sure that your installation of XenServer is properly licensed for StorageLink.

Only customers who have subscribed to the "Enterprise" or "Platinum" licenses through Citrix can use the StorageLink feature. Customers who have the "Advanced" Citrix XenServer License cannot access the StorageLink feature.
{:tip}

2. Stop the StorageLink service by entering the following command at an administrator command prompt or by stopping the StorageLink service from the Services Panel in XenServer.

        c:\> net stop storagelink
        
3. Download and run the StorageLink adapter available from [OSNexus ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://www.osnexus.com/trynow/){: new_window}.
4. After the service has been stopped you can install the QuantaStor StorageLink adapter by running the following command:

        osn_quantastor_csladapter.exe
        
5. Add this block of XML to the end of the StorageLink configuration at:
  * `C:\Program Files\Citrix\StorageLink\Server\scsi_device_id_config.xml`
  * `OSNEXUS`
  * `QUANTASTOR1`
  * At the begining of this file you see a version number that looks like this: 2.0.0.4. Increase the version number, for example: 2.0.0.5. This tells StorageLink that it needs to upgrade your XenServer systems with these new device identification rules.
6. Restart the Citrix StorageLink service:

        c:\> net start storagelink

## Verifying  that StorageLink has been properly loaded
1. Log in to Citrix StorageLink Manager, select the "Administration" tree on the left, and look for OS NEXUS QuantaStor in the [QuantaStor ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://svn.osnexus.com/mediawiki/images/thumb/c/c8/Storagelink_admin.png/640px-Storagelink_admin.png){: new_window} list.
2. Add your storage system credentials so StorageLink can dynamically configure and allocate storage volumes for you QuantaStor storage system.

## Installing the XenServer multipath configuration settings

XenServer uses the Linux device-mapper multipath driver (dmmp) to enable support for hardware multi-pathing. The dmmp driver has a configuration file called multipath.conf that contains the multipath mode and failover rules for each vendor.

There are some special rules that need to be added to the multipath.conf file for QuantaStor. Edit the /etc/multipath-enabled.conf file located on each of XenServer dom0 nodes.  In that file, add the following stanza after the last device { } section:

    device {
          vendor                  "OSNEXUS"
          product                 "QUANTASTOR"
          getuid_callout          "/sbin/scsi_id -g -u -s /block/%n"
          path_selector           "round-robin 0"
          path_grouping_policy    multibus
          failback                immediate
          rr_weight               uniform
          rr_min_io               100
          path_checker            tur
    }
