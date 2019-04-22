---

copyright:
years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: vsphere, Veeam
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}
{:table: .aria-labeledby="caption"}

# Fazendo backup do ambiente VMware vSphere usando o Veeam
{: #backing-up-your-vmware-vsphere-environment-by-using-veeam}

É possível fazer backup de seu ambiente VMware vSphere usando uma solução híbrida que consiste nos serviços a seguir:

* {{site.data.keyword.cloud}} Object Storage Service
* NetApp AltaVault Cloud Storage Gateway
* Software Veeam Backup and Replication

O Veeam Backup and Replication permite uma solução híbrida que inclui o dispositivo de armazenamento integrado à nuvem NetApp AltaVault e o {{site.data.keyword.cos_full}}. O software cria, mantém e restaura ambientes virtuais por meio de backups. Quando usado junto com um dispositivo de armazenamento integrado em nuvem do NetApp AltaVault, você cria backups que são armazenados localmente (no local). O backup também é replicado simultaneamente para o {{site.data.keyword.cos_full_notm}}. Com essa solução híbrida, duas cópias de um backup são feitas, mas apenas uma delas existe localmente.

## AltaVault Cloud-Integrated Storage Gateway
{: #altavault-cloud-integrated-storage-gateway}

É possível usar o AltaVault Cloud Storage Gateway para integrar seu ambiente no local com a nuvem sem precisar gravar scripts ou aplicativos usando APIs de REST para o {{site.data.keyword.cos_full_notm}}. É possível montar ou apontar para os pontos de montagem e começar a copiar dados para a nuvem de forma segura.

### Implementando o AltaVault On-Premises
{: #deploying-altavault-on-premises}

Siga estas etapas para implementar o AltaVault como uma solução de backup no local para o {{site.data.keyword.cos_full_notm}}.

É possível comprar o AltaVault como um dispositivo físico ou virtual. A implementação do dispositivo virtual AltaVault baseado no VMware vSphere ESXi de versão de avaliação é abordada nesse procedimento.
{:tip}

<a name="prerequisites"></a>
#### Pré-requisitos

Verifique se os pré-requisitos a seguir foram atendidos:

* Uma cópia do AltaVault Virtual Appliance. Ela é um arquivo único com uma extensão de arquivo OVA. Entre em contato com o representante do NetApp para o dispositivo ou faça download de uma versão de avaliação de 90 dias por meio do [website do NetApp AltaVault ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://www.netapp.com/us/products/protection-software/altavault/){: new_window}.
* Um ambiente existente do vSphere ESXi 5.5 no local com os requisitos mínimos de CPU, memória e espaço em disco disponíveis para o dispositivo AltaVault. Se você usar a versão de avaliação, esses requisitos serão quatro CPUs virtuais (vCPUs), 24 GB de memória e até 8 TB de espaço em disco.
* Dois controladores de interface de rede (NICs) de 10 Gbps disponíveis no ambiente vSphere. Um NIC é usado para entrada de dados e o outro é usado para replicação de dados para o {{site.data.keyword.cos_full_notm}}.
* Duas redes que correspondem aos dois NICs (VLANs) definidos dentro do ambiente vSphere. A rede de replicação não pode ser designada para a mesma rede que a rede de entrada de dados. Fazer isso pode criar um loop de roteamento.
* Um conjunto de credenciais do {{site.data.keyword.cos_full_notm}}. Essas credenciais incluem um nome de usuário do {{site.data.keyword.cloud_notm}}, um nome de usuário do {{site.data.keyword.cos_full_notm}} e a chave de API que está associada ao nome de usuário do {{site.data.keyword.cloud_notm}}.
* Conhecimento da terminologia do VMware Sphere e a administração de ambientes vSphere ESXi. Esse conhecimento inclui, mas não está limitado, ao uso do vSphere Web Client, do cliente do vSphere e da designação de recursos de hardware que incluem rede e armazenamento.

### Implementando o AltaVault OVA
{: #deploying-altavault-ova}

É possível implementar o AltaVault OVA para o ambiente vSphere após todos os pré-requisitos serem atendidos. Instruções para a implementação do OVA podem ser localizadas no [Guia de instalação e serviço do NetApp AltaVault ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}.

1. Edite a máquina virtual (VM) do AltaVault após a implementação do OVA ser concluída.
2. Mude a memória alocada para corresponder à versão do AltaVault que está na janela de edição. Se você estiver usando a versão de avaliação, designe 24 GB de memória e inclua um disco que seja menor ou igual a 8 TB. **Nota:** esse dispositivo de armazenamento em disco secundário é usado para armazenar dados de backup deduplicados.
* Certifique-se de designar redes diferentes (VLANs) para o dispositivo AltaVault após a memória e as configurações de disco serem modificadas.

Os NICs são designados às funções de interface a seguir:

* Primário. Usado como a interface de gerenciamento.
* e0a. Uma interface que é usada para replicar dados do dispositivo AltaVault para a nuvem.
* e0b. Uma interface que é usada para exportar o ponto de montagem para o compartilhamento SMB/CIFS ou NFS.
* e0c. Uma interface opcional que é usada para exportar o ponto de montagem para o compartilhamento SMB/CIFS ou NFS.

Nesta configuração de exemplo, o dispositivo AltaVault usa a interface **e0a** como a interface de replicação na nuvem e a interface **e0b** para exportar um ponto de montagem CIFS/SMB. **Nota:** um compartilhamento CIFS/SMB e um compartilhamento NFS não podem ser usados para acessar os mesmos dados. Em outras palavras, se os dados forem colocados em um compartilhamento CIFS/SMB, ele não poderá ser acessado por meio de um compartilhamento NFS e vice-versa.

Para obter mais informações sobre a implementação do dispositivo AltaVault e a definição das configurações de VM para o dispositivo, consulte [Guia de instalação e serviço do NetApp AltaVault ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}.

### Configuração inicial do dispositivo AltaVault <!--initial configuration?-->
{: #initial-configuration-of-the-altavault-appliance-initial-configuration-}

É possível ligar a VM do AltaVault depois de ela ser configurada com o hardware apropriado. **Nota:** leva algum tempo para que a VM do AltaVault seja ligada inicialmente, pois o dispositivo AltaVault está formatando o disco de cache de metadados secundário.

1. Depois que o dispositivo concluir o processo de início, efetue login no console do AltaVault. Use ``*admin`` como o **nome do usuário** e ``password`` como a **Senha**. É possível mudar essas credenciais após a conclusão da configuração inicial.
2. Depois de efetuar login, será perguntado se você deseja usar o assistente para a configuração inicial. Insira **s** e pressione **Enter** para salvar suas mudanças.

Use as informações na Tabela 1 após a abertura do assistente.

|Pergunta|Resposta|
|---|---|
|Etapa 1: Senha do administrador?|Insira uma nova senha do administrador (ela não pode ser "senha")|
|Etapa 2: Nome do host?|Insira o nome do host que você deseja usar|
|Etapa 3: Usar o DHCP na interface primária?|Insira **n**|
|Etapa 4: Endereço IP primário?|Insira o endereço IP da rede primária. Na configuração de exemplo, o endereço IP primário é a rede que é usada para replicação de nuvem e gerenciamento de dispositivo (192.168.50.15)|
|Etapa 5: Máscara de rede?|Insira a máscara de rede (255.255.255.0)|
|Etapa 6: Gateway padrão?|Insira o gateway padrão (192.168.50.1)|
|Etapa 7: Servidor DNS primário?|Insira o servidor do sistema de nome do domínio principal (DNS) em seu ambiente|
|Etapa 8: Nome do domínio?|Insira o nome de domínio de seu ambiente (testenv.org)|
{: caption="Tabela 1. Valores de configuração inicial de AltaVault" caption-side="top"}

### Configurando o AltaVault para o Object Storage
{: #configuring-altavault-for-object-storage}

Use as etapas a seguir para configurar o dispositivo para se conectar ao serviço {{site.data.keyword.cos_full_notm}}.

1. Abra um navegador da web e insira o endereço IP da interface primária do dispositivo AltaVault (consulte a etapa anterior).
2. Efetue login no console com as credenciais do administrador. No primeiro login, o Painel do assistente é exibido.
3. Selecione **Configurações do sistema** e verifique se as informações estão corretas na tela seguinte e ajuste o fuso horário para refletir o fuso horário de seu ambiente.
4. Clique em **Avançar >Salvar e Aplicar >Sair**. Você é retornado para o Painel do assistente.
5. Selecione **Configurações de nuvem** e clique em **Provedor**. Escolha **IBM Cloud Object Storage**.
6. Selecione uma **Região do Object Storage** apropriada. **Nota:** nem todas as regiões são exibidas (como Melbourne). No entanto, o nome do host do serviço {{site.data.keyword.cos_short}} é modificado usando o campo **Nome do host**. Por exemplo, se você desejar usar Melbourne como a região, será possível selecionar **San Jose 1** no menu suspenso **Região** e modificar o campo **Nome do host** para **mel01.objectstorage.softlayer.net**.
7. Insira suas credenciais do {{site.data.keyword.cos_full_notm}} no campo **Nome do usuário**. O formato do nome do usuário deve ser `object_storage_username:IBM_Cloud_username`. Por exemplo: **ABC-DE123456-7:user**. É possível localizar o nome do usuário do Object Storage em **Armazenamento >Armazenamento de objeto** no [{{site.data.keyword.slportal_full}} ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://control.softlayer.com/){: new_window}.
8. Insira um **Nome do depósito** para armazenar os dados. O nome do depósito é o nome do contêiner no qual você deseja armazenar os dados no {{site.data.keyword.cos_full_notm}}.
* Não modifique a porta padrão, a menos que seja instruído de outra forma pelo administrador de rede. O campo **Ativar arquivamento** é padronizado como **Não** e clique em **Avançar**.
9. Insira o **Token de solicitação de licença**, se necessário, e clique em **Avançar**.
10. Insira uma **Chave de criptografia**. É possível permitir que AltaVault gere uma nova chave de criptografia ou insira uma chave existente que você deseja usar para criptografar e decriptografar os dados. Clique em **Avançar**.
11. Verifique se todas as suas configurações estão corretas e, em seguida, clique em **Concluir e aplicar**. Agora, o AltaVault tenta entrar em contato com o serviço {{site.data.keyword.cos_full_notm}} usando as entradas e as configurações que estão no Assistente de configurações de nuvem. Se a conexão falhar, revise suas configurações e certifique-se de que tenha o acesso apropriado para o serviço.
12. Clique em **Sair** após uma conexão ser estabelecida para retornar ao Painel do assistente e clicar em **Sair do assistente** para retornar à página de status do dispositivo AltaVault.
13. Verifique se o *Storage Optimization Service* está em execução e se o status está pronto. **Nota:** pode levar alguns minutos para que o status mude para *Pronto*.

O dispositivo AltaVault está configurado para se comunicar com o serviço {{site.data.keyword.cos_full_notm}}.

### Configurando o ponto de montagem CIFS/SMB em AltaVault
{: #configuring-the-cifs-smb-mount-point-in-altavault}

A interface **e0b** precisa ser configurada para criar um ponto de montagem CIFS/SMB. Use as etapas a seguir para configurar o **e0b**.

1. Acesse **Configurações > Interfaces de dados** e expanda a interface **e0b**. Marque **Ativar interface de dados** e insira o **Endereço IP, Máscara de sub-rede,** e o **Gateway** que você usa para montar o compartilhamento CIFS/SMB.
2. Deixe o valor padrão **MTU** de **1500 bytes**.<br/><br/>Embora a unidade de transmissão máxima (MTU) padrão esteja configurada como 1.500, será possível mudá-la para 9.000 se você usar quadros gigantes. **Nota:** o host ESXi e a infraestrutura física são necessários para suportar quadros gigantes. Por padrão, o {{site.data.keyword.cloud_notm}} já suporta um tamanho de MTU de 9.000 bytes sem mudanças de configuração necessárias.
3. Clique em **Aplicar**. O ponto de montagem está pronto para a configuração.
4. Selecione **Armazenamento > CIFS > Incluir compartilhamento CIFS** e insira um nome exclusivo.
5. Clique no menu suspenso **Compartilhamento fixado** e selecione **Sim**. **Nota:** os backups de Veeam Backup and Replication podem falhar em um compartilhamento desafixado.
6. Insira um caminho exclusivo para o compartilhamento no campo **Caminho**. É preferível usar o nome de compartilhamento como o caminho, ou seja, se o nome do compartilhamento for `cifs_share0, enter /cifs_share0` como o caminho.
7. Limpe **Permitir acesso a todos** se a segurança não for um problema. É preferível a lista de desbloqueio dos clientes que usam o compartilhamento CIFS/SMB. Caso contrário, deixe **Permitir acesso a todos** selecionado se a segurança for um problema e clique em **Incluir compartilhamento**.
8. Clique em **Incluir usuário do CIFS** para criar contas para usuários autorizados e preencher os campos **Nome do usuário** e **Senha**.
9. Expanda o novo compartilhamento do CIFS e clique em **Incluir um usuário ou grupo** para incluir as contas de usuário autorizado.
10. Acesse **Configurações globais do CIFS** e clique no menu suspenso **Interface de atendimento** e selecione **e0b** e clique em **Aplicar**.

O dispositivo AltaVault é configurado para permitir comunicações entre si, o {{site.data.keyword.cos_full_notm}} e o computador que está executando o Veeam Backup and Replication. É recomendado que você exporte a configuração do dispositivo AltaVault para expedir implementações futuras, se necessário.

Para exportar a configuração do dispositivo AltaVault, siga estas etapas.

1. Clique em **Configurações > Assistente de configuração** para acessar o Painel do assistente no console de gerenciamento da web do dispositivo AltaVault no local.
2. Clique em **Exportar configuração** e clique em **Exportar configuração**.
3. Salve o arquivo de configuração (um tarball/.tar) em uma localização segura.

## Veeam Backup and Replication
{: #veeam-backup-replication-backup}

O software Veeam Backup and Replication fornece recursos completos de backup, replicação e recuperação para VMs e seus dados. O backup pode ser totalmente integrado com um AltaVault Cloud Gateway Appliance.

### Fornecendo Veeam em um novo servidor
{: #provisioning-veeam-on-a-new-server}

É possível solicitar o Veeam quando você fornece um novo servidor VSI ou bare metal. Use as informações a seguir ao fornecer.
  * O Veeam está disponível somente com faturamento mensal
  * O Veeam está disponível somente com sistema operacional Windows

Para incluir o Veeam:
  * Na seção Complementos do sistema, em Complementos específicos do S.O., clique na guia Veeam e selecione uma das opções de Veeam.
  * Na seção Complementos do sistema, em Complementos de CDP, selecione qualquer opção adicional de Veeam para incluir. <br><br>**Nota**: se você selecionar *Veeam Backup and Replication 9.5 Atualização 3*, será necessário selecionar pelo menos uma opção na lista de Complementos de CDP.

### Pedindo o Veeam em um servidor existente
{: #ordering-veeam-on-an-existing-server}

É possível incluir o Veeam em um servidor existente com o procedimento de [Recarregamento do S.O.](/docs/infrastructure/software?topic=software-reloading-the-os). O servidor deve estar executando um S.O. Windows e deve ser configurado para faturamento mensal.

Para incluir o Veeam:
1. Na seção Complementos do sistema, em Complementos específicos do S.O., clique na guia Veeam e selecione uma das opções de Veeam.
2. Na seção Complementos do sistema, em Complementos de CDP, selecione qualquer opção adicional de Veeam para incluir. <br><br>**Nota**: se você selecionar *Veeam Backup and Replication 9.5 Atualização 3*, será necessário selecionar pelo menos uma opção na lista de Complementos de CDP.

### Implementando o Veeam Backup and Replication
{: #deploying-veeam-backup-replication}

Uma versão de avaliação do Veeam Backup and Replication Versão 8 é usada no exemplo.

#### Pré-requisitos
{: #prerequisites-deploying-veeam-backup-replication}

Antes de continuar com a implementação, verifique se os pré-requisitos a seguir estão satisfeitos:

* Ter um dispositivo AltaVault existente que esteja configurado para uso com o {{site.data.keyword.cos_full_notm}} e o Veeam Backup and Replication.
* Obtenha uma cópia do Veeam Backup and Replication para ambientes VMware, que é um arquivo executável único. Entre em contato com o representante do Veeam para obter uma cópia ou faça download de uma [versão de avaliação de 30 dias ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}.
* Obtenha um arquivo de licença para usar com o Veeam Backup and Replication. Na maioria dos casos, esse arquivo é enviado por e-mail para o endereço de e-mail que é usado para fazer download do Veeam Backup and Replication. Se você não recebeu esse arquivo, entre em contato com o representante do Veeam.<br/><br/>O arquivo de licença é usado para ativar todas as funções do Veeam Backup and Replication. Se esse arquivo não for fornecido durante a instalação do programa, todos os recursos e funções serão revertidos para a versão de avaliação de 30 dias.
* Ter um servidor de backup existente, que pode ser no local ou externo, com as especificações que estão localizadas na Tabela 2. O sistema operacional instalado deve ser uma versão de 64 bits.

||Mínimo|Recomendado|
|---|---|---|
|**OS**|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>Windows Server 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>Windows Server 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|
|**Número de núcleos ou vCPUs**|2|4|
|**Memória**|RAM de base de 4 GB mais 500 MB para cada tarefa de backup simultânea.|RAM de base de 16 GB mais 4 GB para cada tarefa de backup simultânea.|
|**Espaço em disco**|2 GB para instalação do produto; 10 GB por 100 VMs para dados do catálogo do sistema de arquivos guest (dados persistentes).|2 GB para instalação do produto; 10 GB para 100 VMs para o catálogo do sistema de arquivos guest (dados persistentes).|
|**Rede**|LAN de 1 Gbps para backup e replicação no local; WAN de 1 Mbps para backup e replicação externos.|LAN de 1Gbps para backup e replicação no local; WAN de 1 Mbps para backups e replicação externos.|
{: caption="Tabela 2. Requisitos do sistema para o servidor de backup do Veeam Backup & Replication" caption-side="top"}

#### Instalando o Veeam Backup and Replication
{: #installing-veeam-backup-replication}

Use as etapas a seguir para instalar o Veeam Backup and Replication para o servidor de backup depois que todos os pré-requisitos forem atendidos.

1. Para iniciar o assistente de configuração, dê um clique duplo no arquivo executável do Veeam e clique em **Veeam Backup and Replication - Instalação**.
2. Clique em **Avançar** e aceite os termos no contrato de licença.
3. Clique em **Avançar** e depois em **Instalar** para **Veeam Backup and Replication**.
4. Insira o local do arquivo de licença que foi obtido anteriormente e clique em **Avançar**.
5. Selecione os componentes do Veeam Backup and Replication que você deseja instalar e forneça a localização de instalação na tela **Configuração do Veeam Backup and Replication**. **O Veeam Backup and Replication e o catálogo do Veeam Backup** são componentes necessários. Clique em **Avançar**. O assistente de configuração executa uma série de verificações para assegurar que todas as estruturas do programa e os componentes de suporte necessários estejam instalados. Se algum estiver ausente, o assistente de configuração se oferecerá para instalá-los automaticamente. Se você precisar instalar estruturas ou componentes ausentes, clique em **Instalar**.
6. Verifique se todos os componentes foram **Aprovados** na verificação dos sistemas e clique em **Avançar**.
7. Selecione a **Conta de serviço (usuário)** na qual o serviço de backup do Veeam é executado. **Nota:** a conta de serviço padrão é a *conta do SISTEMA LOCAL*. Clique em **Avançar**.
8. Selecione a **Instância do SQL Server** que é usada para criar e armazenar bancos de dados do Veeam Backup and Replication.<!--Contact your database administrator for more information, if necessary. --> Clique em **Avançar**.
9. Insira a **Porta de serviço de catálogo** e a **Porta de serviço do Veeam Backup** (as portas padrão são **9393** e **9392**).<!--Contact your network administrator for more information, if necessary.--> Clique em **Avançar**.
10. Selecione os diretórios nos quais o catálogo do sistema de arquivos guest (dados persistentes) e o cache de gravação do vPower NFS (dados não persistentes) são armazenados. Clique em **Avançar**.
11. Verifique se todas as configurações e valores estão corretos e clique em **Instalar** para iniciar a instalação. Após a instalação ser concluída, clique em **Concluir**.

### Configurando o Veeam Backup and Replication para backups
{: #configuring-veeam-backup-replication-for-backups}

Depois de instalar o Veeam Backup and Replication, você está pronto para conectá-lo ao host ESXi do vSphere que contém o dispositivo virtual AltaVault.

1. Inicie o Veeam Backup and Replication.
2. No lado inferior esquerdo da tela, clique em **Infraestrutura de backup > Servidores gerenciados**.
3. Clique em **Incluir servidor** e dê um clique duplo em **VMware vSphere**.
4. Insira o **Nome do DNS ou endereço IP** e a **Descrição** do servidor vCenter e clique em **Avançar**.
5. Insira as **Credenciais** de uma conta local que tenha privilégios de administrador no servidor vSphere. **Nota:** o nome do usuário da conta deve estar no formato **DOMÍNIO\USUÁRIO** para contas de domínio ou no formato **HOST\USUÁRIO** para contas locais. Para incluir uma conta, clique em **Incluir** e insira o nome do usuário e a senha da conta.<br/>Não mude a **Porta de serviços da web padrão do VMware** durante a instalação do Veeam Backup and Replication, a menos que seu administrador de rede diga o contrário.
6. Clique em **Avançar**. O Veeam Backup and Replication se conecta ao servidor VMware vSphere. Se a tentativa de conexão falhar, verifique se a conta existe e tem privilégios de administrador no servidor VMware vSphere antes de tentar se conectar novamente.
7. Clique em **Concluir** na janela **Resumo** e verifique se o servidor vSphere foi incluído com êxito clicando em **Servidores gerenciados > VMware vSphere**.

### Incluindo um repositório de backup no Veeam Backup and Replication
{: #adding-a-backup-repository-to-veeam-backup-replication}

Por padrão, o Veeam Backup and Replication cria um repositório de backup local na unidade C:\ do servidor de backup do Veeam Backup and Replication durante a instalação do programa.

Use as etapas a seguir para criar um repositório de backup para armazenar todos os backups no dispositivo AltaVault. Se você desejar usar o repositório de backup padrão, ignore as etapas a seguir.

1. Na parte inferior esquerda da tela **Infraestrutura de backup**, clique em **Infraestrutura de backup > Repositórios de backup > Incluir repositório**.
2. Insira um nome de repositório exclusivo no campo **Nome**. Opcionalmente, é possível fornecer uma **Descrição**. Clique em **Avançar**.
3. Selecione **Pasta compartilhada**. O compartilhamento CIFS/SMB que você criou é o compartilhamento que esse repositório de backup usa. Clique em **Avançar**.
4. Especifique a localização do compartilhamento CIFS/SMB no dispositivo AltaVault. Para determinar a localização, abra um navegador da web e insira o endereço IP do dispositivo AltaVault. Acesse **Armazenamento > CIFS** e anote o **Caminho de compartilhamento** do compartilhamento. **Nota:** isso NÃO é o mesmo que o caminho local do compartilhamento.<br/><br/>O formato do caminho de compartilhamento é `\\<AltaVault appliance hostname>\<share name>`. Substitua o nome do host do dispositivo AltaVault no Caminho de compartilhamento pelo endereço IP da interface de rede **e0b** (o ponto de montagem do compartilhamento) do dispositivo AltaVault.<br/><br/>Para localizar o endereço IP da interface **e0b**, clique em **Configurações > Interfaces de dados** na janela de gerenciamento do dispositivo AltaVault. O caminho de compartilhamento que é especificado no Veeam Backup and Replication é `\\192.168.50.16\cifs_test2`.
5. Retorne para o Veeam Backup and Replication, insira o caminho de compartilhamento do ponto de montagem no campo **Pasta compartilhada** e clique em **Avançar**. O Veeam Backup and Replication tenta estabelecer uma conexão com o ponto de montagem. Se a tentativa de conexão falhar, volte e verifique se as configurações de rede para o dispositivo AltaVault estão corretas antes de tentar novamente.
6. Insira um valor para limitar as tarefas simultâneas máximas para o número de recursos que estão disponíveis. Esse valor é o número máximo de tarefas que um proxy de backup pode enviar para o compartilhamento selecionado. O número padrão de tarefas simultâneas é 4. O AltaVault sugere que você inicie com cinco tarefas simultâneas e aumente ou diminua esse valor conforme os recursos permitirem. Esse valor pode ser ajustado após a criação do repositório de backup.
7. Clique em **Avançar**.
8. Se necessário, especifique as **Configurações do vPower NFS **. Se a seleção **Ativar servidor NFS do vPower** for deixada limpa, o Veeam Backup and Replication usará o vPower para recuperação e verificação de recuperação. Clique em **Avançar**.
9. Na tela **Revisão**, confirme que todas as suas configurações estão corretas e clique em **Avançar**.
10. Clique em **Concluir** para sair do assistente. Agora é possível começar a fazer backup de seus dados.

### Fazendo backup do ambiente
{: #backing-up-the-environment}

Siga estas etapas para criar um backup de um ambiente virtual completo.

1. Na tela **Infraestrutura de backup**, clique em **Backup & Replication**.
2. Na janela **Backup & Replication **, clique em **Tarefas > Tarefa de backup**.
3. Insira um nome exclusivo no campo **Nome**. Opcionalmente, é possível inserir uma **Descrição**. Clique em **Avançar**.
4. Selecione as VMs das quais deseja fazer backup clicando em **Incluir objetos** e clique na estrutura em árvore para selecionar as VMs. Clique em **Incluir** depois de selecionar as VMs apropriadas.<br/>Se apenas partes específicas das VMs forem submetidas a backup (o disco de início), clique em **Exclusões** e especifique as partes. Caso contrário, clique em **Avançar**.
5. Selecione o repositório de backup que você criou usando o menu suspenso **Repositório de backup**.

**Nota:** para obter um desempenho ideal, certifique-se de mudar a deduplicação de dados e as configurações de compactação. Use as etapas a seguir para otimizar o desempenho.

1. Clique em **Avançado**, selecione a guia **Armazenamento** e limpe **Ativar duplicação de dados sequenciais**. O desempenho é melhorado porque o dispositivo AltaVault executa a deduplicação em nível de bloco dos backups do Veeam Backup and Replication que passam por ele.
2. Selecione **Nenhum** no menu suspenso **Nível de compactação** e selecione **Destino de LAN** no menu suspenso **Otimização de armazenamento**.<br/>**Nota:** se o local de rede do compartilhamento CIFS/SMB estiver congestionado, deixar **Ativar deduplicação de dados sequenciais** marcado poderá aliviar problemas de desempenho de rede, mas ao custo de proporções menores de deduplicação de dados experimentadas no dispositivo AltaVault.
3. Clique em **Avançar**.
4. Se você desejar o processamento de reconhecimento de aplicativo e a indexação do sistema de arquivos guest, marque a caixa de seleção apropriada. Configure as **Credenciais de S.O. do guest** [o nome de usuário e a senha do S.O. guest das VMs que estão sendo submetidas a backup], se necessário. Clique em **Avançar**.
5. Marque a caixa de seleção **Executar a tarefa automaticamente** se os backups são executados regularmente e configure os intervalos que desejar. Caso contrário, clique em **Criar** e **Concluir**.

#### Iniciando um backup manual
{: starting-a-manual-backup}
Para iniciar manualmente um backup, clique com o botão direito na tarefa de backup e selecione **Iniciar**. Como alternativa, selecione **Integral ativo** se desejar um novo backup.

**Nota:** o Veeam Backup and Replication pode restaurar ambientes virtuais de um backup. Para obter mais informações sobre a restauração de ambientes virtuais, consulte o website [Veeam Backup and Replication ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}.
