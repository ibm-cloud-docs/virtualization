---
copyright:
  years: 2014, 2018
lastupdated: "2018-08-14"

subcollection: virtualization

keywords: Citrix XenServer
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Incluindo uma unidade de disco rígido no Citrix XenServer
{: #adding-a-hard-disk-drive-to-citrix-xenserver}

A inclusão de nova unidade de disco rígido no XenServer é diferente do processo do Linux tradicional. Para o XenServer, é necessário criar um contêiner chamado de "repositório de armazenamento" para definir um destino de armazenamento específico (como um disco rígido). Este contêiner é onde as imagens de disco virtual (VDIs) de VMs são armazenadas. Uma VDI é um espaço de armazenamento abstrato que age como o disco rígido para VMs.

O repositório de armazenamento Xen suporta unidades IDE, SATA, SCSI e SAS quando conectado localmente, além de iSCSI, NFS, SAS e Fibre Channel para armazenamento remoto.

## Criando um repositório de armazenamento em um XenServer
{: #creating-a-storage-repository-in-a-xenserver}

1. SSH para o XenServer como raiz.

2. Localize o ID do disco do novo dispositivo usando o comando a seguir:

       # cat /proc/partitions

  As listas de comandos de todas as unidades de disco rígido e partições. Localize seu novo disco local, que provavelmente é "sdx" (mais provavelmente sdb) ou '/cciss/c0d1p0'. Use o comando a seguir para listar os IDs de disco de todas as partições/unidades de disco rígido presentes no servidor.

       # ll /dev/disk/by-id

  Localize o ID de disco do disco “sdx” ou 'cciss/c0d1'. O formato“scsi-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx”ou“cciss-xxxxxxxxxxxxxxxxxxxxxxxxxx”é o que você precisa.

3. Localize o 'host-uuid' em um XenServer usando o comando a seguir:

        #xe host-list

  O uuid (RO) é o 'host-uuid' de que você precisa.

4. Crie um repositório de armazenamento (SR):

  > **Nota:** o comando [sr-create ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](http://support.citrix.com/article/CTX121313){: new_window} é para incluir uma nova unidade de disco rígido. A criação de uma nova unidade de disco rígido é um processo destrutivo que particiona e formata a unidade, causando a perda de todos os dados na unidade. Se você desejar reintroduzir uma unidade que tenha dados existentes, use [sr-introduce ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](http://support.citrix.com/article/CTX121896){: new_window}.

  `# xe sr-create content-type=user device-config:device=/dev/disk/by-id/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`<br/>
  \- Or -<br/>
  `# xe sr-create content-type=user device-config:device=/dev/disk/by-id/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`<br/>
  \- Or -<br/>
  `# xe sr-create content-type=user device-config:device=/dev/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`

## Verificando o repositório de armazenamento no XenCenter
{: #verifying-the-storage-repository-in-xencenter}

É possível verificar o repositório de armazenamento por meio do XenCenter seguindo estas etapas:

1. Conecte-se ao XenCenter.

2. Acesse **Armazenamento** para localizar os detalhes de todos os repositórios de armazenamento. É possível ver o armazenamento 'Local Storage2' na lista que mostra que o disco foi incluído. Agora é possível iniciar a criação de VDIs nele.
