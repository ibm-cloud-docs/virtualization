---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Fornecendo acesso aos contêineres para a rede privada por meio do PIM
{: #giving-containers-access-to-the-private-network-through-pim}

É possível fornecer acesso aos contêineres para a sua rede privada. Mas, é necessário entender as implicações no fornecimento de acesso à sua infraestrutura interna para seus clientes, o que pode levar a problemas de segurança.

1. Acesse o contêiner para o qual você deseja conceder acesso de rede privada.
2. Clique na guia **Rede** e clique em **Configurar**.
3. Agora é possível configurar a seção Rede em bridge:
  * **Rede em bridge**: marque a caixa de seleção.
  * **Conectar-se a**: não é necessário fornecer acesso à rede privada do {{site.data.keyword.cloud}}.
  * **Obter endereço IP por DHCP**: desmarque essa caixa de seleção.
  * **Endereço IP/máscara de sub-rede**: escolha um endereço IP INTERNO por meio do conjunto de endereços privados.
  * Clique em **Enviar**

Agora é necessário atualizar a página por meio do link na parte superior direita para concluir.
