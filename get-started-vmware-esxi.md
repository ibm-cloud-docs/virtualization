---
copyright:
  years: 2014, 2023
lastupdated: "2023-04-04"

subcollection: virtualization

---

{{site.data.keyword.attribute-definition-list}}

# Getting started with VMware ESXi
{: #getting-started-with-vmware-esxi}

After your ESXi server is provisioned, you need to connect it to the {{site.data.keyword.cloud}} Private Network VPN to access the server. By default, all {{site.data.keyword.cloud_notm}}-deployed VMware servers have only the private network interface or bond that is configured with an IP address. This deployment is an extra security measure to limit exposure of your new ESXi server to the public internet. As a result, the server listens only on an {{site.data.keyword.cloud_notm}} private 10.x.x.x IP address, which is accessible only through the {{site.data.keyword.cloud_notm}} VPN or another one of your existing {{site.data.keyword.cloud_notm}} hosts with access to the same private VLAN on which your new VMware server resides.
{: shortdesc}

For more information about the {{site.data.keyword.cloud_notm}} VPN, see [Getting started with Virtual Private Networking (VPN)](/docs/iaas-vpn?topic=iaas-vpn-getting-started).

## Accessing your ESXi host
{: #accessing-your-esx-host}

1. After you connect to the {{site.data.keyword.cloud_notm}} VPN, you can communicate with your new VMware server. ESXi is managed by a vSphere client. You can download your new VMware server by entering the private network IP address into a web browser and click the "Download vSphere Client" link on the resulting page.
1. After the vSphere client downloads and you install it on your local workstation, you can start the application and enter your server private address and login credentials. Your login credentials are viewed on the Devices page in the {{site.data.keyword.slportal_full}} after your click the Passwords tab.
1. Enter the server's private IP address, root user, and password in the appropriate fields of the vSphere client and click **Login** to connect.
1. Go to your new ESXi host by selecting **Inventory**
1. Your ESXi host is shown as an available node for configuration on the resulting page. From here, you have multiple ways to deploy a virtual machine. One method is to upload an installation ISO of your preferred operating system to the server local data store. After the ISO is uploaded, you can select the ISO as the installation medium when a VM is created.  

## Uploading an ISO
{: #uploading-an-iso}

Complete the following steps to upload an ISO to the server's data store.

1. Highlight the server (represented by a server icon and its private IP address) in the left pane, and select the **Configuration** tab in the right pane.
1. Select **Hardware > Storage**. Make sure the **View** is set to **Data stores**.
1. Right-click the appropriate data-store and click **browse data store**.
1. The resulting page has a file manager to browse, upload and download files to and from the data store.  
   * To upload an ISO, click the volume icon (with the upward pointing arrow) and select **Upload File**.
1. Select the ISO file on your local file system that you want to upload to the data store and click **Open**.
1. The ISO of your choosing is now in the data store.
1. Now that your install medium is on the VMWare server, you can proceed with [creating your virtual machine](/docs/vmware?topic=vmware-create-esx-vm).
