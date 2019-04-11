---
copyright:
  years: 2014, 2018
lastupdated: "2018-05-18"

subcollection: virtualization

keywords: TCP window size
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 优化虚拟实例的 TCP 窗口大小
{: #optimizing-tcp-window-size-for-virtual-instances}

使用以下准则来调整 VSI 上的 TCP 窗口大小，以在 {{site.data.keyword.cloud}} 系统管理程序上获得最佳性能。{{site.data.keyword.cloud_notm}} 虚拟主机具有缺省 TCP 缓冲区大小，范围为 3 到 6 MB。

要使 VSI 实现最佳性能，您需要将 TCP 窗口大小设置为在 3 到 6 MB 范围内。内核将实际内存限制设置为所请求值的两倍，从而有效地使最大设置翻倍。请使最大 TCP 窗口大小保持在小于或等于 2 MB。启用自动调整后，接收方缓冲区大小（和 TCP 窗口大小）会针对每个连接进行动态更新。每个连接的内存空间缺省值可使用以下元素数组进行设置：

* net.ipv4.tcp_rmem - 这是为 TCP 接收缓冲区保留的内存
* net.ipv4.tcp_wmem - 这是为 TCP 发送缓冲区保留的内存

这些数组中的值是最小缓冲区大小、初始缓冲区大小和最大缓冲区大小，用于设置自动调整的限制以及均衡内存使用情况。要查看当前内存窗口设置，请运行以下命令：`sysctl -a | grep mem`

    # sysctl -a | grep mem
    vm.overcommit_memory = 0
    vm.nr_hugepages_mempolicy = 0
    vm.lowmem_reserve_ratio = 256   256     32
    vm.meminfo_legacy_layout = 1
    vm.memory_failure_early_kill = 0
    vm.memory_failure_recovery = 1
    net.core.wmem_max = 124928
    net.core.rmem_max = 124928
    net.core.wmem_default = 124928
    net.core.rmem_default = 124928
    net.core.optmem_max = 20480
    net.ipv4.igmp_max_memberships = 20
    net.ipv4.tcp_mem = 365184       486912  730368
    net.ipv4.tcp_wmem = 4096        16384   1048576
    net.ipv4.tcp_rmem = 4096        87830   1048576
    net.ipv4.udp_mem = 365184       486912  730368
    net.ipv4.udp_rmem_min = 4096
    net.ipv4.udp_wmem_min = 4096

要查看所有 TCP 设置，请运行以下命令：`sysctl -a | grep tcp`

验证标准高级 TCP 功能是否已启用：

    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_sack = 1
    net.ipv4.tcp_window_scaling = 1

要验证是否启用了自动调整，请检查以下设置的值是否为“1”：

    net.ipv4.tcp_moderate_rcvbuf = 1

要更改 TCP 窗口设置，请向 /etc/sysctl.conf 文件添加相应设置。

<!--**Note:** The preceding general recommendations are used to tune the TCP window sizes of a VSI in the public cloud. You have many ways to optimize your network for different workloads.-->

要在重新启动之间保留更改，请确保在 `rc.local` 文件中添加调整命令。
