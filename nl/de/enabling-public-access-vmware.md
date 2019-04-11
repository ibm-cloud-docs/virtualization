---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-11"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Öffentlichen Zugriff auf VMware aktivieren
{: #enabling-public-access-to-vmware}

Standardmäßig wird der ESX-Host mit der Servicekonsole nur auf dem privaten Netz installiert. Dies bedeutet, dass der öffentliche Datenverkehr mit dem öffentlichen Netz auf dem ESX-Host verhindert wird.

Zu Beginn müssen Sie über einen VMware vSphere-Client in der privaten Schnittstelle des Servers eine Verbindung zu dem Server herstellen. Führen Sie dazu die folgenden Schritte aus.

1. Klicken Sie auf **ESX-Host > Konfigurationsregisterkarte > Netzbetrieb > Netzbetrieb hinzufügen**.
2. Wählen Sie die Servicekonsole aus.
3. Wählen Sie den zu verwendenden vSwitch aus. In diesem Fall handelt es sich um die externen/öffentlichen Schnittstellen. Wählen Sie also **vSwitch1** ('vmnic1' und 'vmnic3') aus.
4. Benennen Sie die Servicekonsole um und ordnen Sie ihr einen einfach nachvollziehbaren Namen zu (z. B. 'Public Service Console').
5. Geben Sie die primäre externe IP-Adresse ein, die dem Server mit der richtigen Teilnetzmaske zugeordnet ist.
6. Klicken Sie auf **Bearbeiten** und fügen Sie das Standardgateway hinzu, das dem Server zugeordnet ist.
