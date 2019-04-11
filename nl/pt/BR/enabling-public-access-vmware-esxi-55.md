---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization

keywords: ESXi, VMware
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Ativando o acesso público ao VMware ESXi
{: #enabling-public-access-to-vmware-esxi}

Por padrão, o host ESXi é instalado com o console de serviço apenas na rede privada. Dessa forma, o tráfego público para e da rede pública é impedido no host ESXi.

Para iniciar, é necessário conectar-se ao servidor usando um VMware vSphere Client na interface privada para o servidor.

É necessário ter as informações do IP público 'primário' do servidor para concluir a configuração. As informações referentes ao IP público do servidor podem ser localizadas no [{{site.data.keyword.slportal_full}} ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://control.softlayer.com/){: new_window}.
{:tip}

Depois de efetuar login no host ESXi, siga estas etapas para ativar a interface pública:

1. Talvez seja necessário efetuar login no servidor por meio de SSH usando o IP privado para corrigir o roteamento.
* Execute `esxcfg-route -l` para determinar as rotas atuais
* Execute `esxcfg-route -a 10.0.0.0/8 [seu IP do gateway privador do servidor]`
* Execute `esxcfg-route [seu gateway público do servidor]` para assegurar que o gateway público seja o padrão

Efetue login no vSphere e siga estas etapas:

1. Clique na guia Configuração e clique em **Rede**.
2. Para vSwitch1, clique em **Propriedades >Incluir**
3. Escolha **Kernel da VM** e clique em **Avançar**
* As configurações podem ser deixadas como estão. É possível rotular novamente a rede para "VMKernelPublic" para propósitos de identificação.
4. Insira as informações de IP público do servidor.
5. Para o gateway padrão do kernel da VM, clique em **Editar**, insira o IP de gateway público e clique em **OK**.
* Clique em **OK**. **Nota:** uma desconexão é normal e esperada.*

Agora é possível acessar o servidor usando os endereços IP público e privado.

A ativação do acesso de IP público ao ESXi contém riscos de segurança inerentes. Certifique-se de que você tenha as etapas apropriadas para restringir o acesso ao ESXi para apenas os usuários que precisam dele.
{:tip}
