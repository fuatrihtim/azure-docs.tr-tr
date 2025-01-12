---
title: Yüksek yoğunluklu barındırma için uygulama başına ölçeklendirme
description: Uygulamaları App Service planlarından bağımsız olarak ölçeklendirin ve planınızdaki ölçeği en iyi hale getirin.
author: btardif
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: f1ca4958fe2608d0c040ef5b93827a7e71a4151c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "74672344"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Uygulama başına ölçeklendirmeyi kullanarak Azure App Service yüksek yoğunluklu barındırma

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

App Service kullanırken, üzerinde çalıştıkları [App Service planı](overview-hosting-plans.md) ölçeklendirerek uygulamalarınızı ölçeklendirebilirsiniz. Aynı App Service planında birden çok uygulama çalıştırıldığında, her ölçeklenen örnek plandaki tüm uygulamaları çalıştırır.

Uygulama *başına ölçeklendirme* , uygulamayı barındıran App Service planından bağımsız olarak bir uygulamanın ölçeklendirilmesine olanak tanımak için App Service plan düzeyinde etkinleştirilebilir. Bu şekilde, bir App Service planı 10 örneğe ölçeklendirilebilir, ancak bir uygulama yalnızca beş adet kullanılacak şekilde ayarlanabilir.

> [!NOTE]
> Uygulama başına ölçeklendirme yalnızca **Standart**, **Premium**, **Premium v2** ve **yalıtılmış** fiyatlandırma katmanları için kullanılabilir.
>

Uygulamalar, örnekler arasında eşit bir dağıtım için en iyi çaba yaklaşımını kullanarak kullanılabilir App Service planına ayrılır. Eşit bir dağıtım garanti edilmediği sürece, platform aynı uygulamanın iki örneğinin aynı App Service plan örneği üzerinde barındırılmaz olmasını sağlayacaktır.

Platform, çalışan ayırması için karar vermek üzere ölçümlere bağlı değildir. Uygulamalar, App Service planına yalnızca örnekler eklendiğinde veya kaldırıldığında yeniden dengelenebilir.

## <a name="per-app-scaling-using-powershell"></a>PowerShell kullanarak uygulama Ölçeklendirmesi başına

Parametresini cmdlet 'e geçirerek uygulama başına ölçeklendirmeyle bir plan oluşturun ```-PerSiteScaling $true``` ```New-AzAppServicePlan``` .

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Parametreyi cmdlet 'e geçirerek mevcut bir App Service planıyla uygulama başına ölçeklendirmeyi etkinleştirin `-PerSiteScaling $true` ```Set-AzAppServicePlan``` .

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

Uygulama düzeyinde, uygulamanın App Service planında kullanabileceği örnek sayısını yapılandırın.

Aşağıdaki örnekte, temel alınan App Service planının kaç örnek tarafından ölçeklendirdiğine bakılmaksızın uygulama iki örnekle sınırlandırılmıştır.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` , öğesinden farklıdır `$newapp.MaxNumberOfWorkers` . Uygulama başına ölçeklendirme `$newapp.SiteConfig.NumberOfWorkers` , uygulamanın ölçek özelliklerini belirlemede kullanır.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Azure Resource Manager kullanarak uygulama başına ölçeklendirme

Aşağıdaki Azure Resource Manager şablonu şunları oluşturur:

- 10 örneğe ölçeklenebilen bir App Service planı
- en fazla beş örnek için ölçeklendirilecek şekilde yapılandırılmış bir uygulama.

App Service planı, **Persiteölçeklendirme** özelliğini true olarak ayarlıyor `"perSiteScaling": true` . Uygulama, 5 ' e kadar kullanılacak **çalışan sayısını** ayarlıyor `"properties": { "numberOfWorkers": "5" }` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>Yüksek yoğunluklu barındırma için önerilen yapılandırma

Uygulama başına ölçeklendirme, hem genel Azure bölgelerinde hem de [App Service ortamlarda](environment/app-service-app-service-environment-intro.md)etkinleştirilen bir özelliktir. Ancak, önerilen strateji gelişmiş özelliklerden ve daha büyük App Service planı kapasitesinden yararlanmak için App Service ortamları kullanmaktır.  

Uygulamalarınız için yüksek yoğunluklu barındırma yapılandırmak için aşağıdaki adımları izleyin:

1. App Service planını yüksek yoğunluklu plan olarak belirleyin ve istediğiniz kapasiteye göre ölçeklendirin.
1. `PerSiteScaling`App Service planında bayrağı true olarak ayarlayın.
1. Yeni uygulamalar oluşturulur ve bu App Service, **numberofworker** özelliği **1** olarak ayarlanan plana atanır.
   - Bu yapılandırmanın kullanılması mümkün olan en yüksek yoğunluğu verir.
1. Çalışan sayısı, gerektiğinde ek kaynaklar sağlamak için her uygulama için bağımsız olarak yapılandırılabilir. Örnek:
   - Yüksek kullanım uygulaması, bu uygulama için daha fazla işlem kapasitesi sağlamak üzere **numberofworker** 'ları **3** olarak ayarlayabilir.
   - Az kullanılan uygulamalar, **numberofworker** 'ları **1** olarak ayarlar.

## <a name="next-steps"></a>Sonraki adımlar

- [Derinlemesine Azure App Service planlar genel bakış](overview-hosting-plans.md)
- [App Service Ortamı’na giriş](environment/app-service-app-service-environment-intro.md)
