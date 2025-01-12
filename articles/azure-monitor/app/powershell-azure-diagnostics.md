---
title: Azure’da Application Insights’ı kurmak için PowerShell’i kullanma | Microsoft Belgeleri
description: Verileri Application Insights için kanal oluşturma Azure Tanılama yapılandırmayı otomatikleştirin.
ms.topic: conceptual
ms.date: 08/06/2019
ms.openlocfilehash: c7c385888d7322b212cdd62497c9bbbabb970d65
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100583834"
---
# <a name="using-powershell-to-set-up-application-insights-for-azure-cloud-services"></a>Azure Cloud Services için Application Insights ayarlamak üzere PowerShell kullanma

[Microsoft Azure](https://azure.com), [Azure Application Insights](./app-insights-overview.md)'a [Azure Tanılama verileri gönderecek şekilde yapılandırılabilir.](../agents/diagnostics-extension-to-application-insights.md) Tanılama verileri Azure Cloud Services ve Azure VM’leriyle ilişkilidir. Uygulama içinde Application Insights SDK’sı kullanarak gönderdiğiniz telemetriyi tamamlar. Azure’da yeni kaynaklar oluşturma işlemini otomatikleştirmenin bir parçası olarak tanılamayı PowerShell kullanarak yapılandırabilirsiniz.

## <a name="azure-template"></a>Azure şablonu
web uygulaması Azure’deyse ve Azure Resource Manager şablonu kullanarak kaynaklarınızı oluşturuyorsanız, bunu kaynak düğümüne ekleyerek Application Insights’ı yapılandırabilirsiniz:

```json
{
  resources: [
    /* Create Application Insights resource */
    {
      "apiVersion": "2015-05-01",
      "type": "microsoft.insights/components",
      "name": "nameOfAIAppResource",
      "location": "centralus",
      "kind": "web",
      "properties": { "ApplicationId": "nameOfAIAppResource" },
      "dependsOn": [
        "[concat('Microsoft.Web/sites/', myWebAppName)]"
      ]
    }
  ]
}
``` 

* `nameOfAIAppResource` - Application Insights kaynağı adı
* `myWebAppName` -Web uygulamasının KIMLIĞI

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Bulut Hizmeti dağıtımının bir parçası olarak tanılama uzantısını etkinleştirme
`New-AzureDeployment` cmdlet’i, bir dizi tanılama yapılandırması içeren `ExtensionConfiguration` parametresine sahiptir. Bunlar, `New-AzureServiceDiagnosticsExtensionConfig` cmdlet’i kullanılarak oluşturulabilir. Örnek:

```azurepowershell
$service_package = "CloudService.cspkg"
$service_config = "ServiceConfiguration.Cloud.cscfg"
$diagnostics_storagename = "myservicediagnostics"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$primary_storagekey = (Get-AzStorageKey `
  -StorageAccountName "$diagnostics_storagename").Primary
$storage_context = New-AzStorageContext `
  -StorageAccountName $diagnostics_storagename `
  -StorageAccountKey $primary_storagekey

$webrole_diagconfig = `
  New-AzureServiceDiagnosticsExtensionConfig `
    -Role "WebRole" -Storage_context $storageContext `
    -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = `
  New-AzureServiceDiagnosticsExtensionConfig `
    -Role "WorkerRole" `
    -StorageContext $storage_context `
    -DiagnosticsConfigurationPath $workerrole_diagconfigpath

  New-AzureDeployment `
    -ServiceName $service_name `
    -Slot Production `
    -Package $service_package `
    -Configuration $service_config `
    -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)
``` 

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Mevcut bir Bulut Hizmetinde tanılama uzantısını etkinleştirme
Mevcut bir hizmet üzerinde `Set-AzureServiceDiagnosticsExtension` kullanma

```azurepowershell
$service_name = "MyService"
$diagnostics_storagename = "myservicediagnostics"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
$primary_storagekey = (Get-AzStorageKey `
  -StorageAccountName "$diagnostics_storagename").Primary
$storage_context = New-AzStorageContext `
  -StorageAccountName $diagnostics_storagename `
  -StorageAccountKey $primary_storagekey

Set-AzureServiceDiagnosticsExtension `
  -StorageContext $storage_context `
  -DiagnosticsConfigurationPath $webrole_diagconfigpath `
  -ServiceName $service_name `
  -Slot Production `
  -Role "WebRole" 
Set-AzureServiceDiagnosticsExtension `
  -StorageContext $storage_context `
  -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
  -ServiceName $service_name `
  -Slot Production `
  -Role "WorkerRole"
```

## <a name="get-current-diagnostics-extension-configuration"></a>Güncel tanılama uzantı yapılandırmasını alma

```azurepowershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## <a name="remove-diagnostics-extension"></a>Tanılama uzantısını kaldırma

```azurepowershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Tanılama uzantısını Rol parametresi olmadan `Set-AzureServiceDiagnosticsExtension` veya `New-AzureServiceDiagnosticsExtensionConfig` ile etkinleştirdiyseniz, uzantıyı Rol parametresi olmadan `Remove-AzureServiceDiagnosticsExtension` kullanarak kaldırabilirsiniz. Uzantı etkinleştirilirken Rol parametresi kullanıldıysa, uzantı kaldırılırken de kullanılması gerekir.

Tanılama uzantısını her bir rolden kaldırmak için:

```azurepowershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## <a name="see-also"></a>Ayrıca bkz.
* [Application Insights’la Azure Cloud Services uygulamalarını izleme](./cloudservices.md)
* [Azure Tanılama verilerini Application Insights’a gönderme](../agents/diagnostics-extension-to-application-insights.md)


