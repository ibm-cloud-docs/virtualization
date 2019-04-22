---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Configurazione di una rete di macchine virtuali
{: #setting-up-a-virtual-machine-network}

La configurazione di rete sulla tua nuova macchina virtuale viene eseguita in pochi passi. Hai bisogno di un blocco di IP portatili separato sia per la rete pubblica sia per quella privata. Si presume che tu stia utilizzando un'offerta di virtualizzazione che richiede un dominio di broadcast Secondary on VLAN e non una sottorete Routed to VLAN (senza id rete/gateway/broadcast). Se non intendi utilizzare la rete privata sulla tua macchina virtuale, hai bisogno solo della sottorete pubblica. I blocchi di IP portatili possono essere ordinati direttamente tramite il portale a [Sales->Add IP Addresses ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://manage.softlayer.com/Sales/orderAdditionalServices/subnet){: new_window} oppure dal [Public Network IP Manager ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://manage.softlayer.com/PublicNetwork/ipManager){: new_window}.

Dopo che hai ottenuto i tuoi blocchi di IP, puoi configurare la rete sulla macchina virtuale. Se la macchina virtuale non è installata, puoi configurare il tuo blocco di rete pubblica tramite il processo di installazione del sistema operativo. Questo processo è il metodo più rapido per configurare la tua rete pubblica. Tutti i passi di configurazione della rete presumono che la prima interfaccia di rete sia la rete privata e la seconda interfaccia di rete sia la rete pubblica.

La configurazione della tua rete è diversa per ogni sistema operativo. Puoi trovare informazioni dettagliate sulla configurazione della tua rete per i seguenti sistemi operativi. **Nota:** i sistemi operativi raggruppati hanno la stessa configurazione di rete.

* Windows 2008 Server Core
* Windows 2003 Standard ed Enterprise
* Windows 2008 Web, Standard, Enterprise e Datacenter
* RedHat, Fedora e CentOS
* Ubuntu e Debian

La configurazione di rete richiede le seguenti informazioni sia per i blocchi di IP pubblici sia per quelli privati. Le informazioni sono disponibili nel portale per i blocchi di IP pubblici in [Public Network -> IP Manager ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://manage.softlayer.com/PublicNetwork/ipManager){: new_window} e per i blocchi di IP privati in [Private Network -> IP Manager ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://manage.softlayer.com/PrivateNetwork/ipManager){: new_window}.

    -IP Address
    -Gateway
    -Subnet Mask
    --Subnet conversion
    ---/29 - 255.255.255.248
    ---/28 - 255.255.255.240
    ---/27 - 255.255.255.224
    ---/26 - 255.255.255.192
    ---/25 - 255.255.255.128
    ---/24 - 255.255.255.0

## Windows 2008 Server Core
{: #windows-2008-server-core}

Windows 2008 Server Core Edition non fornisce un'interfaccia grafica per configurare la rete di sistema. La configurazione deve essere eseguita manualmente utilizzando il prompt dei comandi. Devi eseguire questi comandi. Gli indirizzi IP utilizzati in questo esempio devono essere sostituiti con gli indirizzi IP dai tuoi blocchi di IP. Questi comandi funzionano con versioni meno recenti di Windows Server che includono il comando [netsh ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://docs.microsoft.com/en-us/previous-versions/tn-archive/bb490939(v=technet.10)){: new_window}.

**Blocco di IP pubblici di esempio – 192.0.2.0/29**

* Indirizzo IP – 192.0.2.2
* Gateway – 192.0.2.1
* Maschera di sottorete – 255.255.255.248

**Blocco di IP privati di esempio – 10.0.0.0/29**

* Indirizzo IP – 10.0.0.2
* Gateway – 10.0.0.1
* Maschera di sottorete – 255.255.255.248

**Rete pubblica**

Questo comando crea la rete pubblica per connettere il tuo server a Internet.

* *Netsh interface ip set address name=”Local Area Connection 2” static 192.0.2.2 255.255.255.248 192.0.2.1*

Questi comandi creano le voci DNS. Se non stai utilizzando la rete privata, devi sostituire questi indirizzi IP con dei server DNS funzionanti.

* *Netsh interface ip add dns “Local Area Connection 2” 10.0.80.11*
* *Netsh interface ip add dns “Local Area Connection 2” 10.0.80.12*

**Rete privata**

I seguenti due comandi configurano la rete privata e creano l'instradamento persistente per la rete privata. L'instradamento persistente fornisce l'accesso all'intera rete privata. L'accesso include i server DNS nella configurazione della rete pubblica. **Nota:** alla rete privata non è assegnato alcun gateway.

* `Netsh interface IP set address name=”Local Area Connection” static 10.0.0.2 255.255.255.248`
* `Route add 10.0.0.0 mask 255.0.0.0 10.0.0.1 -p`

## Windows 2003 Standard ed Enterprise
{: #windows-2003-standard-and-enterprise}

Puoi utilizzare Windows 2003 per configurare la rete utilizzando la riga di comando o l'interfaccia grafica utente (GUI, graphical user interface). Se vuoi utilizzare la riga di comando, vedi le istruzioni di configurazione per Windows 2008 Server Core. Queste istruzioni funzionano anche per Windows 2003. Le istruzioni per utilizzare l'interfaccia grafica sono fornite qui. Gli indirizzi IP utilizzati in questo esempio devono essere sostituiti con gli indirizzi IP dai tuoi blocchi di IP. 

**Blocco di IP privati di esempio – 10.0.0.0/29**

* Indirizzo IP – 10.0.0.2
* Gateway – 10.0.0.1
* Maschera di sottorete – 255.255.255.248

### Apertura delle tue configurazioni di rete
{: #opening-your-network-configurations}

1. Vai al **menu Start > Impostazioni > Pannello di controllo**
2. Apri **Connessioni di rete**
   **Nota:** se hai installato due adattatori (schede) di rete, vedi “Connessione alla rete locale (LAN)” e “Connessione alla rete locale 2”

**Rete pubblica**

Utilizza le seguenti istruzioni per configurare la rete pubblica tramite l'interfaccia grafica Windows.

1. Fai clic con il pulsante destro del mouse su **Connessione alla rete locale (LAN) > Proprietà** e seleziona **Protocollo Internet (TCP/IP) > Proprietà**.
2. Vai a **Generale > Utilizza il seguente indirizzo IP:**
3. Immetti il tuo indirizzo IP pubblico, la tua maschera di sottorete e il tuo gateway.
4. Seleziona **Utilizza i seguenti indirizzi server DNS:**. Se stai configurando la rete privata, utilizza i seguenti server DNS. Se non stai configurando la rete privata, devi fornire i server DNS.
    * Server DNS preferito: 10.0.80.11
    * Server DNS alternativo: 10.0.80.12
5. Fai clic su **OK**

**Rete privata**

1. Fai clic con il pulsante destro del mouse su **Connessione alla rete locale 2 > Proprietà** e seleziona **Protocollo Internet (TCP/IP) > Proprietà**.
2. Vai a **Generale > Utilizza il seguente indirizzo IP:** e immetti il tuo indirizzo IP privato e la tua maschera di sottorete. Lascia il campo del gateway vuoto e fai clic su **OK**
3. Fai di nuovo clic su **OK** per chiudere la finestra di configurazione della rete.

Per fornire accesso all'intera rete privata che include i server DNS, devi aggiungere un instradamento personalizzato al server. Attieniti alla seguente procedura per fornire l'accesso alla rete privata:

1. Vai a **Start > Esegui**, immetti *cmd* e premi **OK**.
2. Esegui questo comando: **Nota:** assicurati di sostituire l'indirizzo gateway (10.0.0.1) con il tuo gateway del blocco di IP privati.<br/>
*“route add 10.0.0.0 mask 255.0.0.0 10.0.0.1 –p”*

## Windows 2008 Web, Standard, Enterprise e Datacenter
{: #windows-2008-web-standard-enterprise-and-datacenter}

Se hai Windows 2008, configuri la rete utilizzando il prompt dei comandi o l'interfaccia grafica utente (GUI, graphical user interface). Se intendi utilizzare il prompt dei comandi, fai riferimento alle istruzioni di configurazione per Windows 2008 Server Core. Queste istruzioni funzionano per tutti i server 2008. Le istruzioni per utilizzare l'interfaccia grafica sono fornite qui. Gli indirizzi IP utilizzati in questo esempio devono essere sostituiti con gli indirizzi IP dai tuoi blocchi di IP. 

**Blocco di IP privati di esempio – 10.0.0.0/29**

* Indirizzo IP – 10.0.0.2
* Gateway – 10.0.0.1
* Maschera di sottorete – 255.255.255.248

**Apri le tue configurazioni di rete**

1. Vai al **menu Start > Impostazioni > Pannello di controllo**
2. Apri **Centro connessioni di rete e condivisione** e fai clic su **Gestisci connessioni di rete**
* Se hai installato due adattatori (schede) di rete, vedi “Connessione alla rete locale (LAN)” e “Connessione alla rete locale 2”.

**Rete pubblica**

Le seguenti istruzioni forniscono i passi dettagliati relativi alla configurazione della rete pubblica tramite l'interfaccia grafica Windows.

1. Fai clic con il pulsante destro del mouse su **Connessione alla rete locale (LAN) > Proprietà**.
2. Seleziona **Protocollo Internet versione 4 (TCP/IPv4) > Proprietà**.
3. Vai a **Generale > Utilizza il seguente indirizzo IP:** e immetti l'indirizzo IP pubblico, la maschera di sottorete e il gateway.
4. Seleziona **Utilizza i seguenti indirizzi server DNS:**. Se stai configurando la rete privata, utilizza i seguenti server DNS. Se non stai configurando la rete privata, devi fornire i server DNS.
    * Server DNS preferito: 10.0.80.11
    * Server DNS alternativo: 10.0.80.12
5. Fai clic su **OK**

**Rete privata**

1. Fai clic con il pulsante destro del mouse su **Connessione alla rete locale 2 > Proprietà** e seleziona **Protocollo Internet versione 4 (TCP/IPv4) > Proprietà**.
2. Vai a **Generale > Utilizza il seguente indirizzo IP:**, immetti il tuo indirizzo IP privato e la maschera di sottorete e fai clic su **OK**.
3. Fai di nuovo clic su **OK** per chiudere la finestra di configurazione della rete.

Per fornire accesso all'intera rete privata che include i server DNS, devi aggiungere un instradamento personalizzato al server. 

1. Vai a **Start > Esegui**, immetti “cmd” e fai clic su **OK**
2. Esegui questo comando: **Nota:** sostituisci l'indirizzo gateway (_10.0.0.1_) con il tuo gateway del blocco di IP privati.
  * `route add 10.0.0.0 mask 255.0.0.0 10.0.0.1 –p`

## RedHat, Fedora e CentOS
{: #redhat-fedora-and-centos}

La configurazione della rete su RedHat, Fedora e CentOS viene eseguita modificando manualmente i file di configurazione. Per modificare manualmente i file, devi eseguire l'accesso alla macchina virtuale.

**Blocco di IP pubblici di esempio – 192.0.2.0/29**

* Indirizzo IP – 192.0.2.2
* Gateway – 192.0.2.1
* Maschera di sottorete – 255.255.255.248

**Blocco di IP privati di esempio – 10.0.0.0/29**

* Indirizzo IP – 10.0.0.2
* Gateway – 10.0.0.1
* Maschera di sottorete – 255.255.255.248

**Rete pubblica**

Le impostazioni di rete pubblica sono contenute nel seguente file. Devi modificare questo file con le informazioni fornite. Sostituisci gli indirizzi IP di esempio con gli indirizzi IP dal tuo blocco di IP pubblici. Se il file non esiste, crealo. Se esiste, sostituisci tutti i dati presenti nel file con le seguenti informazioni:
* /etc/sysconfig/network-scripts/ ifcfg-eth1
      DEVICE=eth1
      BOOTPROTO=static
      BROADCAST=192.0.2.7
      IPADDR=192.0.2.2
      NETMASK=255.255.255.248
      NETWORK=192.0.2.0

      GATEWAY=192.0.2.1
      ONBOOT=yes

**Rete privata**

Le impostazioni di rete privata sono contenute nel seguente file. Devi modificare questo file con le informazioni fornite. Sostituisci gli indirizzi IP di esempio con gli indirizzi IP corretti dal tuo blocco di IP privati. Se il file non esiste, crealo. Se il file esiste, sostituisci tutti i dati al suo interno con le seguenti informazioni: **Nota:** la rete privata *NON* avrà un instradamento privato e, pertanto, il *GATEWAY* non è definito.

* /etc/sysconfig/network-scripts/ ifcfg-eth0
      DEVICE=eth0
      BOOTPROTO=static
      IPADDR=10.0.0.2
      NETMASK=255.255.255.248
      ONBOOT=yes

Per finire, è necessario un nuovo instradamento per fornire l'accesso privato all'intera rete privata per includere il server DNS; tale operazione viene eseguita modificando il seguente file. Questo file non esiste, quindi deve essere creato. **NOTA:** sostituisci "10.0.0.1" nell'esempio con il tuo gateway d IP di sottorete privato.

* /etc/sysconfig/network-scripts/route-eth0
      10.0.0.0/8 via 10.0.0.1

**Configurazione DNS**

Le configurazioni di DNS primario e secondario sono contenute in un file separato. Devi modificare il file con le seguenti informazioni. Se questa macchina virtuale non ha accesso alla rete privata, devi sostituire gli IP server con gli indirizzi IP dei server DNS che desideri utilizzare.

* /etc/resolv.conf
      nameserver 10.0.80.11
      nameserver 10.0.80.12

Per rendere effettive queste modifiche, devi riavviare la rete sul server. Puoi riavviare la rete eseguendo il seguente comando:

* `service network restart`

## Ubuntu e Debian
{: #ubuntu-and-debian}

Configuri le reti Ubuntu e Debian tramite un singolo file di configurazione. Devi modificare questo file di configurazione con le seguenti informazioni. Per modificare questo file, devi disporre dell'accesso root al server. L'installazione di base di Ubuntu non fornisce un accesso root. Tuttavia, l'utente che è stato creato nel processo di installazione può accedere a sudo. Devi utilizzare il comando sudo per modificare il file, se stai eseguendo Ubuntu.

**Blocco di IP pubblici di esempio – 192.0.2.0/29**

* Indirizzo IP – 192.0.2.2
* Gateway – 192.0.2.1
* Maschera di sottorete – 255.255.255.248

**Blocco di IP privati di esempio – 10.0.0.0/29**

·Indirizzo IP – 10.0.0.2
·Gateway – 10.0.0.1
·Maschera di sottorete – 255.255.255.248

**Rete pubblica e privata**

Modifica il seguente file con qualsiasi editor di testo e sostituisci gli indirizzi IP di esempio con gli IP che si trovano nei tuoi blocchi di IP pubblici e privati.

* /etc/network/interfaces
      auto lo
      iface lo inet loopback

      auto eth1
      iface eth1 inet static
      address 192.0.2.2
      netmask 255.255.255.248
      gateway 192.0.2.1

      auto eth0
      iface eth0 inet static
      address 10.0.0.2
      netmask 255.255.255.248

      #Static route for backend service network
      up route add -net 10.0.0.0/8 gw 10.0.0.1

**Configurazione DNS**

La configurazione di DNS primario e secondario è contenuta in un file separato. Devi modificare questo file di configurazione con le seguenti informazioni. Se questa macchina virtuale non ha accesso alla rete privata, devi sostituire gli IP server con gli indirizzi IP dei server DNS che desideri utilizzare.

* /etc/resolv.conf
      nameserver 10.0.80.11
      nameserver 10.0.80.12

Per rendere effettive queste modifiche, procedi al riavvio della rete sul server. Puoi riavviare la rete eseguendo questo comando:

* */etc/init.d/network restart*

**Nota:** 192.0.2.0/24 viene utilizzato come documentazione IP pubblico per ogni RFC1166 e RFC5737. Non utilizzi questi indirizzi IP sui tuoi server.
