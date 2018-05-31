---
copyright:
  years: 2014, 2018
lastupdated: "2018-05-18"
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Overview of Parallels Server 4 Bare Metal

Parallels Server Bare Metal is a virtualization solution that provides hardware virtualization along side the software virtualization of Virtuozzo, providing technology for both virtual machines and containers*.
{:shortdesc}

**Command Line Interface**

Along with the usual Virtuozzo commands, there are additional commands that include `pctl`, `pmigrate`, `pstat`, and `prl_disk_tool`.

**Migration**

You can now migrate Virtuozzo Containers to virtual machines, physical servers to Virtuozzo Containers, and physical servers to virtual machines to allow for consolidation of services. This functionality also allows for one to migrate containers or VMs between bare metal servers, convert VMs that were created on different virtualized environments (V2V), and change Windows SIDs when you clone or deploy a Windows VM from a template. <!--Remember to not put all of your eggs into a single basket, but instead diversely architect your environment to meet your needs while maintaining a strong backup strategy.-->

**IP Address usage and VLAN's**

You can assign VMs to an IP from the physical server by using Parallels Tools that are in the VM. Additionally, PSBM allows for virtual switches and VLANs to be created within the VMs to better secure intra-VM network traffic.

**Executing commands**

You can run raw commands from the physical server directly within the Virtuozzo Containers and now within VMs (when parallels tools are installed within the VM), including user password resets.

**Process Accounting**

You use Parallels Server Bare Metal to increase and reduce the priority of resources that are allocated (CPU, Disk I/O Priority) to a VM quickly.

**Backup**

Parallels Server Bare Metal provides the functionality to backup and restore VMs and containers on either the local bare metal server or a remote bare metal server, to include full and incremental backups.

**Network accounting**

You use Parallels Server Bare Metal to easily view and locate VMs or Containers based on current and historical network throughput.

**Network**

Parallels Server Bare Metal uses Portable IP Addresses, while Virtuozzo uses either Portable or Static IP Addresses (depending on the configuration).

\* {{site.data.keyword.BluSoftlayer_full}} licenses only hardware virtual machines on Parallels Server 4 Bare Metal, unless otherwise indicated on the order form.
_VM_ = Virtual Machine. _V_ = VPS or Container.
