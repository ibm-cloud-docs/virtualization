---
copyright:
  years: 2014, 2018
lastupdated: "2018-05-18"

subcollection: virtualization

keywords: Virtuozzo
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Fazendo backup de um contêiner Virtuozzo na linha de comandos
{: #backing-up-a-virtuozzo-container-in-the-command-line}

Use as etapas a seguir para fazer backup de um contêiner em um servidor Virtuozzo.

1. Verifique a lista de contêineres em seu host.

        [root@virtuozzo01 ~]# vzlist

          CTID      NPROC STATUS    IP_ADDR         HOSTNAME

             1         86 running   192.168.151.2   ServiceCT

           101          5 running   75.126.27.32    VZTest01.softlayer.local

           201          9 running   12.34.234.142   vz201.domain.com

         [root@virtuozzo01 ~]#

2. Inicie o backup depois de verificar a conectividade com o nó de hardware de backup.

        [root@virtuozzo01 ~]# ping virtuozzo00.softlayer.local

        PING virtuozzo00.softlayer.local (75.126.77.100) 56(84) bytes of data.

        64 bytes from (75.126.77.100): icmp_seq=1 ttl=64 time=0.149 ms

        64 bytes from (75.126.77.100): icmp_seq=2 ttl=64 time=0.142 ms

        [root@virtuozzo00 ~]# vzabackup virtuozzo01.softlayer.local -e 201

        * A operação backup_env com os Envs vz201.domain.com é iniciada

        * Fazendo backup do ambiente vz201.domain.com para 10.4.9.85

        * Verificando parâmetros

        * Cota de dump

        * Criando o backup 5daa073c-2dd0-0a41-8274-a1ba1ee0d693/20081029151602

        * Ajustando o tipo de backup (completo)

        * Armazenamento de backup: recebendo o arquivo de backup

        * Preparando para a operação de backup

        * Fazendo backup de área privada

        100% |**************************************************************|

        * Enviando dados de backup privados

        * Armazenamento de backup: armazenando dados de backup privados

        * Armazenamento de backup: preenchendo informações de backup resultantes

        * A operação backup_env com os Envs vz201.domain.com foi concluída com êxito

        .

        A operação de backup para o nó 'virtuozzo01.softlayer.local' foi concluída com êxito.

Duas coisas para observar sobre o comando anterior:
1. Deve-se efetuar login e executar o comando backup no servidor de "destino" (o servidor no qual você deseja que o backup esteja). Depois de executar o comando, uma solicitação para o ID do contêiner por meio do nó de hardware original é feita.
2. A opção `-e` é usada para designar um contêiner único. Sem essa opção, o comando faz o backup de todos os contêineres no servidor host para a nova localização.

        [root@virtuozzo00 vz]# vzabackup virtuozzo01.softlayer.local

        A operação backup_env com os Envs vz201.domain.com,VZTest01.softlayer.local foi iniciada


É possível visualizar os backups que são feitos e armazenados no servidor usando o comando a seguir:

    [root@virtuozzo00 vz]# vzarestore --list --full

    Mostrar backups existentes...

    CTID: 100

    Título: migrateme

    Tipo: completo

    ID do backup: ab8743f4-d436-3a44-b35d-090f2bd2ea35/20081023173802

    Descrição: esse é o backup completo inicial de migrateme no virtuozzo00.

    Ambiente pai: virtuozzo00.softlayer.local

    Tamanho: 97,78 Mb

    Data de criação: 2008-10-23T123802-005

    CTID: 101

    Título: VZTest01.softlayer.local

    Tipo: completo

    ID do backup: f440f67b-76e7-684b-9f1b-f64f755803f7/20081029153257

    Descrição:

    Ambiente pai: virtuozzo01.softlayer.local

    Tamanho: 98,22 Mb

    Data de criação: 2008-10-29T103257-005

    CTID: 201

    Título: vz201.domain.com

    Tipo: completo

    ID do backup: 5daa073c-2dd0-0a41-8274-a1ba1ee0d693/20081029151602

    Descrição:

    Ambiente pai: virtuozzo01.softlayer.local

    Tamanho: 3,80 Mb

    Data de Criação: 2008-10-29T101602-005

    CTID: 201

    Título: vz201.domain.com

    Tipo: completo

    ID do backup: 5daa073c-2dd0-0a41-8274-a1ba1ee0d693/20081029153047

    Descrição:

    Ambiente pai: virtuozzo01.softlayer.local

    Tamanho: 3,80 Mb

    Data de criação: 2008-10-29T103047-005
