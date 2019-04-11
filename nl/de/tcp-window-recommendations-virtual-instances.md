---
copyright:
  years: 2014, 2018
lastupdated: "2018-05-18"

subcollection: virtualization

keywords: TCP window size
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# TCP-Fenstergröße für virtuelle Instanzen optimieren
{: #optimizing-tcp-window-size-for-virtual-instances}

Verwenden Sie die folgenden Richtlinien, um die TCP-Fenstergröße einer VSI (Virtual Server-Instanz) zur Erzielung der optimalen Leistung auf einem {{site.data.keyword.cloud}}-Hypervisor zu optimieren. Virtuelle {{site.data.keyword.cloud_notm}}-Hosts verfügen über eine standardmäßige TCP-Puffergröße im Bereich zwischen 3 und 6 MB.

Zur Erzielung der besten Leistung mit Ihren VSIs müssen Sie die TCP-Fenstergröße auf einen Wert unterhalb des Bereichs zwischen 3 und 6 MB festlegen. Der Kernel legt die tatsächliche Speicherbegrenzung auf einen Wert fest, der dem Doppelten des angeforderten Werts entspricht, sodass die maximalen Einstellungen verdoppelt werden. Die maximale TCP-Fenstergröße sollte maximal 2 MB betragen. Wenn die Funktion für die automatische Optimierung aktiviert ist, dann werden die Größe des Empfangspuffers (und die TCP-Fenstergröße) dynamisch für jede Verbindung aktualisiert. Die Standardwerte für die pro Verbindung vorgesehene Speicherkapazität werden mithilfe der folgenden Elementarrays festgelegt:

* net.ipv4.tcp_rmem - Dies ist der Speicher, der für die TCP-Empfangspuffer reserviert ist
* net.ipv4.tcp_wmem - Dies ist der Speicher, der für die TCP-Sendepuffer reserviert ist

Die Werte in diesen Arrays stellen die Mindest-, Anfangs- und Maximalwerte für die Puffergrößen dar, die Sie zum Festlegen der Grenzwerte für die automatische Optimierung und den Lastausgleich für die Speicherbelegung verwenden. Zur Anzeige der aktuellen Speichereinstellungen für Fenster können Sie den Befehl `sysctl -a | grep mem` ausführen.

    # sysctl -a | grep mem
    vm.overcommit_memory = 0
    vm.nr_hugepages_mempolicy = 0
    vm.lowmem_reserve_ratio = 256   256     32
    vm.meminfo_legacy_layout = 1
    vm.memory_failure_early_kill = 0
    vm.memory_failure_recovery = 1
    net.core.wmem_max = 124928
    net.core.rmem_max = 124928
    net.core.wmem_default = 124928
    net.core.rmem_default = 124928
    net.core.optmem_max = 20480
    net.ipv4.igmp_max_memberships = 20
    net.ipv4.tcp_mem = 365184       486912  730368
    net.ipv4.tcp_wmem = 4096        16384   1048576
    net.ipv4.tcp_rmem = 4096        87830   1048576
    net.ipv4.udp_mem = 365184       486912  730368
    net.ipv4.udp_rmem_min = 4096
    net.ipv4.udp_wmem_min = 4096

Zum Anzeigen aller TCP-Einstellungen können Sie den Befehl `sysctl -a | grep tcp` ausführen.

Überprüfen Sie, ob die standardmäßigen erweiterten TCP-Funktionen aktiviert wurden:

    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_sack = 1
    net.ipv4.tcp_window_scaling = 1

Um festzustellen, ob die automatische Optimierung aktiviert ist, müssen Sie überprüfen, ob die folgende Einstellung den Wert '1' hat:

    net.ipv4.tcp_moderate_rcvbuf = 1

Um die TCP-Fenstereinstellungen zu ändern, müssen Sie die Einstellungen zu Ihrer Datei '/etc/sysctl.conf' hinzufügen.

<!--**Note:** The preceding general recommendations are used to tune the TCP window sizes of a VSI in the public cloud. You have many ways to optimize your network for different workloads.-->

Um die Änderungen auch nach einem Neustart beizubehalten, müssen Sie sicherstellen, dass die Optimierungsbefehle in der Datei `rc.local` hinzugefügt wurden.
