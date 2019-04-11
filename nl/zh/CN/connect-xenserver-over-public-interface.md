---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: Xenserver
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# 通过公共接口连接到 XenServer
{: #connecting-to-xenserver-over-a-public-interface}

缺省情况下，在 {{site.data.keyword.BluSoftlayer}} 上安装的 XenServer 配置为将专用网络接口用于所有管理连接。如果专用网络上存在连接问题，请开具支持凭单。

更改此配置存在安全风险，因此建议不要更改。
{:tip}

如果要将 XenServer 重新配置为使用公共接口，请以 root 用户身份从主机（不能对主机进行汇聚！）运行以下命令。

1. `xe pif-list`

2. 记下要启用的接口的 UUID（最有可能是 eth1 或 bond1）。

3. 运行以下命令：

        `xe host-management-reconfigure pif-uuid=`

这些更改会立即生效，无需重新启动。
