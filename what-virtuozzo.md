---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-11"
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# What is Virtuozzo?

Virtuozzo is a container-based virtualization solution that allows the sharing of hardware, via an abstraction layer.  Virtuozzo creates Containers, also known as VE’s or VPS’s, that simulate a server.  The container acts and responds mostly as if it were a stand-alone server. The container is completely separate from other containers that are located on the same physical server in that they can not access other containers files, ipc resources, or memory.  The network can be configured to be shared between multiple containers or completely isolated.
{:shortdesc}

Virtuozzo is not a hypervisor or a software interface to a hypervisor. It strictly operates in a mixture of kernel and userspace within a proprietary kernel. This configuration allows containers to essentially borrow resources when needed, as long as the resources are available on the host node.

More information can be found at [Virtuozzo ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://virtuozzo.com/){: new_window}.
