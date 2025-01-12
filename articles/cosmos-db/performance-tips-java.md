---
title: Azure Cosmos DB Sync Java SDK v2 için performans ipuçları
description: Eşitleme Java SDK v2 için Azure Cosmos veritabanı performansını iyileştirecek istemci yapılandırma seçeneklerini öğrenin
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 05/11/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 6e7b01ae88645d8b16c3a43e21e40b53d242fdde
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96549251"
---
# <a name="performance-tips-for-azure-cosmos-db-sync-java-sdk-v2"></a>Azure Cosmos DB Sync Java SDK v2 için performans ipuçları
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK’sı v4](performance-tips-java-sdk-v4-sql.md)
> * [Zaman uyumsuz Java SDK v2](performance-tips-async-java.md)
> * [Zaman uyumlu Java SDK v2](performance-tips-java.md)
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> 

> [!IMPORTANT]  
> Bu, Azure Cosmos DB için en son Java SDK 'Sı *değildir* ! Projenizi [Java SDK v4 Azure Cosmos DB](sql-api-sdk-java-v4.md) yükseltmeniz ve ardından Azure Cosmos DB Java SDK v4 [performansı ipuçları kılavuzunu](performance-tips-java-sdk-v4-sql.md)okumanız gerekir. Yükseltmek için [Azure Cosmos DB Java SDK 'sı v4](migrate-java-v4-sdk.md) Kılavuzu ve [reaktör vs rxjava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) kılavuzundaki yönergeleri izleyin. 
> 
> Bu performans ipuçları yalnızca Azure Cosmos DB Sync Java SDK v2 içindir. Daha fazla bilgi için lütfen Azure Cosmos DB eşitleme Java SDK v2 [sürüm notları](sql-api-sdk-java.md) ve [Maven deposunu](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb) görüntüleyin.
>

Azure Cosmos DB, garantili gecikme ve verimlilik ile sorunsuz bir şekilde ölçeklenen hızlı ve esnek bir dağıtılmış veritabanıdır. Veritabanınızı Azure Cosmos DB ölçeklendirmek için önemli mimari değişiklikler yapmanız veya karmaşık kod yazmanız gerekmez. Ölçeği artırma ve azaltma, tek bir API çağrısı yapmak kadar kolaydır. Daha fazla bilgi edinmek için bkz. [kapsayıcı verimini sağlama](how-to-provision-container-throughput.md) veya [veritabanı verimini sağlama](how-to-provision-database-throughput.md). Ancak, Azure Cosmos DB ağ çağrılarıyla erişildiği için, [Azure Cosmos DB eşitleme Java SDK v2](./sql-api-sdk-java.md)kullanırken en yüksek performans elde etmek için, istemci tarafı iyileştirmeler vardır.

Bu nedenle "veritabanı performanmy nasıl iyileştirebilirim?" diye soruyoruz Aşağıdaki seçenekleri göz önünde bulundurun:

## <a name="networking"></a>Ağ
<a id="direct-connection"></a>

1. **Bağlantı modu: DirectHttps kullanın**

    İstemcinin Azure Cosmos DB'ye nasıl bağlandığının performans üzerinde, özellikle de gözlemlenen istemci tarafı gecikme süresi olarak önemli etkileri olur. İstemci bağlantı [ilkesini](/java/api/com.microsoft.azure.documentdb.connectionpolicy) yapılandırmak için kullanılabilecek bir anahtar yapılandırma ayarı vardır: [connectionmode](/java/api/com.microsoft.azure.documentdb.connectionmode).  Kullanılabilir iki Connectionmode şunlardır:

   1. [Ağ Geçidi (varsayılan)](/java/api/com.microsoft.azure.documentdb.connectionmode)
   2. [DirectHttps](/java/api/com.microsoft.azure.documentdb.connectionmode)

      Ağ Geçidi modu tüm SDK platformlarında desteklenir ve yapılandırılmış varsayılandır.  Uygulamanız, katı güvenlik duvarı kısıtlamalarına sahip bir kurumsal ağ içinde çalışıyorsa, standart HTTPS bağlantı noktasını ve tek bir uç noktayı kullandığından, ağ geçidi en iyi seçenektir. Ancak performans zorunluluğunu getirir, ağ geçidi modunun, verilerin her okunduğu veya Azure Cosmos DB yazıldığı her seferinde ek bir ağ atlaması içerir. Bu nedenle, DirectHttps modu, daha az ağ atlaması nedeniyle daha iyi performans sunar. 

      Azure Cosmos DB Sync Java SDK v2, aktarım protokolü olarak HTTPS kullanır. HTTPS, ilk kimlik doğrulaması ve trafiği şifrelemek için TLS kullanır. Azure Cosmos DB Sync Java SDK v2 kullanılırken, yalnızca HTTPS bağlantı noktası 443 açık olmalıdır. 

      ConnectionMode, DocumentClient örneğinin oluşturulması sırasında ConnectionPolicy parametresiyle yapılandırılır. 

    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-connectionpolicy"></a>Java SDK v2 'yi eşitleme (Maven com. Microsoft. Azure:: Azure-DocumentDB)

      ```Java
      public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.DirectHttps);
        policy.setMaxPoolSize(1000);
        return policy;
      }
        
      ConnectionPolicy connectionPolicy = new ConnectionPolicy();
      DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
      ```

      :::image type="content" source="./media/performance-tips-java/connection-policy.png" alt-text="Diyagram Azure Cosmos D B bağlantı ilkesini gösterir." border="false":::

   <a id="same-region"></a>
