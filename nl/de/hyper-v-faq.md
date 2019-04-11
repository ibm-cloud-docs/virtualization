---
copyright:
  years: 2014, 2018
lastupdated: "2018-08-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Häufig gestellte Fragen: Hyper-V
{: #faqs-hyper-v}

## Was ist Hyper-V?
{: #what-is-hyper-v-}

Hyper-V ist ein Virtualisierungssystem für Windows 2008 Server Datacenter Edition, das es einem einzigen physischen Server ermöglicht, mehrere virtuelle Maschinen zu hosten, die  alle unter einem eigenen Betriebssystem ausgeführt werden.

## Welche Voraussetzungen gelten für die Ausführung von Hyper-V?
{: #what-are-the-requirements-to-run-hyper-v-}

Hyper-V muss unter Windows 2008 Server Datacenter Edition (64-Bit) installiert werden. Die gesamte {{site.data.keyword.BluSoftlayer}}-Hardware muss die Systemanforderungen von Hyper-V erfüllen. Microsoft empfiehlt mindestens 2 GB Arbeitsspeicher (RAM) für den Server.

## Steht Hyper-V auch unter anderen 2008-Versionen zur Verfügung?
{: #is-hyper-v-available-on-any-other-versions-of-2008-}

{{site.data.keyword.BluSoftlayer_notm}} bietet Hyper-V nur für die Datacenter Edition an.

## Kann Hyper-V unter Windows 2003 ausgeführt werden?
{: #can-hyper-v-run-on-windows-2003}

Hyper-V wurde für Windows 2008 entwickelt und kann nicht unter Windows 2003 ausgeführt werden.

## Welche Betriebssysteme können auf einer virtuellen Maschine installiert werden?
{: #what-operating-systems-can-be-installed-on-a-virtual-machine}

{{site.data.keyword.BluSoftlayer_notm}} unterstützt die folgenden Betriebssysteme für Hyper-V:

* Alle Versionen von Windows 2003 und 2008 Server
* CentOS-, Fedora- und Ubuntu Linux-Distributionen

## Wie viele virtuelle Maschinen können von einem Server ausgeführt werden?
{: #how-many-virtual-machines-can-a-server-run-}

Die Anzahl der virtuellen Maschinen, die von einem Server ausgeführt werden können, variiert abhängig von den Serverprozessoren, vom Arbeitsspeicher und vom Festplattenspeicherplatz.

## Kann der Arbeitsspeicher auf jeder virtuellen Maschine angepasst werden?
{: #is-the-ram-customizable-on-each-virtual-machine-}

Ja. Sie können Hyper-V verwenden, um die Systemressourcen für jede virtuelle Maschine (einschließlich Speicher) anzupassen.

## Wie viel Arbeitsspeicher benötigt eine virtuelle Maschine?
{: #how-much-ram-does-a-virtual-machine-need-}

Der Arbeitsspeicherbedarf variiert abhängig von den Anforderungen der virtuellen Maschine. Überprüfen Sie die Systemvoraussetzungen für das Gastbetriebssystem. Die Menge des für eine virtuelle Maschine bereitgestellten Speicherplatzes kann jederzeit geändert werden.

## Kann eine virtuelle Maschine auf mehrere Prozessoren zugreifen?
{: #can-a-virtual-machine-access-to-more-than-one-processor-}

Sie können einer virtuellen Maschine Zugriff auf mehrere Prozessoren mit einer beliebigen virtuellen Maschine unter Windows erteilen. Für virtuelle Maschinen unter Linux besteht allerdings eine Beschränkung auf nur einen Prozessor.

## Können Festplattengrößen nach der Erstellung einer virtuellen Maschine geändert werden?
{: #can-hard-disk-sizes-change-after-a-virtual-machine-is-created-}

Ja. 

## Muss für jedes virtuelle Betriebssystem eine Lizenz vorhanden sein?
{: #does-each-virtual-operating-system-need-to-have-a-license-}

Virtuelle Maschinen, die mit Windows 2003 und 2008 arbeiten, werden über {{site.data.keyword.BluSoftlayer_notm}} lizenziert. Für virtuelle Maschinen unter Linux besteht eine freie Lizenz, sodass keine Aktion erforderlich ist.

## Haben die virtuellen Maschinen Zugriff auf das private Netz?
{: #will-the-virtual-machines-have-access-to-the-private-network-}

Ja. Virtuelle Maschinen können Verbindungen zu öffentlichen und privaten Netzen herstellen.

## Welche Vorteile bietet der Zugriff auf private Netze für virtuelle Maschinen?
{: #what-advantages-are-there-to-providing-private-network-access-to-virtual-machines-}

Der Zugriff virtueller Maschinen auf private Netze erlaubt es diesen virtuellen Maschinen, untereinander zu kommunizieren. Der Zugriff auf private Netze erlaubt virtuellen Maschinen außerdem die Kommunikation mit anderen internen Systemen wie beispielsweise NAS und iSCSI sowie mit allen anderen Servern, die mit {{site.data.keyword.BluSoftlayer_notm}} arbeiten.

## Können virtuelle Maschinen den sekundären IP-Block nutzen, der zum Lieferumfang des Servers gehört?
{: #can-virtual-machines-use-the-secondary-ip-block-that-came-with-the-server-}

Nein. Die sekundäre IP, die mit Ihrem Server bereitgestellt wurde, ist speziell für das Arbeiten auf dem physischen Server und nicht auf einer virtuellen Maschine vorgesehen. Sie benötigen portierbare IP-Blöcke, um Ihre virtuelle Maschine mit dem Netz zu verbinden.

## Was sind portierbare IP-Adressen?
{: #what-are-portable-ip-addresses-}

Informationen zu portierbaren IP-Adressen finden Sie im Abschnitt mit der [Einführung zu Teilnetzen und IPs](/docs/infrastructure/subnets?topic=subnets-getting-started-with-subnets-and-ips).

## Wie kann ich Hyper-V bestellen?
{: #how-do-i-order-hyper-v-}

Sie können Hyper-V auf einem bereits vorhandenen Server installieren, indem Sie einen Befehl zum erneuten Laden des Betriebssystems (OS Reload) absetzen und die Angabe für das Betriebssystem in 'Windows 2008 Server Datacenter Edition with Hyper-V' ändern. Zur Bereitstellung eines neuen Servers mit Hyper-V müssen Sie einen neuen Server bestellen und dann
'Windows 2008 Server Datacenter Edition with Hyper-V' als Betriebssystem auswählen.

## Wird Hyper-V mit Windows 2008 Server Datacenter Edition vorinstalliert?
{: #is-hyper-v-preinstalled-with-windows-2008-server-datacenter-edition-}

Hyper-V wird auf Windows 2008-Servern nicht vorinstalliert. Wenn Sie Hyper-V auf einem Windows 2008-Server installieren wollen, dann müssen Sie einen Befehl zum erneuten Laden des Betriebssystems (OS Reload) absetzen und als Betriebssystem 'Windows 2008 Server Datacenter Edition with Hyper-V' auswählen.

## Das erneute Laden des Betriebssystems (OS Reload) für Hyper-V ist abgeschlossen. Welcher Schritt muss als Nächstes ausgeführt werden?
{: #the-os-reload-for-hyper-v-is-done-what-s-next-}

Hyper-V muss konfiguriert werden. Weitere Informationen zum Konfigurieren von Hyper-V finden Sie im Abschnitt zum [Konfigurieren von Hyper-V](/docs/infrastructure/virtualization?topic=Virtualization-setting-up-hyper-v).

## Eine virtuelle Maschine wird nicht gestartet. Das System gibt einen Fehler aus, weil das ferne System der Maschine nicht initialisiert werden kann (Fehler: Nicht angegebener Fehler)? Wie kann ich diesen Fehler beheben?
{: #a-virtual-machine-does-not-start-cannot-initialize-machine-remoting-system-error-unspecified-error-how-do-i-resolve-this-issue-}

Zur Behebung dieses Problems müssen Sie den Hyper-V-Service stoppen und dann erneut starten. Dieses Problem wird normalerweise dadurch verursacht, dass Hyper-V während der Aktivierung des Servers gestartet wird. Wenn Sie den Service erneut starten, dann wird die virtuelle Maschine nach Abschluss der Aktivierung wieder gestartet.
