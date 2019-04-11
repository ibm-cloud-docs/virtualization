---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Installazione degli strumenti XenServer quando utilizzi Linux
{: #installing-xenserver-tools-when-using-linux}

Attieniti alla seguente procedura per installare gli strumenti XenServer sulle tue macchine virtuali che utilizzano i sistemi operativi CentOS, Debian e RedHat.

Questa procedura è specifica per gli XenServer gestiti dal cliente, non per le VSI fornite da {{site.data.keyword.cloud}}.
{:tip}

1. Apri XenCenter e stabilisci una connessione al tuo XenServer. Vedi l'esempio di una nuova macchina virtuale che non ha gli strumenti XenServer configurati. Puoi vedere che, per **"Virtualization State:"**, il software indica "Tools not installed".

2. Fai clic con il pulsante destro del mouse sul nome della macchina virtuale su cui desideri installare gli strumenti XenServer e fai quindi clic su **Install XenServer Tools**. Viene visualizzata un'avvertenza che indica che verranno espulsi tutti i CD o DVD attualmente presenti nell'unità CD della macchina virtuale quando verranno installati gli strumenti XenServer (Any CD or DVD currently in the virtual machine's CD drive ejected when xenServer Tools are installed).

3. Fai clic su **Install XenServer Tools** per continuare.

Sulla scheda della console di XenCenter per la tua macchina virtuale, vedi un'immagine CD di "xs-tools.iso" caricata nell'unità DVD. Ti fornisce anche il nome del dispositivo all'interno del sistema.

4. Puoi eseguire questa procedura servendoti della console o mediante una sessione SSH sul sistema. Seguendo questo esempio, l'installazione utilizza SSH. **Nota:** il nome del dispositivo di unità è fornito da XenCenter:

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

Se desideri copiare e incollare nella tua shell, le seguenti righe eseguono tutti i passi, se a te è assegnato lo stesso nome dispositivo di /dev/xvdd.

        mkdir /mnt/xs-tools
        mount /dev/xvdd /mnt/xs-tools
        cd /mnt/xs-tools/Linux/
        bash install.sh

In alcuni casi, l'ISO degli strumenti Xen è montata su /dev/cdrom.

        mkdir /mnt/xs-tools
        mount /dev/cdrom /mnt/xs-tools
        cd /mnt/xs-tools/Linux
        ./install.sh

5. Riavvia il tuo sistema dalla riga comandi; puoi quindi vedere che gli strumenti XenServer sono installati.

6. Ora XenCenter ha più controllo del sistema operativo all'interno della macchina virtuale e puoi visualizzare i contatori precedentemente non disponibili nella scheda **Performance** di XenCenter.
