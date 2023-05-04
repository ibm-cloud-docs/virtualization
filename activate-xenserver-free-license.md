---
copyright:
  years: 2014, 2023
lastupdated: "2023-04-04"

subcollection: virtualization

keywords: Citrix XenServer
---

{{site.data.keyword.attribute-definition-list}}

# Activating XenServer
{: #activating-xenserver}

The Citrix XenServer free license is licensed for 30 days from the initial install or reload. During this time, you need to register with Citrix and request a fully functional 1-year license. To create this license, Citrix requires personal information that {{site.data.keyword.BluSoftlayer}} doesn't have access to. The license is an agreement between you and Citrix to use XenServer.
{: shortdesc}

If you don't register your system within 30 days, you can't power on any virtual machines.
{: tip}

To register your system, complete the following steps:

1. Download and install the XenCenter client.

2. Connect to your private network through the VPN.

3. Start XenCenter and click **Add new server**, or click **Server > Add...** from the drop-down menu.

4. Use the private IP address for the **hostname**, 'root' for **username**, and your root password for **Password**.

5. After the system connects, a dialog box appears. This dialog box alerts you that your license expires in 30 days. If you don't see a dialog box, go to **Tools > License Manager...** from the drop-down menu.

6. Check your server and click **Activate...**. A browser opens and goes to the Citrix registration site. Complete the form and submit it. Check your inbox for an email from Citrix. This email has your license key as an attachment.

7. After you receive the email, save the attached license key file locally.

8. In the XenCenter left pane, highlight your server. In the menu bar, go to **Server > Install license key...**. A dialog box appears that asks for the location of the license key. Point it to the saved location from Step 7.
