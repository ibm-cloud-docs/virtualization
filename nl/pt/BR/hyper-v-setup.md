---



copyright:
  years: 2014, 2018
lastupdated: "2018-08-16"

subcollection: virtualization


---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Configurando o Hyper-V
{: #setting-up-hyper-v}

O processo de configuração do Hyper-V inclui a configuração inicial do Hyper-V, a criação de uma máquina virtual e a configuração de rede na nova máquina virtual. As diretrizes a seguir explicam esses processos.

## Usando o Hyper-V Manager
{: #using-hyper-v-manager}

O Hyper-V Manager é o console de gerenciamento que você usa para operar o Hyper-V. Por meio do Hyper-V Manager, é possível criar, iniciar, parar, excluir e configurar todas as máquinas virtuais. O console de gerenciamento também é o local em que você configura a localização padrão para armazenar seus discos rígidos virtuais e a localização padrão para armazenar seus arquivos de configuração de máquina virtual.

É possível localizar o Hyper-V Manager em **Ferramentas administrativas** no Painel de Controle do Windows. Em uma nova instalação do Windows 2008, é possível localizar **Ferramentas administrativas** em Programas no menu Iniciar. Depois de abrir o programa Hyper-V Manager, uma nova janela aparece na tela. Pare um momento para familiarizar-se com essa tela. Todas as configurações e o uso de suas máquinas virtuais são feitos aqui.

## Configurando o Hyper-V
{: #configuring-hyper-v}

A primeira configuração a ser verificada é a localização de armazenamento para os seus discos rígidos virtuais. Na janela do Hyper-V Manager, no lado esquerdo, aparecem **Hyper-V Manager** e o nome do servidor. Clique no nome do servidor para ver as informações que estão disponíveis para o serviço Hyper-V em seu servidor. Agora, o meio da tela está vazio, pois mostra que nenhuma máquina virtual está instalada. No lado direito da tela, há um link para **Configurações do Hyper-V**. Ao clicar no link, as configurações básicas para o Hyper-V são exibidas.

A configuração primária que você precisa examinar é **Discos rígidos virtuais**. No lado direito está a localização padrão que armazena os discos rígidos virtuais. O local padrão é:

`C:\Users\Public\Documents\Hyper-V\Virtual Hard Disks`

Ao criar uma máquina virtual, é possível definir um local específico se você não desejar armazenar esse disco rígido virtual específico na localização padrão. Por exemplo, você deseja armazenar seus discos rígidos virtuais em uma segunda unidade maior (unidade D, neste exemplo) e você deseja que essas unidades virtuais sejam armazenadas na pasta **VirtualMachine**, insira `D: \VirtualMachine` no campo.

****

Também é possível pesquisar manualmente as pastas para selecionar a localização que você deseja usar. Depois de selecionar uma localização, clique em **OK**.

## Configurando dispositivos de rede
{: #configuring-network-devices}

Antes de configurar dispositivos de rede virtuais, determine quais adaptadores de rede estão conectados às redes privada e pública. No menu Iniciar, clique em **Conexões de rede**. Há dois dispositivos de rede aqui. Certifique-se de observar que seus dispositivos são denominados **PrivateNetwork** e **PublicNetwork**. Clique com o botão direito em **Rede privada** e selecione **Propriedades**. **Conectar usando:** mostra o nome do dispositivo para o adaptador de rede privada. Anote esse nome, pois precisará dele quando criar seu dispositivo de rede privada virtual.

### Configurando um dispositivo de rede privada
{: #configuring-a-private-network-device}

Agora que a localização padrão para armazenar seus discos rígidos virtuais está configurada, é necessário configurar os dispositivos de rede virtual que as máquinas virtuais usam. Para abrir o Virtual Network Manager, clique em **Virtual Network Manager** no lado direito da janela do Hyper-V Manager que está rotulada.

Ao abrir essa janela pela primeira, você verá Redes virtuais à esquerda com a única opção listada, **Nova rede virtual**. No lado direito, você tem três opções:
* Externo
* Interna
* Privada.
Certifique-se de que **Externa** esteja selecionado e clique em **Incluir**.

Continue inserindo informações nos campos restantes:
* **Nome** o nome que está associado a esse dispositivo de rede. Para este exemplo, use _Privada_ para indicar que esse dispositivo está conectado à rede privada do IBM Cloud.
* **Tipo de conexão** Selecione o dispositivo que corresponde à sua rede privada. Esse nome é o nome do dispositivo que você localizou anteriormente na janela **Conexões de rede**. Depois de selecionar o dispositivo correto, clique em **OK**. Um aviso de que a sua conexão de rede pode ser perdida é exibido. Clique em **Sim** para continuar.

### Configurando um dispositivo de rede pública
{: #configuring-a-public-network-device}

Agora, o dispositivo de rede privada está instalado. Repita as etapas anteriores para a rede pública. Para este exemplo, use o nome **Pública** para o nome do dispositivo. Sob o tipo de conexão, selecione o outro dispositivo de rede que não foi usado na configuração de rede privada. Depois de criar os dispositivos de rede pública e privada, clique em **OK**.

## Obtendo a mídia de instalação
{: #obtaining-installation-media}

Agora que os dispositivos de rede virtual estão criados, é necessário obter a mídia de instalação. A mídia de instalação que é usada neste exemplo é um arquivo de imagem de CD/DVD. O IBM Cloud suporta os sistemas operacionais a seguir para o Hyper-V:
* Windows 2008
* Windows 2003
* CentOS
* Fedora
* Ubuntu.
**Nota:** se você já tiver a mídia de instalação, acesse **Criando uma máquina virtual**.

A mídia de instalação para o [CentOS ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://centos.org){: new_window}, [Fedora ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://fedoraproject.org/){: new_window} e [Ubuntu ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://www.ubuntu.com/){: new_window} estão disponíveis por meio de seus respectivos websites. Todos os três S.O. são licenciados gratuitamente e nenhuma ação adicional precisa ser executada pelo usuário.

## Criando uma máquina virtual
{: #creating-a-virtual-machine}

Agora que você tem a mídia de instalação, é possível criar uma máquina virtual por meio do Hyper-V Manager. Clique em **Nova > Máquina virtual** para iniciar o **Assistente de nova máquina virtual**.

Na primeira tela, clique em **Avançar** e insira um nome para a nova máquina virtual. Esse nome pode ser mudado em uma data futura. É possível mudar a localização do disco rígido virtual. O valor padrão que você configurou anteriormente é usado, a menos que você especifique uma localização diferente. Depois de inserir as informações, clique em **Avançar**.

Agora, é necessário alocar memória para o servidor. Geralmente, você fornece pelo menos a memória mínima recomendada, conforme especificado pelo sistema operacional. Depois de inserir a quantia de memória a ser alocada, clique em **Avançar**.

Em seguida, configure o primeiro adaptador de rede. Se um sistema operacional Linux estiver sendo instalado na máquina virtual, selecione **Não conectado**. Se o Windows estiver sendo instalado, selecione **Privado** e, em seguida, clique em **Avançar**.

É possível mudar as configurações a seguir para o disco virtual: o nome do arquivo, a localização de armazenamento e o tamanho. Faça qualquer mudança aplicável e clique em **Avançar**.

A mídia de instalação precisa ser configurada. Selecione **Instalar um sistema operacional por meio de um CD/DVD-ROM de inicialização > Arquivo de imagem (.iso)** para configurar a localização do arquivo de imagem do CD/DVD para a mídia de instalação. Clique em **Avançar**, visualize o resumo e clique em **Concluir** para fechar a janela.

Por último, é necessário criar os adaptadores de rede virtual. Agora, a nova máquina virtual está listada sob a seção **Máquinas virtuais** do Hyper-v Manager. Clique com o botão direito na nova máquina virtual e clique em **Configurações**.

### Configurando o adaptador de rede virtual para o sistema operacional Windows
{: #configuring-virtual-network-adapter-for-windows-operating-system}

Agora, uma lista de hardware está disponível para a máquina virtual. Na lista estão **Adaptador de rede** e **Privada**. Se você vir **Não conectado**, clique em Adaptador de rede, selecione **Privada** e clique em **Aplicar**.

Em seguida, clique em **Incluir hardware > Adaptador de rede > Incluir** para incluir o adaptador de rede no hardware.

Na área de janela direita, selecione **Público** na lista suspensa e clique em **OK**.

### Configurando o adaptador de rede virtual para o sistema operacional Linux
{: #configuring-virtual-network-adapter-for-linux-operating-system}

Agora, uma lista de hardware está disponível para a máquina virtual. Clique em **Adaptador de rede >Remover** e selecione **Incluir hardware**. A lista à direta muda.
Na lista, selecione **Adaptador de rede anterior**. **Nota:** essa opção é diferente do adaptador de rede que não é usado em uma máquina virtual Linux.
Clique em **Incluir** para incluir esse adaptador de rede anterior na lista de hardware. O novo adaptador de rede anterior é selecionado automaticamente.
Selecione **Privado** na lista suspensa e clique em **Aplicar**.
Repita as etapas anteriores para incluir o **Adaptador de rede anterior**. No entanto, selecione **Pública**.
Depois que a nova rede anterior for incluída, clique em **OK**.

## Instalando um sistema operacional guest
{: #installing-a-guest-operating-system}

Agora, a máquina virtual está pronta para ser iniciada. Clique com o botão direito na máquina virtual e selecione **Conectar**. O console da máquina virtual é aberto. No menu **Ação**, selecione **Iniciar**. A nova máquina virtual é iniciada usando a mídia de instalação que você selecionou. Agora, você executa a instalação do sistema operacional. Durante o processo de instalação do S.O., é recomendado que você defina as configurações de rede pública. A rede pública é a segunda interface de rede. Quando a instalação do S.O. for concluída, você terá uma máquina virtual funcional. Se uma rede pública estiver configurada, será possível acessar a máquina virtual remotamente.

A etapa final no processo de instalação é configurar a rede privada. Se a máquina virtual não estiver conectada à rede privada, o processo de instalação será concluído. Para obter mais informações sobre como configurar a rede privada, consulte [Configurando uma rede de máquina virtual](/docs/infrastructure/virtualization?topic=Virtualization-setting-up-a-virtual-machine-network).
