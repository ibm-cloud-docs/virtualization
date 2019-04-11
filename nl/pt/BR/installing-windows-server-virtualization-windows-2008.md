---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-23"

subcollection: virtualization

keywords: Windows 2008
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Instalando o Windows Server Virtualization no Windows 2008
{: #installing-windows-server-virtualization-on-windows-2008}

<!--Windows 2008 64-bit edition comes with the option to install Windows next generation virtualization application codenamed Veridian. By default the application will not be a selectable option when Adding and Removing Roles from Windows 2008.

Note: At this time, this is only available in full installation of 64-bit editions of Windows 2008. 32-bit versions and Server-core installations do not support this.-->

## Antes de iniciar
{: #before-you-begin-win-2008}

Antes de iniciar, revise os pré-requisitos a seguir.

### Hardware
{: #hardware}

* Tecnologia de virtualização assistida por hardware ativada no BIOS
* Intel VT
* AMD-V
* Data Execution Prevention ativada no BIOS
* Intel Execute Disable (XD)
* AMD No Execute (NX)

### Software
{: #software}

Para ativar a opção **Função** para a virtualização do Windows Server, algumas correções devem ser instaladas.
1. Abra uma janela do navegador e navegue para %sysdir%\Windows\wsv, geralmente C:\Windows\wsv. Dois arquivos estarão localizados nessa pasta:
    * Windows6.0-KB939853-x64.msu
    * Windows6.0-KB939854-x64.msu
2. Essas correções podem ser instaladas em qualquer ordem. Instale ambas as correções e, em seguida, reinicie o sistema.

## Instalação
{: #installation}

1. Depois que o sistema for reiniciado, será necessário incluir a **Função** no sistema.
2. Depois de incluir a função, a caixa de diálogo **Criar redes virtuais** será exibida.
**Nota:** a conectividade de rede é perdida brevemente.
3. Selecione **Conexão de área local**, seu adaptador de rede privada. Clique em **Continuar**. A instalação continua e requer que você reinicie.
4. Após a reinicialização dos sistemas, efetue login no sistema por meio da conexão pública. **Nota:** deve-se efetuar login com a mesma credencial de login que você usou para instalar essa conexão. Você perderá a conectividade de rede para a interface. Se não, você poderá receber o erro a seguir:
    * *A tentativa de configurar o Windows Server Virtualization falhou com o código de erro 0x80078000.*
Para resolver o erro, acesse **Iniciar > Programas > Ferramentas administrativas > Gerenciamento de virtualização do Windows** no console de gerenciamento para a virtualização do Windows.
5. Na área de janela direita, clique no servidor apropriado.
6. Em seguida, na área de janela de ação, clique em **Gerenciador de rede virtual**. **Gerenciamento do comutador de rede virtual** é exibido.
7. Na área de janela esquerda, clique no comutador de rede sob o comutador de rede **Incluir novo**.
8. Renomeie o comutador de rede para privado.
9. Selecione Adaptador de rede física para **Conexão** e selecione o primeiro adaptador de rede. Agora, todos os protocolos de rede estão desvinculados da interface de rede privada.
10. **IMPORTANTE** para restabelecer a conectividade de rede para privada, é necessário configurar o novo dispositivo de comutador, **NÃO** a interface privada. Acesse **Iniciar > Configurações > Conexões de rede**. Um novo dispositivo chamado **Conexão de área local 2** é exibido.
11. Clique com o botão direito nessa conexão e acesse **Propriedades**.
12. Selecione **ipv4** e suas propriedades. É necessário configurar esse dispositivo com o endereço IP da interface de rede privada, a máscara de rede e os servidores DNS.
13. Clique em **OK > Fechar**. Essa configuração reativa a rede no lado privado. É possível verificar se a rede é privada ao fazer ping do IP privado.

RDP para o IP privado para configurar a rede pública.

### Incluindo um comutador público
{: #adding-a-public-switch}

A inclusão de um comutador público segue o mesmo processo que a inclusão do comutador privado.
1. Volte para **Gerenciamento do comutador de rede virtual** e selecione **Incluir novo comutador de rede**.
2. Selecione **Externo** como o tipo de comutador de rede e clique em **Incluir**.
3. Renomeie o comutador para **Público** e selecione **Adaptador de rede física**.
4. Selecione o segundo adaptador de rede e clique em **OK**. Essa configuração faz com que a porta pública não responda à rede. Configure a nova interface de comutador público exatamente como você fez com a privada com as configurações apropriadas.
