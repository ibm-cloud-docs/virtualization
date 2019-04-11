---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: Virtuozzo
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Creación de un nuevo contenedor de Virtuozzo (CLI)
{: #creating-a-new-virtuozzo-container-cli-}

## Cómo crear un nuevo contenedor de Virtuozzo (CLI)
{: #how-to-create-a-new-virtuozzo-container-cli-}

Realice los pasos siguientes para crear un nuevo contenedor en un servidor de Virtuozzo.

1. Compruebe la lista de contenedores en el host.

        [root@vztrain01 ~]# vzlist -a

          CTID      NPROC STATUS    IP_ADDR         HOSTNAME

             1         93 running   192.168.135.58  ServiceCT

           100          9 running   67.228.128.93   vztest01.softlayer.local

2. Cree un contenedor con un ID abierto. El número debe ser mayor que 100 y no estar ya en uso. En este ejemplo, utilice 122.

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

Tras crear un contenedor, el VZ está en estado detenido. Si no está seguro de qué paquetes se han instalado, puede utilizar el mandato
`vzpkgls`. Este mandato muestra una lista de paquetes de aplicación y de SO de Virtuozzo instalados que se encuentran en el servidor. En el ejemplo, se utiliza una plantilla de RedHat, pero Virtuozzo admite diversos sistemas operativos: RedHat, CentOS, Debian, Fedora Core y SUSE.

3. Revise la configuración predeterminada del contenedor y realice los cambios necesarios.

        [root@vztrain01 ~]# less /vz/private/122/ve.conf

4. Inicie el contenedor.

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

Unos momentos más tarde, se ejecutará el contenedor. Puede comprobar el estado de nuevo con el mandato
`vzctl` status 122.

5. Añada los parámetros necesarios para gestionar el contenedor.

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

La configuración básica de un nuevo contenedor de Virtuozzo utilizando la línea de mandatos ha finalizado.
