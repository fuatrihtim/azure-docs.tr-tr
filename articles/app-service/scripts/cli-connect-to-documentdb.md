---
title: 'CLı: uygulamayı Cosmos DB bağlama'
description: Azure CLı kullanarak App Service uygulamanızın dağıtımını ve yönetimini otomatik hale getirmeyi öğrenin. Bu örnek, bir uygulamanın MongoDB 'ye nasıl bağlanacağını gösterir (Cosmos DB).
author: msangapu-msft
tags: azure-service-management
ms.assetid: bbbdbc42-efb5-4b4f-8ba6-c03c9d16a7ea
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: fa971bbb8526f1b0bc081e61ed5e7a7f2cf76d1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97006370"
---
# <a name="connect-an-app-service-app-to-cosmos-db-using-cli"></a>CLı kullanarak bir App Service uygulamasını Cosmos DB bağlama

Bu örnek betik, MongoDB için Azure Cosmos DB API 'sini ve bir App Service uygulamasını kullanarak bir Azure Cosmos DB hesabı oluşturur. Daha sonra uygulama ayarlarını kullanarak bir MongoDB bağlantı dizesini Web uygulamasına bağlar.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz Azure CLI 2.0 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Örnek betik

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-documentdb/connect-to-documentdb.sh "Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik bir kaynak grubu, App Service uygulaması, Cosmos DB ve tüm ilgili kaynakları oluşturmak için aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | App Service planı oluşturur. |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | App Service uygulaması oluşturur. |
| [`az cosmosdb create`](/cli/azure/cosmosdb#az-cosmosdb-create) | Bir Cosmos DB hesabı oluşturur. |
| [`az cosmosdb list-connection-strings`](/cli/azure/cosmosdb#az-cosmosdb-list-connection-strings) | Belirtilen Cosmos DB hesabı için bağlantı dizelerini listeler. |
| [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | App Service uygulaması için bir uygulama ayarı oluşturur veya güncelleştirir. Uygulama ayarları, uygulamanız için ortam değişkenleri olarak kullanıma sunulur. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).

Ek App Service CLI betik örnekleri, [Azure App Service belgelerinde](../samples-cli.md) bulunabilir.
