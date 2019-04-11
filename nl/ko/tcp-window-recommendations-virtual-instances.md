---
copyright:
  years: 2014, 2018
lastupdated: "2018-05-18"

subcollection: virtualization

keywords: TCP window size
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 가상 인스턴스에 대한 TCP 창 크기 최적화
{: #optimizing-tcp-window-size-for-virtual-instances}

다음 가이드라인을 사용하여 {{site.data.keyword.cloud}} 하이퍼바이저에서 최적의 성능을 얻도록 VSI에서 TCP 창 크기를 조정하십시오. {{site.data.keyword.cloud_notm}} 가상 호스트는 3 - 6MB 범위의 기본 TCP 버퍼 크기를 가집니다.

VSI로부터 최상의 성능을 얻으려면 3 - 6MB 범위 아래로 TCP 창 크기를 설정해야 합니다. 커널이 실제 메모리 한계를 요청된 값의 두 배로 설정하여 최대 설정을 효율적으로 두 배가 되게 합니다. 최대 TCP 창 크기를 2MB 아래로 유지하십시오. 자동 튜닝을 사용하면 각 연결에 대해 수신자 버퍼 크기(및 TCP 창 크기)가 동적으로 업데이트됩니다. 연결당 메모리 공간 기본값은 다음과 같은 요소 배열로 설정됩니다.

* net.ipv4.tcp_rmem - TCP 수신 버퍼를 위해 예약된 메모리입니다.
* net.ipv4.tcp_wmem - TCP 송신 버퍼를 위해 예약된 메모리입니다.

이러한 배열의 값은 자동 튜닝 한계를 설정하고 메모리 사용을 밸런싱하는 데 사용하는 최소, 초기 및 버퍼 크기입니다. 현재 메모리 창 설정을 보려면 `sysctl -a | grep mem` 명령을 실행하십시오. 

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

모든 TCP 설정을 보려면 명령을 실행하십시오. `sysctl -a | grep tcp`

표준 고급 TCP 기능이 사용 가능한지 확인하십시오. 

    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_sack = 1
    net.ipv4.tcp_window_scaling = 1

자동 튜닝이 사용 가능한지 확인하려면 다음 설정에 "1" 값이 있는지 확인하십시오.

    net.ipv4.tcp_moderate_rcvbuf = 1

TCP 창 설정을 변경하려면 설정을 /etc/sysctl.conf 파일에 추가하십시오. 

<!--**Note:** The preceding general recommendations are used to tune the TCP window sizes of a VSI in the public cloud. You have many ways to optimize your network for different workloads.-->

다시 시작하는 동안 변경사항을 보존하려면 `rc.local` 파일에서 튜닝 명령을 추가해야 합니다.
