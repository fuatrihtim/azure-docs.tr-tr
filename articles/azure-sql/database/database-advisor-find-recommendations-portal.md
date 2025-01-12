---
title: Performans önerilerini uygulama
description: Veritabanınızın performansını iyileştirebilmenizi sağlayan performans önerilerini bulmak için Azure portal kullanın.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 12/19/2018
ms.openlocfilehash: 748ac448ad8bf5c06e5be8b7a4a8b00a9b7af84b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96500895"
---
# <a name="find-and-apply-performance-recommendations"></a>Performans önerilerini bulma ve uygulama
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL veritabanı 'nda veritabanınızın performansını iyileştirebilecek veya iş yükünüzün tanımlı bir sorunu düzeltmek için Azure portal kullanabilirsiniz. Azure portal **performans önerisi** sayfası, olası etkilerine göre en iyi önerileri bulmanıza olanak sağlar.

## <a name="viewing-recommendations"></a>Önerileri görüntüleme

Performans önerilerini görüntülemek ve uygulamak için Azure 'da doğru [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md) izinlerine ihtiyacınız vardır. **Okuyucu**, **SQL DB katkıda bulunan** Izinleri, önerileri ve **sahibini** görüntülemek Için gereklidir; **SQL DB katkıda bulunan** izinleri tüm eylemleri yürütmek için gereklidir; dizinler oluşturun veya bırakın ve Dizin oluşturmayı iptal edin.

Azure portal performans önerilerini bulmak için aşağıdaki adımları kullanın:

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. **Tüm hizmetler**  >  **SQL veritabanlarına** gidin ve veritabanınızı seçin.
3. Seçilen veritabanı için kullanılabilir önerileri görüntülemek için **performans önerisi** ' ne gidin.

Performans önerileri aşağıdaki şekilde gösterildiği gibi tabloda gösterilmiştir:

![Ekran görüntüsü, eylem ve öneri açıklamasıyla bir tablodaki performans önerilerini gösterir.](./media/database-advisor-find-recommendations-portal/recommendations.png)

Öneriler, performans üzerindeki olası etkileriyle aşağıdaki kategorilere göre sıralanır:

| Etki | Description |
|:--- |:--- |
| Yüksek |Yüksek etki önerileri en önemli performans etkisini sağlamalıdır. |
| Orta |Orta etki önerileri performansı artırmalı ancak önemli ölçüde kullanılmamalıdır. |
| Düşük |Düşük etki önerileri, olmadan daha iyi performans sağlamalıdır, ancak iyileştirmeler önemli olmayabilir. |

> [!NOTE]
> Azure SQL veritabanı, bazı önerilerin tanımlanması için etkinlikleri en az bir gün boyunca izlemeye ihtiyaç duyuyor. Azure SQL veritabanı, tutarlı sorgu desenleri için daha kolay iyileştirebilmenizi sağlayabilir. Öneriler şu anda kullanılabilir değilse, **performans önerisi** sayfası nedenini açıklayan bir ileti sağlar.

Ayrıca, geçmiş işlemlerin durumunu da görüntüleyebilirsiniz. Daha fazla bilgi görmek için bir öneri veya durum seçin.

Aşağıda, Azure portal "Dizin oluşturma" önerisi örneği verilmiştir.

