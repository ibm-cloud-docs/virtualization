---



copyright:
  years: 2014, 2018
lastupdated: "2018-01-09"

subcollection: virtualization


---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Introdução ao Virtuozzo
{: #getting-started-with-virtuozzo}

## Acessando o Parallels Infrastructure Manager (PIM)
{: #accessing-the-parallels-infrastructure-manager-pim-}

1. Obtenha informações por meio do [{{site.data.keyword.slportal_full}} ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://control.softlayer.com/){: new_window} sobre o seu servidor bare metal.
    1. No menu **Dispositivos**, selecione **Lista de dispositivos**.
    2. Clique em seu servidor.
    3. Localize o endereço IP público e a senha.
5. Acesse o endereço IP público do servidor em seu navegador preferencial usando HTTPS. Use o Firefox, o Opera ou o Internet Explorer para gerenciar o Virtuozzo por meio do PIM.

O JavaScript é necessário. Sem o JavaScript, é possível ser redirecionado para uma página inexistente e o PIM não funcionará.
{:tip}

6. Insira seu nome de usuário e senha para efetuar login no PIM.

O nome de usuário é "raiz" e a senha é a senha raiz para o servidor, conforme listado no {{site.data.keyword.slportal}}.
{:tip}

## Instalando e armazenando modelos em cache
{: #installing-and-caching-templates}

1. Na tela PIM, selecione **Gerenciamento** > **Atualizações**.
2. Acesse o servidor que você deseja atualizar.
3. Clique em **Atualizações de modelo**.
4. Selecione qualquer modelo adicional que você deseja instalar ou atualizar.
5. Para selecionar o servidor que você deseja atualizar, clique em **Modelos** > **Modelos de S.O.**.
6. Selecione os modelos de S.O. que você deseja usar no servidor e no cache. Geralmente, não é necessário armazenar em cache todos os modelos para iniciar. Somente modelos de cache que você deseja usar. Esses modelos consomem uma quantia significativa de espaço na partição `/vz`.
7. Se você tiver apenas um nó, clique em **Próximo cache**.
8. Aguarde até que os RPMs de modelo completos sejam transferidos por download e instalados. Clique em **Detalhes** para visualizar o status de armazenamento em cache.

## Configurando o intervalo de rede para contêineres
{: #setting-up-network-range-for-containers}

Enquanto o servidor faz download e armazena os modelos em cache, é possível configurar o intervalo de rede para os contêineres.

1. Clique em **Selecionar** > **Rede**.
2. Selecione **Novo intervalo de IP**.
3. Inclua endereços IP adicionais para seus contêineres. Se você não tiver comprado endereços IP adicionais para seus servidores virtuais, acesse o [{{site.data.keyword.slportal}} ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://control.softlayer.com/){: new_window}. Selecione **Rede** > **Gerenciamento de IP** > **VLANs** > **IP do pedido**.
4. Acesse o nó de hardware e clique em **Contêineres** > **Novo Contêiner**.
5. Conclua as informações conforme elas se ajustarem às suas necessidades. Para este exemplo, use slm.512MB como o padrão.
6. Insira o nome do host como um nome de domínio completo, inclua o servidor DNS e, se necessário, inclua um domínio de procura.
7. Revise os parâmetros de recurso.

Tenha cuidado para não inserir parâmetros em excesso, pois isso impede a capacidade do contêiner para processar dados, o seu espaço em disco ou a memória, o que pode esgotar os recursos do servidor. Um servidor esgotado de recursos pode parar processos inesperadamente ou torná-los lentos. Se você não entender essas configurações, entre em contato com o administrador do servidor ou com uma [empresa de gerenciamento de terceiro ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://developer.ibm.com/bluemix/){: new_window}.
{:tip}

8. Clique em **Validar** antes de clicar em **Avançar**. A validação assegura que suas configurações não entrem em conflito entre si.
9. Selecione os aplicativos que você deseja instalar e clique em **Avançar**.
10. Revise sua configuração e clique em **Criar**. Em seguida, um contêiner é criado.
11. Agora é possível voltar para **Contêineres** para visualizar os novos contêineres e obter o endereço IP designado automaticamente (por meio dos IPs que você incluiu anteriormente).
12. Agora é possível efetuar login no servidor e usá-lo.

        myname/myserver[0]~$ ssh 1.234.123.4
        Aviso: '1.234.123.4' (DSA) permanentemente incluído na lista de hosts conhecidos.
        root@1.234.123.4's password:
        [root@test01 ~]# ip a s
        1: lo: mtu 16436 qdisc noqueue
            link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
            inet 127.0.0.1/8 scope host lo
            inet6 ::1/128 scope host
              valid_lft forever preferred_lft forever
        3: venet0: mtu 1500 qdisc noqueue
            link/void
            inet 127.0.0.1/32 scope host venet0
            inet 1.234.123.4/32 brd 1.234.123.4 scope global venet0:0
        [root@test01 ~]# ps auxww
        USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
        root         1  0.0  0.1  10328   744 ?        Ss   13:23   0:00 init [3]
        root     20419  0.0  0.1  12580   632 ?        Sroot     21726  0.0  0.2  60532  1220 ?        Ss   13:23   0:00 /usr/sbin/sshd
        root     21737  0.0  0.1  22512   892 ?        Ss   13:23   0:00 xinetd -stayalive -pidfile /var/run/xinetd.pid
        root     21756  0.0  0.3  62732  2052 ?        Ss   13:23   0:00 sendmail: accepting connections
        smmsp    21764  0.0  0.3  57608  1752 ?        Ss   13:23   0:00 sendmail: Queue runner@01:00:00 for /var/spool/clientmqueue
        root     21776  0.0  2.0 283796 10728 ?        Ss   13:23   0:00 /usr/sbin/httpd
        root     21786  0.0  0.2  20832  1144 ?        Ss   13:23   0:00 crond
        root     21796  0.0  0.1  46648   800 ?        Ss   13:23   0:00 /usr/sbin/saslauthd -m /var/run/saslauthd -a pam -n 2
        apache   21797  0.0  1.1 283932  5788 ?        S    13:23   0:00 /usr/sbin/httpd
        root     21798  0.0  0.1  46648   544 ?        S    13:23   0:00 /usr/sbin/saslauthd -m /var/run/saslauthd -a pam -n 2
        root     23573  0.0  0.6  85876  3240 ?        Rs   13:45   0:00 sshd: root@pts/0
        root     23594  0.0  0.3  12044  1604 pts/0    Ss   13:45   0:00 -bash
        root     23626  0.0  0.1  10700   952 pts/0    R+   13:45   0:00 ps auxww

        [root@test01 ~]# df -h
        Filesystem            Size  Used Avail Use% Mounted on
        vzfs                  1.0 G   64 M  961 M   7% /
        none                 1004 M  4.0 K 1004 M   1% /dev
        [root@test01 ~]#
