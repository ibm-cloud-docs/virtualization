---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# XenServer-Tools unter Linux installieren
{: #installing-xenserver-tools-when-using-linux}

Führen Sie die folgenden Schritte aus, um die XenServer-Tools auf Ihren virtuellen Maschinen zu installieren, die CentOS-, Debian- und RedHat-Betriebssysteme verwenden.

Diese Schritte sind speziell für vom Kunden verwaltete XenServer-Systeme vorgesehen und nicht für von {{site.data.keyword.cloud}} bereitgestellte VSIs (Virtual Server Instances; Virtual Server-Instanzen).
{:tip}

1. Öffnen Sie XenCenter und stellen Sie eine Verbindung zu Ihrem XenServer-System her. Betrachten Sie das Beispiel einer neuen virtuellen Maschine, für die keine XenServer-Tools eingerichtet wurden. Wie Sie sehen, zeigt die Software für **Virtualisierungsstatus** den Status 'Tools nicht installiert' an.

2. Klicken Sie mit der rechten Maustaste auf den Namen der virtuellen Maschine, auf der die XenServer-Tools installiert werden sollen, und klicken Sie dann auf **XenServer-Tools installieren**. In einer daraufhin angezeigten Warnung werden Sie darüber informiert, dass jede momentan im CD-Laufwerk der virtuellen Maschine befindliche CD oder DVD ausgeworfen wird, wenn die XenServer-Tools installiert werden.

3. Klicken Sie auf **XenServer-Tools installieren**, um fortzufahren.

Auf der Registerkarte der XenCenter-Konsole für Ihre virtuelle Maschine wird das Bild einer CD für "xs-tools.iso" angezeigt, die in das DVD-Laufwerk geladen wird. Außerdem wird der Einheitenname auf dem System angezeigt.

4. Sie können die folgenden Schritte entweder über die Konsole oder über eine SSH-Sitzung mit dem System ausführen. Im vorliegenden Beispiel wird die Installation mit SSH ausgeführt. **Hinweis:** Der Einheitenname des Laufwerks wird von XenCenter bereitgestellt.

        $ ssh 10.17.37.243
        root@10.17.37.243's password:
        Last login: Thu Sep 11 12:57:31 2008 from 10.0.80.84
        [root@ns0 ~]# cd /mnt
        [root@ns0 mnt]# ls
        [root@ns0 mnt]# mkdir xs-tools
        [root@ns0 mnt]# mount /dev/xvdd /mnt/xs-tools/
        mount: block device /dev/xvdd is write-protected, mounting read-only
        [root@ns0 mnt]# cd /mnt/xs-tools/Linux/
        [root@ns0 Linux]# bash install.sh
        Detected `CentOS release 5.2 (Final)' (centos version 5).

        The following changes are made to this virtual machine:
        * packages to be installed or upgraded:
        - kernel-xen-2.6.18-53.1.13.el5.xs4.1.0.24.x86_64.rpm
        - xe-guest-utilities-4.1.0-257.x86_64.rpm

        Continue? [y/n] y

        Preparing...########################################### [100%]
        1:xe-guest-utilities##########################################[100%]
        Preparing...########################################### [100%]
        package kernel-xen-2.6.18-92.el5 (which is newer than kernel-xen-2.6.18-53.1.13.el5.xs4.1.0.24) is already installed

        Reboot this virtual machine.
        [root@ns0 Linux]#

Wenn Sie die Daten kopieren und in die Shell einfügen wollen, dann finden Sie in den folgenden Zeilen alle Schritte, wenn eine Zuordnung zu demselben Einheitennamen '/dev/xvdd' besteht.

        mkdir /mnt/xs-tools
        mount /dev/xvdd /mnt/xs-tools
        cd /mnt/xs-tools/Linux/
        bash install.sh

In bestimmten Fällen wird die ISO der Xen-Tools über '/dev/cdrom' angehängt.

        mkdir /mnt/xs-tools
        mount /dev/cdrom /mnt/xs-tools
        cd /mnt/xs-tools/Linux
        ./install.sh

5. Führen Sie für Ihr System einen Neustart über die Befehlszeile durch. Daraufhin wird angezeigt, dass die XenServer-Tools installiert werden.

6. Nun verfügt XenCenter über ein höheres Maß an Kontrolle über das Betriebssystem in der virtuellen Maschine und Sie können die zuvor nicht verfügbaren Zähler auf der Registerkarte **Leistung** von XenCenter anzeigen.
