---
title: Yeni bir güncelleştirmeyi içeri aktarma | Microsoft Docs
description: Yeni bir güncelleştirmeyi IoT Hub için IoT Hub cihaz güncelleştirmesine aktarma Kılavuzu How-To.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 3644f26f989fec05ee76afd9f930c31b25234c7f
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608536"
---
# <a name="import-new-update"></a>Yeni güncelleştirme al
IoT Hub için yeni bir güncelleştirmeyi cihaz güncelleştirmesine aktarmayı öğrenin. Henüz yapmadıysanız, temel [içeri aktarma kavramlarını öğrendiğinizden](import-concepts.md)emin olun.

## <a name="prerequisites"></a>Önkoşullar

* [IoT Hub etkinleştirilmiş cihaz güncelleştirmesiyle bir IoT Hub erişim](create-device-update-account.md). 
* IoT Hub içinde cihaz güncelleştirmesi için sağlanan bir IoT cihazı (veya simülatör).
   * Gerçek bir cihaz kullanıyorsanız, görüntü güncelleştirmesi için bir güncelleştirme görüntü dosyası veya paket güncelleştirmesi için [apt bildirim dosyası](device-update-apt-manifest.md) gerekir.
* [PowerShell 5](/powershell/scripting/install/installing-powershell) veya üzeri (Linux, MacOS ve Windows yüklemelerini içerir)
* Desteklenen tarayıcılar:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> Bu hizmete gönderilen bazı veriler, bu örneğin oluşturulduğu bölge dışında bir bölgede işlenebilir.

## <a name="create-device-update-import-manifest"></a>Cihaz güncelleştirme Içeri aktarma bildirimi oluştur

1. Güncelleştirme görüntü dosyanızın veya APT bildirim dosyasının PowerShell 'den erişilebilen bir dizinde bulunduğundan emin olun.

