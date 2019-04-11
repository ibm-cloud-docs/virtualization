---



copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Perguntas frequentes: Virtuozzo
{: #faqs-virtuozzo}

## Eu preciso de endereços IP estáticos ou móveis para Virtuozzo?
{: #do-i-need-static-or-portable-ip-addresses-for-virtuozzo-}

* Endereço IP estático: use um endereço IP estático se você tiver apenas um nó de hardware (por VLAN ou no total)
* Endereço IP móvel:
    * Roteado para VLAN: use esse método se você tiver múltiplos nós de host dentro da mesma VLAN. Você também usa esse método para migrar um contêiner para um nó de hardware diferente dentro da mesma VLAN sem mudar o IP do contêiner.
    * Secundário na VLAN: esse método funciona, mas você não precisa de um gateway secundário. Você perde três dos endereços IP devido ao ID do host, o gateway e a transmissão.

Os custos por IP podem mudar dependendo do tipo roteado. Os preços são mostrados no portal depois que você seleciona o servidor para o qual está pedindo os endereços IP. O roteamento pode ser mudado a qualquer momento, abrindo um chamado de suporte.
{:tip}

## Posso migrar um Virtuozzo 3 Container para um nó de hardware do Virtuozzo 4 Container sem tempo de inatividade?
{: #can-i-migrate-a-virtuozzo-3-container-to-a-virtuozzo-container-4-hardware-node-with-no-downtime-}

**Linux:
**

Sim. Por padrão, o Virtuozzo Containers for Linux 4 é executado no modo de compatibilidade. Se o Virtuozzo Containers não estiver em execução no modo de compatibilidade, então será possível iniciá-lo usando o comando a seguir no nó de hardware por meio de SSH.

`/usr/sbin/vzagent_compat_ctl start`

**Windows (não suportado):**

Com o Virtuozzo Containers 4 Windows, você tem o tempo de inatividade durante a transição (entre 20 segundos até 5 minutos) por não ter uma opção on-line para passar.

Se você usar o Virtuozzo 3.0 for Windows, será necessário fazer upgrade do nó de hardware para pelo menos o Virtuozzo 3.5.1 for Windows.

O Virtuozzo Containers 4 for Windows, por padrão, executa no modo de compatibilidade.

A restauração de backups do 3.5.1 em um nó de hardware do Virtuozzo Containers 4 também pode ser executada copiando o backup completo para o nó de destino, executando `vzbackupsync.exe` e restaurando o backup normalmente.

Certifique-se de que os mesmos modelos estejam instalados no novo nó de hardware.
{:tip}

## Os contêineres estão isolados uns dos outros e do host?
{: #are-containers-isolated-from-each-other-and-the-host-}

Seus contêineres são impedidos de carregar LKMs, executar um `chroot("../../../../")` fora de seu próprio ambiente e não compartilham os recursos do IPC (configurações de semáforo e segmentos de memória compartilhada).

Você não tem preocupações de segurança com o modo como o Virtuozzo gerencia recursos e isola os processos. Com uma configuração de rede em bridge, todos os contêineres na mesma VLAN (no mesmo nó de hardware) podem ver o tráfego uns dos outros. O uso do modo roteado é altamente sugerido.

## Por que não posso armazenar em cache os modelos de S.O. RedHat e SUSE no Virtuozzo?
{: #why-can-t-i-cache-redhat-and-suse-os-templates-in-virtuozzo-}

Será possível ter problemas ao armazenar em cache modelos de S.O. RedHat e SUSE se você usar o Virtuozzo. Esses modelos de S.O. requerem licenciamento extra que o {{site.data.keyword.cloud_notm}} não fornece. Se você receber qualquer um dos erros a seguir, será necessário ter suas próprias contas com os respectivos fornecedores e configurar as variáveis correspondentes em /etc/vztt/url.map (/etc/vztt/vztt.conf no Virtuozzo 3.)

||||
|---|---|---|
|REGISTRO DE DATA E HORA|Clique aqui para abrir/fechar os detalhes da operação. Atualize o cache de modelo do S.O.|** Com falha **|
|REGISTRO DE DATA E HORA|A atualização da operação com os Envs "virtuozzo00.softlayer.local" e os pacotes .redhat-as4-x86_64 é iniciada||
|REGISTRO DE DATA E HORA|A atualização da operação com os Envs "virtuozzo00.softlayer.local" foi concluída com erros. Não é possível atualizar os pacotes: exec com falha: Erro: A URL $RH_SERVER/download/mirrors/redhat-as4 contém variável indefinida. É possível definir essa variável em /etc/vztt/url.map. .|** Com falha **|
|REGISTRO DE DATA E HORA|Clique aqui para abrir/fechar os detalhes da operação. Atualize o cache de modelo do S.O.|** Com falha **|
|REGISTRO DE DATA E HORA|A atualização da operação com os Envs "virtuozzo00.softlayer.local" e os pacotes .redhat-el5-x86_64 é iniciada||
|REGISTRO DE DATA E HORA|A atualização da operação com os Envs "virtuozzo00.softlayer.local" foi concluída com erros. Não é possível atualizar os pacotes: exec com falha: Erro: A URL $RH_SERVER/download/mirrors/redhat-el5 contém variável indefinida. É possível definir essa variável em /etc/vztt/url.map. Consulte o Guia de instalação do Virtuozzo para obter mais detalhes. .|** Com falha **|
|REGISTRO DE DATA E HORA|Clique aqui para abrir/fechar os detalhes da operação. Atualize o cache de modelo do S.O.|** Com falha **|
|REGISTRO DE DATA E HORA|A atualização da operação com os Envs "virtuozzo00.softlayer.local" e os pacotes .sles-10-x86_64 é iniciada||
|REGISTRO DE DATA E HORA|A atualização da operação com os Envs "virtuozzo00.softlayer.local" foi concluída com erros. Não é possível atualizar os pacotes: exec com falha: Erro: A URL $SLES_SERVER/download/download/mirrors/suse-es10 contém variável indefinida. É possível definir essa variável em /etc/vztt/url.map. Consulte o Guia de instalação do Virtuozzo para obter mais detalhes. .|** Com falha **|
|REGISTRO DE DATA E HORA|Clique aqui para abrir/fechar os detalhes da operação. Atualize o cache de modelo do S.O.|** Com falha **|
|REGISTRO DE DATA E HORA|A atualização da operação com os Envs "virtuozzo01.softlayer.local" e os pacotes .redhat-as4-x86_64 é iniciada||
|REGISTRO DE DATA E HORA|A atualização da operação com o "virtuozzo01.softlayer.local" do Env (s) foi concluída com erros: não é possível atualizar pacotes: exec com falha: Erro: A URL $RH_SERVER/download/mirrors/redhat-as4 contém variável indefinida. É possível definir essa variável em /etc/vztt/url.map. Consulte o Guia de instalação do Virtuozzo para obter mais detalhes. .|** Com falha **|
|REGISTRO DE DATA E HORA|Clique aqui para abrir/fechar os detalhes da operação. Atualize o cache de modelo do S.O.|** Com falha **|
|REGISTRO DE DATA E HORA|A atualização da operação com os Envs "virtuozzo01.softlayer.local" e os pacotes .redhat-el5-x86_64 é iniciada||
|REGISTRO DE DATA E HORA|A atualização da operação com o "virtuozzo01.softlayer.local" do Env (s) foi concluída com erros: não é possível atualizar pacotes: exec com falha: Erro: A URL $RH_SERVER/download/mirrors/redhat-el5 contém variável indefinida. É possível definir essa variável em /etc/vztt/url.map. .|** Com falha **|
|REGISTRO DE DATA E HORA|Clique aqui para abrir/fechar os detalhes da operação. Atualize o cache de modelo do S.O. com Falha<br/><br/>29 de outubro de 2008 13:16:56 A atualização da operação com os Envs "virtuozzo01.softlayer.local" e os pacotes .sles-10-x86_64 é iniciada||
|REGISTRO DE DATA E HORA|A atualização da operação com o "virtuozzo01.softlayer.layer.local" do Env é concluída com erros: não é possível atualizar pacotes: exec com falha: Erro: A URL $SLES_SERVER/download/download/download/mirrors/suse-es10 contém variável indefinida.|** Com falha **|
|REGISTRO DE DATA E HORA|Concluindo o processo|** Com falha **|

## Por que posso executar ping no VE do serviço, mas não é possível efetuar login por meio de SSH (PIM/PMC)?
{: #why-can-i-ping-my-serviceve-but-cannot-log-in-to-via-ssh-pim-pmc-}

Certifique-se de que o nó do host não tenha nenhum endereço IP designado diferente dos endereços IP listados no Portal (1 IP público e 1 IP privado [localizado em 10.0.0.0/8]).

Se você tiver qualquer arquivo de intervalo que esteja configurado no nó do host, será necessário remover esse arquivo e reiniciar a rede.

    [root@virtuozzo ~]# mv –vi /etc/sysconfig/network-scripts/eth[0-9]-range[0-9]* ~/.sl-orig-configs/

    [root@virtuozzo ~]# /sbin/service network restart


`vzcp` não é iniciado dentro do VE do serviço. Inicie `vzcp` dentro do VE do serviço por meio do nó de hardware.

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp status

    vzcp is stopped

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp start

    Starting vzcpd: [  OK  ]

    Starting vzcp: [  OK  ]

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp status

    vzcp (pid 3775 3774 3771) is running...

    [root@virtuozzo ~]#
