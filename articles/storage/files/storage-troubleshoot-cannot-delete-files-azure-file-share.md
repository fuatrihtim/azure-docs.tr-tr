---
title: Azure dosya paylaşımı – Azure dosya paylaşımından dosyalar silinemedi
description: Azure dosya paylaşımından dosyaları silme başarısızlığını belirleyip sorun giderin.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/25/2019
ms.service: storage
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: 3d4f10745d90ccd83e7251af40d3e92a230f2fcd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94629691"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Azure dosya paylaşımı – Azure dosya paylaşımından dosyalar silinemedi

Azure dosya paylaşımından dosyaları silme hatası birkaç belirtime sahip olabilir:

**Belirti 1:**

Aşağıdaki iki sorunlardan biri nedeniyle Azure dosya paylaşımında bir dosya silinemedi:

* Silme için işaretlenmiş dosya
* Belirtilen kaynak bir SMB istemcisi tarafından kullanılıyor olabilir

**Belirti 2:**

Bu komutu işlemek için yeterli kota yok

## <a name="cause"></a>Nedeni

Dosya paylaşımının takılabileceği bilgisayarda bir dosya için izin verilen eş zamanlı açık tanıtıcıların üst sınırına ulaştığınızda hata 1816 oluşur. Daha fazla bilgi için bkz. [Azure depolama performansı ve ölçeklenebilirlik denetim listesi](../blobs/storage-performance-checklist.md).

## <a name="resolution"></a>Çözüm

Bazı tutamaçları kapatarak eşzamanlı açma tanıtıcılarının sayısını azaltın.

## <a name="prerequisite"></a>Önkoşul

### <a name="install-the-latest-azure-powershell-module"></a>En son Azure PowerShell modülünü yükler

* [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>Azure 'a Bağlan:

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>Hedef depolama hesabının aboneliğini seçin:

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>Hedef depolama hesabı için bağlam oluşturma:

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>Dosya paylaşımının geçerli açık tutamaçlarını al:

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>Örnek sonuç:

|Handleıd|Yol|ClientIp|Istemci bağlantı noktası|OpenTime|LastReconnectTime|File|ParentID|SessionId|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12:16:50Z|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12:36:20Z|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12:36:53Z|0|0|9507758546259807489|
|259101229136|Yeni klasör/test.zip|10.222.10.123|62758|2019-10-05|12:36:29Z|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|test.zip|37.222.22.143|62758|2019-10-05|12:36:24Z|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>Açık bir tutamacı kapat:

Açık bir tutamacı kapatmak için aşağıdaki komutu kullanın:

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>Sonraki adımlar

* [Windows 'da Azure dosyaları sorunlarını giderme](storage-troubleshoot-windows-file-connection-problems.md)
* [Linux 'ta Azure dosyaları sorunlarını giderme](storage-troubleshoot-linux-file-connection-problems.md)
* [Azure Dosya Eşitleme'de sorun giderme](storage-sync-files-troubleshoot.md)