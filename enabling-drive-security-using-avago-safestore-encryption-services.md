---
copyright:
  years: 2014, 2018
lastupdated: "2018-02-08"
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Enabling Drive Security Using Avago SafeStore Encryption Services

Setting up drive security is a great way to ensure that access to data that is stored in disks that are removed from the hosts cannot be 
made without the knowledge of the security key. The drive data cannot be recovered without this key and hence makes the data in it safe in 
the cloud environment.  {{site.data.keyword.BluSoftlayer_full}} provides Self Encrypting Drives (SED) at select data 
centers for the drives that can be bought on a Bare metal server. 10TB SATA drives are available in our US data centers.

## Prerequisites

* Bare metal Server with SED Drives – 10TB SATA
* LSI/AVAGO MegaRAID SAS 9361 -8i or Similar LSI/AVAGO RAID cards
* Mega RAID Storage Manager Software installed

## How to enable drive security using MegaRAID Storage Manager (MSM)

In this guide we discuss how to set the security key and safe guard data in it by using the MegaRAID Storage Manager. You can also use the 
WebBIOS interface which requires one at server Boot time to enter the MegaRAID card BIOS to configure the drive security setting. For more information about 
MegaRAID Controller Card SAS 9361-8i, see the Broadcom site, [MegaRAID SAS 9361-8i ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.broadcom.com/products/storage/raid-controllers/megaraid-sas-9361-8i#documentation).

### How to identify SED drives present?

MegaRAID Storage Manager should come preinstalled on most supported operating systems. If they are not present, you can manually install it from the
Broadcom site. For more information, see [MegaRAID SAS 9361-8i downloads](https://www.broadcom.com/products/storage/raid-controllers/megaraid-sas-9361-8i#downloads).
You can open MegaRAID Storage Manager by using the system credentials. In this guide a Windows machine was used and MSM was preinstalled. 
When you start MSM you must enter **Username** and **Password** which is the privileged user (Administrator) and password.

<!--![Figure 1](images/1_adapter_login.jpg)-->

Click on the **Physical** tab. Then click the drives that are available on the system. The **Properties** pane will have the 
**Drive Security Properties** mentioning **Full Disk Encryption capable** field which should show **Yes**. In the example used we had 2 non 
SED disks and 4 SED disks.

<!--![Figure 2](images/1_fde_capable_drives.jpg)-->

### Enabling drive security at the controller

1. To enable Drive security right click on the **Controller 0 :AVAGO MegaRAID SAS 9361-8i** from the **Physical** tab and select 
**Enable Drive Security**.
  * This should bring up a screen where you can enter the **Security key identifier** and the **Security key**. If there are multiple security keys used then a security key identifier can help you identify which security key to use. You must record the security key in a safe location. The security key is required when you reconfigure drives such as removing or reinserting a drive. Without the security key it is not possible to retrieve any data that is stored in a volume created out of the SEDs. It is not possible to retrieve a forgotten security key. A boot time password can also be set which holds the system pause for a password set here to be entered. The boot time password is optional and if it is set you must login into IPMI and type the boot password whenever the system is rebooted. Scroll down and check the box that says **I recorded the security settings for future reference** and click **Yes** to enable drive security.
  * When drive security is enabled, a yellow key image appears for **Controller 0 AVAGO MegaRAID SAS 9361-8i**. 
2. Now create a secure volume using these SEDs. You can right click on the **Controller0** from the **Logical** tab and select  
**Create Virtual Drive**.
3. Choose the **Advanced** option. The screen needs to specify the **RAID level** and the **Drive security method** as 
**Full Disk encryption (FDE)**. Select the FDE Drives that are required and click on **Add**. Click on **Create Drive Group** and 
click **Next**.
4. Review the virtual drive settings and make any necessary changes. The suggested setting for the **Read Policy** is **Always Read Ahead**. 
The suggested setting for the **Write policy** is **Write Back**.  Click **Create Virtual Drive**. Accept the Write Back policy impact due 
to BBU by clicking **Yes**. Click **Next** and a summary screen about  Virtual Drive is shown. Click **Finish**. You should see 
“The virtual drives successfully created”.
5. To confirm the virtual disk is secured, click the **Logical** tab and the virtual drive that was created. You should see in the 
**Drive Security Properties** the **Secured** field is marked **Yes**.

<!--![Figure 3](images/2_enable_drive_security.jpg)-->
<!--![Figure 4](images/3_security_key_details_page.jpg)-->
<!--![Figure 5](images/4_security_key_set_0.jpg)-->
<!--![Figure 6](images/9_create_vd_with_fde_drives.jpg)-->
<!--![Figure 7](images/10_create_vd_advanced_select_raid_drive_encryption_0.jpg)-->
<!--![Figure 8](images/create_vd_settings.jpg)-->
<!--![Figure 9](images/6_vd_secured_confirmation_0.jpg)-->

If the server came with RAID volumes already created using SED drives, you can make the volume secure by following these steps. On 
the **Logical** tab, right-click **Drive Group** and elect **Secure Using FDE**. If you have mixed FDE and Non FDE 
drives for a volume then this option is not visible.

<!--![Figure 10](images/5_secure_existing_vd_with_fde_drives_0.jpg)-->

To remove drive security, you must first delete secured virtual disks and right-click on Controller 0 to **Disable Drive Security**. This 
securely erases the data in it and removes drive security.

In this guide we looked at enabling drive security using MSM software. You can also set up drive security by using webBIOS and logging in 
through the IPMI at the boot time and entering the RAID BIOS. For more information see **Avago SafeStore Encryption Services** in the 
**12Gb/s MegaRAID SAS Software User Guide**.
