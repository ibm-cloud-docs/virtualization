---



copyright:
  years: 2014, 2018
lastupdated: "2018-01-05"


---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# FAQs: Virtuozzo

## Do I need static or portable IP addresses for Virtuozzo?

* Static IP address: Use a static IP address if you have only one hardware node (per VLAN or in total)
* Portable IP address:
    * Routed to VLAN: Use this method if you have multiple host nodes within the same VLAN. This method allows you to migrate a container to a different hardware node within the same VLAN without changing the IP of the container.
    * Secondary on VLAN: This method works, but a secondary gateway is not required. You lose 3 of the IP addresses due to host ID, gateway, and broadcast.

Costs per IP can change depending on the routed type. Prices are displayed in the portal after you select the server that you are ordering the IP addresses for. Routing can be changed at any time by opening a support ticket.
{:tip}

## Can I migrate a Virtuozzo 3 Container to a Virtuozzo Container 4 hardware node with no downtime?

**Linux:**

Yes. By default, Virtuozzo Containers for Linux 4 run in compatibility mode. If Virtuozzo Containers is not running in compatibility mode, then it is started by using the following command on the hardware node through SSH:

`/usr/sbin/vzagent_compat_ctl start`

**Windows (unsupported):**

<!--[Yes](http://kb.parallels.com/en/3955) <sup>[1](http://kb.parallels.com/en/3609) [2](http://kb.parallels.com/en/5475)</sup>--> <!--and [No] (http://kb.parallels.com/en/3591).-->  <!--This is not true [the other way around](http://kb.parallels.com/en/3955).--> With Virtuozzo Containers 4 Windows, there is downtime during the transition (between 20 seconds - 5 minutes) as there is no online option to pass.

If you use Virtuozzo 3.0 for Windows, then you need to upgrade the hardware node to at least Virtuozzo 3.5.1 for Windows.

Virtuozzo Containers 4 for Windows, by default, runs in compatibility mode.

Restoring backups from 3.5.1 on a Virtuozzo Containers 4 hardware node can also be performed by copying the full backup to the destination node, running `vzbackupsync.exe` and restoring the backup as normal. 

Make sure that the same templates are installed on the new hardware node. 
{:tip}

<!--1 http://kb.parallels.com/en/3609 - [Info] Containers migration from Virtuozzo 3.5.1 to Virtuozzo 4.0 via Parallels Management Console fails.-->

<!--2 http://kb.parallels.com/en/5475 - How do I migrate Containers from Virtuozzo for Windows 3.5.x to Virtuozzo for Windows 4?-->

<!--3 http://kb.parallels.com/en/3590 - [How To] How do I restore container backup from Virtuozzo 3.5.1 on 4.0 version?-->

## Are containers isolated from each other and the host?

Each container is restricted from loading LKMs, performing a chroot(“../../../”) out of its own environment and does not share IPC Resources (semaphore sets and shared memory segments).

There are no security concerns with how Virtuozzo manages resources and isolates the processes. With a bridged network configuration, all containers in the same VLAN (on the same hardware node) can see each other's traffic. Using routed mode is highly suggested.

## Why can't I cache RedHat and SUSE OS templates in Virtuozzo?

You can experience issues when you cache to RedHat and SUSE OS templates if you use Vitruozzo. These OS templates require extra licensing, which {{site.data.keyword.cloud_notm}} does not provide. If you receive any of the following errors, you need to have your own accounts with the respective vendors and configure the corresponding variables within /etc/vztt/url.map (/etc/vztt/vztt.conf in Virtuozzo 3.)

||||
|---|---|---|
|TIMESTAMP|Click here to open/close the operation details.Update OS Template Cache|**Failed**|
|TIMESTAMP|Operation update with the Env(s) "virtuozzo00.softlayer.local" and package(s) .redhat-as4-x86_64 is started||
|TIMESTAMP|Operation update with the Env(s) "virtuozzo00.softlayer.local" is finished with errors: Can not update packages: exec failed: Error: URL $RH_SERVER/download/mirrors/redhat-as4 contents undefined variable You can define this variable in /etc/vztt/url.map. .|**Failed**|
|TIMESTAMP|Click here to open/close the operation details.Update OS Template Cache|**Failed**|
|TIMESTAMP|Operation update with the Env(s) "virtuozzo00.softlayer.local" and package(s) .redhat-el5-x86_64 is started||
|TIMESTAMP|Operation update with the Env(s) "virtuozzo00.softlayer.local" is finished with errors: Can not update packages: exec failed: Error: URL $RH_SERVER/download/mirrors/redhat-el5 contents undefined variable You can define this variable in /etc/vztt/url.map. See Virtuozzo Installation Guide for more details. .|**Failed**|
|TIMESTAMP|Click here to open/close the operation details.Update OS Template Cache|**Failed**|
|TIMESTAMP|Operation update with the Env(s) "virtuozzo00.softlayer.local" and package(s) .sles-10-x86_64 is started||
|TIMESTAMP|Operation update with the Env(s) "virtuozzo00.softlayer.local" is finished with errors: Can not update packages: exec failed: Error: URL $SLES_SERVER/download/mirrors/suse-es10 contents undefined variable You can define this variable in /etc/vztt/url.map. See Virtuozzo Installation Guide for more details. .|**Failed**|
|TIMESTAMP|Click here to open/close the operation details.Update OS Template Cache|**Failed**|
|TIMESTAMP|Operation update with the Env(s) "virtuozzo01.softlayer.local" and package(s) .redhat-as4-x86_64 is started||
|TIMESTAMP|Operation update with the Env(s) "virtuozzo01.softlayer.local" is finished with errors: Can not update packages: exec failed: Error: URL $RH_SERVER/download/mirrors/redhat-as4 contents undefined variable You can define this variable in /etc/vztt/url.map. See Virtuozzo Installation Guide for more details. .|**Failed**|
|TIMESTAMP|Click here to open/close the operation details.Update OS Template Cache|**Failed**|
|TIMESTAMP|Operation update with the Env(s) "virtuozzo01.softlayer.local" and package(s) .redhat-el5-x86_64 is started||
|TIMESTAMP|Operation update with the Env(s) "virtuozzo01.softlayer.local" is finished with errors: Can not update packages: exec failed: Error: URL $RH_SERVER/download/mirrors/redhat-el5 contents undefined variable You can define this variable in /etc/vztt/url.map. .|**Failed**|
|TIMESTAMP|Click here to open/close the operation details.Update OS Template Cache          Failed<br/><br/>Oct 29, 2008 01:16:56 PM     Operation update with the Env(s) "virtuozzo01.softlayer.local" and package(s) .sles-10-x86_64 is started||
|TIMESTAMP|Operation update with the Env(s) "virtuozzo01.softlayer.local" is finished with errors: Can not update packages: exec failed: Error: URL $SLES_SERVER/download/mirrors/suse-es10 contents undefined variable .|**Failed**|
|TIMESTAMP|Completing the process|**Failed**|

## Why can I ping my ServiceVE but cannot login to via SSH (PIM/PMC)?

Make sure that the host node does not have any IP addresses assigned other than those that are listed in the Portal (1 public IP and 1 private IP [located in 10.0.0.0/8]).

If you have any range files that are configured on the host node, then you need to remove these files and restart networking.

    [root@virtuozzo ~]# mv –vi /etc/sysconfig/network-scripts/eth[0-9]-range[0-9]* ~/.sl-orig-configs/

    [root@virtuozzo ~]# /sbin/service network restart


vzcp is not started within the Service VE. Start vzcp within the Service VE from the hardware node.

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp status

    vzcp is stopped

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp start

    Starting vzcpd: [  OK  ]

    Starting vzcp: [  OK  ]

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp status

    vzcp (pid 3775 3774 3771) is running...

    [root@virtuozzo ~]#

