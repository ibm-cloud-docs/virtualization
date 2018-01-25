---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-09"
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Activating XenServer

The Citrix XenServer free license is licensed for 30 days from the initial install or reload. During this time, you need to register with Citrix and request a fully functional 1-year license. To create this license, Citrix requires personal information that {{site.data.keyword.BluSoftlayer}} does not have access to. The license is an agreement between you and Citrix to use XenServer. 
{:shortdesc}

If you do not register your system within 30 days, you cannot power on any virtual machines. 
{:tip}

To register your system, complete the following steps:

1. Download and install the latest XenCenter Client. <!-- either from the [private network download page ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://downloads.service.softlayer.com/){: new_window} or--> You can access it from the [Citrix XenCenter ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://community.citrix.com/display/xs/XenCenter){: new_window} page.

2. Connect to your private network through VPN.

3. Start XenCenter and click **Add New Server**, or click **Server > Add...** from the drop-down menu.

4. Use the private IP address for the **Hostname**, 'root' for **User Name**, and your root password for **Password**.

5. After the system connects, a dialog box appears. This dialog box alerts you that your license expires in 30 days. If the dialog box does not appear, from the drop-down menu, go to **Tools > License Manager...**.

6. Check your server and click **Activate...**. A browser opens and navigates to the Citrix registration site. Complete the form and submit it. Check your inbox for an email from Citrix. This email contains your license key as an attachment.

7. After you receive the email, save the attachment locally.

8. In the XenCenter left pane, highlight your server and in the top menu, go to **Server > Install License Key...**. A dialog box appears that asks for the location of the license key. Point it to the location you saved it in Step 7.


