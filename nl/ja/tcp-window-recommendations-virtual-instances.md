---
copyright:
  years: 2014, 2018
lastupdated: "2018-05-18"

subcollection: virtualization

keywords: TCP window size
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 仮想インスタンスの TCP ウィンドウ・サイズを最適化する
{: #optimizing-tcp-window-size-for-virtual-instances}

{{site.data.keyword.cloud}} ハイパーバイザーで最適なパフォーマンスが得られるように、以下のガイドラインを使用して VSI の TCP ウィンドウ・サイズを調整してください。 {{site.data.keyword.cloud_notm}} 仮想ホストのデフォルトの TCP バッファー・サイズは、3 MB から 6 MB までの範囲です。

VSI のパフォーマンスを最大限に引き出すには、TCP ウィンドウ・サイズを 3 MB から 6 MB までの範囲より小さく設定する必要があります。 カーネルは、要求された値の 2 倍を実際のメモリー制限として設定するので、事実上、最大設定値は 2 倍になります。 最大 TCP ウィンドウ・サイズは 2 MB 以下にしておいてください。 自動チューニングを有効にすると、受信側バッファー・サイズ (および TCP ウィンドウ・サイズ) が接続ごとに動的に更新されます。 1 接続あたりのメモリー・スペースのデフォルトは、以下のエレメント配列で設定されます。

* net.ipv4.tcp_rmem - これは TCP 受信バッファー用に予約されるメモリーです
* net.ipv4.tcp_wmem - これは TCP 送信バッファー用に予約されるメモリーです

これらの配列の値は、最小バッファー・サイズ、初期バッファー・サイズ、最大バッファー・サイズです。これらのバッファー・サイズを使用して、自動チューニングの制限を設定し、メモリー使用量のバランスを取ります。 現在のメモリー・ウィンドウ設定値を調べるには、`sysctl -a | grep mem` コマンドを実行します。

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

TCP 設定値をすべて表示するには、`sysctl -a | grep tcp` コマンドを実行します。

標準の拡張 TCP 機能が有効になっていることがわかります。

    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_sack = 1
    net.ipv4.tcp_window_scaling = 1

自動チューニングが有効になっていることを調べるには、次の設定の値が「1」であることを確認します。

    net.ipv4.tcp_moderate_rcvbuf = 1

TCP ウィンドウ設定を変更するには、/etc/sysctl.conf ファイルに設定値を追加します。

<!--**Note:** The preceding general recommendations are used to tune the TCP window sizes of a VSI in the public cloud. You have many ways to optimize your network for different workloads.-->

再始動後も変更が保持されるようにするには、`rc.local` ファイルにチューニング・コマンドを追加します。
