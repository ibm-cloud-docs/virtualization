---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-22"
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Migrating a container to a different hardware node

1. Make sure that you have both hardware nodes set up in PIM.
2. Go to **Infrastructure** and select the hardware node that the container is on.
3. Select the container and click **Migrate**.
4. Select the **Destination Node**.
5. Choose if you want the migration to be performed live. 

**Note**: Depending on the size of the container, a live migration might not be feasible because most of the information in the containers need to be held in RAM until the migration is complete.

6. Under **Advanced**, you can select whether or not to remove the containers files that are on the source node after migration. Specify not to automatically start the container on the destination node, or force the migration (which may cause issues if you have another node using the same IP or if the destination node does not have the correct application/OS templates installed and cached).
7. You can view the **Details** to monitor the migration process.
8. The migration is complete. The container automatically moves to the destination node, which can be found under **Infrastructure** in the left menu pane.
