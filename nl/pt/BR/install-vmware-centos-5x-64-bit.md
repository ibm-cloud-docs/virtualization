---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Instalando o VMware no CentOS 5.x (64 bits)
{: #installing-vmware-on-centos-5-x-64-bit-}

Antes de iniciar, efetue login no servidor com o usuário raiz.

## Preparando para instalar o VMware
{: #preparing-to-install-vmware}

1. Faça download do [instalador do VMware ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://vmware.com/download/server/){: new_window}. Clique no link de download, aceite o EULA e faça download do LinuxTarball (VMware-server-1.0.3-44356.tar.gz, nesse exemplo):

* `# wget –O vmware-server.tar.gz https://download3.vmware.com/software/vmserver/VMware-server-1.0.3-44356.tar.gz`

2. Depois de fazer download do software, será necessário obter uma chave de licença (que é grátis na versão grátis do VMWare Server). Para registrar, consulte [VMware ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://register.vmware.com/content/registration.html){: new_window}.

3. Extraia o arquivo .tar:

* `# tar -xzvf vmware-server.tar.gz`

## Antes de iniciar a instalação
{: #before-you-begin-installation}

Antes de iniciar a instalação, revise os pré-requisitos a seguir:

```
# yum update
# yum install libXtst.i386
# yum install libXrender.i386
# yum install xinetd
```

## Instalando o VMWare Server
{: #installing-vmware-server}

1. Acesse o diretório:

* `# cd vmware-server-distrib/`

2. Execute o script de instalação do VMware:

* `# ./vmware-install.pl`

3. A instalação apresenta perguntas básicas e deseja saber em quais diretórios criar e instalar determinadas partes do VMware. A partir daqui, aceite os padrões. Aceite o contrato de licença para continuar com a instalação.

4. A pergunta a seguir pode ser feita a você: "Nenhum dos módulos de vmmon pré-construídos para o VMware Server é adequado para o seu kernel em execução. Deseja que esse programa tente construir o módulo do vmmon para o seu sistema (é necessário um compilador C instalado em seu sistema)?"
* Responda **sim** para essa pergunta (que é o padrão).

## Configurando a rede do VMware
{: #setting-up-vmware-networking}

As seguintes perguntas são feitas a você:

1. "Deseja colocar as suas máquinas virtuais em rede?"
* Responda **sim**. É necessário criar uma configuração de rede para o seu dispositivo de rede pública para que seja possível acessar a Internet em suas máquinas virtuais.

2. "Seu computador tem múltiplas interfaces ethernet disponíveis: eth0, eth1. Em qual você deseja criar uma ponte para vmnet0?"
* Lembre-se, a maneira como todos os servidores {{site.data.keyword.cloud}} são configurados e executados é com a rede pública em eth1 e a rede privada em eth0. No VMware, o dispositivo de ponte padrão para vmnet0 é eth0. Em vez de pressionar Enter, digite eth1.

## Colocando a rede privada em bridge
{: #bridging-the-private-network}

A pergunta a seguir é feita, que pode ser respondida como **sim** ou **não**: *"Deseja configurar outra rede em bridge?"

Se você planeja executar serviços ou outros aplicativos em sua rede privada, continue com "sim" para essa pergunta (a menos que você saiba que não está usando a rede privada) e uma ponte de rede é criada para a sua rede privada. Depois de pressionar Enter, ele usará eth0 automaticamente como a interface, pois essa é a única disponível (desde que você tenha apenas duas placas de rede no servidor).

## Outras configurações de rede
{: #other-networking-settings}

Algumas outras perguntas sobre a configuração de rede do VMware Server são apresentadas. Prossiga com as recomendações a seguir:

* *"Deseja ser capaz de usar a rede NAT em suas máquinas virtuais?"*

- Prossiga com "sim"

* *"Deseja que esse programa analise uma sub-rede privada não usada?"*

- Prossiga com "sim"

- Depois que esse processo for concluído, certifique-se de não configurar outra rede NAT.

* *"Deseja ser capaz de usar a rede apenas de host em suas máquinas virtuais?"*

- Prossiga com "sim"

* *"Deseja que esse programa analise uma sub-rede privada não usada?"*

- Prossiga com "sim"

- Depois que esse processo for concluído, certifique-se de não configure outra rede apenas do host.

## Especificando a porta de atendimento
{: #specifying-listening-port}

A próxima pergunta a ser feita é em qual porta você deseja que o VMware Server atenda. É possível deixar a porta padrão como 904.

## Armazenando as máquinas virtuais
{: #storing-the-virtual-machines}

A próxima pergunta que o instalador faz é *"Em qual diretório você deseja manter seus arquivos de máquina virtual?"*. O local padrão é /var/lib/vmware/virtual machines. Certifique-se de colocar as máquinas virtuais em um local em que você tenha muito espaço em disco, como uma matriz RAID redundante ou um disco rígido secundário grande. Sempre certifique-se de ter espaço suficiente para uma máquina virtual. Nesse caso, é possível usar um ponto de montagem /data/vm que é montado para um disco grande.

## Fornecendo o número de série para o VMware
{: #providing-the-serial-number-for-vmware}

A parte final da instalação requer que você insira uma chave de licença do VMware e um número de série. Se você ainda não tiver uma chave de licença, consulte o [site do VMware ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://register.vmware.com/content/registration.html){: new_window}. Se você tiver o número de série para esse VMware Server, insira-o no prompt e, em seguida, pressione Enter.

Agora, algo semelhante à instrução a seguir é exibido:

    "A configuração da construção do VMware Server 1.0.3-44356 para Linux para esse kernel em execução foi concluída com êxito".

Agora, o VMware agora está configurado em seu servidor. Agora é necessário fazer download do VMware Server Console, que é o cliente da GUI para o VMware Server, configurar e instalar máquinas virtuais.

## Fazendo download do VMware Server Console
{: #downloading-vmware-server-console}

O VMware Server Console é o aplicativo cliente para o VMware Server. Use o console para gerenciar o VMware Server no qual é possível criar, configurar e instalar máquinas virtuais. Para instalar esse aplicativo, faça download do pacote do cliente VMware Server Windows por meio de [Downloads do VMware ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://vmware.com/download/server/){: new_window}. Esse pacote é o arquivo .zip. Depois de transferi-lo por download, extraia o pacote e instale o arquivo VMware-console-1.0.3-x. Quando a instalação for concluída, você terá terminado a instalação do VMware Server Console e estará pronto para configurar o VMware Server.

## Efetuando login no VMware Console
{: #logging-in-to-the-vmware-console}

Abra o VMware Server Console por meio do computador no qual você o instalou. Quando ele é carregado, uma tela "Alternar host" (login) é exibida. O VMware Server usa o nome de usuário e a senha do sistema Linux para autenticar usuários, portanto, é necessário usar os nomes de usuário (raiz em particular) para efetuar login no VMware. Use as credenciais a seguir:

* **Nome do host:** endereço IP mais a porta (por exemplo, 67.228.160.201:904)<br />
* **Nome do usuário:** raiz<br />
* **Senha:** senha (use a senha raiz real do sistema)

## Configurando as regras de firewall (IPTables)
{: #configuring-the-firewall-rules-iptables-}

Se você tiver problemas de conexão com o VMware Server e não for um problema de autenticação (se você receber um erro de nome de usuário e senha, você terá um usuário ou senha inválidos), seu firewall poderá estar bloqueando a conexão com o VMware Server. Como uma resolução, tente incluir a regra de IPTable a seguir em seu /etc/sysconfig/iptablesfile. **Nota:** certifique-se de que a convenção de nomenclatura siga a configuração do servidor:

- `# -A FWALL-INPUT -p tcp -m tcp -s 0/0 --dport 904 -j ACCEPT`
