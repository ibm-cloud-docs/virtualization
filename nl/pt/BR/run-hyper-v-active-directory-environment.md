---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-10"

subcollection: virtualization

keywords: hypervisor
---
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:table: .aria-labeledby="caption"}

# Executando Hyper-V em um ambiente de diretório ativo
{: #running-hyper-v-in-an-active-directory-environment}

<!--Running Hyper-V in an Active Directory environment is the best implementation of Hyper-V. Microsoft has truly shined with the ability to remotely manage a server.You can remotely manage the Hyper-V server allows for it to be installed on a Core installation of Windows freeing up those valuable resources from the system for use within the VM’s. When this is combined with an Active Directory Domain Controller
You manage all your Hyper-V servers from a single Hyper-V Manager that runs on any 2008 or Vista computer that is connected to the Domain.--> <!--Vista?? MS doesn't support Vista.-->

## Antes de iniciar
{: #before-you-begin-running-hyper-v-in-an-active-directory-environment}

Antes de iniciar a execução do Hyper-V em um ambiente de diretório ativo, é necessário o seguinte:

* Servidor de data center do Windows 2008 de 64 bits que executa o Hyper-V (instalação integral ou principal do S.O.).
* Um grupo global no domínio que é usado para gerenciar o Hyper-V.
* Acesso de administrador de domínio para ativar as mudanças no servidor Hyper-V do computador do qual você está trabalhando.

## Configurando o servidor Hyper-V
{: #configuring-the-hyper-v-server}

1. Abra uma conexão de gerenciamento com o servidor Hyper-V.

2. Inclua o grupo **Hyper-V** no grupo **Usuários de COM distribuídos**.

3. Inclua o grupo **Hyper-V** nos namespaces **CIMV2 e Virtualization**.

4. Inclua o grupo **Hyper-V** no armazenamento de autorização para Hyper-V no servidor Hyper-V.

5. Forneça as permissões de grupo **Hyper-V** para o Hyper-V Directory no servidor Hyper-V.

## Configurando a conexão de gerenciamento remoto
{: #setting-up-remote-management-connection}

Certifique-se de ter efetuado login em um computador no domínio com privilégios de administrador de domínio.

1. Acesse **Painel de controle > Ferramentas administrativas > Gerenciamento de computador**.

2. No menu Ação, selecione **Conectar a outro computador**.

3. Insira o nome ou IP do servidor e clique em **OK**.

## Incluindo o grupo Usuários do COM distribuídos no servidor Hyper-V
{: #adding-distributed-com-users-group-to-the-hyper-v-server}

Primeiro, é necessário incluir o grupo Hyper-V no grupo Usuários do COM distribuídos no servidor Hyper-V.

1. Acesse **Ferramentas do sistema > Usuários e grupos locais > Grupos > Usuários do COM distribuídos > Incluir no grupo**.

2. Clique em **Incluir** e insira o nome do grupo para o grupo Hyper-V e clique em **OK**.

## Concedendo acesso remoto ao servidor para os namespaces CIMV2 e Virtualization
{: #granting-remote-access-to-the-server-for-cimv2-and-virtualization-namespaces}

É possível atualizar as permissões para acesso remoto para o servidor para Virtualization e CIMV2.

1. Na janela Gerenciamento do computador, selecione **Serviço e aplicativos > Controle do WMI**.

2. Clique com o botão direito e acesse **Propriedades > Segurança > Raiz > CIMV2 > Segurança**.

3. Inclua o grupo Hyper-V, em seguida, selecione-o e clique em **Avançado**.

4. Certifique-se de que o novo grupo esteja selecionado e clique em **Editar**.

5. Mude **Aplicar para:** para **O namespace e todos os subnamespaces**.

6. Para **Ativar conta e ativação remota**, certifique-se de que **Permitir** esteja selecionado.

7. Selecione para **Aplicar essas permissões a objetos e/ou contêineres somente dentro desse contêiner** e clique em **OK**.

8. Repita essas etapas para Virtualization.

## Atualizando o armazenamento de autorização
{: #updating-authorization-store}

O armazenamento de autorização para Hyper-V é o componente final que fornece o grupo de domínio para o Hyper-V.

1. Abra o gerenciador de autorização executando o comando `azman.msc` por meio do menu de execução ou de um prompt de comandos.

2. No menu Ação, selecione **Abrir armazenamento de autorização**.

3. Certifique-se de que **XML** esteja selecionado. Agora, é necessário acessar remotamente o `InitalStore.xml` no servidor Hyper-V. Use o caminho a seguir:

`\HOSTNAME\c$\ProgramData\Microsoft\Windows\Hyper-V\InitialStore.xml`

4. Acesse **Serviços do Hyper-V > Designações de função > Administrador**.

5. No **Menu Ação**, selecione **Designar usuários e grupos >Do Windows e do Active Directory**.

6. Inclua o grupo Hyper-V.

## Concedendo permissões de pasta
{: #granting-folder-permissions}

Agora que o grupo Hyper-V tem permissões completas para gerenciar o Hyper-V remotamente, é necessário fornecer permissões para gravar na pasta `C:\Users\Public\Documents\Hyper-V`.

1. Abra Meu computador e acesse o endereço a seguir:

`\HOSTNAME\c$\Users\Public\Documents`

2. Acesse **Hyper-V > Propriedades > Segurança**

3. Inclua o grupo Hyper-V e certifique-se de que ele tenha a capacidade de ler, gravar e executar arquivos por meio desse diretório. Em geral, é mais fácil designar **Controle total**.

## Finalizando a configuração
{: #finalizing-configuration}

Todas as mudanças na configuração estão concluídas. Para finalizar a configuração, é necessário reiniciar o servidor Hyper-V. Depois que o servidor estiver de volta on-line, conecte-se a ele por meio de seu gerenciador de Hyper-V local. Agora você tem acesso total para gerenciar todas as VMs e o serviço Hyper-V.
