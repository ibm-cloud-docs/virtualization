---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-16"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Configurando e montando um nó iSCSI no armazenamento compartilhado do XenServer
{: #setting-up-and-mounting-an-iscsi-node-in-xenserver-shared-storage}

O armazenamento compartilhado é um repositório de armazenamento (SR) que é qualquer forma de armazenamento que pode ser usada pelo XenServer. É possível escolher entre duas categorias de SRs: compartilhado e não compartilhado. Um SR compartilhado permite que múltiplos nós compartilhem um local de armazenamento por meio do mesmo conjunto. O {{site.data.keyword.cloud}} suporta duas opções de SRs, NFS e LVMoiSCSI (LVM sobre iSCSI).

É necessário instalar um servidor NFS e gerenciá-lo por meio de outro sistema do cliente ou usando um servidor {{site.data.keyword.cloud_notm}} QuantaStor. Siga estas etapas para montar o repositório NFS no XenCenter:

1. Abra o console do XenCenter.
2. Clique em **Novo armazenamento**.
* Na nova caixa de diálogo, selecione **Armazenamento de disco virtual > NFS**.
* Forneça um nome apropriado para o novo SR.
* Insira o caminho server:/path sob **Nome do compartilhamento**
* Clique em **Varrer**

Esse processo varre o compartilhamento do NFS para qualquer SR anterior.

É possível usar o {{site.data.keyword.blockstoragefull}} para LVMoiSCSI. O iSCSI pode ser de um servidor de armazenamento de gerenciamento do cliente ou de uma oferta do {{site.data.keyword.blockstoragefull}}. Para Desempenho e armazenamento de bloco redundante, recupere o IQN do {{site.data.keyword.slportal}}, acessando **Armazenamento > Armazenamento de bloco > Selecionar nome do LUN**. Para montar o nó iSCSI por meio do XenCenter, siga estas etapas:

1. Abra o console do XenCenter.
2. Acesse **Geral >Propriedades** e configure o IQN.
3. Clique em **Novo armazenamento**
4. Na nova caixa de diálogo, acesse **Armazenamento de disco virtual > Software iSCSI**.
5. Insira um nome apropriado para o novo SR.
6. Insira o nome do host ou o IP do destino de iSCSI e mantenha a porta na 3260 padrão.
7. Selecione **Usar CHAP**.
8. Insira o nome de usuário e a senha para o LUN.
9. Clique em **Varrer host de destino**.
* Selecione uma opção **IQNs de destino**.
* Selecione uma opção **LUNs de destino**.
10. Depois que os campos IQN e LUN são preenchidos, clique em **Concluir**. O destino é varrido para verificar os SRs anteriores. Se um SR existir, o instalador perguntará se você deseja criar um novo SR ou anexar ao SR anterior.

Se os servidores estiverem em um conjunto juntos, o repositório do iSCSI será compartilhado automaticamente.

Para obter informações sobre como configurar o iSCSI para caminhos múltiplos, consulte [Configurando caminhos múltiplos de iSCSI ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus1000/sw/5_x/sys_mgmt_config/b_Cisco_N1KV_VMware_Sys_Mgmt_Config_5x/b_Cisco_N1KV_VMware_Sys_Mgmt_Config_5x_chapter_01110.html?dtid=osscdc000283){: new_window}
