---
copyright:
  years: 2014, 2018
lastupdated: "2018-02-14"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Getting started with VMware ESXi
{: #getting-started-with-vmware-esxi}

After your ESX server is provisioned, the first step to accessing your server is connecting to the {{site.data.keyword.cloud}} Private Network VPN. By default, all {{site.data.keyword.cloud_notm}} deployed VMware servers have only the private network interface or bond that is configured with an IP address. This deployment is an extra security measure that is put in place to limit exposure of your new ESX server to the public internet. As a result of this added security, the server listens only on an {{site.data.keyword.cloud_notm}} private 10.x.x.x IP address, which is accessible only through the {{site.data.keyword.cloud_notm}} VPN or another one of your existing {{site.data.keyword.cloud_notm}} hosts with access to the same private VLAN on which your new VMware server resides.

For more information about the {{site.data.keyword.cloud_notm}} VPN, see [Getting started with Virtual Private Networking (VPN)](/docs/infrastructure/iaas-vpn?topic=VPN-getting-started-with-virtual-private-networking-vpn-).

## Accessing your ESX host
{: #accessing-your-esx-host}

After you connect to the {{site.data.keyword.cloud_notm}} VPN, you can communicate with your new VMware server. ESX is managed by a vSphere client. You can download your new VMware server by entering the private network IP address into a web browser and click the "Download vSphere Client" link on the resulting page.

After the vSphere client downloads and you install it on your local workstation, you can start the application and enter your server private address and login credentials. Your login credentials are viewed on the Devices page in the {{site.data.keyword.slportal_full}} after your click the Passwords tab.

1. Enter the server's private IP address, root user, and password in the appropriate fields of the vSphere client and click **Login** to connect.
2. Now that you are connected to the server, go to your new ESX host by selecting **Inventory**
3. Your ESX host is shown as an available node for configuration on the resulting page. From here, you have a number of ways to deploy a virtual machine. One method is to upload an installation ISO of your preferred operating system to the server local datastore. After the ISO is uploads, you can select the ISO as the installation medium when a VM is created.  

## Uploading an ISO
{: #uploading-an-iso}

Complete the following steps to upload an ISO to the server's datastore.

1. Highlight the server (represented by a server icon and its private IP address) in the left pane, and select the **Configuration** tab in the right pane.
2. Select **Hardware > Storage**. Make sure the **View** is set to **Datastores**.
3. Right-click the appropriate data-store and click **browse datastore**.
4. The resulting page has a file manager to browse, upload/download files to and from the datastore.  
  * To upload an ISO, click the volume icon (with the upward pointing arrow) and select **Upload File**.
5. Select the ISO file on your local filesystem that you want to upload to the datastore and click **Open**.
6. The ISO of your choosing is now in the datastore.
7. Now that your install medium is on the VMWare server, you can proceed with [creating your virtual machine](/docs/infrastructure/vmware?topic=VMware-creating-a-vmware-esx-virtual-machine).
