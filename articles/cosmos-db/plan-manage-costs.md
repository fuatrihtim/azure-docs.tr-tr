---
title: Azure Cosmos DB maliyetlerini planlama ve yönetme
description: Azure portal maliyet analizini kullanarak Azure Cosmos DB maliyetlerini nasıl planlayacağınızı ve yöneteceğinizi öğrenin.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/19/2020
ms.openlocfilehash: 2bea2324817986654de6689a2be15d0cbf999b38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98602133"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Azure Cosmos DB maliyetlerini planlama ve yönetme
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Bu makalede, Azure Cosmos DB maliyetlerini nasıl planlayabileceğinizi ve yönetebileceğiniz açıklanır. İlk olarak, herhangi bir kaynak oluşturmadan önce iş yükü maliyetinizi tahmin etmek için Azure Cosmos DB kapasite hesaplayıcısını kullanırsınız. Daha sonra tahmini maliyeti gözden geçirebilir ve kaynaklarınızı oluşturmaya başlayabilirsiniz.

Azure Cosmos DB kaynaklarını kullanmaya başladıktan sonra, bütçe ayarlamak ve maliyetleri izlemek için maliyet yönetimi özelliklerini kullanın. Ayrıca Tahmini maliyetleri gözden geçirebilir ve hareket etmek isteyebileceğiniz yerleri belirlemek için harcama eğilimlerini belirleyebilirsiniz. Azure Cosmos DB maliyetleri, Azure faturanızda bulunan Aylık maliyetlerin yalnızca bir parçasıdır. Bu makalede Azure Cosmos DB maliyetlerinin nasıl planlanacağı ve yönetileceği açıklanmakta olsa da, üçüncü taraf hizmetler de dahil olmak üzere Azure aboneliğinizde kullanılan tüm Azure hizmetleri ve kaynakları için faturalandırılırsınız.

## <a name="prerequisites"></a>Önkoşullar

### <a name="provisioned-throughput-or-serverless"></a>Sağlanan aktarım hızı veya sunucusuz

Azure Cosmos DB iki tür kapasite modunu destekler: [sağlanan aktarım hızı](set-throughput.md) ve [sunucusuz](serverless.md). Azure Cosmos DB kullanımınız için ücretlendirilebilecek şekilde, bu iki mod arasında çok büyük farklılık gösterir. bu sayede iş yükünüz için en uygun olanı seçmeniz önemlidir. Bu seçimi yapma hakkında yönergeler ve öneriler için [sağlanan aktarım hızı ve sunucusuz makale arasında seçim](throughput-serverless.md) yapma bölümüne bakın.

### <a name="cost-analysis"></a>Maliyet analizi

Maliyet yönetimi 'nde maliyet analizi, çoğu Azure hesap türünü destekler, ancak bunların tümünü kullanmaz. Desteklenen hesap türlerinin tam listesini görüntülemek için, bkz. [Maliyet Yönetimi verilerini anlama](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Maliyet verilerini görüntülemek için bir Azure hesabı için en azından okuma erişiminizin olması gerekir. Azure Maliyet Yönetimi verilerine erişim atama hakkında daha fazla bilgi için bkz. [Verilere erişim atama](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimating-provisioned-throughput-costs-before-using-azure-cosmos-db"></a>Azure Cosmos DB kullanmadan önce sağlanan aktarım hızı maliyetlerini tahmin etme

