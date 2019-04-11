---
copyright:
  years: 2014, 2018
lastupdated: "2018-02-14"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Introdução ao VMware ESXi
{: #getting-started-with-vmware-esxi}

Após o fornecimento do servidor ESX, a primeira etapa para acessar o servidor é se conectar à VPN de rede privada do {{site.data.keyword.cloud}}. Por padrão, todos os servidores VMware implementados pelo {{site.data.keyword.cloud_notm}} têm apenas a interface de rede privada ou a ligação configurada com um endereço IP. Essa implementação é uma medida de segurança adicional que é colocada em vigor para limitar a exposição do seu novo servidor ESX à Internet pública. Como resultado dessa segurança acrescida, o servidor atende somente em um endereço IP 10.x.x.x privado do {{site.data.keyword.cloud_notm}}, que é acessível apenas por meio da VPN do {{site.data.keyword.cloud_notm}} ou de outro de seus hosts {{site.data.keyword.cloud_notm}} existentes com acesso à mesma VLAN privada na qual seu novo VMware Server reside.

Para obter mais informações sobre a VPN do {{site.data.keyword.cloud_notm}}, consulte [Introdução ao Virtual Private Networking (VPN)](/docs/infrastructure/iaas-vpn?topic=VPN-getting-started-with-virtual-private-networking-vpn-).

## Acessando seu host ESX
{: #accessing-your-esx-host}

Depois de se conectar à VPN do {{site.data.keyword.cloud_notm}}, é possível se comunicar com o novo VMware Server. O ESX é gerenciado por um cliente vSphere. É possível recuperar por meio de seu novo VMware Server inserindo o endereço IP de rede privada em um navegador da web e clicando no link "Fazer download do cliente vSphere" na página resultante.

Após o cliente do vSphere ser transferido por download e você instalá-lo em sua estação de trabalho local, é possível iniciar o aplicativo e inserir o endereço privado do servidor e as credenciais de login. Suas credenciais de login são visualizadas na página Dispositivos no {{site.data.keyword.slportal_full}} após clicar na guia Senhas.

1. Insira o endereço IP privado do servidor, o usuário raiz e a senha nos campos apropriados do cliente vSphere e clique em **Login** para conectar.
2. Agora que você está se conectado ao servidor, acesse seu novo host ESX selecionando **Inventário**
3. Seu host ESX é mostrado como um nó disponível para configuração na página resultante. A partir daqui, você tem várias maneiras de implementar uma máquina virtual. Um método é fazer upload de um ISO de instalação de seu sistema operacional preferencial para o armazenamento de dados local do servidor. Depois que o ISO é transferido por upload, é possível selecioná-lo como o meio de instalação quando uma VM é criada.  

## Fazendo upload de um ISO
{: #uploading-an-iso}

Conclua as etapas a seguir para fazer upload de um ISO para o armazenamento de dados do servidor.

1. Realce o servidor (representado por um ícone do servidor e seu endereço IP privado) na área de janela esquerda e selecione a guia **Configuração** na área de janela direita.
2. Selecione **Hardware > Armazenamento**. Certifique-se de que a **Visualização** esteja configurada como **Armazenamentos de dados**.
3. Clique com o botão direito no armazenamento de dados apropriado e clique em **procurar armazenamento de dados**.
4. A página resultante tem um gerenciador de arquivos para procurar, fazer upload/download de arquivos para e de o armazenamento de dados.  
  * Para fazer upload de um ISO, clique no ícone do volume (com a seta apontando para cima) e selecione **Fazer upload do arquivo**.
5. Selecione o arquivo ISO em seu sistema de arquivos local do qual você deseja fazer upload para o armazenamento de dados e clique em **Abrir**.
6. O ISO de sua escolha está agora no armazenamento de dados.
7. Agora que sua mídia de instalação está no servidor VMWare, é possível continuar com a [criação de sua máquina virtual](/docs/infrastructure/vmware?topic=VMware-creating-a-vmware-esx-virtual-machine).
