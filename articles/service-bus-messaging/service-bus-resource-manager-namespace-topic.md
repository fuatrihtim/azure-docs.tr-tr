---
title: Şablon kullanarak Azure Service Bus ad alanı konusu oluşturma
description: 'Hızlı başlangıç: Azure Resource Manager şablonu kullanarak konuyla ve abonelikle Service Bus bir ad alanı oluşturma'
documentationcenter: .net
author: spelluru
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 706432c039bc38e9799828ae752d6ad0c92ffde3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97704614"
---
# <a name="quickstart-create-a-service-bus-namespace-with-topic-and-subscription-using-an-azure-resource-manager-template"></a>Hızlı başlangıç: Azure Resource Manager şablonu kullanarak konuyla ve abonelikle Service Bus bir ad alanı oluşturma

Bu makalede, bu ad alanı içinde bir Service Bus ad alanı ve bir konu ve abonelik oluşturan Azure Resource Manager şablonunun nasıl kullanılacağı gösterilmektedir. Makalesinde hangi kaynakların dağıtıldığını ve dağıtım yürütüldüğünde belirtilen parametrelerin nasıl tanımlanacağı açıklanmaktadır. Bu şablonu kendi dağıtımlarınız için kullanabilir veya kendi gereksinimlerinize göre özelleştirebilirsiniz

Şablon oluşturma hakkında daha fazla bilgi için bkz. [Azure Resource Manager şablonları yazma][Authoring Azure Resource Manager templates].

Tüm şablon için, [konu başlığı ve abonelik şablonuyla Service Bus ad alanı][Service Bus namespace with topic and subscription] ' na bakın.

> [!NOTE]
> Aşağıdaki Azure Resource Manager şablonları indirme ve dağıtım için kullanılabilir.
> 
> * [Service Bus ad alanı oluşturma](service-bus-resource-manager-namespace.md)
> * [Sıraya sahip bir Service Bus ad alanı oluşturma](service-bus-resource-manager-namespace-queue.md)
> * [Kuyruk ve yetkilendirme kuralıyla Service Bus ad alanı oluşturma](service-bus-resource-manager-namespace-auth-rule.md)
> * [Konu, abonelik ve kuralla Service Bus ad alanı oluşturma](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> En son şablonları denetlemek için [Azure hızlı başlangıç şablonları][Azure Quickstart Templates] Galerisi ' ni ziyaret edin ve **Service Bus** arayın.

## <a name="what-do-you-deploy"></a>Ne dağıtırsınız?

Bu şablonla, konu ve abonelikle bir Service Bus ad alanı dağıtırsınız.

[Service Bus konular ve abonelikler](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) , *Yayımla/abone ol* düzeninde bire çok bir iletişim biçimi sağlar.

Dağıtımı otomatik olarak çalıştırmak için aşağıdaki düğmeye tıklayın:

[![Azure’a dağıtın](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-and-subscription%2Fazuredeploy.json)

## <a name="parameters"></a>Parametreler

Azure Resource Manager sayesinde, şablon dağıtıldığında belirtmek istediğiniz değerlerin parametrelerini siz tanımlarsınız. Şablon, `Parameters` tüm parametre değerlerini içeren adlı bir bölümü içerir. Dağıttığınız projeye göre veya dağıttığınız ortama göre değişen değerler için bir parametre tanımlayın. Her zaman aynı kalan değerler için parametre tanımlamayın. Her parametre değeri, dağıtılan kaynakları tanımlamak için şablonda kullanılır.

Şablon aşağıdaki parametreleri tanımlar:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

Oluşturulacak Service Bus ad alanının adı.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName

Service Bus ad alanında oluşturulan konunun adı.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName

Service Bus ad alanında oluşturulan aboneliğin adı.

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion

Şablonun Service Bus API sürümü.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```

## <a name="resources-to-deploy"></a>Dağıtılacak kaynaklar

**İleti** türünde, konu ve abonelikle bir standart Service Bus ad alanı oluşturur.

```json
"resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "Standard",
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]",
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {}
            }]
        }]
    }]
```

JSON sözdizimi ve özellikleri için bkz. [ad alanları](/azure/templates/microsoft.servicebus/namespaces), [konular](/azure/templates/microsoft.servicebus/namespaces/topics)ve [abonelikler](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions).

## <a name="commands-to-run-deployment"></a>Dağıtımı çalıştırma komutları

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az deployment group create \<my-resource-group\> --name \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="next-steps"></a>Sonraki adımlar

Artık Azure Resource Manager kullanarak kaynak oluşturup dağıttığınıza göre, şu makaleleri görüntüleyerek bu kaynakları yönetmeyi öğrenin:

* [Service Bus’ı PowerShell ile yönetme](service-bus-manage-with-ps.md)
* [Service Bus Gezgini ile Service Bus kaynaklarını yönetme](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md
[Service Bus namespace with topic and subscription]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-and-subscription/
