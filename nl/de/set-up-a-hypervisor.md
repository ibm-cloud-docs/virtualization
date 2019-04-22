---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Hypervisor einrichten
{: #setting-up-a-hypervisor}

Führen Sie die folgenden Schritte aus, um einen Hypervisor einzurichten.

1. Melden Sie sich beim [{{site.data.keyword.slportal_full}} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://control.softlayer.com/){: new_window} an und verwenden Sie dazu Ihre eindeutigen Berechtigungsnachweise.
2. Wählen Sie im Menü **Geräte** die Option **Geräteliste** aus und suchen Sie den gewünschten Hypervisor.
3. Stellen Sie über das [sichere VPN ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://www.softlayer.com/vpn-access){: new_window} eine Verbindung zum privaten Netz her, um auf den Hypervisor zuzugreifen.

**Hinweis:** Die {{site.data.keyword.cloud}}-Hypervisor-Provider umfassen XenServer, VMware und Hyper-V. Jeder Provider verfügt über individuelle Managementkonsolen, auf die in unterschiedlicher Weise zugegriffen wird. Weitere Informationen zum Zugreifen auf eine Managementkonsole und zum Arbeiten mit einer Managementkonsole finden Sie über die folgenden Links:

   * [XenServer ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://support.citrix.com/en/products/xenserver){: new_window}
   * [VMware ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://www.vmware.com/support/vsphere-hypervisor.html){: new_window}
   * [Hyper-V ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://technet.microsoft.com/en-us/windowsserver/dd448604){: new_window}

4. Fordern Sie portierbare IPs für Ihre virtuellen Maschinen an.
    * VMs benötigen portierbare IP-Adressen. Blöcke von öffentlichen und privaten portierbaren IPs können über das {{site.data.keyword.slportal}} bestellt werden.
    * Weitere Informationen zur Zuordnung von IP-Adressen finden Sie im Abschnitt mit der [Einführung zu Teilnetzen und IPs](/docs/infrastructure/subnets?topic=subnets-getting-started-subnets-ips#getting-started-subnets-ips).

5. Richten Sie das Routing für VMs im privaten Netz ein. VMs benötigen die folgenden Spezifikationen, um das Routing zu anderen VMs über das private Netz durchzuführen:
    * Portierbare private IPs
    * Statische Route passend zum Netzbereich 10.0.0.0/8

Weitere Informationen zum VM-Routing-Prozess finden Sie im Abschnitt zum [Einrichten eines VM-Netzes](/docs/infrastructure/virtualization?topic=Virtualization-setting-up-a-virtual-machine-network). Maschinen, die sich in demselben VLAN befinden, können kommunizieren, nachdem Sie das VM-Netz eingerichtet haben. [Aktivieren Sie VLAN Spanning](/docs/infrastructure/vlans?topic=vlans-vlan-spanning), wenn Maschinen kommunizieren müssen, die sich in unterschiedlichen VLANs befinden.

## Zugriff auf ISOs durchführen und ISOs sicher speichern
{: #access-and-securely-store-isos}

Auf VMs im {{site.data.keyword.cloud_notm}}-Netz können vorkonfigurierte oder angepasste ISOs ausgeführt werden. VMs im {{site.data.keyword.cloud_notm}}-Netz können auf die interne Spiegelungssite zugreifen, die ausschließlich {{site.data.keyword.cloud_notm}}-Benutzern zur Verfügung steht und die gängige Konfigurationen der am häufigsten verwendeten Betriebssysteme bereitstellt. Wenn Sie eine spezielle Version oder Konfiguration für ein bestimmtes Betriebssystem benötigen, lesen Sie die Informationen auf der Website des Betriebssystemherstellers.

Wenn Sie eine angepasste ISO auf Ihrer VM ausführen, laden Sie Ihre ISO an eine sichere Position hoch, damit sie bei Ausfall eines Geräts abgerufen werden kann. Viele Benutzer entscheiden sich dafür, angepasste ISOs auf dem lokalen System eines Geräts zu speichern, indem Sie SSH oder RDP verwenden. Alternativ wird Speicherplatz durch Speicherservices angeboten, die über verschiedene Funktionen verfügen.
