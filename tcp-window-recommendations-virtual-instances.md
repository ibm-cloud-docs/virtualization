---
copyright:
  years: 2014, 2018
lastupdated: "2018-05-18"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Optimizing TCP window size for virtual instances
{: #optimizing-tcp-window-size-for-virtual-instances}

Use the following guidelines to tune the TCP window size on a VSI for optimal performance on an {{site.data.keyword.cloud}} hypervisor. {{site.data.keyword.cloud_notm}} virtual hosts have a default TCP buffer size with the range 3 - 6 MB.

To get the best performance out of your VSI, you need to set the TCP window size under the 3 - 6 MB range. The kernel sets the actual memory limit to twice the requested value, effectively doubling the maximum settings. Keep the maximum TCP window size at or under 2 MB. With auto-tuning enabled, the receiver buffer size (and TCP window size) dynamically updates for each connection. The per connection memory space defaults are set with the following element arrays:

* net.ipv4.tcp_rmem - This is the memory that is reserved for TCP receive buffers
* net.ipv4.tcp_wmem - This is the memory that is reserved for TCP send buffers

The values in these arrays are the minimum, initial, and maximum buffer sizes that you use to set the limits on auto-tuning and balance memory usage. To view your current memory window settings, run the command: `sysctl -a | grep mem`

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

To view all the TCP settings, run the command: `sysctl -a | grep tcp`

Verify that the standard advanced TCP features are enabled:

    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_sack = 1
    net.ipv4.tcp_window_scaling = 1

To verify whether auto-tuning is enabled, check to that the following setting has a value of "1":

    net.ipv4.tcp_moderate_rcvbuf = 1

To change your TCP window settings, add the settings to your /etc/sysctl.conf file.

<!--**Note:** The preceding general recommendations are used to tune the TCP window sizes of a VSI in the public cloud. You have many ways to optimize your network for different workloads.-->

To preserve the changes across restarts, make sure to add the tuning commands in your `rc.local` file.
