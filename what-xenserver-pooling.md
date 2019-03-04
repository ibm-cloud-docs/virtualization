---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# XenServer pooling

XenServer pooling can be thought of as up to 16 servers that work as a cluster. This cluster not only allows for shared resources but allows for live migration of virtual machines that use XenMotion. One server acts as the "master node", while the rest of them are slaves. Opening any of the servers in the pool through XenCenter opens the management information for the entire pool. When a node joins the pool, its management password is changed to match the management password of the master node. When you remove a node from the pool, this password is not changed. 

All systems in the node must be from the same processor family, as in Intel or AMD. Ideally, hardware must be identical for pooling to be successful. Compatibility issues aren't entirely true with heterogeneous CPU pooling. You need to perform compatibility tests for yourself to see whether your hardware is compatible. For more information, see [Understanding heterogeneous CPU pooling ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://support.citrix.com/article/CTX127059){: new_window}.

You have two methods for creating a XenServer pool. One is through the XenCenter console by using the "New Pool" option. Follow the simple step-by-step on-screen instructions to create and add systems to the pool. The other method is by using the command line. On the new slave node, the following command can be executed:

        `xe pool-join master-address= master-username= master-password=`

**Note**: The management IP address is the 10.x.x.x IP address of the server if you used the default installation.

When you work with bonded networks, a newly joined slave can have its interfaces that are attached to the incorrect bonded networks. To correct this issue, destroy the bonds on the slave from the command line and re-create the bonds with the proper PIFs and networks.

You have two methods of ejecting a slave node from the pool. The first is by using XenCenter. Go to **Pool** and select **Remove Server**. Follow the simple step-by-step on-screen instructions that are provided. The other method is by using the command line. The removal of a node can be done from any node in the pool. You need to locate the _uuid_ of the node you want to eject by running the following command:

        `xe host-list`

Then, run this command:

        `xe pool-eject uuid=`

**Note**: When a node ejects, it reboots into a clean installation, which includes the removal of the networking configuration that is used to provide public and private connectivity. The password of the slave node is still the same as the master node after ejection from the pool. You might have to reconfigure networking via IPMI as the removal can break the slaves' settings.
