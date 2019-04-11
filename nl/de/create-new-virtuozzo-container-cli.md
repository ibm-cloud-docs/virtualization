---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: Virtuozzo
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Neuen Virtuozzo-Container erstellen (CLI)
{: #creating-a-new-virtuozzo-container-cli-}

## Vorgehensweise zum Erstellen eines neuen Virtuozzo-Containers (CLI)
{: #how-to-create-a-new-virtuozzo-container-cli-}

Führen Sie die folgenden Schritte aus, um einen neuen Container auf einem Virtuozzo-Server zu erstellen.

1. Überprüfen Sie die Liste der Container auf Ihrem Host.

        [root@vztrain01 ~]# vzlist -a

          CTID      NPROC STATUS    IP_ADDR         HOSTNAME

             1         93 running   192.168.135.58  ServiceCT

           100          9 running   67.228.128.93   vztest01.softlayer.local

2. Erstellen Sie einen Container mit einer noch nicht belegten ID-Nummer (ID#). Die Nummer muss größer als 100 und noch nicht belegt sein. Verwenden Sie in diesem Beispiel die Nummer 122.

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

Nachdem Sie einen Container erstellt haben, befindet sich das VZ-System im Status 'Gestoppt'. Wenn Sie nicht sicher sind, welche Pakete installiert sind, können Sie den Befehl `vzpkgls` verwenden. Dieser Befehl zeigt eine Liste der installierten Virtuozzo-Betriebssystempakete und -Anwendungspakete an, die sich auf dem Server befinden. Im vorliegenden Beispiel wird eine RedHat-Vorlage verwendet, aber Virtuozzo unterstützt auch mehrere andere Betriebssysteme: RedHat, CentOS, Debian, Fedora Core und SUSE.

3. Überprüfen Sie die Standardkonfiguration für den Container und nehmen Sie die erforderlichen Änderungen vor.

        [root@vztrain01 ~]# less /vz/private/122/ve.conf

4. Starten Sie den Container.

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

Nur kurze Zeit später wird der Container ausgeführt. Sie können den Status mit dem Befehl `vzctl` status 122 erneut überprüfen.

5. Fügen Sie Parameter hinzu, die für die Verwaltung des Containers erforderlich sind.

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

Die grundlegende Einrichtung eines neuen Virtuozzo-Containers über die Befehlszeile ist abgeschlossen.
