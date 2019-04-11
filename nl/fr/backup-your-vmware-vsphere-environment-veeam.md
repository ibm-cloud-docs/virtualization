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

# Sauvegarde de votre environnement VMware vSphere à l'aide de Veeam
{: #backing-up-your-vmware-vsphere-environment-by-using-veeam}

Vous pouvez sauvegarder votre environnement VMware vSphere à l'aide d'une solution hybride composée des services suivants :

* Service {{site.data.keyword.cloud}} Object Storage
* NetApp AltaVault Cloud Storage Gateway
* Logiciel Veeam Backup & Replication

Veeam Backup & Replication active une solution hybride qui inclut un dispositif de stockage intégré dans le cloud NetApp AltaVault et {{site.data.keyword.cos_full}}. Le logiciel crée, gère et restaure des environnements virtuels à partir de sauvegardes. Lorsque vous l'utilisez avec un dispositif de stockage intégré dans le cloud NetApp AltaVault, vous créez des sauvegardes qui sont stockées localement (sur site). La sauvegarde est également répliquée simultanément sur {{site.data.keyword.cos_full_notm}}. Avec cette solution hybride, deux copies de sauvegarde sont créées, mais une seule existe localement. 

## AltaVault Cloud-Integrated Storage Gateway
{: #altavault-cloud-integrated-storage-gateway}

Vous pouvez utiliser AltaVault Cloud Storage Gateway pour intégrer votre environnement sur site au cloud sans avoir à écrire des scripts ou des applications en utilisant des API REST pour {{site.data.keyword.cos_full_notm}}. Vous pouvez monter ou pointer vers les points de montage et commencer la copie des données dans le cloud en toute sécurité.

### Déploiement de AltaVault sur site
{: #deploying-altavault-on-premises}

Pour déployer AltaVault en tant que solution de sauvegarde sur site sur {{site.data.keyword.cos_full_notm}}, procédez comme suit :

Vous pouvez acheter AltaVault en tant que dispositif physique ou dispositif virtuel. Le déploiement de la version d'évaluation du dispositif virtuel AltaVault basé sur VMware vSphere ESXi est présenté dans cette procédure.
{:tip}

<a name="prerequisites"></a>
#### Prérequis

Vérifiez que vous disposez des éléments prérequis suivants :

* Une copie du dispositif virtuel AltaVault. Il s'agit d'un fichier unique avec une extension de fichier OVA. Contactez votre représentant NetApp pour obtenir le dispositif ou téléchargez une version d'évaluation de 90 jours à partir du site Web [NetApp AltaVault ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](http://www.netapp.com/us/products/protection-software/altavault/){: new_window}.
* Un environnement vSphere ESXi 5.5 sur site avec la quantité minimale d'unité centrale, de mémoire et d'espace disque requise pour le dispositif AltaVault. Si vous utilisez la version d'évaluation, ces exigences minimales sont les suivantes : quatre unités centrales virtuelles (vCPU), 24 Go de mémoire et jusqu'à 8 To d'espace disque. 
* Deux contrôleurs NIC 10 Gbps disponibles dans l'environnement vSphere. Un contrôleur NIC est utilisé pour l'entrée de données et l'autre est utilisé pour la réplication de données sur {{site.data.keyword.cos_full_notm}}.
* Deux réseaux correspondant aux deux contrôleurs NIC (VLAN) qui sont définis dans l'environnement vSphere. Le réseau de réplication ne peut pas être affecté au même réseau que le réseau d'entrée de données, car cela risquerait de créer une boucle de routage. 
* Un ensemble de données d'identification {{site.data.keyword.cos_full_notm}}. Ces données d'identification incluent notamment un nom d'utilisateur {{site.data.keyword.cloud_notm}}, un nom d'utilisateur {{site.data.keyword.cos_full_notm}} et la clé d'API qui est associée au nom d'utilisateur {{site.data.keyword.cloud_notm}}. 
* Connaissance de la terminologie VMware Sphere et de l'administration des environnements vSphere ESXi. Cela inclut, mais pas exclusivement, l'utilisation du client Web vSphere, du client vSphere et l'affectation de ressources matériel dont la mise en réseau et le stockage. 

### Déploiement du package OVA AltaVault
{: #deploying-altavault-ova}

Vous pouvez déployer le package OVA AltaVault sur l'environnement vSphere lorsque toutes les conditions prérequises sont remplies. Vous trouverez les instructions relatives au déploiement du package OVA dans le document [NetApp AltaVault Installation and Service Guide ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}.

1. Editez la machine virtuelle AltaVault une fois le déploiement du package OVA terminé. 
2. Modifiez la mémoire allouée de sorte qu'elle corresponde à la version d'AltaVault qui se trouve dans la fenêtre d'édition. Si vous utilisez la version d'évaluation, affectez 24 Go de mémoire et ajoutez un disque inférieur ou égal à 8 To. **Remarque :** cette unité de stockage sur disque secondaire est utilisée pour stocker les données de sauvegarde dédoublonnées. 
* Prenez soin d'affecter différents réseaux (VLAN) au dispositif AltaVault après la modification des configurations de mémoire et de disque. 

Les fonctions d'interface suivantes sont affectées aux contrôleurs NIC :

* Principale. Utilisée comme interface de gestion. 
* e0a. Interface qui est utilisée pour répliquer des données depuis le dispositif AltaVault vers le cloud.
* e0b. Interface qui est utilisée pour exporter le point de montage pour le partage SMB/CIFS ou NFS. 
* e0c. Interface facultative qui est utilisée pour exporter le point de montage pour le partage SMB/CIFS ou NFS. 

Dans cet exemple de configuration, le dispositif AltaVault utilise l'interface **e0a** comme interface de réplication vers le cloud et l'interface **e0b** pour exporter un point de montage CIFS/SMB. **Remarque :** vous ne pouvez pas utiliser à la fois un partage CIFS/SMB et un partage NFS pour accéder aux mêmes données. Ainsi, si des données sont placées dans un partage CIFS/SMB, vous ne pouvez pas y accéder au moyen d'un partage NFS et réciproquement. 

Pour plus d'informations sur le déploiement du dispositif AltaVault et sur la configuration des paramètres de machine virtuelle pour le dispositif, voir le document [NetApp AltaVault Installation and Service Guide ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}.

### Configuration initiale du dispositif AltaVault <!--initial configuration?-->
{: #initial-configuration-of-the-altavault-appliance-initial-configuration-}

Vous pouvez mettre sous tension la machine virtuelle AltaVault après l'avoir configurée avec le matériel approprié. **Remarque :** le premier démarrage de la machine virtuelle AltaVault peut prendre un certain temps car le dispositif AltaVault procède au formatage du disque de cache de métadonnées secondaire.


1. Une fois le dispositif démarré, connectez-vous à la console AltaVault. Utilisez ``*admin`` comme **nom d'utilisateur** et ``password`` comme **mot de passe**. Vous pouvez modifier ces données d'identification une fois la configuration initiale terminée.
2. Une fois connecté, vous êtes invité à indiquer si vous souhaitez utiliser l'assistant de configuration initiale. Tapez **y** et appuyez sur **Entrée** pour sauvegarder vos modifications. 

Utilisez les informations contenues dans le tableau 1 une fois l'assistant ouvert. 

|Question|Réponse|
|---|---|
|Step 1: Admin Password?|Entrez un nouveau mot de passe administrateur (il doit être différent de "password")|
|Step 2: Host name?|Entrez le nom d'hôte que vous souhaitez utiliser|
|Step 3: Use DHCP on primary interface?|Entrez **n**|
|Step 4: Primary IP address?|Entrez l'adresse IP de réseau principale. Dans l'exemple de configuration, l'adresse IP principale correspond au réseau qui est utilisé pour la réplication sur le cloud et la gestion de dispositif (192.168.50.15)|
|Step 5: Netmask?|Entrez le masque de réseau (255.255.255.0)|
|Step 6: Default gateway?|Entrez la passerelle par défaut (192.168.50.1)|
|Step 7: Primary DNS server?|Entrez le serveur DNS principal de votre environnement|
|Step 8: Domain name?|Entrez le nom de domaine de votre environnement (testenv.org)|
{: caption="Tableau 1. Valeurs de la configuration initiale du dispositif AltaVault" caption-side="top"}

### Configuration du dispositif AltaVault pour Object Storage
{: #configuring-altavault-for-object-storage}

Afin de configurer le dispositif pour qu'il se connecte au service {{site.data.keyword.cos_full_notm}}, procédez comme suit :

1. Ouvrez un navigateur Web et entrez l'adresse IP de l'interface principale du dispositif AltaVault (reportez-vous à l'étape précédente). 
2. Connectez-vous à la console à l'aide des données d'identification d'administrateur. Après la première connexion, le tableau de bord de l'assistant s'affiche.
3. Sélectionnez **System Settings** et vérifiez que les informations sont correctes sur l'écran suivant et remplacez le fuseau horaire par celui de votre environnement. 
4. Cliquez sur **Next > Save and Apply > Exit**. Vous revenez au tableau de bord de l'assistant. 
5. Sélectionnez **Cloud Settings** et cliquez sur **Provider**. Choisissez **IBM Cloud Object Storage**.
6. Sélectionnez un élément **Object Storage Region** approprié. **Remarque :** les régions ne sont pas toutes affichées (par exemple, Melbourne). Toutefois, le nom d'hôte du service {{site.data.keyword.cos_short}} service est modifié à l'aide de la zone **Host name**. Par exemple, si vous souhaitez utiliser Melbourne comme région, vous pouvez sélectionner **San Jose 1** dans le menu déroulant **Region** et remplacer le contenu de la zone **Host name** par **mel01.objectstorage.softlayer.net**.
7. Entrez vos données d'identification {{site.data.keyword.cos_full_notm}} dans la zone **User name**. Le format à utiliser pour le nom d'utilisateur est `object_storage_username:IBM_Cloud_username`. Par exemple : **ABC-DE123456-7:user**. Votre nom d'utilisateur Object Storage se trouve sous **Storage > Object Storage** dans le [portail {{site.data.keyword.slportal_full}} ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://control.softlayer.com/){: new_window}.
8. Renseignez la zone **Bucket Name** avec un nom de compartiment dans lequel stocker les données. Il s'agit du nom du conteneur dans lequel vous souhaitez stocker les données dans {{site.data.keyword.cos_full_notm}}.
* Ne modifiez pas le port par défaut sauf si votre administrateur de réseau vous demande de le faire. La zone **Enable Archiving** prend par défaut la valeur **No**. Cliquez sur **Next**.
9. Renseignez la zone **License Request Token**, si nécessaire, puis cliquez sur **Next**.
10. Renseignez la zone **Encryption Key**. Vous pouvez autoriser AltaVault à générer une nouvelle clé de chiffrement ou entrer une clé existante que vous souhaitez utiliser pour chiffrer et déchiffrer les données. Cliquez sur **Next**. 
11. Vérifiez que tous vos paramètres sont corrects, puis cliquez sur **Finish and Apply**. AltaVault tente de contacter le service {{site.data.keyword.cos_full_notm}} en utilisant les entrées et les paramètres définis dans l'assistant des paramètres de cloud. Si la connexion échoue, vérifiez vos paramètres et assurez-vous que vous disposez des droits d'accès appropriés au service. 
12. Cliquez sur **Exit** une fois qu'une connexion est établie afin de revenir au tableau de bord de l'assistant et cliquez sur **Exit Wizard** pour revenir à la page d'état du dispositif AltaVault. 
13. Assurez-vous que le service *Storage Optimization Service* est en cours d'exécution et qu'il est à l'état prêt. **Remarque :** le passage à l'état*Prêt* peut durer quelques minutes. 

Le dispositif AltaVault est configuré pour communiquer avec le service {{site.data.keyword.cos_full_notm}}. 

### Configuration du point de montage CIFS/SMB dans AltaVault
{: #configuring-the-cifs-smb-mount-point-in-altavault}

L'interface **e0b** doit être configurée pour créer un point de montage CIFS/SMB. Utilisez la procédure suivante pour configurer **e0b** :

1. Accédez à **Settings > Data Interfaces** et développez l'interface **e0b**. Cochez **Enable Data Interface** et renseignez les zones **IP address, Subnet Mask,** et **Gateway** avec les informations que vous utilisez pour monter le partage CIFS/SMB. 
2. Conservez la valeur par défaut **1500 bytes** pour **MTU**. <br/><br/>La taille MTU maximale par défaut est 1500, mais vous pouvez indiquer une autre valeur pouvant aller jusqu'à 9000 si vous utilisez des trames Jumbo. **Remarque :** votre infrastructure physique et hôte ESXi doit impérativement prendre en charge les trames Jumbo. Par défaut, {{site.data.keyword.cloud_notm}} prend déjà en charge une taille MTU de 9000 octets sans qu'aucune modification de configuration ne soit nécessaire. 
3. Cliquez sur **Apply**. Le point de montage est prêt pour la configuration.
4. Sélectionnez **Storage > CIFS > Add CIFS Share** et entrez un nom unique. 
5. Cliquez sur le menu déroulant **Pin Share** et sélectionnez **Yes**. **Remarque :** les sauvegardes Veeam Backup & Replication peuvent échouer sur un partage détaché. 
6. Entrez un chemin unique pour le partage dans la zone **Path**. Il est préférable d'utiliser le nom de partage comme chemin, autrement dit, si le nom de partage est `cifs_share0, entrez /cifs_share0` comme chemin. 
7. Désélectionnez **Allow Everyone Access** si la sécurité n'est pas un problème. Il est préférable de placer sur liste blanche les clients qui utilisent le partage CIFS/SMB. Sinon, laissez l'option **Allow Everyone Access** sélectionnée si le problème de la sécurité se pose et cliquez sur **Add Share**.
8. Cliquez sur **Add CIFS User** pour créer des comptes pour des utilisateurs autorisés et renseignez les zones **User name** et **Password**. 
9. Développez le nouveau partage CIFS et cliquez sur **Add a user or group** pour ajouter les comptes d'utilisateur autorisé. 
10. Accédez à **Global CIFS Settings**, cliquez sur le menu déroulant **Listening Interface**, sélectionnez **e0b** et cliquez sur **Apply**.

Le dispositif AltaVault est configuré pour autoriser les communications entre lui-même, {{site.data.keyword.cos_full_notm}} et l'ordinateur sur lequel le logiciel Veeam Backup & Replication est exécuté. Il est recommandé d'exporter la configuration du dispositif AltaVault pour accélérer les futurs déploiements, si nécessaire. 

Pour exporter votre configuration de dispositif AltaVault, procédez comme suit :

1. Cliquez sur **Settings > Setup Wizard** pour accéder au tableau de bord de l'assistant dans la console Web de gestion du dispositif AltaVault sur site. 
2. Cliquez sur **Export Configuration**, puis sur **Export Configuration**.
3. Sauvegardez le fichier de configuration (tarball/.tar) dans un emplacement sûr. 

## Veeam Backup & Replication
{: #veeam-backup-replication-backup}

Le logiciel Veeam Backup & Replication fournit des fonctions de sauvegarde, de réplication et de récupération complètes pour les machines virtuelles et leurs données. La sauvegarde peut s'intégrer entièrement à un dispositif AltaVault Cloud Gateway Appliance.

### Mise à disposition de Veeam sur un nouveau serveur
{: #provisioning-veeam-on-a-new-server}

Vous pouvez commander Veeam lorsque vous mettez à disposition une nouvelle instance de serveur virtuel ou un nouveau serveur bare metal. Utilisez les informations suivantes lors de la mise à disposition : 
  * Veeam est disponible uniquement avec une facturation mensuelle
  * Veeam est disponible uniquement avec un système d'exploitation Windows

Pour ajouter Veeam :
  * Dans la section Modules complémentaires système, sous Module complémentaire propre au SE, cliquez sur l'onglet Veeam et sélectionnez l'une des options Veeam. 
  * Dans la section Modules complémentaires système, sous Module complémentaire CDP, sélectionnez les options Veeam supplémentaires à ajouter. <br><br>**Remarque** : si vous sélectionnez *Veeam Backup and Replication 9.5 Update 3*, vous devez sélectionner au moins une option dans la liste Module complémentaire CDP. 

### Commande de Veeam sur un serveur existant
{: #ordering-veeam-on-an-existing-server}

Vous pouvez ajouter Veeam à un serveur existant à l'aide de la procédure de [rechargement de système d'exploitation](/docs/infrastructure/software?topic=software-reloading-the-os). Le serveur doit exécuter un système d'exploitation Windows et être configuré pour la facturation mensuelle. 

Pour ajouter Veeam :
1. Dans la section Modules complémentaires système, sous Module complémentaire propre au SE, cliquez sur l'onglet Veeam et sélectionnez l'une des options Veeam. 
2. Dans la section Modules complémentaires système, sous Module complémentaire CDP, sélectionnez les options Veeam supplémentaires à ajouter. <br><br>**Remarque** : si vous sélectionnez *Veeam Backup and Replication 9.5 Update 3*, vous devez sélectionner au moins une option dans la liste Module complémentaire CDP. 

### Déploiement de Veeam Backup & Replication
{: #deploying-veeam-backup-replication}

Une version d'évaluation de Veeam Backup & Replication Version 8 est utilisée dans l'exemple. 

#### Prérequis
{: #prerequisites-deploying-veeam-backup-replication}

Avant de procéder au déploiement, vérifiez que les conditions prérequises suivantes sont remplies :

* Vous disposez d'un dispositif AltaVault qui est configuré pour être utilisé avec {{site.data.keyword.cos_full_notm}} et Veeam Backup & Replication.
* Procurez-vous une copie de Veeam Backup & Replication pour les environnements VMware ; il s'agit d'un seul fichier exécutable. Contactez votre représentant Veeam pour obtenir une copie ou téléchargez une [version d'évaluation de 30 jours![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](http://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}.
* Procurez-vous un fichier de licence à utiliser avec Veeam Backup & Replication. Dans la plupart des cas, ce fichier est envoyé à l'adresse électronique qui est utilisée pour télécharger Veeam Backup & Replication. Si vous n'avez pas reçu ce fichier, contactez votre représentant Veeam.<br/><br/>Le fichier de licence permet d'activer toutes les fonctions Veeam Backup & Replication. Si ce fichier n'est pas fourni lors de l'installation du programme, la version d'évaluation de 30 jours est rétablie pour tous les dispositifs et toutes les fonctions. 
* Vous devez disposer d'un serveur de sauvegarde, sur site ou hors site, répondant aux spécifications décrites dans le tableau 2. Le système d'exploitation installé doit être une version 64 bits. 

||Minimum|Recommandé|
|---|---|---|
|**Système d'exploitation**|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>WIndows Server 2008 R2 SP1</li><li>Windows Server 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>Windows Server 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|
|**# de coeurs ou d'unités centrales virtuelles (vCPU)**|2|4|
|**Mémoire**|Mémoire RAM de base de 4 Go plus 500 Mo pour chaque travail de sauvegarde simultané.|Mémoire RAM de base de 16 Go plus 4 Go pour chaque travail de sauvegarde simultané. |
|**Espace disque**|2 Go pour l'installation du produit ; 10 Go par tranche de 100 machines virtuelles pour les données de catalogue de système de fichiers invité (données permanentes). |2 Go pour l'installation du produit ; 10 Go pour 100 machines virtuelles pour le catalogue de système de fichiers invité (données permanentes). |
|**Réseau**|LAN 1 Gbps pour la sauvegarde et la réplication sur site ; WAN 1 Mbps pour la sauvegarde et la réplication hors site.|LAN 1 Gbps pour la sauvegarde et la réplication sur site ; WAN 1 Mbps pour la sauvegarde et la réplication hors site.|
{: caption="Tableau 2. Configuration requise pour le serveur de sauvegarde Veeam Backup & Replication" caption-side="top"}

#### Installation de Veeam Backup & Replication
{: #installing-veeam-backup-replication}

Procédez comme suit pour installer Veeam Backup & Replication sur le serveur de sauvegarde une fois que toutes les conditions prérequises sont remplies :

1. Pour démarrer l'assistant de configuration, cliquez deux fois sur le fichier exécutable Veeam et cliquez sur **Veeam Backup & Replication – Installation**.
2. Cliquez sur **Suivant** et acceptez les termes du contrat de licence. 
3. Cliquez sur **Suivant**, puis sur **Installer** pour **Veeam Backup & Replication**.
4. Entrez l'emplacement du fichier de licence précédemment obtenu, puis cliquez sur **Suivant**.
5. Sélectionnez les composants Veeam Backup & Replication que vous souhaitez installer et fournissez l'emplacement d'installation sur l'écran **Configuration de Veeam Backup & Replication**. **Veeam Backup & Replication et Veeam Backup Catalog** sont des composants obligatoires. Cliquez sur **Suivant**. L'assistant d'installation exécute une série de vérifications pour s'assurer que toutes les infrastructures de programme et tous les composants de support qui sont nécessaires sont installés.
S'il en manque, il propose de les installer automatiquement.
Si vous devez installer les infrastructures ou les composants qui manquent, cliquez sur **Installer**.
6. Vérifiez que tous les composants ont **réussi** le contrôle des systèmes et cliquez sur **Suivant**.
7. Sélectionnez le **compte (utilisateur) de service** sur lequel Veeam Backup Service s'exécute. **Remarque :** le compte de service par défaut est le *compte LOCAL SYSTEM*. Cliquez sur **Suivant**.
8. Sélectionnez l'**instance de serveur SQL** qui est utilisée pour créer et stocker les bases de données Veeam Backup & Replication.<!--Contact your database administrator for more information, if necessary. -->Cliquez sur **Suivant**.
9. Entrez le **port de service de catalogue** et le **port de service Veeam Backup** (les ports par défaut sont **9393** et **9392**).<!--Contact your network administrator for more information, if necessary.--> Cliquez sur **Suivant**.
10. Sélectionnez les répertoires dans lesquels sont stockés le catalogue de systèmes de fichiers invités (données permanente) et le cache en écriture vPower NFS (données non permanentes). Cliquez sur **Suivant**.
11. Assurez-vous que tous les paramètres et toutes les valeurs sont corrects, puis cliquez sur **Installer** pour démarrer l'installation. Une fois l'installation terminée, cliquez sur **Terminer**.

### Configuration de Veeam Backup & Replication pour les sauvegardes
{: #configuring-veeam-backup-replication-for-backups}

Après avoir installé Veeam Backup & Replication, vous êtes prêt à le connecter à l'hôte vSphere ESXi qui contient le dispositif virtuel AltaVault. 

1. Démarrez Veeam Backup & Replication.
2. Dans l'angle inférieur gauche de l'écran, cliquez sur **Infrastructure de sauvegarde > Serveurs gérés**.
3. Cliquez sur **Ajouter un serveur** et cliquez deux fois sur **VMware vSphere**.
4. Entrez le **nom DNS ou l'adresse IP** et une **description** du serveur vCenter, puis cliquez sur **Suivant**.
5. Entrez les **données d'identification** d'un compte local doté des privilèges d'administrateur sur le serveur vSphere. **Remarque :** le nom d'utilisateur de compte doit être au format **DOMAIN\USER** pour les comptes de domaine ou au format **HOST\USER** pour les comptes locaux. Pour ajouter un compte, cliquez sur **Ajouter** et entrez le nom d'utilisateur et le mot de passe de compte. <br/>Ne modifiez pas le **port de services Web VMware par défaut** durant l'installation de Veeam Backup & Replication sauf si votre administrateur de réseau vous demande de le faire. 
6. Cliquez sur **Suivant**. Veeam Backup & Replication se connecte au serveur VMware vSphere. Si la tentative de connexion échoue, vérifiez que le compte existe et possède des privilèges d'administrateur sur le serveur VMware vSphere avant de relancer une tentative de connexion. 
7. Cliquez sur **Terminer** sur la fenêtre **Récapitulatif** et vérifiez que le serveur vSphere a été ajouté en cliquant sur **Serveurs gérés > VMware vSphere**.

### Ajout d'un référentiel de sauvegarde à Veeam Backup & Replication
{: #adding-a-backup-repository-to-veeam-backup-replication}

Par défaut, Veeam Backup & Replication crée un référentiel de sauvegarde locale sur le lecteur C:\ du serveur de sauvegarde Veeam Backup & Replication durant l'installation du programme. 

Utilisez la procédure décrite ci-après pour créer un référentiel de sauvegarde dans lequel stocker toutes les sauvegardes sur le dispositif AltaVault. Si vous souhaitez utiliser le référentiel de sauvegarde par défaut, ignorez les étapes décrites ci-après. 

1. Dans l'angle inférieur gauche de l'écran **Infrastructure de sauvegarde**, cliquez sur **Infrastructure de sauvegarde > Référentiels de sauvegarde > Ajouter un référentiel**.
2. Entrez un nom de référentiel unique dans la zone **Nom**. Vous pouvez éventuellement fournir une **description**. Cliquez sur **Suivant**.
3. Sélectionnez **Dossier partagé**. Le partage CIFS/SMB que vous avez créé est le partage utilisé par ce référentiel de sauvegarde. Cliquez sur **Suivant**.
4. Spécifiez l'emplacement du partage CIFS/SMB sur le dispositif AltaVault. Pour déterminer l'emplacement, ouvrez un navigateur Web et entrez l'adresse IP du dispositif AltaVault. Accédez à **Stockage > CIFS** et notez le **chemin de partage** du partage. **Remarque :** il NE s'agit PAS du chemin local du partage. <br/><br/>Le format du chemin de partage est `\\<AltaVault appliance hostname>\<share name>`. Remplacez le nom d'hôte du dispositif AltaVault dans le chemin de partage par l'adresse IP de l'interface réseau **e0b** (point de montage du partage) du dispositif AltaVault.<br/><br/>Pour trouver l'adresse IP de l'interface **e0b**, cliquez sur **Settings > Data Interfaces** dans la fenêtre de gestion du dispositif AltaVault. Le chemin de partage qui est spécifié dans Veeam Backup & Replication est `\\192.168.50.16\cifs_test2`.
5. Revenez à Veeam Backup & Replication, entrez le chemin de partage du point de montage dans la zone **Dossier partagé** et cliquez sur **Suivant**. Veeam Backup & Replication tente d'établir une connexion au point de montage. Si la tentative de connexion échoue, revenez en arrière et vérifiez que les paramètres réseau du dispositif AltaVault sont corrects avant de faire une nouvelle tentative. 
6. Entrez une valeur afin de limiter le nombre de tâches simultanées au nombre de ressources disponibles. Cette valeur représente le nombre maximal de tâches qu'un proxy de sauvegarde peut envoyer au partage sélectionné. Le nombre par défaut de tâches simultanées est 4. AltaVault suggère de démarrer avec cinq tâches simultanées et d'augmenter ou de réduire cette valeur en fonction des ressources. Cette valeur peut être ajustée une fois le référentiel de sauvegarde créé. 
7. Cliquez sur **Suivant**.
8. Si besoin, spécifiez les **paramètres vPower NFS**. Si vous laissez la case **Activer le serveur vPower NFS** non cochée, Veeam Backup & Replication utilise vPower pour la reprise et la vérification de reprise. Cliquez sur **Suivant**.
9. Sur l'écran **Vérifier**, confirmez que tous les paramètres sont corrects et cliquez sur **Suivant**.
10. Cliquez sur **Terminer** pour quitter l'assistant. Vous pouvez maintenant commencer à sauvegarder vos données.

### Sauvegarde de l'environnement
{: #backing-up-the-environment}

Procédez comme suit pour créer une copie de sauvegarde d'un environnement virtuel complet :

1. Sur l'écran **Infrastructure de sauvegarde**, cliquez sur **Backup & Replication**.
2. Dans la fenêtre **Backup & Replication**, cliquez sur **Travaux > Travail de sauvegarde**.
3. Entrez un nom unique dans la zone **Nom**. Vous pouvez éventuellement entrer une **description**. Cliquez sur **Suivant**.
4. Sélectionnez les machines virtuelles que vous souhaitez sauvegarder en cliquant sur **Ajouter des objets**, puis cliquez dans la structure arborescente pour sélectionner les machines virtuelles. Cliquez sur **Ajouter** après avoir sélectionné les machines virtuelles appropriées. <br/>Si certaines parties seulement des machines virtuelles doivent être sauvegardées (disque de démarrage), cliquez sur **Exclusions** et spécifiez ces parties. Sinon, cliquez sur **Suivant**.
5. Sélectionnez le référentiel de sauvegarde que vous avez créé en utilisant le menu déroulant **Référentiel de sauvegarde**. 

**Remarque :** pour optimiser vos performances, prenez soin de modifier les paramètres de dédoublonnage et de compression de données. Utilisez la procédure décrite ci-après pour optimiser vos performances. 

1. Cliquez sur **Avancé**, sélectionnez l'onglet **Stockage**, puis désélectionnez **Activer le dédoublonnage de données en ligne**. Les performances sont améliorées car le dispositif AltaVault effectue un dédoublonnage de niveau bloc des sauvegardes Veeam Backup & Replication qui le traversent. 
2. Sélectionnez **Aucun** sous le menu déroulant **Niveau de compression** et sélectionnez **Cible LAN** sous le menu déroulant **Optimisation de stockage**. <br/>**Remarque :** si l'emplacement réseau du partage CIFS/SMB est encombré, le fait de laisser la case **Activer le dédoublonnage de données en ligne** cochée peut réduire les problèmes de performance réseau, mais en même temps cela peut générer des ratios de dédoublonnage de données plus petits sur le dispositif AltaVault. 
3. Cliquez sur **Suivant**.
4. Pour un traitement orienté applications et/ou une indexation de système de fichiers invité, cochez la case appropriée. Définissez les **données d'identification de système d'exploitation invité** [le nom d'utilisateur et le mot de passe du système d'exploitation invité des machines virtuelles qui sont sauvegardées], si nécessaire. Cliquez sur **Suivant**.
5. Cochez la case **Exécuter le travail automatiquement** si des sauvegardes sont exécutées régulièrement et définissez les intervalles souhaités. Sinon, cliquez sur **Créer** et **Terminer**.

#### Démarrage d'une sauvegarde manuelle
{: starting-a-manual-backup}
Pour démarrer manuellement une sauvegarde, cliquez avec le bouton droit de la souris sur le travail de sauvegarde et sélectionnez **Démarrer**. Vous pouvez aussi sélectionner **Intégrale active** pour obtenir une nouvelle sauvegarde. 

**Remarque :** Veeam Backup & Replication peut restaurer des environnements virtuels à partir d'une sauvegarde. Pour plus d'informations sur la restauration d'environnements virtuels, voir le site Web [Veeam Backup & Replication ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}. 
