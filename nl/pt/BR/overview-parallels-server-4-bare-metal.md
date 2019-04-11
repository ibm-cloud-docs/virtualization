---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Visão geral do servidor Parallels Server 4 Bare Metal
{: #overview-of-parallels-server-4-bare-metal}

O Parallels Server Bare Metal é uma solução de virtualização que fornece virtualização de hardware juntamente com virtualização de software do Virtuozzo, fornecendo tecnologia para máquinas virtuais e contêineres*.
{:shortdesc}

**Interface da linha de comandos**

Junto com os comandos comuns do Virtuozzo, outros comandos estão disponíveis: `pctl`, `pmigrate`, `pstat` e `prl_disk_tool`.

**Migração**

Agora é possível migrar o Virtuozzo Containers para máquinas virtuais, servidores físicos para o Virtuozzo Containers e servidores físicos para máquinas virtuais para permitir a consolidação de serviços. Essa funcionalidade também permite migrar contêineres ou VMs entre servidores bare metal, converter VMs que foram criadas em diferentes ambientes virtualizados (V2V) e mudar os SIDs do Windows ao clonar ou implementar uma VM do Windows por meio de um modelo.

**Uso de endereço IP e VLANs**

É possível designar VMs a um IP por meio do servidor físico usando o Parallels Tools que está na VM. Além disso, o PSBM permite que comutadores virtuais e VLANs sejam criados dentro das VMs para proteger melhor o tráfego de rede intra-VM.

**Executando comandos**

É possível executar comandos brutos do servidor físico diretamente dentro dos contêineres Virtuozzo e agora dentro de VMs (quando o Parallels Tools está instalado dentro da VM), incluindo reconfigurações de senha do usuário.

**Contabilidade de processo**

Use o Parallels Server Bare Metal para aumentar e reduzir a prioridade de recursos alocados (CPU, prioridade de E/S de disco) para uma VM rapidamente.

**Backup**

O Parallels Server Bare Metal fornece a funcionalidade para fazer backup e restaurar VMs e contêineres no servidor bare metal local ou em um servidor bare metal remoto para incluir backups completos e incrementais.

**Contabilidade de rede**

Use o Parallels Server Bare Metal para visualizar e localizar facilmente VMs ou contêineres com base no rendimento de rede atual e histórico.

**Rede**

O Parallels Server Bare Metal usa endereços IP móveis, enquanto o Virtuozzo usa endereços IP móveis ou estáticos (dependendo da configuração).

\* O {{site.data.keyword.BluSoftlayer_full}} licencia apenas máquinas virtuais de hardware no Parallels Server 4 Bare Metal, a menos que indicado de outra forma no formulário de pedido.
_VM_ = máquina virtual. _V_ = VPS ou contêiner.
