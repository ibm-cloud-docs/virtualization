---
copyright:
  years: 2014, 2018
lastupdated: "2018-08-16"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# What is Virtuozzo?
{: #what-is-virtuozzo}

Virtuozzo is a container-based virtualization solution that allows the sharing of hardware by using an abstraction layer.  Virtuozzo creates containers, also known as VEs or VPSs, that simulate a server. The container acts and responds mostly as if it were a stand-alone server. The container is separate from other containers that are on the same physical server in that they cannot access other containers files, IPC resources, or memory. The network can be configured to be shared between multiple containers or isolated.
{:shortdesc}

Virtuozzo is not a hypervisor or a software interface to a hypervisor. It operates in a mixture of kernel and user space within a proprietary kernel. This configuration allows containers to essentially borrow resources when needed, while the resources are available on the host node.

More information can be found at [Virtuozzo ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://virtuozzo.com/){: new_window}.