2. **Performans için istemcileri aynı Azure bölgesinde birlikte bulundurun**

    Mümkün olduğunda Azure Cosmos veritabanıyla aynı bölgedeki Azure Cosmos DB çağıran tüm uygulamaları yerleştirin. Yaklaşık bir karşılaştırma için, 1-2 ms içinde aynı bölgedeki Azure Cosmos DB çağrıları tamamlanır, ancak ABD 'nin batı ve Doğu yakası arasındaki gecikme 50 MS >. Bu gecikme süresi büyük olasılıkla istemciden Azure veri merkezi sınırına geçerken istek tarafından alınan yola bağlı olarak istek üzerine farklılık gösterebilir. Olası en düşük gecikme süresi, çağıran uygulamanın sağlanan Azure Cosmos DB uç noktası ile aynı Azure bölgesinde bulunduğundan emin olarak elde edilir. Kullanılabilir bölgelerin listesi için bkz. [Azure bölgeleri](https://azure.microsoft.com/regions/#services).

    :::image type="content" source="./media/performance-tips/same-region.png" alt-text="Diyagramda, bilgisayarların bir Cosmos D B hesabına orta katman hizmetleri aracılığıyla bağlanacağı iki bölgedeki istekler ve yanıtlar gösterilmektedir." border="false":::
   
## <a name="sdk-usage"></a>SDK kullanımı
1. **En son SDK 'Yı yükler**

    Azure Cosmos DB SDK 'Ları, en iyi performansı sağlamak için sürekli geliştirilmiştir. En son SDK 'Yı öğrenmek ve geliştirmeleri gözden geçirmek için [Azure Cosmos DB SDK](./sql-api-sdk-java.md) sayfalarına bakın.
2. **Uygulamanızın ömrü boyunca tek bir Azure Cosmos DB istemcisi kullanın**

    Her [Documentclient](/java/api/com.microsoft.azure.documentdb.documentclient) örneği, iş parçacığı açısından güvenlidir ve doğrudan modda çalışırken verimli bağlantı yönetimi ve adres önbelleği gerçekleştirir. Etkin bağlantı yönetimine ve DocumentClient tarafından daha iyi performansa izin vermek için, uygulamanın kullanım ömrü boyunca AppDomain başına tek bir DocumentClient örneği kullanılması önerilir.

   <a id="max-connection"></a>
3. **Ağ Geçidi modu kullanılırken konak başına MaxPoolSize değerini artır**

    Azure Cosmos DB istekleri, ağ geçidi modu kullanılırken HTTPS/REST üzerinden yapılır ve ana bilgisayar adı veya IP adresi başına varsayılan bağlantı sınırına tabi. İstemci kitaplığının Azure Cosmos DB için birden çok eş zamanlı bağlantıyı kullanabilmesi için MaxPoolSize değerini daha yüksek bir değere (200-1000) ayarlamanız gerekebilir. Azure Cosmos DB Sync Java SDK v2 sürümünde, [Connectionpolicy. getMaxPoolSize](/java/api/com.microsoft.azure.documentdb.connectionpolicy.getmaxpoolsize) için varsayılan değer 100 ' dir. Değeri değiştirmek için [Setmaxpoolsize]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setmaxpoolsize) değerini kullanın.

