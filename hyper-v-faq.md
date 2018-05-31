---
copyright:
  years: 2014, 2018
lastupdated: "2018-05-18"
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# FAQs: Hyper-V

## What is Hyper-V?

Hyper-V is a virtualization system for the Windows 2008 Server Datacenter Edition that allows a single physical server to host multiple virtual machines all running their own operating system.

## What are the requirements to run Hyper-V?

Hyper-V must be installed on Windows 2008 Server 64-bit Datacenter Edition. All {{site.data.keyword.BluSoftlayer}} hardware meets the system requirements of Hyper-V. Microsoft recommends at least 2 GB of RAM for the server.

## Is Hyper-V available on any other versions of 2008?

{{site.data.keyword.BluSoftlayer_notm}} offers Hyper-V only on Datacenter Edition.

## Can Hyper-V run on Windows 2003? 

Hyper-V was developed for Windows 2008 and cannot run on Windows 2003.

## What operating systems can be installed on a virtual machine?

{{site.data.keyword.BluSoftlayer_notm}} supports the following operating systems on Hyper-V:

* All versions of Windows 2003 and 2008 Server
* CentOS, Fedora, and Ubuntu Linux Distributions

## How many virtual machines can a server run?

The number of virtual machines a server can run varies depending upon server processors, RAM, and hard disk space.

## Is the RAM customizable on each virtual machine?

Yes. You can use Hyper-V to customize the system resources for each virtual machine, including memory.

## How much RAM does a virtual machine need?

RAM needs vary based on requirements for the virtual machine. Check the system requirements for your guest operating system. The amount of memory that is provided to a virtual machine can be changed at any time.

## Can a virtual machine access to more than one processor?

You can grant a virtual machine access to multiple processors with any Windows virtual machine. However, Linux virtual machines are limited to a single processor.

## Can hard drive sizes change after a virtual machine is created?

Yes.

## Does each virtual operating system need to have a license?

Windows 2003 and 2008 virtual machines are licensed through {{site.data.keyword.BluSoftlayer_notm}}. Linux virtual machines are freely licensed and require no action.

## Will the virtual machines have access to the private network?

Yes. Virtual machines can connect to both a public and private network.

## What advantages are there to providing private network access to virtual machines?

Providing private network access to virtual machines allows virtual machines to communicate with each other. Private network access also allows virtual machines to communicate with other internal systems such as NAS and iSCSI and any other servers that you have with {{site.data.keyword.BluSoftlayer_notm}}.

## Can virtual machines use the secondary IP block that came with the server?

No. The secondary IP that was provided with your server is routed specifically to work on the physical server and not a virtual machine. You need portable IP blocks to connect your virtual machine to the network.

## What are portable IP addresses?

For information about portable IP addresses, see [Getting started with Subnets and IPs](/docs/infrastructure/subnets/getting-started.html).

## How do I order Hyper-V?

If you have an existing server, you can install Hyper-V by issue an OS Reload and changing the operating system to “Windows 2008 Server Datacenter Edition with Hyper-V." If you would like to have a new server that is provisioned with Hyper-V, place an order for the new server and select “Windows 2008 Server Datacenter Edition with Hyper-V” as the operating system.

## Is Hyper-V preinstalled with Windows 2008 Server Datacenter Edition?

Hyper-V is not preinstalled on Windows 2008 servers. If you want to install Hyper-V on your Windows 2008 server, you need to issue an OS Reload and select “Windows 2008 Server Datacenter Edition with Hyper-V” as the operating system.

## The OS Reload for Hyper-V is done. What's next?

For more information, see [Hyper-V setup](hyper-v-setup.html).

## A virtual machine will not start. “Could not initialize machine remoting system: Error:'Unspecified error'” How do I resolve this issue?

To resolve this problem, stop the Hyper-V service and restart it. This is typically caused by the Hyper-V service starting while the server activation occurs. Restarting the service brings the virtual machine back up after the activation completes.
