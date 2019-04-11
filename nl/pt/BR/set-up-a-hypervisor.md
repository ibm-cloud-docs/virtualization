---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Configurando um hypervisor
{: #setting-up-a-hypervisor}

Use as etapas a seguir para configurar um hypervisor.

1. Efetue login no [{{site.data.keyword.slportal_full}} ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](http://control.softlayer.com/){: new_window} usando as suas credenciais exclusivas.
2. No menu **Dispositivos**, selecione **Lista de dispositivos** e localize seu hypervisor.
3. Conecte-se à rede privada por meio da VPN segura do [ ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](http://www.softlayer.com/vpn-access){: new_window} para acessar o seu hypervisor.

**Nota:** os provedores de hypervisor do {{site.data.keyword.cloud}} incluem XenServer, VMware e Hyper-V. Cada provedor tem consoles de gerenciamento exclusivos que são acessados de forma diferente. Para obter mais informações sobre como acessar e trabalhar em um console de gerenciamento, consulte os links a seguir:

   * [XenServer ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://support.citrix.com/en/products/xenserver){: new_window}
   * [VMware ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://www.vmware.com/support/vsphere-hypervisor.html){: new_window}
   * [Hyper-V ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](http://technet.microsoft.com/en-us/windowsserver/dd448604){: new_window}

4. Obtenha IPs móveis para as suas máquinas virtuais.
    * As VMs requerem endereços IP móveis. Blocos de IPs móveis públicos e privados podem ser pedidos por meio do {{site.data.keyword.slportal}}.
    * Para obter mais informações sobre a alocação de endereços IP, consulte [Introdução a sub-redes e IPs](/docs/infrastructure/subnets?topic=subnets-getting-started-with-subnets-and-ips).

5. Estabeleça o roteamento para as VMs na rede privada. As VMs precisam das especificações a seguir para rotear para outras VMs por meio da rede privada:
    * IPs privados móveis
    * Rota estática que está relacionada à faixa de rede 10.0.0.0/8

Para obter mais informações sobre o processo de roteamento da VM, consulte [Configurando uma rede de máquina virtual](/docs/infrastructure/virtualization?topic=Virtualization-setting-up-a-virtual-machine-network). Máquinas que estão na mesma VLAN podem se comunicar após a configuração da rede da VM. [Ative a ampliação de VLAN](/docs/infrastructure/vlans?topic=vlans-vlan-spanning) se as máquinas que estão em VLANs diferentes devem se comunicar.

## Acesse e armazene de forma segura os ISOs
{: #access-and-securely-store-isos}

As VMs na rede do {{site.data.keyword.cloud_notm}} podem executar ISOs pré-configurados ou customizados. As VMs na rede do {{site.data.keyword.cloud_notm}} podem acessar o site de espelhos internos, que está disponível exclusivamente para os usuários do {{site.data.keyword.cloud_notm}} e fornece configurações populares dos sistemas operacionais mais comumente usados. Se você precisar de uma versão especial ou configuração para um sistema operacional específico, consulte o website do fornecedor do S.O.

Se você executar um ISO customizado em sua VM, faça upload de seu ISO para um local seguro para que ele possa ser recuperado se um dispositivo falhar. Muitos usuários escolhem armazenar ISOs customizados em um sistema local do dispositivo usando SSH ou RDP. Alternativamente, o espaço é oferecido por meio de serviços de armazenamento que têm vários recursos.
