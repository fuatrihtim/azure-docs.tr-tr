---
title: 'CLı: bir uygulamayı SQL veritabanına bağlama'
description: Azure CLı kullanarak App Service uygulamanızın dağıtımını ve yönetimini otomatik hale getirmeyi öğrenin. Bu örnek, bir uygulamanın SQL veritabanına nasıl bağlanacağını göstermektedir.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 7c2efdd0-f553-4038-a77a-e953021b3f77
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: abd96e513aadf44d0f313670e1437ebd16aa410c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97006387"
---
# <a name="connect-an-app-service-app-to-sql-database-using-cli"></a>CLı kullanarak bir App Service uygulamasını SQL veritabanına bağlama

Bu örnek betik, Azure SQL veritabanı 'nda ve bir App Service uygulamasında bir veritabanı oluşturur. Daha sonra uygulama ayarlarını kullanarak veritabanını uygulamaya bağlar.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz Azure CLI 2.0 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Örnek betik

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-sql/connect-to-sql.sh?highlight=9-10 "SQL Database")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik, bir kaynak grubu, App Service uygulaması, SQL veritabanı ve tüm ilgili kaynakları oluşturmak için aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | App Service planı oluşturur. |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | App Service uygulaması oluşturur. |
| [`az sql server create`](/cli/azure/sql/server#az-sql-server-create) | Bir sunucu oluşturur.  |
| [`az sql db create`](/cli/azure/sql/db#az-sql-db-create) | Yeni bir veritabanı oluşturur. |
| [`az sql db show-connection-string`](/cli/azure/sql/db#az-sql-db-show-connection-string) | Veritabanına bir bağlantı dizesi oluşturur. |
| [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | App Service uygulaması için bir uygulama ayarı oluşturur veya güncelleştirir. Uygulama ayarları, uygulamanız için ortam değişkenleri olarak kullanıma sunulur. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).

Ek App Service CLI betik örnekleri, [Azure App Service belgelerinde](../samples-cli.md) bulunabilir.
