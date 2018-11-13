---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-13"
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Activating XenServer

The Citrix XenServer free license is licensed for 30 days from the initial install or reload. During this time, you need to register with Citrix and request a fully functional 1-year license. To create this license, Citrix requires personal information that {{site.data.keyword.BluSoftlayer}} doesn't have access to. The license is an agreement between you and Citrix to use XenServer. 
{:shortdesc}

If you don't register your system within 30 days, you can't power on any virtual machines. 
{:tip}

To register your system, complete the following steps:

1. Download and install the XenCenter client. You can access the client from the [Citrix XenCenter ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://community.citrix.com/display/xs/XenCenter){: new_window} page.

2. Connect to your private network through VPN.

3. Start XenCenter and click **Add New Server**, or click **Server > Add...** from the drop-down menu.

4. Use the private IP address for the **Host name**, 'root' for **User name**, and your root password for **Password**.

5. After the system connects, a dialog box appears. This dialog box alerts you that your license expires in 30 days. If you don't see a dialog box, from the drop-down menu, go to **Tools > License Manager...**.

6. Check your server and click **Activate...**. A browser opens and goes to the Citrix registration site. Complete the form and submit it. Check your inbox for an email from Citrix. This email has your license key as an attachment.

7. After you receive the email, save the attached license key file locally.

8. In the XenCenter left pane, highlight your server. In the menu bar, go to **Server > Install license key...**. A dialog box appears that asks for the location of the license key. Point it to the saved location from Step 7.


