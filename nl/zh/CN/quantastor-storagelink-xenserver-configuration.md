---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: QuantaStor
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# 安装 QuantaStor StorageLink Xenserver
{: #installing-quantastor-storagelink-xenserver}

## 安装 QuantaStor Adapter for Citrix StorageLink
{: #installing-the-quantastor-adapter-for-citrix-storagelink}

1. 确保 XenServer 的安装获得使用 StorageLink 的相应许可。

只有通过 Citrix 预订“Enterprise”或“Platinum”许可证的客户才能使用 StorageLink 功能。具有 Citrix XenServer“Advanced”许可证的客户无法访问 StorageLink 功能。
{:tip}

2. 通过在管理员提示符处输入以下命令来停止 StorageLink 服务。或者，可以通过 XenServer 中的“服务”面板来停止 StorageLink 服务。

        C:\> net stop storagelink

3. 从 [OSNexus ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://www.osnexus.com/trynow/){: new_window} 下载可用的 StorageLink 适配器并运行该适配器。
4. 服务停止后，可以通过运行以下命令来安装 QuantaStor StorageLink 适配器：

        osn_quantastor_csladapter.exe

5. 将此 XML 块添加到 StorageLink 配置的末尾：
  * `C:\Program Files\Citrix\StorageLink\Server\scsi_device_id_config.xml`
  * `OSNEXUS`
  * `QUANTASTOR1`
  * 在此文件的开头，您会看到与 _2.0.0.4_ 类似的版本号。增大版本号，例如：2.0.0.5。版本号指示 StorageLink 需要使用这些新设备标识规则来升级 XenServer 系统。
6. 重新启动 Citrix StorageLink 服务：

        C:\> net start storagelink

## 验证 StorageLink 是否已正确装入
{: #verifying-that-storagelink-loaded-properly}

1. 登录到 Citrix StorageLink 管理器，选择左侧的“管理”树，并在 [QuantaStor ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://svn.osnexus.com/mediawiki/images/thumb/c/c8/Storagelink_admin.png/640px-Storagelink_admin.png){: new_window} 列表中查找 OS NEXUS QuantaStor。
2. 添加存储系统凭证，以便 StorageLink 可以为 QuantaStor 存储系统动态配置和分配存储卷。

## 安装 XenServer 多路径配置设置
{: #installing-the-xenserver-multipath-configuration-settings}

XenServer 使用 Linux 设备映射器多路径驱动程序 (`dmmp`) 来启用对硬件多路径的支持。`dmmp` 驱动程序使用名为 `multipath.conf` 的配置文件，该文件包含每个供应商的多路径方式和故障转移规则。

QuantaStor 有一些特殊规则需要添加到 `multipath.conf` 文件。编辑每个 XenServer `dom0` 节点上的 `/etc/multipath-enabled.conf` 文件。在该文件中的最后一个 device { } 部分之后添加以下节：

    device {
          vendor                  "OSNEXUS"
          product                 "QUANTASTOR"
          getuid_callout          "/sbin/scsi_id -g -u -s /block/%n"
          path_selector           "round-robin 0"
          path_grouping_policy    multibus
          failback                immediate
          rr_weight               uniform
          rr_min_io               100
          path_checker            tur
    }
