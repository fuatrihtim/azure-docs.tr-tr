---
title: "Öğretici: MongoDB 'yi MongoDB için Azure Cosmos DB API 'sine çevrimdışına geçirme"
titleSuffix: Azure Database Migration Service
description: Azure veritabanı geçiş hizmeti 'ni kullanarak şirket içi MongoDB 'den MongoDB için Azure Cosmos DB API 'sine geçiş yapmayı öğrenin.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 02/03/2021
ms.openlocfilehash: b669870537ffb58d9ae7e8a5c65276d310ba6a7e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101722033"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-dms"></a>Öğretici: DMS kullanarak MongoDB 'yi Azure Cosmos DB, MongoDB 'ye çevrimdışı geçirme

Azure veritabanı geçiş hizmeti 'ni, MongoDB 'nin şirket içi veya bulut örneğinden MongoDB 'nin API 'sine Azure Cosmos DB bir çevrimdışı (tek seferlik) geçişi gerçekleştirmek için kullanabilirsiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
>
> * Azure Veritabanı Geçiş Hizmeti örneği oluşturun.
> * Azure veritabanı geçiş hizmeti 'ni kullanarak bir geçiş projesi oluşturun.
> * Geçişi çalıştırma.
> * Geçişi izleme.

Bu öğreticide, Azure veritabanı geçiş hizmeti 'ni kullanarak bir Azure sanal makinesinde barındırılan MongoDB 'deki bir veri kümesini Azure Cosmos DB MongoDB için API 'sine geçireceğiniz. Zaten bir MongoDB kaynağınız yoksa, [Azure 'Da WINDOWS VM 'de MongoDB 'Yi kurma ve yapılandırma](/previous-versions/azure/virtual-machines/windows/install-mongodb)makalesine bakın.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakileri yapmanız gerekir:

