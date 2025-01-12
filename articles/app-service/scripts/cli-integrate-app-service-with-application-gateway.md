---
title: Azure CLı betik örneği-App Service Application Gateway ile tümleştirin | Microsoft Docs
description: Azure CLı betik örneği-App Service Application Gateway ile tümleştirin
services: appservice
documentationcenter: appservice
author: madsd
manager: ccompy
editor: ''
tags: azure-service-management
ms.assetid: 6c16d6f8-3c08-4a59-858e-684a2ceccb5f
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 3820e7bf00f99a846dd2be0edeaf4248e0dfd8ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97006098"
---
# <a name="integrate-app-service-with-application-gateway-using-cli"></a>CLı kullanarak Application Gateway App Service tümleştirme

Bu örnek betik, bir Azure App Service Web uygulaması, bir Azure sanal ağı ve bir Application Gateway oluşturur. Daha sonra, Web uygulaması için trafiği yalnızca Application Gateway alt ağından kaynaklanan şekilde kısıtlar.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Bu öğretici, Azure CLı 'nin sürüm 2.0.74 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="sample-script"></a>Örnek betik

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/integrate-with-app-gateway/integrate-with-app-gateway.sh "Integrate with Application Gateway")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik bir kaynak grubu, App Service uygulaması, Cosmos DB ve tüm ilgili kaynakları oluşturmak için aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [`az network vnet create`](/cli/azure/network/vnet#az-network-vnet-create) | Sanal ağ oluşturur. |
| [`az network public-ip create`](/cli/azure/network/public-ip#az-network-public-ip-create) | Genel bir IP adresi oluşturur. |
| [`az network public-ip show`](/cli/azure/network/public-ip#az-network-public-ip-show) | Genel bir IP adresinin ayrıntılarını gösterin. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | App Service planı oluşturur. |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | Bir App Service Web uygulaması oluşturur. |
| [`az webapp show`](/cli/azure/webapp#az-webapp-show) | App Service bir Web uygulamasının ayrıntılarını gösterin. |
| [`az webapp config access-restriction add`](/cli/azure/webapp/config/access-restriction#az-webapp-config-access-restriction-add) | App Service Web uygulamasına bir erişim kısıtlaması ekler. |
| [`az network application-gateway create`](/cli/azure/network/application-gateway#az-network-application-gateway-create) | Bir Application Gateway oluşturur. |
| [`az network application-gateway http-settings update`](/cli/azure/network/application-gateway/http-settings#az-network-application-gateway-http-settings-update) | Application Gateway HTTP ayarlarını güncelleştirir. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).

Ek App Service CLI betik örnekleri, [Azure App Service belgelerinde](../samples-cli.md) bulunabilir.
