---
copyright:
  years: 2014, 2018
lastupdated: "2018-02-14"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Initiation à VMware ESXi
{: #getting-started-with-vmware-esxi}

Une fois votre serveur ESXi mis à disposition, pour y accéder, vous devez avant toute chose vous connecter au VPN {{site.data.keyword.cloud}} Private Network. Par défaut, pour tous les serveurs VMware déployés par {{site.data.keyword.cloud_notm}}, seule l'interface ou liaison de réseau privé est configurée avec une adresse IP. Ce déploiement est une mesure de sécurité supplémentaire qui est mise en place afin de limiter l'exposition de votre nouveau serveur ESXi à l'Internet public. En raison de cette sécurité renforcée, le serveur est en mode écoute uniquement sur une adresse IP 10.x.x.x privée {{site.data.keyword.cloud_notm}}, accessible uniquement via le VPN {{site.data.keyword.cloud_notm}} ou un autre VPN de vos hôtes {{site.data.keyword.cloud_notm}} existants disposant d'un accès au VLAN privé sur lequel réside votre nouveau serveur VMware. 

Pour plus d'informations sur le VPN {{site.data.keyword.cloud_notm}}, voir [Initiation à la mise en réseau VPN](/docs/infrastructure/iaas-vpn?topic=VPN-getting-started-with-virtual-private-networking-vpn-).

## Accès à votre hôte ESXi
{: #accessing-your-esx-host}

Une fois que vous êtes connecté au VPN {{site.data.keyword.cloud_notm}}, vous pouvez communiquer avec votre nouveau serveur VMware. ESXi est géré par un client vSphere. Vous pouvez effectuer des extractions à partir de votre nouveau serveur VMware en entrant l'adresse IP de réseau privé dans un navigateur Web et en cliquant sur le lien "Download vSphere Client" sur la page qui s'affiche. 

Une fois le client vSphere téléchargé et installé sur votre poste de travail en local, vous pouvez démarrer l'application et entrez votre adresse privée de serveur et vos données d'identification de connexion. Vos données d'identification de connexion s'affichent sur la page Devices dans le portail {{site.data.keyword.slportal_full}} après que vous cliquez sur l'onglet Passwords. 

1. Entrez l'adresse IP privée du serveur, l'utilisateur root et le mot de passe dans les zones appropriées du client vSphere et cliquez sur **Login** pour vous connecter. 
2. Maintenant que vous êtes connecté au serveur, accédez à votre nouvel hôte ESXi en sélectionnant **Inventory**
3. Votre hôte ESXi est affiché en tant que noeud disponible pour la configuration sur la page affichée. A partir de là, plusieurs possibilités s'offrent à vous pour déployer une machine virtuelle. Une méthode consiste à télécharger une image ISO d'installation du système d'exploitation de votre choix sur le magasin de données local du serveur. Une fois l'image ISO téléchargée, vous pouvez la sélectionner comme support d'installation lorsqu'une machine virtuelle est créée.   

## Téléchargement d'une image ISO
{: #uploading-an-iso}

Pour télécharger une image ISO sur le magasin de données du serveur, procédez comme suit :

1. Mettez en évidence le serveur (représenté par une icône de serveur et son adresse IP privée) dans le panneau de gauche, puis sélectionnez l'onglet **Configuration** dans le panneau de droite.
2. Sélectionnez **Hardware > Storage**. Assurez-vous que l'option **View** a pour valeur **Datastores**.
3. Cliquez avec le bouton droit de la souris sur le magasin de données approprié et cliquez sur **browse datastore**.
4. La page qui s'affiche présente un gestionnaire de fichiers permettant de rechercher, envoyer/recevoir par téléchargement des fichiers vers et depuis le magasin de données.   
  * Pour télécharger une image ISO, cliquez sur l'icône de volume (avec la flèche vers le haut) et sélectionnez **Upload File**.
5. Sélectionnez sur votre système de fichiers local le fichier ISO que vous souhaitez télécharger sur le magasin de données et cliquez sur **Open**.
6. L'image ISO que vous avez choisie est désormais dans le magasin de données. 
7. A présent que vous support d'installation se trouve sur le serveur VMWare, vous pouvez passer à la [création de votre machine virtuelle](/docs/infrastructure/vmware?topic=VMware-creating-a-vmware-esx-virtual-machine).
