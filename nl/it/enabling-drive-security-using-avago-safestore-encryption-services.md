---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: Avago SafeStore, encryption
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Abilitazione della sicurezza dell'unità utilizzando Avago SafeStore Encryption Services
{: #enabling-drive-security-by-using-avago-safestore-encryption-services}

La configurazione della sicurezza dell'unità aiuta a impedire l'accesso ai dati archiviati sui dischi rimossi senza una chiave di sicurezza. Non è possibile recuperare i dati dell'unità senza questa chiave. {{site.data.keyword.cloud}} fornisce delle unità a crittografia automatica (SED, Self-Encrypting Drive) presso data center selezionati per le unità che possono essere acquistate su un server Bare metal. Le unità SATA da 10 TB sono disponibili nei nostri data center statunitensi.

## Prerequisiti
{: #prerequisites-enabling-drive-security-by-using-avago-safestore-encryption-services}

* Server Bare metal con unità SED – 10 TB SATA
* Schede MegaRAID LSI/AVAGO SAS 9361 -8i o schede RAID LSI/AVAGO simili
* Software MSM (MegaRAID Storage Manager)

## Abilitazione della sicurezza dell'unità utilizzando MSM (MegaRAID Storage Manager)
{: #enabling-drive-security-by-using-megaraid-storage-manager-msm-}

Puoi impostare la chiave di sicurezza e salvaguardare i dati al suo interno utilizzando MegaRAID Storage Manager. Puoi anche utilizzare l'interfaccia WebBIOS che ne richiede una all'avvio del server per accedere al BIOS della scheda MegaRAID per configurare l'impostazione di sicurezza dell'unità. Per ulteriori informazioni sulla scheda del controller MegaRAID 9361-8i, vedi il sito Broadcam, [MegaRAID SAS 9361-8i ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://www.broadcom.com/products/storage/raid-controllers/megaraid-sas-9361-8i#documentation).

### Identificazione delle unità SED preinstallate
{: #identifying-preinstalled-sed-drives}

MegaRAID Storage Manager viene preinstallato sulla maggior parte dei sistemi operativi supportati. Se non è presente, puoi installarlo manualmente dal sito Broadcom. Per ulteriori informazioni, vedi [MegaRAID SAS 9361-8i downloads](https://www.broadcom.com/products/storage/raid-controllers/megaraid-sas-9361-8i#downloads).

Puoi aprire MegaRAID Storage Manager utilizzando le credenziali di sistema. Nell'esempio, viene utilizzata una macchina Windows e MSM è preinstallato.

Quando avvii MSM, devi immettere nome utente (**User name**) e **Password** che è l'utente privilegiato (amministratore) e la password.

<!--![Figure 1](images/1_adapter_login.jpg)-->

Fai clic sulla scheda **Physical**. Fai quindi clic sulle unità disponibili sul sistema. Il riquadro **Properties** ha le proprietà di sicurezza dell'unità (**Drive Security Properties**) che include il campo **Full Disk Encryption capable**, che mostra **Yes**. Nell'esempio utilizzato, vengono utilizzati 2 dischi non SED e 4 dischi SED.

<!--![Figure 2](images/1_fde_capable_drives.jpg)-->

### Abilitazione della sicurezza dell'unità sul controller
{: #enabling-drive-security-at-the-controller}

1. Per abilitare la sicurezza dell'unità, fai clic con il pulsante destro del mouse su **Controller 0 :AVAGO MegaRAID SAS 9361-8i** dalla scheda **Physical** e seleziona **Enable Drive Security**.
  * Puoi ora immettere l'identificativo della chiave di sicurezza (**Security key identifier**) e la chiave di sicurezza (**Security key**). Se hai più chiavi di sicurezza, un identificativo di chiave di sicurezza può aiutarti a identificare quale chiave di sicurezza utilizzare. Devi registrare la chiave di sicurezza in un'ubicazione sicura. La chiave di sicurezza è necessaria quando riconfiguri le unità ad esempio rimuovendo o reinserendo un'unità. Senza la chiave di sicurezza, non è possibile richiamare i dati archiviati in un volume creato dalle SED. Non è possibile richiamare una chiave di sicurezza dimenticata. Può anche essere impostata una password di tempo d'inizio, che mantiene il sistema in pausa finché non viene immessa la password qui impostata. La password di tempo d'inizio è facoltativa e, se è impostata, devi eseguire l'accesso a IPMI e immettere la password di inizio ogni volta che il sistema viene riavviato. Scorri verso il basso e seleziona la casella che indica **I recorded the security settings for future reference** e fai clic su **Yes** per abilitare la sicurezza dell'unità.
  * Quando la sicurezza dell'unità è abilitata, viene visualizzata un'immagine di chiave gialla per **Controller 0 AVAGO MegaRAID SAS 9361-8i**.
2. Ora crea un volume protetto utilizzando le SED. Puoi fare clic con il pulsante destro del mouse su **Controller0** dalla scheda **Logical** e selezionare  
**Create Virtual Drive**.
3. Scegli l'opzione **Advanced**. La schermata deve specificare il livello RAID (**RAID level**) e il metodo di sicurezza dell'unità (**Drive security method**) come crittografia del disco completo (**Full Disk encryption (FDE)**). Seleziona le unità FDE richieste e fai clic su **Add** > **Create Drive Group** > **Next**.
4. Riesamina le impostazioni di unità virtuale e apporta le eventuali modifiche necessarie. L'impostazione consigliata per la politica di lettura (**Read Policy**) è **Always Read Ahead**. L'impostazione consigliata per la politica di scrittura (**Write policy**) è **Write Back**. Fai clic su **Create Virtual Drive**. Accetta l'impatto della politica Write Back dovuto alla BBU facendo clic su **Yes**. Fai clic su **Next** e riesamina la schermata di riepilogo. Fai clic su **Finish**.
5. Per confermare che il disco virtuale è protetto, fai clic sulla scheda **Logical** e sull'unità virtuale che è stata creata. Vedi nelle proprietà di sicurezza dell'unità (**Drive Security Properties**) che il campo **Secured** sia contrassegnato come **Yes**.

<!--![Figure 3](images/2_enable_drive_security.jpg)-->
<!--![Figure 4](images/3_security_key_details_page.jpg)-->
<!--![Figure 5](images/4_security_key_set_0.jpg)-->
<!--![Figure 6](images/9_create_vd_with_fde_drives.jpg)-->
<!--![Figure 7](images/10_create_vd_advanced_select_raid_drive_encryption_0.jpg)-->
<!--![Figure 8](images/create_vd_settings.jpg)-->
<!--![Figure 9](images/6_vd_secured_confirmation_0.jpg)-->

Se il server è stato fornito con volumi RAID già creati utilizzando delle unità SED, puoi rendere sicuro il volume attenendoti alla seguente procedura.

Nella scheda **Logical**, fai clic con il pulsante destro del mouse su **Drive Group** e seleziona **Secure Using FDE**. Se hai unità FDE e non FDE combinate, questa opzione non è visibile.

<!--![Figure 10](images/5_secure_existing_vd_with_fde_drives_0.jpg)-->

Per rimuovere la sicurezza dell'unità, devi prima eliminare i dischi virtuali protetti e fare clic con il pulsante destro del mouse su Controller 0 per disabilitare la sicurezza dell'unità (**Disable Drive Security**). Questa funzione elimina in modo sicuro i dati al suo interno e rimuove la sicurezza dell'unità.

Puoi anche configurare la sicurezza dell'unità utilizzando webBIOS ed eseguendo l'accesso tramite l'IPMI al momento dell'avvio e accedendo al BIOS RAID. Per ulteriori informazioni, vedi **Avago SafeStore Encryption Services** nel manuale **12 Gb/s MegaRAID SAS Software User Guide**.
