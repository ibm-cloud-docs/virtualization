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

# 透過公用介面連接 XenServer
{: #connecting-to-xenserver-over-a-public-interface}

依預設，XenServer 的 {{site.data.keyword.BluSoftlayer}} 安裝會配置為針對所有管理連線使用專用網路介面。如果您在專用網路上有連線問題，請開立支援問題單。

變更此配置會有安全風險，不建議這樣做。
{:tip}

如果您想要重新配置 XenServer 以使用公用介面，請從主機以 root 身分執行下列指令（無法將主機聯合排存！）。

1. `xe pif-list`

2. 記下您要啟用之介面的 uuid（最可能為 eth1 或 bond1）。

3. 執行下列指令：

        `xe host-management-reconfigure pif-uuid=`

這些變更會立即生效，不需要重新啟動。
