---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: QuantaStor
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Installing QuantaStor StorageLink Xenserver
{: #installing-quantastor-storagelink-xenserver}

## Installing the QuantaStor Adapter for Citrix StorageLink
{: #installing-the-quantastor-adapter-for-citrix-storagelink}

1. Make sure that your installation of XenServer is properly licensed for StorageLink.

Only customers who subscribe to the "Enterprise" or "Platinum" licenses through Citrix can use the StorageLink feature. Customers who have the "Advanced" Citrix XenServer license can't access the StorageLink feature.
{:tip}

2. Stop the StorageLink service by entering the following command at an administrator prompt. Or, you can stop the StorageLink service from the Services Panel in XenServer.

        C:\> net stop storagelink

3. Download and run the StorageLink adapter available from OSNexus.

4. After the service stops, you can install the QuantaStor StorageLink adapter by running the following command:

        osn_quantastor_csladapter.exe

5. Add this block of XML to the end of the StorageLink configuration at:
  * `C:\Program Files\Citrix\StorageLink\Server\scsi_device_id_config.xml`
  * `OSNEXUS`
  * `QUANTASTOR1`
  * At the beginning of this file you see a version number that that is similar to _2.0.0.4._ Increase the version number, for example: 2.0.0.5. The version number tells StorageLink that it needs to upgrade your XenServer systems with these new device identification rules.
6. Restart the Citrix StorageLink service:

        C:\> net start storagelink

## Verifying that StorageLink loaded properly
{: #verifying-that-storagelink-loaded-properly}

1. Log in to Citrix StorageLink Manager, select the "Administration" tree on the left, and look for OS NEXUS QuantaStor in the QuantaStor list.
2. Add your storage system credentials so StorageLink can dynamically configure and allocate storage volumes for your QuantaStor storage system.

## Installing the XenServer multipath configuration settings
{: #installing-the-xenserver-multipath-configuration-settings}

XenServer uses the Linux device-mapper multipath driver (`dmmp`) to enable support for hardware multi-pathing. The `dmmp` driver uses a configuration file that is called `multipath.conf` that contains the multipath mode and failover rules for each vendor.

QuantaStor has some special rules that need to be added to the `multipath.conf` file. Edit the `/etc/multipath-enabled.conf` file that is on each of XenServer `dom0` nodes. In that file, add the following stanza after the last device { } section:

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
