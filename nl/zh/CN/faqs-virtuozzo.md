---



copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# 常见问题：Virtuozzo
{: #faqs-virtuozzo}

## 需要将静态 IP 地址还是可移植 IP 地址用于 Virtuozzo？
{: #do-i-need-static-or-portable-ip-addresses-for-virtuozzo-}

* 静态 IP 地址：如果只有一个硬件节点（每个 VLAN 一个或总计一个），请使用静态 IP 地址
* 可移植 IP 地址：
    * 路由到 VLAN：如果同一 VLAN 中有多个主机节点，请使用此方法。您还可以使用此方法将容器迁移到同一 VLAN 中的不同硬件节点，而不用更改容器的 IP。
    * VLAN 上的辅助子网：此方法有效，而且不需要辅助网关。您有 3 个 IP 地址不能使用，因为这 3 个 IP 地址要分别用于主机标识、网关和广播。

每个 IP 的成本会根据路由的类型而变化。选择要为其订购 IP 地址的服务器后，门户网站中会显示相应价格。通过开具支持凭单，可以随时更改路由。
{:tip}

## 可以将 Virtuozzo Containers 3 迁移到 Virtuozzo Containers 4 硬件节点，而不产生停机时间吗？
{: #can-i-migrate-a-virtuozzo-3-container-to-a-virtuozzo-container-4-hardware-node-with-no-downtime-}

**Linux：**

可以。缺省情况下，Virtuozzo Containers for Linux 4 以兼容性方式运行。如果 Virtuozzo Containers 未以兼容性方式运行，那么可以通过 SSH 在硬件节点上使用以下命令来启动 Virtuozzo Containers。

`/usr/sbin/vzagent_compat_ctl start`

**Windows（不受支持）：**

对于 Virtuozzo Containers 4 Windows，在传输期间会产生停机时间（20 秒到 5 分钟），因为没有要传递的联机选项。

如果使用的是 Virtuozzo 3.0 for Windows，那么需要将硬件节点至少升级为 Virtuozzo 3.5.1 for Windows。

缺省情况下，Virtuozzo Containers 4 for Windows 以兼容性方式运行。

此外，还可以通过在 Virtuozzo Containers 4 硬件节点上，将完全备份复制到目标节点，运行 `vzbackupsync.exe` 并将备份复原为正常，从而执行从 3.5.1 复原备份的操作。

确保在新的硬件节点上安装相同的模板。
{:tip}

## 容器是否彼此隔离并且与主机隔离？
{: #are-containers-isolated-from-each-other-and-the-host-}

容器受到限制，无法装入 LKM，无法对其自己的环境执行 `chroot(“../../../”)`，也不会共享 IPC 资源（信号量集和共享内存段）。

对于 Virtuozzo 如何管理资源并隔离进程，您不必有安全性方面的担忧。通过桥接的网络配置，同一 VLAN 中的所有容器（在同一硬件节点上）都可以看到彼此的流量。强烈建议使用路由的方式。

## 为什么无法在 Virtuozzo 中高速缓存 RedHat 和 SUSE 操作系统模板？
{: #why-can-t-i-cache-redhat-and-suse-os-templates-in-virtuozzo-}

如果使用的是 Vitruozzo，那么对 RedHat 和 SUSE 操作系统模板进行高速缓存时，可能会遇到问题。这些操作系统模板需要额外的许可，但 {{site.data.keyword.cloud_notm}} 并未提供这些许可。如果收到以下任何错误，说明您需要向相应的供应商注册您自己的帐户，并在 /etc/vztt/url.map（在 Virtuozzo 3 中为 /etc/vztt/vztt.conf）中配置相应的变量。

||||
|---|---|---|
|TIMESTAMP|单击此处可打开/关闭操作详细信息。更新操作系统模板高速缓存|**失败**|
|TIMESTAMP|对环境“virtuozzo00.softlayer.local”和包 .redhat-as4-x86_64 执行的操作 update 已启动||
|TIMESTAMP|对环境“virtuozzo00.softlayer.local”执行的操作 update 完成，但有错误：无法更新包：执行失败：错误：URL $RH_SERVER/download/mirrors/redhat-as4 内容未定义变量。您可以在 /etc/vztt/url.map 中定义此变量。|**失败**|
|TIMESTAMP|单击此处可打开/关闭操作详细信息。更新操作系统模板高速缓存|**失败**|
|TIMESTAMP|对环境“virtuozzo00.softlayer.local”和包 .redhat-el5-x86_64 执行的操作 update 已启动||
|TIMESTAMP|对环境“virtuozzo00.softlayer.local”执行的操作 update 完成，但有错误：无法更新包：执行失败：错误：URL $RH_SERVER/download/mirrors/redhat-el5 内容未定义变量。您可以在 /etc/vztt/url.map 中定义此变量。请参阅《Virtuozzo 安装指南》以获取更多详细信息。|**失败**|
|TIMESTAMP|单击此处可打开/关闭操作详细信息。更新操作系统模板高速缓存|**失败**|
|TIMESTAMP|对环境“virtuozzo00.softlayer.local”和包 .sles-10-x86_64 执行的操作 update 已启动||
|TIMESTAMP|对环境“virtuozzo00.softlayer.local”执行的操作 update 完成，但有错误：无法更新包：执行失败：错误：URL $SLES_SERVER/download/mirrors/suse-es10 内容未定义变量。您可以在 /etc/vztt/url.map 中定义此变量。请参阅《Virtuozzo 安装指南》以获取更多详细信息。.|**失败**|
|TIMESTAMP|单击此处可打开/关闭操作详细信息。更新操作系统模板高速缓存|**失败**|
|TIMESTAMP|对环境“virtuozzo01.softlayer.local”和包 .redhat-as4-x86_64 执行的操作 update 已启动||
|TIMESTAMP|对环境“virtuozzo01.softlayer.local”执行的操作 update 完成，但有错误：无法更新包：执行失败：错误：URL $RH_SERVER/download/mirrors/redhat-as4 内容未定义变量。您可以在 /etc/vztt/url.map 中定义此变量。请参阅《Virtuozzo 安装指南》以获取更多详细信息。.|**失败**|
|TIMESTAMP|单击此处可打开/关闭操作详细信息。更新操作系统模板高速缓存|**失败**|
|TIMESTAMP|对环境“virtuozzo01.softlayer.local”和包 .redhat-el5-x86_64 执行的操作 update 已启动||
|TIMESTAMP|对环境“virtuozzo01.softlayer.local”执行的操作 update 完成，但有错误：无法更新包：执行失败：错误：URL $RH_SERVER/download/mirrors/redhat-el5 内容未定义变量。您可以在 /etc/vztt/url.map 中定义此变量。|**失败**|
|TIMESTAMP|单击此处可打开/关闭操作详细信息。更新操作系统模板高速缓存。          失败<br/><br/>Oct 29, 2008 01:16:56 PM     对环境“virtuozzo01.softlayer.local”和包 .sles-10-x86_64 执行的操作 update 已启动||
|TIMESTAMP|对环境“virtuozzo01.softlayer.local”执行的操作 update 完成，但有错误：无法更新包：执行失败：错误：URL $SLES_SERVER/download/mirrors/suse-es10 内容未定义变量。|**失败**|
|TIMESTAMP|正在完成进程|**失败**|

## 为什么可以对 ServiceVE 执行 ping 操作，但却无法通过 SSH (PIM/PMC) 进行登录？
{: #why-can-i-ping-my-serviceve-but-cannot-log-in-to-via-ssh-pim-pmc-}

确保分配给主机节点的 IP 地址只有门户网站中列出的 IP 地址（1 个公共 IP 和 1 个专用 IP [位于 10.0.0.0/8 中]）。

如果在主机节点上配置了任何范围文件，那么需要除去这些文件并重新启动联网。

    [root@virtuozzo ~]# mv –vi /etc/sysconfig/network-scripts/eth[0-9]-range[0-9]* ~/.sl-orig-configs/

    [root@virtuozzo ~]# /sbin/service network restart


`vzcp` 未在服务 VE 中启动。请从硬件节点在服务 VE 中启动 `vzcp`。

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp status

    vzcp 已停止

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp start

    正在启动 vzcpd：[  正常  ]

    正在启动 vzcp：[  正常  ]

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp status

    vzcp (pid 3775 3774 3771) 正在运行...

    [root@virtuozzo ~]#
