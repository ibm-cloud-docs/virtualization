---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-23"

subcollection: virtualization

keywords: Windows 2008
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Installation de Windows Server Virtualization sous Windows 2008
{: #installing-windows-server-virtualization-on-windows-2008}

<!--Windows 2008 64-bit edition comes with the option to install Windows next generation virtualization application codenamed Veridian. By default the application will not be a selectable option when Adding and Removing Roles from Windows 2008.

Note: At this time, this is only available in full installation of 64-bit editions of Windows 2008. 32-bit versions and Server-core installations do not support this.-->

## Avant de commencer
{: #before-you-begin-win-2008}

Avant de commencer, passez en revue les conditions requises présentées ci-dessous.

### Matériel
{: #hardware}

* Technologie de virtualisation par matériel activée dans le BIOS
* Intel VT
* AMD-V
* Fonction Data Execution Prevention activée dans le BIOS
* Intel Execute Disable (XD)
* AMD No Execute (NX)

### Logiciels
{: #software}

Pour activer l'option **Rôle** pour Windows Server Virtualization, quelques correctifs doivent être installés. 
1. Ouvrez une fenêtre de navigateur et accédez à %sysdir%\Windows\wsv, en général, C:\Windows\wsv. Les deux fichiers suivants se trouvent dans ce dossier :
    * Windows6.0-KB939853-x64.msu
    * Windows6.0-KB939854-x64.msu
2. Ces correctifs peuvent être installés dans n'importe quel ordre. Installez les deux correctifs, puis redémarrez le système. 

## Installation
{: #installation}

1. Une fois le système redémarré, vous devez ajouter le **rôle** au système. 
2. Après que vous avez ajouté le rôle, la boîte de dialogue **Créer des réseaux virtuels** s'affiche.
**Remarque :** vous perdez temporairement la connectivité du réseau. 
3. Sélectionnez **Local Area Connection**, votre adaptateur de réseau privé. Cliquez sur **Continuer**. L'installation se poursuit et nécessite un redémarrage. 
4. Une fois le système redémarré, connectez-vous via la connexion publique. **Remarque :** vous devez vous connecter avec les données d'identification de connexion que celles que vous avez utilisées pour installer cette connexion. Vous perdrez temporairement la connectivité du réseau avec l'interface. Si tel n'est pas le cas, vous recevrez peut-être le message d'erreur suivant :
    * *Une tentative de configuration de Windows Server Virtualization a échoué avec le code d’erreur 0x80078000.*
Pour résoudre ce problème, accédez à **Démarrer > Programmes > Outils d'administration > Windows Virtualization Management** dans la console de gestion pour Windows Virtualization.
5. Dans le panneau de droite, cliquez sur le serveur approprié.
6. Dans le panneau d'actions, cliquez sur **Virtual Network Manager**. **Virtual Network Switch Management** apparaît.
7. Dans le panneau de gauche, cliquez sur le commutateur réseau sous **Add New Network Switch**. 
8. Renommez le commutateur réseau private.
9. Sélectionnez un adaptateur de réseau physique pour **Connection** et sélectionnez le premier adaptateur de réseau. Tous les protocoles de réseau sont désormais dissociés de l'interface de réseau privé. 
10. **IMPORTANT : ** pour réétablir la connectivité du réseau avec private, vous devez configurer le nouveau commutateur, et **NON** l'interface privée. Accédez **Démarrer > Paramètres > Connexions réseau**. Une nouvelle unité nommée **Local Area Connection 2** est visible.
11. Cliquez avec le bouton droit de la souris sur cette connexion et accédez à **Propriétés**.
12. Sélectionnez **ipv4** et ses propriété. Vous devez configurer cette unité avec l'adresse IP et le masque de réseau de l'interface de réseau privé, et les serveurs DNS. 
13. Cliquez sur **OK > Fermer**. Cette configuration réactive la mise en réseau côté privé. Vous pouvez vérifier que le réseau est privé en contactant l'adresse IP privée au moyen d'une commande PING. 

Utilisez RDP vers l'adresse IP privée pour configurer le réseau public.

### Ajout d'un commutateur public
{: #adding-a-public-switch}

La procédure d'ajout d'un commutateur public est identique à la procédure d'ajout du commutateur privé.
1. Revenez à **Virtual Network Switch Management** et sélectionnez **Add new network switch**.
2. Sélectionnez **External** comme type de commutateur réseau et cliquez sur **Add**.
3. Renommez le commutateur **Public** et sélectionnez **Physical network adapter**.
4. Sélectionnez le second adaptateur de réseau et cliquez sur **OK**. Avec cette configuration, le port public ne répond par au réseau. Configurez la nouvelle interface de commutateur public en procédant comme pour l'interface de commutateur privé mais avec les paramètres qui lui sont propres.
