---
title: Azure CLı betiği-PostgreSQL için Azure veritabanı 'nı ölçeklendirme ve izleme
description: Örnek Azure CLI Betiği - Ölçümleri sorguladıktan sonra PostgreSQL için Azure Veritabanı sunucusunu farklı bir performans düzeyinde olacak şekilde ölçeklendirin.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.custom: mvc, devx-track-azurecli
ms.topic: sample
ms.date: 08/07/2019
ms.openlocfilehash: b0a17b12c51c08cd39d0c4fb7860021b8c68e606
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606411"
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Azure CLI kullanarak tek bir PostgreSQL sunucusunu izleme ve ölçeklendirme
Bu örnek CLı betiği, ölçümleri sorguladıktan sonra, tek bir PostgreSQL için Azure veritabanı sunucusu için işlem ve depolamayı ölçeklendirir. İşlem ölçeği yukarı veya aşağı olabilir. Depolama alanı yalnızca ölçeği değiştirebilir. 

> [!IMPORTANT] 
> Depolama yalnızca yukarı ölçeklenebilen, aşağı doğru değil.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin 2,0 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="sample-script"></a>Örnek betik
Betiği abonelik KIMLIĞINIZLE güncelleştirin.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme
Betik çalıştırıldıktan sonra aşağıdaki komutu kullanarak kaynak grubunu ve bununla ilişkili tüm kaynakları kaldırın. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Betik açıklaması
Bu betik, aşağıdaki tabloda ana hatları verilen komutları kullanır:

| **Komut** | **Notlar** |
|---|---|
| [az group create](/cli/azure/group) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az postgres server create](/cli/azure/postgres/server#az-postgres-server-create) | Veritabanlarını barındıran bir PostgreSQL sunucusu oluşturur. |
| [az Postgres Server Update](/cli/azure/postgres/server#az-postgres-server-update) | PostgreSQL sunucusunun özelliklerini güncelleştirir. |
| [az monitor metrics list](/cli/azure/monitor/metrics) | Kaynaklar için ölçüm değerini listeleyin. |
| [az group delete](/cli/azure/group) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar
- [PostgreSQL Için Azure veritabanı işlem ve depolama](../concepts-pricing-tiers.md) hakkında daha fazla bilgi edinin
- Ek betikleri deneyin: [PostgreSQL için Azure Veritabanı Azure CLI örnekleri](../sample-scripts-azure-cli.md)
- [Azure CLI](/cli/azure) hakkında daha fazla bilgi edinin
