---
copyright:
  years: 2014, 2021
lastupdated: "2021-09-17"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:note: .note}
{:important: .important}

# Migrating a container to a different hardware node
{: #migrating-a-container-to-a-different-hardware-node}

1. Make sure that you have both hardware nodes set up in PIM.
2. Go to **Infrastructure** and select the hardware node that the container is on.
3. Select the container and click **Migrate**.
4. Select the **Destination Node**.
5. Choose if you want the migration to be performed live.

   Depending on the size of the container, a live migration might not be feasible. Most of the information in the containers needs to be held in RAM until the migration is complete.
   {: note}

6. Under **Advanced**, you can select whether or not to remove the containers files that are on the source node after migration. Specify to not automatically start the container and to not force the migration. Forcing the migration can cause issues if you have another node that is using the same IP. Another migration issue is that the destination node does not have the correct application or OS templates that are installed and cached.
7. You can view the **Details** to monitor the migration process.
8. The migration is complete. The container automatically moves to the destination node, which can be found under **Infrastructure** in the left menu pane.
