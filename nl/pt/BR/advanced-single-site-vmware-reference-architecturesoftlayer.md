---
copyright:
  years: 2014, 2019
lastupdated: "2019-01-15"

subcollection: virtualization

keywords: vmware, ESXi
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:table: .aria-labeledby="caption"}

# Arquitetura de referência avançada do VMware de site único
{: #advanced-single-site-vmware-reference-architecture}

Use as etapas a seguir para criar um ambiente vSphere de site único avançado. As etapas a seguir definem uma implementação de arquitetura de referência do {{site.data.keyword.cloud}} usando as melhores práticas sugeridas pelo VMware.

A arquitetura de referência é destinada ao fornecimento de um ambiente que usa armazenamento compartilhado, VMware High Availability (HA) e vSphere Distributed Resource Scheduler (DRS) e um gateway ou um firewall do {{site.data.keyword.cloud_notm}}. A arquitetura de referência avançada do VMware de site único é adequada para a maioria das implementações de produção que podem ser escaladas conforme a carga de trabalho exigir e podem substituir uma implementação no local ou se estender para um cenário de TI híbrido.

## Visão geral do ambiente
{: #environment-overview}

O ambiente VMware representativo avançado que é descrito consiste em um data center que gerencia dois clusters separados: gerenciamento e capacidade. A configuração pode ser definida usando um único cluster de quatro nós que depende de requisitos. O cluster de gerenciamento contém as seguintes máquinas virtuais (VMs) que são usadas para gerenciar a infraestrutura:

* VMware vCenter Server 5.5 ou 6.0 Appliance
* Microsoft Windows 2012 R2 Standard com funções do Active Directory e do Domain Name System (DNS)

O cluster de capacidade contém os recursos e a infraestrutura que são necessários para criar e executar VMs. Para rede, o ambiente consiste em três VLANs internas privadas e uma VLAN pública única que é usada para comunicação externa. A Tabela 1 especifica os tipos e os nomes de VLAN usados em todo o ambiente.

|Tipo de VLAN|Nome da VLAN|Descrição|                                                                             |
|---|---|---|
|Privado primário| Gerenciamento| Designado para gerenciar e acessar os hosts ESXi físicos e os servidores virtuais.|
|Privado primário| Armazenamento| Designado para gerenciar e acessar o armazenamento compartilhado que está conectado a cada host ESXi.|
|Privado primário| Acesso à VM | Designado a máquinas virtuais que são executadas em cada host ESXi.|
|Público primário | Público | Designado a máquinas virtuais ou outros dispositivos que requerem acesso por meio da rede pública.|
<caption>Tabela 1. VLANs primárias</caption>

Para o armazenamento compartilhado, é possível usar o S.O. Nexus QuantaStor, um servidor de armazenamento compartilhado de único locatário, ou os serviços de armazenamento do {{site.data.keyword.cloud_notm}} Endurance ou Performance. Em qualquer um dos casos, o dispositivo de armazenamento compartilhado é usado para armazenar as VMs nos clusters de gerenciamento e de capacidade. Para obter mais informações sobre as opções de armazenamento, consulte [Soluções de armazenamento do {{site.data.keyword.cloud_notm}} ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://www.ibm.com/cloud/storage){: new_window}.

O ambiente de armazenamento é configurado para suportar volumes NFS.

## Etapa 1 Pedindo VLANs públicas e privadas primárias
{: #step-1-ordering-primary-public-and-private-vlans}

**Nota:** os pedidos de VLAN estão sujeitos à revisão e aprovação. Nenhuma condição ou requisito específico que sinaliza a aprovação automática de um pedido de VLAN foi estabelecido. Os pedidos de VLAN podem ser negados por várias razões.

Quatro VLANs são criadas nesta etapa: uma para gerenciamento, uma para armazenamento, uma para VMs e uma para acesso público. É recomendado que você crie essas quatro VLANs antes de pedir seus servidores. Pedir as VLANs primeiro garante que os servidores serão colocados nas VLANs corretas e no data center correto.

O ambiente consiste em cinco hosts ESXi (dois para o cluster de gerenciamento e três para o cluster de capacidade) e um servidor virtual. A VLAN de gerenciamento privada consiste em uma sub-rede com 16 endereços IP para suportá-la. A VLAN privada primária para armazenamento e tráfego de VM consiste em oito endereços, pois o ambiente contém um único servidor de armazenamento e as VMs usam uma sub-rede móvel. Se um intervalo de sub-rede maior for necessário para a rede de gerenciamento, ajuste o intervalo calculando o número de hosts ESXi e multiplicando por dois. Além disso, certifique-se de especificar o data center para o qual essas VLANs serão criadas.

Depois de efetuar login no {{site.data.keyword.cloud_notm}}, abra um chamado de suporte para as VLANs de gerenciamento e de máquina virtual selecionando **Suporte > Incluir chamado**. Preencha os campos com as informações que estão na **Tabela 2:**.

|Campo|Value|
|---|---|
|Assunto|Pergunta de rede privada|
|Título (Title)|Solicitar VLANs|
|Detalhes|Fornecer três VLANs privadas primárias e uma VLAN pública primária. Associar o esquema de endereçamento a seguir para cada VLAN: <br/><ul><li>Associar 1x18 (16 endereços) para a VLAN privada primária</li><li>Associar 2x29 (oito endereços) para a VLAN privada primária</li><li>Associar 1x29 (oito endereços) para a VLAN pública primária</li></ul>|
|Por que eu preciso dessas VLANs adicionais?|Para colocar hosts, armazenamento e VMs em uma rede diferente para um ambiente VMware.
|Eu preciso de VLANs públicas e/ou privadas?|Privada e pública|
|Quantas VLANs eu preciso?|Privada = 3, Pública = 1|
|Quantos endereços IP eu preciso?|8 a 16 para cada VLAN|
|Em qual roteador as VLANs precisam estar?|Não importa que as VLANs estejam todas no mesmo pod.|
|Em qual pod as VLANs precisam estar?|Todas elas vão para o mesmo pod em <NOME DO DATA CENTER>.|
<caption>Tabela 2. Informações do chamado de suporte</caption>

Depois que as VLANs são fornecidas, anote os números da VLAN, o intervalo de sub-rede e o gateway e designe-os às redes lógicas do vSphere. É possível usar a planilha no Apêndice A: Planilha de VLAN para registrar a VLAN e as informações associadas. Por exemplo:

|Tipo de VLAN|Número da VLAN|Intervalo de IP|Gateway|Propósito|
|---|---|---|---|---|
|Privado primário|1101|10.X.Y.Z/28|10.X.Y.1|Gerenciamento|
|Privado primário|1102|10.A.B.C/29|10.A.B.1|Armazenamento|
|Privado primário|1103|10.Q.R.S/29|10.Q.R.1|Máquinas virtuais|
|Público primário|2101|75.S.T.U/29|75.S.T.1|Acesso público|
<caption>Tabela 3. Amostra de VLAN primária</caption>

**Nota:** não continue com a próxima etapa até que as VLANs sejam fornecidas.

## Etapa 2 Pedindo endereços IP privados móveis
{: #step-2-ordering-portable-private-ip-addresses}

Na Etapa 2, uma solicitação é feita para criar sub-redes privadas móveis para VMs de gerenciamento, acesso ao armazenamento kernel da VM e VMs no cluster de capacidade. É necessário determinar quantos endereços você precisa para cada sub-rede de VLAN. No ambiente representativo, peça os endereços a seguir:

* VLAN de gerenciamento - 1x8 endereços /29 - Um endereço para o vCenter Appliance e um endereço para o DNS e o Active Directory.
* VLAN de armazenamento -1x16 endereços /28 - Crie duas sub-redes na mesma VLAN para armazenamento e duas portas kernel da VM em cada host ESXi usando sub-redes diferentes para acessar os dispositivos de armazenamento compartilhado.
* VLAN da VM - 1x32 endereços /27 - Esses endereços são usados para designar endereços IP a VMs no cluster de capacidade.

Ao pedir uma quantia, certifique-se de considerar quantos endereços IP são necessários dentro dos próximos 30 dias e 6 meses. Também é importante observar que o {{site.data.keyword.cloud_notm}} reserva três a quatro endereços IP por bloco de sub-rede móvel. Como resultado, pedir quatro redes de endereço IP engloba um endereço IP ou zero, se o pod suportar o Hot Standby Router Protocol.

Use as etapas a seguir para pedir um bloco de endereços IP móveis para cada VLAN para cada sub-rede que você deseja criar:

1. Abra uma janela do navegador e efetue login no {{site.data.keyword.cloud_notm}}.
2. Selecione **Conta > Fazer um pedido**.
3. Na janela pop-up, acesse **Rede > Sub-redes / IPs > Pedido**.
4. No menu suspenso, selecione **Privado móvel**.
5. Selecione **XX endereço IP privado móvel** e clique em **Continuar**. **Nota:** _XX_ especifica o número de endereços IP.
6. Selecione a VLAN a ser associada ao bloco de endereços IP e clique em **Continuar**.
7. Complete o preenchimento das informações na tela e clique em **Continuar**.

A criação de endereços IP é bastante rápida e é exibida ao selecionar **Sub-redes** em **Rede > Gerenciamento de IP**. É possível registrar esses endereços IP na planilha que está localizada no Apêndice A: Planilha de VLAN.

## Etapa 3 Pedindo um servidor virtual
{: #step-3-ordering-a-virtual-server}

Um servidor virtual Windows 2012 R2 Standard é fornecido para ser usado como um servidor de utilitário para ISOs e fornecer acesso ao ambiente nessa etapa.

1. Abra uma janela do navegador e efetue login no {{site.data.keyword.cloud_notm}}.
2. Selecione **Conta > Fazer um pedido**.
3. Acesse **Servidor virtual > Horário ou mensal**.
4. Selecione o data center apropriado (onde as VLANs foram criadas) para fornecer o servidor virtual e especificar as especificações a seguir para cada opção:
  * Calculando a instância - Núcleos de 1x2.0 GHz
  * RAM: 4 GB
  * Sistema operacional: Windows Server 2012 R2 Standard Edition (64 bits)
  * Primeiro disco: 100 GB (SAN)
  * Velocidades da porta de uplink - Uplinks de rede pública e privada de 1 Gbps
5. Clique em **Continuar seu pedido** e selecione as VLANs de back-end e front-end na tela **Resumo do pedido e faturamento**. **Nota:** a seleção de VLANs é importante para que o utilitário possa ser colocado no pod correto dentro do data center. Para o ambiente de exemplo, a VLAN de back-end é a VLAN de gerenciamento (1101) e a VLAN de front-end é a VLAN pública (2101)
6. Insira um nome de host e um nome de domínio para o servidor e clique em **Fazer pedido**.

## Etapa 4 Pedindo hosts ESXi e gateway/firewall
{: #step-4-ordering-esxi-hosts-and-gateway-firewall}

É necessário pedir os hosts ESXi e o dispositivo de gateway e firewall do Brocade vRouter (Vyatta) enquanto o servidor virtual é fornecido. Peça todos esses servidores ao mesmo tempo para que eles sejam colocados no mesmo pod ao mesmo tempo. Pedir hardware em horários separados pode fazer com que os hosts e os firewalls estejam em pods separados dentro de um data center do {{site.data.keyword.cloud_notm}}.

### Hosts ESXi
{: #esxi-hosts}

O sistema operacional para cada host ESXi pedido para o ambiente é o VMware ESXi 5.5. Se desejar usar licenças do {{site.data.keyword.cloud_notm}} vSphere, os encargos mensais incorridos serão baseados no uso.

Outra opção é instalar o ESXi usando seu próprio ISO. As instruções para esse processo podem ser localizadas em [Instalando o VMware vSphere ESXi por meio de console remoto e mídia virtual](/docs/infrastructure/vmware?topic=VMware-installing-vmware-vsphere-esxi-via-remote-console-and-virtual-media). Se desejar instalar o ESXi usando seu próprio ISO, certifique-se de selecionar **Sem sistema operacional** como o sistema operacional para os hosts de gerenciamento e capacidade durante o processo de pedido.

**Nota:** essa implementação requer o licenciamento do Enterprise Plus para usar os vSphere Distributed Virtual Switches. Se a sua licença não for válida para o Enterprise Plus, será necessário usar a licença VMware Service Provider Program (VSPP) fornecida pelo {{site.data.keyword.cloud_notm}}.

### Pedindo hosts de gerenciamento
{: #ordering-management-hosts}

Use as etapas a seguir para pedir os servidores host de gerenciamento.

1. Abra uma janela do navegador e efetue login no {{site.data.keyword.cloud_notm}}.
2. Selecione **Conta > Fazer um pedido**.
3. Selecione **Bare Metal Servers > Mensal**.
4. Escolha um servidor apropriado que atenda aos requisitos para o cluster de gerenciamento na tela de lista de servidores. **Nota:** no mínimo, o ESXi 5.5 requer um processador dual-core único, 4 GB de RAM e controlador Ethernet de 1 Gb.
5. Selecione o data center apropriado (onde as VLANs foram criadas) para fornecer os servidores ESXi e determinar as especificações a seguir para cada opção:
  * Quantidade: 2
  * RAM: 32 GB
  * Sistema operacional: VMware ESXi 5.5 ('sem sistema operacional ' se você estiver usando o [Instalando o VMware vSphere ESXi por meio de console remoto e mídia virtual](/docs/infrastructure/vmware?topic=VMware-installing-vmware-vsphere-esxi-via-remote-console-and-virtual-media))
  * Discos rígidos: discos 1 & 2: SATA de 500 GB em RAID 1
  * Largura da banda pública: rede privada apenas-> 0 GB de largura da banda
  * Velocidades da porta de uplink: uplinks de rede privada redundante de 10 Gbps
6. Clique em **Continuar seu pedido**.
7. Clique em **Incluir servidor** para iniciar a inclusão de hosts ESXi para o cluster de capacidade para o pedido.

### Pedindo hosts de capacidade
{: #ordering-capacity-hosts}

1. Escolha um servidor apropriado que atenda aos requisitos para os hosts de cluster de capacidade na tela de lista de servidores. **Nota:** no mínimo, o ESXi 5.5 requer um processador dual-core único, 4 GB de RAM e um controlador Ethernet de 1 GB.
2. Selecione o data center apropriado (onde as VLANs foram criadas) para fornecer os servidores ESXi e determinar as especificações a seguir para cada opção:
  * Quantidade: 3
  * RAM: 128 GB
  * Sistema operacional: VMware ESXi 5.5 ('sem sistema operacional ' se você estiver usando o [Instalando o VMware vSphere ESXi por meio de console remoto e mídia virtual](/docs/infrastructure/vmware?topic=VMware-installing-vmware-vsphere-esxi-via-remote-console-and-virtual-media))
  * Disco rígido: discos 1 e 2: SATA de 500 GB no RAID 1
  * Largura da banda pública: rede privada apenas > 0 GB de largura da banda
  * Velocidades da porta de uplink: uplinks de rede privada redundante de 10 Gbps
3. Clique em **Continuar seu pedido**.

### Concluindo a configuração
{: #completing-configuration}

Agora, você tem hosts ESXi em seu carrinho de compras. Para que os dispositivos sejam fornecidos corretamente, é necessário designar a VLAN pública (se aplicável), a VLAN privada, o nome do host e o domínio para os dispositivos.

1. Designe as VLANs a seguir e crie nomes de host para os dispositivos:

* Hosts ESXi - VLAN de back-end: (1101)
* VLAN de back-end: (1101)
* VLAN de front-end: (2101)

2. Selecione o método de pagamento, concorde com os termos e condições e clique em **Finalizar seu pedido**. **Importante:** não continue com a Etapa 5 até que os servidores sejam fornecidos e estejam acessíveis por meio da VPN ou do servidor virtual que foi pedido na etapa anterior.

## Etapa 5 Entroncamento de VLANs em comutadores BCS
{: #step-5-trunking-vlans-on-bcs-switches}

Por padrão, as portas são colocadas nos comutadores do cliente de back-end (ou seja, o comutador de rede privada em um pod, como um Backend Customer Switch (BCS)) no modo de acesso. Como resultado, é necessário fazer o entroncamento das portas que estão conectadas aos hosts ESXi para que os hosts possam acessar o armazenamento e as VMs possam se comunicar na rede privada.

Antes de abrir o chamado para o entroncamento das VLANs, é necessário determinar quais interfaces de rede estão na rede privada. Para determinar a interface, acesse os **Detalhes do dispositivo** para cada servidor ESXi e acesse **Rede >Privada**. Para esse ambiente, `eth0` e `eth2` são os adaptadores de rede privada.

Além de entroncamento das VLANs para os hosts ESXi, deve-se também desvincular os NICs para os hosts de gerenciamento e de capacidade. Você desvincula os NICs porque o Link Aggregation Control Protocol (LACP) não é compatível com os caminhos múltiplos de iSCSI de software.

Para fazer o entroncamento das VLANs e desvincular os NICs, é necessário abrir um chamado seguindo estas etapas:

1. Abra uma janela do navegador e efetue login no {{site.data.keyword.cloud_notm}}.
2. Selecione **Suporte, Incluir chamado**.
3. Digite as informações a seguir:
  * Assunto: Pergunta de rede privada
  * Título: Entroncamento de VLANs e desvinculação de NICs
  * Detalhes: VLANs de tronco `<Management VLAN>`, `<Storage VLAN>`
e `<VM VLAN>` no par eth0 e eth2 do NIC para os hosts a seguir [listar cada host ESXi]. Além disso, desvincule (remova o LACP) os NICs privados (eth0 e eth2) nos servidores a seguir: [listar cada host ESXi]
4. Clique em **Incluir chamado**.

Certifique-se de mudar as VLANs que são designadas no <> com suas VLANs reais

## Etapa 6 Configurando a rede do host de gerenciamento
{: #step-6-configuring-management-host-networking}

Depois que os servidores são fornecidos e as VLANs são entroncadas, é necessário configurar a rede nos hosts do cluster de gerenciamento. Para essa configuração, use comutadores vSphere padrão para o cluster de gerenciamento. Exceto para os grupos da porta do vMotion e de tolerância a falhas, use endereços IP móveis para configurar os grupos da porta do kernel da VM. **Nota:** use seu próprio esquema de IP para vMotion e tolerância a falhas porque o tráfego não precisa ser roteado. No entanto, todos os hosts precisam estar na mesma sub-rede que outros hosts no cluster para usar os recursos do vMotion e de tolerância a falhas. Se a sub-rede precisa ser roteada, é recomendado que você use endereços IP móveis do {{site.data.keyword.cloud_notm}}.

Para configurar os grupos da porta, o cliente vSphere deve ser instalado no servidor virtual de utilitário ou na estação de trabalho que acessa os hosts por meio da VPN de gerenciamento do {{site.data.keyword.cloud_notm}}.

1. Abra uma janela do navegador e efetue login no {{site.data.keyword.cloud_notm}}.
2. Depois de conectar-se ao servidor de utilitário ou à VPN do {{site.data.keyword.cloud_notm}}, inicie o cliente vSphere.
3. Insira o endereço IP, o nome do usuário e a senha de um dos hosts ESXi de gerenciamento. (É possível localizar a senha raiz para o host ESXi selecionando **Detalhes do dispositivo > Senhas**).
4. Acesse **Configurações > Rede** e crie ou modifique os grupos da porta a seguir no comutador de padrões do vSphere (mais provavelmente vSwitch0).

Conclua estas etapas para cada host no cluster de gerenciamento.

### Propriedades do vSwitch0
{: #vswitch0-properties}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabela 4. Propriedades do vSwitch0</caption>
<tbody>
<tr><th>Adaptador de rede</th><th>vmnicX e vmnicY</th></tr>
<tr><td>Balanceamento de Car</td><td>Roteador com base no ID da porta virtual de origem</td></tr>
<tr><td>Adaptadores ativos</td><td>vmnicX e vmnicY</td></tr>
</tbody>
</table>

### Editar grupo da porta de máquina virtual existente
{: #edit-existing-virtual-machine-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabela 5. Grupo da porta vmPG-Management</caption>
<tbody>
<tr><th>Rótulo da rede</th><th>*vmPG-Management*</th></tr>
</tbody>
</table>

### Editar grupo da porta do kernel da VM existente
{: #edit-existing-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabela 6. Grupo da porta vmkPG-Management</caption>
<tbody>
<tr><th>Rótulo da rede</th><th>*vmkPG-Management*</th></tr>
</tbody>
</table>

### Incluir grupo da porta do kernel da VM vMotion
{: #add-vmotion-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabela 7. Grupo da porta vMotion</caption>
<tbody>
<tr><th>Tipo de conexão</th><th>VMKernel</th></tr>
<tr><td>Rótulo da rede</td><td>*vmkPG-vMotion*</td></tr>
<tr><td>ID de VLAN</td><td>*&lt;VLAN de armazenamento&gt; (1102)*</td></tr>
<tr><td>Tráfego de vMotion</td><td>Ativado</td></tr>
<tr><td>Endereço IP</td><td>*172.16.10.X/24*<br/><br/>* Endereço definido pelo usuário que pode ser uma sub-rede diferente. Certifique-se de que os outros endereços vMotion em cada host estejam na mesma sub-rede.*</td></tr>
<tr><td>Máscara de sub-rede</td><td>255.255.255.0</td></tr>
</tbody>
</table>

### Incluir o grupo da porta de kernel da VM de tolerância
{: #add-fault-tolerance-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabela 8. Grupo da porta FT</caption>
<tbody>
<tr><th>Tipo de conexão</th><th>Kernel da VM</th></tr>
<tr><td>Rótulo da rede</td><td>*vmkPG-FT*</td></tr>
<tr><td>ID de VLAN</td><td>*&lt;VLAN de armazenamento&gt; (1102)*</td></tr>
<tr><td>Criação de log de tolerância a falhas</td><td>Ativado</td></tr>
<tr><td>Endereço IP</td><td>*172.16.20.X/24*<br/><br/>*Endereço definido pelo usuário que pode ser uma sub-rede diferente, se necessário. Certifique-se de que os outros endereços FT em cada host estejam na mesma sub-rede.*</td></tr>
<tr><td>Máscara de rede</td><td>255.255.255.0</td></tr>
</tbody>
</table>

### Incluir grupo da porta do kernel da VM de armazenamento
{: #add-storage-vm-kernel-port-group}

Atualize a seção **Notas** de cada endereço IP móvel com o nome do host e a porta do kernel da VM designados. A seção **Notas** pode ser localizada acessando o {{site.data.keyword.slportal_full}} e selecionando **Rede > Gerenciamento de IP > Sub-redes > [Sub-rede]**.

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabela 9. Grupo da porta de armazenamento</caption>
<tbody>
<tr><th>Tipo de conexão</th><th>Kernel da VM</th></tr>
<tr><td>Rótulo da rede</td><td>*vmkPG-Storage*</td></tr>
<tr><td>ID de VLAN</td><td>*&lt;VLAN de armazenamento&gt; (1102)*</td></tr>
<tr><td>Endereço IP</td><td>*Endereço privado móvel*<br/><br/>*Um endereço IP que é selecionado por meio dos endereços privados móveis que são ligados à VLAN de armazenamento.*</td></tr>
<tr><td>Máscara de rede</td><td>*Máscara de sub-rede associada ao intervalo de IP*</td></tr>
</tbody>
</table>

### Incluir grupo da porta de endereço público
{: #add-public-address-port-group}

Atualize a seção **Notas** de cada endereço IP móvel com o nome do host e a porta do kernel da VM designados. A seção **Notas** está no {{site.data.keyword.slportal}}, selecionando **Rede > Gerenciamento de IP > Sub-redes > [Sub-rede]**.

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabela 10. Grupo da porta público</caption>
<tbody>
<tr><th>Tipo de conexão</th><th>Máquina virtual</th></tr>
<tr><td>Rótulo da rede</td><td>vmPG-Public</td></tr>
<tr><td>ID de VLAN</td><td>&lt;VLAN pública primária&gt; (por exemplo, 2101)</td></tr>
</tbody>
</table>

## Etapa 7 Download ou upload de imagens ISO e do vCenter Virtual Appliance
{: #step-7-download-or-upload-iso-images-and-vcenter-virtual-appliance}

Agora, o ambiente está pronto para implementar o VMware vCenter Virtual Appliance e instalar uma máquina virtual para DNS, Active Directory ou BIND. No entanto, antes de implementar, é necessário fazer download das imagens. Para fazer download das imagens, acesse remotamente a área de trabalho para o servidor virtual fornecido anteriormente e faça download das imagens apropriadas no servidor virtual para o seu ambiente:

* Active Directory / Windows DNS: imagem ISO do Windows Server 2008R2/Windows Server 2012 (sua mídia licenciada)
* Linux BIND: [Imagem de instalação do CentOS ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](http://isoredirect.centos.org/centos/6/isos/x86_64/){: new_window}
* [vCenter Virtual Appliance ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://my.vmware.com/web/vmware/info/slug/datacenter_cloud_infrastructure/vmware_vsphere/5_5){: new_window} (requer uma assinatura válida do VMware)

Depois de fazer download do ISO, é necessário fazer upload da imagem para um armazenamento de dados do host de gerenciamento para que ela possa ser anexada a uma máquina virtual. Use o cliente vSphere para se conectar a um host de gerenciamento e criar um diretório ISO no armazenamento de dados local.

## Etapa 8 Implementando o DNS
{: #step-8-deploying-dns}

É necessário implementar uma VM (em um host de gerenciamento) e instalar os serviços DNS. Use o cliente vSphere tradicional para criar uma VM no host ESXi de gerenciamento no qual o ISO do Linux ou do Windows está localizado. Conecte o ISO apropriado (Windows ou CentOS) para implementar um servidor DNS na VM. Certifique-se de associar a VM ao grupo da porta da VM (vmpg) - grupo da porta de gerenciamento que foi criado em uma etapa anterior.

Após a VM ser instalada, designe um endereço IP e o gateway padrão por meio do grupo de sub-rede privada móvel. Se você estiver usando a planilha de VLAN no Apêndice A, a Sub-rede da VM de gerenciamento. Atualize a seção **Notas** de cada endereço IP móvel com o nome da VM designada. A seção **Notas** pode ser localizada acessando o {{site.data.keyword.slportal}} e selecionando **Rede > Gerenciamento de IP > Sub-redes > [Sub-rede]**.

Embora esteja além do escopo deste documento detalhar as etapas que são necessárias para ativar o DNS, a orientação a seguir é fornecida:

1. Configure o **Encaminhamento de DNS** para os hosts de DNS locais service.softlayer.com:
  * `rs1.service.softlayer.com 10.0.80.11`
  * `rs2.service.softlayer.com 10.0.80.12`
2. Depois de configurar o DNS, crie uma zona de DNS local (dal06.mycompany.local) e uma zona de consulta reversa para todas as sub-redes móveis e primárias fornecidas.
3. Inclua um registro A HOST para cada endereço IP de gerenciamento do host (vmk0 em vmkPG-management).
4. Inclua um registro A HOST por meio da sub-rede privada móvel que está ligada à VLAN de gerenciamento para o seu vCenter Virtual Appliance.
5. Atualize a seção **Notas** da Sub-rede de IP móvel que você designou ao vCenter.

Para obter mais informações, consulte os links a seguir:
* [DNS do Windows e Active Directory ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://social.technet.microsoft.com/wiki/contents/articles/12370.step-by-step-guide-for-setting-up-a-windows-server-2012-domain-controller.aspx){: new_window}.
<!--* [CentOS BIND ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://www.centos.org/docs/2/rhl-rg-en-7.2/s1-bind-configuration.html){: new_window}.-->

## Etapa 9 Implementando e configurando o vCenter Virtual Appliance
{: #step-9-deploying-and-configuring-vcenter-virtual-appliance}

Agora que o DNS está configurado, é possível implementar e configurar o vCenter Server Appliance. Para implementar o dispositivo, siga estas etapas:

1. Remotamente, acesse a área de trabalho para o servidor virtual e abra o vSphere Client.
2. Conecte-se a um host de gerenciamento e selecione **Arquivo, Implementar modelo OVF**.
3. Siga o assistente para concluir a implementação.

<!--For more information about OVF Template deployment, see [VMware documentation ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://pubs.vmware.com/vsphere-55/index.jsp?topic=%2Fcom.vmware.vsphere.hostclient.doc%2FGUID-6C847F77-8CB2-4187-BD7F-E7D3D5BD897B.html&resultof=%22thick%22%20%22thin%22%20){: new_window}.-->

Como nenhum servidor de Protocolo de Configuração de Host Dinâmico (DHCP) está disponível para designar ao vCenter Virtual Appliance um endereço IP quando ele for ligado, deve-se usar o console raiz para configurar o dispositivo. **Nota:** uma mensagem `NO NETWORKING DETECTED` é exibida no vCenter Virtual Appliance Console.

Siga estas etapas para configurar o dispositivo:

1. Efetue login no console com um **Nome de usuário** de 'raiz' e uma **Senha** de 'vmware'.
2. Execute `/opt/vmware/share/vami/vami_config_net` e siga o assistente de texto para configurar as propriedades de IP, sub-rede e DNS. Lembre-se de usar o endereço IP do servidor DNS ou BIND que foi criado na **Etapa 8: Implementar o DNS**.
3. Salve as configurações de rede, saia do console e abra um navegador no servidor virtual.
4. Acesse o endereço IP que você forneceu para o vCenter Virtual Appliance (VCVA) anexado com a porta 5480<!-- (https://:5480)-->.
5. Aceite o Contrato de licença de usuário final do assistente, responda as perguntas do **Programa de experiência de melhoria do cliente** e selecione **Configurar opções, Definir configuração customizada**.
6. Clique em **Avançar** e insira os valores a seguir:
<table border="1" cellpadding="0" cellspacing="0"><caption>Tabela 11. Assistente de configuração do VCVA</caption><tbody><tr><th>Menu Assistente</th><th>Opção</th><th>Value</th></tr><tr><td>Configurações de banco de dados</td><td>Tipo de banco de dados</td><td>integrado</td></tr><tr><td>Configurações de SSO</td><td>Tipo de implementação de SSO</td><td>integrado</td></tr><tr><td>Configurações de SSO</td><td>Nova senha do administrador *(para administrator@vsphere.local)*</td><td>&lt;Insira uma senha&gt;</td></tr><tr><td>Configurações de SSO</td><td>Digite novamente a nova senha</td><td>&lt;Insira a mesma senha que você usou anteriormente&gt;</td></tr><tr><td>Sincronização de</td><td>Sincronização de NTP</td><td>servertime.service.softlayer.com</td></tr></tbody></table>
7. Clique em **Iniciar**. O VCVA está configurado.
8. Mude a senha raiz usando as opções em **Administrador**.
9. Efetue logout da página da web de configuração do VCVA.
10. Acesse o vSphere Web Client inserindo o endereço IP do VCVA anexado com `9443/vsphere-client`<!-- (https://:9443/vsphere-client)-->.
11. Efetue login na raiz e selecione **Administração, Licenças**.
12. Insira sua licença do VMware vCenter.

## Etapa 10 Criar clusters do vCenter e comutador virtual distribuído
{ #step-10-create-vcenter-clusters-and-distributed-virtual-switch}

Agora que o VCVA está configurado e licenciado, é possível criar o data center, as construções de cluster e os comutadores virtuais distribuídos para o cluster de capacidade.

### Criando o data center e os clusters
{: #creating-data-center-and-clusters}

1. No cliente vSphere, acesse a tela **Página inicial**.
2. Selecione **Introdução** e clique em **clicar aqui**.
3. Clique em **Criar data center**.
4. Insira um nome de data center (o ambiente de exemplo é o data center Toronto 01. Use `Toronto 01` como o nome do data center).
5. Depois que o data center for criado, clique em **Criar um cluster** na página **Introdução**.
6. Nomeie o primeiro cluster `Management`. Deixe todas as outras opções limpas e clique em **OK**.
7. Crie outro cluster quando terminar usando o mesmo processo do cluster de gerenciamento.

Agora é possível incluir os hosts de gerenciamento e de capacidade nos clusters de gerenciamento e de capacidade usando **Incluir um host**. Certifique-se de usar o nome completo do domínio (FQDN) do servidor em vez do endereço IP para que o DNS seja usado quando cada host for incluído.

Depois de incluir os hosts ESXi no vCenter, você pode observar um par de mensagens de aviso sobre a ativação do shell e do SSH em cada host ESXi. Para suprimir esses avisos, clique em **Suprimir aviso > Sim** na janela pop-up. Se o link Suprimir avisos não estiver presente, siga estas etapas:

1. Acesse a guia **Gerenciar** do host ESXi.
2. Selecione as **Configurações** e clique em **Configurações avançadas do sistema**.
3. Localize a chave **UserVars.SupressShellWarning** e mude o valor para **1**.
4. Clique em **OK**.

Depois que os hosts de gerenciamento e de capacidade forem incluídos em seus respectivos clusters, acesse cada host e configure o DNS e o NTP. Para configurar o DNS, siga estas etapas:

1. Clique em um host e selecione **Gerenciar > Rede**.
2. Selecione a pilha do sistema padrão (**Configuração de TCP/IP**) e clique no ícone de lápis.
3. Insira o endereço IP do servidor DNS que você criou anteriormente e o nome do host e do domínio.

Para configurações de NTP:

1. Acesse **Gerenciar, Configurações, Configuração de horário**.
* Insira `servertime.service.softlayer.com` como o servidor NTP.
* Configure a **Política de inicialização de serviço do NTP** para `Iniciar e parar com o host`.

***Criando um comutador virtual distribuído para os hosts de capacidade***

1. Use o vSphere Web Client para acessar a **Rede** e clique com o botão direito no nome do data center.
2. Selecione **Novo comutador distribuído**.
3. Nomeie o comutador distribuído e clique em **Avançar**.
4. Selecione a versão de comutador distribuído apropriada e clique em **Avançar**.
5. Na tela **Editar configurações**, insira `2` como o número de uplinks e limpe a seleção **Criar um grupo da porta padrão**.
6. Clique em **Avançar > Concluir** e crie o comutador virtual distribuído.

***Criar grupos da porta para comutador virtual distribuído***

Agora que o comutador virtual distribuído está presente, deve-se criar grupos da porta para vMotion, tolerância a falhas, VMs e armazenamento.

***Criando o grupo da porta de gerenciamento dvpg-Private-VM***

1. Acesse a seção Rede usando o vSphere Web Client.
2. Clique com o botão direito no comutador virtual distribuído (DVS).
3. Clique em **Novo grupo distribuído da porta...** e insira as informações na Tabela 14 para o primeiro grupo da porta.
4. Deixe os valores padrão para qualquer opção que não esteja especificada na tabela.

| Menu Novo grupo distribuído da porta | Campo | Value |
| --- | --- | --- |
| Nome e localização | Nome | dvpg-Private-VM Management |
| Definir configurações | Avançado | Marcar Customizar configuração de políticas padrão |
| Configurar políticas (Teaming e Failover)| Balanceamento de Car| Rota que é baseada na carga física do NIC |
| Configurar políticas (Teaming e Failover)| Pedido de Failover| Uplinks ativos: uplink 1 e uplink 2 |
<caption>Tabela 12. Grupo da porta de gerenciamento da VM DVS</caption>

Depois de criar o primeiro grupo da porta, crie os grupos da porta restantes com as opções de configurações a seguir (Tabela 15 até a Tabela 19).

***Criar grupo da porta dvpg-Private-vMotion***

|Menu Novo grupo distribuído da porta|Campo|Value|
|---|---|---|
|Nome e localização|Nome|dvpg-Private-vMotion|
|Definir configurações|Tipo de VLAN|VLAN|
|Definir configurações|ID de VLAN|&lt;VLAN de armazenamento&gt;|
|Definir configurações|Avançado|Marcar Customizar configuração de políticas padrão|
|Configurar políticas (Teaming e Failover)|Balanceamento de Car|Rota que é baseada na carga física do NIC|
|Configurar políticas (Teaming e Failover)|Pedido de Failover|Uplinks ativos: uplink 1 e uplink 2|
<caption>Tabela 13. Grupo da porta vMotion DVS</caption>

***Criar grupo da porta dvpg-Private-Fault Tolerance***

|Menu Novo grupo distribuído da porta|Campo|Value|
|---|---|---|
|Nome e localização|Nome|dvpg-Private-Fault Tolerance|
|Definir configurações|Tipo de VLAN|VLAN|
|Definir configurações|ID de VLAN|&lt;VLAN de armazenamento&gt;|
|Definir configurações|Avançado|Marcar Customizar configuração de políticas padrão|
|Configurar políticas (Teaming e Failover)|Balanceamento de Car|Rota que é baseada na carga física do NIC|
|Configurar políticas (Teaming e Failover)|Pedido de Failover|Uplinks ativos: uplink 1 e uplink 2|
<caption>Tabela 14. Grupo da porta FT do DVS</caption>

***Criar grupo da porta dvpg-Private-VM Access***

|Menu Novo grupo distribuído da porta|Campo|Value|
|---|---|---|
|Nome e localização|Nome|dvpg-Private-VM Access|
|Definir configurações|Tipo de VLAN|VLAN|
|Definir configurações|ID de VLAN|&lt;VLAN de máquina virtual&gt;|
|Definir configurações|Avançado|Marcar Customizar configuração de políticas padrão|
|Configurar políticas (Teaming e Failover)|Balanceamento de Car|Rota que é baseada na carga física do NIC|
|Configurar políticas (Teaming e Failover)|Pedido de Failover|Uplinks ativos: uplink 1 e uplink 2|
<caption>Tabela 15. Grupo de portas de acesso da VM DVS</caption>

***Criar dvpg-Private-Storage***

|Menu Novo grupo distribuído da porta|Campo|Value|
|---|---|---|
|Nome e localização|Nome|Caminho A de dvpg-Private-Storage|
|Definir configurações|Tipo de VLAN|VLAN|
|Definir configurações|ID de VLAN|&lt;VLAN de armazenamento&gt;|
|Definir configurações|Avançado|Marcar Customizar configuração de políticas padrão|
|Configurar políticas (Teaming e Failover)|Balanceamento de Car|Rota que é baseada na carga física do NIC|
|Configurar políticas (Teaming e Failover)|Pedido de Failover|Uplinks ativos: uplink 1 e uplink 2|
<caption>Tabela 16. Grupo da porta de armazenamento DVS</caption>

***Criar dvpg-Primary-Public***

|Menu Novo grupo distribuído da porta|Campo|Valor|
|Nome e localização|Nome|dvpg-Private-Storage|
|Definir configurações|Tipo de VLAN|VLAN
|Definir configurações|ID da VLAN|&lt;VLAN pública primária&gt;|
|Definir configurações|Avançado|Verificar a configuração das políticas padrão customizadas|
|Configurar políticas (Teaming e Failover)|Balanceamento de carga|Rota que se baseia no carregamento físico de NIC|
|Configurar políticas (Teaming e Failover)|Pedido de failover|Uplinks ativos: uplink 1 e uplink 2|
<caption>Tabela 17. Grupo da porta B do caminho do armazenamento do DVS</caption>

## Etapa 11 Migrando hosts ESXi no cluster de capacidade para o comutador virtual distribuído
{: #step-11-migrating-esxi-hosts-in-capacity-cluster-to-distributed-virtual-switch}

Agora que os hosts de capacidade são incluídos no cluster de capacidade, é possível migrar a configuração do comutador padrão virtual para o comutador virtual distribuído que você criou na *Etapa 10: Criar clusters vCenter e comutador virtual distribuído*. A migração é feita para um host e você aplica um perfil do host posteriormente para configurar o restante do cluster.

Antes de começar a incluir adaptadores VMkernel, designe os vmnics para os uplinks no comutador virtual distribuído.

1. Clique em **Listas de inventário do vCenter, comutadores distribuídos**.
2. Selecione o comutador distribuído apropriado para os hosts de capacidade.
3. Clique em **Incluir e gerenciar hosts** na página **Introdução**.
4. Use as configurações a seguir para incluir uplinks e migrar o VMkernel existente que está associado ao gerenciamento do host.
  - <table border="1" cellpadding="0" cellspacing="0"><caption>Tabela 18. DVS Incluir hosts</caption><tbody><tr><th>Menu</th><th>Campo</th><th>Value</th></tr><tr><td>Selecionar tarefa</td><td>Selecionar tarefas</td><td>Incluir hosts</td></tr><tr><td>Selecionar hosts</td><td>Clique em **Novos hosts**</td><td>Clique no host de capacidade</td></tr><tr><td>Selecionar tarefas do adaptador de rede</td><td>Selecionar tarefas do adaptador de rede</td><td>Selecione Gerenciar adaptadores físicos e Gerenciar adaptadores VMkernel</td></tr></tbody></table>
5. Selecione um dos vmnics privados e clique em **Gerenciar adaptadores de rede físicos > Designar uplink**.
6. Selecione `uplink1` na tela pop-up e clique em **OK**.
7. Repita essas etapas para o outro vmnic privado e designe-o a `uplink2`.
8. Clique em **Avançar**, destaque o adaptador VMkernel vmk0 e clique em **Designar grupo da porta**.
9. Selecione **Gerenciamento dvpg-Private-VM** na janela pop-up e clique em **OK**.
10. Clique em **Avançar** duas vezes e, em seguida, clique em **Concluir** para concluir a migração para o comutador virtual distribuído. **Nota:** você pode perder brevemente a conectividade de rede para o host. A conexão restabelece rapidamente.

Depois de migrar o adaptador vmk0 para o comutador virtual distribuído, é possível incluir kernels de VM em cada grupo da porta no DVS.

11. Clique em **Gerenciar > Rede** no host no vCenter.
12. Acesse **Adaptadores kernel da VM > Incluir rede de host** e inclua os adaptadores kernel da VM que estão nas Tabelas 19 e 21. Para incluir esses adaptadores, migre para o menu "Adaptadores kernel da VM" que está na guia **Gerenciar > Rede** no host dentro do vCenter. Em seguida, clique no ícone "Incluir rede do host" e inclua os adaptadores de Kernel da VM a seguir.

***Incluir vmk1 para vMotion***

|Menu|Campo|Value|
|---|---|---|
|Selecionar o tipo de conexão|Selecionar o tipo de conexão|Adaptador de rede VMKernel|
|Selecionar o dispositivo de destino|Selecionar um grupo distribuído da porta existente|dvpg-Private-vMotion|
|Selecionar tarefas do adaptador de rede|Selecionar tarefas do adaptador de rede|Selecionar **Gerenciar adaptadores físicos** e **Gerenciar adaptadores de kernel da VM**|
|Propriedades da porta|Ativar serviços|Verificar o tráfego de vMotion|
|Configurações de IPv4|Endereço IPv4|*172.16.10.X/24*<br/><br/>*Este é um endereço definido pelo usuário e pode ser uma sub-rede diferente, se necessário. Certifique-se de que os outros endereços vMotion em cada host estejam na mesma sub-rede.*|
|Configurações de IPv4|Máscara de sub-rede|255.255.255.0|
<caption>Tabela 19. vMotion de rede do host</caption>

***Incluir vmk2 para tolerância a falhas***

|Menu|Campo|Value|
|---|---|---|
|Selecionar o tipo de conexão|Selecionar o tipo de conexão|Adaptador de rede VMKernel|
|Selecionar o dispositivo de destino|Selecionar um grupo distribuído da porta existente|dvpg-Private-Fault Tolerance|
|Selecionar tarefas do adaptador de rede|Selecionar tarefas do adaptador de rede|Selecione **Gerenciar adaptadores físicos** e **Gerenciar adaptadores VMKernel**|
|Propriedades da porta|Ativar serviços|Verificar criação de log de tolerância a falhas|
|Configurações de IPv4|Endereço IPv4|*172.16.20.X/24*<br/><br/>*Este é um endereço definido pelo usuário e pode ser uma sub-rede diferente, se necessário. Certifique-se de que os outros endereços FT em cada host estejam na mesma sub-rede.*|
|Configurações de IPv4|Máscara de sub-rede|255.255.255.0|
<caption>Tabela 20. Tolerância a falhas de rede do host</caption>

***Incluir vmk3 para armazenamento***

|Menu|Campo|Value|
|---|---|---|
|Selecionar o tipo de conexão|Selecionar o tipo de conexão|Adaptador de rede VMkernel|
|Selecionar o dispositivo de destino|Selecionar um grupo distribuído da porta existente|dvpg-Private-Storage|
|Selecionar tarefas do adaptador de rede|Selecionar tarefas do adaptador de rede|Selecione Gerenciar adaptadores físicos e Gerenciar adaptadores VMkernel|
|Configurações de IPv4|Endereço IPv4|*Endereço privado móvel*<br/><br/>*Esse endereço IP é selecionado por meio dos endereços privados móveis que são ligados à VLAN de armazenamento. Esse endereço precisa de uma sub-rede diferente do caminho do armazenamento B.*|
|Configurações de IPv4|Máscara de sub-rede|Máscara de sub-rede associada ao intervalo de IP|
<caption>Tabela 21. Armazenamento de rede do host</caption>

***Criando um perfil do host***

Para criar um perfil do host, deve-se capturá-lo por meio do host de capacidade único que você configurou anteriormente.

1. Acesse a tela inicial do vSphere Web Client, clique no ícone **Perfis do host**.
2. Clique no sinal de mais (+) verde, **Extrair perfil de um host** e selecione o host de capacidade configurado anteriormente na janela pop-up.
3. Clique em **Avançar**.
4. Forneça ao perfil do host (perfil do host Capacity01) um nome apropriado, insira uma descrição e clique em **Avançar**.
5. Revise as configurações e clique em **Concluir**.

Depois de criar o perfil do host, é necessário modificá-lo para que ele não solicite endereços MAC quando o perfil for aplicado ao restante dos hosts no cluster de capacidade.

1. Clique com o botão direito no perfil do host que você criou e selecione **Editar configurações > Editar perfil do host, NIC virtual do host**.
2. Na área de janela direita, mude **Determinar como o endereço de MAC para vmknic é decidido** para **O usuário deve escolher explicitamente a opção de política**.
3. Clique em  ** Avançar **  e, em seguida, clique em  ** Concluir **.

***Anexando o perfil do host ao cluster de capacidade***

Agora que você criou um perfil do host, é necessário anexar o perfil do host ao cluster. Os clusters são conectados para que possam ser aplicados aos hosts de capacidade.

1. Acesse a visualização **Host e clusters** no vSphere Web Client.
2. Insira o modo de manutenção para cada host no cluster. **Nota:** os perfis podem ser aplicados somente a hosts que estão no modo de manutenção.
3. Clique com o botão direito no cluster de capacidade e selecione **Anexar perfil do host** no menu pop-up.
4. Selecione o perfil do host que você criou e clique em **Avançar**.
5. Insira as informações apropriadas na tela **Customizar host** e clique em **Concluir**.
6. Remova os hosts do modo de manutenção (modo de não manutenção).

## Etapa 12 Pedir, configurar e anexar armazenamento compartilhado
{: #step-12-order-configure-and-attach-shared-storage}

Antes de continuar, é fundamental pedir, configurar e anexar o armazenamento compartilhado para uso com os clusters de gerenciamento e de capacidade e os hosts dentro do ambiente. Se você desejar usar a solução de armazenamento compartilhado de diversos locatários (File Storage) do {{site.data.keyword.cloud_notm}}, consulte o [Guia de arquitetura para o IBM File Storage for {{site.data.keyword.cloud_notm}} com o VMware](/docs/infrastructure/FileStorage?topic=FileStorage-architectureguide).

## Etapa 13 Ativando o HA/DRS e o svMotion vCVA
{: #step-13-enabling-ha-drs-and-svmotion-vcva}

***Ativando o HA/DRS nos clusters de gerenciamento e capacidade***

Agora que o armazenamento compartilhado está configurado, é necessário ativar o HA e o DRS para fornecer proteção extra e recursos de balanceamento de carga para as VMs no cluster de gerenciamento.

1. Acesse o vSphere Web Client.
* Selecione Gerenciar, Configurações para o cluster de gerenciamento.
* Selecione o DRS do vSphere e clique em Editar. Assegure-se de que as configurações a seguir estejam selecionadas: **Ligar DRS do vSphere**, Nível de automação - **Totalmente automatizado**, Régua de controle de limite de migração configurada para o meio, automação de máquina virtual - **Ativar níveis de automação de máquina virtual individual.**, Gerenciamento de energia - **Desligar**.  
* Na tela Configurações de HA do vSphere, assegure-se de que as configurações a seguir estejam selecionadas: **Ligar HA do vSphere**, **Monitoramento de host**, prioridade de reinicialização da VM - **Médio**, Resposta de isolamento de host - **Deixar ligado**.  
***Storage vMotion, o vCenter Virtual Appliance***

Agora que o armazenamento está configurado no cluster de gerenciamento e HA e DRS estão ativados, é necessário configurar o vCenter Virtual Appliance para o armazenamento compartilhado.

1. Clique com o botão direito no dispositivo apropriado e selecione **Migrar** no menu pop-up.
2. Selecione **Mudar armazenamento de dados** e clique em **Avançar**.
3. Selecione o volume iSCSI que você montou anteriormente para o cluster de gerenciamento e clique em **Avançar**.
4. Revise as seleções e clique em **Avançar**.

O ambiente VMware avançado de site único está completo.

## Sumarização
{: #summary}

Agora você tem um ambiente VMware que está em execução em um data center do IBM Cloud. O seu ambiente VMware pode executar cargas de trabalho de produção e complementar uma implementação no IBM Cloud no local. O ambiente decreta as melhores práticas do VMware e ativa recursos como VMware DRS, HA, Storage DRS e redundância de rede. É possível estender essa implementação de arquitetura de referência com maior capacidade ou hosts de gerenciamento e mais armazenamento.

Para obter mais informações sobre o VMware, consulte [Implementar o VMware](/docs/infrastructure/vmware?topic=VMware-using-cookbooks-for-vmware-deployments) e [Perguntas frequentes do VMware](/docs/infrastructure/vmware?topic=VMware-faqs-vmware)

## Apêndice A: Planilha de VLAN
{: #appendix-a-vlan-worksheet}

|Tipo de VLAN|Número da VLAN|Intervalo de IP|Gateway|Propósito|
|---|---|---|---|---|
|Privado primário||||Gerenciamento|
|Privado primário||||Armazenamento|
|Privado primário||||Máquinas virtuais|
|Público primário||||Acesso público|
|Privado móvel||||MVs de gerenciamento|
|Privado móvel||||Armazenamento|
|Privado móvel||||Máquinas virtuais|
|vMotion|||N/A||
|Tolerância a falhas|||N/A|||
<caption>Planilha de VLAN</caption>
