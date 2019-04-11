---
copyright:
  years: 2014, 2018
lastupdated: "2018-05-14"

subcollection: virtualization

keywords: Virtuozzo
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Sauvegarde d'un conteneur Virtuozzo
{: #backing-up-a-virtuozzo-container}

1. Assurez-vous que les deux noeuds matériels sont configurés dans PIM.
2. Accédez au noeud d'infrastructure et au noeud matériel dans lesquels se trouve le conteneur. 
3. Sélectionnez le conteneur approprié et cliquez sur **Back up**.
4. Ajoutez un commentaire pertinent décrivant la raison pour laquelle vous souhaitez sauvegarder ces données. 
5. Sélectionnez le type de sauvegarde :
   * Utilisez las paramètres de noeud matériel
   * Intégrale (pour cet exemple)
   * Incrémentielle
   * Différentielle
6. Sélectionnez le niveau de compression. **Remarque :** Un niveau de compression plus élevé permet d'économiser de l'espace disque, mais consomme davantage de ressources d'unité centrale. 
   * Utilisez las paramètres de noeud matériel
   * Aucun
   * Normal (choisissez la charge de serveur normale)
   * Elevé
   * Maximal
7. Sélectionnez le noeud matériel qui doit héberger le fichier de sauvegarde. Si vous possédez plusieurs noeuds matériels, nous vous conseillons de sauvegarder vos sauvegardes sur ces noeuds, plutôt que sur le même serveur. 
  * Les autres options sont laissées à la discrétion de l'administrateur. 
8. Effectuez la sauvegarde. La sauvegarde est terminée. Vous pouvez afficher les détails de la sauvegarde en accédant à l'onglet **Hardware Node, Container, and Backups** et en sélectionnant la sauvegarde. 
