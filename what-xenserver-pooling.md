---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-23"
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# XenServer pooling

XenServer pooling can be thought of as up to 16 servers working as a cluster. This not only allows for shared resources but allows for live migration of virtual machines using XenMotion. One server will act as the "master node" while the rest of them will be slaves. Opening any of the servers in the pool through XenCenter will open the management information for the entire pool. When a node joins the pool, its management password is changed to match the management password of the master node. When removing a node from the pool this password is not changed. All systems in the node must be from the same processor family, as in Intel or AMD. You cannot have both Intel-based systems and AMD-based systems in the same pool as XenServer will not allow you to add them. Ideally, hardware should be identical for pooling to be most successful. This is not entirely true with Heterogeneous CPU Pooling.  You will have to perform compatibility tests for yourself to see if your hardware is compatible. For more information, see [Understanding Heterogeneous CPU Pooling ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://support.citrix.com/article/CTX127059){: new_window}.

There are two methods for creating a XenServer pool. One is through the XenCenter console through the "New Pool" at the top of the console. Follow the simple step-by-step on screen instructions to create and add systems to the pool. The other method is through the command line. On the new slave node, the following command can be executed:

        xe pool-join master-address= master-username= master-password=

**Note**: The management IP address is the 10.x.x.x IP address of the server using the default installation.

When dealing with bonded networks, a newly joined slave may have its interfaces attached to the incorrect bonded networks. To correct this issue destroy the bonds on the slave from the command line and recreate the bonds with the proper pifs and networks.

There are also two methods of ejecting a slave node from the pool. The first is through XenCenter by going to **Pool** and then selecting **Remove Server** from the top of the console. Follow the simple step-by-step on screen instructions provided on which node you wish to eject. The other method is through the command line. The removal of a node can be done from any node in the pool. The first step is to locate the uuid of the node you wish to eject by running the following command:

        xe host-list

The next command to be executed is the following:

        xe pool-eject uuid= 

**Note**: When a node ejects, it reboots into a clean installation, which includes the removal of the networking configuration used to provide public and private connectivity. The password of the slave node is still the same as the master node after ejection from the pool. You might have to reconfigure networking via IPMI as the removal could have broken the slaves settings completely.
