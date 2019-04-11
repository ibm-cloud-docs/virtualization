---
copyright:
  years: 2014, 2018
lastupdated: "2018-08-16"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# O que é Virtuozzo?
{: #what-is-virtuozzo-}

O Virtuozzo é uma solução de virtualização baseada em contêiner que permite o compartilhamento de hardware usando uma camada de abstração.  O Virtuozzo cria contêineres, também conhecidos como VEs ou VPSs, que simulam um servidor. O contêiner atua e responde principalmente como se fosse um servidor independente. O contêiner é separado de outros contêineres que estão no mesmo servidor físico no qual não podem acessar outros arquivos de contêineres, recursos do IPC ou memória. A rede pode ser configurada para ser compartilhada entre múltiplos contêineres ou isolada.
{:shortdesc}

O Virtuozzo não é um hypervisor ou uma interface de software para um hypervisor. Ele opera em uma combinação de kernel e espaço do usuário dentro de um kernel proprietário. Essa configuração permite que os contêineres emprestem os recursos essencialmente quando necessário, enquanto os recursos estão disponíveis no nó do host.

Mais informações podem ser localizadas em [Virtuozzo ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://virtuozzo.com/){: new_window}.
