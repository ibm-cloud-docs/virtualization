---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Regroupement en pool pour XenServer
{: #xenserver-pooling}

Le regroupement en pool pour XenServer s'apparente à un groupe de 16 serveurs au maximum qui fonctionnent comme un cluster. Ce cluster autorise non seulement des ressources partagées mais également la migration active de machines virtuelles qui utilisent XenMotion. Un serveur agit en tant que "noeud maître" tandis que les autres noeuds sont des esclaves. Le fait d'ouvrir l'un des serveurs du pool via XenCenter affiche les informations de gestion pour l'ensemble du pool. Lorsqu'un noeud rejoint le pool, son mot de passe de gestion est modifié afin de correspondre au mot de passe de gestion du noeud maître. Lorsque vous retirez un noeud du pool, ce mot de passe n'est pas modifié. 

Tous les systèmes du noeud doivent provenir de la même famille de processeurs, par exemple, Intel ou AMD. Dans l'idéal, le matériel doit être identique pour que le regroupement en pool aboutisse. Les problèmes de compatibilité n'existent pas vraiment si un regroupement en pool d'unité centrale hétérogène est utilisé. Vous devez effectuer des tests de compatibilité par vous-même afin de déterminer si votre matériel est compatible. Pour plus d'informations, voir [Understanding heterogeneous CPU pooling ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://support.citrix.com/article/CTX127059){: new_window}.

Vous disposez de deux méthodes pour créer un pool XenServer. L'une d'elles consiste à utiliser l'option "New Pool" de la console XenCenter. Suivez les instructions étape par étape affichées à l'écran afin de créer et d'ajouter des systèmes au pool. L'autre méthode revient à utiliser la ligne de commande. Sur le nouveau noeud esclave, la commande suivante peut être exécutée : 

        `xe pool-join master-address= master-username= master-password=`

**Remarque** : l'adresse IP de gestion est l'adresse IP 10.x.x.x du serveur si vous avez utilisé l'installation par défaut. 

Lorsque vous travaillez avec des réseaux liés, il se peut que les interfaces d'un esclave nouvellement attaché soient associées à des réseaux liés incorrects. Pour remédier à ce problème, détruisez les liens sur l'esclave à partir de la ligne de commande et recréez les liens avec les PIF et les réseaux appropriés. 

Vous disposez de deux méthodes pour éjecter un noeud esclave du pool. La première consiste à utiliser XenCenter. Accédez à **Pool** et sélectionnez **Remove Server**. Suivez les instructions étape par étape affichées à l'écran. L'autre méthode revient à utiliser la ligne de commande. Le retrait d'un noeud peut être effectué à partir de n'importe quel noeud du pool. Vous devez localiser l'_uuid_ du noeud que vous souhaitez éjecter en exécutant la commande suivante :

        `xe host-list`

Ensuite, exécutez la commande suivante :

        `xe pool-eject uuid=`

**Remarque **: lorsqu'un noeud est éjecté, il est réamorcé dans une nouvelle installation, qui inclut le retrait de la configuration de la mise en réseau qui est utilisée pour fournir la connectivité publique et privée. Le mot de passe du noeud esclave est toujours le même que celui du noeud maître après l'éjection du pool. Vous pouvez être amené à  reconfigurer la mise en réseau via l'interface IPMI car le retrait peut interrompre les paramètres des esclaves. 
