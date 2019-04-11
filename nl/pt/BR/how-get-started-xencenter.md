---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-24"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Introdução ao XenCenter
{: #getting-started-with-xencenter}

## Antes de iniciar
{: #before-you-begin-xencenter}

Antes de iniciar, revise os pré-requisitos a seguir:

- Novos intervalos de endereços IP móveis (públicos e privados) que são roteados como "Secundários na VLAN". Essa solução de exemplo para a configuração de uma nova VM requer que você tenha IPs utilizáveis disponíveis na rede privada. É possível pedir sub-redes por meio do [{{site.data.keyword.slportal_full}} ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://control.softlayer.com/network/subnets/order).
- Capacidade de conexão com a rede privada do {{site.data.keyword.cloud}} por meio da VPN. Para obter mais informações sobre o acesso à VPN, consulte [Ativar acesso à rede privada de infraestrutura do {{site.data.keyword.cloud_notm}} ](/docs/customer-portal?topic=customer-portal-getting-started#enable-private-network).
- O Citrix XenCenter está instalado em seu sistema local. <!-- . http://downloads.service.softlayer.com/citrix/xen/-->

**Nota:** seu servidor é pré-configurado com vários modelos para ajudá-lo a iniciar rapidamente com o XenServer.

## Criando uma máquina virtual com o XenCenter
{: #creating-a-virtual-machine-with-xencenter}

Use as etapas a seguir para ajudar a criar uma máquina virtual com o XenCenter.

1. Certifique-se de que você esteja conectado à rede privada do {{site.data.keyword.cloud_notm}} por meio da VPN. Estabeleça uma conexão por meio de VPN SSL ou PPTP.
2. Abra o XenCenter e clique em **"Incluir seu XenServer"**.
3. Insira o nome do host do servidor, o nome do usuário e a senha. É necessário usar o endereço IP privado do servidor (é semelhante a 10.x.x.x), o nome do usuário `root` e, em seguida, a senha raiz do servidor. Essas informações estão disponíveis no {{site.data.keyword.slportal}} acessando os seus [Dispositivos ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://control.softlayer.com/devices){: new_window} e, em seguida, clicando no nome do XenServer. Clique em  ** Conectar **.
4. Se você receber uma tela de ativação de licença grátis, preencha estas informações agora.
5. Clique com o botão direito no nome do {{site.data.keyword.cloud_notm}} e selecione **Nova VM...**.<!--You can now create your first Virtual Machine. Create a CentOS virtual machine with a disk of 10 GB and have both Public and Private Networks functioning-->
6. Selecione o sistema operacional que você deseja usar para a nova máquina virtual e, em seguida, clique em **Avançar**. **Nota:** alguns modelos requerem que você forneça sua própria mídia.<!--Because you are using CentOS, you can use {{site.data.keyword.BluSoftlayer_notm}} private mirrors for CentOS to get our installation going.Select a version of CentOS and then click **Next**.-->
7. Insira um nome apropriado e uma descrição para a nova máquina virtual.
8. Insira a localização da mídia de instalação do sistema operacional guest e clique em **Avançar**. <!-- In the example, {{site.data.keyword.BluSoftlayer_notm}} a CentOS mirror is used as installation media. Provide the Install URL of: http://mirrors.service.softlayer.com/centos/5/os/x86_64 and click **Next**.
  *A trailing ‘/’ at the end of the URL can sometimes break the installation.*
  *This mirror is available only on the {{site.data.keyword.BluSoftlayer_notm}} Private Network. The full mirror's contents are  available here: http://mirrors.service.softlayer.com/.-->
9. Selecione o número de CPUs e a quantia de alocação de memória. (Você está construindo um sistema aperfeiçoado e não precisa de muito RAM. Então, 512 MB é mais do que suficiente.). Clique em **Avançar**.
10. Selecione seus discos virtuais para alocar espaço em disco para sua VM.<!--Remember that this is like adding hard disks, it is not like partitioning your system. Partitioning is done during the installation of the OS.--> O tamanho padrão é 8 GB para esse modelo. Essa quantia é mais do que suficiente para o servidor que você está construindo. *Opcional: é possível expandir o disco para atender às suas necessidades, destacando o disco e clicando em **Editar...**.* Clique em **Avançar**.
11. Inclua ou remova interfaces de rede virtuais para a máquina virtual. Os padrões são aceitáveis, a menos que você não queira que seu sistema seja capaz de se comunicar na rede privada. O exemplo requer que você deixe ambas as interfaces no sistema, mas um Administrador Xen experiente pode remover uma delas para seus próprios propósitos. Nesse caso, será possível destacar e excluir uma interface, se desejar. Clique em **Avançar**.
12. A configuração está concluída. Deixe "Iniciar VM automaticamente" selecionado e clique em **Concluir**. A instalação é iniciada. Você retorna para a tela principal. É possível ver a nova VM listada à esquerda.
13. Selecione sua nova VM e clique na guia **Console**. <!--You can now see that your system is booted into the CentOS installer awaiting your input.-->
14. <!--All of the parameters of a CentOS installation are outside of the scope of this article and will need to be customized by your System Administrator, but this article will provide some specific pieces of information that you need to complete the installation. Select your language to get started. The CentOS installer will then ask you for assistance in configuring the Networking Devices in the system.--> Selecione **Ethernet virtual eth0 - xen** e clique em **OK**.
  <!--![14](images/14.png)-->
15. <!--In the pre-requisite notes, we made sure that we already had a set of Portable IP Addresses routed as "Secondary on VLAN" ready for this installation.--> Certifique-se de que você tenha as informações prontas para configurar o TCP/IP. É necessário configurar manualmente a interface com o suporte IPV4 e desativar o suporte ao IPV6.
  <!--[15](images/15.png)-->
16. Confirme que você está ciente de como usar a sub-rede de IP privada (10.17.37.240/29) e clique em **OK**<!-- to go to the CentOS installer-->. Você e o administrador do sistema podem instalar o sistema operacional guest de acordo com suas próprias diretrizes. Para obter mais informações sobre blocos de IP estáticos e móveis, consulte [Introdução a sub-redes e IPs](/docs/infrastructure/subnets?topic=subnets-getting-started-with-subnets-and-ips).

Você criou uma nova VM.
