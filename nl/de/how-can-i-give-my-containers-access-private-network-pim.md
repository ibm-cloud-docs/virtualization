---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Containern über PIM Zugriff auf das private Netz erteilen
{: #giving-containers-access-to-the-private-network-through-pim}

Sie können den Containern Zugriff auf Ihr privates Netz erteilen. Sie müssen sich jedoch der Auswirkungen bewusst sein, die die Erteilung des Zugriffs auf Ihre interne Infrastruktur durch Ihre Kunden haben kann und die zu Sicherheitsproblemen führen können.

1. Rufen Sie den Container auf, für den der Zugriff auf das private Netz erteilt werden soll.
2. Klicken Sie auf die Registerkarte **Netz** und dann auf **Konfigurieren**.
3. Jetzt können Sie den Abschnitt für das überbrückte Netz einrichten:
  * **Überbrücktes Netz**: Wählen Sie das Kontrollkästchen aus.
  * **Verbinden mit**: Ist nicht erforderlich, um den Zugriff auf das private {{site.data.keyword.cloud}}-Netz zu ermöglichen.
  * **IP-Adresse durch DHCP abrufen**: Wählen Sie dieses Kontrollkästchen ab.
  * **IP-Adresse/Teilnetzmaske**: Wählen Sie eine interne IP-Adresse aus Ihrem Pool privater Adressen aus.
  * Klicken Sie auf **Senden**.

Nun müssen Sie die Seite über den Link oben rechts aktualisieren. Damit haben Sie alle erforderlichen Arbeitsschritte ausgeführt.
