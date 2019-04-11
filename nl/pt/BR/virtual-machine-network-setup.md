---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Configurando uma rede de máquina virtual
{: #setting-up-a-virtual-machine-network}

A configuração de rede em sua nova máquina virtual é feita em algumas etapas. É necessário um bloco de IP móvel separado para a rede pública e a privada. Supõe-se que você esteja usando uma oferta de virtualização que requer secundário no domínio de transmissão de VLAN e não roteado para a sub-rede VLAN (sem ID de rede/gateway/transmissão). Se você não planeja usar a rede privada em sua máquina virtual, será necessário apenas a sub-rede pública. Os blocos de IP móveis podem ser pedidos diretamente por meio do portal em [Vendas->Incluir endereços IP ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://manage.softlayer.com/Sales/orderAdditionalServices/subnet){: new_window} ou por meio do [Gerenciador de IP de rede pública ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://manage.softlayer.com/PublicNetwork/ipManager){: new_window}.

Depois de obter os blocos de IP, é possível configurar a rede na máquina virtual. Se a máquina virtual não estiver instalada, será possível configurar o bloco de rede pública por meio do processo de instalação do sistema operacional. Esse processo é o método mais rápido para configurar sua rede pública. Todas as etapas de configuração de rede supõem que a primeira interface de rede seja a rede privada e a segunda interface de rede seja a rede pública.

A configuração de sua rede é diferente para cada S.O. É possível localizar informações detalhadas sobre como configurar sua rede para os sistemas operacionais a seguir. **Nota:** os sistemas operacionais que são agrupados têm a mesma configuração de rede.

* Windows 2008 Server Core
* Windows 2003 Standard e Enterprise
* Windows 2008 Web, Standard, Enterprise e Datacenter
* RedHat, Fedora e CentOS
* Ubuntu e Debian

A configuração de rede requer as informações a seguir para os blocos de IP público e privado. As informações podem ser localizadas no portal para os blocos de IP público em [Rede pública -> Gerenciador de IP ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://manage.softlayer.com/PublicNetwork/ipManager){: new_window} e os blocos de IP privado em [Rede privada -> Gerenciador de IP ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://manage.softlayer.com/PrivateNetwork/ipManager){: new_window}.

    -IP Address
    -Gateway
    -Subnet Mask
    --Subnet conversion
    ---/29 - 255.255.255.248
    ---/28 - 255.255.255.240
    ---/27 - 255.255.255.224
    ---/26 - 255.255.255.192
    ---/25 - 255.255.255.128
    ---/24 - 255.255.255.0

## Windows 2008 Server Core
{: #windows-2008-server-core}

O Windows 2008 Server Core Edition não fornece uma interface gráfica para configurar a rede do sistema. A configuração precisa ser feita manualmente usando o prompt de comandos. É necessário executar os comandos a seguir. Os endereços IP que são usados nesse exemplo precisam ser substituídos pelos endereços IP de seus blocos de IP. Esses comandos funcionam com versões mais antigas do Windows Server que incluem o comando [netsh ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](http://support.microsoft.com/kb/242468){: new_window}.

**Exemplo de bloco de IP público – 192.0.2.0/29**

* Endereço IP - 192.0.2.2
* Gateway - 192.0.2.1
* Máscara de sub-rede - 255.255.255.248

**Exemplo de bloco de IP privado – 10.0.0.0/29**

* Endereço IP - 10.0.0.2
* Gateway - 10.0.0.1
* Máscara de sub-rede - 255.255.255.248

**Rede pública**

Esse comando cria a rede pública para conectar o servidor à Internet.

* *Netsh interface ip set address name=”Local Area Connection 2” static 192.0.2.2 255.255.255.248 192.0.2.1*

Esses comandos criam as entradas DNS. Se você não estiver usando a rede privada, será necessário substituir esses endereços IP por servidores DNS ativos

* *Netsh interface ip add dns “Local Area Connection 2” 10.0.80.11*
* *Netsh interface ip add dns “Local Area Connection 2” 10.0.80.12*

**Rede privada                                            **

Os dois comandos a seguir configuram a rede privada e criam a rota persistente para a rede privada. A rota persistente fornece seu acesso de rede privada para a rede privada inteira. O acesso inclui os servidores DNS na configuração de rede pública. **Nota:** nenhum gateway está designado para a rede privada.

* `Netsh interface IP set address name=”Local Area Connection” static 10.0.0.2 255.255.255.248`
* `Route add 10.0.0.0 mask 255.0.0.0 10.0.0.1 -p`

## Windows 2003 Standard e Enterprise
{: #windows-2003-standard-and-enterprise}

É possível usar o Windows 2003 para configurar a rede usando a linha de comandos ou a interface gráfica com o usuário. Se desejar usar a linha de comandos, consulte as instruções de configuração para o Windows 2008 Server Core. Essas instruções também funcionam para o Windows 2003. As instruções para usar a interface gráfica são fornecidas aqui. Os endereços IP que são usados nesse exemplo precisam ser substituídos pelos endereços IP de seus blocos de IP.

**Exemplo de bloco de IP privado – 10.0.0.0/29**

* Endereço IP - 10.0.0.2
* Gateway - 10.0.0.1
* Máscara de sub-rede - 255.255.255.248

### Abrindo suas configurações de rede
{: #opening-your-network-configurations}

1. Acesse **Menu Iniciar >Configurações >Painel de Controle**
2. Abra **Conexões de rede**
   **Nota:** se você instalou dois adaptadores de rede, verá "Conexão de área local" e "Conexão de área local 2"

**Rede pública**

Use as instruções a seguir para configurar a rede pública por meio da interface gráfica do Windows.

1. Clique com o botão direito em **Conexão de área local > Propriedades** e selecione **Protocolo da Internet (TCP/IP) > Propriedades**.
2. Acesse **Geral > Usar o endereço IP a seguir:**
3. Insira o endereço IP público, a máscara de sub-rede e o gateway.
4. Selecione **Usar os endereços do servidor DNS a seguir:**. Se você estiver configurando a rede privada, use os servidores DNS a seguir. Se você não estiver configurando a rede privada, será necessário fornecer servidores DNS.
    * Servidor DNS preferencial: 10.0.80.11
    * Servidor DNS alternativo: 10.0.80.12
5. Clique em **OK**

**Rede privada                                            **

1. Clique com o botão direito em **Conexão da área local 2 > Propriedades**, selecione **Protocolo da Internet (TCP/IP) > Propriedades**.
2. Acesse **Geral > Usar o endereço IP a seguir:** e insira o endereço IP privado e a máscara de sub-rede. Deixe o campo de gateway vazio e clique em **OK**
3. Clique em **OK** novamente para fechar a janela de configuração de rede.

Para fornecer acesso à rede privada inteira que inclui os servidores DNS, é necessário incluir uma rota customizada para o servidor. Seguindo estas etapas para fornecer acesso à rede privada:

1. Acesse **Iniciar > Executar ** e insira *cmd* e pressione **OK**.
2. Execute o comando a seguir: **Nota:** certifique-se de substituir o endereço do gateway (10.0.0.1) por seu gateway de bloco de IP privado.<br/>
*“route add 10.0.0.0 mask 255.0.0.0 10.0.0.1 –p”*

## Windows 2008 Web, Standard, Enterprise e Datacenter
{: #windows-2008-web-standard-enterprise-and-datacenter}

Se você tiver o Windows 2008, configure a rede usando o prompt de comandos ou a interface gráfica com o usuário. Se você pretende usar o prompt de comandos, consulte as instruções de configuração para o Windows 2008 Server Core. Essas instruções funcionam para todos os servidores 2008. As instruções para usar a interface gráfica são fornecidas aqui. Os endereços IP que são usados nesse exemplo precisam ser substituídos pelos endereços IP de seus blocos de IP.

**Exemplo de bloco de IP privado – 10.0.0.0/29**

* Endereço IP - 10.0.0.2
* Gateway - 10.0.0.1
* Máscara de sub-rede - 255.255.255.248

**Abra as suas configurações de rede**

1. Acesse **Menu Iniciar >Configurações >Painel de Controle**
2. Abra o **Centro de rede e compartilhamento ** e clique em **Gerenciar conexões de rede**
* Se você instalou dois adaptadores de rede, verá "Conexão de área local" e "Conexão de área local 2".

**Rede pública**

As instruções a seguir fornecem etapas detalhadas sobre como configurar a rede pública por meio da interface gráfica do Windows.

1. Clique com o botão direito em **Conexão da área local > Propriedades**.
2. Selecione **Propriedades do Protocolo da Internet Versão 4 (TCP/IPv4)**.
3. Acesse **Geral > Usar o endereço IP a seguir:** e insira seu endereço IP público, a máscara de sub-rede e o gateway.
4. Selecione **Usar os endereços do servidor DNS a seguir:**. Se você estiver configurando a rede privada, use os servidores DNS a seguir. Se você não estiver configurando a rede privada, será necessário fornecer servidores DNS
    * Servidor DNS preferencial: 10.0.80.11
    * Servidor DNS alternativo: 10.0.80.12
5. Clique em **OK**

**Rede privada                                            **

1. Clique com o botão direito em **Conexão de área local 2 > Propriedades** e selecione **Protocolo da Internet Versão 4 (TCP/IPv4) > Propriedades**.
2. Acesse **Geral > Usar o endereço IP a seguir:**, insira o endereço IP privado e a máscara de sub-rede e clique em **OK**.
3. Clique em **OK** novamente para fechar a janela de configuração de rede.

Para fornecer acesso à rede privada inteira que inclui os servidores DNS, é necessário incluir uma rota customizada para o servidor.

1. Acesse **Iniciar > Executar** e insira "cmd" e clique em **OK**
2. Execute o comando a seguir: **Nota:** substitua o endereço do gateway (_10.0.0.1_) por seu gateway de bloco de IP privado.
  * `route add 10.0.0.0 mask 255.0.0.0 10.0.0.1 –p`

## RedHat, Fedora e CentOS
{: #redhat-fedora-and-centos}

A configuração da rede no RedHat, Fedora e CentOS é feita editando manualmente os arquivos de configuração. Para editar manualmente os arquivos, é necessário efetuar login na máquina virtual.

**Exemplo de bloco de IP público – 192.0.2.0/29**

* Endereço IP - 192.0.2.2
* Gateway - 192.0.2.1
* Máscara de sub-rede - 255.255.255.248

**Exemplo de bloco de IP privado – 10.0.0.0/29**

* Endereço IP - 10.0.0.2
* Gateway - 10.0.0.1
* Máscara de sub-rede - 255.255.255.248

**Rede pública**

As configurações de rede pública estão contidas no arquivo a seguir. É necessário editar esse arquivo com as informações que são fornecidas. Substitua os endereços IP de exemplo pelos endereços IP de seu bloco de IP público. Se o arquivo não existir, crie-o. Se ele existir, substitua todos os dados que estão no arquivo pelas informações a seguir:
* /etc/sysconfig/network-scripts/ ifcfg-eth1
      DEVICE=eth1
      BOOTPROTO=static
      BROADCAST=192.0.2.7
      IPADDR=192.0.2.2
      NETMASK=255.255.255.248
      NETWORK=192.0.2.0

      GATEWAY=192.0.2.1
      ONBOOT=yes

**Rede privada                                            **

As configurações de rede privada estão no arquivo a seguir. É necessário editar esse arquivo com as informações que são fornecidas. Substitua os endereços IP de exemplo pelos endereços IP corretos de seu bloco de IP privado. Se o arquivo não existir, crie-o. Se o arquivo existir, substitua todos os dados no arquivo pelas informações a seguir: **Nota:** a rede privada *NÃO* terá uma rota padrão, portanto, o *GATEWAY* não será definido.

* /etc/sysconfig/network-scripts/ ifcfg-eth0
      DEVICE=eth0
      BOOTPROTO=static
      IPADDR=10.0.0.2
      NETMASK=255.255.255.248
      ONBOOT=yes

Finalmente, uma nova rota é necessária para fornecer acesso privado à rede privada inteira para incluir o servidor DNS, o que é feito editando o arquivo a seguir. Este preenchimento não existe, portanto, ele precisa ser criado. **NOTA:** substitua "10.0.0.1" no exemplo por seu gateway de IP de sub-rede privada.

* /etc/sysconfig/network-scripts/route-eth0
      10.0.0.0/8 via 10.0.0.1

**Configuração de DNS**

As configurações de DNS primário e secundário estão contidas em um arquivo separado. É necessário editar o arquivo com as informações a seguir. Se essa máquina virtual não tiver acesso à rede privada, será necessário substituir os IPs do servidor pelos endereços IP dos servidores DNS que você deseja usar.

* /etc/resolv.conf
      nameserver 10.0.80.11
      nameserver 10.0.80.12

Para que essas mudanças entrem em vigor, é necessário reiniciar a rede no servidor. É possível reiniciar a rede ao executar o comando a seguir:

* `service network restart`

## Ubuntu e Debian
{: #ubuntu-and-debian}

Configure as redes Ubuntu e Debian por meio de um único arquivo de configuração. É necessário editar esse arquivo de configuração com as informações a seguir. Para editar esse arquivo, é necessário ter acesso raiz para o servidor. A instalação base do Ubuntu não fornece um login raiz. No entanto, o usuário que foi criado no processo de instalação pode acessar o sudo. Será necessário usar o comando sudo para editar o arquivo se você estiver executando o Ubuntu.

**Exemplo de bloco de IP público – 192.0.2.0/29**

* Endereço IP - 192.0.2.2
* Gateway - 192.0.2.1
* Máscara de sub-rede - 255.255.255.248

**Exemplo de bloco de IP privado – 10.0.0.0/29**

·Endereço IP – 10.0.0.2
·Gateway – 10.0.0.1
·Máscara de sub-rede – 255.255.255.248

**Rede privada e pública**

Edite o arquivo a seguir com qualquer editor de texto e substitua os endereços IP de exemplo pelos IPs que estão em seus blocos de IP públicos e privados.

* /etc/network/interfaces
      auto lo
      iface lo inet loopback

      auto eth1
      iface eth1 inet static
      address 192.0.2.2
      netmask 255.255.255.248
      gateway 192.0.2.1

      auto eth0
      iface eth0 inet static
      address 10.0.0.2
      netmask 255.255.255.248

      #Static route for backend service network
      up route add -net 10.0.0.0/8 gw 10.0.0.1

**Configuração de DNS**

A configuração de DNS primária e secundária está contida em um arquivo separado. É necessário editar esse arquivo de configuração com as informações a seguir. Se essa máquina virtual não tiver acesso à rede privada, será necessário substituir os IPs do servidor pelos endereços IP dos servidores DNS que você deseja usar.

* /etc/resolv.conf
      nameserver 10.0.80.11
      nameserver 10.0.80.12

Para que essas mudanças entrem em vigor, reinicie a rede no servidor. É possível reiniciar a rede ao executar o comando a seguir:

* */etc/init.d/network restart*

**Nota:** 192.0.2.0/24 é usado como Documentação de IP público por RFC1166 e RFC5737. Você não usa esses endereços IP em seus servidores.
