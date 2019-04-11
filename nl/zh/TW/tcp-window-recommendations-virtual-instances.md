---
copyright:
  years: 2014, 2018
lastupdated: "2018-05-18"

subcollection: virtualization

keywords: TCP window size
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 最佳化虛擬實例的 TCP 視窗大小
{: #optimizing-tcp-window-size-for-virtual-instances}

請使用下列準則來調整 VSI 上的 TCP 視窗大小，以取得 {{site.data.keyword.cloud}} Hypervisor 上的最佳效能。{{site.data.keyword.cloud_notm}} 虛擬主機具有範圍 3 - 6 MB 的預設 TCP 緩衝區大小。

若要取得 VSI 的最佳效能，您需要設定範圍在 3 - 6 MB 下的 TCP 視窗大小。核心會將實際記憶體限制設為所要求值的兩倍，有效地將設定上限加倍。將 TCP 視窗大小上限保留為或低於 2 MB。啟用自動調整後，接收端緩衝區大小（及 TCP 視窗大小）會在每次連線時動態更新。每個連線記憶體空間預設值是使用下列元素陣列來設定的：

* net.ipv4.tcp_rmem - 這是保留給 TCP 接收緩衝區的記憶體
* net.ipv4.tcp_wmem - 這是保留給 TCP 傳送緩衝區的記憶體

這些陣列中的值為最小、起始及最大緩衝區大小，您用來設定自動調整及平衡記憶體用量的限制。若要檢視現行記憶體視窗設定，請執行指令：`sysctl -a | grep mem`

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

若要檢視所有 TCP 設定，請執行指令：`sysctl -a | grep tcp`

驗證標準進階 TCP 功能是否已啟用：

    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_sack = 1
    net.ipv4.tcp_window_scaling = 1

若要驗證自動調整是否已啟用，請檢查下列設定的值是否為 "1"：

    net.ipv4.tcp_moderate_rcvbuf = 1

若要變更 TCP 視窗設定，請將這些設定新增至 /etc/sysctl.conf 檔案。

<!--**Note:** The preceding general recommendations are used to tune the TCP window sizes of a VSI in the public cloud. You have many ways to optimize your network for different workloads.-->

若要保留重新啟動之間的變更，請確定在 `rc.local` 檔中新增調整指令。
