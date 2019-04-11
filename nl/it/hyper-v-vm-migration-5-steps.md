---
copyright:
  years: 2014, 2018
lastupdated: "2018-06-22"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Migrazione di una macchina virtuale Hyper-V
{: #migrating-a-hyper-v-virtual-machine}

## Prima di iniziare
{: #before-you-begin-migrating-a-hyper-v-virtual-machine}

Prima di iniziare, controlla i seguenti prerequisiti.

* Un server compatibile (Windows 2008 Server DC Edition con Hyper-V).
* Un metodo di connessione dal server di origine al server di destinazione.
* Il server di origine e quello di destinazione devono risiedere sulla stessa VLAN. Se non risiedono sulla stessa VLAN, la riemissione di un IP per la macchina virtuale è necessaria (richiede l'apertura di un ticket di supporto).
* Delle macchine virtuali con uplink di rete funzionanti e con installato del software funzionante/supportato.

## Perché potresti aver bisogno di riposizionare la tua macchina virtuale Hyper-V
Ci sono due motivi comuni per cui potresti dover riposizionare la tua macchina virtuale Hyper-V:
* La VM si trova su hardware che non funziona correttamente,
* Il server host corrente sta esaurendo le risorse.
In entrambi gli eventi, la migrazione di Hyper-V può essere completata rapidamente; se hai i requisiti menzionati in precedenza, completa la seguente procedura:

1. Esegui l'accesso al server di origine e apri la Console di gestione di Hyper-V. Seleziona la macchina virtuale di cui desideri eseguire la migrazione al server di destinazione.
2. Arresta la macchina virtuale di cui desideri eseguire la migrazione. Seleziona quindi **Export** nell'elenco delle azioni del server e immetti l'ubicazione del file di esportazione.
3. Ora, utilizzando RDP sul server di origine, puoi eseguire l'accesso al server di destinazione con l'unità C: montata per trasferire il file.

Stai trasferendo il file tramite un montaggio di risorse utilizzando RDP. Puoi scegliere il metodo di trasferimento più comodo per te per questo processo (Windows Sharing, montaggio di risorse tramite RDP, FTP e altri metodi di trasferimento).{:tip}

4. Con il file in fase di esportazione al server di destinazione, assicurati che si trovi nell'ubicazione predefinita per i VHD (virtual hard disk) Hyper-V ed eseguine l'importazione. L'ubicazione predefinita è `C:\Users\Public\Documents\Hyper-V\Virtual hard disks`. Se hai modificato questa ubicazione predefinita e non sei sicuro di quale fosse, puoi selezionare **Hyper-V Settings > Import Virtual Machine** per visualizzare l'ubicazione.
5. Dopo che hai individuato il file esportato e hai fatto clic su **Import**, nella macchina virtuale nella tua Console di gestione di Hyper-V viene eseguita la copia di tutte le configurazioni e tutti i file che aveva precedentemente. Il server è ora in linea e funzionante. Se il tuo server non corrispondeva ai requisiti e il server di destinazione risiede in una VLAN differenti, devi riconfigurare l'IP della macchina virtuale con una sottorete portatile (instradata come Secondary on VLAN).
