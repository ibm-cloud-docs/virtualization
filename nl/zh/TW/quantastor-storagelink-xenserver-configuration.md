---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: QuantaStor
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# 安裝 QuantaStor StorageLink Xenserver
{: #installing-quantastor-storagelink-xenserver}

## 安裝 QuantaStor Adapter for Citrix StorageLink
{: #installing-the-quantastor-adapter-for-citrix-storagelink}

1. 確定已針對 StorageLink 適當地授權 XenServer 的安裝。

只有透過 Citrix 訂閱「企業」或「白金」授權的客戶才能使用 StorageLink 功能。具有 Citrix XenServer 之「進階」授權的客戶無法存取 StorageLink 功能。
{:tip}

2. 在管理者提示下輸入下列指令，以停止 StorageLink 服務。或者，您可以從 XenServer 中的「服務畫面」停止 StorageLink 服務。

        C:\> net stop storagelink

3. 下載並執行可從 [OSNexus ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](http://www.osnexus.com/trynow/){: new_window} 取得的 StorageLink 配接器。
4. 在服務停止之後，您可以執行下列指令來安裝 QuantaStor StorageLink 配接器：

        osn_quantastor_csladapter.exe

5. 將此 XML 區塊新增至位於以下位置的 StorageLink 配置結尾：
  * `C:\Program Files\Citrix\StorageLink\Server\scsi_device_id_config.xml`
  * `OSNEXUS`
  * `QUANTASTOR1`
  * 在此檔案的開頭，您會看到類似 _2.0.0.4._ 的版本號碼。請增加版本號碼，例如：2.0.0.5。版本號碼告訴 StorageLink，它需要使用這些新的裝置識別規則，來升級您的 XenServer 系統。
6. 重新啟動 Citrix StorageLink 服務：

        C:\> net start storagelink

## 驗證是否已適當地載入 StorageLink
{: #verifying-that-storagelink-loaded-properly}

1. 登入 Citrix StorageLink Manager、選取左側的「管理」樹狀結構，然後在 [QuantaStor ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](http://svn.osnexus.com/mediawiki/images/thumb/c/c8/Storagelink_admin.png/640px-Storagelink_admin.png){: new_window} 清單中尋找 OS NEXUS QuantaStor。
2. 新增儲存空間系統認證，讓 StorageLink 可以為 QuantaStor 儲存空間系統動態配置儲存空間磁區。

## 安裝 XenServer 多路徑配置設定
{: #installing-the-xenserver-multipath-configuration-settings}

XenServer 使用 Linux 裝置對映器多路徑驅動程式 (`dmmp`)，來啟用硬體多路徑的支援。`dmmp` 驅動程式使用一個稱為 `multipath.conf` 的配置檔，其中包含每一個供應商的多路徑模式及失效接手規則。

QuantaStor 具有一些需要新增至 `` 檔案的特殊規則。編輯位於每一個 XenServer `dom0` 節點上的 `/etc/multipath-enabled.conf` 檔案。在該檔案中，將下列段落新增至最後一個裝置 { } 區段之後：

    device {
          vendor                  "OSNEXUS"
          product                 "QUANTASTOR"
          getuid_callout          "/sbin/scsi_id -g -u -s /block/%n"
          path_selector           "round-robin 0"
          path_grouping_policy    multibus
          failback                immediate
          rr_weight               uniform
          rr_min_io               100
          path_checker            tur
    }
