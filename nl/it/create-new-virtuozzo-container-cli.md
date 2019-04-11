---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: Virtuozzo
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Creazione di un nuovo contenitore Virtuozzo (CLI)
{: #creating-a-new-virtuozzo-container-cli-}

## Come creare un nuovo contenitore Virtuozzo (CLI)
{: #how-to-create-a-new-virtuozzo-container-cli-}

Completa la seguente procedura per creare un nuovo contenitore su un server Virtuozzo.

1. Controlla l'elenco dei contenitori sul tuo host.

        [root@vztrain01 ~]# vzlist -a

          CTID      NPROC STATUS    IP_ADDR         HOSTNAME

             1         93 running   192.168.135.58  ServiceCT

           100          9 running   67.228.128.93   vztest01.softlayer.local

2. Crea un contenitore con un numero di ID aperto. Il numero deve essere maggiore di 100 e non deve essere già in uso. In questo esempio, utilizza 122.

       [root@vztrain01 ~]# vzctl create 122 --pkgset redhat-as3-minimal-x86_64  --config basic

        Creating Container private area (redhat-as3-minimal-x86_64/20080630)

        Container is mounted

        Postcreate action done

        Container is unmounted

        Container private area created

        Container registered succesfully

        [root@vztrain01 ~]# vzlist -a

          CTID      NPROC STATUS    IP_ADDR         HOSTNAME

             1         93 running   192.168.135.58  ServiceCT

           100          9 running   67.228.128.93   vztest01.softlayer.local

           122          - stopped   -               -

Dopo che hai creato un contenitore, VZ è nello stato di arrestato. Se non sei sicuro di quali pacchetti siano installati, puoi utilizzare il comando `vzpkgls`. Questo comando ti mostra un elenco dei pacchetti delle applicazioni e del sistema operativo Virtuozzo installati che si trovano sul server. Ad esempio, viene utilizzato un template RedHat ma Virtuozzo supporta vari sistemi operativi differenti: RedHat, CentOS, Debian, Fedora Core e SUSE.

3. Controlla la configurazione predefinita per il contenitore e apporta le modifiche necessarie.

        [root@vztrain01 ~]# less /vz/private/122/ve.conf

4. Avvia il contenitore.

        [root@vztrain01 ~]# vzctl start 122

        Starting Container ...

        Container is mounted

        Setup slm memory limit

        Setup slm subgroup (default)

        Setting devperms 20002 dev 0x7d00

        Adding port redirection to Container(1): 4643 8443

        Adding IP address(es):

        Configure meminfo: 65536

        Container start in progress...

Subito dopo viene eseguito il contenitore. Puoi controllare nuovamente lo stato con il comando `vzctl` status 122.

5. Aggiungi i parametri necessari per gestire il contenitore.

        [root@vztrain01 ~]# vzctl set 122 --ipadd 12.34.234.142 --hostname vz122.domain.com --userpasswd root:password123 --save

        Hostname for Container set: vz122.domain.com

        Adding IP address(es) to pool: 12.34.234.142

        Adding IP address(es): 12.34.234.142

        Shutting down loopback interface:  [  OK  ]

        Setting network parameters:  [  OK  ]

        Bringing up loopback interface:  [  OK  ]

        Bringing up interface venet0:  [  OK  ]

        Changing password for user root.

        passwd: all authentication tokens updated successfully.

        Saved parameters for Container 122

La configurazione di base di un nuovo contenitore Virtuozzo utilizzando la riga di comando è completa.
