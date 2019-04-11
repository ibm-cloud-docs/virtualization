---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization
---

# Guia de referência rápida do Parallels Virtuozzo Containers 4.0
{: #parallels-virtuozzo-containers-4-0-quick-reference-guide}

Use estas informações para localizar os utilitários de contêiner e os termos comuns do Virtuozzo.

## Utilitários gerais
{: #general-utilities}

Os utilitários gerais são para tarefas de manutenção do dia-a-dia.

|Utilitários gerais||
|---|---|
|Vzctl|Utilitário para controlar contêineres|
|vzlist|Utilitário para visualizar uma lista de contêineres existentes no nó com informações adicionais.|
|vzquota|Utilitário para controlar cotas de disco do Virtuozzo Containers.|
{: caption="Tabela 1. Utilitários gerais" caption-side="top"}

## Utilitários de licenciamento
{: #licensing-utilities}

Os utilitários de licenciamento são usados para instalar uma nova licença, visualizar o estado da licença ou gerar uma solicitação de licença para uma nova licença.

|Utilitários de licenciamento||
|---|---|
|vzlicview|Utilitário para exibir o status da licença e os parâmetros do Virtuozzo.|
|vzlicload|Utilitário para gerenciar licenças do Virtuozzo no nó do hardware.|
|vzlicupdate|Utilitário para ativar a instalação do Virtuozzo Containers, atualizar as licenças do Virtuozzo instaladas no nó do hardware ou transferir a licença do Virtuozzo por meio do nó de origem para o nó de destino.|
{: caption="Tabela 2. Utilitários de licenciamento" caption-side="top"}

## Utilitários de migração de contêiner
{: #container-migration-utilities}

As ferramentas de migração do contêiner são usadas para migrar contêineres entre os nós de hardware ou dentro de um nó de hardware.

|Utilitários de migração de contêiner||
|---|---|
|vzmigrate|Utilitário para migrar contêineres de um nó de hardware para outro.|
|vzmlocal|Utilitário para a clonagem ou movimentação local dos contêineres.|
|vzp2v|Utilitário para migrar um servidor físico para um contêiner no nó.|
|vzv2p|Utilitário para migrar um contêiner para um servidor físico.|
{: caption="Tabela 3. Utilitários de migração de contêiner" caption-side="top"}

## Utilitários de backup de contêiner
{: #container-backup-utilities}

Os utilitários de backup de contêiner são usados para fazer backup e restaurar as áreas privadas do contêiner, os arquivos de configuração, os scripts de ação e as informações de cota.

|Utilitários de backup de contêiner||
|---|---|
|vzbackup|Utilitário para fazer backup de contêineres.|
|vzrestore|Utilitário para restaurar contêineres submetidos a backup.|
|vzabackup|Utilitário para fazer backup de nós de hardware e seus contêineres. Esse utilitário requer o software Parallels Agent.|
|vzarestore|Utilitário para restaurar nós de hardware e contêineres submetidos a backup. Esse utilitário requer o software Parallels Agent.|
{: caption="Tabela 4. Utilitários de backup de contêiner" caption-side="top"}

## Utilitários de gerenciamento de modelo
{: #template-management-utilities}

As ferramentas de gerenciamento de modelo são usadas para criar modelos e para manter e instalar aplicativos em um contêiner.

|Utilitários de gerenciamento de modelo||
|---|---|
|vzpkg|Utilitário para gerenciar os modelos de S.O. e do aplicativo EZ dentro de seus contêineres ou no próprio nó de hardware.|
|vzmktmpl|Utilitário para criar modelos de S.O. e do aplicativo EZ.|
|vzveconvert|Utilitário para converter contêineres com base em modelos padrão do Virtuozzo para contêineres baseados em modelo do EZ.|
|vzpkgproxy|Utilitário para criar servidores proxy de armazenamento em cache para manipular modelos de S.O. e de aplicativo EZ.|
|vzrhnproxy|Utilitário para criar servidores proxy RHN para manipular os pacotes incluídos nos modelos RHEL 4 e RHEL 5 OS EZ.|
|vzpkgls|Utilitário para obter uma lista de modelos disponíveis no nó de hardware e em contêineres.|
|vzpkginfo|Utilitário para obter as informações sobre qualquer modelo instalado no nó de hardware.|
|vzpkgcreat| Crie um novo conjunto de pacotes por meio de arquivos binários RPM ou DEB.|
|vzpkgadd|Utilitário para incluir um modelo em um contêiner.|
|vzpkglink|Utilitário para substituir arquivos reais dentro de um contêiner por symlinks para os arquivos no nó.|
|vzpkgrm|Utilitário para remover um modelo de um contêiner.|
|vzpkgcache|Atualize um conjunto de archives de contêiner pré-instalados após a instalação do novo modelo.|
{: caption="Tabela 5. Utilitários de gerenciamento de modelo" caption-side="top"}

## Ferramentas complementares
{: #supplementary-tools}

As ferramentas complementares são usadas para tarefas diversas no nó de hardware e no contexto do contêiner.

|Ferramentas complementares||
|---|---|
|vzup2date|Utilitário para atualizar o software Virtuozzo e os modelos.|
|vzup2date-mirror|Utilitário para criar espelhos locais do repositório oficial do Virtuozzo.|
|vzfsutil|Utilitário para a otimização do VZFS e a verificação de consistência.|
|vzcache|Utilitário para obter espaço em disco extra, armazenando em cache os arquivos idênticos em contêineres diferentes.|
|vzsveinstall|Utilitário para criar o contêiner de serviço no nó de hardware.|
|vzsveupgrade|Utilitário para atualizar os pacotes dentro do contêiner de serviço.|
|vzps|O utilitário está funcionando como os utilitários padrão e os utilitários principais, com as funções relacionadas ao contêiner incluídas.|
|vztop|O utilitário está funcionando como os utilitários padrão e os utilitários principais, com as funções relacionadas ao contêiner incluídas.|
|vzsetxinetd|Utilitário para alternar alguns serviços entre o modo independente e o modo `xinetddependent`.|
|vzdqcheck|Imprimir o uso atual do espaço no arquivo do ponto de vista da cota.|
|vzdqdump|Utilitário para fazer dump dos limites de cota do usuário ou do grupo do contêiner e dos tempos de carência por meio do kernel ou do arquivo de cota ou para carregá-los para um arquivo de cota.|
|vzdqload|Utilitário para fazer dump dos limites de cota do usuário ou do grupo do contêiner e dos tempos de carência por meio do kernel ou do arquivo de cota ou para carregá-los para um arquivo de cota.|
|vznetstat|Utilitário que imprime estatísticas de uso de tráfego de rede por contêineres.|
|vzcpucheck|Utilitário para verificar uso da CPU por contêineres|
|vzmemcheck|Utilitário para verificar os parâmetros de memória atual do nó de hardware e do contêiner.|
|vzcalc|Utilitário para calcular o uso de recursos por um contêiner.|
|vzcheckovr|Utilitário para verificar o comprometimento excessivo do sistema atual e a segurança do total de configurações de controle de recurso.|
|vzstat|Utilitário para monitorar o nó de hardware e o consumo de recursos de contêiner em tempo real.|
|vzpid|Utilitário que imprime o ID do contêiner ao qual o processo pertence.|
|vzsplit|Utilitário para gerar o arquivo de configuração de contêiner de amostra, "dividindo" o nó de hardware em partes iguais.|
|vzcfgscale|Utilitário para escalar a configuração do contêiner.|
|vzcfgvalidate|Utilitário para validar a exatidão do arquivo de configuração do contêiner.|
|vzcfgconvert|Utilitário para converter arquivos de configuração de contêiner do Virtuozzo 2.0.2 no formato Virtuozzo 2.5.x.|
|vzstatrep|Utilitário para analisar os logs que são coletados pelo vzlmond e para gerar relatórios de estatísticas com base nesses logs (no formato de texto e de gráfico).|
|vzreport|Utilitário para elaborar um relatório de problemas e enviá-lo automaticamente para a equipe de suporte do Parallels.|
|vzhwcalc|Utilitário para varrer os principais recursos em qualquer servidor Linux e criar um arquivo no qual essas informações são especificadas.|
|vzveconvert|Utilitário para converter os contêineres com base nos modelos de S.O. padrão do Virtuozzo para os modelos baseados em modelo EZ.|
|vznetcfg|Utilitário para gerenciar dispositivos de rede no nó de hardware.|
|vzmtemplate|Utilitário para migrar os modelos de S.O. instalados e de aplicativo de um nó de hardware para outro.|
{: caption="Tabela 6. Ferramentas suplementares" caption-side="top"}

## Termos comuns
{: #common-terms}

|Termos comuns||
|---|---|
|Nó de hardware|O nó de hardware (ou HN) é um computador físico que hospeda contêineres.|
|Contêiner|O contêiner é funcionalmente idêntico a um servidor independente isolado com seus próprios endereços IP, processos, arquivos, usuários, sua própria versão, seus próprios arquivos de configuração, seus próprios aplicativos, bibliotecas do sistema e outras funções do servidor. Os contêineres compartilham nós de hardware e o mesmo kernel do S.O., mas eles estão isolados um do outro. Cada contêiner tem um ID numérico exclusivo que inicia com 1.|
|Sistema operacional do host|O sistema operacional do host (ou host) é um sistema operacional instalado no nó de hardware. Ele também é chamado de contêiner 0, em contraste com contêineres reais que têm IDs que iniciam com 1.|
|Modelo|O modelo (ou conjunto de pacotes) é um conjunto de arquivos de aplicativos originais que são reempacotados para montagem sobre o Virtuozzo File System (VZFS). Dois tipos de modelos estão disponíveis no Virtuozzo: modelos de S.O. que podem ser usados para criar novos contêineres em seus modelos de base e aplicativos que podem ser incluídos em contêineres após a sua criação.|
|PIM|O Parallels Infrastructure Manager (ou PIM) é uma ferramenta de gerenciamento baseada na web projetada para administradores do host.|
|PMC|O Parallels Management Console (ou PMC) é uma ferramenta de gerenciamento remoto com uma interface gráfica com o usuário projetada para administradores do host.|
|PPP|O Parallels Power Panel é uma ferramenta de gerenciamento baseada na web projetada para proprietários de contêiner.|
{: caption="Tabela 7. Termos comuns" caption-side="top"}
