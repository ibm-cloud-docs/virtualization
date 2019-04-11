---
copyright:
  years: 2014, 2018
lastupdated: "2018-05-18"

subcollection: virtualization

keywords: TCP window size
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Ottimizzazione della dimensione della finestra TCP per le istanze virtuali
{: #optimizing-tcp-window-size-for-virtual-instances}

Utilizza le seguenti linee guida per ottimizzare la dimensione della finestra TCP su una VSI per prestazioni ottimali su un hypervisor {{site.data.keyword.cloud}}. Gli host virtuali {{site.data.keyword.cloud_notm}} hanno una dimensione di buffer TCP predefinita con un intervallo compreso tra 3 e 6 MB.

Per ottenere le migliori prestazioni dalla tua VSI, devi impostare la dimensione della finestra TCP al di sotto dell'intervallo compreso tra 3 e 6 MB. Il kernel imposta il limite di memoria effettivo al doppio del valore richiesto, raddoppiando di fatto le impostazioni massime. Mantieni la dimensione massima della finestra TCP a 2 MB o meno. Con l'ottimizzazione automatica abilitata, la dimensione del buffer del ricevitore (e la dimensione della finestra TCP) vengono aggiornate dinamicamente per ogni connessione. I valori predefiniti dello spazio di memoria per ogni connessione sono impostati con i seguenti array di elementi:

* net.ipv4.tcp_rmem - questa è la memoria riservata per i buffer di ricezione TCP
* net.ipv4.tcp_wmem - questa è la memoria riservata per i buffer di invio TCP

I valori in questi array sono le dimensioni buffer minima, iniziale e massima che utilizzi per impostare i limiti sull'ottimizzazione automatica e bilanciare l'utilizzo della memoria. Per visualizzare le tue impostazioni della finestra di memoria correnti, esegui il comando `sysctl -a | grep mem`

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

Per visualizzare tutte le impostazioni TCP, esegui il comando: `sysctl -a | grep tcp`

Verifica che le funzioni TCP avanzate standard siano abilitate:

    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_sack = 1
    net.ipv4.tcp_window_scaling = 1

Per verificare se l'ottimizzazione automatica è abilitata, controlla che la seguente impostazione abbia un valore di "1":

    net.ipv4.tcp_moderate_rcvbuf = 1

Per modificare le tue impostazioni della finestra TCP, aggiungi le impostazioni al tuo file /etc/sysctl.conf.

<!--**Note:** The preceding general recommendations are used to tune the TCP window sizes of a VSI in the public cloud. You have many ways to optimize your network for different workloads.-->

Per conservare le modifiche tra i riavvii, assicurati di aggiungere i comandi di ottimizzazione nel tuo file `rc.local`.
