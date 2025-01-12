---
title: Azure Application Insights Genel Bakış Panosu | Microsoft Docs
description: Azure Application Insights ve Genel Bakış Panosu işlevselliğine sahip uygulamaları izleyin.
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 1b0708fa70d3a3ecb406f1d974bb1f2b47e55b40
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97504109"
---
# <a name="application-insights-overview-dashboard"></a>Application Insights Genel Bakış Panosu

Application Insights, uygulamanızın sistem durumunu ve performansını hızlı bir şekilde değerlendirmesine olanak tanımak için her zaman bir Özet genel bakış bölmesi sağlamıştır. Yeni Genel Bakış Panosu daha hızlı bir daha esnek deneyim sağlar.

## <a name="how-do-i-test-out-the-new-experience"></a>Yeni deneyimi test Nasıl yaparım? mı?

Yeni Genel Bakış Panosu artık varsayılan olarak başlatılır:

![Genel Bakış Önizleme bölmesi](./media/overview-dashboard/overview.png)

## <a name="better-performance"></a>Daha iyi performans

Zaman aralığı seçimi basit bir tıklama arabirimine basitleştirildi.

![Zaman aralığı](./media/overview-dashboard/app-insights-overview-dashboard-03.png)

Genel performans büyük ölçüde artmıştır. **Arama** ve **analiz** gibi popüler özelliklere tek tıklamayla erişebilirsiniz. Her varsayılan dinamik olarak KPI kutucuğu, karşılık gelen Application Insights özellikleriyle ilgili öngörüler sağlar. Başarısız istekler hakkında daha fazla bilgi edinmek için **Araştır** üst bilgisinde **hata** seçin:

![Hatalar](./media/overview-dashboard/app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Uygulama panosu

Uygulama panosu, uygulama sistem durumu ve Performanslarınızın tamamen özelleştirilebilir tek bir görünümünü sağlamak için Azure 'daki mevcut Pano teknolojisini kullanır.

Varsayılan panoya erişmek için sol üst köşedeki _uygulama panosu_ ' nu seçin.

![Ekran görüntüsünde, uygulama panosu düğmesi vurgulanmış olarak gösterilir.](./media/overview-dashboard/app-insights-overview-dashboard-05.png)

Bu panoya ilk kez erişiyorsanız, varsayılan bir görünüm başlatılır:

![Pano görünümü](./media/overview-dashboard/0001-dashboard.png)

İsterseniz varsayılan görünümü koruyabilirsiniz. Ayrıca, takımınızın ihtiyaçlarına en iyi şekilde uyum sağlamak için panodan ekleme ve silme de yapabilirsiniz.

> [!NOTE]
> Application Insights kaynağına erişimi olan tüm kullanıcılar aynı uygulama panosu deneyimini paylaşır. Bir kullanıcı tarafından yapılan değişiklikler tüm kullanıcılar için görünümü değiştirir.

Genel Bakış deneyimine geri gitmek için yalnızca şunları seçin:

![Genel bakış düğmesi](./media/overview-dashboard/app-insights-overview-dashboard-07.png)

## <a name="troubleshooting"></a>Sorun giderme

Şu anda bir panoda görüntülenen veriler için 30 günlük veri sınırı vardır. 30 günden daha fazla zaman filtresi seçerseniz veya **kutucuk ayarlarını yapılandır** ' ı seçerseniz ve 30 günden fazla bir özel zaman aralığı ayarlarsanız, 90 günlük varsayılan veri saklama süresi ile birlikte Pano 30 günden daha fazla veri ile gösterilmez. Şu anda bu davranış için geçici çözüm yoktur.

## <a name="next-steps"></a>Sonraki adımlar

- [Huniler](./usage-funnels.md)
- [Deposuna](./usage-retention.md)
- [Kullanıcı Akışları](./usage-flows.md)

