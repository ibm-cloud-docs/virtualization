---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-13"

subcollection: virtualization

keywords: Citrix XenServer 
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Ativando o XenServer
{: #activating-xenserver}

A licença grátis do Citrix XenServer é licenciada por 30 dias a partir da instalação ou recarregamento inicial. Durante esse tempo, é necessário registrar-se com a Citrix e solicitar uma licença totalmente funcional de 1 ano. Para criar essa licença, a Citrix requer informações pessoais às quais o {{site.data.keyword.BluSoftlayer}} não tem acesso. A licença é um contrato entre você e a Citrix para usar o XenServer.
{:shortdesc}

Se você não registrar seu sistema em 30 dias, não será possível ligar nenhuma máquina virtual.
{:tip}

Para registrar seu sistema, conclua as etapas a seguir:

1. Faça download e instale o cliente XenCenter. É possível acessar o cliente por meio da página [Citrix XenCenter ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://community.citrix.com/display/xs/XenCenter){: new_window}.

2. Conecte-se à sua rede privada por meio da VPN.

3. Inicie o XenCenter e clique em **Incluir novo servidor** ou clique em **Servidor > Incluir...** no menu suspenso.

4. Use o endereço IP privado para o **Nome do host**, 'raiz ' para o **Nome do usuário** e sua senha raiz para **Senha**.

5. Depois que o sistema se conectar, uma caixa de diálogo aparecerá. Essa caixa de diálogo alerta que sua licença expira em 30 dias. Se você não vir uma caixa de diálogo, acesse **Ferramentas > Gerenciador de licença...** no menu suspenso.

6. Verifique seu servidor e clique em **Ativar...**. Um navegador se abre e acessa o site de registro da Citrix. Preencha o formulário e envie. Verifique sua caixa de entrada para obter um e-mail da Citrix. Esse e-mail tem sua chave de licença como anexo.

7. Depois de receber o e-mail, salve o arquivo da chave de licença anexado localmente.

8. Na área de janela esquerda do XenCenter, destaque o seu servidor. Na barra de menus, acesse **Servidor > Instalar chave de licença...**. Uma caixa de diálogo solicitando a localização da chave de licença é exibida. Aponte-a para a localização salva na Etapa 7.
