---
copyright:
  years: 2014, 2024
lastupdated: "2024-04-19"

subcollection: virtualization

---

{{site.data.keyword.attribute-definition-list}}

# What is Citrix XenServer?
{: #what-is-citrix-xenserver}

XenServer is a server virtualization platform that offers virtualization performance for virtualized server and client operating systems that nearly matches the performance of bare metal servers.
{: shortdesc}

XenServer uses the Xen hypervisor to virtualize each server, on which it is installed, enabling each to host multiple virtual machines simultaneously with great performance. You can also use XenServer to combine multiple Xen-enabled servers into a powerful resource pool, by using industry-standard shared storage architectures and by using resource clustering. In doing so, XenServer enables seamless virtualization of multiple servers as a resource pool. These resources are dynamically controlled to deliver optimal performance, increased resiliency and availability, and maximum use of data center resources.

IT managers can use XenServer to create multiple clusters of resource pools and manage the clusters and their resources from a single point of control. With XenServer, a rack of servers can become a highly available compute cluster that protects key application workloads by using industry standard storage architectures. XenServer also offers no-downtime maintenance by moving virtual machines while they are running between servers in the cluster. XenServer extends the most powerful abstraction: virtualization across servers, storage, and networking to enable users to realize the full potential of a dynamic, responsive, efficient data center environment for Windows and Linux workloads.

## Differences between versions of XenServer
{: #differences-between-versions-of-xenserver}

Licensing is the only difference between versions of XenServer that are installed on the system. If you want to upgrade your license to a higher class license after installation, you experience no downtime to reinstall your server. Contact {{site.data.keyword.cloud}} sales for pricing information.

Not all available features are supported.
{: note}

The following sections lists features that are included for each of the different licenses that are offered (as of XenServer 6.0):

### XenServer free, advanced, enterprise license features
{: #xenserver-free-advanced-ent-features}

The following features are included with the free, advanced, and enterprise XenServer licenses.

- XenServer Hypervisor
- Conversion Tools
- Management integration with Microsoft System Center VMM
- Resilient distributed management architecture
- VM disk snapshot and revert
- XenCenter Management Console
- XenMotion Live Migration

### XenServer advanced and enterprise license features
{: #xenserver-advanced-ent-features}

The following features are included with the advanced and enterprise XenServer licenses.

- Automated VM protection and recovery (Automated VM protection and recovery is only available for the Advanced and Enterprise editions in the 6.0 release and later.)
- Distributed virtual switching
- Heterogeneous Pools
- High Availability
- Memory Optimization
- Performance alerting and reporting

### XenServer Enterprise license features
{: #xenserver-ent-features}

The following features are included with enterprise XenServer licenses.

- Dynamic workload balancing
- GPU pass-thru
- Host power management
- IntelliCache
- Live memory snapshot and revert
- Provisioning Services (virtual)
- Role-based administration
- StorageLink
- Web management console with delegated admin

## Operating systems are supported by {{site.data.keyword.cloud}} with XenServer
{: #operating-systems-supported-by-ibm-cloud-under-xenserver}

The following operating systems can integrate with {{site.data.keyword.slapi_full}} while XenServer runs:

- CentOS 6.x, 7.x
- CloudLinux 6.x
- CoreOS Stable
- Red Hat Enterprise Linux 6.x, 7.x
- Debian 7.x through 9.x
- Ubuntu 14.04 LTS through 16.04 LTS
- Microsoft Windows Server 2012, 2012 R2, 2016, 2019

XenServer supports various software beyond the preceding list that might not tie into the {{site.data.keyword.slapi_short}}. Any guest operating system that is installed by *“Other installation media”* options in XenCenter, is not supported by {{site.data.keyword.BluSoftlayer_notm}} and might not work in the XenServer environment.

## Changes with Citrix XenServer 6.2 and greater
{: #changes-with-citrix-xenserver-6-2-and-greater}

The release of Citrix XenServer 6.2+ brings several changes that not only impact how XenServer functions but how the product is offered.

XenServer 6.2+ Free is now an option, but it is unlicensed. After a device is provisioned with XenServer Free, it can be upgraded to the licensed version at any time. If you want to upgrade to XenServer Paid, the license is available for direct purchase from [Citrix Licensing Overview](https://www.citrix.com/buy/licensing/overview.html){: external}. Licensing is acquired on a per-socket basis.

Upgrading to XenServer Paid can still be initiated from within the XenServer application. Go to **Tools > License Manager** to view each Pool or Host that is associated with the account. Click **Buy Licenses** to be directed to the XenServer site to purchase licenses.
{: note}

For more information about XenServer 6.2+, see the [XenServer](https://www.xenserver.com/){: external} home page.
