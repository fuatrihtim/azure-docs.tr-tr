---
title: Elastik veritabanı istemci kitaplığındaki kimlik bilgilerini yönetme
description: Elastik veritabanı uygulamaları için doğru kimlik bilgileri, yönetici salt-salt okuma düzeyi nasıl ayarlanır
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
ms.openlocfilehash: 5b91986d4f94861b87e413c9ff781107c3ed04a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92786607"
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Elastik veritabanı istemci kitaplığına erişmek için kullanılan kimlik bilgileri
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Elastik veritabanı istemci kitaplığı](elastic-database-client-library.md) , parça [eşleme Yöneticisi](elastic-scale-shard-map-management.md)'ne erişmek için üç farklı türde kimlik bilgisi kullanır. İhtiyaya bağlı olarak, mümkün olan en düşük erişim düzeyiyle kimlik bilgilerini kullanın.

* **Yönetim kimlik bilgileri**: parça eşleme Yöneticisi oluşturmak veya değiştirmek için. ( [Sözlüğe](elastic-scale-glossary.md)bakın.)
* **Erişim kimlik bilgileri**: parçalara parçalar hakkında bilgi edinmek için mevcut bir parça eşleme yöneticisine erişmek.
* **Bağlantı kimlik bilgileri**: parçalara bağlanmak için.

Ayrıca bkz. [Azure SQL veritabanı 'nda veritabanlarını ve oturum açma bilgilerini yönetme](logins-create-manage.md).

## <a name="about-management-credentials"></a>Yönetim kimlik bilgileri hakkında

Yönetim kimlik bilgileri, parça haritalarını düzenleyen uygulamalar için bir **Shardmapmanager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) nesnesi oluşturmak için kullanılır. (Örneğin, bkz. [elastik veritabanı araçları](elastic-scale-add-a-shard.md) ve [verilere bağımlı yönlendirme](elastic-scale-data-dependent-routing.md)kullanarak parça ekleme). Elastik ölçek istemci kitaplığı kullanıcısı SQL kullanıcıları ve SQL oturum açmaları oluşturur ve her birine Global parça eşleme veritabanında ve tüm parça veritabanlarında okuma/yazma izinlerinin verildiğinden emin olur. Bu kimlik bilgileri, parça haritadaki değişiklikler gerçekleştirildiğinde küresel parça haritasını ve yerel parça haritalarını korumak için kullanılır. Örneğin, parça eşleme Yöneticisi nesnesini oluşturmak için yönetim kimlik bilgilerini kullanın (bkz. **Getsqlshardmapmanager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)):

```java
// Obtain a shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy);
```

**Smmadminconnectionstring** değişkeni, yönetim kimlik bilgilerini içeren bir bağlantı dizesidir. Kullanıcı KIMLIĞI ve parolası, hem parça eşleme veritabanına hem de bireysel parçalara okuma/yazma erişimi sağlar. Yönetim bağlantı dizesi Ayrıca, genel parça eşleme veritabanını tanımlamak için sunucu adını ve veritabanı adını da içerir. Bu amaçla tipik bir bağlantı dizesi aşağıda verilmiştir:

```java
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;”
```

"" Biçiminde değerler kullanmayın, username@server bunun yerine yalnızca "username" değerini kullanın.  Bunun nedeni, kimlik bilgilerinin hem parça eşleme Yöneticisi veritabanına hem de farklı sunucularda olabilecek ayrı parçalara karşı çalışması gerekir.

## <a name="access-credentials"></a>Erişim kimlik bilgileri

Parça haritalarını yönetmez bir uygulamada parça eşleme Yöneticisi oluştururken, genel parça eşlemesinde salt okuma izinlerine sahip kimlik bilgilerini kullanın. Bu kimlik bilgileri altındaki genel parça haritasından alınan bilgiler, [verilere bağımlı yönlendirme](elastic-scale-data-dependent-routing.md) için kullanılır ve istemci üzerindeki parça eşleme önbelleğini doldurur. Kimlik bilgileri, **Getsqlshardmapmanager** için aynı çağrı düzeniyle sağlanır:

```java
// Obtain shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

**Yönetici olmayan** kullanıcılar adına bu erişim için farklı kimlik bilgilerinin kullanımını yansıtmak üzere **Smmreadonlyconnectionstring** 'in kullanımını not edin: Bu kimlik bilgileri, genel parça eşlemesinde yazma izinleri sağlamamalıdır.

## <a name="connection-credentials"></a>Bağlantı kimlik bilgileri

Bir parça anahtarıyla ilişkili bir parçaya erişmek için **Openconnectionforkey**  ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) yöntemi kullanılırken ek kimlik bilgileri gerekir. Bu kimlik bilgilerinin, parça üzerinde bulunan yerel parça eşleme tablolarına salt okuma erişimi için izinleri sağlaması gerekir. Bu, parça üzerinde verilere bağımlı yönlendirme için bağlantı doğrulaması yapmak için gereklidir. Bu kod parçacığı, verilere bağımlı yönlendirme bağlamında veri erişimine izin verir:

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate))
```

Bu örnekte, **Smmuserconnectionstring** Kullanıcı kimlik bilgileri için bağlantı dizesini barındırır. Azure SQL veritabanı için Kullanıcı kimlik bilgileri için tipik bir bağlantı dizesi aşağıda verilmiştir:

```java
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

Yönetici kimlik bilgilerinde olduğu gibi, "" biçimindeki değerleri kullanmayın username@server . Bunun yerine, yalnızca "kullanıcıadı" kullanın.  Ayrıca, bağlantı dizesinin bir sunucu adı ve veritabanı adı içermediğini unutmayın. Bunun nedeni, **Openconnectionforkey** çağrısının anahtara göre doğru parça bağlantısını otomatik olarak yönlendirmesdir. Bu nedenle, veritabanı adı ve sunucu adı sağlanmaz.

## <a name="see-also"></a>Ayrıca bkz.

[Azure SQL Veritabanı’nda veritabanlarını ve oturum açma bilgilerini yönetme](logins-create-manage.md)

[SQL Veritabanınızı güvenli hale getirme](security-overview.md)

[Elastik Veritabanı işleri](elastic-jobs-overview.md)

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]