---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-13"

subcollection: virtualization

keywords: Citrix XenServer 
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Activation de XenServer
{: #activating-xenserver}

La licence gratuite pour Citrix XenServer est valide pendant 30 jours à partir de la première installation ou du rechargement. Durant cette période, vous devez vous enregistrer auprès de Citrix et demander une licence d'un an entièrement fonctionnelle. Pour créer cette licence, Citrix a besoin d'informations personnelles auxquelles {{site.data.keyword.BluSoftlayer}} n'a pas accès. La licence est un accord entre vous et Citrix qui régit l'utilisation de XenServer.
{:shortdesc}

Si vous ne vous enregistrez pas votre système dans les 30 jours, vous ne pourrez mettre sous tension aucune machine virtuelle.
{:tip}

Pour enregistrer votre système, procédez comme suit :

1. Téléchargez et installez le client XenCenter. Vous pouvez accéder au client à partir de la page [Citrix XenCenter ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](http://community.citrix.com/display/xs/XenCenter){: new_window}. 

2. Connectez-vous à votre réseau privé via un VPN.

3. Démarrez XenCenter et cliquez sur **Add New Server**, ou cliquez sur **Server > Add...** dans le menu déroulant. 

4. Indiquez l'adresse IP privée dans la zone **Host name**, 'root' dans la zone **User name** et votre mot de passe root dans la zone **Password**.

5. Une fois le système connecté, une boîte de dialogue s'affiche. Cette boîte de dialogue vous informe que votre licence expirera dans 30 jours. Si aucune boîte de dialogue ne s'affiche, à partir du menu déroulant, accédez à **Tools > License Manager...**.

6. Cochez votre serveur et cliquez sur **Activate...**. Un navigateur s'ouvre et affiche la page du site d'enregistrement de Citrix. Remplissez le formulaire et soumettez-le. Recherchez dans votre boîte de réception un courrier électronique envoyé par Citrix. Votre clé de licence est jointe à ce courrier électronique. 

7. Après avoir reçu le courrier électronique, sauvegardez le fichier de clé de licence localement.

8. Dans le panneau de gauche de XenCenter, mettez en évidence votre serveur. Dans la barre de menus, accédez à **Server > Install license key...**. Une boîte de dialogue s'affiche pour vous demander l'emplacement de la clé de licence. Dirigez votre serveur vers l'emplacement de sauvegarde utilisé lors de l'étape 7. 
