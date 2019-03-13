---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-10"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:table: .aria-labeledby="caption"}

# Running Hyper-V in an active directory environment
{: #running-hyper-v-in-an-active-directory-environment}

<!--Running Hyper-V in an Active Directory environment is the best implementation of Hyper-V. Microsoft has truly shined with the ability to remotely manage a server.You can remotely manage the Hyper-V server allows for it to be installed on a Core installation of Windows freeing up those valuable resources from the system for use within the VM’s. When this is combined with an Active Directory Domain Controller
You manage all your Hyper-V servers from a single Hyper-V Manager that runs on any 2008 or Vista computer that is connected to the Domain.--> <!--Vista?? MS doesn't support Vista.-->

## Before you begin
{: #before-you-begin-running-hyper-v-in-an-active-directory-environment}

Before you begin running Hyper-V in an active directory environment, you need the following:

* 64-bit Windows 2008 Datacenter server that runs Hyper-V (Full or Core installation of the OS).
* A global group on the domain that is used to manage Hyper-V.
* Domain Admin access to enable changes on the Hyper-V server from the computer that you are working from.

## Configuring the Hyper-V server
{: #configuring-the-hyper-v-server}

1. Open a management connect to the Hyper-V server.

2. Add **Hyper-V** group to the **Distributed COM Users** group.

3. Add **Hyper-V** group to the **CIMV2 and Virtualization** namespaces.

4. Add **Hyper-V** group to the Authorization store for Hyper-V on the Hyper-V server.

5. Provide the **Hyper-V** group permissions to the Hyper-V Directory on the Hyper-V server.

## Setting up remote management connection
{: #setting-up-remote-management-connection}

Make sure you are logged into a computer on the domain with Domain Admin privileges.

1. Go to **Control panel > Administrative Tools > Computer Management**.

2. In the Action menu, select **Connect to another computer**.

3. Enter the server name or IP and click **OK**.

## Adding Distributed COM Users group to the Hyper-V server
{: #adding-distributed-com-users-group-to-the-hyper-v-server}

You first need to add the Hyper-V group to the Distributed COM Users group on the Hyper-V server.

1. Go to **System Tools > Local Users and Groups > Groups > Distributed COM Users > Add to Group**.

2. Click **Add** and enter the group name for the Hyper-V group and click **OK**.

## Granting remote access to the server for CIMV2 and Virtualization namespaces
{: #granting-remote-access-to-the-server-for-cimv2-and-virtualization-namespaces}

You can update the permissions for Remote access to the server for Virtualization and CIMV2.

1. From the Computer Management window, select **Service and Applications > WMI Control**.

2. Right-click and go to **Properties > Security > Root > CIMV2 > Security**.

3. Add the Hyper-V group, then select it and click **Advanced**.

4. Make sure that the new group is selected and click **Edit**.

5. Change **Apply to:** to **The namespace and all subnamespaces**.

6. For **Enable Account and Remote enable**, make sure that **Allow** is selected.

7. Select for **Apply these permissions to objects and/or containers within this container only** and click **OK**.

8. Repeat these steps for Virtualization.

## Updating authorization store
{: #updating-authorization-store}

The authorization store for Hyper-V is the final component that gives domain group to Hyper-V.

1. Open the Authorization Manager by running the command `azman.msc` from the run menu or a command prompt.

2. From the Action menu select **Open Authorization Store**.

3. Make sure that **XML** is selected. Now, you need to remotely access `InitalStore.xml` on the Hyper-V server. Use the following path:

`\HOSTNAME\c$\ProgramData\Microsoft\Windows\Hyper-V\InitialStore.xml`

4. Go to **Hyper-V services > Role Assignments > Administrator**.

5. From the **Action Menu**, select **Assign Users and Groups > From Windows and Active Directory**.

6. Add the Hyper-V group.

## Granting folder permissions
{: #granting-folder-permissions}

Now that the Hyper-V group has complete permissions to manage Hyper-V remotely, you need to give permissions to write to the `C:\Users\Public\Documents\Hyper-V` folder.

1. Open My Computer and go to the following address:

`\HOSTNAME\c$\Users\Public\Documents`

2. Go to **Hyper-V > Properties > Security**

3. Add the Hyper-V group and make sure that is has the ability to Read, Write and Execute files from that directory. In general, it is easier to assign **Full control**.

## Finalizing configuration
{: #finalizing-configuration}

All configuration changes are complete. To finalize the configuration, you need to restart the Hyper-V server. After the server is back online, connect to it from your Local Hyper-V Manager. You now have full access to manage all VMs and the Hyper-V Service.
