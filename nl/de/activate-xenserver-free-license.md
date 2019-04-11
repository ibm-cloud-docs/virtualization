---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-13"

subcollection: virtualization

keywords: Citrix XenServer 
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# XenServer aktivieren
{: #activating-xenserver}

Die kostenlose Citrix XenServer-Lizenz gilt für 30 Tage. Diese Frist beginnt mit der Erstinstallation oder dem erneuten Laden des Produkts. Während dieses Zeitraums müssen Sie sich bei Citrix registrieren und eine Lizenz mit vollem Funktionsumfang für die Dauer von einem Jahr anfordern. Zur Erstellung dieser Lizenz benötigt Citrix persönliche Informationen, auf die {{site.data.keyword.BluSoftlayer}} keinen Zugriff hat. Die Lizenz stellt eine Vereinbarung zwischen Ihnen und Citrix zur Nutzung von XenServer dar.
{:shortdesc}

Wenn Sie Ihr System nicht innerhalb von 30 Tagen registrieren, dann können Sie keine virtuellen Maschinen aktivieren.
{:tip}

Führen Sie die folgenden Schritte aus, um Ihr System zu registrieren:

1. Laden Sie den XenCenter-Client herunter und installieren Sie ihn. Sie können über die Seite von [Citrix XenCenter ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](http://community.citrix.com/display/xs/XenCenter){: new_window} auf den Client zugreifen.

2. Stellen Sie über das VPN (Virtual Private Network; virtuelles privates Netz) eine Verbindung zu Ihrem privaten Netz her.

3. Starten Sie XenCenter und klicken Sie auf die Option zum **Hinzufügen eines neuen Servers** oder klicken Sie im Dropdown-Menü auf
**Server > Hinzufügen...**.

4. Geben Sie die private IP-Adresse als **Hostname**, 'root' als **Benutzername** und Ihr Rootkennwort als **Kennwort** an.

5. Nachdem das System eine Verbindung hergestellt hat, wird ein Dialogfenster angezeigt. In diesem Dialogfenster werden Sie darauf aufmerksam gemacht, dass Ihre Lizenz in 30 Tagen abläuft. Wird kein Dialogfenster angezeigt, dann rufen Sie über das Dropdown-Menü die Optionen **Tools > Lizenzmanager...** auf.

6. Überprüfen Sie Ihren Server und klicken Sie auf **Aktivieren...**. Daraufhin wird ein Browser geöffnet, in dem die Citrix-Registrierungssite aufgerufen wird. Füllen Sie das Formular aus und senden Sie es ab. Überprüfen Sie Ihren Posteingang auf eine E-Mail von Citrix. Im Anhang dieser E-Mail erhalten Sie Ihren Lizenzschlüssel.

7. Nach Erhalt der E-Mail müssen Sie die angehängte Lizenzschlüsseldatei auf dem lokalen System speichern.

8. Heben Sie im linken Teilfenster von XenCenter Ihren Server hervor. Rufen Sie in der Menüleiste die Optionen für **Server > Lizenzschlüssel installieren...** auf. Daraufhin wird ein Dialogfenster angezeigt, in dem Sie aufgefordert werden, die Position des Lizenzschlüssels anzugeben. Geben Sie hier die in Schritt 7 festgelegte Speicherposition an.
