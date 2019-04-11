---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 了解 UBC/用户 Bean 计数器值
{: #understanding-ubc-user-beancounter-values}

如果对 UBC 不了解，请使用 SLM 方式。SLM 使用起来更容易，并且提供了相同类型的控制。

|名称|类型|描述|
|---|---|---|
|*主要参数*|||
|`numproc`|*系统*|进程数和线程数|
|`numtcpsock`|*系统*|TCP 套接字数|
|`numothersock`|*系统*|除 TCP 以外的套接字数|
|`vmguardpages`|*系统*|内存分配保证|
|`cpuunits`|*CPU*|CPU 计算能力|
|`diskspace`|*磁盘*|磁盘空间配额|
|`rate`|*网络*|网络带宽|
|`ratebound`|*网络*|网络带宽是否有限制|
|*其他参数*|||
|`kmemsize`|*系统*|分配给此容器中进程的不可交换内核内存的大小|
|`tcpsndbuf`|*系统*|TCP 发送缓冲区的总大小|
|`tcprcvbuf`|*系统*|TCP 接收缓冲区的总大小|
|`othersockbuf`|*系统*|UNIX 域套接字缓冲区、UDP 和其他数据报协议发送缓冲区的总大小|
|`dgramrcvbuf`|*系统*|UDP 和其他数据报协议的接收缓冲区|
|`oomguardpages`|*系统*|内存过量分配时保证的内存量（内存不足终止保证）|
|`privvmpages`|*系统*|内存分配限制|
|`lockedpages`|*系统*|不允许换出的进程页面数（由 [mlock(2) ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](http://linux.die.net/man/2/mlock) 锁定的页面数）|
|`shmpages`|*系统*|页面中共享内存的总大小（进程间通信或 IPC、共享匿名映射和 tmpfs 对象）|
|`physpages`|*系统*|进程使用的 RAM 页面总数|
|`numfile`|*系统*|打开文件数|
|`numflock`|*系统*|文件锁定数|
|`numpty`|*系统*|伪终端数|
|`numsiginfo`|*系统*|siginfo 结构数|
|`dcachesize`|*系统*|内存中锁定的 dentry 和 inode 结构的总大小|
|`numiptent`|*系统*|NETFILTER（IP 包过滤）条目数|
|`cpulimit`|*CPU*|CPU 使用量限制|
|`diskinodes`|*磁盘*|磁盘索引节点（文件系统对象）配额|
|`quotatime`|*磁盘*|磁盘配额宽限期|
|`quotaugidlimit`|*磁盘*|对 uid 和 gid 统计条目数的限制|
<caption>表 1. SLM 参数描述</caption>

可以通过在服务器上的 PIM 中，转至**管理 > 支持 > 下载 > 管理 UBC 资源管理员指南**，以访问 VzLinuxUBCMgmt.pdf 或以 HTML 格式查看此指南。

有关 UBC 的更多信息，请参阅 [OpenVZ Virtuozzo Containers, Category: UBC ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](http://wiki.openvz.org/Category:UBC)。**注：**OpenVZ 中并非所有内容都与 Virtuozzo 相同。
