---
copyright:
  years: 2014, 2018
lastupdated: "2018-05-18"

subcollection: virtualization

keywords: TCP window size
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Optimisation de la taille de fenêtre TCP pour des instances virtuelles
{: #optimizing-tcp-window-size-for-virtual-instances}

Utilisez les instructions décrites ci-après pour ajuster la taille de fenêtre TCP afin d'optimiser les performances sur un hyperviseur {{site.data.keyword.cloud}}. Les hôtes virtuels {{site.data.keyword.cloud_notm}} ont une taille de mémoire tampon TCP par défaut comprise entre 3 et 6 Mo. 

Pour optimiser les performances de votre instance de serveur virtuel, vous devez définir une taille de fenêtre TCP inférieure à la plage comprise entre 3 et 6 Mo. Le noyau définit une limite de mémoire réelle qui est deux fois supérieure à la valeur demandée, ce qui double effectivement les paramètres maximum. Conservez une taille de fenêtre TCP maximum égale ou inférieure à 2 Mo. Avec le réglage automatique activé, la taille de mémoire tampon du récepteur (et la taille de fenêtre TCP) est mise à jour dynamiquement pour chaque connexion. Les valeurs par défaut d'espace mémoire par connexion sont définies avec les tableaux d'éléments suivants : 

* net.ipv4.tcp_rmem - Mémoire qui est réservée pour les mémoires tampon de réception TCP
* net.ipv4.tcp_wmem - Mémoire qui est réservée pour les mémoire tampon d'envoi TCP

Les valeurs indiquées dans ces tableaux correspondent aux tailles de mémoire tampon minimale, initiale et maximale que vous utilisez pour définir les limites du réglage automatique et équilibrer l'utilisation de la mémoire. Pour afficher vos paramètres de fenêtre de mémoire en cours, exécutez la commande suivante : `sysctl -a | grep mem`

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

Pour afficher tous les paramètres TCP, exécutez la commande suivante : `sysctl -a | grep tcp`

Vérifiez que les fonctions TCP avancées standard sont activées :

    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_sack = 1
    net.ipv4.tcp_window_scaling = 1

Pour vérifier si le réglage automatique est activé, assurez-vous que le paramètre suivant a pour valeur "1" :

    net.ipv4.tcp_moderate_rcvbuf = 1

Pour modifier vos paramètres de fenêtre TCP, ajoutez les paramètres au fichier /etc/sysctl.conf. 

<!--**Note:** The preceding general recommendations are used to tune the TCP window sizes of a VSI in the public cloud. You have many ways to optimize your network for different workloads.-->

Pour préserver les modifications entre les redémarrages, prenez soin d'ajouter les commandes de réglage à votre fichier `rc.local`. 