4. **Bölümlenmiş koleksiyonlar için Paralel sorguları ayarlama**

    Azure Cosmos DB Java SDK sürüm 1.9.0 Eşitle ve üzeri, bölümlenmiş bir koleksiyonu paralel olarak sorgulamanızı sağlayan paralel sorguları destekler. Daha fazla bilgi için bkz. SDK 'lar ile çalışma ile ilgili [kod örnekleri](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples) . Paralel sorgular, kendi seri karşılarındaki sorgu gecikmesini ve aktarım hızını artırmak için tasarlanmıştır.

    (a) ***Setmaxdegreeofparalellik \:*** Paralel sorguları ayarlama, birden çok bölümü paralel olarak sorgulayarak çalışır. Ancak, tek bir bölümlenmiş koleksiyondaki veriler, sorguya göre işlem içine alınır. Bu nedenle, en iyi performansı elde etmek için en yüksek performansa sahip bölüm sayısını ayarlamak için [Setmaxdegreeofparalellik](/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxdegreeofparallelism) kullanın, diğer tüm sistem koşulları aynı kalır. Bölüm sayısını bilmiyorsanız, yüksek bir sayı ayarlamak için Setmaxdegreeofparalellik kullanabilirsiniz ve sistem en az paralellik derecesi olarak en düşük (bölüm sayısı, Kullanıcı tarafından girilen giriş) değerini seçer. 

    Verilerin sorguya göre tüm bölümler arasında eşit bir şekilde dağıtılması halinde paralel sorguların en iyi avantajları ürettiğine dikkat edin. Bölümlenmiş koleksiyon, bir sorgu tarafından döndürülen verilerin tümünün veya çoğunluğunun birkaç bölümde (en kötü durumda bir bölüm) yoğunlaşarak bir şekilde bölümlenmişse, sorgunun performansı bu bölümler tarafından bottlenecked olacaktır.

    (b) ***ayarlama setMaxBufferedItemCount \:*** paralel sorgusu, geçerli sonuç toplu işi istemci tarafından işlenirken sonuçları önceden getirmek üzere tasarlanmıştır. Önceden getirme, bir sorgunun genel gecikme artışında yardımcı olur. setMaxBufferedItemCount, önceden getirilen sonuçların sayısını sınırlar. [Setmaxbuffereditemcount](/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxbuffereditemcount) değerini döndürülen beklenen sonuç sayısına (veya daha yüksek bir sayıya) ayarlayarak, sorgunun ön alma işleminden en fazla avantaj almasına olanak sağlar.

    Önceden getirme, Maxdegreeofparalelliği ne olursa olsun aynı şekilde çalışıyor ve tüm bölümlerdeki veriler için tek bir arabellek vardır.  

