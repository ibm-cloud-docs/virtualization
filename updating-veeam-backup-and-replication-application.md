---
copyright:
  years: 2014, 2018
lastupdated: "2018-02-05"
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Updating Veeam Backup and Replication Application

## Overview

Use the following steps to update the Veeam Backup and Replication application that is running on your server when new updates to the product are available.

### Prerequisites

* A free Veeam account. You can sign up at 
[Veeam Registration ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.veeam.com/signin.html).
* Internet Explorer, Chrome, Firefox, or Safari.

### Checking for updates

The Veeam Backup and Replication application includes an update checker. To make sure that you are notified about updates, go to the upper left menu icon and click **General Options** > **Notifications**. Make sure that **Check for product and hypervisor updates periodically** is selected.

### Updating Veeam

To start the update, follow these steps.
1. Open the **Backup Infrastructure** view.
2. In the inventory pane, go to **Managed servers node** > **Missing Updates**.
3. Click the update link. 

A few things to note before you start the update:

* Make sure that the current run for all existing jobs is complete. If some jobs failed, rerun the failed jobs.
* Make sure that no jobs are running, including restore sessions, Instant VM Recovery sessions, and SureBackup jobs. It is recommended that you do not stop running jobs. 
* Temporarily disable any periodic and backup copy jobs to prevent them from starting during the upgrade.

### Next steps

You can now extract the downloaded update and double-click the Veeam update installer. The update pushes to both the local Veeam server and the hypervisors that the Veeam agent is on. After the process completes, start Veeam Backup and Replication and re-enable your backup jobs.
