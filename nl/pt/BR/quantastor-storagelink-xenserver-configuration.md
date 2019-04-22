---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: QuantaStor
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Instalando o QuantaStor StorageLink Xenserver
{: #installing-quantastor-storagelink-xenserver}

## Instalando o adaptador QuantaStor para o Citrix StorageLink
{: #installing-the-quantastor-adapter-for-citrix-storagelink}

1. Certifique-se de que a instalação do XenServer esteja adequadamente licenciada para o StorageLink.

Somente clientes que assinam as licenças "Enterprise" ou "Platinum" por meio do Citrix podem usar o recurso StorageLink. Os clientes que têm a licença "Advanced" do Citrix XenServer não podem acessar o recurso StorageLink.
{:tip}

2. Pare o serviço StorageLink inserindo o comando a seguir em um prompt do administrador. Ou, é possível parar o serviço StorageLink por meio do Painel de serviço no XenServer.

        C:\> net stop storagelink

3. Faça download e execute o adaptador StorageLink disponível por meio do [OSNexus ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://www.osnexus.com/trynow/){: new_window}.
4. Depois que o serviço parar, será possível instalar o adaptador QuantaStor StorageLink executando o comando a seguir:

        osn_quantastor_csladapter.exe

5. Inclua esse bloco de XML no término da configuração do StorageLink em:
  * `C:\Program Files\Citrix\StorageLink\Server\scsi_device_id_config.xml`
  * `OSNEXUS`
  * `QUANTASTOR1`
  * No início deste arquivo, há um número de versão semelhante a _2.0.0.4._ Aumente o número da versão, por exemplo: 2.0.0.5. O número da versão informa ao StorageLink que ele precisa fazer upgrade de seus sistemas XenServer com essas novas regras de identificação de dispositivo.
6. Reinicie o serviço Citrix StorageLink:

        C:\> net start storagelink

## Verificando se o StorageLink foi carregado corretamente
{: #verifying-that-storagelink-loaded-properly}

1. Efetue login no Citrix StorageLink Manager, selecione a árvore "Administração" à esquerda e procure por OS NEXUS QuantaStor na lista [QuantaStor ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://svn.osnexus.com/mediawiki/images/thumb/c/c8/Storagelink_admin.png/640px-Storagelink_admin.png){: new_window}.
2. Inclua as credenciais do sistema de armazenamento para que o StorageLink possa configurar e alocar dinamicamente os volumes de armazenamento para o sistema de armazenamento do QuantaStor.

## Instalando as definições de configuração de caminhos múltiplos do XenServer
{: #installing-the-xenserver-multipath-configuration-settings}

O XenServer usa o driver de caminhos múltiplos do mapeador de dispositivos Linux (`dmmp`) para ativar o suporte para caminhos múltiplos de hardware. O driver `dmmp` usa um arquivo de configuração que é chamado `multipath.conf` que contém o modo de caminhos múltiplos e as regras de failover para cada fornecedor.

O QuantaStor tem algumas regras especiais que precisam ser incluídas no arquivo `multipath.conf`. Edite o arquivo `/etc/multipath-enabled.conf` que está em cada um dos nós `dom0` do XenServer. Nesse arquivo, inclua a sub-rotina a seguir após a última seção do dispositivo { }:

    device {
          vendor                  "OSNEXUS"
          product                 "QUANTASTOR"
          getuid_callout          "/sbin/scsi_id -g -u -s /block/%n"
          path_selector           "round-robin 0"
          path_grouping_policy    multibus
          failback                immediate
          rr_weight               uniform
          rr_min_io               100
          path_checker            tur
    }
