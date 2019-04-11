---
copyright:
  years: 2014, 2018
lastupdated: "2018-02-05"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Atualizando o aplicativo Veeam Backup and Replication
{: #updating-veeam-backup-and-replication-application}

## Visão geral
{: #overview}

Use as etapas a seguir para atualizar o aplicativo Veeam Backup and Replication que está em execução em seu servidor quando novas atualizações para o produto estiverem disponíveis.

### Pré-requisitos
{: #prerequisites-updating-veeam-backup-and-replication-application}

* Uma conta grátis do Veeam. É possível se inscrever no [Veeam Registration ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://www.veeam.com/signin.html).
* Internet Explorer, Chrome, Firefox ou Safari.

### Verificando atualizações
{: #checking-for-updates}

O aplicativo Veeam Backup and Replication inclui um verificador de atualização. Para certificar-se de que você seja notificado sobre atualizações, acesse o ícone de menu superior esquerdo e clique em **Opções gerais** > **Notificações**. Certifique-se de que **Verificar atualizações do produto e do hypervisor periodicamente** esteja selecionado.

### Atualizando o Veeam
{: #updating-veeam}

Para iniciar a atualização, siga estas etapas.
1. Abra a visualização **Infraestrutura de backup**.
2. Na área de janela de inventário, acesse **Nó de servidores gerenciados** > **Atualizações ausentes**.
3. Clique no link de atualização.

Antes de iniciar a atualização, observe o seguinte:

* Certifique-se de que a execução atual para todas as tarefas existentes esteja concluída. Se algumas tarefas falharam, execute novamente as tarefas com falha.
* Certifique-se de que nenhuma tarefa esteja em execução, incluindo as sessões de restauração, as sessões de recuperação de VM instantânea e as tarefas do SureBackup. É recomendado que você não pare a execução de tarefas.
* Desative temporariamente qualquer tarefa de cópia periódica e de backup para evitar que elas iniciem durante o upgrade.

### Etapas Seguintes
{: #next-steps-updating-veeam-backup-and-replication-application}

Agora é possível extrair a atualização transferida por download e dar um clique duplo no instalador de atualização do Veeam. A atualização é enviada por push para o servidor do Veeam local e para os hypervisores nos quais está o agente do Veeam. Após a conclusão do processo, inicie o Veeam Backup and Replication e reative suas tarefas de backup.
