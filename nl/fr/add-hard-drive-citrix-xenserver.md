---
copyright:
  years: 2014, 2018
lastupdated: "2018-08-14"

subcollection: virtualization

keywords: Citrix XenServer
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Ajout d'une unité de disque dur à Citrix XenServer
{: #adding-a-hard-disk-drive-to-citrix-xenserver}

Le processus d'ajout d'une unité de disque dur dans XenServer est différent du processus Linux traditionnel. Pour XenServer, vous devrez créer un conteneur appelé "référentiel de stockage" pour définir une cible de stockage spécifique (un disque dur, par exemple). Ce conteneur est l'emplacement dans lequel les images de disque virtuel des machines virtuelles sont stockées. Une image de disque virtuel est un espace de stockage abstrait qui agit comme un disque dur pour des machines virtuelles. 

Votre référentiel de stockage prend en charge des unités IDE, SATA, SCSI et SAS lorsqu'il est connecté localement, en plus des unités iSCSI, NFS, SAS et Fibre Channel pour le stockage distant. 

## Création d'un référentiel de stockage dans un serveur XenServer
{: #creating-a-storage-repository-in-a-xenserver}

1. Ouvrez une session SSH sur le serveur XenServer en tant qu'utilisateur root.

2. Localisez l'ID de disque de la nouvelle unité en exécutant la commande suivante :

       # cat /proc/partitions

  Cette commande répertorie tous les disques durs et toutes les partitions. Localisez votre nouveau disque local, qui est probablement “sdx” (ou sdb) ou '/cciss/c0d1p0'. Utilisez la commande suivante pour répertorier les ID de disque pour l'ensemble des partitions/disques durs existant sur le serveur :

       # ll /dev/disk/by-id

  Localisez l'ID de disque du disque "sdx" ou 'cciss/c0d1'. Le format "scsi-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" ou "cciss-xxxxxxxxxxxxxxxxxxxxxxxxxx" doit être utilisé. 

3. Localisez l'élément 'host-uuid' dans un serveur XenServer en exécutant la commande suivante :

        #xe host-list

  L'identificateur unique universel (RO) est l'élément 'host-uuid' dont vous avez besoin.

4. Créez un référentiel de stockage :

  > **Remarque :** la commande [sr-create ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://support.citrix.com/article/CTX121313){: new_window} permet d'ajouter une nouvelle unité de disque dur. La création d'une nouvelle unité de disque dur est un processus destructeur qui partitionne et formate l'unité, et les données sur le disque sont perdues. Si vous souhaitez réintroduire une unité contenant déjà des données, utilisez la commande [sr-introduce ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://support.citrix.com/article/CTX121896){: new_window}.

  `# xe sr-create content-type=user device-config:device=/dev/disk/by-id/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`<br/>
  \- Ou -<br/>
  `# xe sr-create content-type=user device-config:device=/dev/disk/by-id/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`<br/>
  \- Ou -<br/>
  `# xe sr-create content-type=user device-config:device=/dev/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`

## Vérification du référentiel de stockage dans XenCenter
{: #verifying-the-storage-repository-in-xencenter}

Vous pouvez vérifier le référentiel de stockage à partir de XenCenter en procédant comme suit 

1. Connectez-vous à XenCenter.

2. Accédez à **Storage** pour prendre connaissance des détails relatifs à tous les référentiels de stockage. L'espace de stockage 'Local Storage2' est répertorié dans la liste, ce qui indique que le disque a été ajouté. Vous pouvez à présent commencer à créer des images de disque virtuel sur le disque. 
