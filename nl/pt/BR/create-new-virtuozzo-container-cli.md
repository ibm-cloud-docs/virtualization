---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: Virtuozzo
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Criando um novo contêiner do Virtuozzo (CLI)
{: #creating-a-new-virtuozzo-container-cli-}

## Como criar um novo contêiner do Virtuozzo (CLI)
{: #how-to-create-a-new-virtuozzo-container-cli-}

Conclua as etapas a seguir para criar um novo contêiner em um servidor Virtuozzo.

1. Verifique a lista de contêineres em seu host.

        [root@vztrain01 ~]# vzlist -a

          CTID      NPROC STATUS    IP_ADDR         HOSTNAME

             1         93 running   192.168.135.58  ServiceCT

           100          9 running   67.228.128.93   vztest01.softlayer.local

2. Crie um contêiner com um número de ID aberto. O número deve ser maior que 100 e ainda não estar em uso. Neste exemplo, use 122.

       [root@vztrain01 ~]# vzctl create 122 --pkgset redhat-as3-minimal-x86_64  --config basic

        Criando a área privada do contêiner (redhat-as3-minimal-x86_64/20080630)

        O contêiner está montado

        Ação de pós-criação concluída

        O contêiner está desmontado

        Área privada do contêiner criada

        Contêiner registrado com êxito

        [root@vztrain01 ~]# vzlist -a

          CTID      NPROC STATUS    IP_ADDR         HOSTNAME

             1         93 running   192.168.135.58  ServiceCT

           100          9 running   67.228.128.93   vztest01.softlayer.local

           122          - stopped   -               -

Depois de criar um contêiner, o VZ está no estado pausado. Se você não tiver certeza de quais pacotes estão instalados, será possível usar o comando `vzpkgls`. Esse comando mostra uma lista de pacotes do S.O. Virtuozzo e do aplicativo instalados que estão no servidor. Para o exemplo, um modelo RedHat é usado, mas o Virtuozzo suporta vários sistemas operacionais diferentes: RedHat, CentOS, Debian, Fedora Core e SUSE.

3. Revise a configuração padrão para o contêiner e faça as mudanças necessárias.

        [root@vztrain01 ~]# less /vz/private/122/ve.conf

4. Inicie o contêiner.

        [root@vztrain01 ~]# vzctl start 122

        Iniciando o contêiner...

        O contêiner está montado

        Configurar o limite de memória do slm

        Configurar o subgrupo de slm (padrão)

        Configurando devperms 20002 dev 0x7d00

        Incluindo redirecionamento de porta para o contêiner (1): 4643 8443

        Incluindo endereços IP:

        Configurar meminfo: 65536

        Início do contêiner em andamento...

Momentos depois, o contêiner é executado. É possível verificar o status novamente com o comando `vzctl` status 122.

5. Inclua parâmetros que são necessários para gerenciar o contêiner.

        [root@vztrain01 ~]# vzctl set 122 --ipadd 12.34.234.142 --hostname vz122.domain.com --userpasswd root:password123 --save

        Nome do host para o contêiner configurado: vz122.domain.com

        Incluindo endereços IP no conjunto: 12.34.234.142

        Incluindo endereços IP: 12.34.234.142

        Encerrando a interface de loopback: [ OK ]

        Configurando parâmetros de rede: [ OK ]

        Trazendo a interface de loopback: [ OK ]

        Trazendo venet0 da interface: [ OK ]

        Mudando a senha para o usuário raiz.

        passwd: todos os tokens de autenticação atualizados com sucesso.

        Parâmetros salvos para o contêiner 122

A configuração básica de um novo contêiner do Virtuozzo usando a linha de comandos está concluída.