5. **GetRetryAfterInMilliseconds aralıklarında geri alma Uygula**

    Performans testi sırasında, küçük bir istek hızı kısıtlanana kadar yükü artırmanız gerekir. Kısıtlanmamışsa, sunucu tarafından belirtilen yeniden deneme aralığı için istemci uygulamanın azaltılmasından geri dönüş olması gerekir. Geri alma işleminin en düşük olması, yeniden denemeler arasında bekleyen minimum süreyi harcamanızı sağlar. Yeniden deneme ilkesi desteği, [Azure Cosmos DB Sync Java SDK 'sının](./sql-api-sdk-java.md)1.8.0 sürümüne ve üstüne dahildir. Daha fazla bilgi için bkz. [getRetryAfterInMilliseconds](/java/api/com.microsoft.azure.documentdb.documentclientexception.getretryafterinmilliseconds).

6. **İstemcinizi genişletme-iş yükü**

    Yüksek aktarım hızı düzeylerinde (>50.000 RU/sn) test ediyorsanız, istemci uygulaması makine CPU veya ağ kullanımında kullanıma hazır hale geldiği için performans sorunlarına neden olabilir. Bu noktaya ulaştığınızda, istemci uygulamalarınızı birden çok sunucu arasında ölçeklendirerek Azure Cosmos DB hesabını daha fazla göndermeye devam edebilirsiniz.

7. **Ad tabanlı adresleme kullan**

    Bağlantıların, `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId` \_ `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` bağlantıyı oluşturmak için kullanılan tüm kaynakların RESOURCEID 'lerini almaktan kaçınmak için biçimi olan, kendi kendini bağlayan, (self) değil, ad tabanlı adresleme kullanın. Ayrıca, bu kaynaklar yeniden oluşturulmuş (büyük olasılıkla aynı ada sahip) olduğundan, önbelleğe alma işlemi yardımcı olmayabilir.

   <a id="tune-page-size"></a>
8. **Daha iyi performans için, sorguların/okunan akışların sayfa boyutunu ayarlayın**

    Belgeleri okuma akışı işlevselliğini (örneğin, [Readdocuments](/java/api/com.microsoft.azure.documentdb.documentclient.readdocuments)) kullanarak veya bir SQL sorgusu verirken belge okuma işlemi gerçekleştirirken, sonuç kümesi çok büyükse sonuçlar parçalı olarak döndürülür. Varsayılan olarak, sonuçlar 100 öğe veya 1 MB Öbekle döndürülür, bu sınır ilk önce dönüştürülür.

    Tüm geçerli sonuçları almak için gereken ağ gidiş dönüşlerin sayısını azaltmak için, [x-MS-Max-item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) istek üst bilgisini 1000 'e kadar kullanarak sayfa boyutunu artırabilirsiniz. Örneğin, Kullanıcı arabiriminiz veya uygulama API 'niz bir kez yalnızca 10 sonuç döndürürse, okuma ve sorgular için tüketilen aktarım hızını azaltmak için sayfa boyutunu 10 ' a da azaltabilirsiniz.

    Ayrıca, [SetPageSize yöntemini](/java/api/com.microsoft.azure.documentdb.feedoptionsbase.setpagesize)kullanarak sayfa boyutunu da ayarlayabilirsiniz.

## <a name="indexing-policy"></a>Dizin Oluşturma İlkesi
 
1. **Daha hızlı yazma işlemleri için, kullanılmayan yolları dizin oluşturmanın dışında bırakma**

    Azure Cosmos DB Dizin oluşturma ilkesi, dizin oluşturma yollarını ([Setıncludedpaths](/java/api/com.microsoft.azure.documentdb.indexingpolicy.setincludedpaths) ve [setexcludedpaths](/java/api/com.microsoft.azure.documentdb.indexingpolicy.setexcludedpaths)) kullanarak hangi belge yollarının dizine eklenmesini veya dışlanacağını belirtmenize olanak tanır. Dizin oluşturma yollarının kullanımı, sorgu desenlerinin önceden bildiği senaryolar için gelişmiş yazma performansı ve daha düşük dizin depolaması sunabilir, dizin oluşturma maliyetleri doğrudan dizinli benzersiz yolların sayısıyla bağıntılı olur.  Örneğin, aşağıdaki kod, "*" joker karakterini kullanarak, belgelerin tamamının (alt ağaç) dizin oluşturma işleminden nasıl hariç tutulacağı gösterilmektedir.


    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-indexing"></a>Java SDK v2 'yi eşitleme (Maven com. Microsoft. Azure:: Azure-DocumentDB)

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Daha fazla bilgi için bkz. [Azure Cosmos DB Dizin oluşturma ilkeleri](./index-policy.md).

## <a name="throughput"></a>Aktarım hızı
<a id="measure-rus"></a>

1. **Düşük istek birimleri/ikinci kullanım için ölçme ve ayarlama**

    Azure Cosmos DB, UDF 'ler, saklı yordamlar ve tetikleyicilerle ilişkisel ve hiyerarşik sorgular dahil olmak üzere zengin bir veritabanı işlemleri kümesi sunar ve bunların tümü bir veritabanı koleksiyonu içindeki belgeler üzerinde çalışıyor. Bu işlemlerden her biriyle ilişkilendirilmiş maliyet, işlemi tamamlamak için gereken CPU, GÇ ve belleğe göre değişiklik gösterir. Donanım kaynaklarını düşünmek ve yönetmek yerine, çeşitli veritabanı işlemlerini gerçekleştirmek ve bir uygulama isteğine hizmet vermek için gereken kaynaklar için bir istek birimi (RU) tek bir ölçü olarak düşünebilirsiniz.

    Aktarım hızı, her bir kapsayıcı için ayarlanan [istek birimi](request-units.md) sayısına göre sağlanır. İstek birimi tüketimi, saniye başına bir hız olarak değerlendirilir. Kapsayıcı için sağlanan istek birimi oranını aşan uygulamalar, oran için sağlanan düzeyin altına düşene kadar sınırlandırılır. Uygulamanız daha yüksek bir işleme düzeyi gerektiriyorsa, ek istek birimleri sağlayarak aktarım hızını artırabilirsiniz. 

    Bir sorgunun karmaşıklığı, bir işlem için kaç istek biriminin tüketildiğini etkiler. Koşulların sayısı, koşulların doğası, UDF sayısı ve kaynak veri kümesinin boyutu, sorgu işlemlerinin maliyetini etkiler.

    Herhangi bir işlemin (oluşturma, güncelleştirme veya silme) yükünü ölçmek için, bu işlemler tarafından tüketilen istek birimlerinin sayısını ölçmek üzere [x-MS-Request-ücret](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) üst bilgisini (veya [ \<T> Resourceres,](/java/api/com.microsoft.azure.documentdb.resourceresponse) [feedresponse \<T> ](/java/api/com.microsoft.azure.documentdb.feedresponse) içindeki eşdeğer requestücretler özelliğini) inceleyin.


    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-requestcharge"></a>Java SDK v2 'yi eşitleme (Maven com. Microsoft. Azure:: Azure-DocumentDB)

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    Bu üst bilgide döndürülen istek ücreti, sağlanan aktarım hızının bir bölümü. Örneğin, sağlanan 2000 RU/sn varsa ve önceki sorgu 1000 1KB-belgeler döndürürse, işlem maliyeti 1000 ' dir. Bu nedenle, bir saniye içinde sunucu, sonraki istekleri sınırlayan orandan önce yalnızca iki istek için geçerlidir. Daha fazla bilgi için bkz. [İstek birimleri](request-units.md) ve [istek birimi Hesaplayıcısı](https://www.documentdb.com/capacityplanner).
   <a id="429"></a>
1. **Tanıtıcı hız sınırlandırma/istek hızı çok büyük**

    Bir istemci bir hesap için ayrılan aktarım hızını aşmaya çalıştığında, sunucuda bir performans düşüşü olmaz ve ayrılan düzeyin ötesinde üretilen iş kapasitesi yoktur. Sunucu, isteği RequestRateTooLarge (HTTP durum kodu 429) ile sona erpreemptively ve kullanıcının isteği yeniden denemeden önce beklemesi gereken süreyi milisaniye olarak belirten [x-MS-retry-After-MS](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) üst bilgisini döndürür.
    
    ```xml
        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100
    ```
    SDK 'lar, sunucu tarafından belirtilen yeniden deneme üst bilgisine göre bu yanıtı dolaylı olarak yakalar ve isteği yeniden dener. Hesabınız birden çok istemci tarafından aynı anda erişilmediği takdirde, sonraki yeniden deneme başarılı olur.

    İstek hızının sürekli olarak birden fazla istemciniz varsa, bu, şu anda istemci tarafından dahili olarak 9 olarak ayarlanmış olan varsayılan yeniden deneme sayısı çalışmayabilir; Bu durumda, istemci uygulamaya 429 durum kodu ile bir [Documentclientexception](/java/api/com.microsoft.azure.documentdb.documentclientexception) oluşturur. Varsayılan yeniden deneme sayısı, [Connectionpolicy](/java/api/com.microsoft.azure.documentdb.connectionpolicy) örneğinde [setRetryOptions](/java/api/com.microsoft.azure.documentdb.connectionpolicy.setretryoptions) kullanılarak değiştirilebilir. Varsayılan olarak, durum kodu 429 olan DocumentClientException, istek istek hızının üzerinde çalışmaya devam ederse, 30 saniyelik birikimli bir bekleme süresi dolduktan sonra döndürülür. Bu durum, geçerli yeniden deneme sayısı en fazla yeniden deneme sayısından az olduğunda bile, varsayılan olarak 9 veya Kullanıcı tanımlı bir değer olmalıdır.

    Otomatik yeniden deneme davranışı, çoğu uygulama için esneklik ve kullanılabilirliği artırmaya yardımcı olmakla birlikte, özellikle gecikmeyi ölçirken performans kıyaslamaları yapılırken gürültü 'ye gelebilir.  Deneme sunucu tarafından azaldıysanız, istemci SDK 'sının sessizce yeniden denenmesine neden olursa istemci gözlenen gecikme süresi izlenir. Performans denemeleri sırasında gecikme sürelerini önlemek için, her bir işlem tarafından döndürülen ücreti ölçün ve isteklerin ayrılan istek oranının altında çalıştığından emin olun. Daha fazla bilgi için bkz. [İstek birimleri](request-units.md).
3. **Daha yüksek aktarım hızı için daha küçük dosyalar tasarlayın**

    Belirli bir işlemin istek ücreti (istek işleme maliyeti), belgenin boyutuyla doğrudan bağıntılı olur. Büyük belgelerle ilgili işlemler, küçük belgeler için işlemlerden daha fazla maliyetlidir.

## <a name="next-steps"></a>Sonraki adımlar
Uygulamanızı ölçek ve yüksek performans için tasarlama hakkında daha fazla bilgi için, bkz. [Azure Cosmos DB bölümleme ve ölçeklendirme](partitioning-overview.md).