---
title: Elastik ölçek SSS
description: Azure SQL veritabanı elastik ölçeği hakkında sık sorulan sorular.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 51e15a8dc5e9f918c630397d6d6593f5bf561755
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92786913"
---
# <a name="elastic-database-tools-frequently-asked-questions-faq"></a>Elastik veritabanı araçları sık sorulan sorular (SSS)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Parça başına tek kiracılı ve parçalara ayırma anahtarı yoksa, şema bilgileri için parçalama anahtarını nasıl doldurabilirim?

Şema bilgisi nesnesi yalnızca birleştirme senaryolarını ayırmak için kullanılır. Bir uygulama doğal olarak tek kiracılı ise, bölünmüş birleştirme aracı gerektirmez ve bu nedenle şema bilgisi nesnesini doldurmanız gerekmez.

## <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Bir veritabanı sağladım ve zaten bir parça eşleme Yöneticisi var, bu yeni veritabanını parça olarak nasıl kaydedebilirim?

Lütfen [elastik veritabanı istemci kitaplığını kullanarak bir uygulamaya parça ekleme](elastic-scale-add-a-shard.md)bölümüne bakın.

## <a name="how-much-do-elastic-database-tools-cost"></a>Esnek veritabanı araçları maliyeti ne kadar

Elastik veritabanı istemci kitaplığı kullanmak herhangi bir maliyet uygulamaz. Maliyetler yalnızca parçalar ve parça eşleme Yöneticisi için kullandığınız Azure SQL veritabanındaki veritabanları için ve bölünmüş birleştirme aracında sağladığınız web/çalışan rolleri için tahakkuk eder.

## <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Farklı bir sunucudan parça eklerken neden kimlik bilgilerim çalışmıyor?

"User ID =" biçiminde kimlik bilgilerini kullanmayın username@servername , bunun yerine "User ID = username" kullanın.  Ayrıca, "Kullanıcı adı" oturumunun parça üzerinde izinlere sahip olduğundan emin olun.

## <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Uygulamalarımı her başlattığımda parça eşleme Yöneticisi oluşturup parçaları doldurmanız gerekir

Hayır — parça eşleme yöneticisinin oluşturulması (örneğin, [Shardmapmanagerfactory. CreateSqlShardMapManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)) tek seferlik bir işlemdir.  Uygulamanız uygulama başlangıç zamanı sırasında [Shardmapmanagerfactory. TryGetSqlShardMapManager ()](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) çağrısını kullanmalıdır.  Uygulama etki alanı başına yalnızca bir tür çağrı olmalıdır.

## <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Elastik veritabanı araçlarını kullanma hakkında sorularım var, bunların cevaplanmasını nasıl edinebilirim?

Lütfen [SQL veritabanı Için Microsoft Q&soru sayfasında](/answers/topics/azure-sql-database.html)bize ulaşın.

## <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Bir parçalama anahtarı kullanarak bir veritabanı bağlantısı aldığımda, aynı parça üzerindeki diğer parçalara ayırma anahtarları için de verileri sorgulayabilir.  Bu, tasarıma göre

Elastik ölçek API 'Leri, parçalara ayırma anahtarınız için doğru veritabanına bağlantı sağlar, ancak parçalama anahtar filtrelemesi sağlamaz.  Gerekirse, kapsamı belirtilen parçalama anahtarıyla kısıtlamak için sorgunuza **WHERE** yan tümceleri ekleyin.

## <a name="can-i-use-a-different-sql-database-edition-for-each-shard-in-my-shard-set"></a>Parça semdeki her parça için farklı bir SQL veritabanı sürümü kullanabilir miyim

Evet, parça tek bir veritabanıdır ve bu nedenle bir parça bir Premium sürüm olabilir ve diğeri de standart bir sürümdür. Ayrıca, parçanın ömrü boyunca parça sürümü birden çok kez ölçeği değiştirebilir veya azaltabilirsiniz.

## <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>Bölünmüş birleştirme aracı, bölünmüş veya birleştirme işlemi sırasında bir veritabanını sağlama (veya silme)

Hayır. **Bölme** işlemleri için hedef veritabanı uygun şemayla bulunmalı ve parça eşleme Yöneticisi ile kaydedilmelidir.  **Birleştirme** işlemleri için parça eşleme Yöneticisi ' nden parça silmeniz ve ardından veritabanını silmeniz gerekir.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]