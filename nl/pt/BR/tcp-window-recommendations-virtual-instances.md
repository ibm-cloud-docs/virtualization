---
copyright:
  years: 2014, 2018
lastupdated: "2018-05-18"

subcollection: virtualization

keywords: TCP window size
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Otimizando o tamanho da janela TCP para instâncias virtuais
{: #optimizing-tcp-window-size-for-virtual-instances}

Use as diretrizes a seguir para ajustar o tamanho da janela TCP em uma VSI para obter o desempenho ideal em um hypervisor do {{site.data.keyword.cloud}}. Os hosts virtuais do {{site.data.keyword.cloud_notm}} têm um tamanho de buffer TCP padrão com o intervalo de 3 a 6 MB.

Para obter o melhor desempenho de sua VSI, é necessário configurar o tamanho da janela TCP sob o intervalo de 3 a 6 MB. O kernel configura o limite de memória real para o dobro do valor solicitado, dobrando efetivamente as configurações máximas. Mantenha o tamanho máximo da janela TCP em 2 MB ou abaixo. Com o ajuste automático ativado, o tamanho do buffer do receptor (e o tamanho da janela TCP) é atualizado dinamicamente para cada conexão. Os padrões de espaço de memória de conexão são configurados com as matrizes de elemento a seguir:

* net.ipv4.tcp_rmem - Essa é a memória que está reservada para buffers de recebimento do TCP
* net.ipv4.tcp_wmem - Essa é a memória que está reservada para buffers de envio do TCP

Os valores nessas matrizes são os tamanhos de buffer mínimo, inicial e máximo que você usa para configurar os limites no ajuste automático e balancear o uso de memória. Para visualizar as configurações atuais da janela de memória, execute o comando: `sysctl -a | grep mem`

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

Para visualizar todas as configurações de TCP, execute o comando: `sysctl -a | grep tcp`

Verifique se os recursos do TCP avançados padrão estão ativados:

    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_sack = 1
    net.ipv4.tcp_window_scaling = 1

Para verificar se o ajuste automático está ativado, verifique se a configuração a seguir tem um valor de "1":

    net.ipv4.tcp_moderate_rcvbuf = 1

Para mudar as configurações de sua janela TCP, inclua as configurações em seu arquivo /etc/sysctl.conf.

<!--**Note:** The preceding general recommendations are used to tune the TCP window sizes of a VSI in the public cloud. You have many ways to optimize your network for different workloads.-->

Para preservar as mudanças entre reinicializações, certifique-se de incluir os comandos de ajuste em seu arquivo `rc.local`.
