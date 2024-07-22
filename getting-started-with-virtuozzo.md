---



copyright:
  years: 2014, 2023
lastupdated: "2024-04-19"

subcollection: virtualization

---

{{site.data.keyword.attribute-definition-list}}

# Getting started with Virtuozzo
{: #getting-started-with-virtuozzo}

Virtuozzo is a container-based virtualization solution that allows the sharing of hardware by using an abstraction layer.  Virtuozzo creates containers, also known as VEs or VPSs, that simulate a server. The container acts and responds mostly as if it were a stand-alone server. The container is separate from other containers that are on the same physical server in that they cannot access other containers files, IPC resources, or memory. The network can be configured to be shared between multiple containers or isolated.
{: shortdesc}

Virtuozzo is not a hypervisor or a software interface to a hypervisor. It operates in a mixture of kernel and user space within a proprietary kernel. This configuration allows containers to essentially borrow resources when needed, while the resources are available on the host node.

## Before you begin
{: #before-you-begin-virtuozzo}

* Go to your console's device menu. For more information, see [Navigating to devices](/docs/virtual-servers?topic=virtual-servers-navigating-devices).
* Make sure that you have any necessary account permissions and device access. Only the account owner, or a user with the **Manage Users** Classic infrastructure permission can adjust the permissions.

For more information about permissions, see [Managing classic infrastructure access](/docs/account?topic=account-mngclassicinfra) and [Managing device access](/docs/virtual-servers?topic=virtual-servers-managing-device-access).

## Virtuozzo hardware requirements
{: #virtuozzo-hardware-requirements}

* You need at least 2 GB of RAM. 4 GB to 8 GB or more of RAM is recommended. Virtuozzo alone, with no Containers, can use 2 GB (or more) of RAM depending on what processes are running.
* At least 4 GB of free disk space is recommended.
* Network


