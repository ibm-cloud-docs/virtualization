---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-05"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Utilizzo di un semplice server dei nomi di memorizzazione in cache con Virtuozzo
{: #using-a-simple-caching-name-server-with-virtuozzo}

Virtuozzo prova a inviare pacchetti dall'interfaccia interna del nodo hardware utilizzando l'indirizzo IP pubblico del contenitore. Non devi fare altro che eseguire un server dei nomi di memorizzazione in cache sul nodo hardware, che è disponibile solo sull'interfaccia privata.

```
    [root@virtuozzo ~]# yum install bind

    Loading "security" plugin

    Loading "rhnplugin" plugin

    rhel-x86_64-server-vt-5   100% |=========================| 1.2 kB    00:00

    rhel-x86_64-server-5      100% |=========================| 1.2 kB    00:00

    Excluding Packages in global exclude list

    Finished

    Setting up Install Process

    Parsing package install arguments

    Resolving Dependencies

    --> Running transaction check

    ---> Package bind.x86_64 30:9.3.4-6.0.2.P1.el5_2 set to be updated

    --> Finished Dependency Resolution

    Dependencies Resolved

    =============================================================================

     Package                 Arch       Version          Repository        Size

    =============================================================================

    Installing:

     bind                    x86_64     30:9.3.4-6.0.2.P1.el5_2  rhel-x86_64-server-5  965 k

    Transaction Summary

    =============================================================================

    Install      1 Package(s)

    Update       0 Package(s)

    Remove       0 Package(s)

    Total download size: 965 k

    Is this ok [y/N]: y

    Downloading Packages:

    (1/1): bind-9.3.4-6.0.2.P 100% |=========================| 965 kB    00:00

    Running rpm_check_debug

    Running Transaction Test

    Finished Transaction Test

    Transaction Test Succeeded

    Running Transaction

      Installing: bind                         ######################### [1/1]

    Installed: bind.x86_64 30:9.3.4-6.0.2.P1.el5_2

    Complete!

    [root@virtuozzo ~]# cat > /etc/named.conf <_EOF_

    options

    {

            directory  "/var/named";

            forwarders { 10.0.80.11; 10.0.80.12; };

            listen-on  { 10/8; };

    };

    _EOF_

    [root@virtuozzo ~]# chkconfig named on

    [root@virtuozzo ~]# chkconfig --list named

    named           0:off   1:off   2:on    3:on    4:on    5:on    6:off

    [root@virtuozzo ~]# service named start

    Starting named:                                            [  OK  ]

    [root@virtuozzo ~]#

```


In alternativa, puoi aggiungere un'interfaccia di rete con bridge utilizzando Virtuozzo, assegnare indirizzi IP interni univoci dal tuo blocco di rete interno e aggiungere un instradamento statico. L'instradamento interno fornisce l'accesso alla tua infrastruttura interna; tieni pertanto presente questa soluzione alternativa se hai una rete mista e un filtro non rigido sui server nella tua rete interna.
