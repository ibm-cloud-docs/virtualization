---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Configuration d'un réseau de machines virtuelles
{: #setting-up-a-virtual-machine-network}

La configuration de réseau sur votre nouvelle machine virtuelle s'effectue en quelques étapes. Vous devez disposer d'un bloc d'adresses IP portables séparées pour le réseau public et pour le réseau privé. L'on suppose que vous utilisez une offre de virtualisation qui requiert des blocs d'adresses IP secondaires sur le domaine de diffusion VLAN et qui n'est pas routée vers un sous-réseau VLAN (sans ID de réseau/passerelle/diffusion). Si vous ne prévoyez pas d'utiliser le réseau privé sur votre machine virtuelle, vous n'avez besoin que du sous-réseau public. Les blocs d'adresses IP portables peuvent être commandés directement sur le portail en accédant à [Ventes->Ajouter des adresses IP ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://manage.softlayer.com/Sales/orderAdditionalServices/subnet){: new_window} ou à partir de [Public Network IP Manager ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://manage.softlayer.com/PublicNetwork/ipManager){: new_window}.

Après vous être procuré vos blocs d'adresses IP, vous pouvez configurer la mise ne réseau sur la machine virtuelle. Si la machine virtuelle n'est pas installée, vous pouvez configurer votre bloc de réseau public via le processus d'installation de système d'exploitation. Il s'agit de la méthode la plus rapide pour configurer un réseau public. Toutes les étapes de configuration de réseau partent du principe que la première interface réseau est le réseau privé et que la seconde interface réseau est le réseau public.

Le processus de configuration d'un réseau est différent pour chaque système d'exploitation. Cette rubrique contient des informations détaillées relatives à la configuration du réseau pour les systèmes d'exploitation ci-dessous. **Remarque :** la configuration de réseau est identique pour les systèmes d'exploitation qui sont regroupés. 

* Windows 2008 Server Core
* Windows 2003 Standard et Entreprise
* Windows 2008 Web, Standard, Entreprise et Datacenter
* RedHat, Fedora et CentOS
* Ubuntu et Debian

La configuration de réseau requiert les informations ci-après pour les blocs d'adresses IP publiques et privées. Ces informations figurent sur le portail, sous [Réseau public -> Gestionnaire d'adresses IP![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://manage.softlayer.com/PublicNetwork/ipManager){: new_window} pour les blocs d'adresses publiques et sous [Réseau privé -> Gestionnaire d'adresses IP![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://manage.softlayer.com/PrivateNetwork/ipManager){: new_window} pour les blocs d'adresses IP privées. 

    -IP Address
    -Gateway
    -Subnet Mask
    --Subnet conversion
    ---/29 - 255.255.255.248
    ---/28 - 255.255.255.240
    ---/27 - 255.255.255.224
    ---/26 - 255.255.255.192
    ---/25 - 255.255.255.128
    ---/24 - 255.255.255.0

## Windows 2008 Server Core
{: #windows-2008-server-core}

L'édition Windows 2008 Server Core ne fournit pas d'interface graphique pour configurer le réseau système. La configuration doit être effectuée manuellement à l'aide de l'invite de commande. Vous devez exécuter les commandes ci-dessous. Les adresses IP qui sont utilisées dans cet exemple doivent être remplacées par les adresses IP provenant de vos blocs d'adresses IP. Ces commandes fonctionnent avec des versions antérieures de Windows Server qui incluent la commande [netsh ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://docs.microsoft.com/en-us/previous-versions/tn-archive/bb490939(v=technet.10)){: new_window}. 

**Exemple de bloc d'adresses IP publiques – 192.0.2.0/29**

* Adresse IP – 192.0.2.2
* Passerelle – 192.0.2.1
* Masque de sous-réseau – 255.255.255.248

**Exemple de bloc d'adresses IP privées – 10.0.0.0/29**

* Adresse IP – 10.0.0.2
* Passerelle – 10.0.0.1
* Masque de sous-réseau – 255.255.255.248

**Réseau public**

La commande suivante crée le réseau public qui permet de connecter votre serveur à Internet :

* *Netsh interface ip set address name=”Local Area Connection 2” static 192.0.2.2 255.255.255.248 192.0.2.1*

Les commandes suivantes créent les entrées DNS (si vous n'utilisez pas le réseau privé, vous devez remplacer ces adresses IP par des serveurs DNS actifs) :

* *Netsh interface ip add dns “Local Area Connection 2” 10.0.80.11*
* *Netsh interface ip add dns “Local Area Connection 2” 10.0.80.12*

**Réseau privé**

Les deux commandes ci-après configurent le réseau privé et créent la route permanente pour celui-ci. La route permanente permet à votre réseau privé d'accéder à l'ensemble du réseau privé. L'accès inclut les serveurs DNS dans la configuration de réseau public. **Remarque :** aucune passerelle n'est affectée au réseau privé. 

* `Netsh interface IP set address name=”Local Area Connection” static 10.0.0.2 255.255.255.248`
* `Route add 10.0.0.0 mask 255.0.0.0 10.0.0.1 -p`

## Windows 2003 Standard et Entreprise
{: #windows-2003-standard-and-enterprise}

Vous pouvez utiliser Windows 2003 pour configurer la mise en réseau à l'aide de la ligne de commande ou de l'interface graphique. Si vous souhaitez utiliser la ligne de commande, reportez-vous aux instructions de configuration pour Windows 2008 Server Core. Ces instructions s'appliquent également à Windows 2003. Les instructions relatives à l'utilisation de l'interface graphique sont indiquées ici. Les adresses IP qui sont utilisées dans cet exemple doivent être remplacées par les adresses IP issues de vos blocs d'adresses IP. 

**Exemple de bloc d'adresses IP privées – 10.0.0.0/29**

* Adresse IP – 10.0.0.2
* Passerelle – 10.0.0.1
* Masque de sous-réseau – 255.255.255.248

### Ouverture de vos configurations de réseau
{: #opening-your-network-configurations}

1. Accédez à **Menu Démarrer > Paramètres > Panneau de configuration**
2. Ouvrez **Connexions réseau**
   **Remarque :** si vous avez installé deux adaptateurs de réseau, "Connexion au réseau local" et "Connexion au réseau local 2" sont affichés. 

**Réseau public**

Utilisez les instructions suivantes pour configurer le réseau public via l'interface graphique Windows :

1. Cliquez avec le bouton droit de la souris sur **Connexion au réseau local > Propriétés** et sélectionnez **Protocole Internet (TCP/IP) > Propriétés**.
2. Accédez à **Général > Utiliser l’adresse IP suivante :**
3. Entrez votre adresse IP publique, votre masque de sous-réseau et votre passerelle. 
4. Sélectionnez **Utiliser les adresses de serveur DNS suivantes :**. Si vous configurez le réseau privé, utilisez les serveurs DNS ci-après. Si vous ne configurez pas le réseau privé, vous devez fournir des serveurs DNS. 
    * Serveur DNS préféré : 10.0.80.11
    * Serveur DNS auxiliaire : 10.0.80.12
5. Cliquez sur **OK**.

**Réseau privé**

1. Cliquez avec le bouton droit de la souris sur **Connexion au réseau local 2 > Propriétés** et sélectionnez **Protocole Internet (TCP/IP) > Propriétés**.
2. Accédez à **Général > Utiliser l’adresse IP suivante :** et entrez votre adresse IP privée et votre masque de sous-réseau. Laissez la zone de passerelle vide et cliquez sur **OK**
3. Cliquez à nouveau sur **OK** pour fermer la fenêtre de configuration de réseau. 

Pour fournir l'accès à l'ensemble du réseau privé incluant les serveurs DNS, vous devez ajouter une route personnalisée au serveur. Procédez comme suit pour fournir l'accès au réseau privé :

1. Accédez à **Démarrer > Exécuter**, entrez *cmd* et appuyez sur **OK**.
2. Exécutez la commande ci-après. **Remarque :** prenez soin de remplacer l'adresse de passerelle (10.0.0.1) par votre passerelle de bloc d'adresses IP privées. <br/>
*“route add 10.0.0.0 mask 255.0.0.0 10.0.0.1 –p”*

## Windows 2008 Web, Standard, Entreprise et Datacenter
{: #windows-2008-web-standard-enterprise-and-datacenter}

Si vous disposez de Windows 2008, vous configurez la mise en réseau à l'aide de l'invite de commande ou de l'interface graphique. Si vous souhaitez utiliser l'invite de commande, reportez-vous aux instructions de configuration pour Windows 2008 Server Core. Ces instructions s'appliquent également à tous les serveurs 2008. Les instructions relatives à l'utilisation de l'interface graphique sont indiquées ici. Les adresses IP qui sont utilisées dans cet exemple doivent être remplacées par les adresses IP issues de vos blocs d'adresses IP. 

**Exemple de bloc d'adresses IP privées – 10.0.0.0/29**

* Adresse IP – 10.0.0.2
* Passerelle – 10.0.0.1
* Masque de sous-réseau – 255.255.255.248

**Ouvrez vos configurations de réseau**

1. Accédez à **Menu Démarrer > Paramètres > Panneau de configuration**
2. Ouvrez **Centre Réseau et partage** et cliquez sur **Gérer les connexions réseau**
* Si vous avez installé deux adaptateurs de réseau, "Connexion au réseau local" et "Connexion au réseau local 2" sont affichés. 

**Réseau public**

Utilisez les instructions suivantes pour configurer le réseau public via l'interface graphique Windows :

1. Cliquez avec le bouton droit de la souris sur **Connexion au réseau local > Propriétés**.
2. Sélectionnez **Protocole Internet version 4 (TCP/IPv4)**.
3. Accédez à **Général > Utiliser l'adresse IP suivante :** et entrez votre adresse IP publique, votre masque de sous-réseau et votre passerelle. 
4. Select **Utiliser l'adresse de serveur DNS suivante :**. Si vous configurez le réseau privé, utilisez les serveurs DNS ci-après. Si vous ne configurez pas le réseau privé, vous devez fournir des serveurs DNS. 
    * Serveur DNS préféré : 10.0.80.11
    * Serveur DNS auxiliaire : 10.0.80.12
5. Cliquez sur **OK**.

**Réseau privé**

1. Cliquez avec le bouton droit de la souris sur **Connexion au réseau local 2 > Propriétés** et sélectionnez **Protocole Internet (TCP/IPv4) > Propriétés**.
2. Accédez à **Général > Utiliser l'adresse IP suivante :** et entrez votre adresse IP privée et votre masque de sous-réseau et cliquez sur **OK**. 
3. Cliquez à nouveau sur **OK** pour fermer la fenêtre de configuration de réseau. 

Pour fournir l'accès à l'ensemble du réseau privé incluant les serveurs DNS, vous devez ajouter une route personnalisée au serveur. 

1. Accédez à **Démarrer > Exécuter**, entrez "cmd" et appuyez sur **OK**.
2. Exécutez la commande ci-après. **Remarque :** prenez soin de remplacer l'adresse de passerelle (_10.0.0.1_) par votre passerelle de bloc d'adresses IP privées. 
  * `route add 10.0.0.0 mask 255.0.0.0 10.0.0.1 –p`

## RedHat, Fedora et CentOS
{: #redhat-fedora-and-centos}

La configuration du réseau sur RedHat, Fedora et CentOS s'effectue en éditant manuellement les fichiers de configuration. Pour éditer manuellement les fichiers, vous devez vous connecter à la machine virtuelle.

**Exemple de bloc d'adresses IP publiques – 192.0.2.0/29**

* Adresse IP – 192.0.2.2
* Passerelle – 192.0.2.1
* Masque de sous-réseau – 255.255.255.248

**Exemple de bloc d'adresses IP privées – 10.0.0.0/29**

* Adresse IP – 10.0.0.2
* Passerelle – 10.0.0.1
* Masque de sous-réseau – 255.255.255.248

**Réseau public**

Les paramètres de réseau public sont contenus dans le fichier ci-après. Vous devez éditer ce fichier à l'aide des informations fournies. Remplacez les exemples d'adresse IP par les adresses IP provenant de votre bloc d'adresses IP publiques. Si le fichier n'existe pas, créez-le. S'il existe, remplacez toutes les données qu'il contient par les informations suivantes :
* /etc/sysconfig/network-scripts/ ifcfg-eth1
      DEVICE=eth1
      BOOTPROTO=static
      BROADCAST=192.0.2.7
      IPADDR=192.0.2.2
      NETMASK=255.255.255.248
      NETWORK=192.0.2.0

      GATEWAY=192.0.2.1
      ONBOOT=yes

**Réseau privé**

Les paramètres de réseau privé sont contenus dans le fichier ci-après. Vous devez éditer ce fichier à l'aide des informations fournies. Remplacez les exemples d'adresse IP par les adresses IP correctes de votre bloc d'adresses IP privées. Si le fichier n'existe pas, créez-le. S'il existe, remplacez toutes les données qu'il contient par les informations ci-après.   **Remarque :** le réseau privé *NE CONTIENDRA PAS* de route par défaut, par conséquent, la *PASSERELLE* n'est pas définie. 

* /etc/sysconfig/network-scripts/ ifcfg-eth0
      DEVICE=eth0
      BOOTPROTO=static
      IPADDR=10.0.0.2
      NETMASK=255.255.255.248
      ONBOOT=yes

Enfin, une nouvelle route est requise pour fournir un accès privé à l'ensemble du réseau privé afin d'inclure le serveur DNS. Cela s'effectue en éditant le fichier ci-après. Ce fichier n'existe pas, par conséquent, il doit être créé. **Remarque :** remplacez "10.0.0.1" dans l'exemple par votre passerelle IP de sous-réseau privé. 

* /etc/sysconfig/network-scripts/route-eth0
      10.0.0.0/8 via 10.0.0.1

**Configuration DNS**

La configuration DNS primaire et la configuration DNS secondaire sont contenues dans un fichier séparé. Vous devez éditer le fichier à l'aide des informations ci-après. Si cette machine virtuelle ne possède pas d'accès au réseau privé, vous devez remplacer les adresses IP du serveur par celles des serveurs DNS que vous souhaitez utiliser. 

* /etc/resolv.conf
      nameserver 10.0.80.11
      nameserver 10.0.80.12

Pour que ces modifications soient prises en compte, vous devez redémarrer la mise en réseau sur le serveur. Vous pouvez redémarrer la mise en réseau en exécutant la commande suivante :

* `service network restart`

## Ubuntu et Debian
{: #ubuntu-and-debian}

Vous configurez les réseaux Ubuntu et Debian via un seul fichier de configuration. Vous devez éditer ce fichier de configuration à l'aide des informations ci-après. Pour éditer ce fichier, vous devez disposer d'un accès root au serveur. L'installation de base d'Ubuntu ne fournit pas de connexion root. Cependant, l'utilisateur qui a été créé au cours du processus d'installation peut accéder à sudo. Vous devez utiliser la commande sudo pour éditer le fichier si vous exécutez Ubuntu.

**Exemple de bloc d'adresses IP publiques – 192.0.2.0/29**

* Adresse IP – 192.0.2.2
* Passerelle – 192.0.2.1
* Masque de sous-réseau – 255.255.255.248

**Exemple de bloc d'adresses IP privées – 10.0.0.0/29**

·Adresse IP – 10.0.0.2
·Passerelle – 10.0.0.1
·Masque de sous-réseau – 255.255.255.248

**Réseau public et réseau privé**

Editez le fichier suivant à l'aide de n'importe quel éditeur de texte et remplacez les exemples d'adresse IP par les adresses IP provenant de vos blocs d'adresses IP publiques et privées :

* /etc/network/interfaces
      auto lo
      iface lo inet loopback

      auto eth1
      iface eth1 inet static
      address 192.0.2.2
      netmask 255.255.255.248
      gateway 192.0.2.1

      auto eth0
      iface eth0 inet static
      address 10.0.0.2
      netmask 255.255.255.248

      #Static route for backend service network
      up route add -net 10.0.0.0/8 gw 10.0.0.1

**Configuration DNS**

La configuration DNS primaire et la configuration DNS secondaire sont contenues dans un fichier séparé. Vous devez éditer ce fichier de configuration à l'aide des informations ci-après. Si cette machine virtuelle ne possède pas d'accès au réseau privé, vous devez remplacer les adresses IP du serveur par celles des serveurs DNS que vous souhaitez utiliser. 

* /etc/resolv.conf
      nameserver 10.0.80.11
      nameserver 10.0.80.12

Pour que ces modifications soient prises en compte, vous devez redémarrer la mise en réseau sur le serveur. Vous pouvez redémarrer le réseau en exécutant la commande suivante :

* */etc/init.d/network restart*

**Remarque :** l'adresse 192.0.2.0/24 est utilisée pour la documentation sur les adresses IP publiques conformément à RFC1166 et RFC5737. Vous ne pouvez pas utiliser cette adresse IP sur vos serveurs.