Sağlanan aktarım hızı modunda Azure Cosmos DB kullanmayı planlıyorsanız, kaynakları bir Azure Cosmos hesabında oluşturmadan önce maliyetleri tahmin etmek için [Azure Cosmos DB kapasite hesaplayıcısını](https://cosmos.azure.com/capacitycalculator/) kullanın. Kapasite hesaplayıcı, iş yükünüzün gerekli aktarım hızını ve maliyetini tahmin etmek için kullanılır. Azure Cosmos veritabanlarınızı ve Kapsayıcılarınızı, iş yükünüz için doğru sağlanan aktarım hızı veya [Istek birimleri (ru/s)](request-units.md)ile yapılandırarak maliyeti ve performansı iyileştirmek için gereklidir. API türü, bölge sayısı, öğe boyutu, saniye başına okuma/yazma isteği, maliyet tahmini almak için depolanan toplam veri gibi ayrıntıları yazmanız gerekir. Kapasite Hesaplayıcı hakkında daha fazla bilgi için bkz. [tahmin](estimate-ru-with-capacity-planner.md) makalesi.

Aşağıdaki ekran görüntüsünde kapasite Hesaplayıcı kullanılarak aktarım hızı ve maliyet tahmini gösterilmektedir:

:::image type="content" source="./media/plan-manage-costs/capacity-calculator-cost-estimate.png" alt-text="Azure Cosmos DB kapasite hesaplayıcıda maliyet tahmini":::

## <a name="estimating-serverless-costs-before-using-azure-cosmos-db"></a><a id="estimating-serverless-costs"></a> Azure Cosmos DB kullanmadan önce sunucusuz maliyetler tahmin ediliyor

Azure Cosmos DB sunucusuz modda kullanmayı planlıyorsanız, aylık olarak kaç [Istek birimi](request-units.md) ve GB depolama alanı kullanacağınızı tahmin etmeniz gerekir. Bir ayda verilecek veritabanı işlemlerinin sayısını değerlendirerek gereken Istek birimi miktarını tahmin edebilir ve bunların tutarını karşılık gelen RU maliyetlerine göre çarpmanız gerekir. Aşağıdaki tabloda, yaygın veritabanı işlemlerine yönelik tahmini RU ücretleri listelenmektedir:

| İşlem | Tahmini maliyet | Notlar |
| --- | --- | --- |
| Öğe oluşturma | 5 ru | Dizin için 5 ' ten az özelliği olan 1 KB 'lık öğe için Ortalama maliyet |
| Öğeyi güncelleştirme | 10 RU | Dizin için 5 ' ten az özelliği olan 1 KB 'lık öğe için Ortalama maliyet |
| Tek bir öğeyi KIMLIĞE ve bölüm anahtarına göre oku (nokta-oku) | 1 RU | 1 KB 'lık öğe için Ortalama maliyet |
| Öğeyi silme | 5 ru | |
| Sorgu yürütme | 10 RU | [Dizin oluşturmanın](index-overview.md) tam avantajlarından yararlanan ve 100 sonucu veya daha az döndüren bir sorgu için Ortalama maliyet |

> [!IMPORTANT] 
> Yukarıdaki tablodaki notlara dikkat edin. Operasyonlarınızın gerçek maliyetlerinin daha doğru bir şekilde tahmin etmek için [Azure Cosmos DB öykünücüsünü](local-emulator.md) kullanabilir ve [OPERASYONLARıNıZıN tam ru maliyetini ölçebilirsiniz](find-request-unit-charge.md). Azure Cosmos DB öykünücüsü sunucusuz 'yi desteklemediğinden, veritabanı işlemleri için standart RU ücreti rapor edebilir ve bu tahmin için kullanılabilir.

Bir ay boyunca tükettiğiniz toplam Istek birimi ve GB depolama alanı sayısını hesapladıktan sonra, aşağıdaki formül maliyet tahmininizi döndürür: **([istek birimi sayısı]/1.000.000 * $0,25) + ([GB depolama] * $0,25)**.

> [!NOTE]
> Önceki örnekte gösterilen maliyetler yalnızca tanıtım amaçlıdır. En son fiyatlandırma bilgileri için [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/cosmos-db/) bakın.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Azure portalında tahmini maliyetleri gözden geçirme

Azure portal Azure Cosmos DB kaynaklarını kullanmaya başladığınızda Tahmini maliyetleri görebilirsiniz. Maliyet tahminini gözden geçirmek için aşağıdaki adımları kullanın:

1. Azure portal oturum açın ve Azure Cosmos hesabınıza gidin.
1. **Genel bakış** bölümüne gidin.
1. Alttaki **Maliyet** grafiğini kontrol edin. Bu grafik, yapılandırılabilir bir dönemdeki geçerli maliyetinizi gösterir:
1. Grafik kapsayıcısı gibi yeni bir kapsayıcı oluşturun.
1. İş yükünüz için gereken aktarım hızını girin. Örneğin, 400 RU/s. Üretilen iş değerini girdikten sonra, fiyatlandırma tahminini aşağıdaki ekran görüntüsünde gösterildiği gibi görebilirsiniz:

   :::image type="content" source="./media/plan-manage-costs/cost-estimate-portal.png" alt-text="Azure portal maliyet tahmini":::

Azure aboneliğinizin harcama limiti varsa, Azure kredi miktarınızdan harcamanıza engel olur. Azure kaynaklarını oluştururken ve kullanırken kredileriniz kullanılır. Kredi sınırınıza ulaştığınızda, dağıttığınız kaynaklar bu fatura döneminin geri kalanı için devre dışı bırakılır. Kredi sınırınızı değiştiremezsiniz, ancak kaldırabilirsiniz. Harcama limitleri hakkında daha fazla bilgi için bkz. [Azure harcama limiti](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Azure ön ödeyiniz (daha önce parasal taahhüt olarak adlandırılır) krediyle Azure Cosmos DB ücretleri için ödeme yapabilirsiniz. Ancak Azure ön ödeme kredisi, Azure Marketi 'nden dahil olan üçüncü taraf ürün ve hizmetlerine yönelik ücretler için ödeme yapmak üzere kullanamazsınız.

## <a name="monitor-costs"></a>Maliyetleri izleme

Azure Cosmos DB ile kaynakları kullanırken maliyetleriniz vardır. Kaynak kullanımı birim maliyetleri, zaman aralıklarına (saniye, dakika, saat ve gün) veya istek birimi kullanımına göre değişir. Azure Cosmos DB kullanımı başladığı anda, maliyetler ücretlendirilir ve bunları Azure portal [Maliyet Analizi](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) bölmesinde görebilirsiniz.

Maliyet analizini kullandığınızda, grafik ve tablolardaki Azure Cosmos DB maliyetlerini farklı zaman aralıkları için görüntüleyebilirsiniz. Bazı örnekler güne, geçerli, önceki aya ve yıla göre yapılır. Ayrıca bütçeleri ve tahmin edilen maliyetlerden de maliyet görüntüleyebilirsiniz. Zamana göre daha uzun görünümlere geçiş yapmak, harcama eğilimlerini belirlemenize ve fazla harcama oluşmuş olabileceğini görmenizi sağlar. Bütçeleri oluşturduysanız, nerede aşıldığını da kolayca görebilirsiniz. 

Maliyet analizinde Azure Cosmos DB maliyetlerini görüntülemek için:

1. [Azure portal](https://portal.azure.com) oturum açın.

1. Azure portal kapsamı açın ve menüden **Maliyet Analizi** ' ni seçin. Örneğin, **abonelikler**' e gidin, listeden bir abonelik seçin ve ardından menüde  **Maliyet Analizi** ' ni seçin. Maliyet analizinde farklı bir kapsama geçiş yapmak için **kapsam** ' ı seçin.

1. Varsayılan olarak, tüm hizmetlerin maliyeti ilk halka grafiğinde gösterilir. Grafikteki "Azure Cosmos DB" etiketli alanı seçin.

1. Azure Cosmos DB gibi tek bir hizmetin maliyetlerini daraltmak için, **Filtre Ekle** ' yi ve ardından **hizmet adı**' nı seçin. Sonra listeden **Azure Cosmos DB** öğesini seçin. Yalnızca Azure Cosmos DB maliyetlerini gösteren bir örnek aşağıda verilmiştir:
 
   :::image type="content" source="./media/plan-manage-costs/cost-analysis-pane.png" alt-text="Maliyet Analizi bölmesi ile maliyetleri izleme":::

Yukarıdaki örnekte, Şubat ayı için geçerli Azure Cosmos DB maliyeti görürsünüz. Grafiklerde Ayrıca konuma ve kaynak grubuna göre Azure Cosmos DB maliyetleri de bulunur.

## <a name="create-budgets"></a>Bütçe oluşturma

Maliyetleri yönetmek için [bütçeler](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oluşturabilir ve paydaşları harcama anormallikleri ve fazla harcama riskleri ile ilgili otomatik olarak bilgilendiren [uyarılar](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oluşturabilirsiniz. Uyarılar, bütçe ve maliyet eşiklerine kıyasla harcamaya göre belirlenir. Bütçeler ve uyarılar Azure abonelikleri ve kaynak grupları için oluşturulur, bu nedenle genel maliyet izleme stratejisinin bir parçası olarak faydalıdır. 

İzlemekte daha fazla ayrıntı düzeyi istiyorsanız, Azure 'daki belirli kaynaklara veya hizmetlere yönelik filtrelerle bütçeler oluşturulabilir. Filtreler, yanlışlıkla ek para maliyeti sağlayan yeni kaynaklar oluşturmamasını sağlamaya yardımcı olur. Bir bütçe oluştururken filtre seçenekleri hakkında daha fazla bilgi için bkz. [Grup ve filtre seçenekleri](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Maliyet verilerini dışarı aktarma

Ayrıca, maliyet verilerinizi bir depolama hesabına [dışarı aktarabilirsiniz](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) . Bu, maliyetler için ek veri analizi yapmanız gerektiğinde veya başkalarının yararlı olması durumunda faydalıdır. Örneğin, bir finans ekipleri, Excel veya Power BI kullanarak verileri analiz edebilir. Maliyetlerinizi günlük, haftalık veya aylık bir zamanlamaya göre dışa aktarabilir ve özel bir tarih aralığı ayarlayabilirsiniz. Maliyet verilerini dışarı aktarmak, maliyet veri kümelerini almak için önerilen yoldur.

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos DB ' de fiyatlandırmanın nasıl çalıştığı hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

* [Azure Cosmos DB’de modeli fiyatlandırma](how-pricing-works.md)
* [Azure Cosmos DB’de sağlanan işlem hızını iyileştirme](optimize-cost-throughput.md)
* [Azure Cosmos DB'de sorgu maliyetini iyileştirme](./optimize-cost-reads-writes.md)
* [Azure Cosmos DB'de depolama maliyetini iyileştirme](optimize-cost-storage.md)
* [Azure maliyet yönetimi ile bulut yatırımınızın nasıl iyileştirileceği hakkında](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)bilgi edinin.
* [Maliyet Analizi](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)ile maliyetleri yönetme hakkında daha fazla bilgi edinin.
* [Beklenmeyen maliyetlerin nasıl önleneceği](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)hakkında bilgi edinin.
* [Maliyet yönetimi](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) destekli öğrenme kursuna katılın.