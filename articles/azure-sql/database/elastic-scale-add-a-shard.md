---
title: Elastik veritabanı araçlarını kullanarak parça ekleme
description: Yeni parçaları bir parça kümesine eklemek için elastik ölçek API 'Leri kullanma.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: efab0234d428a8283845946289cdd1e8a17ded26
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792064"
---
# <a name="adding-a-shard-using-elastic-database-tools"></a>Elastik veritabanı araçlarını kullanarak parça ekleme
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Yeni bir Aralık veya anahtar için parça eklemek için

Uygulamalar, zaten var olan bir parça eşlemesi için yeni anahtarlar veya anahtar aralıklarından beklenen verileri işlemek üzere genellikle yeni parçalar eklemesi gerekir. Örneğin, kiracı KIMLIĞI tarafından oluşturulmuş bir uygulamanın yeni bir kiracı için yeni bir parça sağlaması veya aylık veri parçaları, her yeni ay başlamadan önce sağlanan yeni bir parça gerektirebilir.

Yeni anahtar değerleri aralığı zaten varolan bir eşlemenin parçası değilse, yeni parçayı eklemek ve yeni anahtarı ya da aralığı bu parça ile ilişkilendirmek basittir.

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Örnek: var olan parça eşlemesine parça ve onun aralığını ekleme

Bu örnek, TryGetShard[(Java,](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.trygetshard).net [),](/previous-versions/azure/dn823929(v=azure.100))createkıard[(Java, .net](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard) [),](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)createrangemapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) yöntemleri) kullanır ve shardlocation ([Java](/java/api/com.microsoft.azure.elasticdb.shard.base.shardlocation), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation)) sınıfının bir örneğini oluşturur. Aşağıdaki örnekte, **sample_shard_2** adlı bir veritabanı ve içindeki tüm gerekli şema nesneleri [300, 400) aralığını tutmak için oluşturulmuştur.  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range being added.
Shard shard2 = null;

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2))
{
    shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
}

// Create the mapping and associate it with the new shard
sm.CreateRangeMapping(new RangeMappingCreationInfo<long>
                            (new Range<long>(300, 400), shard2, MappingStatus.Online));
```

.NET sürümünde, yeni bir parça eşleme Yöneticisi oluşturmak için alternatif olarak PowerShell 'i de kullanabilirsiniz. [Burada](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)bir örnek kullanılabilir.

## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Varolan bir aralığın boş bir bölümü için parça eklemek için

Bazı durumlarda, bir aralığı bir parça ile eşleştirerek verileri kısmen doldurmuş olabilirsiniz ancak bundan sonra yaklaşan verilerin farklı bir parçaya yönlendirilmek istiyorsunuz. Örneğin, gün aralığına göre parçalarmış ve bir parça için 50 gün önceden ayırmış olursunuz, ancak 24. gün, gelecekteki verilerin farklı bir parça içinde yer kaplamasını istiyorsunuz. Elastik veritabanı [bölünmüş birleştirme aracı](elastic-scale-overview-split-and-merge.md) bu işlemi gerçekleştirebilir, ancak veri taşıma gerekli değilse (örneğin, gün aralığı için veriler [25, 50), diğer bir deyişle, 50 dışlamalı gün, henüz yok), bunu tamamen doğrudan parça eşleme yönetim API 'lerini kullanarak gerçekleştirebilirsiniz.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Örnek: aralığı bölme ve yeni eklenen parçaya boş bölüm atama

"Sample_shard_2" adlı bir veritabanı ve içindeki tüm gerekli şema nesneleri oluşturuldu.  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range we will move
Shard shard2 = null;

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2))
{
    shard2 = sm.CreateShard(new ShardLocation(shardServer,"sample_shard_2"));  
}

// Split the Range holding Key 25
sm.SplitMapping(sm.GetMappingForKey(25), 25);

// Map new range holding [25-50) to different shard:
// first take existing mapping offline
sm.MarkMappingOffline(sm.GetMappingForKey(25));

// now map while offline to a different shard and take online
RangeMappingUpdate upd = new RangeMappingUpdate();
upd.Shard = shard2;
sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd));
```

**Önemli**: Bu tekniği yalnızca, güncelleştirilmiş eşleme aralığının boş olduğundan eminseniz kullanın.  Önceki yöntemler, taşınmakta olan aralığın verilerini denetlemez, bu nedenle kodunuzda denetimleri eklemek en iyisidir.  Taşınmakta olan aralıkta satırlar varsa, gerçek veri dağıtımı güncelleştirilmiş parça eşlemesiyle eşleşmez. Bu gibi durumlarda işlemi gerçekleştirmek için [bölünmüş birleştirme aracını](elastic-scale-overview-split-and-merge.md) kullanın.  

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]