---
copyright:
  years: 2014, 2018
lastupdated: "2018-06-22"

subcollection: virtualization

keywords: disaster recovery
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:table: .aria-labeledby="caption"}

# Recuperando o ambiente VMware vSphere
{: #recovering-your-vmware-vsphere-environment}

Atualmente, o backup de dados é o método primário para manter a segurança de dados e a integridade. Em um mundo ideal e perfeito, seu uso nunca seria necessário.  A realidade é que acidentes e desastres acontecem.  Caso aconteçam, é primordial minimizar o tempo de inatividade do sistema e voltar a funcionar o mais rapidamente possível.

No passado, o armazenamento de backups em armazenamento de alto desempenho ou fita eram soluções viáveis. Hoje, as empresas estão buscando aliviar o capital e os custos operacionais associados ao armazenamento no local físico, aumentando ou até mesmo substituindo-o pelo Object Storage. No entanto, com o Object Storage, porque os backups não estão mais localizados no armazenamento no local, a restauração deles não é mais uma questão de simplesmente localizar o backup necessário e restaurá-lo. Felizmente, há uma solução híbrida que executa grande parte do trabalho braçal de restauração de backups armazenados no Object Storage. A solução consiste em

* {{site.data.keyword.cos_full}}
* NetApp AltaVault Cloud Storage Gateway do NetApp (anteriormente Riverbed SteelStore)
* Software Veeam Backup and Replication

Se ocorrer um evento catastrófico e o ambiente primário ficar off-line, um site de recuperação secundário poderá ser criado no {{site.data.keyword.cloud}} para recuperá-lo. É possível implementar o software Veeam Backup and Replication e um dispositivo de armazenamento integrado em nuvem AltaVault para se comunicar com o {{site.data.keyword.cos_full_notm}} para acessar backups anteriores do ambiente primário. O software se conecta ao dispositivo de armazenamento para restaurar os backups para uma nova localização, trazendo o ambiente primário de volta on-line e evitando qualquer perda adicional de tempo de atividade.

Os endereços IP no local são retidos quando você restaura as máquinas virtuais (VMs) no site de recuperação do {{site.data.keyword.cloud_notm}}. Como resultado, é importante mudar os endereços IP após a recuperação bem-sucedida ou projetar uma infraestrutura BYOIP. Para obter assistência em qualquer um dos casos, entre em contato com o Representante IBM.

O procedimento a seguir foca o uso coordenado de um dispositivo NetApp AltaVault Cloud Storage Gateway, a infraestrutura do {{site.data.keyword.cloud_notm}} e o Veeam Backup and Replication para restaurar completamente um ambiente VMware vSphere com falha. O software assume que pelo menos um backup do ambiente foi feito usando as três tecnologias anteriores e reside no {{site.data.keyword.cos_full_notm}} no momento da recuperação.

O "dispositivo AltaVault" é identificado de duas maneiras diferentes. A primeira é "dispositivo AltaVault no local", que se refere ao dispositivo AltaVault original no local que falhou e do qual os backups são recuperados e/ou restaurados. A segunda é "dispositivo {{site.data.keyword.cloud_notm}} AltaVault", que se refere ao dispositivo AltaVault que é usado para recuperar backups do dispositivo AltaVault no local com falha. O dispositivo {{site.data.keyword.cloud_notm}} AltaVault implementa em um ambiente vSphere usando um servidor de utilitário.

## Introduzindo uma solução híbrida
{: #introducing-a-hybrid-solution}

O Veeam Backup and Replication permite uma solução híbrida para incluir o dispositivo de armazenamento integrado em nuvem NetApp AltaVault e o {{site.data.keyword.cos_full_notm}}. A preocupação primária é criar, manter e restaurar de backups de ambientes virtuais. O dispositivo de armazenamento integrado à nuvem NetApp AltaVault é uma solução de software para integrar continuamente um ambiente no local com nuvens privadas ou públicas. Quando usados juntos, o Veeam Backup and Replication cria backups que o dispositivo de armazenamento integrado à nuvem AltaVault armazena em armazenamento no local e é replicado simultaneamente para o {{site.data.keyword.cos_full_notm}}. Além disso, o modelo de precificação pré-pago do {{site.data.keyword.cloud}} e a integração completa com a rede de entrega de conteúdo (CDN) oferecem a capacidade de armazenar e distribuir dados em 24 nós geograficamente diferentes.

### IBM Cloud Object Storage
{: #ibm-cloud-object-storage}

Para acessar os arquivos de dados submetidos a backup para o {{site.data.keyword.cos_full_notm}}, os arquivos de dados são solicitados das maneiras a seguir:

* O dispositivo AltaVault já está localizado no local
* Um dispositivo AltaVault secundário que está localizado no {{site.data.keyword.cloud_notm}}

Siga este procedimento para implementar um dispositivo AltaVault secundário no {{site.data.keyword.cloud_notm}} para recuperar um dispositivo AltaVault primário no local que falhou.

O ambiente do {{site.data.keyword.cloud_notm}} consiste em um único host ESXi com armazenamento local para hospedar e executar o dispositivo AltaVault secundário. A infraestrutura é representativa da arquitetura de site único básica com um único host ESXi que é gerenciado por um servidor vCenter dentro de uma {{site.data.keyword.cloud_notm}} Virtual Server Instance (VSI).

Se você tiver uma infraestrutura mais substancial que requeira armazenamento compartilhado e/ou suporte para esses recursos

* Alta disponibilidade (HA) do vSphere
* vSphere Distributed Resource Scheduler (DRS)
* vSphere vMotion

#### Pré-requisitos
{: #prerequisites-ibm-cloud-object-storage}

Verifique se os pré-requisitos a seguir foram atendidos antes de continuar: **Nota:** para este exemplo, uma versão de avaliação de um dispositivo AltaVault AVA-v8 foi usada com o AltaVault Cloud-Integrated Storage 4.1.

* Certifique-se de que o ambiente existente consista em um único host ESXi gerenciado por um servidor vCenter dentro de uma VSI do {{site.data.keyword.cloud_notm}}.
* Conhecimento da terminologia do VMware Sphere e a administração de ambientes vSphere ESXi. Esse conhecimento inclui, mas não está limitado ao, uso do vSphere Web client, vSphere Client e a designação de recursos de hardware para incluir rede e armazenamento.

#### Pedir duas redes privadas móveis
{: #order-two-portable-private-networks}

O AltaVault requer que as interfaces de rede estejam em redes diferentes dentro do ambiente. Use as etapas a seguir para pedir duas redes privadas móveis por meio do {{site.data.keyword.slportal_full}}.

1. Acesse o [{{site.data.keyword.slportal_full}} ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://control.softlayer.com/){: new_window} usando suas credenciais exclusivas.
2. Clique em **Conta > Fazer um pedido**.
3. Selecione a seção **Rede** e clique em **Sub-redes/IPs > Pedido**.
4. No menu suspenso, selecione **Privado móvel**. A seleção de acionadores privados móveis é uma opção para selecionar o número de endereços IP privados móveis a serem exibidos. **Nota:** o {{site.data.keyword.cloud_notm}} reserva automaticamente pelo menos três endereços IP no bloco de endereço para cada rede privada móvel. Os endereços reservados são para o endereço de rede, o endereço de gateway e o endereço de transmissão. Esses endereços devem ser ligados diretamente à LAN virtual (VLAN). <!--If your IBM Cloud pod supports Hot Standby Ready Protocol (HSRP), then the number of usable IP addresses is reduced. For instance, if you select **4 Portable Private IP Addresses** will yield only one usable IP address or zero if the pod supports HSRP.--> Para obter mais informações sobre endereços IP móveis, consulte [Introdução às sub-redes e IPs](/docs/infrastructure/subnets?topic=subnets-getting-started-with-subnets-and-ips).
5. Clique em **Continuar** depois de selecionar o número de endereços IP privados móveis.
6. Selecione a VLAN na qual os endereços IP privados móveis serão roteados e clique em **Continuar**.
7. Conclua as informações de contato necessárias e clique em **Fazer pedido**.
8. Repita o processo de pedido para obter a segunda rede privada móvel necessária.
9. Clique em **Rede > Gerenciamento de IP > Sub-redes** na página {{site.data.keyword.slportal}} para visualizar suas redes privadas móveis e os endereços IP após a alocação. Recomenda-se rastrear suas designações de endereço IP. Use a seção **Notas** da página **Sub-redes** para cada endereço IP para refletir a máquina à qual ele está designado.

#### Configurando o vSphere
{: #configuring-vsphere}

É necessário configurar o vSphere para refletir a adição das redes privadas móveis e certifique-se de que elas estejam logicamente segregadas.

1. Acesse o ambiente vSphere e crie um grupo da porta da máquina virtual para refletir a inclusão de um dos blocos de IP móveis (dois no total).

O grupo da porta da máquina virtual é rotulado **Compartilhamento de rede**. Um bloco de IP móvel é usado para VMs que são colocadas no grupo da porta da máquina virtual padrão (primária). O outro é usado para transferência de dados entre um dispositivo AltaVault e um ponto de montagem exportado do Common Internet File System (CIFS)/Server Message Block (SMB).

## AltaVault Cloud Storage Gateway
{: #altavault-cloud-storage-gateway}

É possível usar o AltaVault para integrar o ambiente no local com a nuvem sem a necessidade de gravar scripts ou aplicativos usando APIs de REST para o {{site.data.keyword.cos_full_notm}}. O AltaVault expõe um ponto de montagem CIFS/SMB ou Network File System (NFS) no front-end e conecta-se com segurança à interface do {{site.data.keyword.cos_full_notm}} no back-end. Em seguida, é possível montar ou apontar para os pontos de montagem e copiar dados para e/ou recuperar/restaurar ambientes da nuvem com segurança.<!-- , as shown in Figure 1.-->

<!-- ![Figure 1](images/veeam_restore_fig2.png)
<br/>`Figure 1: AltaVault end-to-end flow with Veeam and IBM Cloud`-->

### Implementando AltaVault em uma configuração de recuperação de desastre
{: #deploying-altavault-in-a-disaster-recovery-configuration}

Siga estas etapas para implementar o AltaVault como uma solução de recuperação de desastre com o {{site.data.keyword.cos_full_notm}}.

#### Antes de iniciar
{: #before-you-get-started-deploying-altavault-in-a-disaster-recovery-configuration}

Verifique se os pré-requisitos a seguir foram atendidos antes de continuar:

* Obtenha uma cópia do AltaVault Virtual Appliance e certifique-se de que ela resida no servidor de utilitário. Ela é um arquivo único com uma extensão de arquivo OVA. Entre em contato com o representante do NetApp para o dispositivo ou faça download de uma versão de avaliação de 90 dias por meio do [website do NetApp AltaVault ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](http://www.netapp.com/us/products/protection-software/altavault/){: new_window}.
* Tenha um ambiente vSphere ESXi 5.5 existente no local com os requisitos mínimos de CPU, memória e espaço em disco disponíveis para o dispositivo AltaVault. Se você usar a versão de avaliação, aqui estão os requisitos de recurso: quatro CPUs virtuais (vCPUs), 24 GB de memória e até 8 TB de espaço em disco.
* Tenha dois controladores de interface de rede (NICs) de 10 Gbps disponíveis no ambiente vSphere. Um NIC é usado para acesso a dados e o outro é usado para replicação de dados para o {{site.data.keyword.cos_full_notm}}.
* Ter duas redes privadas móveis que correspondam aos dois NICs definidos no ambiente vSphere. A rede de replicação não pode ser designada para a mesma rede que a rede de acesso a dados, o que pode criar um loop de roteamento.
* Tenha credenciais do {{site.data.keyword.cos_full_notm}}. Essas credenciais incluem um nome de usuário do {{site.data.keyword.cloud_notm}}, um nome de usuário do {{site.data.keyword.cos_full_notm}} e a chave de API que está associada ao nome do usuário do {{site.data.keyword.cloud_notm}}.
* Suspenda a replicação de dados no dispositivo AltaVault no local ou desconecte-o do contêiner/depósito que é usado para acessar o archive de backup no {{site.data.keyword.cloud_notm}} se o ambiente no local ainda estiver intacto.
  * Use um dos dois métodos a seguir para suspender ou parar a replicação:
    * Desligue o dispositivo AltaVault no local.
    * Acesse o console de gerenciamento da web do dispositivo AltaVault e clique em **Armazenamento > Configurações de nuvem > Replicação**. Selecione **Suspender replicação**.
    * Obtenha uma cópia do arquivo de configuração do dispositivo AltaVault no local ou tenha-o acessível por meio de uma URL. O arquivo de configuração é um arquivo .tar.
  * Acesse o console de gerenciamento da web do dispositivo AltaVault e clique em **Configurar > Assistente de configuração > Exportar configuração > Exportar configuração**. Salve o arquivo de configuração (arquivo .tar) e transfira-o para o servidor de utilitário ou torne-o acessível por meio de uma URL.
    * Conhecimento da terminologia do VMware Sphere e a administração de ambientes vSphere ESXi. Esse conhecimento inclui, mas não está limitado ao, uso do vSphere Web client, vSphere Client e a designação de recursos de hardware, como rede e armazenamento.

### Implementando o AltaVault OVA
{: #deploying-altavault-ova-disaster}

Implemente o AltaVault OVA para o ambiente vSphere usando o servidor de utilitário após todos os pré-requisitos serem atendidos. Instruções para a implementação do OVA podem ser localizadas no [Guia de instalação e serviço do NetApp AltaVault ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}. A versão de avaliação do AltaVault vem configurada com quatro CPUs virtuais, 6 GB de memória e um disco primário de 150 GB. Use o Guia de instalação e serviço para executar as etapas a seguir:

1. Aumente a quantidade de memória para 24 GB.
2. Inclua um dispositivo de armazenamento em disco secundário que seja menor ou igual a 8 TB em tamanho para armazenar dados de backup deduplicados.
3. Designe redes privadas móveis diferentes para o dispositivo AltaVault após a memória e as configurações do disco serem mudadas.

Os NICs são designados às funções de interface a seguir:

* **Primário**: utilizado para o gerenciamento de dispositivo AltaVault e a replicação de dados para a nuvem. Ele é designado ao grupo da porta **Rede primária** no ambiente de exemplo.
* **e0a**: uma interface opcional que é usada para replicar dados do dispositivo AltaVault para a nuvem.
* **e0b**: uma interface que é usada para exportar o ponto de montagem para o compartilhamento SMB/CIFS ou NFS. Ele é designado ao grupo da porta **Compartilhamento de rede** no ambiente de exemplo.
* **e0c**: uma interface opcional que é usada para exportar o ponto de montagem para o compartilhamento SMB/CIFS ou NFS.

Na configuração de exemplo nesse procedimento, o dispositivo AltaVault usa a interface **Primária** como a interface de replicação para a nuvem e a interface **e0b** para exportar um ponto de montagem CIFS/SMB. **Nota:** não é possível usar um compartilhamento CIFS/SMB e um compartilhamento de NFS para acessar os mesmos dados. O protocolo que é usado deve ser o mesmo usado com o dispositivo AltaVault no local para replicar dados para a nuvem.

<!-- ![Figure 2](images/veeam_restore_fig3.png)
<br/>`Figure 2: IBM Cloud setup with VMware`-->

Para obter mais informações sobre a implementação do dispositivo AltaVault e a definição das configurações da VM, consulte [Guia de instalação e serviço do NetApp AltaVault ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}.

### Configurando o AltaVault Appliance
{: #configuring-the-altavault-appliance}

1. É possível ligar a VM do AltaVault depois de ela ser configurada. Pode levar algum tempo para que a VM seja iniciada, pois o dispositivo AltaVault está formatando o disco de armazenamento auxiliar.

2. Efetue login no console do AltaVault usando **admin** como o **Nome de usuário** e **Senha** como a **Senha** quando o dispositivo tiver concluído o processo de inicialização. É possível mudar essas credenciais após concluir a configuração inicial. Consulte a Tabela 1: valores de configuração iniciais do AltaVault

3. Depois de efetuar login no dispositivo, será perguntado se você deseja usar o assistente para configuração. Insira *s*.
4. Use as informações na Tabela 1 depois de inserir o assistente.
5. Pressione **Enter** para salvar suas mudanças.

|Pergunta|Resposta|
|---|---|
|Etapa 1: Senha do administrador?|Insira uma nova senha do administrador (não pode ser **senha**).|
|Etapa 2: Nome do host?|Insira o nome do host apropriado.|
|Etapa 3: Usar o DHCP na interface primária?|Insira **n** ou **no**.|
|Etapa 4: Endereço IP primário?|Insira o endereço IP da rede primária. Na configuração de exemplo, essa é a rede usada para a interface de gerenciamento (por exemplo, 10.120.108.132).|
|Etapa 5: Máscara de rede?|Insira a máscara de rede (por exemplo, 255.255.255.192).|
|Etapa 6: Gateway padrão?|Insira o gateway padrão (por exemplo, 10.120.108.129).|
|Etapa 7: Servidor DNS primário?|Insira o servidor do Sistema de Nomes de Domínio (DNS) primário em seu ambiente.|
|Etapa 8: Nome do domínio?|Insira o nome de domínio de seu ambiente (por exemplo, testenv.org).|
{: caption="Tabela 1. Valores de configuração inicial de AltaVault" caption-side="top"}

### Configurando o AltaVault para o IBM Cloud Object Storage
{: #configuring-altavault-for-ibm-cloud-object-storage}

O dispositivo AltaVault deve ser conectado ao serviço {{site.data.keyword.cos_full_notm}} depois de ser configurado. Na configuração no local, o DNS público é usado para se conectar ao Object Storage Service. Nessa configuração, o nome do DNS interno é usado no lugar.

1. Abra um navegador da web e insira o endereço IP do dispositivo AltaVault.
2. Efetue login no console com as credenciais do administrador. Após o login inicial, o **Painel do assistente** será exibido.
3. Selecione **Configurações do sistema** e verifique se todas as configurações de rede estão corretas e ajuste o fuso horário para refletir o fuso horário de seu ambiente.
4. Selecione **Avançar > Salvar e aplicar > Sair**. Você retorna para o **Painel do assistente** e seu logout é efetuado da sessão do navegador atual.
5. Efetue login novamente no dispositivo AltaVault e clique em **Configurações > Assistente de configuração > Importar configuração** e especifique a localização do arquivo de configuração do dispositivo AltaVault no local.
6. Selecione **Importar apenas dados compartilhados**, que importa apenas configurações compartilhadas em comum, como configurações de nuvem e de e-mail.
7. Insira a passphrase para a chave de criptografia que é especificada durante a criação do dispositivo AltaVault no local, se um tiver sido configurado, na caixa de texto **Passphrase de chave**.
8. Clique em **Importar configuração > Sair**.

<!--The AltaVault appliance web console displays a message indicating a successful import. If the import was not successful, verify that the configuration file is in the correct format and that the passphrase is correct before trying again.-->

Você importou a configuração do dispositivo AltaVault no local para o dispositivo {{site.data.keyword.cloud_notm}} AltaVault.

Use as etapas a seguir para modificar as configurações de nuvem do dispositivo {{site.data.keywrod.cloud_notm}} AltaVault para dar a ele acesso ao serviço {{site.data.keyword.cos_full_notm}} por meio da rede privada.

1. Em AltaVault, acesse **Armazenamento > Configurações de nuvem > Nuvem** e modifique o nome do host para refletir o endereço privado do serviço {{site.data.keyword.cos_full_notm}}.
  * A sintaxe de nome da rede privada é <location>.objectstorage.service.networklayer.com, em que *<location>* designa o nome abreviado do data center (exemplo: mel01 para o data center Melbourne 01).
2. Clique em **Aplicar**. O dispositivo AltaVault tenta se conectar ao serviço {{site.data.keyword.cos_full_notm}}. Se essa tentativa de conexão falhar, verifique se as configurações do provedor em nuvem estão corretas antes de tentar se conectar novamente.

Depois de se conectar ao {{site.data.keyword.cos_full_notm}}, o dispositivo precisa ser colocado no modo de recuperação para sincronizar o conteúdo de metadados dos dados de backup originais do {{site.data.keyword.cos_full_notm}}.

Use as etapas a seguir para colocar o dispositivo AltaVault no modo de recuperação.

O dispositivo AltaVault sincroniza dados do {{site.data.keyword.cos_full_notm}}. **Nota:** o tempo que leva para concluir a sincronização depende do tamanho e do número de backups que estão sendo restaurados.

1. Acesse o dispositivo AltaVault, efetue login e insira os comandos que são mostrados na Figura 3:<br/>![Figura 3](images/veeam_restore_fig5.png)<br/>`Figure 5: Recovery mode commands`
2. Retorne para o console da web do dispositivo AltaVault após a conclusão do processo de sincronização.
3. Verifique se o **Storage Optimization Service** está **em execução** e se seu **Status** é **pronto**. Pode levar alguns minutos para que o **Status** mude para **pronto**.

### Configure o ponto de montagem CIFS/SMB em AltaVault
{: #configure-the-cifs-smb-mount-point-in-altavault}

Após a conexão interna com o {{site.data.keyword.cos_full_notm}} ser estabelecida, configure a interface **e0b** para acessar um ponto de montagem CIFS/SMB.

1. No console da web do AltaVault, acesse **Configurações > Interfaces de dados**.
2. Expanda a interface **e0b** e selecione **Ativar interface de dados**, se necessário.
3. Insira o endereço IP, a máscara de sub-rede e o gateway que você deseja usar para montar o compartilhamento CIFS/SMB. Certifique-se de usar uma sub-rede diferente daquela que é usada para a interface primária.
4. Deixe o valor padrão **MTU** de **1500 bytes**.
  * Embora a unidade máxima de transmissão padrão (MTU) seja 1.500, é possível mudá-la para 9.000 se você estiver usando quadros gigantes. Seu host ESXi e a infraestrutura física precisam suportar quadros gigantes. Por padrão, o {{site.data.keyword.cloud_notm}} suporta um tamanho de MTU de 9.000 bytes sem mudanças de configuração necessárias.
5. Clique em **Aplicar**.

Agora, o dispositivo AltaVault é configurado minimamente para permitir comunicações entre si, o {{site.data.keyword.cos_full_notm}} e o Veeam Backup and Replication.

## Veeam Backup and Replication
{: #veeam-backup-replication-recover}

O software Veeam Backup and Replication fornece recursos completos de backup, replicação e recuperação para VMs e seus dados. O Veeam Backup and Replication pode ser totalmente integrado a um AltaVault Cloud Gateway Appliance, tornando contínua a experiência de backup e recuperação.

### Implemente o Veeam Backup and Replication
{: #deploy-veeam-backup-replication}

Uma versão de avaliação do Veeam Backup and Replication Versão 8 é usada no exemplo.

#### *pré-requisitos*
{: #prerequisites-deploy-veeam-backup-replication}

Antes de continuar com a implementação, certifique-se de que os pré-requisitos a seguir sejam atendidos:

* Tenha um dispositivo AltaVault existente que esteja configurado para uso com o IBM Cloud Object Storage e o Veeam Backup and Replication.
* Obtenha uma cópia do Veeam Backup and Replication para ambientes VMware, que é um arquivo executável único. Entre em contato com o Representante do Veeam para obter uma cópia ou faça download de uma [versão de avaliação de 30 dias ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](http://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}.
* Obtenha um arquivo de licença para uso com o Veeam Backup and Replication. Na maioria dos casos, esse arquivo é enviado por e-mail para o endereço de e-mail que foi usado para fazer download do Veeam Backup and Replication. Se você não recebeu esse arquivo, entre em contato com o Representante do Veeam.<br/><br/>O arquivo de licença é usado para ativar a funcionalidade completa do Veeam Backup and Replication. Se esse arquivo não for fornecido durante a instalação do programa, todos os recursos e funcionalidades serão revertidos para aqueles da versão de avaliação de 30 dias.
* Tenha uma instância de servidor virtual (VSI) fornecida no IBM Cloud com as especificações na Tabela 2. **Nota:** o sistema operacional instalado deve ser uma versão de 64 bits.

||Mínimo|Recomendado|
|---|---|---|
|**OS**|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>Windows Server 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|<ul><li>Windows Servers 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>Windows 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|
|**Número de núcleos ou vCPUs**|2|4|
|**Memória**|4 GB de RAM de base mais 500 MB para cada tarefa de backup e restauração simultânea.|RAM de base de 16 GB mais 4 GB para cada tarefa de backup e restauração simultânea.|
|**Espaço em disco**|2 GB para instalação do produto, 10 GB por 100 VMs para a pasta do catálogo do sistema guest (dados persistentes).|2 GB para instalação do produto, 10 GB por 100 VMs para a pasta do catálogo do sistema guest (dados persistentes).|
|**Rede**|1 Gbps de LAN para backup no local e replicação, 1 Mbps de WAN para backup externo e replicação.|1 Gbps de LAN para backup no local e replicação, 1 Mbps de WAN para backup externo e replicação.|
{: caption="Tabela 2. Requisitos do sistema para a VSI do {{site.data.keyword.cloud_notm}}" caption-side="top"}

Fornecimento da VSI dentro do mesmo data center que o dispositivo AltaVault e o ambiente vSphere. A VSI deve ter acesso ao dispositivo AltaVault e ao vCenter.

### Instalando o Veeam Backup and Replication
{: #installing-veeam-backup-replication-disaster}

Use as etapas a seguir para instalar o Veeam Backup and Replication para a VSI do {{site.data.keyword.cloud_notm}} após todos os pré-requisitos serem atendidos.

1. Dê um clique duplo no executável do programa e clique em **Veeam Backup and Replication - Instalação**. O assistente de configuração é aberto.
* Clique em **Avançar** e selecione **Eu aceito os termos no contrato de licença**.
* Clique em **Avançar** e especifique a localização do arquivo de licença que é obtido em Implementar o Veeam Backup and Replication.
* Clique em **Avançar** e selecione quais componentes do Veeam Backup and Replication você deseja instalar e seus locais de instalação na tela **Recursos do programa**. **Nota:** **Veeam Backup and Replication** e **Veeam Backup Catalog** são componentes necessários.
* Clique em **Avançar**.<br/>O assistente de configuração executa uma série de verificações para assegurar que todas as estruturas do programa e os componentes de suporte necessários estejam instalados. Se algum componente estiver ausente, o assistente de configuração oferece para instalá-los. <!--Click **Install** if this is the case.-->
2. Verifique se todos os componentes foram **Aprovados** na verificação dos sistemas e clique em **Avançar**.
3. Selecione a **Conta de serviço (usuário)** na qual deseja que o serviço de backup de Veeam seja executado. A conta de serviço padrão é a **conta SISTEMA LOCAL**. Clique em **Avançar**.
4. Selecione a **Instância do SQL Server** que você deseja usar para criar e armazenar bancos de dados do Veeam Backup and Replication. Para obter mais informações, entre em contato com o administrador de banco de dados. Clique em **Avançar**.
5. Insira a **Porta de serviço do catálogo** (9393) e a **Porta de serviço de backup do Veeam** (9392). Clique em **Avançar**.
6. Selecione os diretórios nos quais você deseja armazenar o catálogo do sistema de arquivos guest (dados persistentes) e o cache de gravação do vPower NFS (dados não persistentes). Clique em **Avançar**.
7. Verifique se todas as configurações e os valores estão corretos e clique em **Instalar**. Após a instalação ser concluída, clique em **Concluir**.

### Configurando o Veeam Backup and Replication
{: #configuring-veeam-backup-replication}

Após a instalação do Veeam Backup and Replication, você está pronto para conectar-se ao dispositivo AltaVault.

1. Ative o Veeam Backup and Replication.
2. No lado inferior esquerdo da tela, clique em **Infraestrutura de backup**.
3. Na janela **Infraestrutura de backup**, clique em **Servidores gerenciados > Servidores gerenciados**.
4. No menu da faixa de opções superior, clique em **Incluir servidor** e dê um clique duplo em **VMware vSphere**.
5. Insira o endereço IP privado do servidor vSphere e clique em **Avançar**.
6. Insira as credenciais de conta de uma conta local com privilégios de administrador no servidor vSphere especificado anteriormente. **Nota:** o nome do usuário da conta deve estar no formato DOMAIN\USER para contas de domínio ou no formato HOST\USER para contas locais. Para incluir uma conta, clique em **Incluir** e insira o nome de usuário e a senha apropriados. Não mude a porta de serviços da web do VMware padrão, a menos que seu administrador de rede instrua. <!-- otherwise during [Veeam Backup & Replication installation](#_Installing_Veeam_Backup).--> Clique em **Avançar**.
  * O Veeam Backup and Replication tenta se conectar ao servidor vSphere. Se a tentativa de conexão falhar, verifique se a conta existe e tem privilégios de administrador no servidor vSphere antes de tentar novamente.
7. Clique em **Concluir**.
8. Verifique se o servidor vSphere foi incluído com êxito clicando em **Servidores gerenciados > VMware vSphere**.

### Incluindo um repositório de backup no Veeam Backup and Replication
{: #adding-a-backup-repository-to-veeam-backup-replication-disaster}

Siga estas etapas para criar um novo repositório de backup para armazenar backups restaurados no dispositivo AltaVault. **Nota:** um compartilhamento CIFS/SMB é usado para o exemplo porque o servidor de backup do Veeam Backup and Replication deve ser hospedado em uma máquina que está executando o Windows. O protocolo de compartilhamento que é usado para restaurar backups no dispositivo {{site.data.keyword.cloud_notm}} AltaVault deve ser idêntico ao protocolo de compartilhamento usado para esses mesmos backups no dispositivo AltaVault no local. Um compartilhamento CIFS/SMB é usado.

1. No canto inferior esquerdo, clique em **Infraestrutura de backup > Repositórios de backup**.
2. No menu da faixa de opções superior, clique em **Incluir repositório**.
3. Dê ao repositório um **Nome** exclusivo e forneça uma **Descrição** apropriada. Clique em **Avançar**.
4. Selecione o tipo de compartilhamento que corresponde ao tipo de compartilhamento usado no dispositivo AltaVault no local. Exemplo: se o tipo de compartilhamento usado no dispositivo AltaVault no local for CIFS/SMB, selecione **Pasta compartilhada**.
5. Especifique o local em que você deseja que os backups do compartilhamento CIFS/SMB sejam restaurados no dispositivo AltaVault. O compartilhamento tem o mesmo nome que o compartilhamento por meio do qual os backups foram processados no dispositivo AltaVault no local. Por exemplo, se o compartilhamento denominado `cifs_test1` for usado para backups no dispositivo AltaVault no local, especifique a localização do compartilhamento `cifs_test1` no dispositivo {{site.data.keyword.cloud_notm}} AltaVault.
  * Abra um navegador da web e insira o endereço IP do dispositivo AltaVault para determinar a localização.
  * Acesse **Armazenamento > CIFS** e anote o **Caminho de compartilhamento** do compartilhamento. **Nota:** isso NÃO é o mesmo que o caminho local do compartilhamento. O formato do caminho de compartilhamento é `\\<AltaVault appliance hostname>\<share name>`. Substitua o nome do host do dispositivo AltaVault nas necessidades de caminho de compartilhamento pelo endereço IP da interface de rede e0b (o ponto de montagem do compartilhamento) do dispositivo AltaVault.<br/>
   * Clique em **Configurações > Interfaces de dados** na janela de gerenciamento de dispositivo AltaVault para localizar o endereço IP da interface **e0b**. Para obter mais informações sobre como configurar montagens de IFS/SMB, consulte Configurar o ponto de montagem CIFS/SMB em AltaVault.
   * O caminho de compartilhamento especificado no Veeam Backup and Replication seria `\\10.120.108.133\cifs_test1` e não `\\restore-appliance\cifs_test1`.
6. Retorne ao Veeam Backup and Replication, insira o caminho de compartilhamento do ponto de montagem no campo **Pasta compartilhada** e clique em **Avançar**. O Veeam Backup and Replication tenta estabelecer uma conexão com o ponto de montagem. Se a conexão falhar, volte e verifique se as configurações de rede para o dispositivo AltaVault estão corretas antes de tentar novamente.
7. Insira um valor para **Limite máximo de tarefas simultâneas** para o número de recursos disponíveis na tela **Novo repositório de backup**. Esse valor é o número máximo de tarefas que um proxy de backup pode enviar para o compartilhamento selecionado. O número padrão de tarefas simultâneas é quatro. O AltaVault recomenda que você inicie com cinco tarefas simultâneas e aumente ou diminua esse valor conforme os recursos permitirem.
8. Clique em **Avançar**.
9. Especifique as configurações opcionais do vPower NFS na tela **vPower NFS**. Se a caixa de seleção **Ativar o servidor vPower NFS** for deixada desmarcada, o Veeam Backup and Replication usará o vPower para recuperação e verificação de recuperação. Clique em **Avançar**.
10. Selecione **Importar backups existentes automaticamente**, a menos que você use a seleção manual de backups da VM.
11. Verifique se todas as configurações estão corretas e clique em **Avançar**.
* Clique em **Concluir** para sair do assistente.
12. Clique em **Sim** para continuar.

O Veeam Backup and Replication está configurada e é possível iniciar a restauração de backups.

### Restaurando o ambiente
{: #restoring-the-environment}

Siga estas etapas para restaurar completamente uma restauração de VM.

1. Na parte inferior esquerda da tela, clique em **Backup e replicação**.
2. No menu da faixa de opções superior, clique em **Restaurar**.
3. Selecione **VM inteira (incluindo registro)**. **Nota:** não faça nenhuma seleção em **Restaurar da réplica**. Clique em **Avançar**.
4. Clique em **Incluir VM > Do backup**. São exibidas as VMs que foram replicadas anteriormente para o {{site.data.keyword.cos_full_notm}} por meio do dispositivo AltaVault no local e do compartilhamento CIFS/SMB selecionado. Clique em **Incluir > Avançar**.
5. Restaure as VMs para uma nova localização  <! -- [ porque o local original da VM (s) falhou ] -- >  selecionando **Restaurar para uma nova localização ou com configurações diferentes**. Clique em **Avançar**.
6. Na tela **Host**, selecione um host de destino para cada VM que você está restaurando. Selecione uma VM e, em seguida, clique em **Host**.
7. Selecione o host de destino por meio de **Selecionar host** e clique em **OK**.<br/>O Veeam Backup and Replication tenta entrar em contato com os hosts de destino para certificar-se de que eles estejam on-line e prontos para receber dados. Se essa tentativa falhar, verifique se todas as configurações de rede estão corretas antes de tentar novamente. Após a conclusão da verificação, clique em **Avançar**.
8. Selecione um **Conjunto de recursos** opcional para cada VM. Clique em **Avançar**.
9. Selecione um armazenamento de dados de destino e um tipo de disco para cada VM que você está restaurando. Clique em **Avançar**.<br/>O Veeam Backup and Replication tenta validar os armazenamentos de dados de destino. Se a validação falhar, verifique se o armazenamento de dados tem capacidade suficiente para as VMs que estão sendo restauradas e verifique todas as configurações de rede antes de tentar se conectar novamente.
10. Selecione uma pasta de destino nos armazenamentos de dados de destino para cada VM que você deseja restaurar. Clique em **Avançar**.
11. Especifique as conexões de rede e as configurações para cada VM. Clique em **Avançar**.
12. Insira um **Motivo de restauração** opcional fornecendo uma razão pela qual você está executando a operação de restauração. Clique em **Avançar**.<br/>O Veeam Backup and Replication tenta validar as VMs que você está restaurando. Aguarde até que essa validação seja concluída com êxito antes de continuar.
13. Verifique as operações de restauração e as configurações na janela **Backup e replicação** e clique em **Concluir**. O Veeam Backup and Replication começa automaticamente a restaurar as VMs selecionadas.

## Etapas Seguintes
{: #next-steps-recovering-your-vmware-vsphere-environment}

<!--The recent rise in Object Storage can be heavily attributed to enterprises' desire to reduce or even eliminate the operational and capital expenditures associated with comparatively expensive on-premises storage. While Object Storage is a highly cost-effective solution, it introduces a new problem of how to access stored files. In the case of stored data backups, the issue is how to use them to recover an environment that has failed for whatever reason.-->

<!--This problem is handily resolved by Veeam Backup & Replication, along with a NetApp AltaVault Cloud Gateway appliance, and {{site.data.keyword.cos_full_notm}}. When used together, they create a seamless backup and recovery experience that can be tailored to fit a wide variety of environmental requirements and constraints. By following the steps outlined in this procedure, enterprises can quickly realize the benefits of cloud-based storage, while reducing the complexities involved in shifting from on-premises storage to Object Storage using the three aforementioned technologies.-->

Para obter mais informações sobre os componentes dessa solução híbrida, consulte:

* [Website do NetApp AltaVault ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](http://www.netapp.com/us/products/protection-software/altavault/){: new_window}
* [Website do Veeam Backup and Replication ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}
* [Website do IBM Cloud Object Storage ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://www.ibm.com/cloud/storage){: new_window}

*Servidor de utilitário* refere-se a um servidor que contém o VMware vSphere Client que é usado para acessar um ambiente privado e por meio do qual o dispositivo AltaVault que é usado para operações de recuperação de dados é implementado.
