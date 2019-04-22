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

# Récupération de votre environnement VMware vSphere
{: #recovering-your-vmware-vsphere-environment}

La sauvegarde de données est la principale méthode pour maintenir la sécurité et l'intégrité des données. Dans un monde idéal et parfait, il ne serait jamais nécessaire d'avoir recours à cette procédure. Malheureusement, dans la réalité, des accidents et des sinistres se produisent. Lorsque cela arrive, il est primordial que les systèmes soient arrêtés le moins de temps possible et qu'ils puissent être remis en service très vite.

Dans le passé, il était possible de stocker les sauvegardes sur des disques à hautes performances ou sur bande.
Aujourd'hui, les entreprises cherchent à alléger les coûts d'investissement et d'exploitation liés au stockage physique sur site en le remplaçant partiellement ou totalement par Object Storage.
Toutefois, avec Storage Object, comme les sauvegardes ne se trouvent plus sur site, les utiliser pour une restauration ne consiste plus à simplement les trouver et effectuer la restauration.
Heureusement, il existe solution hybride qui effectue une grande partie du travail de restauration des sauvegardes stockées dans Object Storage.
Cette solution est constituée des composants suivants :


* {{site.data.keyword.cos_full}}
* La passerelle NetApp AltaVault Cloud Storage Gateway (anciennement Riverbed SteelStore)
* Le logiciel Veeam Backup & Replication

Si une catastrophe survient et met hors ligne votre environnement principal, un site de récupération secondaire peut être créé dans {{site.data.keyword.cloud}}. Vous pouvez déployer le logiciel Veeam Backup & Replication et un dispositif de stockage intégré au cloud AltaVault pour communiquer avec {{site.data.keyword.cos_full_notm}} afin d'accéder aux sauvegardes précédentes de l'environnement principal. Le logiciel se connecte au dispositif de stockage pour restaurer les sauvegardes sur un nouvel emplacement, afin de remettre l'environnement principal en ligne et d'éviter un temps d'indisponibilité plus important.


Les adresses IP sur site sont conservées lors de la restauration des machines virtuelles sur le site de récupération {{site.data.keyword.cloud_notm}}. Par conséquent, il est important de les changer après la récupération ou de concevoir une infrastructure BYOIP.
Pour obtenir de l'aide pour l'une ou l'autre de ces opérations, contactez votre représentant IBM. 

La procédure décrite ci-après se concentre sur l'utilisation coordonnée d'un dispositif NetApp AltaVault Cloud Storage Gateway, de l'infrastructure {{site.data.keyword.cloud_notm}} et de Veeam Backup & Replication pour restaurer entièrement un environnement VMware vSphere qui a échoué.
Le logiciel suppose qu'au moins une sauvegarde de l'environnement a été faite auparavant avec les trois technologies susmentionnées et réside dans {{site.data.keyword.cos_full_notm}} au  moment de la récupération. 

Le terme "dispositif AltaVault" désigne deux dispositifs différents dans cette procédure.
Le premier est le "dispositif AltaVault sur site" ; il s'agit du dispositif AltaVault sur site d'origine qui a échoué et à partir duquel les sauvegardes sont récupérées et/ou restaurées.
Le second est le "dispositif AltaVault {{site.data.keyword.cloud_notm}}" ; il s'agit du dispositif utilisé pour récupérer les sauvegardes à partir du dispositif AltaVault sur site qui a échoué.
Le dispositif AltaVault {{site.data.keyword.cloud_notm}} est déployé sur un environnement vSphere à l'aide d'un serveur utilitaire. 

## Présentation d'une solution hybride
{: #introducing-a-hybrid-solution}

Veeam Backup & Replication active une solution hybride pour inclure un dispositif de stockage intégré dans le cloud NetApp AltaVault et {{site.data.keyword.cos_full_notm}}. Avant tout, il s'agit de créer et gérer des sauvegardes d'environnements virtuels et d'effectuer des restaurations à partir de ces sauvegardes. Le dispositif de stockage intégré dans le cloud NetApp AltaVault est une solution logicielle permettant d'intégrer de façon transparente un environnement sur site à des clouds privés ou publics. Lorsque ce dispositif est utilisé avec Veeam Backup & Replication, celui-ci peut créer des sauvegardes que le dispositif de stockage intégré au cloud AltaVault stocke sur site et réplique simultanément dans {{site.data.keyword.cos_full_notm}}. Par ailleurs, le modèle de facturation à l'utilisation de {{site.data.keyword.cloud}} et son intégration complète avec le CDN (Content Delivery Network) offrent la possibilité de stocker et de distribuer les données sur 24 noeuds répartis géographiquement.


### IBM Cloud Object Storage
{: #ibm-cloud-object-storage}

L'accès aux fichiers de données sauvegardés dans {{site.data.keyword.cos_full_notm}} se fait en les demandant au moyen des dispositifs suivants :

* Le dispositif AltaVault qui se trouve déjà sur site
* Un dispositif AltaVault secondaire qui se trouve dans {{site.data.keyword.cloud_notm}}

Suivez la procédure décrite ci-après pour déployer un dispositif AltaVault secondaire dans {{site.data.keyword.cloud_notm}} afin de récupérer un dispositif AltaVault principal sur site qui a échoué. 

L'environnement {{site.data.keyword.cloud_notm}} se compose d'un hôte ESXi unique avec un espace de stockage local pour héberger et exécuter le dispositif AltaVault secondaire. L'infrastructure est représentative de l'architecture mono-site de base constituée d'un hôte ESXi unique géré par un serveur vCenter dans une instance de serveur virtuel {{site.data.keyword.cloud_notm}}. 

Si vous disposez d'une infrastructure plus substantielle qui nécessite un espace de stockage partagé et/ou le support de fonctionnalités telles que les suivantes :


* vSphere High Availability (HA)
* vSphere Distributed Resource Scheduler (DRS)
* vSphere vMotion

#### Prérequis
{: #prerequisites-ibm-cloud-object-storage}

Vérifiez que les conditions prérequises suivantes sont remplies avant de poursuivre : **Remarque :** pour cet exemple, une version d'évaluation d'un dispositif AltaVault AVA-v8 a été utilisée avec AltaVault Cloud-Integrated Storage 4.1.

* Assurez-vous que l'environnement existant se compose d'un hôte ESXi unique qui est géré par un serveur vCenter dans une instance de serveur virtuel {{site.data.keyword.cloud_notm}} .
* Connaissance de la terminologie VMware Sphere et de l'administration des environnements vSphere ESXi. Cela inclut, mais pas exclusivement, l'utilisation du client Web vSphere, du client vSphere et l'affectation de ressources matériel dont la mise en réseau et le stockage. 

#### Commande de deux réseaux privés portables
{: #order-two-portable-private-networks}

AltaVault requiert que les interfaces réseau se trouvent sur des réseaux différents dans l'environnement. Utilisez la procédure décrite ci-après pour commander deux réseaux privés portables via le portail {{site.data.keyword.slportal_full}}.

1. Accédez au portail [{{site.data.keyword.slportal_full}} ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://control.softlayer.com/){: new_window} en utilisant vos données d'identification uniques.
2. Cliquez sur **Compte > Passer une commande**.
3. Sélectionnez la section **Réseau**, puis cliquez sur **Sous-réseaux/Adresses IP > Commander**.
4. Dans le menu déroulant, sélectionnez **Privé portable**. Cela déclenche une option vous permettant de sélectionner le nombre d'adresses IP privées portables à afficher. **Remarque :** {{site.data.keyword.cloud_notm}} réserve automatiquement au moins trois adresses IP dans le bloc d'adresses pour chaque réseau privé portable. Les adresses réservées le sont pour l'adresse de réseau, l'adresse de passerelle et l'adresse de diffusion. Ces adresses doivent être liées directement au réseau local virtuel (LAN).<!--If your IBM Cloud pod supports Hot Standby Ready Protocol (HSRP), then the number of usable IP addresses is reduced. For instance, if you select **4 Portable Private IP Addresses** will yield only one usable IP address or zero if the pod supports HSRP.--> Pour plus d'informations sur les adresses IP portables, voir [Initiation aux sous-réseaux et aux adresses IP](/docs/infrastructure/subnets?topic=subnets-getting-started-subnets-ips#getting-started-subnets-ips).
5. Cliquez sur **Continuer** après avoir sélectionné le nombre d'adresses IP privées portables. 
6. Sélectionnez le VLAN sur lequel l'adresse IP privée portable doit être routée, puis cliquez sur **Continuer**.
7. Entrez les informations de contact demandées et cliquez sur **Passer une commande**.
8. Répétez la procédure de commande pour obtenir le second réseau privé portable requis. 
9. Cliquez sur **Réseau > Gestion IP > Sous-réseaux** sur la page du portail {{site.data.keyword.slportal}} pour afficher vos réseaux et adresses IP privés portables une fois qu'ils sont alloués. Il est recommandé de suivre les affectations d'adresse IP. Utilisez la section **Remarques** de la page **Sous-réseaux** pour chaque adresse IP afin de répercuter la machine à laquelle elle est affectée. 

#### Configuration de vSphere
{: #configuring-vsphere}

Vous devez configurer vSphere pour répercuter l'ajout des réseaux privés portables et vous assurer qu'ils sont séparés de façon logique. 

1. Accédez à votre environnement vSphere et créez un groupe de ports de machine virtuelle afin de répercuter l'ajout de l'un des blocs d'adresses IP portables (deux au total). 

Le groupe de ports de machine virtuelle est libellé **Réseau de partage**. Un bloc d'adresses IP portables est utilisé pour les machines virtuelles placées sur le groupe de ports de machine virtuelle (principal) par défaut. L'autre est utilisé pour le transfert de données entre un dispositif AltaVault et un point de montage CIFS (Common Internet File System)/SMB (Server Message Block) exporté. 

## AltaVault Cloud Storage Gateway
{: #altavault-cloud-storage-gateway}

Vous pouvez utiliser AltaVault pour intégrer votre environnement sur site au cloud sans avoir à écrire des scripts ou des applications en utilisant des API REST pour {{site.data.keyword.cos_full_notm}}. AltaVault expose un point de montage CIFS/SMB ou NFS (Network File System) sur le système frontal et se connecte de manière sécurisée à l'interface {{site.data.keyword.cos_full_notm}} sur le système dorsal. Vous pouvez ensuite monter ou pointer vers les points de montage et copier les données dans le cloud et/ou récupérer/restaurer les environnements à partir du cloud en toute sécurité.<!-- , as shown in Figure 1.-->

<!-- ![Figure 1](images/veeam_restore_fig2.png)
<br/>`Figure 1: AltaVault end-to-end flow with Veeam and IBM Cloud`-->

### Déploiement de AltaVault dans une configuration de reprise après incident
{: #deploying-altavault-in-a-disaster-recovery-configuration}

Pour déployer AltaVault en tant que solution de reprise après incident à l'aide d'{{site.data.keyword.cos_full_notm}}, procédez comme suit :

#### Avant de commencer
{: #before-you-get-started-deploying-altavault-in-a-disaster-recovery-configuration}

Vérifiez que vous disposez des éléments prérequis suivants avant de poursuivre :

* Procurez-vous une copie du dispositif virtuel AltaVault et assurez-vous qu'elle figure sur le serveur utilitaire. Il s'agit d'un fichier unique avec une extension de fichier OVA. Contactez votre représentant NetApp pour obtenir le dispositif ou téléchargez une version d'évaluation de 90 jours à partir du site Web [NetApp AltaVault ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://www.netapp.com/us/products/protection-software/altavault/){: new_window}.
* Vous devez disposer d'un environnement vSphere ESXi 5.5 sur site avec la quantité minimale d'unité centrale, de mémoire et d'espace disque requise pour le dispositif AltaVault. Si vous utilisez la version d'évaluation, ces exigences minimales sont les suivantes : quatre unités centrales virtuelles (vCPU), 24 Go de mémoire et jusqu'à 8 To d'espace disque. 
* Vous devez disposer de deux contrôleurs NIC 10 Gbps disponibles dans l'environnement vSphere. Un contrôleur NIC est utilisé pour l'accès aux données et l'autre est utilisé pour la réplication de données sur {{site.data.keyword.cos_full_notm}}.
* Vous devez disposer de deux réseaux privés portables correspondant aux deux contrôleurs NIC qui sont définis dans l'environnement vSphere. Le réseau de réplication ne peut pas être affecté au même réseau que le réseau d'accès aux données, car cela risquerait de créer une boucle de routage. 
* Vous devez disposer de données d'identification {{site.data.keyword.cos_full_notm}}. Ces données d'identification incluent notamment un nom d'utilisateur {{site.data.keyword.cloud_notm}}, un nom d'utilisateur {{site.data.keyword.cos_full_notm}} et la clé d'API qui est associée au nom d'utilisateur {{site.data.keyword.cloud_notm}}. 
* Suspendez la réplication de données sur le dispositif AltaVault sur site ou déconnectez-la du conteneur/compartiment qui permet d'accéder à l'archive de sauvegarde dans {{site.data.keyword.cloud_notm}} si l'environnement sur site est toujours intact.
  * Utilisez l'une des deux méthodes suivantes pour suspendre ou arrêter la réplication :
    * Mettez hors tension le dispositif AltaVault sur site. 
    * Accédez à la console de gestion Web du dispositif AltaVault et cliquez sur **Stockage > Paramètres Cloud > Réplication**. Sélectionnez **Suspendre la réplication**.
    * Procurez-vous une copie du fichier de configuration du dispositif AltaVault sur site ou l'accès à ce fichier via une URL. Le fichier de configuration est un fichier .tar.
  * Accédez à la console de gestion Web du dispositif AltaVault et cliquez sur **Configurer > Assistant de configuration > Exporter la configuration > Exporter la configuration**.
Sauvegardez le fichier de configuration (fichier .tar) et transférez-le sur le serveur utilitaire ou rendez-le accessible via une URL.
    * Connaissance de la terminologie VMware Sphere et de l'administration des environnements vSphere ESXi. Cela inclut, mais pas exclusivement, l'utilisation du client Web vSphere, du client vSphere et l'affectation de ressources matériel dont la mise en réseau et le stockage. 

### Déploiement du package OVA AltaVault
{: #deploying-altavault-ova-disaster}

Déployez le package OVA AltaVault sur l'environnement vSphere à l'aide du serveur utilitaire lorsque toutes les conditions prérequises sont remplies. Vous trouverez les instructions relatives au déploiement du package OVA dans le document [NetApp AltaVault Installation and Service Guide ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}.La version d'évaluation de AltaVault fournie est configurée avec quatre unités centrales virtuelles, 6 Go de mémoire et 150 Go de disque principal. Reportez-vous au document Installation and Service Guide pour effectuer les étapes suivantes : 

1. Augmentez la quantité de mémoire à 24 Go.
2. Ajoutez une unité de stockage secondaire dont la taille est inférieure ou égale à 8 To pour stocker les données de sauvegarde dédoublonnées. 
3. Affectez différents réseaux privés portables au dispositif AltaVault après la modification des configurations de mémoire et de disque. 

Les fonctions d'interface suivantes sont affectées aux contrôleurs NIC :

* **Principale** : Utilisée pour la gestion du dispositif AltaVault et la région propriétaire de fichier sur le cloud. Le groupe de ports **Réseau principal** lui est affecté dans l'exemple d'environnement. 
* **e0a** : Interface facultative qui est utilisée pour répliquer des données depuis le dispositif AltaVault vers le cloud. 
* **e0b** : Interface qui est utilisée pour exporter le point de montage pour le partage SMB/CIFS ou NFS. Le groupe de ports **Réseau de partage** lui est affecté dans l'exemple d'environnement. 
* **e0c** : Interface facultative qui est utilisée pour exporter le point de montage pour le partage SMB/CIFS ou NFS.

Dans l'exemple de configuration de cette procédure, le dispositif AltaVault utilise l'interface **Principale** comme interface de réplication vers le cloud et l'interface **e0b** pour exporter un point de montage CIFS/SMB. **Remarque :** vous ne pouvez pas utiliser à la fois un partage CIFS/SMB et un partage NFS pour accéder aux mêmes données. Le protocole qui est utilisé doit être le même que celui qui est utilisé avec le dispositif AltaVault sur site pour répliquer des données vers le cloud. 

<!-- ![Figure 2](images/veeam_restore_fig3.png)
<br/>`Figure 2: IBM Cloud setup with VMware`-->

Pour plus d'informations sur le déploiement du dispositif AltaVault et sur la configuration des paramètres de machine virtuelle, voir le document [NetApp AltaVault Installation and Service Guide ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}.

### Configuration du dispositif AltaVault
{: #configuring-the-altavault-appliance}

1. Vous pouvez mettre sous tension la machine virtuelle AltaVault après l'avoir configurée. Le premier démarrage de la machine virtuelle peut prendre un certain temps car le dispositif AltaVault procède au formatage du disque de stockage secondaire.


2. Connectez-vous à la console AltaVault en utilisant **admin** comme **nom d'utilisateur** et **password** comme **mot de passe** une fois le processus d'amorçage du dispositif terminé. Vous pouvez modifier ces données d'identification une fois la configuration initiale terminée. Reportez-vous au tableau 1 : Valeurs de la configuration initiale du dispositif AltaVault

3. Une fois connecté au dispositif, vous êtes invité à indiquer si vous souhaitez utiliser l'assistant de configuration. Entrez *y*.
4. Utilisez les informations contenues dans le tableau 1 une fois que l'assistant est ouvert. 
5. Appuyez sur **Entrée** pour sauvegarder vos modifications. 

|Question|Réponse|
|---|---|
|Step 1: Admin Password?|Entrez un nouveau mot de passe administrateur (il doit être différent de **password**)|
|Step 2: Hostname?|Entrez le nom d'hôte approprié.|
|Step 3: Use DHCP on primary interface?|Entrez **n** ou **no**.|
|Step 4: Primary IP Address?|Entrez l'adresse IP de réseau principale. Dans l'exemple de configuration, il s'agit du réseau utilisé pour l'interface de gestion (par exemple, 10.120.108.132).|
|Step 5: Netmask?|Entrez le masque de réseau (par exemple, 255.255.255.192).|
|Step 6: Default gateway?|Entrez la passerelle par défaut (par exemple, 10.120.108.129).|
|Step 7: Primary DNS server?|Entrez le serveur DNS principal de votre environnement.|
|Step 8: Domain name?|Entrez le nom de domaine de votre environnement (par exemple, testenv.org)|
{: caption="Tableau 1. Valeurs de la configuration initiale du dispositif AltaVault" caption-side="top"}

### Configuration du dispositif AltaVault pour IBM Cloud Object Storage
{: #configuring-altavault-for-ibm-cloud-object-storage}

Le dispositif AltaVault doit être connecté au service {{site.data.keyword.cos_full_notm}} une fois qu'il est configuré. Dans la configuration sur site, le serveur DNS public est utilisé pour la connexion au service Object Storage. Dans cette configuration, le nom de serveur DNS interne est utilisé à la place. 

1. Ouvrez un navigateur Web et entrez l'adresse IP du dispositif AltaVault. 
2. Connectez-vous à la console à l'aide des données d'identification d'administrateur. Après la première connexion, le **tableau de bord de l'assistant** s'affiche.
3. Sélectionnez **System Settings** et vérifiez que tous les paramètres réseau sont corrects et remplacez le fuseau horaire par celui de votre environnement. 
4. Sélectionnez **Next > Save and Apply > Exit**. Vous revenez à au **tableau de bord de l'assistant** et vous êtes déconnecté de votre session de navigateur en cours.
5. Reconnectez-vous au dispositif AltaVault, cliquez sur **Settings > Setup Wizard > Import Configuration** et spécifiez l'emplacement du fichier de configuration du dispositif AltaVault sur site. 
6. Sélectionnez l'option **Import Shared Data Only**, qui permet d'importer uniquement les paramètres partagés, tels que les paramètres de cloud et de messagerie électronique. 
7. Entrez la phrase de passe affectée à la clé de chiffrement qui est spécifiée lors de la création du dispositif AltaVault sur site, le cas échéant, dans la zone de texte **Key Passphrase**. 
8. Cliquez sur **Import Configuration > Exit**.

<!--The AltaVault appliance web console displays a message indicating a successful import. If the import was not successful, verify that the configuration file is in the correct format and that the passphrase is correct before trying again.-->

Vous avez importé la configuration de dispositif AltaVault sur site dans le dispositif AltaVault {{site.data.keyword.cloud_notm}}. 

Utilisez la procédure décrite ci-après pour modifier les paramètres de cloud du dispositif AltaVault {{site.data.keywrod.cloud_notm}} afin d'octroyer un accès au service {{site.data.keyword.cos_full_notm}} via le réseau privé. 

1. Dans AltaVault, accédez à **Storage > Cloud Settings > Cloud** et modifiez le nom d'hôte pour répercuter l'adresse privée du service {{site.data.keyword.cos_full_notm}}. 
  * La syntaxe du nom de réseau privée est <location>.objectstorage.service.networklayer.com, où *<location>* désigne le nom de centre de données abrégé (par exemple, mel01 pour le centre de données Melbourne 01). 
2. Cliquez sur **Apply**. Le dispositif AltaVault tente de se connecter au service {{site.data.keyword.cos_full_notm}}. Si cette tentative de connexion échoue, vérifiez que les paramètres de fournisseur de cloud sont corrects avant de relancer une tentative de connexion. 

Une fois que vous êtes connecté à {{site.data.keyword.cos_full_notm}}, le dispositif doit être placé en mode reprise pour synchroniser le contenu des métadonnées des données de sauvegarde d'origine à partir d'{{site.data.keyword.cos_full_notm}}.

Utilisez la procédure suivante pour placer le dispositif AltaVault en mode reprise :

Le dispositif AltaVault synchronise les données à partir d'{{site.data.keyword.cos_full_notm}}. **Remarque :** la durée de la synchronisation varie en fonction de la taille des sauvegardes restaurées et de leur nombre. 

1. Accédez au dispositif AltaVault, connectez-vous et entrez les commandes illustrées dans la figure 3 :<br/>![Figure 3](images/veeam_restore_fig5.png)<br/>`Figure 5 : Commandes du mode reprise`
2. Revenez à la console Web du dispositif AltaVault une fois la synchronisation terminée. 
3. Vérifiez que le service **Storage Optimization Service** est **en cours d'exécution** et qu'il est à l'**état**  **prêt**. Le passage à l'état **Prêt** peut durer quelques minutes. 

### Configuration du point de montage CIFS/SMB dans AltaVault
{: #configure-the-cifs-smb-mount-point-in-altavault}

Une fois la connexion interne à {{site.data.keyword.cos_full_notm}} établie, configurez l'interface **e0b** pour l'accès à un point de montage CIFS/SMB. 

1. Dans la console Web AltaVault, accédez à **Settings > Data Interfaces**.
2. Développez l'interface **e0b** et sélectionnez **Enable Data Interface**, si nécessaire. 
3. Entrez l'adresse IP, le masque de sous-réseau et la passerelle que vous souhaitez utiliser pour monter le partage CIFS/SMB. Prenez soin d'utiliser un autre sous-réseau que celui utilisé pour l'interface principale.
4. Conservez la valeur par défaut **1500 bytes** pour **MTU**. 
  * La taille MTU maximale par défaut est 1500, mais vous pouvez indiquer une autre valeur pouvant aller jusqu'à 9000 si vous utilisez des trames Jumbo. Votre infrastructure physique et hôte ESXi doit impérativement prendre en charge les trames Jumbo. Par défaut, {{site.data.keyword.cloud_notm}} prend en charge une taille MTU de 9000 octets sans qu'aucune modification de configuration ne soit nécessaire. 
5. Cliquez sur **Apply**. 

Le dispositif AltaVault est maintenant configuré pour autoriser les communications entre lui-même, {{site.data.keyword.cos_full_notm}} et Veeam Backup & Replication. 

## Veeam Backup & Replication
{: #veeam-backup-replication-recover}

Le logiciel Veeam Backup & Replication fournit des fonctions de sauvegarde, de réplication et de récupération complètes pour les machines virtuelles et leurs données. Veeam Backup & Replication peut être entièrement intégré à un dispositif AltaVault Cloud Gateway Appliance, pour une expérience de sauvegarde et de reprise transparente. 

### Déploiement de Veeam Backup & Replication
{: #deploy-veeam-backup-replication}

Une version d'évaluation de Veeam Backup & Replication Version 8 est utilisée dans l'exemple. 

#### *Prérequis*
{: #prerequisites-deploy-veeam-backup-replication}

Avant de procéder à ce déploiement, vérifiez que les conditions prérequises suivantes sont remplies :

* Vous disposez d'un dispositif AltaVault qui est configuré pour être utilisé avec IBM Cloud Object Storage et Veeam Backup & Replication.
* Procurez-vous une copie de Veeam Backup & Replication pour les environnements VMware ; il s'agit d'un seul fichier exécutable. Contactez votre représentant Veeam pour obtenir une copie ou téléchargez une [version d'évaluation de 30 jours![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}.
* Procurez-vous un fichier de licence à utiliser avec Veeam Backup & Replication. Dans la plupart des cas, ce fichier est envoyé à l'adresse électronique qui a été utilisée pour télécharger Veeam Backup & Replication. Si vous n'avez pas reçu ce fichier, contactez votre représentant Veeam.<br/><br/>Le fichier de licence permet d'activer toutes les fonctions Veeam Backup & Replication. Si ce fichier n'est pas fourni lors de l'installation du programme, la version d'évaluation de 30 jours est rétablie pour tous les dispositifs et toutes les fonctions. 
* Vous devez disposer d'une instance de serveur virtuel mise à disposition dans IBM Cloud répondant aux spécifications décrites dans le tableau 2. **Remarque :** le système d'exploitation installé doit être une version 64 bits. 

||Minimum|Recommandé|
|---|---|---|
|**Système d'exploitation**|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>Windows Server 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|<ul><li>Windows Servers 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>WIndows 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|
|**# de coeurs ou d'unités centrales virtuelles (vCPU)**|2|4|
|**Mémoire**|Mémoire RAM de base de 4 Go plus 500 Mo pour chaque travail de sauvegarde et de restauration simultané.|Mémoire RAM de base de 16 Go plus 4 Go pour chaque travail de sauvegarde et de restauration simultané. |
|**Espace disque**|2 Go pour l'installation du produit, 10 Go par tranche de 100 machines virtuelles pour le dossier du catalogue de systèmes invités (données permanentes). |2 Go pour l'installation du produit, 10 Go par tranche de 100 machines virtuelles pour le dossier du catalogue de systèmes invités (données permanentes). |
|**Réseau**|LAN 1 Gbps pour la sauvegarde et la réplication sur site, WAN 1 Mbps pour la sauvegarde et la réplication hors site.|LAN 1 Gbps pour la sauvegarde et la réplication sur site, WAN 1 Mbps pour la sauvegarde et la réplication hors site.|
{: caption="Tableau 2. Configuration requise pour une instance de serveur virtuel {{site.data.keyword.cloud_notm}}" caption-side="top"}

Mettez à disposition l'instance de serveur virtuel dans le même centre de données que le dispositif AltaVault et l'environnement vSphere. L'instance de serveur virtuel doit avoir accès au dispositif AltaVault et à vCenter.

### Installation de Veeam Backup & Replication
{: #installing-veeam-backup-replication-disaster}

Procédez comme suit pour installer Veeam Backup & Replication sur l'instance de serveur virtuel {{site.data.keyword.cloud_notm}} une fois que toutes les conditions prérequises sont remplies :

1. Cliquez deux fois sur le programme exécutable et cliquez sur **Veeam Backup & Replication – Installation**. L'assistant de configuration s'ouvre.
* Cliquez sur **Suivant** et sélectionnez **J'accepte les termes du contrat de licence**. 
* Cliquez sur **Suivant** et spécifiez l'emplacement du fichier de licence obtenu sous Déployer Veeam Backup & Replication.
* Cliquez sur **Suivant** et sélectionnez les composants Veeam Backup & Replication que vous avez installés et leur emplacement d'installation sur l'écran **Fonctions du programme**. **Remarque :** **Veeam Backup & Replication** et **Veeam Backup Catalog** sont des composants obligatoires. 
* Cliquez sur **Suivant**.<br/>L'assistant d'installation exécute une série de vérifications pour s'assurer que toutes les infrastructures de programme et tous les composants de support qui sont nécessaires sont installés.
S'il en manque, il propose de les installer. <!--Click **Install** if this is the case.-->
2. Vérifiez que tous les composants ont **réussi** le contrôle des systèmes et cliquez sur **Suivant**.
3. Sélectionnez le **compte (utilisateur) de service** sur lequel Veeam Backup Service doit s'exécuter. Le compte de service par défaut est le **compte LOCAL SYSTEM**. Cliquez sur **Suivant**.
4. Sélectionnez l'**instance de serveur SQL** que vous souhaitez utiliser pour créer et stocker les bases de données Veeam Backup & Replication. Pour plus d'informations, contactez votre administrateur de base de données.
Cliquez sur **Suivant**.
5. Entrez le **port de service de catalogue** (9393) et le **port de service Veeam Backup** (9392). Cliquez sur **Suivant**.
6. Sélectionnez les répertoires dans lesquels vous souhaitez stocker le catalogue de systèmes de fichiers invités (données permanentes) et le cache en écriture vPower NFS (données non permanentes). Cliquez sur **Suivant**.
7. Assurez-vous que tous les paramètres et toutes les valeurs sont corrects, puis cliquez sur **Installer**. Une fois l'installation terminée, cliquez sur **Terminer**.

### Configuration de Veeam Backup & Replication
{: #configuring-veeam-backup-replication}

Une fois Veeam Backup & Replication installé, vous êtes prêt à vous connecter au dispositif AltaVault. 

1. Lancez Veeam Backup & Replication.
2. Dans l'angle inférieur gauche de l'écran, cliquez sur **Infrastructure de sauvegarde**.
3. Dans la fenêtre **Infrastructure de sauvegarde**, cliquez sur **Serveurs gérés > Serveurs gérés**.
4. Dans le menu ruban en haut de l'écran, cliquez sur **Ajouter un serveur** et cliquez deux fois sur **VMware vSphere**.
5. Entrez l'adresse IP privée du serveur vSphere et cliquez sur **Suivant**.
6. Entrez les données d'identification d'un compte local doté des privilèges d'administrateur sur le serveur vSphere précédemment spécifié. **Remarque :** le nom d'utilisateur de compte doit être au format DOMAIN\USER pour les comptes de domaine ou au format HOST\USER pour les comptes locaux. Pour ajouter un compte, cliquez sur **Ajouter** et entrez le nom d'utilisateur et le mot de passe appropriés. Ne modifiez pas le port de services Web VMware par défaut sauf si votre administrateur de réseau vous demande de le faire.<!-- otherwise during [Veeam Backup & Replication installation](#_Installing_Veeam_Backup).-->Cliquez sur **Suivant**.
  * Veeam Backup & Replication tente de se connecter au serveur vSphere. Si la tentative de connexion échoue, vérifiez que le compte existe et possède des privilèges d'administrateur sur le serveur vSphere avant de relancer une tentative de connexion. 
7. Cliquez sur **Terminer**.
8. Vérifiez que le serveur vSphere a été ajouté en cliquant sur **Serveurs gérés > VMware vSphere**. 

### Ajout d'un référentiel de sauvegarde à Veeam Backup & Replication
{: #adding-a-backup-repository-to-veeam-backup-replication-disaster}

Utilisez la procédure décrite ci-après pour créer un nouveau référentiel de sauvegarde dans lequel stocker toutes les sauvegardes restaurées sur le dispositif AltaVault. **Remarque : ** un partage CIFS/SMB est utilisé pour l'exemple car le serveur de sauvegarde Veeam Backup & Replication doit être hébergé sur une machine qui exécute Windows. Le protocole de partage qui est utilisé pour la restauration de sauvegardes dans le dispositif AltaVault {{site.data.keyword.cloud_notm}} doit être le même que celui qui est utilisé pour ces mêmes sauvegardes dans le dispositif AltaVault sur site pour répliquer des données vers le cloud. 

1. Dans l'angle supérieur gauche, cliquez sur **Infrastructure de sauvegarde > Référentiels de sauvegarde**.
2. Dans le menu ruban en haut de l'écran, cliquez sur **Ajouter un référentiel**. 
3. Affectez au référentiel un **nom** unique ainsi qu'une **description** appropriée. Cliquez sur **Suivant**.
4. Sélectionnez le type de partage qui correspond au type de partage qui est utilisé dans le dispositif AltaVault sur site. Par exemple, si le type de partage qui est utilisé dans le dispositif AltaVault sur site est CIFS/SMB, sélectionnez **Dossier partagé**.
5. Spécifiez l'emplacement à utiliser pour la restauration des sauvegardes du partage CIFS/SMB sur le dispositif AltaVault. Le partage porte le même nom que le partage utilisé pour le traitement des sauvegardes sur le dispositif AltaVault sur site. Par exemple, si le partage nommé `cifs_test1` est utilisé pour les sauvegardes dans le dispositif AltaVault sur site, spécifiez l'emplacement du partage `cifs_test1` sur le dispositif AltaVault {{site.data.keyword.cloud_notm}}. 
  * Ouvrez un navigateur Web et entrez l'adresse IP du dispositif AltaVault pour déterminer l'emplacement. 
  * Accédez à **Stockage > CIFS** et notez le **chemin de partage** du partage. **Remarque :** il NE s'agit PAS du chemin local du partage. Le format du chemin de partage est `\\<AltaVault appliance hostname>\<share name>`. Remplacez le nom d'hôte du dispositif AltaVault dans le chemin de partage par l'adresse IP de l'interface réseau e0b (point de montage du partage) du dispositif AltaVault.<br/>
   * Cliquez sur **Settings > Data Interfaces** dans la fenêtre de gestion du dispositif AltaVault pour trouver l'adresse IP de l'interface **e0b**. Pour plus d'informations sur la configuration des montages CIFS/SMB, voir Configuration du point de montage CIFS/SMB dans AltaVault.
   * Le chemin de partage spécifié dans Veeam Backup & Replication doit être `\\10.120.108.133\cifs_test1`, et non `\\restore-appliance\cifs_test1`.
6. Revenez à Veeam Backup & Replication, entrez le chemin de partage du point de montage dans la zone **Dossier partagé** et cliquez sur **Suivant**. Veeam Backup & Replication tente d'établir une connexion au point de montage. Si la tentative de connexion échoue, revenez en arrière et vérifiez que les paramètres réseau du dispositif AltaVault sont corrects avant de faire une nouvelle tentative. 
7. Renseignez la zone **Limiter le nombre maximum de tâches simultanées** avec le nombre de ressources disponibles sur l'écran **Nouveau référentiel de sauvegarde**. Cette valeur représente le nombre maximal de tâches qu'un proxy de sauvegarde peut envoyer au partage sélectionné. Le nombre par défaut de tâches simultanées est quatre. AltaVault suggère de démarrer avec cinq tâches simultanées et d'augmenter ou de réduire cette valeur en fonction des ressources. 
8. Cliquez sur **Suivant**.
9. Spécifiez les paramètres vPower NFS facultatifs sur l'écran **vPower NFS**. Si vous laissez la case **Activer le serveur vPower NFS** non cochée, Veeam Backup & Replication utilise vPower pour la reprise et la vérification de reprise. Cliquez sur **Suivant**.
10. Sélectionnez **Importer les sauvegardes existantes automatiquement** sauf si vous utilisez la sélection manuelle des sauvegardes de machine virtuelle. 
11. Assurez-vous que tous les paramètres sont corrects, puis cliquez sur **Suivant**. 
* Cliquez sur **Terminer** pour quitter l'assistant. 
12. Cliquez sur **Oui** pour poursuivre.

Veeam Backup & Replication est configuré et vous pouvez commencer à restaurer des sauvegardes. 

### Restauration de l'environnement
{: #restoring-the-environment}

Procédez comme suit pour restaurer intégralement une machine virtuelle :

1. Dans l'angle inférieur gauche de l'écran, cliquez sur **Backup & Replication**.
2. Dans le menu ruban en haut de l'écran, cliquez sur **Restaurer**. 
3. Sélectionnez **VM entière (y compris l'enregistrement)**. **Remarque :** n'effectuez aucune sélection sous **Répliquer à partir d'une réplique**. Cliquez sur **Suivant**.
4. Cliquez sur **Ajouter une VM > A partir d'une sauvegarde**. Les machines virtuelles précédemment répliquées sur {{site.data.keyword.cos_full_notm}} via le  dispositif AltaVault sur site et le partage CIFS/SMB sélectionné s'affichent. Cliquez sur **Ajouter > Suivant**.
5. Restaurez les machines virtuelles sur un nouvel emplacement <!--[because the original location of the VM(s) failed]--> en sélectionnant **Restaurer à un nouvel emplacement, ou avec des paramètres différents**. Cliquez sur **Suivant**.
6. Sur l'écran **Hôte**, sélectionnez un hôte cible pour chaque machine virtuelle que vous restaurez. Sélectionnez une machine virtuelle et cliquez sur **Hôte**.
7. Sélectionnez l'hôte cible dans la fenêtre **Sélectionner l'hôte** et cliquez sur **OK**.<br/>Veeam Backup & Replication tente de contacter les hôtes cible pour s'assurer qu'ils sont en ligne et prêts à recevoir des données. Si cette tentative échoue, vérifiez que tous les paramètres réseau sont corrects avant de faire une nouvelle tentative. Une fois la vérification terminée, cliquez sur **Suivant**.
8. Sélectionnez un **pool de ressources** facultatif pour chaque machine virtuelle. Cliquez sur **Suivant**.
9. Sélectionnez un magasin de données cible et un type de disque pour chaque machine virtuelle que vous restaurez. Cliquez sur **Suivant**.<br/>Veeam Backup & Replication tente de valider les magasins de données cible. Si la validation échoue, assurez-vous que le magasin possède suffisamment d'espace pour les machines virtuelles qui sont en cours de restauration et vérifiez tous les paramètres réseau avant d'essayer de vous reconnecter.
10. Sélectionnez un dossier de destination sur les magasins de données cible pour chaque machine virtuelle que vous souhaitez restaurer. Cliquez sur **Suivant**.
11. Spécifiez des connexions et des paramètres réseau pour chaque machine virtuelle. Cliquez sur **Suivant**.
12. Entrez éventuellement un **motif de restauration** pour expliquer les raisons pour lesquelles vous effectuez la restauration. Cliquez sur **Suivant**.<br/>Veeam Backup & Replication tente de valider les machines virtuelles que vous restaurez. Attendez la fin de cette validation pour continuer. 
13. Vérifiez les paramètres et les opérations de restauration dans la fenêtre **Backup & Replication** et cliquez sur **Terminer**. Veeam Backup & Replication démarre automatiquement la restauration des machines virtuelles sélectionnées. 

## Etapes suivantes
{: #next-steps-recovering-your-vmware-vsphere-environment}

<!--The recent rise in Object Storage can be heavily attributed to enterprises' desire to reduce or even eliminate the operational and capital expenditures associated with comparatively expensive on-premises storage. While Object Storage is a highly cost-effective solution, it introduces a new problem of how to access stored files. In the case of stored data backups, the issue is how to use them to recover an environment that has failed for whatever reason.-->

<!--This problem is handily resolved by Veeam Backup & Replication, along with a NetApp AltaVault Cloud Gateway appliance, and {{site.data.keyword.cos_full_notm}}. When used together, they create a seamless backup and recovery experience that can be tailored to fit a wide variety of environmental requirements and constraints. By following the steps outlined in this procedure, enterprises can quickly realize the benefits of cloud-based storage, while reducing the complexities involved in shifting from on-premises storage to Object Storage using the three aforementioned technologies.-->

Pour plus d'informations sur les composants de cette solution hybride, voir :

* Le [site Web NetApp AltaVault ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://www.netapp.com/us/products/protection-software/altavault/){: new_window}
* Le [site Web Veeam Backup & Replication ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}
* Le [site Web IBM Cloud Object Storage![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://www.ibm.com/cloud/storage){: new_window}

Le terme *serveur utilitaire* désigne un serveur qui contient le client VMware vSphere qui est utilisé pour accéder à un environnement privé et via lequel le dispositif AltaVault qui est utilisé pour les opérations de récupération de données est déployé. 
