---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Concessione ai contenitori dell'accesso alla rete privata tramite PIM
{: #giving-containers-access-to-the-private-network-through-pim}

Puoi concedere l'accesso alla tua rete privata ai contenitori. Devi però comprendere le implicazioni derivanti dal concedere l'accesso alla tua infrastruttura interna ai tuoi clienti, cosa che potrebbe portare a problemi di sicurezza.

1. Vai al contenitore al quale desideri concedere l'accesso alla rete privata.
2. Fai clic sulla scheda **Network** e fai clic su **Configure**.
3. Puoi ora configurare la sezione Bridged Network:
  * **Bridged Network**: seleziona la casella di spunta.
  * **Connect to**: non è obbligatorio fornire l'accesso alla rete privata {{site.data.keyword.cloud}}.
  * **Get IP address by DHCP**: deseleziona questa casella di spunta.
  * **IP address / Subnet Mask**: scegli un indirizzo IP INTERNO dal tuo pool di indirizzi privati.
  * Fai clic su **Submit**

Ora devi aggiornare la pagina dal link nell'angolo superiore destro e hai finito.
