---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-13"

subcollection: virtualization

keywords: Citrix XenServer 
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Attivazione di XenServer
{: #activating-xenserver}

La licenza gratuita di Citrix XenServer viene concessa per 30 giorni dall'installazione iniziale o dal ricaricamento. Durante tale periodo, devi eseguire la registrazione presso Citrix e richiedere una licenza di 1 anno pienamente funzionante. Per creare questa licenza, Citrix ha bisogno di informazioni personali a cui {{site.data.keyword.BluSoftlayer}} non ha accesso. La licenza è un accordo tra te e Citrix per l'utilizzo di XenServer.
{:shortdesc}

Se non esegui la registrazione del tuo sistema entro 30 giorni, non puoi accendere alcuna macchina virtuale.
{:tip}

Per registrare il tuo sistema, completa la seguente procedura:

1. Scarica e installa il client XenCenter. Puoi accedere al client dalla pagina [Citrix XenCenter ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](http://community.citrix.com/display/xs/XenCenter){: new_window}.

2. Stabilisci una connessione alla tua rete privata tramite VPN.

3. Avvia XenCenter e fai clic su **Add New Server** oppure fai clic su **Server > Add...** dal menu a discesa.

4. Utilizza l'indirizzo IP privato per **Host name**, 'root' per **User name** e la tua password root per **Password**.

5. Dopo che il sistema ha stabilito la connessione, viene visualizzata una finestra di dialogo. Questa finestra di dialogo ti avvisa che la tua licenza scade entro 30 giorni. Se non vedi una finestra di dialogo, dal menu a discesa vai a **Tools > License Manager...**.

6. Controlla il tuo server e fai clic su **Activate...**. Si apre un browser che va al sito di registrazione di Citrix. Completa il modulo e inoltralo. Controlla nella tua casella di posta in arrivo se hai ricevuto una email da Citrix. Questa email presenta come allegato la tua chiave di licenza.

7. Quando ricevi la email, salva in locale il file di chiave di licenza allegato.

8. Nel riquadro di sinistra di XenCenter, evidenzia il tuo server. Nella barra dei menu, vai a **Server > Install license key...**. Viene visualizzata una finestra di dialogo che chiede l'ubicazione della chiave di licenza. Indirizzala all'ubicazione in cui hai eseguito il salvataggio del passo 7.