![Dizin Oluştur](./media/database-advisor-find-recommendations-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Öneriler uygulanıyor

Azure SQL veritabanı, aşağıdaki üç seçenekten birini kullanarak önerilerin nasıl etkinleştirildiği konusunda tam denetim sağlar:

* Her defasında tek tek öneriler uygulayın.
* Otomatik ayarlamayı otomatik olarak önerileri uygulayacak şekilde etkinleştirin.
* Bir öneriyi el ile uygulamak için, veritabanınıza karşı önerilen T-SQL betiğini çalıştırın.

Ayrıntılarını görüntülemek için herhangi bir öneri seçin ve sonra önerilerin nasıl oluşturulduğuna ilişkin tam ayrıntıları gözden geçirmek için **betiği görüntüle** ' ye tıklayın.

Öneri uygulanırken veritabanı çevrimiçi kalır; performans önerisi veya otomatik ayarlama kullanılması hiçbir zaman bir veritabanını çevrimdışı duruma getirir.

### <a name="apply-an-individual-recommendation"></a>Tek bir öneri uygulayın

Önerileri tek seferde gözden geçirebilir ve kabul edebilirsiniz.

1. **Öneriler** sayfasında, bir öneri seçin.
2. **Ayrıntılar** sayfasında **Uygula** düğmesine tıklayın.

   ![Öneriyi Uygula](./media/database-advisor-find-recommendations-portal/apply.png)

Seçili öneriler veritabanına uygulandı.

### <a name="removing-recommendations-from-the-list"></a>Listeden öneriler kaldırılıyor

Önerileriniz listeniz listeden kaldırmak istediğiniz öğeleri içeriyorsa, öneriyi atabilirsiniz:

1. **Öneriler** listesinde, ayrıntıları açmak için bir öneri seçin.
2. **Ayrıntılar** sayfasında **at** ' a tıklayın.

İsterseniz, atılan öğeleri **öneriler** listesine geri ekleyebilirsiniz:

1. **Öneriler** sayfasında, **Görünüm atıldı**' ı tıklatın.
2. Ayrıntılarını görüntülemek için listeden atılan bir öğe seçin.
3. İsteğe bağlı olarak, dizini **önerilerin** ana listesine geri eklemek Için almayı **geri al** ' a tıklayın.

> [!NOTE]
> SQL veritabanı [otomatik ayarlama](automatic-tuning-overview.md) etkinse ve listeden bir öneriyi el ile iptal ederseniz bu tür bir öneri otomatik olarak uygulanmaz. Bir öneriyi atmak, kullanıcıların belirli bir öneriyi uygulamak zorunda kalmadan otomatik ayarlama özelliğinin etkinleştirilmesi için kullanışlı bir yoldur.
> Geri alma atma seçeneğini belirleyerek, öneriler listesine geri atılan öneriler ekleyerek bu davranışı geri döndürebilirsiniz.

### <a name="enable-automatic-tuning"></a>Otomatik ayarlamayı etkinleştirme

Veritabanınızı, önerileri otomatik olarak uygulayacak şekilde ayarlayabilirsiniz. Öneriler kullanılabilir hale geldiğinde, bunlar otomatik olarak uygulanır. Hizmet tarafından yönetilen tüm önerilerden itibaren, performans etkisi negatifse, öneri geri döndürülür.

1. **Öneriler** sayfasında **otomatikleştir**' e tıklayın:

   ![Danışman ayarları](./media/database-advisor-find-recommendations-portal/settings.png)
2. Otomatikleştirebileceğiniz eylemleri seçin:

   ![Otomatikleştirebileceğiniz eylemlerin nerede seçdiğinin gösterildiği ekran görüntüsü.](./media/database-advisor-find-recommendations-portal/server.png)

> [!NOTE]
> **DROP_INDEX** seçeneğinin Şu anda bölüm değiştirme ve Dizin ipuçları kullanan uygulamalarla uyumlu olmadığına lütfen unutmayın.

İstediğiniz yapılandırmayı seçtikten sonra Uygula ' ya tıklayın.

### <a name="manually-apply-recommendations-through-t-sql"></a>T-SQL aracılığıyla önerileri el ile uygulama

Herhangi bir önerisi seçin ve ardından **betiği görüntüle**' ye tıklayın. Öneriyi el ile uygulamak için bu betiği veritabanınıza çalıştırın.

*El ile yürütülen dizinler izlenmez ve hizmetin performans etkisi için doğrulanmamıştır* . bu sayede, performans kazancı sağlamalarını ve gerekirse bunları ayarlamayı veya silmeyi doğrulamak üzere oluşturulduktan sonra bu dizinleri izlemeniz önerilir. Dizinleri oluşturma hakkında ayrıntılı bilgi için bkz. [Dizin oluşturma (Transact-SQL)](/sql/t-sql/statements/create-index-transact-sql). Ayrıca, el ile uygulanan öneriler etkin kalır ve 24-48 saat önerisi listesinde gösterilir. sistemi otomatik olarak çizmadan önce. Bir öneriyi daha erken kaldırmak isterseniz, el ile atabilirsiniz.

### <a name="canceling-recommendations"></a>Öneriler iptal ediliyor

**Bekleyen**, **doğrulama** veya **başarı** durumundaki öneriler iptal edilebilir. **Yürütme** durumu ile ilgili öneriler iptal edilemez.

1. Öneri **ayrıntıları** sayfasını açmak Için **ayarlama geçmişi** alanında bir öneri seçin.
2. Öneriyi uygulama işlemini durdurmak için **iptal** ' e tıklayın.

## <a name="monitoring-operations"></a>İzleme işlemleri

Öneri uygulamak anında gerçekleşmeyebilir. Portal, önerinin durumuyla ilgili ayrıntıları sağlar. Aşağıda, bir dizinin bulunabileceği durumlar olabilir:

| Durum | Açıklama |
|:--- |:--- |
| Beklemede |Öneri Uygula komutu alındı ve yürütme için zamanlandı. |
| Gerçekleştirilirken |Öneri uygulanıyor. |
| Doğrulamada |Öneri başarıyla uygulandı ve hizmet avantajları ölçdi. |
| Başarılı |Öneri başarıyla uygulandı ve avantajlar ölçülüyor. |
| Hata |Öneriyi uygulama işlemi sırasında bir hata oluştu. Bu geçici bir sorun olabilir veya tabloda bir şema değişikliği olabilir ve betik artık geçerli değildir. |
| Geri alınıyor |Öneri uygulandı, ancak performans dışı olarak kabul edildi ve otomatik olarak geri döndürülüyor. |
| Çevrildi |Öneri geri döndürüldü. |

Daha fazla bilgi için listeden bir işlem içi öneriye tıklayın:

![İşlem içi önerilerin listesini gösteren ekran görüntüsü.](./media/database-advisor-find-recommendations-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Öneriyi geri alma

Öneriyi uygulamak için performans önerilerini kullandıysanız (T-SQL betiğini el ile çalıştırmadınız), performans etkisini negatif olarak bulursa değişikliği otomatik olarak döndürür. Yalnızca bir öneriyi dönmek istiyorsanız, şunları yapabilirsiniz:

1. **Ayarlama geçmişi** alanında başarıyla uygulanan bir öneri seçin.
2. **Öneri ayrıntıları** sayfasında, **dön** ' e tıklayın.

![Önerilen dizinler](./media/database-advisor-find-recommendations-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Dizin önerilerinin performans etkisini izleme

Öneriler başarıyla uygulandıktan sonra (Şu anda, dizin işlemleri ve parametrelenen sorgu önerileri), [sorgu performansı öngörülerini](query-performance-insight-use.md) açmak ve en iyi sorguların performans etkisini görmek için öneri ayrıntıları sayfasında **sorgu öngörüleri** ' ne tıklayabilirsiniz.

![Performans etkisini izleme](./media/database-advisor-find-recommendations-portal/query-insights.png)

## <a name="summary"></a>Özet

Azure SQL veritabanı, veritabanı performansını iyileştirmeye yönelik öneriler sağlar. T-SQL betikleri sunarak, veritabanınızı iyileştirmek ve sonuç olarak sorgu performansını iyileştirmek için yardım alın.

## <a name="next-steps"></a>Sonraki adımlar

Önerilerinizi izleyin ve performansı iyileştirmek için bunları uygulamaya devam edin. Veritabanı iş yükleri dinamiktir ve sürekli olarak değişir. Azure SQL veritabanı izlemeye devam eder ve veritabanınızın performansını iyileştirebilecek öneriler sağlar.

* Azure SQL veritabanı 'nda otomatik ayarlama hakkında daha fazla bilgi için bkz. [otomatik ayarlama](automatic-tuning-overview.md) .
* Azure SQL veritabanı performans önerilerine genel bakış için bkz. [performans önerileri](database-advisor-implement-performance-recommendations.md) .
* En iyi sorguların performans etkisini görüntüleme hakkında bilgi edinmek için bkz. [sorgu Performans öngörüleri](query-performance-insight-use.md) .

## <a name="additional-resources"></a>Ek kaynaklar

* [Sorgu Deposu](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)
* [CREATE INDEX](/sql/t-sql/statements/create-index-transact-sql)
* [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md)