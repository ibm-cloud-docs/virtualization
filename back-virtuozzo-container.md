---
copyright:
  years: 2014, 2018
lastupdated: "2018-05-14"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Backing up a Virtuozzo container

1. Make sure that both hardware nodes are set up in PIM.
2. Go to the infrastructure and hardware nodes that the container is on.
3. Select the appropriate container and click **Back up**.
4. Add an appropriate comment that describes the reason for backing up this data.
5. Select the backup type:
   * Use Hardware Node Settings
   * Full (for this example)
   * Incremental
   * Differential
6. Select the Compression Level: **Note:** Higher compression can save disk space, but uses more CPU resources
   * Use Hardware Node Settings
   * None
   * Normal (choose normal minimize server load)
   * High
   * Maximum
7. Select the hardware node where you want the backup file housed. If you have multiple hardware nodes, then it is advised that you save your backups to these nodes, rather than the same server.
  * Other options are at the administrator's digression.
8. Backup. The backup is complete. You can view the details of the backup by going to the **Hardware Node, Container, and Backups** tab and selecting the backup. 

