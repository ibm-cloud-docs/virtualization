---
copyright:
  years: 2014, 2018
lastupdated: "2018-05-18"

subcollection: virtualization

keywords: TCP window size
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Optimización del tamaño de ventana de TCP para instancias virtuales
{: #optimizing-tcp-window-size-for-virtual-instances}

Utilice las directrices siguientes para ajustar el tamaño de ventana de TCP en una VSI para obtener un rendimiento óptimo en un hipervisor de
{{site.data.keyword.cloud}}. Los hosts virtuales de {{site.data.keyword.cloud_notm}} tienen un tamaño de almacenamiento intermedio de TCP predeterminado con el rango de 3 a 6 MB.

Para obtener el mejor rendimiento de su VSI, necesitará establecer el tamaño de ventana de TCP por debajo del rango de 3 a 6 MB. El kernel establece el límite de memoria real en el doble del valor solicitado, duplicando de manera efectiva los valores máximos. Mantenga el tamaño máximo de la ventana de TCP en 2 MB o menos. Con el ajuste automático habilitado, el tamaño del almacenamiento dinámico de recepción (y el tamaño de ventana de TCP) se actualiza de forma dinámica para cada conexión. Los valores predeterminados de espacio de memoria por conexión se establecen con las matrices de elementos siguientes:

* net.ipv4.tcp_rmem - Esta es la memoria reservada para almacenamientos intermedios de recepción TCP
* net.ipv4.tcp_wmem - Esta es la memoria reservada para almacenamientos intermedios de envío TCP

Los valores de estas matrices son los tamaños de almacenamiento intermedio mínimo, inicial y máximo que puede utilizar para establecer los límites del ajuste automático y equilibrar el uso de memoria. Para ver los valores actuales de la ventana de memoria, ejecute el mandato:
`sysctl -a | grep mem`

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

Para ver todos los valores de TCP, ejecute el mandato: `sysctl -a | grep tcp`

Compruebe que las características TCP estándares avanzadas estén habilitadas:

    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_sack = 1
    net.ipv4.tcp_window_scaling = 1

Para comprobar si el ajuste automático está habilitado, compruebe que el parámetro siguiente tienen el valor "1":

    net.ipv4.tcp_moderate_rcvbuf = 1

Para cambiar los valores de la ventana de TCP, añada los valores al archivo /etc/sysctl.conf.

<!--**Note:** The preceding general recommendations are used to tune the TCP window sizes of a VSI in the public cloud. You have many ways to optimize your network for different workloads.-->

Para conservar los cambios entre reinicios, asegúrese de añadir los mandatos de ajuste en el archivo `rc.local`.
