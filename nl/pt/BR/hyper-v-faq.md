---
copyright:
  years: 2014, 2018
lastupdated: "2018-08-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Perguntas frequentes: Hyper-V
{: #faqs-hyper-v}

## O que é Hyper-V?
{: #what-is-hyper-v-}

O Hyper-V é um sistema de virtualização para o Windows 2008 Server Datacenter Edition que permite que um único servidor físico hospede múltiplas máquinas virtuais, todas executando o seu próprio sistema operacional.

## Quais são os requisitos para executar o Hyper-V?
{: #what-are-the-requirements-to-run-hyper-v-}

O Hyper-V deve ser instalado no Windows 2008 Server 64-bit Datacenter Edition. Todo o hardware do {{site.data.keyword.cloud}} atende aos requisitos do sistema do Hyper-V. A Microsoft recomenda pelo menos 2 GB de RAM para o servidor.

## O Hyper-V está disponível em qualquer outra versão de 2008?
{: #is-hyper-v-available-on-any-other-versions-of-2008-}

O {{site.data.keyword.cloud_notm}} oferece o Hyper-V somente no Datacenter Edition.

## O Hyper-V pode ser executado no Windows 2003?
{: #can-hyper-v-run-on-windows-2003}

O Hyper-V foi desenvolvido para Windows 2008 e não pode ser executado no Windows 2003.

## Quais sistemas operacionais podem ser instalados em uma máquina virtual?
{: #what-operating-systems-can-be-installed-on-a-virtual-machine}

O {{site.data.keyword.cloud_notm}} suporta os sistemas operacionais a seguir no Hyper-V:

* Todas as versões do Windows 2003 e 2008 Server
* CentOS, Fedora e distribuições do Ubuntu Linux

## Quantas máquinas virtuais podem ser executadas por um servidor?
{: #how-many-virtual-machines-can-a-server-run-}

O número de máquinas virtuais que um servidor pode executar varia, dependendo dos processadores do servidor, da RAM e do espaço de disco rígido.

## A RAM é customizável em cada máquina virtual?
{: #is-the-ram-customizable-on-each-virtual-machine-}

Sim. É possível usar o Hyper-V para customizar os recursos do sistema para cada máquina virtual, incluindo memória.

## Quanto de RAM uma máquina virtual precisa?
{: #how-much-ram-does-a-virtual-machine-need-}

As necessidades de RAM variam com base em requisitos para a máquina virtual. Verifique os requisitos do sistema para seu sistema operacional guest. A quantidade de memória que é fornecida a uma máquina virtual pode ser mudada a qualquer momento.

## Uma máquina virtual pode acessar mais de um processador?
{: #can-a-virtual-machine-access-to-more-than-one-processor-}

É possível conceder a uma máquina virtual acesso a múltiplos processadores com qualquer máquina virtual do Windows. No entanto, as máquinas virtuais Linux são limitadas a um único processador.

## Os tamanhos de disco rígido podem mudar depois que uma máquina virtual é criada?
{: #can-hard-disk-sizes-change-after-a-virtual-machine-is-created-}

Sim.

## Cada sistema operacional virtual precisa ter uma licença?
{: #does-each-virtual-operating-system-need-to-have-a-license-}

As máquinas virtuais do Windows 2003 e 2008 são licenciadas por meio do {{site.data.keyword.cloud_notm}}. As máquinas virtuais Linux são licenciadas gratuitamente e não requerem ação.

## As máquinas virtuais terão acesso à rede privada?
{: #will-the-virtual-machines-have-access-to-the-private-network-}

Sim. As máquinas virtuais podem se conectar a uma rede pública e privada.

## Quais são as vantagens de fornecer acesso de rede privada às máquinas virtuais?
{: #what-advantages-are-there-to-providing-private-network-access-to-virtual-machines-}

Fornecer acesso de rede privada para máquinas virtuais permite que máquinas virtuais se comuniquem umas com as outras. O acesso à rede privada também permite que máquinas virtuais se comuniquem com outros sistemas internos, como NAS e iSCSI, e quaisquer outros servidores que você tenha com o {{site.data.keyword.cloud_notm}}.

## As máquinas virtuais podem usar o bloco de IP secundário que vem com o servidor?
{: #can-virtual-machines-use-the-secondary-ip-block-that-came-with-the-server-}

Não. O IP secundário fornecido com o servidor é roteado especificamente para trabalhar no servidor físico e não em uma máquina virtual. Blocos de IP móveis são necessários para conectar a sua máquina virtual à rede.

## O que são endereços IP móveis?
{: #what-are-portable-ip-addresses-}

Para obter informações sobre endereços IP móveis, consulte [Introdução às sub-redes e IPs](/docs/infrastructure/subnets?topic=subnets-getting-started-subnets-ips#getting-started-subnets-ips).

## Como pedir o Hyper-V?
{: #how-do-i-order-hyper-v-}

É possível instalar o Hyper-V em um servidor existente emitindo um recarregamento de S.O. e mudando o sistema operacional para "Windows 2008 Server Datacenter Edition with Hyper-V". Para fornecer um novo servidor com o Hyper-V, peça um novo servidor e selecione "Windows 2008 Server Datacenter Edition with Hyper-V" como o sistema operacional.

## O Hyper-V é pré-instalado com o Windows 2008 Server Datacenter Edition?
{: #is-hyper-v-preinstalled-with-windows-2008-server-datacenter-edition-}

O Hyper-V não é pré-instalado em servidores Windows 2008. Se desejar instalar o Hyper-V em seu servidor Windows 2008, será necessário emitir um recarregamento do S.O. e selecionar "Windows 2008 Server Datacenter Edition with Hyper-V" como o sistema operacional.

## O recarregamento de S.O. para o Hyper-V está pronto. Qual o próximo?
{: #the-os-reload-for-hyper-v-is-done-what-s-next-}

O Hyper-V precisa ser configurado. Para obter mais informações sobre a configuração do Hyper-V, consulte [Configurando o Hyper-V](/docs/infrastructure/virtualization?topic=Virtualization-setting-up-hyper-v).

## Uma máquina virtual não é iniciada. "Não é possível inicializar o sistema remoto da máquina: Erro: 'Erro não especificado'". Como resolver esse problema?
{: #a-virtual-machine-does-not-start-cannot-initialize-machine-remoting-system-error-unspecified-error-how-do-i-resolve-this-issue-}

Para resolver esse problema, pare o serviço Hyper-V e reinicie-o. Esse problema geralmente é causado pelo Hyper-V tentando iniciar enquanto ocorre a ativação do servidor. A reinicialização do serviço traz a máquina virtual de volta após a conclusão da ativação.
