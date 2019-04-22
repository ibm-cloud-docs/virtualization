---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: Xenserver
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Conectando-se ao XenServer por meio de uma interface pública
{: #connecting-to-xenserver-over-a-public-interface}

Por padrão, a instalação do {{site.data.keyword.cloud}} do XenServer é configurada para usar a interface de rede privada para todas as conexões de gerenciamento. Se você tiver problemas de conexão na rede privada, abra um chamado de suporte.

Mudar essa configuração é um risco de segurança e não é aconselhável.
{:tip}

Se você deseja reconfigurar o XenServer para usar a interface pública, execute os comandos a seguir como raiz por meio do host (o host não pode ser agrupado)..

1. `xe pif-list`

2. Tome nota do UUID da interface que você deseja ativar (mais provavelmente eth1 ou bond1).

3. Execute o comando a seguir:

        `xe host-management-reconfigure pif-uuid=`

Essas mudanças entram em vigor imediatamente e não requerem uma reinicialização.
