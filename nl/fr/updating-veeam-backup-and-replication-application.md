---
copyright:
  years: 2014, 2018
lastupdated: "2018-02-05"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Mise à jour de l'application Veeam Backup and Replication
{: #updating-veeam-backup-and-replication-application}

## Présentation
{: #overview}

Procédez comme suit pour mettre à jour l'application Veeam Backup and Replication qui s'exécute sur votre serveur lorsque de nouvelles mises à jour sont disponibles pour le produit :

### Prérequis
{: #prerequisites-updating-veeam-backup-and-replication-application}

* Un compte Veeam gratuit. Vous pouvez vous inscrire sur le site
[Veeam Registration ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://www.veeam.com/signin.html).
* Internet Explorer, Chrome, Firefox ou Safari.

### Recherche de mises à jour
{: #checking-for-updates}

L'application Veeam Backup and Replication comprend un vérificateur de mises à jour. Pour être certain d'être prévenu lorsque des mises à jour sont disponibles, accédez à l'icône de menu dans l'angle supérieur gauche et cliquez sur **General Options** > **Notifications**. Assurez-vous que l'option **Check for product and hypervisor updates periodically** est cochée.

### Mise à jour de Veeam
{: #updating-veeam}

Pour démarrer la mise à jour, procédez comme suit :
1. Ouvrez la vue **Backup Infrastructure**.
2. Dans le panneau de l'inventaire, accédez au noeud **Managed servers** > **Missing Updates**.
3. Cliquez sur le lien de Update.

Quelques points à prendre en compte avant de démarrer la mise à jour :

* Assurez-vous que l'exécution en cours de tous les travaux existants est terminée. Si certains travaux ont échoué, relancez-les.
* Assurez-vous qu'aucun travail n'est en cours d'exécution, y compris les sessions de restauration, les sessions Instant VM Recovery et les travaux SureBackup. Il est recommandé de ne pas arrêter les travaux qui sont en cours d'exécution.
* Désactivez temporairement les travaux de copie de sauvegarde et les travaux périodiques pour les empêcher de démarrer lors de la mise à niveau.

### Etapes suivantes
{: #next-steps-updating-veeam-backup-and-replication-application}

Vous pouvez maintenant extraire la mise à jour téléchargée et cliquez deux fois sur le programme d'installation de mises à jour Veeam. La mise à jour est envoyée à la fois sur le serveur Veeam local et sur les hyperviseurs sur lesquels réside l'agent Veeam. Une fois le processus terminé, démarrez l'application Veeam Backup and Replication et réactivez vos travaux de sauvegarde.