* Aktarım hızını tahmin etme, bölüm anahtarı seçme ve dizin oluşturma ilkesi gibi [geçiş öncesi adımları doldurun](../cosmos-db/mongodb-pre-migration.md) .
* [MongoDB hesabı için bir Azure Cosmos DB API 'Si oluşturun](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* [ExpressRoute](../expressroute/expressroute-introduction.md) veya [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)kullanarak şirket içi kaynak sunucularınıza siteden siteye bağlantı sağlayan Azure Resource Manager dağıtım modelini kullanarak Azure veritabanı geçiş hizmeti için bir Microsoft Azure sanal ağ oluşturun. Sanal ağ oluşturma hakkında daha fazla bilgi için [sanal ağ belgelerine](../virtual-network/index.yml)ve özellikle adım adım ayrıntılarla birlikte hızlı başlangıç makalelerine bakın.

    > [!NOTE]
    > Sanal ağ kurulumu sırasında, Microsoft 'a ağ eşlemesi ile ExpressRoute kullanırsanız, hizmetin sağlanacağı alt ağa aşağıdaki hizmet [uç noktalarını](../virtual-network/virtual-network-service-endpoints-overview.md) ekleyin:
    >
    > * Hedef veritabanı uç noktası (örneğin, SQL uç noktası, Cosmos DB uç noktası vb.)
    > * Depolama uç noktası
    > * Service Bus uç noktası
    >
    > Azure veritabanı geçiş hizmeti internet bağlantısı olmadığından bu yapılandırma gereklidir.

* Sanal ağ ağ güvenlik grubu (NSG) kurallarınızın şu iletişim bağlantı noktalarını engellemediğinden emin olun: 53, 443, 445, 9354 ve 10000-20000. Sanal ağ NSG trafik filtrelemesi hakkında daha fazla bilgi için ağ [güvenlik grupları ile ağ trafiğini filtreleme](../virtual-network/virtual-network-vnet-plan-design-arm.md)makalesine bakın.
* Azure veritabanı geçiş hizmeti 'nin kaynak MongoDB sunucusuna erişmesine izin vermek için Windows Güvenlik duvarınızı açın, varsayılan olarak TCP bağlantı noktası 27017 ' dir.
* Kaynak veritabanınızın önünde bir güvenlik duvarı gereci kullanırken, Azure veritabanı geçiş hizmeti 'nin geçiş için kaynak veritabanına erişmesine izin vermek üzere güvenlik duvarı kuralları eklemeniz gerekebilir.

## <a name="configure-azure-cosmos-db-server-side-retries-for-efficient-migration"></a>Verimli geçiş için Azure Cosmos DB sunucu tarafı yeniden denemelerini yapılandırma

MongoDB 'den geçiş yapan müşteriler kaynak idare özelliğinden Azure Cosmos DB avantajdan faydalanabilir. Azure Cosmos DB, bu istek, sağlanan RU/s kapsayıcısını aşarsa geçiş sırasında verilen bir veri geçiş hizmeti isteğini kısıtlayabilir daha sonra bu isteğin yeniden denenilmesi gerekir. Veri geçiş hizmeti yeniden denemeler gerçekleştirebilir, ancak veri geçiş hizmeti ve Azure Cosmos DB arasındaki ağ atlamada yer alan gidiş dönüş süresi, bu isteğin genel yanıt süresini etkiler. Kısıtlanmış istekler için yanıt süresini iyileştirmek, geçiş için gereken toplam süreyi kısaltabilir. *Sunucu tarafı yeniden deneme* özelliği Azure Cosmos DB, hizmetin kısıtlama hata kodlarını kesmesini ve daha sonra istek yanıt sürelerini önemli ölçüde iyileştirerek çok daha düşük bir gidiş dönüş süresi ile yeniden denemesini sağlar.

Sunucu tarafı yeniden deneme özelliğini Azure Cosmos DB portalının *Özellikler* dikey penceresinde bulabilirsiniz.

![MongoDB SSR özelliği](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

*Devre dışıysa*, aşağıda gösterildiği gibi etkinleştirmenizi öneririz

![MongoDB SSR etkinleştir](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration kaynak sağlayıcısını kaydetme

1. Azure portal'da oturum açın, **Tüm hizmetler**'i ve ardından **Abonelikler**'i seçin.

   ![Portal aboneliklerini gösterme](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)

2. Azure Veritabanı Geçiş Hizmeti örneğini oluşturmak istediğiniz aboneliği seçin ve sonra **Kaynak sağlayıcıları**’nı seçin.

    ![Kaynak sağlayıcılarını gösterme](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)

3. "migration" araması yapın ve **Microsoft.DataMigration** öğesinin sağ tarafındaki **Kaydet**'i seçin.

    ![Kaynak sağlayıcısını kaydetme](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Örnek oluşturma

1. Azure portal + **kaynak oluştur**' u seçin, Azure veritabanı geçiş hizmeti ' ni arayın ve ardından açılan listeden **Azure veritabanı geçiş hizmeti** ' ni seçin.

    ![Azure Market](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. **Azure Veritabanı Geçiş Hizmeti** ekranında **Oluştur**'u seçin.

    ![Azure Veritabanı Geçiş Hizmeti örneğini oluşturma](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. **Geçiş Hizmeti oluşturun** ekranında hizmet için bir ad belirtin, aboneliği ve yeni ya da var olan bir kaynak grubunu seçin.

4. Azure veritabanı geçiş hizmeti örneğini oluşturmak istediğiniz konumu seçin. 

5. Var olan bir sanal ağı seçin veya yeni bir ağ oluşturun.

    Sanal ağ, kaynak MongoDB örneğine ve hedef Azure Cosmos DB hesabına erişimi olan Azure veritabanı geçiş hizmeti sağlar.

    Azure portal sanal ağ oluşturma hakkında daha fazla bilgi için [Azure Portal kullanarak sanal ağ oluşturma](../virtual-network/quick-create-portal.md)makalesine bakın.

6. Fiyatlandırma katmanını seçin.

    Maliyetler ve fiyatlandırma katmanları hakkında daha fazla bilgi için [fiyatlandırma sayfasına](https://aka.ms/dms-pricing) bakın.

    ![Azure Veritabanı Geçiş Hizmeti örneği ayarlarını yapılandırma](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. Hizmeti oluşturmak için **Oluştur**’u seçin.

## <a name="create-a-migration-project"></a>Geçiş projesi oluşturma

Hizmet oluşturulduktan sonra Azure portaldan bulun, açın ve yeni bir geçiş projesi oluşturun.

1. Azure portalda **Tüm hizmetler**'i seçin, Azure Veritabanı Geçiş Hizmeti araması yapın ve **Azure Veritabanı Geçiş Hizmeti**'ni seçin.

      ![Azure veritabanı geçiş hizmeti 'nin tüm örneklerini bulun](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. **Azure veritabanı geçiş Hizmetleri** ekranında, oluşturduğunuz Azure veritabanı geçiş hizmeti örneğinin adını arayın ve ardından örneği seçin.

3. +**Yeni Geçiş Projesi**'ni seçin.

4. **Yeni geçiş projesi** ekranında, proje için bir ad belirtin, **kaynak sunucu türü** metin kutusunda **MongoDB**' yi seçin, **hedef sunucu türü** metın kutusunda **cosmosdb (MongoDB API)** öğesini seçin ve ardından **etkinlik türü seç**' i seçin, **çevrimdışı veri geçişi**' ni seçin. 

    ![Veritabanı geçiş hizmeti projesi oluştur](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. Projeyi oluşturmak ve geçiş etkinliğini çalıştırmak için **Etkinlik oluştur ve çalıştır**'ı seçin.

## <a name="specify-source-details"></a>Kaynak ayrıntılarını belirtme

1. **Kaynak ayrıntıları** ekranında, kaynak MongoDB sunucusu için bağlantı ayrıntılarını belirtin.

   > [!IMPORTANT]
   > Azure veritabanı geçiş hizmeti kaynak olarak Azure Cosmos DB desteklemez.

    Bir kaynağa bağlanmak için üç mod vardır:
   * Tam etki alanı adı veya IP adresi, bağlantı noktası numarası ve bağlantı kimlik bilgilerini kabul eden **Standart mod**.
   * Makale [bağlantı DIZESI URI biçimi](https://docs.mongodb.com/manual/reference/connection-string/)bölümünde açıklandığı gibi MongoDB bağlantı dizesini kabul eden **bağlantı dizesi modu**.
   * Blob kapsayıcı SAS URL 'sini kabul eden **Azure Storage verileri**. Blob kapsayıcısının MongoDB [bsondump aracı](https://docs.mongodb.com/manual/reference/program/bsondump/)tarafından üretilen bSon dökümlerini içermesi ve kapsayıcıda JSON dosyaları varsa onu yeniden seçmesini istiyorsanız **BLOB** ' u seçin.

     Bu seçeneği belirlerseniz, depolama hesabı bağlantı dizesinin şu biçimde göründüğünden emin olun:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Bu blob kapsayıcısı SAS bağlantı dizesi, Azure Depolama Gezgini 'nde bulunabilir. İlgili kapsayıcı için SAS oluşturmak, size istenen biçimdeki URL 'YI sağlar.
     
     Ayrıca, Azure Storage 'daki tür dökümü bilgilerine göre aşağıdaki ayrıntıyı göz önünde bulundurun.

     * BSON dökümler için, blob kapsayıcısı içindeki verilerin bsondump biçiminde olması gerekir, bu nedenle veri dosyaları koleksiyon. bSon biçiminde kapsayan veritabanlarının ardından adlandırılan klasörlere yerleştirilir. Meta veri dosyaları (varsa), üzerinde.metadata.jsbiçim *koleksiyonu* kullanılarak adlandırılmalıdır.

     * JSON dökümlerinde, blob kapsayıcısındaki dosyaların, kapsayan veritabanlarının ardından adlandırılan klasörlere yerleştirilmesi gerekir. Her veritabanı klasörü içinde, veri dosyalarının "Data" adlı bir alt klasöre yerleştirilmesi ve *Collection*. JSON biçimi kullanılarak adlandırılması gerekir. Meta veri dosyaları (varsa), "metadata" adlı bir alt klasöre yerleştirilmelidir ve aynı biçim olan *Collection*. JSON kullanılarak adlandırılmalıdır. Meta veri dosyaları MongoDB bsondump aracı tarafından oluşturulan biçimde olmalıdır.

    > [!IMPORTANT]
    > Mongo sunucusunda kendinden imzalı bir sertifika kullanılması önerilmez. Ancak, bir tane kullanılmışsa **bağlantı dizesi modunu** kullanarak sunucuya bağlanın ve bağlantı dizeniz "" olduğundan emin olun
    >
    >```
    >&sslVerifyCertificate=false
    >```

   DNS ad çözümlemenin mümkün olmadığı durumlarda IP Adresini de kullanabilirsiniz.

   ![Kaynak ayrıntılarını belirtme](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. **Kaydet**’i seçin.

## <a name="specify-target-details"></a>Hedef ayrıntılarını belirtme

1. **Geçiş hedefi ayrıntıları** ekranında, MongoDB verilerinizi geçirdiğiniz MongoDB hesabı için önceden sağlanmış Azure Cosmos DB API 'si olan hedef Azure Cosmos DB hesabının bağlantı ayrıntılarını belirtin.

    ![Hedef ayrıntılarını belirtme](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. **Kaydet**’i seçin.

## <a name="map-to-target-databases"></a>Hedef veritabanlarıyla eşleyin

1. **Hedef veritabanlarına eşle** ekranında, geçiş için kaynağı ve hedef veritabanını eşleyin.

    Hedef veritabanı, kaynak veritabanıyla aynı veritabanı adını içeriyorsa, Azure veritabanı geçiş hizmeti varsayılan olarak hedef veritabanını seçer.

    Dize **oluşturma** veritabanı adının yanında görünürse, Azure veritabanı geçiş hizmeti 'nin hedef veritabanını bulmadığını ve hizmetin sizin için veritabanını oluşturacağını belirtir.

    Geçişin bu noktasında [üretilen iş sağlama](../cosmos-db/set-throughput.md)sağlayabilirsiniz. Cosmos DB ' de, her koleksiyon için veritabanı düzeyinde veya ayrı ayrı üretilen iş sağlama sağlayabilirsiniz. Aktarım hızı, [Istek birimleri](../cosmos-db/request-units.md) (ru) cinsinden ölçülür. [Azure Cosmos DB fiyatlandırması](https://azure.microsoft.com/pricing/details/cosmos-db/)hakkında daha fazla bilgi edinin.

    ![Hedef veritabanlarıyla eşleyin](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. **Kaydet**’i seçin.
3. **Koleksiyon ayarı** ekranında koleksiyonlar listesini genişletin ve geçirilecek koleksiyonların listesini gözden geçirin.

    Azure veritabanı geçiş hizmeti, hedef Azure Cosmos DB hesabında mevcut olmayan kaynak MongoDB örneğinde mevcut olan tüm koleksiyonları otomatik olarak seçer. Zaten veri içeren koleksiyonları yeniden geçirmek istiyorsanız, bu dikey penceredeki koleksiyonları açıkça seçmeniz gerekir.

    Koleksiyonların kullanmasını istediğiniz RUs miktarını belirtebilirsiniz. Azure veritabanı geçiş hizmeti, koleksiyon boyutuna bağlı olarak akıllı varsayılanlar önerir.

    > [!NOTE]
    > Gerekirse, çalışmayı hızlandırmak için Azure veritabanı geçiş hizmeti 'nin birden çok örneğini kullanarak veritabanı geçişini ve toplamayı paralel olarak gerçekleştirin.

    Ayrıca, en iyi ölçeklenebilirlik için [Azure Cosmos DB bölümlemeden](../cosmos-db/partitioning-overview.md) yararlanmak üzere bir parça anahtarı belirtebilirsiniz. Parça  [/bölüm anahtarı seçmek için en iyi uygulamaları](../cosmos-db/partitioning-overview.md#choose-partitionkey)gözden geçirdiğinizden emin olun.

    ![Koleksiyon tablolarını Seç](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. **Kaydet**’i seçin.

5. **Geçiş özeti** ekranının **Etkinlik adı** metin kutusunda geçiş etkinliği için bir ad belirtin.

    ![Geçiş özeti](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Geçişi çalıştırma

* **Geçişi çalıştır**'ı seçin.

    Geçiş etkinliği penceresi görüntülenir ve etkinliğin **durumu** **başlatılmaz**.

    ![Etkinlik durumu](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Geçişi izleme

* Geçiş etkinliği ekranında **Yenile**'yi seçerek geçişin **Durum** bilgisi **Tamamlandı** olana kadar gösterilen verileri güncelleştirebilirsiniz.

   > [!NOTE]
   > Veritabanı ve koleksiyon düzeyinde geçiş ölçümlerinin ayrıntılarını almak için etkinliğini seçebilirsiniz.

    ![Etkinlik durumu tamamlandı](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Cosmos DB verileri doğrulama

* Geçiş tamamlandıktan sonra, tüm koleksiyonların başarıyla geçirildiğini doğrulamak için Azure Cosmos DB hesabınızı kontrol edebilirsiniz.

    ![Tüm koleksiyonların başarıyla geçirildiğini doğrulamak üzere Azure Cosmos DB hesabınızın nerede kontrol ettiğini gösteren ekran görüntüsü.](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>Geçiş sonrası iyileştirmesi

MongoDB veritabanında depolanan verileri MongoDB için Azure Cosmos DB API 'sine geçirdikten sonra, Azure Cosmos DB bağlanabilir ve verileri yönetebilirsiniz. Ayrıca, dizin oluşturma ilkesini iyileştirmek, varsayılan tutarlılık düzeyini güncelleştirmek veya Azure Cosmos DB hesabınız için genel dağıtımı yapılandırmak gibi geçiş sonrası en iyi duruma getirme adımlarını da gerçekleştirebilirsiniz. Daha fazla bilgi için, [geçiş sonrası iyileştirme](../cosmos-db/mongodb-post-migration.md) makalesine bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* [Cosmos DB hizmeti bilgileri](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Sonraki adımlar

* Microsoft [veritabanı geçiş kılavuzu](https://datamigration.microsoft.com/)'ndaki ek senaryolar için geçiş kılavuzunu gözden geçirin.