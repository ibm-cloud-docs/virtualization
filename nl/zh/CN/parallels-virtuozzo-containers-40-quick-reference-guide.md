---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization
---

# Parallels Virtuozzo Containers 4.0 快速参考指南
{: #parallels-virtuozzo-containers-4-0-quick-reference-guide}

使用以下信息可了解 Virtuozzo 容器实用程序和常用术语。

## 常规实用程序
{: #general-utilities}

常规实用程序用于日常维护任务。

|常规实用程序||
|---|---|
|vzctl|用于控制容器的实用程序。|
|vzlist|用于查看节点上现有容器的列表以及其他信息的实用程序。|
|vzquota|用于控制 Virtuozzo Containers 磁盘限额的实用程序。|
{: caption="表 1. 常规实用程序" caption-side="top"}

## 许可实用程序
{: #licensing-utilities}

许可实用程序用于安装新许可证，查看许可证状态或生成需要新许可证的许可证请求。

|许可实用程序||
|---|---|
|vzlicview|用于显示 Virtuozzo 许可证状态和参数的实用程序。|
|vzlicload|用于管理硬件节点上 Virtuozzo 许可证的实用程序。|
|vzlicupdate|用于激活 Virtuozzo Containers 安装，更新在硬件节点上安装的 Virtuozzo 许可证，或者将 Virtuozzo 许可证从源节点传输到目标节点的实用程序。|
{: caption="表 2. 许可实用程序" caption-side="top"}

## 容器迁移实用程序
{: #container-migration-utilities}

容器迁移工具用于在硬件节点之间或在一个硬件节点内迁移容器。

|容器迁移实用程序||
|---|---|
|vzmigrate|用于将容器从一个硬件节点迁移到另一个硬件节点的实用程序。|
|vzmlocal|用于本地克隆或移动容器的实用程序。|
|vzp2v|用于将物理服务器迁移到节点上容器的实用程序。|
|vzv2p|用于将容器迁移到物理服务器的实用程序。|
{: caption="表 3. 容器迁移实用程序" caption-side="top"}

## 容器备份实用程序
{: #container-backup-utilities}

容器备份实用程序用于备份和复原容器专用区域、配置文件、操作脚本和配额信息。

|容器备份实用程序||
|---|---|
|vzbackup|用于备份容器的实用程序。|
|vzrestore|用于复原已备份容器的实用程序。|
|vzabackup|用于备份硬件节点及其容器的实用程序。此实用程序需要 Parallels Agent 软件。|
|vzarestore|用于复原已备份硬件节点和容器的实用程序。此实用程序需要 Parallels Agent 软件。|
{: caption="表 4. 容器备份实用程序" caption-side="top"}

## 模板管理实用程序
{: #template-management-utilities}

模板管理工具用于创建模板，维护容器中的应用程序以及将应用程序安装到容器中。

|模板管理实用程序||
|---|---|
|vzpkg|用于在容器内或硬件节点本身上管理操作系统和应用程序 EZ 模板的实用程序。|
|vzmktmpl|用于创建操作系统和应用程序 EZ 模板的实用程序。|
|vzveconvert|用于将基于 Virtuozzo 标准模板的容器转换为基于 EZ 模板的容器的实用程序。|
|vzpkgproxy|用于创建高速缓存代理服务器来处理操作系统和应用程序 EZ 模板的实用程序。|
|vzrhnproxy|用于创建 RHN 代理服务器来处理 RHEL 4 和 RHEL 5 操作系统 EZ 模板中随附包的实用程序。|
|vzpkgls|用于获取硬件节点上和容器中可用模板列表的实用程序。|
|vzpkginfo|用于获取有关硬件节点上所安装任何模板的信息的实用程序。|
|vzpkgcreat|用于通过二进制 RPM 或 DEB 文件创建新的包集。|
|vzpkgadd|用于将模板添加到容器的实用程序。|
|vzpkglink|用于将容器内的实际文件替换为节点上文件的符号链接的实用程序。|
|vzpkgrm|用于从容器中除去模板的实用程序。|
|vzpkgcache|用于在安装新模板后更新一组预安装的容器归档。|
{: caption="表 5. 模板管理实用程序" caption-side="top"}

## 补充工具
{: #supplementary-tools}

补充工具用于在硬件节点和容器上下文中执行各种任务。

|补充工具||
|---|---|
|vzup2date|用于更新 Virtuozzo 软件和模板的实用程序。|
|vzup2date-mirror|用于创建 Virtuozzo 官方存储库本地镜像的实用程序。|
|vzfsutil|用于 VZFS 优化和一致性检查的实用程序。|
|vzcache|用于通过对不同容器中完全相同的文件进行高速缓存，以获取额外磁盘空间的实用程序。|
|vzsveinstall|用于在硬件节点上创建服务容器的实用程序。|
|vzsveupgrade|用于更新服务容器内包的实用程序。|
|vzps|作为标准 ps 和 top 实用程序运行，并添加了容器相关功能的实用程序。|
|vztop|作为标准 ps 和 top 实用程序运行，并添加了容器相关功能的实用程序。|
|vzsetxinetd|用于将某些服务在独立方式和 `xinetddependent` 方式之间切换的实用程序。|
|vzdqcheck|用于从配额角度显示文件空间当前使用情况。|
|vzdqdump|用于从内核或配额文件转储 Container 用户或组配额限制和宽限期，或者将这些信息装入到配额文件的实用程序。|
|vzdqload|用于从内核或配额文件转储 Container 用户或组配额限制和宽限期，或者将这些信息装入到配额文件的实用程序。|
|vznetstat|用于按容器显示网络流量使用情况统计信息的实用程序。|
|vzcpucheck|用于按容器检查 CPU 使用率的实用程序。|
|vzmemcheck|用于检查硬件节点和容器当前内存参数的实用程序。|
|vzcalc|用于按容器计算资源使用情况的实用程序。|
|vzcheckovr|用于检查当前系统过量配置和总资源控制设置安全性的实用程序。|
|vzstat|用于实时监视硬件节点和容器资源使用量的实用程序。|
|vzpid|用于显示进程所属的容器标识的实用程序。|
|vzsplit|用于生成容器配置文件样本的实用程序，可将硬件节点“拆分”成相等部分。|
|vzcfgscale|用于缩放容器配置的实用程序。|
|vzcfgvalidate|用于验证容器配置文件正确性的实用程序。|
|vzcfgconvert|用于将 Virtuozzo 2.0.2 容器配置文件转换为 Virtuozzo 2.5.x 格式的实用程序。|
|vzstatrep|用于分析 vzlmd 收集的日志，并根据这些日志生成统计信息报告（文本和图形格式）的实用程序。|
|vzreport|用于起草问题报告并自动将其发送给 Parallels 支持团队的实用程序。|
|vzhwcalc|用于扫描任何 Linux 服务器上的主资源，并创建指明这些信息的文件的实用程序。|
|vzveconvert|用于将基于 Virtuozzo 标准操作系统模板的容器转换为基于 EZ 模板的容器的实用程序。|
|vznetcfg|用于管理硬件节点上的网络设备的实用程序。|
|vzmtemplate|用于将安装的操作系统和应用程序模板从一个硬件节点迁移到另一个硬件节点的实用程序。|
{: caption="表 6. 补充工具" caption-side="top"}

## 常用术语
{: #common-terms}

|常用术语||
|---|---|
|硬件节点|硬件节点 (HN) 是用于托管容器的物理计算机。|
|容器|容器在功能上与隔离的独立服务器完全相同，具有自己的 IP 地址、进程、文件、用户、自己的版本、自己的配置文件、自己的应用程序、系统库和其他服务器功能。容器共享硬件节点和相同的操作系统内核，但容器彼此之间是隔离的。每个容器都具有以 1 开头的唯一数字标识。|
|主机操作系统|主机操作系统（或主机）是安装在硬件节点上的操作系统。也称为容器 0，与具有以 1 开头的标识的实际容器相对。|
|模板|模板（或包集）是一组原始应用程序文件，经过重新打包，可通过 Virtuozzo 文件系统 (VZFS) 进行安装。Virtuozzo 中提供了两种类型的模板：操作系统模板（可用于基于这些模板创建新容器）和应用程序模板（可以在容器创建后添加到容器）。|
|PIM|Parallels Infrastructure Manager (PIM) 是一种基于 Web 的管理工具，专为主机管理员而设计。|
|PMC|Parallels Management Console (PMC) 是一种具有图形用户界面的远程管理工具，专为主机管理员而设计。|
|PPP|Parallels Power Panel 是一种基于 Web 的管理工具，专为容器所有者而设计。|
{: caption="表 7. 常用术语" caption-side="top"}
