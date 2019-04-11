---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-10"

subcollection: virtualization

keywords: hypervisor
---
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:table: .aria-labeledby="caption"}

# Exécution de Hyper-V dans un environnement Active Directory
{: #running-hyper-v-in-an-active-directory-environment}

<!--Running Hyper-V in an Active Directory environment is the best implementation of Hyper-V. Microsoft has truly shined with the ability to remotely manage a server.You can remotely manage the Hyper-V server allows for it to be installed on a Core installation of Windows freeing up those valuable resources from the system for use within the VM’s. When this is combined with an Active Directory Domain Controller
You manage all your Hyper-V servers from a single Hyper-V Manager that runs on any 2008 or Vista computer that is connected to the Domain.--> <!--Vista?? MS doesn't support Vista.-->

## Avant de commencer
{: #before-you-begin-running-hyper-v-in-an-active-directory-environment}

Avant de commencer à exécuter Hyper-V dans un environnement Active Directory, vous devez disposer des éléments suivants :

* Un serveur Windows 2008 Datacenter 64 bits qui exécute Hyper-V (installation complète ou minimale du système d'exploitation)
* Un groupe global sur le domaine qui est utilisé pour gérer Hyper-V
* Des droits d'accès d'administrateur de domaine pour activer les modifications sur le serveur Hyper-V à partir de l'ordinateur sur lequel vous travaillez

## Configuration du serveur Hyper-V
{: #configuring-the-hyper-v-server}

1. Ouvrez une connexion de gestion avec le serveur Hyper-V. 

2. Ajoutez le groupe **Hyper-V** au groupe **Utilisateurs du modèle COM distribué**.

3. Ajoutez le groupe **Hyper-V** aux espaces de nom **CIMV2 et Virtualisation**. 

4. Ajoutez le groupe **Hyper-V** au magasin d'autorisations pour Hyper-V sur le serveur Hyper-V. 

5. Fournissez les droits de groupe **Hyper-V** au répertoire Hyper-V sur le serveur Hyper-V. 

## Configuration de la connexion de gestion à distance
{: #setting-up-remote-management-connection}

Assurez-vous que vous êtes connecté à un ordinateur sur le domaine avec des privilèges d'administrateur de domaine. 

1. Accédez à **Panneau de configuration > Outils d'administration > Gestion de l'ordinateur**.

2. Dans le menu Action, sélectionnez **Se connecter à un autre ordinateur**.

3. Entrez le nom ou l'adresse IP du serveur et cliquez sur **OK**.

## Ajout du groupe Utilisateurs du modèle COM distribué au serveur Hyper-V
{: #adding-distributed-com-users-group-to-the-hyper-v-server}

Vous devez d'abord ajouter le groupe Hyper-V au groupe Utilisateurs du modèle COM distribué sur le serveur Hyper-V. 

1. Accédez à **Outils système > Utilisateurs et groupes locaux > Groupes > Utilisateurs du modèle COM distribués > Ajouter au groupe**.

2. Cliquez sur **Ajouter**, entrez le nom du groupe Hyper-V et cliquez sur **OK**.

## Octroi de droits d'accès à distance au serveur pour les espaces de nom CIMV2 et Virtualisation
{: #granting-remote-access-to-the-server-for-cimv2-and-virtualization-namespaces}

Vous pouvez mettre à jour les droits d'accès à distance au serveur pour les espaces de nom Virtualisation et CIMV2.

1. Sur la fenêtre Gestion de l'ordinateur, sélectionnez **Service et applications > Contrôle WMI**.

2. Cliquez avec le bouton droit de la souris et accédez à **Propriétés > Sécurité > Racine > CIMV2 > Sécurité**.

3. Ajoutez le groupe Hyper-V, sélectionnez-le, puis cliquez sur **Avancé**.

4. Assurez-vous que le nouveau groupe est sélectionné, puis cliquez sur **Editer**.

5. Remplacez la valeur de **Appliquer à :** par **Cet espace de noms et les sous-espaces de noms**.

6. Pour **Activer le compte et Appel à distance autorisé**, assurez-vous que l'option **Autoriser** est sélectionnée. 

7. Sélectionnez **Appliquer ces autorisations uniquement aux objets et/ou aux conteneurs faisant partie de ce conteneur** et cliquez sur **OK**.

8. Répétez ces étapes pour l'espace de nom Virtualisation. 

## Mise à jour du magasin d’autorisations
{: #updating-authorization-store}

Le magasin d'autorisations pour Hyper-V est le composant final qui associe le groupe de domaines à Hyper-V.

1. Ouvrez le gestionnaire des autorisations en exécutant la commande `azman.msc` à partir du menu Exécuter ou d'une invite de commande. 

2. Dans le menu Action, sélectionnez **Ouvrir un magasin d’autorisations**.

3. Assurez-vous que **XML** est sélectionné. A présent, vous devez accéder à distance à `InitalStore.xml` sur le serveur Hyper-V. Utilisez le chemin d'accès suivant : 

`\HOSTNAME\c$\ProgramData\Microsoft\Windows\Hyper-V\InitialStore.xml`

4. Accédez à **Services Hyper-V > Affectation des rôles > Administrateur**.

5. Dans le menu **Action**, sélectionnez **Affecter des utilisateurs et des groupe > A partir de Windows et Active Directory**.

6. Ajoutez le groupe Hyper-V.

## Octroi de droits d'accès aux dossiers
{: #granting-folder-permissions}

Maintenant que le groupe Hyper-V possède les droits complets pour gérer Hyper-V à distance, vous devez octroyer des droits en écriture sur le dossier `C:\Users\Public\Documents\Hyper-V`. 

1. Ouvrez Mon ordinateur et accédez à l'adresse suivante :

`\HOSTNAME\c$\Users\Public\Documents`

2. Accédez à **Hyper-V > Propriétés > Sécurité**

3. Ajoutez le groupe Hyper-V et assurez-vous qu'il dispose des droits en lecture, en écriture et en exécution sur les fichiers de ce répertoire. En général, il est plus facile d'affecter un **contrôle complet**.

## Finalisation de la configuration
{: #finalizing-configuration}

Toutes les modifications de la configuration sont terminées. Pour finaliser la configuration, vous devez redémarrer le serveur Hyper-V. Une fois le serveur de nouveau en ligne, vous pouvez vous y connecter à partir de votre gestionnaire Hyper-V local. Vous disposez désormais d'un accès complet vous permettant de gérer toutes les machines virtuelles et le service Hyper-V. 
