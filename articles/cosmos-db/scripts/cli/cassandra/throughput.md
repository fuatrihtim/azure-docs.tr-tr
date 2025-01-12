---
title: Azure Cosmos DB Cassandra API kaynakları için üretilen iş (RU/s) işlemleri için Azure CLı betikleri
description: Azure Cosmos DB Cassandra API kaynakları için üretilen iş (RU/s) işlemleri için Azure CLı betikleri
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 1979c59af53ebeccdbd7c910a87fb4c2536fe403
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94565594"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-keyspace-or-table-for-azure-cosmos-db---cassandra-api"></a>Azure Cosmos DB için anahtar uzayı veya tablo için Azure CLı ile üretilen iş (RU/s) işlemleri Cassandra API
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.12.1 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="sample-script"></a>Örnek betik

Bu betik, paylaşılan aktarım hızı ile bir Cassandra anahtar alanı ve adanmış aktarım hızı içeren bir Cassandra tablosu oluşturur ve hem anahtar alanı hem de tablo için üretilen işi güncelleştirir. Komut dosyası daha sonra standart 'den otomatik ölçeklendirme aktarım hızına geçirilir ve sonra otomatik ölçeklendirme üretilen işinin değerini geçiş yapıldıktan sonra okur.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/throughput.sh "Throughput operations for Cassandra keyspace and table.")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme

Betik örneği çalıştırıldıktan sonra, kaynak grubunu ve onunla ilişkili tüm kaynakları kaldırmak için aşağıdaki komut kullanılabilir.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Betik açıklaması

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Azure Cosmos DB hesabı oluşturur. |
| [az cosmosdb Cassandra anahtar alanı oluşturma](/cli/azure/cosmosdb/cassandra/keyspace#az-cosmosdb-cassandra-keyspace-create) | Azure Cosmos Cassandra keyspace oluşturur. |
| [az cosmosdb Cassandra tablo oluşturma](/cli/azure/cosmosdb/cassandra/table#az-cosmosdb-cassandra-table-create) | Azure Cosmos Cassandra tablosu oluşturur. |
| [az cosmosdb Cassandra anahtar alanı üretilen iş güncelleştirmesi](/cli/azure/cosmosdb/cassandra/keyspace/throughput#az-cosmosdb-cassandra-keyspace-throughput-update) | Azure Cosmos Cassandra keyspace için RU/s 'yi güncelleştirin. |
| [az cosmosdb Cassandra tablo aktarım hızı güncelleştirmesi](/cli/azure/cosmosdb/cassandra/table/throughput#az-cosmosdb-cassandra-table-throughput-update) | Azure Cosmos Cassandra tablosu için RU/s 'yi güncelleştirin. |
| [az cosmosdb Cassandra anahtar alanı verimlilik geçişi](/cli/azure/cosmosdb/cassandra/keyspace/throughput#az_cosmosdb_cassandra_keyspace_throughput_migrate) | Azure Cosmos Cassandra keyspace için aktarım hızını geçirin. |
| [az cosmosdb Cassandra tablo verimlilik geçişi](/cli/azure/cosmosdb/cassandra/table/throughput#az_cosmosdb_cassandra_table_throughput_migrate) | Azure Cosmos Cassandra tablosu için aktarım hızını geçirin. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar

CLı Azure Cosmos DB hakkında daha fazla bilgi için bkz. [Azure Cosmos DB CLI belgeleri](/cli/azure/cosmosdb).

Tüm Azure Cosmos DB CLı betiği örnekleri [Azure Cosmos DB CLI GitHub deposunda](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)bulunabilir.
