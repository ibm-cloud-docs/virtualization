---
copyright:
  years: 2014, 2019
lastupdated: "2019-01-15"

subcollection: virtualization

keywords: vmware, ESXi
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:table: .aria-labeledby="caption"}

# Erweiterte Single-Site-VMware-Referenzarchitektur
{: #advanced-single-site-vmware-reference-architecture}

Verwenden Sie die folgenden Schritte, um eine erweiterte Single-Site-vSphere-Umgebung zu erstellen. In den folgenden Schritten wird anhand der bewährten VMware-Verfahren (Best Practices) eine {{site.data.keyword.cloud}}-Referenzarchitekturbereitstellung definiert.

Die Referenzarchitektur dient zur Bereitstellung einer Umgebung, in der gemeinsam genutzter Speicher, VMware High Availability (HA) und vSphere Distributed Resource Scheduler (DRS) sowie ein {{site.data.keyword.cloud_notm}}-Gateway oder eine entsprechende Firewall eingesetzt werden. Die erweiterte Single-Site-VMware-Referenzarchitektur eignet sich für die Mehrzahl der Produktionsbereitstellungen, die abhängig von den Workloadanforderungen skaliert werden können und die eine lokale Implementierung ersetzen oder sie zu einem IT-Hybridszenario erweitern können.

## Überblick zur Umgebung
{: #environment-overview}

Die erweiterte VMware-Umgebung, die entworfen wird, besteht aus einem Rechenzentrum, das zwei separate Cluster (Management-Cluster und Kapazitätscluster) verwaltet. Die Konfiguration kann abhängig von den Anforderungen mithilfe eines einzelnen, aus vier Knoten bestehenden Clusters eingerichtet werden. Der Management-Cluster umfasst die folgenden virtuellen Maschinen (VMs), die zum Verwalten der Infrastruktur verwendet werden:

* VMware vCenter Server 5.5 oder 6.0 Appliance
* Microsoft Windows 2012 R2 Standard mit den Rollen für Active Directory und Domain Name System (DNS)

Der Kapazitätscluster enthält die Ressourcen und die Infrastruktur, die zum Erstellen und Ausführen von VMs benötigt werden. Für den Netzbetrieb besteht die Umgebung aus drei privaten, internen VLANs und einem einem öffentlichen VLAN, das für die externe Kommunikation verwendet wird. In Tabelle 1 sind die VLAN-Typen und VLAN-Namen angegeben, die in der Umgebung verwendet werden.

|VLAN-Typ|VLAN-Name|Beschreibung|                                                                             |
|---|---|---|
|Primär privat| Management| Zugeordnet für Verwaltung und Zugriff auf physische ESXi-Hosts und virtuelle Server.|
|Primär privat|Speicherung| Zugeordnet für Verwaltung und Zugriff auf gemeinsam genutzten Speicher, der jedem ESXi-Host zugeordnet ist.|
|Primär privat| VM-Zugriff | Zugeordnet für virtuelle Maschinen, die auf den ESXi-Hosts ausgeführt werden.|
|Primär öffentlich | Öffentlich | Zugeordnet für virtuelle Maschinen oder andere Einheiten, die Zugriff über das öffentliche Netz benötigen.|
<caption>Tabelle 1. Primäre VLANs</caption>

Als gemeinsam genutzten Speicher können Sie OS Nexus QuantaStor, einen gemeinsam genutzten Single-Tenant-Speicherserver oder die {{site.data.keyword.cloud_notm}} Endurance or Performance-Speicherservices verwenden. In allen diesen Fällen wird die gemeinsam genutzte Speichereinheit verwendet, um die VMs sowohl auf den Management- als auch auf den Kapazitätsclustern zu speichern. Weitere Informationen zu den verfügbaren Speicheroptionen finden Sie in den [{{site.data.keyword.cloud_notm}} Storage-Lösungen ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://www.ibm.com/cloud/storage){: new_window}.

Die Speicherumgebung ist so konfiguriert, dass NFS-Datenträger unterstützt werden.

## Schritt 1: Primäre öffentliche und private VLANs bestellen
{: #step-1-ordering-primary-public-and-private-vlans}

**Hinweis:** VLAN-Bestellungen unterliegen der Prüfung und Genehmigung. Es sind keine speziellen Bedingungen oder Voraussetzungen definiert, die eine VLAN-Bestellung für die automatische Genehmigung markieren. VLAN-Bestellungen können aus verschiedenen Gründen abgelehnt werden.

In diesem Schritt werden vier VLANs erstellt: eines für das Management, eines für die Speicherung, eines für VMs und eines für den öffentlichen Zugriff. Sie sollten diese vier VLANs erstellen, bevor Sie die Server bestellen. Durch das Bestellen der VLANs vor den anderen Komponenten wird sichergestellt, dass die Server in den richtigen VLANs und im korrekten Rechenzentrum platziert werden.

Die Umgebung besteht aus fünf ESXi-Hosts (zwei für den Management-Cluster und drei für den Kapazitätscluster) und einem virtuellen Server. Das private Management-VLAN besteht aus einem Teilnetz mit 16 IP-Adressen, die zur Unterstützung dienen. Das primäre private VLAN für die Speicherung und den VM-Datenverkehr besteht aus acht Adressen, da die Umgebung einen einzelnen Speicherserver enthält und die VMs ein portierbares Teilnetz verwenden. Wird ein größerer Teilnetzbereich für das Managementnetz benötigt, dann müssen Sie den Bereich anpassen, indem Sie die Anzahl der ESXi-Hosts berechnen und diesen Wert mit 2 multiplizieren. Außerdem müssen Sie sicherstellen, dass das Rechenzentrum angegeben wird, für das diese VLANs erstellt werden.

Nachdem Sie sich bei {{site.data.keyword.cloud_notm}} angemeldet haben, müssen Sie ein Support-Ticket für das Management- und das VM-VLAN öffnen, indem Sie
**Support > Ticket hinzufügen** auswählen. Füllen Sie die Felder mit den Daten aus, die in **Tabelle 2** aufgelistet sind.

|Feld|Wert|
|---|---|
|Betreff|Frage für privates Netz|
|Titel|VLANs bestellen|
|Details|Stellen Sie drei primäre private VLANs und ein primäres öffentliches VLAN bereit. Ordnen Sie den VLANs das folgende Adressierungsschema zu: <br/><ul><li>Ordnen Sie 1x18 (16 Adressen) für das primäre private VLAN zu.</li><li>Ordnen Sie 2x29 (acht Adressen) für das primäre private VLAN zu.</li><li>Ordnen Sie 1x29 (acht Adressen) für das primäre öffentliche VLAN zu.</li></ul>|
|Weshalb benötige ich diese zusätzlichen VLANs?|Zum Platzieren von Hosts, Speichereinheiten und VMs in einem anderen Netz einer VMware-Umgebung.
|Benötige ich öffentliche und/oder private VLANs?|Private und öffentliche|
|Wie viele VLANs benötige ich?|Private = 3, Öffentliche = 1|
|Wie viele IP-Adressen benötige ich?|8 - 16 für jedes VLAN|
|Welche Router müssen für die VLANs vorhanden sein?|Es spielt keine Rolle, dass die VLANs sich in demselben Pod befinden.|
|In welchem Pod müssen die VLANs enthalten sein?|Sie werden alle in demselben Pod in <DATA CENTER NAME> definiert.|
<caption>Tabelle 2. Informationen zum Support-Ticket</caption>

Nach der Bereitstellung der VLANs sollten Sie die VLAN-Nummern, den Teilnetzbereich und das Gateway notieren und eine Zuordnung zu logischen vSphere-Netzen herstellen. Sie können das Arbeitsblatt in Anhang A (VLAN-Arbeitsblatt zum Aufzeichnen des VLAN und der zugehörigen Informationen) verwenden. Beispiel:

|VLAN-Typ|VLAN-Nummer|IP-Bereich|Gateway|Zweck|
|---|---|---|---|---|
|Primär privat|1101|10.X.Y.Z/28|10.X.Y.1|Management|
|Primär privat|1102|10.A.B.C/29|10.A.B.1|Speicherung|
|Primär privat|1103|10.Q.R.S/29|10.Q.R.1|Virtuelle Maschinen|
|Primär öffentlich |2101|75.S.T.U/29|75.S.T.1|Öffentlicher Zugriff|
<caption>Tabelle 3. Beispiel für primäres VLAN</caption>

**Hinweis:** Fahren Sie erst dann mit dem nächsten Schritt fort, wenn die VLANs bereitgestellt wurden.

## Schritt 2 - Portierbare private IP-Adressen bestellen
{: #step-2-ordering-portable-private-ip-addresses}

In Schritt 2 wird eine Anforderung zum Erstellen portierbarer privater Teilnetze für Management-VMs, den Zugriff auf VM-Kernelspeicher und VMs im Kapazitätscluster abgesetzt. Sie müssen ermitteln, wie viele Adressen für jedes VLAN-Teilnetz benötigt werden. In der repräsentativen Umgebung müssen Sie die folgenden Adressen bestellen:

* Management-VLAN - 1x8 Adressen /29 – Eine Adresse für vCenter Appliance; eine Adresse für DNS und Active Directory.
* Speicher-VLAN - 1x16 Adressen /28 – Erstellen Sie zwei Teilnetze in demselben VLAN für die Speicherung und zwei VM-Kernel-Ports auf jedem ESXi-Host, indem Sie unterschiedliche Teilnetze verwenden, um auf die gemeinsam genutzten Speichereinheiten zuzugreifen.
* VM-VLAN - 1x32 Adressen /27 – Diese Adressen werden verwendet, um IP-Adressen bestimmten VMs im Kapazitätscluster zuzuordnen.

Wenn Sie eine bestimmte Menge bestellen, dann müssen Sie berücksichtigen, wie viele IP-Adressen innerhalb der nächsten 30 Tage und 6 Monate benötigt werden. Außerdem muss berücksichtigt werden, dass {{site.data.keyword.cloud_notm}} drei bis vier IP-Adressen für jeden portierbaren Teilnetzblock reserviert. Demzufolge erhalten Sie bei Bestellung von vier IP-Adressnetzen eine IP-Adresse. Unterstützt der Pod das Hot Standby Router Protocol, dann sind es null IP-Adressen.

Führen Sie die folgenden Schritte aus, um einen Block mit portierbaren IP-Adressen für jedes VLAN und jedes Teilnetz zu bestellen, das erstellt werden soll:

1. Öffnen Sie ein Browserfenster und melden Sie sich bei {{site.data.keyword.cloud_notm}} an.
2. Wählen Sie **Konto > Bestellen** aus.
3. Rufen Sie im Popup-Fenster **Netz > Teilnetze / IPs > Bestellen** auf.
4. Wählen Sie im Dropdown-Menü **Portierbar Privat** aus.
5. Wählen Sie **XX portierbare private IP-Adressen** aus und klicken Sie auf **Weiter**. **Hinweis:** _XX_ gibt die Anzahl der IP-Adressen an. 
6. Wählen Sie das VLAN aus, das dem IP-Adressblock zugeordnet werden soll, und klicken Sie auf **Weiter**.
7. Geben Sie die Informationen in der Anzeige ein und klicken Sie auf **Weiter**.

Die Erstellung von IP-Adressen ist nicht zeitaufwändig und wird aufgerufen, indem Sie unter **Netz > IP-Management** die Option **Teilnetze** auswählen. Sie können diese IP-Adressen in dem Arbeitsblatt aufzeichnen, das in Anhang A (VLAN-Arbeitsblatt) aufgeführt ist.

## Schritt 3 Virtuellen Server bestellen
{: #step-3-ordering-a-virtual-server}

Ein virtueller Server für Windows 2012 R2 Standard wird in diesem Schritt bereitgestellt, um als Utility Server für ISOs zu dienen und den Zugriff auf die Umgebung zu ermöglichen.

1. Öffnen Sie ein Browserfenster und melden Sie sich bei {{site.data.keyword.cloud_notm}} an.
2. Wählen Sie **Konto > Bestellen** aus.
3. Rufen Sie **Virtueller Server > Stündlich oder monatlich** auf.
4. Wählen Sie das Rechenzentrum aus, in dem die VLANs erstellt wurden, um den virtuellen Server bereitzustellen, und geben Sie die folgenden Spezifikationen für jede Option an:
  * Datenverarbeitungsinstanz - Kerne mit 1x2,0 GHz
  * Arbeitsspeicher: 4 GB
  * Betriebssystem: Windows Server 2012 R2 Standard Edition (64-Bit)
  * Erste Platteneinheit: 100 GB (SAN)
  * Uplink-Port-Geschwindigkeiten - 1 Gbps für Uplinks zu öffentlichen und privaten Netzen
5. Klicken Sie auf **Weiter mit Bestellung** und wählen Sie in der Anzeige **Bestellübersicht und Abrechnung** die Back-End- und Front-End-VLANs aus. **Hinweis:** Die Auswahl von VLANs ist wichtig, damit das Dienstprogramm im korrekten Pod innerhalb des Rechenzentrums platziert werden kann. Für die Beispielumgebung handelt es sich beim Back-End-VLAN um das Management-VLAN (1101) und beim Front-End-VLAN um das öffentliche VLAN (2101).
6. Geben Sie einen Host- und einen Domänennamen für den Server ein und klicken Sie dann auf **Bestellen**.

## Schritt 4 - ESXi-Hosts und Gateway / Firewall bestellen
{: #step-4-ordering-esxi-hosts-and-gateway-firewall}

Sie müssen die ESXi-Hosts und das Brocade vRouter-Gateway (Vyatta) sowie die entsprechende Firewall-Appliance bestellen, während der virtuelle Server bereitgestellt wird. Bestellen Sie alle diese Server zur selben Zeit, sodass sie gleichzeitig in denselben Pod gestellt werden. Wenn Sie Hardware zu unterschiedlichen Zeitpunkten bestellen, kann dies dazu führen, dass Hosts und Firewalls in einem {{site.data.keyword.cloud_notm}}-Rechenzentrum in separaten Pods gespeichert werden. 

### ESXi-Hosts
{: #esxi-hosts}

Für jeden ESXi-Host, der für die Umgebung bestellt wird, wird als Betriebssystem VMware ESXi 5.5 verwendet. Wenn Sie {{site.data.keyword.cloud_notm}}-vSphere-Lizenzen verwenden möchten, dann werden monatliche Gebühren erhoben, die sich nach der Nutzung richten. 

Eine weitere Möglichkeit ist die Installation von ESXi unter Verwendung einer eigenen ISO-Komponente. Anweisungen zu diesem Prozess finden Sie im Abschnitt zum [Installieren von VMware vSphere ESXi über Remote Console and
Virtual Media](/docs/infrastructure/vmware?topic=VMware-installing-vsphere-esxi#installing-vsphere-esxi). Wenn Sie ESXi mithilfe einer eigenen ISO-Komponente installieren wollen, dann müssen Sie für das Betriebssystem der Management-Hosts und Kapazitätshosts während des Bestellprozesses die Option Ohne Betriebssystem auswählen.

**Hinweis:** Für diese Implementierung ist die Enterprise Plus-Lizenzierung erforderlich, um vSphere Distributed Virtual Switches zu verwenden. Wenn Ihre Lizenz für Enterprise Plus nicht gültig ist, müssen Sie die von {{site.data.keyword.cloud_notm}} bereitgestellte VSPP-Lizenz (VSPP = VMware Service Provider Program) verwenden.

### Management-Hosts bestellen
{: #ordering-management-hosts}

Führen Sie die folgenden Schritte aus, um die Management-Host-Server zu bestellen.

1. Öffnen Sie ein Browserfenster und melden Sie sich bei {{site.data.keyword.cloud_notm}} an.
2. Wählen Sie **Konto > Bestellen** aus.
3. Wählen Sie **Bare Metal Server > Monatlich** aus.
4. Wählen Sie in der Anzeige mit der Serverliste einen geeigneten Server aus, der die Anforderungen für den Management-Cluster erfüllt. **Hinweis:** Für
ESXi 5.5 ist mindestens ein Dual-Core-Prozessor mit 4 GB Arbeitsspeicher (RAM) und ein 1-Gb-Ethernet-Controller erforderlich. 
5. Wählen Sie das Rechenzentrum aus, in dem die VLANs erstellt wurden, um die ESXi-Server bereitzustellen, und geben Sie die folgenden Spezifikationen für jede Option an:
  * Menge: 2
  * Arbeitsspeicher: 32 GB
  * Betriebssystem: VMware ESXi 5.5 ('Ohne Betriebssystem', wenn Sie die Informationen im Abschnitt zum [Installieren von VMware vSphere ESXi über Remote Console and
Virtual Media](/docs/infrastructure/vmware?topic=VMware-installing-vsphere-esxi#installing-vsphere-esxi) verwenden)
  * Festplatten: Festplatten 1 & 2: 500 GB SATA in RAID 1
  * Öffentliche Bandbreite: Nur privates Netz -> 0 GB Bandbreite
  * Uplink-Port-Geschwindigkeiten: 10 Gbps Redundant Private Network Uplinks
6. Klicken Sie auf **Weiter mit Bestellung**.
7. Klicken Sie auf **Server hinzufügen**, um mit dem Hinzufügen von ESXi-Hosts für den Kapazitätscluster zur Bestellung zu beginnen.

### Kapazitätshosts bestellen
{: #ordering-capacity-hosts}

1. Wählen Sie in der Anzeige mit der Serverliste einen geeigneten Server aus, der die Anforderungen für die Hosts der Kapazitätscluster erfüllt. **Hinweis:** Für ESXi 5.5 ist mindestens ein Dual-Core-Prozessor mit 4 GB Arbeitsspeicher (RAM) und ein 1-GB-Ethernet-Controller erforderlich.
2. Wählen Sie das Rechenzentrum aus, in dem die VLANs erstellt wurden, um die ESXi-Server bereitzustellen, und geben Sie die folgenden Spezifikationen für jede Option an:
  * Menge: 3
  * Arbeitsspeicher: 128 GB
  * Betriebssystem: VMware ESXi 5.5 ('Ohne Betriebssystem', wenn Sie die Informationen im Abschnitt zum [Installieren von VMware vSphere ESXi über Remote Console and
Virtual Media](/docs/infrastructure/vmware?topic=VMware-installing-vsphere-esxi#installing-vsphere-esxi) verwenden)
  * Festplatte: Festplatten 1 & 2: 500 GB SATA in RAID 1
  * Öffentliche Bandbreite: Nur privates Netz > 0 GB Bandbreite
  * Uplink-Port-Geschwindigkeiten: 10 Gbps Redundant Private Network Uplinks
3. Klicken Sie auf **Weiter mit Bestellung**.

### Konfiguration ausführen
{: #completing-configuration}

In Ihrem Warenkorb befinden sich nun ESXi-Hosts. Damit die Einheiten korrekt bereitgestellt werden können, müssen Sie ihnen das öffentliche VLAN (sofern maßgeblich), das private VLAN, den Hostnamen und die Domäne hinzufügen.

1. Ordnen Sie die folgenden VLANs zu und erstellen Sie die Hostnamen für die Einheiten:

* ESXi-Hosts – Back-End-VLAN: (1101)
* Back-End-VLAN: (1101)
* Front-End-VLAN: (2101)

2. Wählen Sie Ihre Zahlungsmethode aus, stimmen Sie den Bedingungen zu und klicken Sie auf **Bestellung abschließen**. **Wichtig:** Fahren Sie erst mit Schritt 5 fort, nachdem die Server bereitgestellt wurden und wenn auf sie über das VPN oder den virtuellen Server zugegriffen werden kann, der im vorherigen Schritt bestellt wurde.

## Schritt 5 - Trunking von VLANs auf BCS-Switches
{: #step-5-trunking-vlans-on-bcs-switches}

Standardmäßig werden Ports im Zugriffsmodus auf den Back-End-Switches des Kunden (d. h. auf einem Switch des privaten Netzes in einem Pod wie beispielsweise einem BCS (Backend Customer Switch)) platziert. Demzufolge müssen Sie die Ports, die den ESXi-Hosts zugeordnet sind, zusammenfassen, damit die Hosts auf den Speicher zugreifen und die VMs im privaten Netz kommunizieren können.

Bevor Sie das Ticket zum Zusammenfassen der VLANs öffnen, müssen Sie ermitteln, welche Netzschnittstellen sich auf dem privaten Netz befinden. Zur Ermittlung der Schnittstelle müssen Sie die **Gerätedetails** für jeden ESXi-Server aufrufen und dann **Netz > Privat** auswählen. In der vorliegenden Umgebung werden die Adapter für das private Netz `eth0` und `eth2` verwendet.

Zusätzlich zum Trunking der VLANs für die ESXi-Hosts müssen Sie auch die Bindung der NICs für die Management-Hosts und Kapazitätshosts auflösen. Die Bindung der NICs muss aufgelöst werden, weil das Link Aggregation Control Protocol (LACP) nicht mit dem iSCSI-Multipathing der Software kompatibel ist.

Öffnen Sie ein Ticket, um die VLANs zusammenzufassen und die Bindung der NICs aufzulösen:

1. Öffnen Sie ein Browserfenster und melden Sie sich bei {{site.data.keyword.cloud_notm}} an.
2. Wählen Sie **Support, Ticket hinzufügen** aus.
3. Geben Sie die folgenden Informationen ein:
  * Betreff: Frage für privates Netz
  * Titel: VLANs zusammenfassen und Bindung für NICs auflösen
  * Details: VLANs `<Management VLAN>`, `<Storage VLAN>` und `<VM VLAN>` im NIC-Paar 'eth0' und 'eth2' für die folgenden Hosts zusammenfassen [Auflisten aller ESXi-Hosts]. Außerdem die Bindung der privaten NICS ('eth0' und 'eth2') auf den folgenden Servern auflösen (Entfernen von LACP): [Auflisten aller ESXi-Hosts]
4. Klicken Sie auf **Ticket hinzufügen**.

Ändern Sie unbedingt die VLANs, die in <> angegeben sind, in die aktuellen VLANs.

## Schritt 6 - Netzbetrieb für Management-Host konfigurieren
{: #step-6-configuring-management-host-networking}

Nach der Bereitstellung der Server und der Zusammenfassung der VLANs müssen Sie den Netzbetrieb auf den Hosts im Management-Cluster einrichten. Für diese Konfiguration verwenden Sie vSphere-Standardswitches für den Management-Cluster. Mit Ausnahme der vMotion-Portgruppen sowie der Fehlertoleranzportgruppen verwenden Sie portierbare IP-Adressen, um die Portgruppen des VM-Kernels zu konfigurieren. **Hinweis:** Sie verwenden ein eigenes IP-Schema für vMotion und die Fehlertoleranz, weil der Datenverkehr nicht weitergeleitet werden muss. Allerdings müssen sich alle Hosts in demselben Teilnetz wie die anderen Hosts im Cluster befinden, damit die vMotion- und Fehlertoleranzfunktionen verwendet werden können. Wenn das Teilnetz weitergeleitet werden muss, dann sollten Sie portierbare {{site.data.keyword.cloud_notm}}-IP-Adressen verwenden.

Zur Konfiguration der Portgruppen muss der vSphere-Client auf dem virtuellen Utility Server oder auf der Workstation installiert werden, der bzw. die über den {{site.data.keyword.cloud_notm}}-Management-VPN auf die Hosts zugreift.

1. Öffnen Sie ein Browserfenster und melden Sie sich bei {{site.data.keyword.cloud_notm}} an.
2. Nachdem die Verbindung zum Utility Server oder zum {{site.data.keyword.cloud_notm}}-VPN hergestellt ist, müssen Sie den vSphere-Client starten.
3. Geben Sie die IP-Adresse, den Benutzernamen und das Kennwort eines der Management-ESXi-Hosts an. (Das Rootkennwort für den ESXi-Host können Sie ermitteln, indem Sie **Gerätedetails > Kennwörter** auswählen.
4. Rufen Sie **Konfigurationen > Netzbetrieb** auf und erstellen oder ändern Sie die folgenden Portgruppen auf dem vSphere-Standardswitch (meist vSwitch0).

Führen Sie die folgenden Schritte für jeden Host im Management-Cluster aus.

### vSwitch0-Eigenschaften
{: #vswitch0-properties}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabelle 4. Eigenschaften von vSwitch0</caption>
<tbody>
<tr><th>Netzadapter</th><th>vmnicX und vmnicY</th></tr>
<tr><td>Lastausgleich</td><td>Routerbasiert auf der ursprünglichen ID des virtuellen Ports</td></tr>
<tr><td>Aktive Adapter</td><td>vmnicX und vmnicY</td></tr>
</tbody>
</table>

### Bearbeiten vorhandener VM-Portgruppen
{: #edit-existing-virtual-machine-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabelle 5. vmPG-Management-Portgruppe</caption>
<tbody>
<tr><th>Netzbezeichnung</th><th>*vmPG-Management*</th></tr>
</tbody>
</table>

### Vorhandene Portgruppen des VM-Kernels bearbeiten
{: #edit-existing-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabelle 6. Portgruppe für vmkPG-Management</caption>
<tbody>
<tr><th>Netzbezeichnung</th><th>*vmkPG-Management*</th></tr>
</tbody>
</table>

### vMotion-Portgruppen für VM-Kernel hinzufügen
{: #add-vmotion-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabelle 7. vMotion-Portgruppe</caption>
<tbody>
<tr><th>Verbindungstyp</th><th>VMKernel</th></tr>
<tr><td>Netzbezeichnung</td><td>*vmkPG-vMotion*</td></tr>
<tr><td>VLAN-ID</td><td>*&lt;Speicher-VLAN&gt; (1102)*</td></tr>
<tr><td>vMotion-Datenverkehr</td><td>Aktiviert</td></tr>
<tr><td>IP-Adresse</td><td>*172.16.10.X/24*<br/><br/>*Benutzerdefinierte Adresse, die möglicherweise in einem anderen Teilnetz liegt. Vergewissern Sie sich, dass die anderen vMotion-Adressen auf den Hosts sich in demselben Teilnetz befinden.*</td></tr>
<tr><td>Teilnetzmaske</td><td>255.255.255.0</td></tr>
</tbody>
</table>

### Fehlertoleranzportgruppe für VM-Kernel hinzufügen
{: #add-fault-tolerance-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabelle 8. FT-Portgruppe</caption>
<tbody>
<tr><th>Verbindungstyp</th><th>VM-Kernel</th></tr>
<tr><td>Netzbezeichnung</td><td>*vmkPG-FT*</td></tr>
<tr><td>VLAN-ID</td><td>*&lt;Speicher-VLAN&gt; (1102)*</td></tr>
<tr><td>Fehlertoleranzprotokollierung</td><td>Aktiviert</td></tr>
<tr><td>IP-Adresse</td><td>*172.16.20.X/24*<br/><br/>*Benutzerdefinierte Adresse, die bei Bedarf in einem anderen Teilnetz liegen kann. Vergewissern Sie sich, dass die anderen FT-Adressen auf den Hosts sich in demselben Teilnetz befinden.*</td></tr>
<tr><td>Netzmaske</td><td>255.255.255.0</td></tr>
</tbody>
</table>

### VM-Kernel-Portgruppen für Speicher hinzufügen
{: #add-storage-vm-kernel-port-group}

Aktualisieren Sie den Abschnitt **Hinweise** für jede portierbare IP-Adresse mit dem Namen des Hosts und dem zugeordneten VM-Kernel-Port. Der Abschnitt
**Hinweise** kann lokalisiert werden, indem das {{site.data.keyword.slportal_full}} aufgerufen und dann **Netz > IP-Management > Teilnetze > [Teilnetz]** ausgewählt wird.

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabelle 9. Speicherportgruppe</caption>
<tbody>
<tr><th>Verbindungstyp</th><th>VM-Kernel</th></tr>
<tr><td>Netzbezeichnung</td><td>*vmkPG-Storage*</td></tr>
<tr><td>VLAN-ID</td><td>*&lt;Speicher-VLAN&gt; (1102)*</td></tr>
<tr><td>IP-Adresse</td><td>*Portierbare private Adresse*<br/><br/>*Eine IP-Adresse, die aus den portierbaren privaten Adressen ausgewählt wird, die an das Speicher-VLAN gebunden sind.*</td></tr>
<tr><td>Netzmaske</td><td>*Dem IP-Bereich zugeordnete Teilnetzmaske*</td></tr>
</tbody>
</table>

### Portgruppe für öffentliche Adresse hinzufügen
{: #add-public-address-port-group}

Aktualisieren Sie den Abschnitt **Hinweise** für jede portierbare IP-Adresse mit dem Namen des Hosts und dem zugeordneten VM-Kernel-Port. Der Abschnitt
**Hinweise** befindet sich im {{site.data.keyword.slportal}} und kann über **Netz > IP-Management > Teilnetze > [Teilnetz]** ausgewählt werden.

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tabelle 10. Öffentliche Portgruppe</caption>
<tbody>
<tr><th>Verbindungstyp</th><th>Virtuelle Maschine</th></tr>
<tr><td>Netzbezeichnung</td><td>vmPG-Public</td></tr>
<tr><td>VLAN-ID</td><td>&lt;Primäres öffentliches VLAN&gt; (z. B. 2101)</td></tr>
</tbody>
</table>

## Schritt 7 - Download und Upload von ISO-Images und vCenter Virtual Appliance
{: #step-7-download-or-upload-iso-images-and-vcenter-virtual-appliance}

Nun ist die Umgebung bereit für die Bereitstellung von VMware vCenter Virtual Appliance und die Installation einer virtuellen Maschine für DNS, Active Directory oder BIND. Vor der Bereitstellung müssen Sie allerdings die Images herunterladen. Um die Images herunterzuladen, rufen Sie den Remote Desktop für den virtuellen Server auf, der zuvor bereitgestellt wurde, und laden Sie die entsprechenden Images auf den virtuellen Server für Ihre Umgebung herunter:

* Active Directory / Windows DNS: Windows Server 2008 R2/Windows Server 2012 ISO Image (Ihre lizenzierten Medien)
* Linux BIND: [CentOS-Installationsimage ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://isoredirect.centos.org/centos/6/isos/x86_64/){: new_window}
* [vCenter Virtual Appliance ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://my.vmware.com/web/vmware/info/slug/datacenter_cloud_infrastructure/vmware_vsphere/5_5){: new_window} (gültiges VMware-Abonnement erforderlich)

Nach dem Herunterladen der ISO-Komponente müssen Sie das Image in den Datenspeicher eines Management-Hosts hochladen, um die Zuordnung zu einer virtuellen Maschine durchzuführen. Verwenden Sie den vSphere-Client, um eine Verbindung zu einem Management-Host herzustellen und ein ISO-Verzeichnis im lokalen Datenspeicher zu erstellen.

## Schritt 8 DNS bereitstellen
{: #step-8-deploying-dns}

Sie müssen eine virtuelle Maschine (auf einem Management-Host) bereitstellen und die DNS-Services installieren. Verwenden Sie den traditionellen vSphere-Client, um eine virtuelle Maschine auf dem Management-ESXi-Host zu erstellen, auf dem sich die Windows- oder Linux-ISO-Komponente befindet. Stellen Sie eine Verbindung zur passenden ISO-Komponente (Windows oder CentOS) her, um einen DNS-Server auf der virtuellen Maschine bereitzustellen. Vergewissern Sie sich, dass die virtuelle Maschine der VM-Portgruppe ((vmpg)-management-Portgruppe) zugeordnet wird, die in einem der vorherigen Schritte erstellt wurde.

Nach der Installation der virtuellen Maschine müssen Sie eine IP-Adresse und das Standardgateway aus der portierbaren privaten Teilnetzgruppe zuweisen. Wenn Sie das VLAN-Arbeitsblatt in Anhang A verwenden, ist das Management-VM-Teilnetz zu verwenden. Aktualisieren Sie den Abschnitt **Hinweise** für jede portierbare IP-Adresse mit dem Namen der zugeordneten virtuellen Maschine. Der Abschnitt **Hinweise** kann lokalisiert werden, indem das {{site.data.keyword.slportal}} aufgerufen und dann **Netz > IP-Management > Teilnetze > [Teilnetz]** ausgewählt wird.

Obwohl die detaillierte Behandlung der Schritte, die zum Aktivieren des DNS ausgeführt werden müssen, den Rahmen des vorliegenden Dokuments sprengt, werden die folgenden Anleitungen bereitgestellt:

1. Geben Sie für die **DNS-Weiterleitung** die lokalen DNS-Host des Typs 'service.softlayer.com' an:
  * `rs1.service.softlayer.com 10.0.80.11`
  * `rs2.service.softlayer.com 10.0.80.12`
2. Nach der Einrichtung des DNS müssen Sie eine lokale DNS-Zone (dal06.mycompany.local) und eine Reverse-Lookup-Zone für alle portierbaren und primären Teilnetze erstellen, die bereitgestellt werden.
3. Fügen Sie Datensätze vom Typ A HOST für die Management-IP-Adresse jedes Hosts (vmk0 für vmkPG-management) hinzu.
4. Fügen Sie einen Datensatz vom Typ A HOST aus dem portierbaren privaten Teilnetz hinzu, das an das Management-VLAN für vCenter Virtual Appliance gebunden ist.
5. Aktualisieren Sie den Abschnitt **Hinweise** des portierbaren IP-Teilnetzes, das Sie vCenter zugeordnet haben.

Weitere Informationen finden Sie unter den folgenden Links:
* [Windows DNS und Active Directory ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://social.technet.microsoft.com/wiki/contents/articles/12370.step-by-step-guide-for-setting-up-a-windows-server-2012-domain-controller.aspx){: new_window}.
<!--* [CentOS BIND ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.centos.org/docs/2/rhl-rg-en-7.2/s1-bind-configuration.html){: new_window}.-->

## Schritt 9 - vCenter Virtual Appliance bereitstellen und konfigurieren
{: #step-9-deploying-and-configuring-vcenter-virtual-appliance}

Nachdem das DNS nun konfiguriert ist, können Sie vCenter Server Appliance bereitstellen und konfigurieren. Führen Sie die folgenden Schritte aus, um die Appliance bereitzustellen:

1. Rufen Sie den Remote Desktop für den virtuellen Server auf und öffnen Sie den vSphere-Client.
2. Stellen Sie eine Verbindung zu einem Management-Host her und wählen Sie **Datei, OVF-Vorlage bereitstellen** aus.
3. Folgen Sie den Anweisungen des Assistenten, um die Bereitstellung abzuschließen.

<!--For more information about OVF Template deployment, see [VMware documentation ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://pubs.vmware.com/vsphere-55/index.jsp?topic=%2Fcom.vmware.vsphere.hostclient.doc%2FGUID-6C847F77-8CB2-4187-BD7F-E7D3D5BD897B.html&resultof=%22thick%22%20%22thin%22%20){: new_window}.-->

Da kein DHCP-Server (DHCP = Dynamic Host Configuration Protocol) verfügbar ist, um vCenter Virtual Appliance beim Einschalten eine IP-Adresse zuzuordnen, müssen Sie die Stammkonsole verwenden, um die Appliance zu konfigurieren. **Hinweis:** An der vCenter Virtual Appliance-Konsole wird eine Nachricht wie die folgende angezeigt: `NO NETWORKING DETECTED`. 

Führen Sie die folgenden Schritte aus, um die Appliance zu konfigurieren:

1. Melden Sie sich bei der Konsole an und geben Sie als **Benutzername** 'root' und als **Kennwort** 'vmware' an.
2. Führen Sie `/opt/vmware/share/vami/vami_config_net` aus und folgen Sie den Anweisungen des Textassistenten, um die IP-, Teilnetz- und DNS-Eigenschaften zu konfigurieren. Verwenden Sie hierbei die IP-Adresse des DNS- oder BIND-Servers, der in **Schritt 8: DNS bereitstellen** erstellt wurde.
3. Speichern Sie die Netzeinstellungen, beenden Sie die Konsole und öffnen Sie einen Browser für den virtuellen Server.
4. Rufen Sie die IP-Adresse auf, die Sie dem VCVA-System (VCVA = vCenter Virtual Appliance) zugeordnet haben, das über den Port 5480 angeschlossen wurde<!-- (https://:5480)-->.
5. Akzeptieren Sie den Endbenutzerlizenzvertrag im Assistenten, beantworten Sie die Frage zum **Customer Improvement Experience Program** und wählen Sie dann  **Optionen konfigurieren, Angepasste Konfiguration definieren** aus.
6. Klicken Sie auf **Weiter** und geben Sie die folgenden Werte ein:
<table border="1" cellpadding="0" cellspacing="0"><caption>Tabelle 11. VCVA-Installationsassistent</caption><tbody><tr><th>Assistentenmenü</th><th>Option</th><th>Wert</th></tr><tr><td>Datenbankeinstellungen</td><td>Datenbanktyp</td><td>eingebettet</td></tr><tr><td>SSO-Einstellungen</td><td>SSO-Bereitstellungstyp</td><td>eingebettet</td></tr><tr><td>SSO-Einstellungen</td><td>Neues Administratorkennwort *(für administrator@vsphere.local)*</td><td>&lt;Kennwort eingeben&gt;</td></tr><tr><td>SSO-Einstellungen</td><td>Neues Kennwort erneut eingeben</td><td>&lt;Geben Sie das gleiche Kennwort wie zuvor ein&gt;</td></tr><tr><td>Zeitsynchronisation</td><td>NTP-Synchronisation</td><td>servertime.service.softlayer.com</td></tr></tbody></table>
7. Klicken Sie auf **Starten**. Die VCVA wird konfiguriert.
8. Ändern Sie das Rootkennwort, indem Sie die Optionen unter **Administrator** verwenden.
9. Melden Sie sich von der Webseite für die VCVA-Konfiguration ab.
10. Rufen Sie vSphere Web Client auf, indem Sie die IP-Adresse der VCVA und dann `9443/vsphere-client` angeben<!-- (https://:9443/vsphere-client)-->.
11. Melden Sie sich beim Root an und wählen Sie **Verwaltung, Lizenzen** aus.
12. Geben Sie Ihre VMware vCenter-Lizenz ein.

## Schritt 10 - vCenter-Cluster und verteilten virtuellen Switch erstellen
{ #step-10-create-vcenter-clusters-and-distributed-virtual-switch}

Nachdem die VCVA konfiguriert und lizenziert wurde, können Sie nun das Rechenzentrum und die Clusterkonstrukte sowie die verteilten virtuellen Switches für den Kapazitätscluster erstellen.

### Rechenzentrum und Cluster erstellen
{: #creating-data-center-and-clusters}

1. Rufen Sie auf dem vSphere-Client die **Hauptanzeige** auf.
2. Wählen Sie dort **Einführung** aus und klicken Sie dann auf **hier klicken**.
3. Klicken Sie auf **Rechenzentrum erstellen**.
4. Geben Sie einen Namen für das Rechenzentrum ein. (Die Beispielumgebung umfasst das Rechenzentrum 'Toronto 01'. Verwenden Sie `Toronto 01` als Name des Rechenzentrums.)
5. Nach der Erstellung des Rechenzentrums müssen Sie auf der Seite **Einführung** auf **Cluster erstellen** klicken.
6. Ordnen Sie dem ersten Cluster den Namen `Management` zu. Geben Sie für die anderen Optionen keinen Wert an und klicken Sie dann auf **OK**.
7. Erstellen Sie einen weiteren Cluster, wenn Sie diesen Arbeitsschritt abgeschlossen haben, indem Sie den gleichen Prozess wie für den Management-Cluster wiederholen.

Nun können Sie die Management-Hosts und Kapazitätshosts zum Management-Cluster und zum Kapazitätscluster hinzufügen, indem Sie **Host hinzufügen** verwenden. Verwenden Sie unbedingt den vollständig qualifizierten Domänennamen (FQDN) des Servers und nicht die IP-Adresse, sodass beim Hinzufügen der Hosts das DNS verwendet wird.

Nachdem Sie die ESXi-Hosts zu vCenter hinzugefügt haben, werden möglicherweise verschiedene Warnungen bezüglich der Aktivierung der Shell und von SSH auf jedem ESXi-Host angezeigt. Wenn Sie diese Warnungen unterdrücken möchten, klicken Sie im Popup-Fenster auf **Warnung unterdrücken > Ja**. Wird der Link zum Unterdrücken der Warnungen nicht angezeigt, dann führen Sie die folgenden Schritte aus:

1. Rufen Sie die Registerkarte **Verwalten** des ESXi-Hosts auf.
2. Wählen Sie **Einstellungen** aus und klicken Sie auf **Erweiterte Systemeinstellungen**.
3. Suchen Sie den Schlüssel **UserVars.SupressShellWarning** und ändern Sie den Wert in **1**.
4. Klicken Sie auf **OK**.

Nachdem die Management-Hosts und Kapazitätshosts zu den jeweiligen Clustern hinzugefügt wurden, rufen Sie die einzelnen Hosts auf und richten Sie DNS und NTP ein. Führen Sie die folgenden Schritte aus, um DNS einzurichten:

1. Klicken Sie auf einen Host und wählen Sie **Verwalten > Netzbetrieb** aus.
2. Wählen Sie den Standardsystemstack (**TCP/IP-Konfiguration**) aus und klicken Sie auf das Stiftsymbol.
3. Geben Sie die IP-Adresse des DNS-Servers, den Sie zuvor erstellt haben, und den Host- und Domänennamen ein.

Für NTP-Einstellungen:

1. Rufen Sie **Verwalten, Einstellungen, Zeitkonfiguration** auf.
* Geben Sie `servertime.service.softlayer.com` als NTP-Server ein.
* Legen Sie für die **NTP-Servicestartrichtlinie** die Einstellung `Mit Host starten und stoppen` fest.

***Verteilten virtuellen Switch für Kapazitätshosts erstellen***

1. Verwenden Sie vSphere Web Client, um zum **Netzbetrieb** zu wechseln, und klicken Sie mit der rechten Maustaste auf den Namen des Rechenzentrums.
2. Wählen Sie **Neuer verteilter Switch** aus.
3. Ordnen Sie dem verteilten Switch einen Namen zu und klicken Sie dann auf **Weiter**.
4. Wählen Sie die gewünschte Version des verteilten Switchs aus und klicken Sie dann auf **Weiter**.
5. Geben Sie in der Anzeige **Einstellungen bearbeiten** für die Anzahl der Uplinks den Wert `2` ein und nehmen Sie die Auswahl von **Standardportgruppe erstellen** zurück.
6. Klicken Sie auf **Weiter > Fertigstellen** und erstellen Sie den verteilten virtuellen Switch.

***Portgruppen für verteilten virtuellen Switch erstellen***

Nachdem der verteilte virtuelle Switch nun existiert, müssen Sie Portgruppen für vMotion, die Fehlertoleranz, VMs und den Speicher erstellen.

***Portgruppe 'dvpg-Private-VM Management' erstellen***

1. Rufen Sie mit vSphere Web Client den Abschnitt 'Netzbetrieb' auf.
2. Klicken Sie mit der rechten Maustaste auf den verteilten virtuellen Switch (DVS).
3. Klicken Sie auf **Neue verteilte Portgruppe…** und geben Sie die Informationen in Tabelle 14 für die erste Portgruppe ein.
4. Übernehmen Sie für alle Optionen, die in der Tabelle nicht angegeben werden, die Standardwerte.

| Menü 'Neue verteilte Portgruppe' | Feld | Wert |
| --- | --- | --- |
| Name und Position | Name | dvpg-Private-VM Management |
| Einstellungen konfigurieren | Erweitert | Konfiguration der angepassten Standardrichtlinien überprüfen |
| Richtlinien konfigurieren (Teaming und Failover)| Lastausgleich | Route auf Basis der Arbeitslast des physischen NIC |
| Richtlinien konfigurieren (Teaming und Failover)| Failoverreihenfolge | Aktive Uplinks: Uplink 1 & Uplink 2 |
<caption>Tabelle 12. DVS-VM-Management-Portgruppe</caption>

Nachdem Sie die erste Portgruppe erstellt haben, erstellen Sie die verbleibenden Portgruppen mit den folgenden Konfigurationsoptionen (Tabelle 15 bis Tabelle 19).

***Portgruppe 'dvpg-Private-vMotion' erstellen***

| Menü 'Neue verteilte Portgruppe' |Feld|Wert|
|---|---|---|
| Name und Position |Name|dvpg-Private-vMotion|
|Einstellungen konfigurieren|VLAN-Typ|VLAN|
|Einstellungen konfigurieren|VLAN-ID|&lt;Speicher-VLAN&gt;|
|Einstellungen konfigurieren| Erweitert | Konfiguration der angepassten Standardrichtlinien überprüfen |
|Richtlinien konfigurieren (Teaming und Failover)|Lastausgleich| Route auf Basis der Arbeitslast des physischen NIC |
|Richtlinien konfigurieren (Teaming und Failover)| Failoverreihenfolge |Aktive Uplinks: Uplink 1 & Uplink 2 |
<caption>Tabelle 13. DVS-vMotion-Portgruppe</caption>

***Portgruppe 'dvpg-Private-Fault Tolerance' erstellen***

| Menü 'Neue verteilte Portgruppe' |Feld|Wert|
|---|---|---|
|Name und Position |Name|dvpg-Private-Fault Tolerance|
|Einstellungen konfigurieren|VLAN-Typ|VLAN|
|Einstellungen konfigurieren|VLAN-ID|&lt;Speicher-VLAN&gt;|
|Einstellungen konfigurieren| Erweitert |Konfiguration der angepassten Standardrichtlinien überprüfen |
|Richtlinien konfigurieren (Teaming und Failover)|Lastausgleich| Route auf Basis der Arbeitslast des physischen NIC |
|Richtlinien konfigurieren (Teaming und Failover)|Failoverreihenfolge |Aktive Uplinks: Uplink 1 & Uplink 2 |
<caption>Tabelle 14. DVS-FT-Portgruppe</caption>

***Portgruppe 'dvpg-Private-VM Access' erstellen***

|Menü 'Neue verteilte Portgruppe' |Feld|Wert|
|---|---|---|
|Name und Position |Name|dvpg-Private-VM Access|
|Einstellungen konfigurieren|VLAN-Typ|VLAN|
|Einstellungen konfigurieren|VLAN-ID|&lt;VM-VLAN&gt;|
|Einstellungen konfigurieren|Erweitert |Konfiguration der angepassten Standardrichtlinien überprüfen |
|Richtlinien konfigurieren (Teaming und Failover)|Lastausgleich|Route auf Basis der Arbeitslast des physischen NIC |
|Richtlinien konfigurieren (Teaming und Failover)|Failoverreihenfolge |Aktive Uplinks: Uplink 1 & Uplink 2 |
<caption>Tabelle 15. DVS-VM-Access-Portgruppe</caption>

***'dvpg-Private-Storage' erstellen***

|Menü 'Neue verteilte Portgruppe' |Feld|Wert|
|---|---|---|
|Name und Position |Name|dvpg-Private-Storage Pfad A|
|Einstellungen konfigurieren|VLAN-Typ|VLAN|
|Einstellungen konfigurieren|VLAN-ID|&lt;Speicher-VLAN&gt;|
|Einstellungen konfigurieren|Erweitert |Konfiguration der angepassten Standardrichtlinien überprüfen |
|Richtlinien konfigurieren (Teaming und Failover)|Lastausgleich|Route auf Basis der Arbeitslast des physischen NIC |
|Richtlinien konfigurieren (Teaming und Failover)|Failoverreihenfolge |Aktive Uplinks: Uplink 1 & Uplink 2 |
<caption>Tabelle 16. DVS-Storage-Portgruppe</caption>

***'dvpg-Primary-Public' erstellen***

|Menü 'Neue verteilte Portgruppe'|Feld|Wert|
|Name und Position|Name|dvpg-Private-Storage|
|Einstellungen konfigurieren|VLAN-Typ|VLAN
|Einstellungen konfigurieren|VLAN-ID|&lt;Primäres öffentliches VLAN&gt;|
|Einstellungen konfigurieren|Erweitert|Konfiguration der angepassten Standardrichtlinien überprüfen|
|Richtlinien konfigurieren (Teaming und Failover)|Lastausgleich|Route auf Basis der Arbeitslast des physischen NIC|
|Richtlinien konfigurieren (Teaming und Failover)|Failoverreihenfolge|Aktive Uplinks: Uplink 1 & Uplink 2|
<caption>Tabelle 17. DVS-Storage-Portgruppe (Pfad B)</caption>

## Schritt 11 - ESXi-Hosts im Kapazitätscluster auf verteilten virtuellen Switch migrieren
{: #step-11-migrating-esxi-hosts-in-capacity-cluster-to-distributed-virtual-switch}

Nachdem die Kapazitätshosts nun zum Kapazitätscluster hinzugefügt wurden, können Sie die Konfiguration für den virtuellen Standardswitch auf den verteilten virtuellen Switch migrieren, den Sie in *Schritt 10: vCenter-Cluster & Distributed Virtual Switch erstellen* erstellt haben. Die Migration wird für einen Host durchgeführt. Später wenden Sie ein Hostprofil an, um den Rest des Clusters zu konfigurieren.

Bevor Sie mit dem Hinzufügen von VMkernel-Adaptern beginnen können, müssen Sie die vmnics den Uplinks des verteilten virtuellen Switchs zuordnen.

1. Klicken Sie auf **vCenter-Bestandslisten, Verteilte Switches**.
2. Wählen Sie den gewünschten verteilten Switch für die Kapazitätshosts aus.
3. Klicken Sie auf der Seite **Einführung** auf **Hosts hinzufügen und verwalten**.
4. Verwenden Sie die folgenden Einstellungen, um Uplinks hinzuzufügen und die vorhandene VMkernel-Komponente zu migrieren, die für das Management des Hosts vorgesehen sind.
  - <table border="1" cellpadding="0" cellspacing="0"><caption>Tabelle 18. DVS - Hosts hinzufügen</caption><tbody><tr><th>Menü</th><th>Feld</th><th>Wert</th></tr><tr><td>Task auswählen</td><td>Tasks auswählen</td><td>Hosts hinzufügen</td></tr><tr><td>Hosts auswählen</td><td>Auf **Neue Hosts** klicken</td><td>Auf Kapazitätshost klicken</td></tr><tr><td>Netzadaptertasks auswählen</td><td>Netzadaptertasks auswählen</td><td>'Physische Adapter verwalten' und 'VMkernel-Adapter verwalten' auswählen</td></tr></tbody></table>
5. Wählen Sie einen der privaten vmnics aus und klicken Sie dann auf **Physische Netzadapter verwalten > Uplink zuordnen**.
6. Wählen Sie in der Popup-Anzeige `uplink1` aus und klicken Sie dann auf **OK**.
7. Wiederholen Sie diese Schritte für den anderen privaten vmnic und ordnen Sie ihn dem Element `uplink2` zu.
8. Klicken Sie auf **Weiter**, heben Sie den VMkernel-Adapter 'vmk0' hervor und klicken Sie auf **Portgruppe zuordnen**.
9. Wählen Sie im Popup-Fenster **dvpg-Private-VM Management** aus und klicken Sie auf **OK**.
10. Klicken Sie zwei Mal auf **Weiter** und dann auf **Fertigstellen**, um die Migration des verteilten virtuellen Switchs abzuschließen.**Hinweis:** Möglicherweise kommt es zu einem kurzen Verlust der Netzkonnektivität zum Host. Die Verbindung wird rasch wiederhergestellt.

Nach der Migration des Adapters 'vmk0' auf den verteilten virtuellen Switch können Sie VM-Kernel zu jeder Portgruppe im DVS hinzufügen.

11. Klicken Sie in vCenter für den Host auf **Verwalten > Netzbetrieb**.
12. Rufen Sie **VM-Kerneladapter > Netzbetrieb für Host hinzufügen** auf und fügen Sie die VM-Kernel-Adapter hinzu, die in den Tabellen 19 und 21 aufgeführt sind. Zum Hinzufügen dieser Adapter müssen Sie das Menü 'VM-Kernel-Adapter' aufrufen, auf das Sie über die Registerkarte **Verwalten > Netzbetrieb** auf dem Host in vCenter zugreifen können. Klicken Sie anschließend auf das Symbol 'Netzbetrieb für Host hinzufügen' und fügen Sie die folgenden VM-Kernel-Adapter hinzu.

***'vmk1' für vMotion hinzufügen***

|Menü|Feld|Wert|
|---|---|---|
|Verbindungstyp auswählen|Verbindungstyp auswählen|VMKernel Network Adapter|
|Zieleinheit auswählen|Vorhandene verteilte Portgruppe auswählen|dvpg-Private-vMotion|
|Netzadaptertasks auswählen|Netzadaptertasks auswählen|**Physische Adapter verwalten** und **VM-Kernel-Adapter verwalten** auswählen|
|Porteigenschaften|Services aktivieren|vMotion-Datenverkehr prüfen|
|IPv4-Einstellungen|IPv4-Adresse|*172.16.10.X/24*<br/><br/>*Dies ist eine benutzerdefinierte Adresse, die bei Bedarf in einem anderen Teilnetz liegen kann. Vergewissern Sie sich, dass die anderen vMotion-Adressen auf den Hosts sich in demselben Teilnetz befinden.*|
|IPv4-Einstellungen|Teilnetzmaske|255.255.255.0|
<caption>Tabelle 19. Netzbetrieb für Host - vMotion</caption>

***'vmk2' für Fehlertoleranz hinzufügen***

|Menü|Feld|Wert|
|---|---|---|
|Verbindungstyp auswählen|Verbindungstyp auswählen|VMKernel Network Adapter|
|Zieleinheit auswählen|Vorhandene verteilte Portgruppe auswählen|dvpg-Private-Fault Tolerance|
|Netzadaptertasks auswählen|Netzadaptertasks auswählen|**Physische Adapter verwalten** und **VM-Kernel-Adapter verwalten** auswählen|
|Porteigenschaften|Services aktivieren|Fehlertoleranzprotokollierung prüfen|
|IPv4-Einstellungen|IPv4-Adresse|*172.16.20.X/24*<br/><br/>*Dies ist eine benutzerdefinierte Adresse, die bei Bedarf in einem anderen Teilnetz liegen kann. Vergewissern Sie sich, dass die anderen FT-Adressen auf den einzelnen Hosts sich in demselben Teilnetz befinden.*|
|IPv4-Einstellungen|Teilnetzmaske|255.255.255.0|
<caption>Tabelle 20. Netzbetrieb für Host - Fehlertoleranz</caption>

***'vmk3' für Speicher hinzufügen***

|Menü|Feld|Wert|
|---|---|---|
|Verbindungstyp auswählen|Verbindungstyp auswählen|VMKernel Network Adapter|
|Zieleinheit auswählen|Vorhandene verteilte Portgruppe auswählen|dvpg-Private-Storage|
|Netzadaptertasks auswählen|Netzadaptertasks auswählen|'Physische Adapter verwalten' und 'VMkernel-Adapter verwalten' auswählen|
|IPv4-Einstellungen|IPv4-Adresse|*Portierbare private Adresse*<br/><br/>*Diese IP-Adresse wird aus den portierbaren privaten Adressen ausgewählt, die an das Speicher-VLAN gebunden sind. Diese Adresse muss sich in einem anderen Teilnetz als Speicherpfad B befinden.*|
|IPv4-Einstellungen|Teilnetzmaske|Dem IP-Bereich zugeordnete Teilnetzmaske|
<caption>Tabelle 21. Netzbetrieb für Host - Speicher</caption>

***Hostprofil erstellen***

Zur Erstellung eines Hostprofils müssen Sie es auf dem einzigen Kapazitätshost erfassen, den Sie zuvor konfiguriert haben.

1. Rufen Sie die Hauptanzeige von vSphere Web Client auf und klicken Sie dann auf das Symbol **Hostprofile**.
2. Klicken Sie auf das grüne Pluszeichen (+) und dann auf **Profil aus Host extrahieren** und wählen Sie anschließend den zuvor konfigurierten Kapazitätshost im Popup-Fenster aus.
3. Klicken Sie auf **Weiter**.
4. Ordnen Sie dem Hostprofil (Hostprofil 'Capacity01') einen geeigneten Namen zu, geben Sie eine Beschreibung ein und klicken Sie dann auf **Weiter**.
5. Überprüfen Sie die Einstellungen und klicken Sie dann auf **Fertigstellen**.

Nach der Erstellung des Hostprofils müssen Sie es so ändern, dass keine Anforderungen für MAC-Adressen abgesetzt werden, wenn das Profil auf die restlichen Hosts im Kapazitätscluster angewendet wird.

1. Klicken Sie mit der rechten Maustaste auf das Hostprofil, das Sie erstellt haben, und wählen Sie dann **Einstellungen bearbeiten > Hostprofil bearbeiten, Virtueller NIC des Hosts** aus.
2. Ändern Sie im rechten Teilfenster die Einstellung **Ermitteln, wie MAC-Adresse für 'vmknic' ausgewählt wird** in **Benutzer muss die Richtlinienoption explizit auswählen**.
3. Klicken Sie auf **Weiter** und dann auf **Fertigstellen**.

***Hostprofil einem Kapazitätscluster zuordnen***

Nach der Erstellung eines Hostprofils müssen Sie das Hostprofil einem Cluster zuordnen. Cluster werden zugeordnet, damit sie auf die Kapazitätshosts angewendet werden können.

1. Rufen Sie die Ansicht **Host und Cluster** in vSphere Web Client auf.
2. Rufen Sie für jeden Host im Cluster den Wartungsmodus auf. **Hinweis:** Profile können nur auf Hosts angewendet werden, die sich im Wartungsmodus befinden.
3. Klicken Sie mit der rechten Maustaste auf den Kapazitätscluster und wählen Sie dann im Popup-Menü **Hostprofil zuordnen** aus.
4. Wählen Sie das von Ihnen erstellte Hostprofil aus und klicken Sie auf **Weiter**.
5. Geben Sie in der Anzeige **Host anpassen** die benötigten Informationen ein und klicken Sie dann auf **Fertigstellen**.
6. Versetzen Sie die Hosts wieder in den Nicht-Wartungsmodus.

## Schritt 12 - Gemeinsam genutzten Speicher bestellen, konfigurieren und zuordnen
{: #step-12-order-configure-and-attach-shared-storage}

Bevor Sie fortfahren, ist es zwingend erforderlich, dass Sie gemeinsam genutzten Speicher zur Verwendung mit den Management-Clustern, Kapazitätsclustern und Hosts in der Umgebung bestellen, konfigurieren und zuordnen. Wenn Sie die Multi-Tenant-Lösung für gemeinsam genutzten Speicher (File Storage) von {{site.data.keyword.cloud_notm}} verwenden wollen, dann sollten Sie die Informationen im [Architekturhandbuch für IBM File Storage for {{site.data.keyword.cloud_notm}} mit VMware](/docs/infrastructure/FileStorage?topic=FileStorage-architectureguide) lesen.

## Schritt 13 - HA/DRS und svMotion vCVA aktivieren
{: #step-13-enabling-ha-drs-and-svmotion-vcva}

***HA/DRS auf Management-Clustern und Kapazitätsclustern aktivieren***

Nachdem der gemeinsam genutzte Speicher eingerichtet wurde, müssen Sie HA und DRS aktivieren, um zusätzliche Schutz- und Lastausgleichsfunktionen für die VMs im Management-Cluster bereitzustellen.

1. Rufen Sie vSphere Web Client auf.
* Wählen Sie 'Verwalten', 'Einstellungen für Management-Cluster' aus.
* Wählen Sie vSphere DRS aus und klicken Sie dann auf 'Bearbeiten'. Stellen Sie sicher, dass die folgenden Einstellungen ausgewählt wurden: **vSphere DRS aktivieren**, Automatisierungsstufe - **Vollständig automatisiert**, Schiebereinstellung für Migrationsschwellenwert - Mittel, VM-Automatisierung - **Individuelle VM-Automatisierungsstufen aktivieren**, Energiemanagement - **Aus**.  
* Vergewissern Sie sich in der Anzeige für die vSphere-HA-Einstellungen, dass die folgenden Einstellungen ausgewählt wurden: **vSphere HA aktivieren**, **Hostüberwachung**, VM-Neustartpriorität - **Mittel**, Hostisolationsantwort - **Eingeschaltet lassen**.  
***Storage vMotion - vCenter Virtual Appliance***

Nachdem der Speicher auf dem Management-Cluster eingerichtet und HA und DRS aktiviert wurden, müssen Sie für vCenter Virtual Appliance den gemeinsam genutzten Speicher angeben.

1. Klicken Sie mit der rechten Maustaste auf die passende Appliance und wählen Sie dann im Popup-Menü **Migrieren** aus.
2. Wählen Sie **Datenspeicher ändern** aus und klicken Sie auf **Weiter**.
3. Wählen Sie den iSCSI-Datenträger aus, der zuvor im Management-Cluster angehängt wurde, und klicken Sie auf **Weiter**.
4. Überprüfen Sie Ihre Auswahl und klicken Sie dann auf **Weiter**.

Die erweiterte Single-Site-VMware-Umgebung ist nun vollständig.

## Zusammenfassung
{: #summary}

Sie verfügen nun über eine VMware-Umgebung, die in einem IBM Cloud-Rechenzentrum ausgeführt wird. Ihre VMware-Umgebung kann Produktionsworkloads ausführen und eine lokale IBM Cloud-Bereitstellung ergänzen. Die Umgebung stellt eine Umsetzung der bewährten Verfahren (Best Practices) für VMware dar und aktiviert Funktionen wie z. B. VMware DRS, HA, Storage DRS und die Redundanz beim Netzbetrieb. Sie können diese Referenzarchitekturbereitstellung durch größere Kapazitätshosts oder Management-Hosts und eine höhere Speicherkapazität ausbauen.

Weitere Informationen zu VMware finden Sie in [VMware bereitstellen](/docs/infrastructure/vmware?topic=VMware-using-cookbooks-for-vmware-deployments) und [Häufig gestellte Fragen zu VMware](/docs/infrastructure/vmware?topic=VMware-faqs-vmware).

## Anhang A: VLAN-Arbeitsblatt
{: #appendix-a-vlan-worksheet}

|VLAN-Typ|VLAN-Nummer|IP-Bereich|Gateway|Zweck|
|---|---|---|---|---|
|Primär privat||||Management|
|Primär privat||||Speicherung|
|Primär privat||||Virtuelle Maschinen|
|Primär öffentlich ||||Öffentlicher Zugriff|
|Portierbar privat||||Management-VMs|
|Portierbar privat||||Speicherung|
|Portierbar privat||||Virtuelle Maschinen|
|vMotion|||Nicht zutreffend||
|Fehlertoleranz|||Nicht zutreffend|||
<caption>VLAN-Arbeitsblatt</caption>
