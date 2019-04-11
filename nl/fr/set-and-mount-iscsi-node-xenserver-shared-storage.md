---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-16"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Configuration et montage d'un noeud iSCSI dans un stockage partagé XenServer
{: #setting-up-and-mounting-an-iscsi-node-in-xenserver-shared-storage}

Le stockage partagé est un référentiel de stockage de n'importe quelle forme qui peut être utilisé par XenServer. On distingue deux catégories de référentiel de stockage, partagé et non partagé. Un référentiel de stockage partagé permet à plusieurs noeuds de partager un emplacement de stockage à partir du même pool. {{site.data.keyword.cloud}} prend en charge deux options de référentiel de stockage, NFS et LVMoiSCSI (LVM over iSCSI).

Vous devez installer un serveur NFS et le gérer à partir d'un autre système client ou à l'aide d'un serveur {{site.data.keyword.cloud_notm}} QuantaStor. Procédez comme suit pour monter le référentiel NFS dans XenCenter :

1. Ouvrez la console XenCenter. 
2. Cliquez sur **New Storage**.
* Dans la nouvelle boîte de dialogue, sélectionnez **Virtual disk storage > NFS**.
* Entrez un nom approprié pour le nouveau référentiel de stockage. 
* Entrez le chemin d'accès au serveur sous **Share Name**
* Cliquez sur **Scan**

Ce processus recherche d'éventuels référentiels de stockage précédents sur le partage NFS. 

Vous pouvez utiliser l'offre {{site.data.keyword.blockstoragefull}} pour LVMoiSCSI. Le stockage iSCSI peut provenir d'un serveur de stockage géré par un client ou d'une offre {{site.data.keyword.blockstoragefull}}. Pour Performance and Redundant Block Storage, extrayez le nom qualifié iSCSI du portail {{site.data.keyword.slportal}} en accédant à **Stockage > Stockage par blocs > Sélectionner un numéro d'unité logique**. Pour monter le noeud iSCSI via XenCenter, procédez comme suit :

1. Ouvrez la console XenCenter. 
2. Accédez à **General > Properties** et définissez le nom qualifié iSCSI. 
3. Cliquez sur **New Storage**
4. Dans la nouvelle boîte de dialogue, sélectionnez **Virtual disk storage > Software iSCSI**.
5. Entrez un nom approprié pour le nouveau référentiel de stockage. 
6. Entrez le nom d'hôte ou l'adresse IP de la cible iSCSI et conservez le port par défaut 3260.
7. Sélectionnez **Use CHAP**.
8. Entrez le nom d'utilisateur et le mot de passe pour le numéro d'unité logique. 
9. Cliquez sur **Scan Target Host**.
* Sélectionnez une option **Target IQNs**. 
* Sélectionnez une option **Target Luns**. 
10. Une fois les zones IQN et Lun renseignées, cliquez sur **Finish**. D'éventuels référentiels de stockage précédents sont recherchés sur la cible. Si un référentiel de stockage est détecté, le programme d'installation vous demande si vous souhaitez en créer un nouveau ou si vous souhaitez établir une connexion avec ce référentiel existant. 

Si les serveurs sont regroupés dans un pool, le référentiel iSCSI est automatiquement partagé. 

Pour plus d'informations sur la configuration de iSCSI pour multi-accès, voir  [Configuring iSCSI Multipath ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus1000/sw/5_x/sys_mgmt_config/b_Cisco_N1KV_VMware_Sys_Mgmt_Config_5x/b_Cisco_N1KV_VMware_Sys_Mgmt_Config_5x_chapter_01110.html?dtid=osscdc000283){: new_window}
