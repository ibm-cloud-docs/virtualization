---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: Virtuozzo
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Création d'un nouveau conteneur Virtuozzo (CLI)
{: #creating-a-new-virtuozzo-container-cli-}

## Création d'un nouveau conteneur Virtuozzo (CLI)
{: #how-to-create-a-new-virtuozzo-container-cli-}

Pour créer un nouveau conteneur sur un serveur Virtuozzo, procédez comme suit :

1. Vérifiez la liste de conteneurs sur votre hôte.

        [root@vztrain01 ~]# vzlist -a

          CTID      NPROC STATUS    IP_ADDR         HOSTNAME

             1         93 running   192.168.135.58  ServiceCT

           100          9 running   67.228.128.93   vztest01.softlayer.local

2. Créez un conteneur avec un ID# ouvert. Le nombre doit être supérieur à 100 et ne pas être déjà utilisé. Dans cet exemple, utilisez 122.

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

Après que vous avez créé un conteneur, le VZ est à l'état arrêté. Si vous ne savez pas quels sont les packages installés, vous pouvez utiliser la commande `vzpkgls`. Cette commande affiche une liste de packages d'application et de système d'exploitation Virtuozzo installés sur le serveur. Pour cet exemple, un modèle RedHat est utilisé, mais Virtuozzo prend en charge différents systèmes d'exploitation : RedHat, CentOS, Debian, Fedora Core et SUSE.

3. Passez en revue la configuration par défaut pour le conteneur et effectuez les modifications nécessaires :

        [root@vztrain01 ~]# less /vz/private/122/ve.conf

4. Démarrez le conteneur.

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

Peu après, le conteneur s'exécute. Vous pouvez vérifier à nouveau l'état à l'aide de la commande `vzctl` status 122. 

5. Ajoutez les paramètres qui sont nécessaires pour la gestion des conteneurs :

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

La configuration de base d'un nouveau conteneur Virtuozzo à l'aide de la ligne de commande est terminée.
