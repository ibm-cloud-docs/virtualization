---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization

keywords: ESXi, VMware
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Öffentlichen Zugriff auf VMware ESXi aktivieren
{: #enabling-public-access-to-vmware-esxi}

Standardmäßig wird der ESX-Host mit der Servicekonsole nur auf dem privaten Netz installiert. Auf diese Weise wird der öffentliche Datenverkehr mit dem öffentlichen Netz auf dem ESXi-Host verhindert.

Zu Beginn müssen Sie über einen VMware vSphere-Client in der privaten Schnittstelle des Servers eine Verbindung zu dem Server herstellen.

Sie benötigen die Informationen zum primären öffentlichen IP des Servers, um die Einrichtung abzuschließen. Informationen zum öffentlichen IP des Servers befinden sich im [{{site.data.keyword.slportal_full}} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://control.softlayer.com/){: new_window}.
{:tip}

Nach der Anmeldung beim ESXi-Host müssen Sie die folgenden Schritte ausführen, um die öffentliche Schnittstelle zu aktivieren:

1. Möglicherweise müssen Sie sich beim Server über SSH anmelden, indem Sie die private IP verwenden, um das Routing zu korrigieren.
* Führen Sie `esxcfg-route -l` aus, um die aktuellen Routen zu ermitteln.
* Führen Sie `esxcfg-route -a 10.0.0.0/8 [private Gateway-IP des Servers]` aus.
* Führen Sie `esxcfg-route [öffentliches Gateway des Servers]` aus, um sicherzustellen, dass das öffentliche Gateway der Standardeinstellung entspricht.

Melden Sie sich bei vSphere an und führen Sie die folgenden Schritte aus:

1. Klicken Sie auf die Registerkarte 'Konfiguration' und dann auf **Netzbetrieb**.
2. Klicken Sie für vSwitch1 auf **Eigenschaften > Hinzufügen**.
3. Wählen Sie **VM-Kernel** aus und klicken Sie auf **Weiter**.
* Die Einstellungen können unverändert übernommen werden. Sie können dem Netz zu Identifikationszwecken die neue Bezeichnung 'VMKernelPublic' zuordnen.
4. Geben Sie die Informationen zur öffentlichen IP des Servers ein.
5. Klicken Sie für das Standardgateway des VM-Kernels auf **Bearbeiten**, geben Sie die öffentliche Gateway-IP ein und klicken Sie dann auf **OK**.
* Klicken Sie auf **OK**. **Hinweis:** Eine Trennung der Verbindung ist normal und wird erwartet.*

Sie können nun über die öffentlichen und privaten IP-Adressen auf den Server zugreifen.

Die Aktivierung des öffentlichen IP-Zugriffs auf ESXi birgt inhärente Sicherheitsrisiken. Vergewissern Sie sich, dass die erforderlichen Schritte zur Beschränkung des Zugriffs auf ESXi ausschließlich auf die erforderlichen Benutzer beschränkt wird.
{:tip}
