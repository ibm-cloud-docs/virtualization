---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-11"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Enabling public access to VMware

By default, the ESX Host is installed with the service console on only the private network, which means public traffic to and from the public network is prevented on the ESX Host. 

To get started, you need to connect to the server by using a VMware vSphere Client on the server's private interface by following these steps.

1. Click **ESX Host > Configuration Tab > Networking > Add Networking**
2. Select Service Console.
3. Select a vSwitch to use. In this case, it is the external/public interfaces, so select **vSwitch1** (vmnic1 and vmnic3).
4. Rename the Service Console to something that is easily recognizable (for example, "Public Service Console").
5. Input the primary external IP address that is assigned to the server with the correct subnet mask.
6. Click **Edit** and add the default gateway that is assigned to the server.


