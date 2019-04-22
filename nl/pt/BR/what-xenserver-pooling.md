---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Conjunto do XenServer
{: #xenserver-pooling}

O conjunto do XenServer pode ser considerado como até 16 servidores que funcionam como um cluster. Esse cluster não apenas permite recursos compartilhados, mas a migração em tempo real de máquinas virtuais que usam o XenMotion. Um servidor age como o "nó principal", enquanto o restante deles é escravo. A abertura de qualquer um dos servidores no conjunto por meio do XenCenter abre as informações de gerenciamento para o conjunto inteiro. Quando um nó se associa ao conjunto, sua senha de gerenciamento é mudada para corresponder à senha de gerenciamento do nó principal. Quando você remove um nó do conjunto, essa senha não muda.

Todos os sistemas no nó devem ser da mesma família de processadores, como em Intel ou AMD. Idealmente, o hardware deve ser idêntico para que o conjunto seja bem-sucedido. Os problemas de compatibilidade não são inteiramente verdadeiros com o conjunto de CPU heterogêneo. É necessário executar testes de compatibilidade para ver se o hardware é compatível. Para obter mais informações, consulte [Entendendo o conjunto de CPUs heterogêneas ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://support.citrix.com/article/CTX127059){: new_window}.

Há dois métodos para criar um conjunto do XenServer. Um é por meio do console do XenCenter usando a opção "Novo conjunto". Siga as instruções simples passo a passo na tela para criar e incluir sistemas no conjunto. O outro método é usando a linha de comandos. No novo nó escravo, o comando a seguir pode ser executado:

        `xe pool-join master-address= master-username= master-password=`

**Nota**: o endereço IP de gerenciamento é o endereço IP 10.x.x.x do servidor se você usou a instalação padrão.

Quando você trabalha com redes de ligação, um escravo recém-associado pode ter suas interfaces conectadas às redes de ligação incorretas. Para corrigir esse problema, destrua as ligações no escravo por meio da linha de comandos e recrie as ligações com os PIFs e redes adequados.

Há dois métodos de ejeção de um nó escravo do conjunto. O primeiro é usando o XenCenter. Acesse **Conjunto** e selecione **Remover servidor**. Siga as instruções simples passo a passo que são fornecidas na tela. O outro método é usando a linha de comandos. A remoção de um nó pode ser feita por meio de qualquer nó no conjunto. É necessário localizar o _uuid_ do nó que você deseja ejetar, executando o comando a seguir:

        `xe host-list`

Em seguida, execute este comando:

        `xe pool-eject uuid=`

**Nota**: quando um nó ejeta, ele é reinicializado em uma instalação limpa, que inclui a remoção da configuração de rede que é usada para fornecer conectividade pública e privada. A senha do nó escravo ainda é a mesma que o nó principal após a ejeção do conjunto. Você pode ter que reconfigurar a rede via IPMI, pois a remoção pode quebrar as configurações dos escravos.
