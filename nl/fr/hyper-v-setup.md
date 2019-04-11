---



copyright:
  years: 2014, 2018
lastupdated: "2018-08-16"

subcollection: virtualization


---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Configuration de Hyper-V
{: #setting-up-hyper-v}

Le processus de configuration de Hyper-V comprend la configuration initiale de Hyper-V, la création d'une machine virtuelle et la configuration de réseau sur la nouvelle machine virtuelle. Ces processus sont expliqués ci-après. 

## Utilisation de Hyper-V Manager
{: #using-hyper-v-manager}

Hyper-V Manager est la console de gestion que vous utilisez pour faire fonctionner Hyper-V. A partir de Hyper-V Manager, vous pouvez créer, démarrer, arrêter, supprimer et configurer toutes vos machines virtuelles. Vous utilisez également la console de gestion pour définir l'emplacement de stockage par défaut de vos disques durs virtuels et l'emplacement de stockage par défaut de vos fichiers de configuration de machine virtuelle. 

Hyper-V Manager se trouve sous l'option **Outils d'administration** dans le panneau de configuration Windows. Sur une nouvelle installation de Windows 2008, l'option **Outils d'administration** se trouve sous Programmes dans le menu Démarrer. Après que vous avez ouvert Hyper-V Manager, une nouvelle fenêtre s'affiche à l'écran. Prenez un moment pour vous familiariser avec cet écran. Toutes les configurations et l'utilisation de vos machines virtuelles s'effectuent ici. 

## Configuration de Hyper-V
{: #configuring-hyper-v}

La première configuration à vérifier est l'emplacement de stockage de vos disques durs virtuels. Dans la fenêtre Hyper-V Manager, sur le côté gauche, **Hyper-V Manager** et le nom du serveur sont affichés. Cliquez sur le nom du serveur pour afficher les informations disponibles pour le service Hyper-V sur votre serveur. Pour l'instant, le milieu de l'écran est vide, ce qui indique qu'aucune machine virtuelle n'est installée. Sur le côté droit de l'écran figure un lien intitulé **Hyper-V Settings**. Si vous cliquez sur ce lien, les paramètres de base de Hyper-V s'affichent.

Le principal paramètre que vous devez vérifier est **Virtual Hard Disks**. Sur le côté droit est affiché l'emplacement de stockage par défaut de vos disques durs virtuels. L'emplacement par défaut est le suivant :

`C:\Users\Public\Documents\Hyper-V\Virtual Hard Disks`

Lorsque vous créez une machine virtuelle, vous pouvez spécifier un emplacement si vous ne souhaitez pas stocker ce disque dur virtuel spécifique dans l'emplacement par défaut. Par exemple, vous souhaitez stocker vos disques durs virtuels sur une seconde unité plus volumineuse (dans cet exemple, l'unité D) et vous souhaitez que ces unités virtuelles soient stockées dans le dossier **VirtualMachine** (entrez `D:\VirtualMachine` dans la zone). 

****

Vous pouvez également parcourir manuellement les dossiers afin de sélectionner l'emplacement que vous souhaitez utiliser. Après avoir sélectionné un emplacement, cliquez sur **OK**.

## Configuration des périphériques de réseau
{: #configuring-network-devices}

Avant de configurer vos périphériques de réseau virtuel, déterminez les adaptateurs de réseau qui sont connectés au réseau privé et au réseau public. Dans le menu Démarrer, cliquez sur **Connexions réseau**. Deux périphériques de réseau sont affichés. Prenez soin de vérifier que les périphériques sont nommés **PrivateNetwork** et**PublicNetwork**. Cliquez avec le bouton droit de la souris sur **PrivateNetwork** et sélectionnez **Propriétés**. **Se connecter en utilisant :** apparaît, avec le nom du périphérique pour l'adaptateur de réseau privé. Notez ce nom car vous aurez besoin de cette information pour créer votre périphérique de réseau privé virtuel. 

### Configuration d'un périphérique de réseau privé
{: #configuring-a-private-network-device}

Maintenant que l'emplacement de stockage par de vos disques durs virtuels est configuré, vous devez configurer les périphériques de réseau virtuel qui sont utilisés par les machines virtuels. Pour ouvrir le gestionnaire de réseau virtuel, cliquez sur **Virtual Network Manager** sur le côté droit de la fenêtre Hyper-V. 

La première fois que vous accédez à cette fenêtre, les réseaux virtuels sont présentés sur la gauche avec la seule option **Nouveau réseau virtuel**. Les trois options suivantes sont affichées sur la droite :
* Externe
* Interne
* Privé. Assurez-vous que l'option **Externe** est sélectionnée et cliquez sur **Ajouter**.

Entrez des informations dans les autres zones. 
* **Nom** : Nom qui est associé au périphérique de réseau. Pour cet exemple, utilisez _Privé_ pour indiquer que ce périphérique est connecté au réseau privé d'IBM Cloud. 
* **Type de connexion** : Sélectionnez le périphérique qui correspond à votre réseau privé. Il s'agit du nom que vous avez découvert précédemment dans la fenêtre **Connexions réseau**. Après avoir sélectionné le périphérique approprié, **OK**. Un message d'avertissement apparaît pour vous signaler qu'une perte de la connexion réseau peut survenir. Cliquez sur **Oui** pour continuer. 

### Configuration d'un périphérique de réseau public
{: #configuring-a-public-network-device}

Votre périphérique de réseau privé est désormais installé. Répétez les étapes précédentes pour le réseau public. Pour cet exemple, utilisez **Public** pour le nom de périphérique. Sous Type de connexion, sélectionnez l'autre périphérique de réseau, c'est-à-dire qui n'a pas été utilisé dans la configuration du réseau privé. Après avoir créé les périphériques de réseau public et privé, cliquez sur **OK**.

## Obtention du support d'installation
{: #obtaining-installation-media}

Maintenant que les périphériques de réseau virtuel sont créés, vous devez vous procurer le support d'installation. Le support d'installation qui est utilisé dans cet exemple est un fichier image de CD/DVD. IBM Cloud prend en charge les systèmes d'exploitation suivants pour Hyper-V :
* Windows 2008
* Windows 2003
* CentOS
* Fedora
* Ubuntu
**Remarque :** si vous disposez déjà du support d'installation, accédez à la rubrique **Création d'une machine virtuelle**.

Le support d'installation de [CentOS ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](http://centos.org){: new_window}, [Fedora ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](http://fedoraproject.org/){: new_window} et [Ubuntu ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](http://www.ubuntu.com/){: new_window} est disponible sur le site Web de chacun de ces systèmes d'exploitation. Ces trois systèmes d'exploitation sont concédés sous licence libre et aucune autre action n'est requise de la part de l'utilisateur. 

## Création d'une machine virtuelle
{: #creating-a-virtual-machine}

Maintenant que vous disposez du support d'installation, vous pouvez créer une machine virtuelle à partir de  Hyper-V Manager. Cliquez sur **Nouveau > Machine virtuelle** pour démarrer l'**assistant de création de machine virtuelle**.

Sur le premier écran, cliquez sur **Suivant** et entrez un nom pour la nouvelle machine virtuelle. Ce nom peut être modifié ultérieurement. Vous pouvez modifier l'emplacement du disque dur virtuel. La valeur par défaut que vous avez précédemment définie est utilisée sauf si vous indiquez un autre emplacement. Après avoir entré les informations, cliquez sur **Suivant**.

Maintenant, vous devez allouer de la mémoire pour le serveur. En général, vous fournissez au minimum la mémoire minimale recommandée spécifiée par le système d'exploitation. Après avoir entré la quantité de mémoire à allouer, cliquez sur **Suivant**.

Ensuite, définissez le premier adaptateur de réseau. Si un système d'exploitation Linux est en cours d'installation sur la machine virtuelle, sélectionnez **Non connecté**. Si Windows est en cours d'installation, sélectionnez **Privé**, puis cliquez sur **Suivant**.

Vous pouvez modifier les paramètres suivants pour le disque virtuel : le nom de fichier, l'emplacement de stockage et la taille. Effectuez les modifications qui s'appliquent, puis cliquez sur **Suivant**.

Le support d'installation doit être défini. Sélectionnez **Installer un système d'exploitation à partir d'un CD/DVD-ROM de démarrage > Fichier image (.iso)** afin de définir l'emplacement du fichier image de CD/DVD pour votre support d'installation. Cliquez sur **Suivant**, consultez le récapitulatif, puis cliquez sur **Terminer** pour fermer la fenêtre. 

Enfin, vous devez créer les adaptateurs de réseau virtuel. La nouvelle machine virtuelle est désormais répertoriée sous la section **Machines virtuelles** de Hyper-v Manager. Cliquez avec le bouton droit de la souris sur la nouvelle machine virtuelle, puis cliquez sur **Paramètres**.

### Configuration d'un adaptateur de réseau virtuel pour le système d'exploitation Windows
{: #configuring-virtual-network-adapter-for-windows-operating-system}

Une liste de périphériques matériels est désormais disponible pour la machine virtuelle. Cette liste contient les entrées **Adaptateur de réseau** et **Privé**. Si **Non connecté** est affiché, cliquez sur l'adaptateur de réseau, sélectionnez **Privé**, puis cliquez sur **Appliquer**.

Ensuite, cliquez sur **Ajouter un matériel > Adaptateur de réseau > Ajouter** afin d'ajouter l'adaptateur de réseau au matériel. 

Sur le panneau de droite, sélectionnez **Public** dans la liste déroulante, puis cliquez sur **OK**.

### Configuration d'adaptateur de réseau virtuel pour le système d'exploitation Linux
{: #configuring-virtual-network-adapter-for-linux-operating-system}

Une liste de périphériques matériels est désormais disponible pour la machine virtuelle. Cliquez sur **Adaptateur de réseau > Retirer** et sélectionnez **Ajouter un matériel**. La liste présentée dans le panneau de droite est modifiée.
Dans cette liste, sélectionnez **Adaptateur de réseau hérité**. **Remarque :** cette option ne concerne pas l'adaptateur de réseau qui n'est pas utilisé sur une machine virtuelle Linux.
Cliquez sur **Ajouter** pour ajouter cet adaptateur de réseau hérité à la liste de périphériques matériels. Le nouvel adaptateur de réseau hérité est automatiquement sélectionné.
Sélectionnez **Privé** dans la liste déroulante, puis cliquez sur **Appliquer**.
Répétez les étapes précédentes pour ajouter l'**adaptateur de réseau hérité**. Toutefois, sélectionnez **Public**.
Une fois le nouvel adaptateur de réseau hérité ajouté, cliquez sur **OK**.

## Installation d'un système d'exploitation invité
{: #installing-a-guest-operating-system}

La machine virtuelle est maintenant prête à être démarrée. Cliquez avec le bouton droit de la souris sur la machine virtuelle et sélectionnez **Connecter**. La console de la machine virtuelle s'ouvre. Dans le menu **Action**, sélectionnez **Démarrer**. La nouvelle machine virtuelle démarrer en utilisant le support d'installation que vous avez sélectionné. A présent, vous exécutez l'installation du système d'exploitation. Durant le processus d'installation du système d'exploitation, il est recommandé de configurer les paramètres de réseau public. Le réseau public est la seconde interface de réseau. Une fois l'installation du système d'exploitation terminée, vous disposez d'une machine virtuelle qui fonctionne. Si un réseau public est configuré, vous pouvez accéder à distance à la machine virtuelle. 

L'étape finale du processus d'installation consiste à configurer le réseau privé. Si la machine virtuelle n'est pas connectée au réseau privé, le processus d'installation est terminé. Pour plus d'informations sur la configuration du réseau privé, voir [Configuration d'un réseau de machines virtuelles](/docs/infrastructure/virtualization?topic=Virtualization-setting-up-a-virtual-machine-network).
