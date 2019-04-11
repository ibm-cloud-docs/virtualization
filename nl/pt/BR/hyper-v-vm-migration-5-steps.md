---
copyright:
  years: 2014, 2018
lastupdated: "2018-06-22"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Migrando uma máquina virtual Hyper-V
{: #migrating-a-hyper-v-virtual-machine}

## Antes de iniciar
{: #before-you-begin-migrating-a-hyper-v-virtual-machine}

Antes de iniciar, revise os pré-requisitos a seguir.

* Um servidor compatível (Windows 2008 Server DC Edition w/Hyper-V).
* Um método de conexão do servidor de origem para o servidor de destino.
* O servidor de origem e de destino devem residir na mesma VLAN. Se eles não residirem na mesma VLAN, a nova emissão de um IP para a máquina virtual será necessária (requer a abertura de um chamado de suporte).
* Máquinas virtuais com uplinks de rede funcionais e software funcional/suportado instalado.

## Por que você pode precisar realocar a sua máquina virtual Hyper-V
Há dois motivos comuns para a necessidade de realocação de sua máquina virtual Hyper-V:
* A VM está no hardware que não está funcionando corretamente,
* O servidor host atual está ficando sem recursos.
Em qualquer um dos eventos, a migração do Hyper-V pode ser concluída rapidamente se você tiver os requisitos mencionados anteriormente, Conclua as etapas a seguir.

1. Efetue login no servidor de origem e abra o Hyper-V Manager. Selecione a máquina virtual que você deseja migrar para o servidor de destino.
2. Encerre a máquina virtual que você deseja migrar. Em seguida, selecione **Exportar** na lista de ações do servidor e insira a localização do arquivo de exportação.
3. Agora, usando o RDP no servidor de origem, é possível efetuar login no servidor de destino com a unidade C: montada para trazer o arquivo.

Você está transferindo o arquivo por meio de uma montagem de recurso usando RDP. É possível escolher o método de transferência mais confortável para você para esse processo (compartilhamento do Windows, montagem de recursos por meio de RDP, FTP e outros métodos de transferência).
{:tip}

4. Com o arquivo sendo exportado para o servidor de destino, certifique-se de que ele esteja na localização padrão para os discos rígidos virtuais Hyper-V (VHD) e importe-o. A localização padrão é `C:\Users\Public\Documents\Hyper-V\Virtual hard disks`. Se você mudou essa localização padrão e não se lembra dela, será possível selecionar **Configurações do Hyper-V > Importar máquina virtual** para visualizar a localização.
5. Depois de localizar o arquivo exportado e clicar em **Importar**, a máquina virtual é preenchida em seu Hyper-V Manager com todas as configurações e arquivos que tinha anteriormente. Agora, o servidor está on-line e funcionando. Se o seu servidor não corresponder aos requisitos e o servidor de destino residir em uma VLAN diferente, será necessário executar novamente o IP na máquina virtual com uma sub-rede móvel (roteada como secundária na VLAN).