2. Güncelleştirme görüntü dosyanızın veya APT bildirim dosyasının bulunduğu dizinde **Aduupdate. psm1** adlı bir metin dosyası oluşturun. Daha sonra [Aduupdate. psm1](https://github.com/Azure/iot-hub-device-update/tree/main/tools/AduCmdlets) PowerShell cmdlet 'ini açın, içeriği metin dosyanıza kopyalayın ve metin dosyasını kaydedin.

3. PowerShell 'de adım 2 ' den PowerShell cmdlet 'ini oluşturduğunuz dizine gidin. Ardından şunu çalıştırın:

    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
    Import-Module .\AduUpdate.psm1
    ```

4. Örnek parametre değerlerini, güncelleştirmeyi açıklayan bir JSON dosyası olan içeri aktarma bildirimi oluşturacak şekilde değiştirerek aşağıdaki komutları çalıştırın:
    ```powershell
    $compat = New-AduUpdateCompatibility -DeviceManufacturer 'deviceManufacturer' -DeviceModel 'deviceModel'

    $importManifest = New-AduImportManifest -Provider 'updateProvider' -Name 'updateName' -Version 'updateVersion' `
                                            -UpdateType 'updateType' -InstalledCriteria 'installedCriteria' `
                                            -Compatibility $compat -Files 'updateFilePath(s)'

    $importManifest | Out-File '.\importManifest.json' -Encoding UTF8
    ```

    Hızlı başvuru için yukarıdaki parametrelere ilişkin bazı örnek değerler aşağıda verilmiştir. Ayrıca, daha fazla ayrıntı için tüm [içeri aktarma bildirimi şemasını](import-schema.md) görüntüleyebilirsiniz.

    | Parametre | Açıklama |
    | --------- | ----------- |
    | deviceManufacturer | Güncelleştirmenin, örneğin contoso ile uyumlu olduğu cihaz üreticisi. _Üretici_ [cihaz özelliği](./device-update-plug-and-play.md#device-properties)ile eşleşmelidir.
    | deviceModel | Güncelleştirmenin uyumlu olduğu cihazın modeli, örneğin, Toaster. _Model_ [cihaz özelliği](./device-update-plug-and-play.md#device-properties)ile eşleşmelidir.
    | updateProvider | Güncelleştirme tarafından oluşturulan veya doğrudan sorumlu olan varlık. Genellikle şirket adı olacaktır.
    | updateName | Bir güncelleştirme sınıfının tanımlayıcısı. Sınıf, seçtiğiniz herhangi bir şey olabilir. Genellikle bir cihaz veya model adı olacaktır.
    | updateVersion | Sürüm numarası, aynı sağlayıcıya ve ada sahip diğer başkalarından bu güncelleştirmeyi ayırt eder. , Cihazdaki bireysel Yazılım bileşenlerinden oluşan bir sürümle eşleşmiyor (ancak seçeneğini belirlerseniz).
    | Güncelleştirme türü | <ul><li>`microsoft/swupdate:1`Görüntü güncelleştirmesi için belirt</li><li>`microsoft/apt:1`Paket güncelleştirmesi için belirtin</li></ul>
    | ınstalınstalbu ölçüt | <ul><li>Güncelleştirme türü için SWVersion değerini belirtin `microsoft/swupdate:1`</li><li>Güncelleştirme türü için önerilen değeri belirtin `microsoft/apt:1` .
    | updateFilePath | Bilgisayarınızdaki güncelleştirme dosyalarının yolu


## <a name="review-generated-import-manifest"></a>Oluşturulan Içeri aktarma bildirimini gözden geçirin

Örnek:
```json
{
  "updateId": {
    "provider": "Microsoft",
    "name": "Toaster",
    "version": "2.0"
  },
  "updateType": "microsoft/swupdate:1",
  "installedCriteria": "5",
  "compatibility": [
    {
      "deviceManufacturer": "Fabrikam",
      "deviceModel": "Toaster"
    },
    {
      "deviceManufacturer": "Contoso",
      "deviceModel": "Toaster"
    }
  ],
  "files": [
    {
      "filename": "file1.json",
      "sizeInBytes": 7,
      "hashes": {
        "sha256": "K2mn97qWmKSaSaM9SFdhC0QIEJ/wluXV7CoTlM8zMUo="
      }
    },
    {
      "filename": "file2.zip",
      "sizeInBytes": 11,
      "hashes": {
        "sha256": "gbG9pxCr9RMH2Pv57vBxKjm89uhUstD06wvQSioLMgU="
      }
    }
  ],
  "createdDateTime": "2020-10-08T03:32:52.477Z",
  "manifestVersion": "2.0"
}
```

## <a name="import-update"></a>Güncelleştirmeyi içeri aktar

[!NOTE]
Aşağıdaki yönergelerde Azure portal Kullanıcı arabirimi aracılığıyla bir güncelleştirmenin nasıl içeri aktarılacağı gösterilmektedir. Ayrıca, bir güncelleştirmeyi içeri aktarmak için [IoT Hub API 'leri Için cihaz güncelleştirmesini](https://github.com/Azure/iot-hub-device-update/tree/main/docs/publish-api-reference) de kullanabilirsiniz. 

1. [Azure Portal](https://portal.azure.com) oturum açın ve cihaz güncelleştirmesiyle IoT Hub gidin.

2. Sayfanın sol tarafında, "otomatik cihaz yönetimi" altında "cihaz güncelleştirmeleri" ni seçin.

   :::image type="content" source="media/import-update/import-updates-3.png" alt-text="Güncelleştirmeleri içeri aktar" lightbox="media/import-update/import-updates-3.png":::

3. Ekranın üst kısmında birkaç sekme görürsünüz. Güncelleştirmeler sekmesini seçin.

   :::image type="content" source="media/import-update/updates-tab.png" alt-text="Güncelleştirmeler" lightbox="media/import-update/updates-tab.png":::

4. "Dağıtmaya hazırlanma" üstbilgisinin altında "+ yeni güncelleştirme al" seçeneğini belirleyin.

   :::image type="content" source="media/import-update/import-new-update-2.png" alt-text="Yeni güncelleştirme al" lightbox="media/import-update/import-new-update-2.png":::

5. "Içeri aktarma bildirim dosyası seçin" altında klasör simgesini veya metin kutusunu seçin. Bir dosya Seçicisi iletişim kutusu görürsünüz. PowerShell cmdlet 'ini kullanarak daha önce oluşturduğunuz Içeri aktarma bildirimini seçin. Sonra, "bir veya daha fazla güncelleştirme dosyası seçin" altında klasör simgesini veya metin kutusunu seçin. Bir dosya Seçicisi iletişim kutusu görürsünüz. Güncelleştirme dosyanızı seçin.

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Güncelleştirme dosyalarını seçin" lightbox="media/import-update/select-update-files.png":::

6. "Bir depolama kapsayıcısı seçin" altında klasör simgesini veya metin kutusunu seçin. Ardından uygun depolama hesabını seçin. Depolama kapsayıcısı güncelleştirme dosyalarını geçici olarak hazırlamak için kullanılır.

   :::image type="content" source="media/import-update/storage-account.png" alt-text="Depolama hesabı" lightbox="media/import-update/storage-account.png":::

7. Zaten bir kapsayıcı oluşturduysanız, onu yeniden kullanabilirsiniz. (Aksi takdirde, güncelleştirmeler için yeni bir depolama kapsayıcısı oluşturmak üzere "+ kapsayıcı" seçeneğini belirleyin.).  Kullanmak istediğiniz kapsayıcıyı seçin ve "Seç" e tıklayın.

   :::image type="content" source="media/import-update/container.png" alt-text="Kapsayıcı Seç" lightbox="media/import-update/container.png":::

8. İçeri aktarma işlemini başlatmak için "Gönder" i seçin.

   :::image type="content" source="media/import-update/publish-update.png" alt-text="Güncelleştirme Yayımla" lightbox="media/import-update/publish-update.png":::

9. İçeri aktarma işlemi başlar ve ekran "Içeri aktarma geçmişi" bölümüne geçer. İçeri aktarma işlemi tamamlanana kadar ilerlemeyi görüntülemek için "Yenile" seçeneğini belirleyin (güncelleştirmenin boyutuna bağlı olarak, bu işlem birkaç dakika sürebilir, ancak daha uzun sürebilir).

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Içeri aktarma sıralamasını Güncelleştir" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Durum sütunu içeri aktarma işleminin başarılı olduğunu gösteriyorsa, "dağıtıma hazırlanıyor" üst bilgisini seçin. İçeri aktarılan güncelleştirmenizi şimdi listede görmeniz gerekir.

   :::image type="content" source="media/import-update/update-ready.png" alt-text="İş Durumu" lightbox="media/import-update/update-ready.png":::

## <a name="next-steps"></a>Sonraki Adımlar

[Grup Oluştur](create-update-group.md)

[İçeri aktarma kavramları hakkında bilgi edinin](import-concepts.md)