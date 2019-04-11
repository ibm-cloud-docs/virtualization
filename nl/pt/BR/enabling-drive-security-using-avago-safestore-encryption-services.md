---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: Avago SafeStore, encryption
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Ativando a segurança da unidade usando o Avago SafeStore Encryption Services
{: #enabling-drive-security-by-using-avago-safestore-encryption-services}

A configuração da segurança da unidade ajuda a evitar o acesso a dados armazenados em discos removidos sem uma chave de segurança. Os dados da unidade não podem ser recuperados sem essa chave. O {{site.data.keyword.BluSoftlayer_full}} fornece Self-Encrypting Drives (SED) em data centers selecionados para as unidades que podem ser compradas em um servidor Bare metal. As unidades SATA de 10 TB estão disponíveis em nossos data centers dos EUA.

## Pré-requisitos
{: #prerequisites-enabling-drive-security-by-using-avago-safestore-encryption-services}

* Bare metal Server com unidades SED - SATA de 10 TB
* LSI/AVAGO MegaRAID SAS 9361 -8i ou placas similares à LSI/AVAGO RAID
* Software Mega RAID Storage Manager instalado

## Ativando a segurança da unidade usando o MegaRAID Storage Manager (MSM)
{: #enabling-drive-security-by-using-megaraid-storage-manager-msm-}

É possível configurar a chave de segurança e os dados de segurança nela usando o MegaRAID Storage Manager. Também é possível usar a interface do WebBIOS que requer uma no momento do início do servidor para inserir o BIOS da placa MegaRAID para definir a configuração de segurança da unidade. Para obter mais informações sobre a placa controladora SAS 9361-8i do MegaRAID, consulte o site da Broadcom, [MegaRAID SAS 9361-8i ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](https://www.broadcom.com/products/storage/raid-controllers/megaraid-sas-9361-8i#documentation).

### Identificando unidades SED pré-instaladas
{: #identifying-preinstalled-sed-drives}

O MegaRAID Storage Manager vem pré-instalado na maioria dos sistemas operacionais suportados. Se ele não estiver presente, será possível instalá-lo manualmente por meio do site da Broadcom. Para obter mais informações, consulte [Downloads do MegaRAID SAS 9361-8i](https://www.broadcom.com/products/storage/raid-controllers/megaraid-sas-9361-8i#downloads).

É possível abrir o MegaRAID Storage Manager usando as credenciais do sistema. No exemplo, uma máquina Windows é usada e o MSM é pré-instalado.

Ao iniciar o MSM, deve-se inserir **Nome do usuário** e **Senha** que é o usuário privilegiado (Administrador) e a senha.

<!--![Figure 1](images/1_adapter_login.jpg)-->

Clique na guia **Físico**. Em seguida, clique nas unidades que estão disponíveis no sistema. A área de janela **Propriedades** tem as **Propriedades de segurança da unidade** que mencionam o campo **Capacidade de criptografia total de disco**, que mostra **Sim**. No exemplo usado, dois discos não SED e quatro discos SED são usados.

<!--![Figure 2](images/1_fde_capable_drives.jpg)-->

### Ativando a segurança da unidade no controlador
{: #enabling-drive-security-at-the-controller}

1. Para ativar a segurança da unidade, clique com o botão direito no **Controlador 0 :AVAGO MegaRAID SAS 9361-8i** na guia **Físico** e selecione **Ativar segurança da unidade**.
  * Agora é possível inserir o **Identificador de chave de segurança** e a **Chave de segurança**. Se você tiver múltiplas chaves de segurança, um identificador de chave de segurança poderá ajudar a identificar qual chave de segurança usar. Deve-se registrar a chave de segurança em uma localização segura. A chave de segurança é necessária quando você reconfigura unidades, como ao remover ou inserir novamente uma unidade. Sem a chave de segurança, não é possível recuperar nenhum dado que esteja armazenado em um volume criado fora dos SEDs. Não é possível recuperar uma chave de segurança esquecida. Uma senha de horário de início também pode ser configurada, que contém uma pausa do sistema para que uma senha configurada aqui seja inserida. A senha de horário de início é opcional e, se ela estiver configurada, você deverá efetuar login no IPMI e digitar a senha de início sempre que o sistema for reinicializado. Role para baixo e marque a caixa que diz **Eu gravei as configurações de segurança para referência futura** e clique em **Sim** para ativar a segurança da unidade.
  * Quando a segurança da unidade é ativada, uma imagem de chave amarela aparece para o **Controlador 0 AVAGO MegaRAID SAS 9361-8i**.
2. Agora crie um volume seguro usando as SEDs. É possível clicar com o botão direito em **Controller0** por meio da guia **Lógico** e selecionar  
**Criar unidade virtual**.
3. Escolha a opção **Avançado**. A tela precisa especificar o **Nível do RAID** e o **Método de segurança da unidade** como **Criptografia total de disco (FDE)**. Selecione as unidades FDE que são necessárias e clique em **Incluir** > **Criar grupo de unidades** > **Avançar**.
4. Revise as configurações da unidade virtual e faça qualquer mudança necessária. A configuração sugerida para a **Política de leitura** é **Sempre ler mais adiante**. A configuração sugerida para a **Política de gravação** é **Gravar novamente**. Clique em **Criar unidade virtual**. Aceite o impacto da política de Gravar novamente devido ao BBU clicando em **Sim**. Clique em **Avançar** e revise a tela de resumo. Clique em **Concluir**.
5. Para confirmar que o disco virtual está protegido, clique na guia **Lógico** e na unidade virtual que foi criada. Você vê nas **Propriedades de segurança da unidade** que o campo **Protegido** está marcado como **Sim**.

<!--![Figure 3](images/2_enable_drive_security.jpg)-->
<!--![Figure 4](images/3_security_key_details_page.jpg)-->
<!--![Figure 5](images/4_security_key_set_0.jpg)-->
<!--![Figure 6](images/9_create_vd_with_fde_drives.jpg)-->
<!--![Figure 7](images/10_create_vd_advanced_select_raid_drive_encryption_0.jpg)-->
<!--![Figure 8](images/create_vd_settings.jpg)-->
<!--![Figure 9](images/6_vd_secured_confirmation_0.jpg)-->

Se o servidor veio com volumes RAID que já estão criados usando unidades SED, é possível tornar o volume seguro seguindo estas etapas.

Na guia **Lógico**, clique com o botão direito em **Grupo de unidades** e selecione **Proteger usando FDE**. Se você tiver unidades FDE e não FDE combinadas para um volume, essa opção não estará visível.

<!--![Figure 10](images/5_secure_existing_vd_with_fde_drives_0.jpg)-->

Para remover a segurança da unidade, deve-se primeiro excluir os discos virtuais seguros e clicar com o botão direito no Controlador 0 para **Desativar a segurança da unidade**. Essa função apaga de forma segura os dados nele e remove a segurança da unidade.

Também é possível configurar a segurança da unidade usando o webBIOS e efetuando login por meio do IPMI no horário de início e inserindo o BIOS do RAID. Para obter mais informações, consulte **Avago SafeStore Encryption Services** no **Guia do usuário de software MegaRAID SAS de 12 Gb/s**.
