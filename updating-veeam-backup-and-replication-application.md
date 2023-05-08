---
copyright:
  years: 2014, 2023
lastupdated: "2023-04-08"

subcollection: virtualization
---

{{site.data.keyword.attribute-definition-list}}

# Updating Veeam Backup and Replication
{: #updating-veeam-backup-and-replication-application}

Use the following information to update Veeam Backup and Replication when new updates are available.
{: shortdesc}

## Prerequisites
{: #prerequisites-updating-veeam-backup-and-replication-application}

* A Veeam account. You can sign up at
[Veeam registration](https://www.veeam.com/signin.html){: external}.
* Internet Explorer, Chrome, Firefox, or Safari installed.

## Checking for updates
{: #checking-for-updates}

Veeam Backup and Replication includes an update checker. To make sure that you are notified about updates, go to the upper-left menu icon and click **General options** > **Notifications**. Make sure that **Check for product and hypervisor updates periodically** is selected.

## Updating Veeam
{: #updating-veeam}

To start the update, follow these steps.

1. Open the **Back up infrastructure** view.
2. In the inventory page, go to **Managed servers node** > **Missing updates**.
3. Click the update link.

Before you start the update, keep the following information in mind.

* Make sure that the current run for all existing jobs is complete. If some jobs failed, rerun the failed jobs.
* Make sure that no jobs are running, including restore sessions, Instant VM Recovery sessions, and SureBackup jobs. It is recommended that you do not stop running jobs.
* Temporarily disable any periodic and backup copy jobs to prevent them from starting during the upgrade.

## Next steps
{: #next-steps-updating-veeam-backup-and-replication-application}

You can now extract the downloaded update and double-click the Veeam update installer. The update pushes to both the local Veeam server and the hypervisors that the Veeam agent is on. After the process completes, start Veeam Backup and Replication, and re-enable your backup jobs.
