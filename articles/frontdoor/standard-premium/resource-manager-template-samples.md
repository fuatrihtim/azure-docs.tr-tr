---
title: Kaynak Yöneticisi şablonu örnekleri-Azure ön kapısı
description: Azure ön kapısı için sunulan örnek Azure Resource Manager şablonları hakkında bilgiler.
services: frontdoor
author: johndowns
ms.author: jodowns
ms.service: frontdoor
ms.topic: sample
ms.date: 03/24/2021
ms.openlocfilehash: 929adb0be948339af033d85b0dabd7e1cedf353e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561755"
---
# <a name="azure-resource-manager-templates-for-azure-front-door"></a>Azure ön kapısına yönelik Azure Resource Manager şablonları

> [!Note]
> Bu belge, Azure ön kapısının Standart/Premium (Önizleme) içindir. Azure ön kapısı hakkında bilgi mi arıyorsunuz? [Burada](../front-door-overview.md)görüntüleyin.

Aşağıdaki tabloda, diğer Azure hizmetleri dahil olmak üzere başvuru mimarilerine sahip Azure ön kapısının Azure Resource Manager şablonlarının bağlantıları yer almaktadır.

| Örnek | Açıklama |
|-|-|
| [Kural kümesi](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rule-set/) | Ön kapı profili ve kural kümesi oluşturur.  |
|**App Service kaynakları**| **Açıklama** |
| [App Service](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-app-service-public) | Ortak uç nokta ve bir ön kapı profili olan App Service bir uygulama oluşturur.  |
| [Özel bağlantıyla birlikte App Service](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-private-link) | Özel bir uç nokta ve bir ön kapı profili olan App Service bir uygulama oluşturur.  |
| [Özel bağlantıyla App Service ortam](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-environment-internal-private-link) | Bir App Service ortamı, özel bir uç noktası olan bir uygulama ve bir ön kapı profili oluşturur.  |
|**Azure Işlevleri kaynakları**| **Açıklama** |
| [Azure İşlevleri](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-function-public/) | Genel bir uç nokta ve bir ön kapı profili içeren bir Azure Işlevleri uygulaması oluşturur.  |
| [Özel bağlantısı olan Azure Işlevleri](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-function-private-link) | Özel bir uç nokta ve bir ön kapı profili içeren bir Azure Işlevleri uygulaması oluşturur.  |
|**API Management kaynakları**| **Açıklama** |
| [API Management (harici)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-api-management-external) | Dış VNet tümleştirmesiyle bir API Management örneği ve bir ön kapı profili oluşturur.  |
|**Depolama kaynakları**| **Açıklama** |
| [Depolama statik Web sitesi](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-storage-static-website) | Bir Azure depolama hesabı ve statik Web sitesini ortak bir uç nokta ve bir ön kapı profili oluşturur.  |
| [Özel bağlantısı olan depolama Blobları](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-storage-blobs-private-link) | Bir Azure depolama hesabı ve bir özel uç nokta ile blob kapsayıcısı ve bir ön kapı profili oluşturur.  |
|**Application Gateway kaynakları**| **Açıklama** |
| [Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-application-gateway-public) | Bir Application Gateway ve bir ön kapı profili oluşturur. |
|**Sanal makine kaynakları**| **Açıklama** |
| [Özel bağlantı hizmeti olan sanal makine](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-vm-private-link) | Bir sanal makine ve özel bağlantı hizmeti ve bir ön kapı profili oluşturur. |
| | |
