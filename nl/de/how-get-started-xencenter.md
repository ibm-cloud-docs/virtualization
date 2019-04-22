---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-24"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Einführung zu XenCenter
{: #getting-started-with-xencenter}

## Vorbereitende Schritte
{: #before-you-begin-xencenter}

Bevor Sie beginnen, sollten Sie die folgenden Voraussetzungen prüfen:

- Neue Bereiche portierbarer IP-Adressen (öffentlich und privat), die als 'Sekundär im VLAN' weitergeleitet werden. Diese Beispiellösung für die Konfiguration einer neuen VM setzt voraus, dass Sie über verwendbare IPs verfügen, die im privaten Netz verfügbar sind. Sie können Teilnetze im  [{{site.data.keyword.slportal_full}} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://control.softlayer.com/network/subnets/order) bestellen.
- Funktionalität zum Herstellen einer Verbindung zum privaten {{site.data.keyword.cloud}}-Netz über das VPN. Weitere Informationen zum VPN-Zugriff finden Sie im Abschnitt zum [Aktivieren des Zugriffs auf das private Netz der {{site.data.keyword.cloud_notm}}-Infrastruktur](/docs/customer-portal?topic=customer-portal-getting-started#enable-private-network).
- Citrix XenCenter ist auf dem lokalen System installiert. <!-- . https://downloads.service.softlayer.com/citrix/xen/-->

**Hinweis:** Ihr Server ist mit verschiedenen Vorlagen vorkonfiguriert, mit denen Sie sich schnell mit XenServer vertraut machen können.

## Virtuelle Maschine mit XenCenter erstellen
{: #creating-a-virtual-machine-with-xencenter}

Führen Sie die folgenden Schritte aus, um eine virtuelle Maschine mit XenCenter zu erstellen.

1. Vergewissern Sie sich, dass Sie über das VPN mit dem privaten {{site.data.keyword.cloud_notm}}-Netz verbunden sind. Stellen Sie über SSL VPN oder PPTP eine Verbindung her.
2. Öffnen Sie XenCenter und klicken Sie auf **XenServer hinzufügen**.
3. Geben Sie den Hostnamen Ihres Servers, Ihren Benutzernamen und Ihr Kennwort ein. Sie müssen die private IP-Adresse Ihres Servers (z. B 10.x.x.x), den Benutzernamen `root` und dann das Rootkennwort Ihres Servers verwenden. Diese Informationen stehen im {{site.data.keyword.slportal}} zur Verfügung. Rufen Sie Ihre [Einheiten ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://control.softlayer.com/devices){: new_window} auf und klicken Sie auf den XenServer-Namen. Klicken Sie auf **Verbinden**.
4. Wenn die Aktivierungsanzeige für eine freie Lizenz aufgerufen wird, geben Sie diese Informationen jetzt an.
5. Klicken Sie mit der rechten Maustaste auf den {{site.data.keyword.cloud_notm}}-Namen und wählen Sie **Neue VM...** aus.<!--You can now create your first Virtual Machine. Create a CentOS virtual machine with a disk of 10 GB and have both Public and Private Networks functioning-->
6. Wählen Sie das Betriebssystem aus, das Sie für die neue virtuelle Maschine verwenden möchten, und klicken Sie dann auf **Weiter**. **Hinweis:** Bestimmte Vorlagen erfordern die Bereitstellung eigener Datenträger.<!--Because you are using CentOS, you can use {{site.data.keyword.BluSoftlayer_notm}} private mirrors for CentOS to get our installation going.Select a version of CentOS and then click **Next**.-->
7. Geben Sie einen geeigneten Namen und eine Beschreibung für die neue virtuelle Maschine ein.
8. Geben Sie die Position der Installationsmedien für das Gastbetriebssystem ein und klicken Sie auf **Weiter**. <!-- In the example, {{site.data.keyword.BluSoftlayer_notm}} a CentOS mirror is used as installation media. Provide the Install URL of: https://mirrors.service.softlayer.com/centos/5/os/x86_64 and click **Next**.
  *A trailing ‘/’ at the end of the URL can sometimes break the installation.*
  *This mirror is available only on the {{site.data.keyword.BluSoftlayer_notm}} Private Network. The full mirror's contents are  available here: https://mirrors.service.softlayer.com/.-->
9. Wählen Sie die Anzahl der CPUs und die Menge des zuzuordnenden Hauptspeichers aus. (Sie erstellen ein schlankes System und benötigen deshalb wenig Arbeitsspeicher. 512 MB reichen völlig aus). Klicken Sie auf **Weiter**.
10. Wählen Sie Ihre virtuellen Platten aus, um Plattenspeicherplatz für Ihre VM
zuzuordnen.<!--Remember that this is like adding hard disks, it is not like partitioning your system. Partitioning is done during the installation of the OS.--> Die Standardgröße für diese Vorlage beträgt 8 GB. Dieser Betrag ist für den Server, den Sie erstellen, mehr als ausreichend. *Optional: Sie können die Platte entsprechend Ihren Anforderungen vergrößern, indem Sie die Platte hervorheben und auf **Bearbeiten...** klicken.* Klicken Sie auf **Weiter**.
11. Fügen Sie virtuelle Netzschnittstellen für die virtuelle Maschine hinzu oder entfernen Sie sie. Die Standardwerte können übernommen werden, es sei denn, Sie möchten nicht, dass Ihr System in der Lage ist, über das private Netz zu kommunizieren. Das Beispiel erfordert, dass Sie beide Schnittstellen im System belassen. Ein erfahrener Xen-Administrator kann jedoch eine von ihnen entfernen und für eigene Zwecke nutzen. In diesem Fall können Sie eine Schnittstelle markieren und löschen. Klicken Sie auf **Weiter**.
12. Die Konfiguration ist nun abgeschlossen. Behalten Sie die Auswahl von 'VM automatisch starten' bei und klicken Sie auf **Fertigstellen**. Die Installation beginnt. Die Hauptanzeige wird erneut aufgerufen. Die neue VM wird auf der linken Seite aufgelistet.
13. Wählen Sie die neue VM aus und klicken Sie auf die Registerkarte **Konsole**. <!--You can now see that your system is booted into the CentOS installer awaiting your input.-->
14. <!--All of the parameters of a CentOS installation are outside of the scope of this article and will need to be customized by your System Administrator, but this article will provide some specific pieces of information that you need to complete the installation. Select your language to get started. The CentOS installer will then ask you for assistance in configuring the Networking Devices in the system.--> Wählen Sie **eth0 - xen Virtual Ethernet** aus und klicken Sie auf **OK**.
  <!--![14](images/14.png)-->
15. <!--In the pre-requisite notes, we made sure that we already had a set of Portable IP Addresses routed as "Secondary on VLAN" ready for this installation.--> Halten Sie die Informationen zur Konfiguration von TCP/IP bereit. Sie müssen die Schnittstelle manuell mit IPv4-Unterstützung konfigurieren und die IPv6-Unterstützung inaktivieren.
  <!--[15](images/15.png)-->
16. Bestätigen Sie, dass Sie wissen, wie Ihr privates IP-Teilnetz verwendet werden muss (10.17.37.240/29), und klicken Sie auf **OK**<!-- to go to the CentOS installer-->. Sie und Ihr Systemadministrator können das Gastbetriebssystem entsprechend Ihren Richtlinien installieren. Weitere Informationen zu statischen und portierbaren IP-Blöcken finden Sie im Abschnitt mit der [Einführung zu Teilnetzen und IPs](/docs/infrastructure/subnets?topic=subnets-getting-started-subnets-ips#getting-started-subnets-ips).

Sie haben eine neue VM erstellt.
