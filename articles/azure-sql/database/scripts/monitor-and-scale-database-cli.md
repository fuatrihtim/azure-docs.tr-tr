---
title: "Azure CLı: Azure SQL veritabanı 'nda tek bir veritabanını Izleme ve ölçeklendirme"
description: Azure SQL veritabanı 'nda tek bir veritabanını izlemek ve ölçeklendirmek için bir Azure CLı örnek betiği kullanın.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 06/25/2019
ms.openlocfilehash: 0a72bea08052e5cd17502a21a98b41cb5582a3b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96493648"
---
# <a name="use-the-azure-cli-to-monitor-and-scale-a-single-database-in-azure-sql-database"></a>Azure SQL veritabanı 'nda tek bir veritabanını izlemek ve ölçeklendirmek için Azure CLı 'yi kullanma

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Bu Azure CLı betik örneği, veritabanının boyut bilgilerini sorguladıktan sonra Azure SQL veritabanı 'ndaki tek bir veritabanını farklı bir işlem boyutuna ölçeklendirir.

Azure CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu makale, Azure CLı sürüm 2,0 veya üstünü çalıştırıyor olmanızı gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Örnek betik

### <a name="sign-in-to-azure"></a>Azure'da oturum açma

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Betiği çalıştırın

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale a database in Azure SQL Database")]

> [!TIP]
> Veritabanında gerçekleştirilen işlemlerin bir listesini almak için [az SQL DB op listesini](/cli/azure/sql/db/op?#az-sql-db-op-list) kullanın ve veritabanında bir güncelleştirme işlemini iptal etmek için [az SQL DB op Cancel](/cli/azure/sql/db/op#az-sql-db-op-cancel) kullanın.

### <a name="clean-up-deployment"></a>Dağıtımı temizleme

Kaynak grubunu ve onunla ilişkili tüm kaynakları kaldırmak için aşağıdaki komutu kullanın.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Örnek başvuru

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut Dosyası | Description |
|---|---|
| [az sql server](/cli/azure/sql/server) | Sunucu komutları. |
| [az sql db show-usage](/cli/azure/sql#az-sql-show-usage) | Bir veritabanının boyut kullanım bilgilerini gösterir. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).

Ek CLı betik örnekleri, [Azure CLI örnek betiklerine](../az-cli-script-samples-content-guide.md)bulunabilir.
