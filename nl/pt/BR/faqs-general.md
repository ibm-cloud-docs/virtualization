---



copyright:
  years: 2014, 2018
lastupdated: "2018-01-04"

subcollection: virtualization


---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Perguntas frequentes: geral
{: #faqs-general}

## Por que o meu servidor não está listado na lista de servidores virtuais no Portal do cliente?
{: #why-isn-t-my-server-listed-in-the-virtual-servers-list-in-the-customer-portal-}

Se você tiver **XenServer**, **Hyper-V** ou **Virtuozzo** em seu servidor, mas ele não estiver listado na lista [{{site.data.keyword.virtualmachinesshort}} ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://manage.softlayer.com/Virtual/live){: new_window} na seção [Hardware ![Ícone de link externo](../../icons/launch-glyph.svg "External link icon")](https://manage.softlayer.com/Hardware/configuration){: new_window} do [Portal ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://manage.softlayer.com/){: new_window}, então, será necessário abrir um [chamado de suporte padrão ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://manage.softlayer.com/Support/addTicket){: new_window} com o departamento de suporte para que ele possa ser incluído em sua lista.

Essa solicitação é preenchida durante os horários comerciais do CST dos Estados Unidos.
{:tip}

## Se os clientes forem um provedor de serviços, eles poderão usar o seu próprio licenciamento de VSPP para os hosts que eles alugam no IBM Cloud?
{: #if-clients-are-a-service-provider-can-they-use-their-own-vspp-licensing-for-hosts-that-they-rent-in-ibm-cloud-}

Não. É possível usar o {{site.data.keyword.cloud}} VSPP ou BYOL para licenciamento baseado em soquete. Não é possível usar seu próprio contrato VSPP.

## Posso usar o bloco de IP secundário que veio com o servidor para máquinas virtuais?
{: #can-i-use-the-secondary-ip-block-that-came-with-the-server-for-virtual-machines-}

Não. O IP secundário que é fornecido com o servidor é roteado para trabalhar no servidor físico e não em uma máquina virtual. É necessário ter blocos de IP móveis para conectar sua máquina virtual à rede.
