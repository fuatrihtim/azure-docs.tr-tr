---
title: Dağıtımlarla ilgili sorunları giderme
description: Azure Resource Manager şablonu (ARM şablonu) dağıtımlarını izlemeyi ve sorunlarını gidermeyi öğrenin. Etkinlik günlüklerini ve dağıtım geçmişini gösterir.
author: mumian
ms.date: 01/15/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 31c4e6383b5eaea2bb66dc1baafa0fbff4918a7c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97589126"
---
# <a name="tutorial-troubleshoot-arm-template-deployments"></a>Öğretici: ARM şablon dağıtımlarının sorunlarını giderme

Azure Resource Manager şablonu (ARM şablonu) dağıtım hatalarıyla ilgili sorunları nasıl giderebileceğinizi öğrenin. Bu öğreticide, bir şablonda iki hata ayarlarsınız ve sorunları çözmek için etkinlik günlüklerinin ve dağıtım geçmişinin nasıl kullanılacağını öğreneceksiniz.

Şablon dağıtımıyla ilgili iki tür hata vardır:

- Dağıtımdan önce belirlenebileceği senaryolardan **doğrulama hataları** oluşur. Bunlar şablonunuzdaki söz dizimi hataları veya abonelik kotalarınızı aşabilecek kaynak dağıtımı denemeleri olabilir.
- Dağıtım işlemi sırasında oluşan koşullardan **dağıtım hataları** oluşur. Bu paralel olarak dağıtılan bir kaynağa erişme denemesi olabilir.

Her iki tür hata da dağıtım sorunlarını gidermek için kullanabileceğiniz bir hata kodu döndürür. Her iki tür hata da etkinlik günlüğünde görüntülenir. Öte yandan doğrulama hataları dağıtım geçmişinizde görüntülenmez çünkü dağıtım hiç başlatılmamıştır.

Bu öğretici aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> - Sorunlu şablon oluşturma
> - Doğrulama hatalarında sorun giderme
> - Dağıtım hatalarını giderme
> - Kaynakları temizleme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlamak için gerekenler:

- Visual Studio Code ve Resource Manager Araçları uzantısı. Bkz. [hızlı başlangıç: VISUAL STUDIO Code ARM şablonları oluşturma](quickstart-create-templates-use-visual-studio-code.md).

## <a name="create-a-problematic-template"></a>Sorunlu şablon oluşturma

[Azure hızlı başlangıç şablonlarından](https://azure.microsoft.com/resources/templates/) [Standart depolama hesabı oluşturma](https://azure.microsoft.com/resources/templates/101-storage-account-create/) adlı bir şablon açın ve iki şablon sorunu kurun.

1. Visual Studio Code **Dosya**  >  **Aç dosya**' yı seçin.
2. **Dosya adı**’na şu URL’yi yapıştırın:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Dosyayı açmak için **Aç**’ı seçin.
4. `apiVersion`Satırı aşağıdaki satıra değiştirin:

    ```json
    "apiVersion1": "2018-07-02",
    ```

    - `apiVersion1` geçersiz bir öğe adı. Bu bir doğrulama hatasıdır.
    - API sürümü olmalıdır `"2018-07-01"` .  Bu bir dağıtım hatasıdır.

5. Dosyayı   >  yerel bilgisayarınızaazuredeploy.jsolarak kaydetmek için dosya **farklı kaydet** _'_ i seçin.

## <a name="troubleshoot-the-validation-error"></a>Doğrulama hatası sorunlarını giderme

Şablonu dağıtmak için [şablonu dağıtma](template-tutorial-create-multiple-instances.md#deploy-the-template) bölümüne bakın.

Kabuktan şuna benzer bir hata alacaksınız:

```azurepowershell
New-AzResourceGroupDeployment : 4:29:24 PM - Error: Code=InvalidRequestContent; Message=The request content was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 36, position 24.'.
```

Hata iletisi, sorun olduğunu gösterir `apiVersion1` .

Öğesini olarak değiştirerek sorunu düzeltmek için Visual Studio Code kullanın `apiVersion1` `apiVersion` ve şablonu kaydedin.

## <a name="troubleshoot-the-deployment-error"></a>Dağıtım hatası sorunlarını giderme

Şablonu dağıtmak için [şablonu dağıtma](template-tutorial-create-multiple-instances.md#deploy-the-template) bölümüne bakın.

Kabuktan şuna benzer bir hata alacaksınız:

```azurepowershell
New-AzResourceGroupDeployment : 4:48:50 PM - Resource Microsoft.Storage/storageAccounts 'storeqii7x2rce77dc' failed with message '{
  "error": {
    "code": "NoRegisteredProviderFound",
    "message": "No registered resource provider found for location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions are '2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'. The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast, japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast, australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth, francecentral'."
  }
}'
```

Dağıtım hatası, aşağıdaki yordam kullanılarak Azure portal bulunabilir:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Kaynak **grupları** ' nı ve ardından kaynak grubu adı ' nı seçerek kaynak grubunu açın. **Dağıtım** altında **1 başarısız** olduğunu göreceksiniz.

    ![Başarısız dağıtımı vurgulayan ekran görüntüsü.](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error.png)
3. **Hata ayrıntılarını** seçin.

    ![Hata ayrıntıları bağlantısını vurgulayan ekran görüntüsü.](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-details.png)

    Hata iletisi, daha önce gösterilenle aynıdır:

    ![Hata ayrıntılarını gösteren ekran görüntüsü.](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-summary.png)

Ayrıca, etkinlik günlüklerinden hatayı bulabilirsiniz:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. **İzleme**  >  **etkinlik günlüğü**' nü seçin.
3. Günlüğü bulmak için filtreleri kullanın.

    ![Kaynak Yöneticisi öğretici sorunlarını giderme](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-activity-log.png)

Sorunu düzeltmek için Visual Studio Code kullanın ve ardından şablonu yeniden dağıtın.

Yaygın hataların listesi için bkz. [Azure Resource Manager ile yaygın Azure dağıtım hatalarıyla Ilgili sorunları giderme](common-deployment-errors.md).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık Azure kaynakları gerekli değilse, kaynak grubunu silerek dağıttığınız kaynakları temizleyin.

1. Azure portal, sol menüden **kaynak grubu** ' nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin.
3. Kaynak grubu adını seçin.  Kaynak grubundaki toplam altı kaynak görüyor olmalısınız.
4. Üstteki menüden **kaynak grubunu sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, ARM şablonu dağıtım hatalarıyla ilgili sorunları nasıl giderebileceğiniz öğrendiniz.  Daha fazla bilgi için bkz. [Azure Resource Manager ile yaygın Azure dağıtım hatalarıyla Ilgili sorunları giderme](common-deployment-errors.md).
