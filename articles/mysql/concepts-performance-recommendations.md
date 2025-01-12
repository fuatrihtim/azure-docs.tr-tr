---
title: Performans önerileri-MySQL için Azure veritabanı
description: Bu makalede MySQL için Azure veritabanı 'nda performans önerisi özelliği açıklanmaktadır
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 1445ad3d6be020326c34d60c555c24c435548fd0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94537654"
---
# <a name="performance-recommendations-in-azure-database-for-mysql"></a>MySQL için Azure Veritabanı’ndaki Performans Önerileri

**Uygulama hedefi:** MySQL için Azure veritabanı 5,7, 8,0

Performans önerileri özelliği, gelişmiş performans için özelleştirilmiş öneriler oluşturmak üzere veritabanlarınızı analiz eder. Bu önerileri oluşturmak için analiz, şema dahil çeşitli veritabanı özelliklerine bakar. Performans önerileri özelliğini tam olarak kullanmak için sunucunuzda [sorgu deposunu](concepts-query-store.md) etkinleştirin. Performans şeması KAPALıYSA, sorgu deposu ' nu açmak performance_schema ve özellik için gereken performans şeması gereçlerinin bir alt kümesini sunar. Herhangi bir performans önerisi uygulandıktan sonra, bu değişikliklerin etkisini değerlendirmek için performansı sınamalısınız.

## <a name="permissions"></a>İzinler

Performans Önerileri özelliğini kullanarak analiz çalıştırmak için **Sahip** veya **Katkıda bulunan** izinleri gereklidir.

## <a name="performance-recommendations"></a>Performans önerileri

[Performans Önerileri](concepts-performance-recommendations.md) özelliği, performansı iyileştirme potansiyeli olan dizinleri tanımlamak için sunucunuzdaki iş yüklerini analiz eder.

MySQL sunucunuzun Azure portal sayfasındaki menü çubuğunun **akıllı performans** bölümünde **performans önerilerini** açın.

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-page.png" alt-text="Performans Önerileri giriş sayfası":::

**Çözümle** ' yi seçin ve analiz işlemini başlatmak için bir veritabanı seçin. İş yükünüze bağlı olarak, çözümlemenin tamamlanması birkaç dakika sürebilir. Analiz tamamlanınca portalda bir bildirim olur. Analiz, veritabanınızı ayrıntılı bir şekilde inceleme işlemini gerçekleştirir. Yoğun olmayan dönemler sırasında analiz gerçekleştirmenizi öneririz.

**Öneriler** penceresi, varsa önerilerin bir listesini ve bu öneriyi oluşturan ılgılı sorgu kimliğini gösterir. Sorgu KIMLIĞIYLE, sorgu hakkında daha fazla bilgi edinmek için [MySQL.query_store](concepts-query-store.md#mysqlquery_store) görünümünü kullanabilirsiniz.

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-result.png" alt-text="Performans önerileri yeni sayfa":::

Öneriler otomatik olarak uygulanmaz. Öneriyi uygulamak için, sorgu metnini kopyalayın ve tercih ettiğiniz istemciden çalıştırın. Öneriyi değerlendirmek için sınamayı ve izlemeyi unutmayın.

## <a name="recommendation-types"></a>Öneri türleri

### <a name="index-recommendations"></a>Dizin önerileri

*Dizin önerilerini oluşturma* , iş yükünde en sık çalıştırılan veya zaman alan sorguları hızlandırmak için yeni dizinler önerir. Bu öneri türü, [sorgu deposunun](concepts-query-store.md) etkinleştirilmesini gerektirir. Sorgu deposu sorgu bilgilerini toplar ve çözümlemenin öneriyi yapmak için kullandığı ayrıntılı sorgu çalışma zamanı ve sıklık istatistiklerini sağlar.

### <a name="query-recommendations"></a>Sorgu önerileri

Sorgu önerileri, iş yükünde sorgular için iyileştirmeleri ve yeniden yazar önerir. MySQL sorgu düzenlerini tanımlayarak ve bunları sözdizimsel olarak düzelterek, zaman alan sorguların performansı artırılabilir. Bu öneri türü, sorgu deposunun etkinleştirilmesini gerektirir. Sorgu deposu sorgu bilgilerini toplar ve çözümlemenin öneriyi yapmak için kullandığı ayrıntılı sorgu çalışma zamanı ve sıklık istatistiklerini sağlar.

## <a name="next-steps"></a>Sonraki adımlar
- MySQL için Azure veritabanı 'nda [izleme ve ayarlama](concepts-monitoring.md) hakkında daha fazla bilgi edinin.