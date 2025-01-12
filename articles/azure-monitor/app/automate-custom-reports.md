---
title: Azure Application Insights verileriyle özel raporları otomatikleştirin
description: Azure Application Insights verileriyle özel günlük/haftalık/aylık raporları otomatikleştirin
ms.topic: conceptual
ms.date: 05/20/2019
ms.reviewer: sdash
ms.openlocfilehash: 39970227fc94d3073688b23cc530462f368a6ecf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100590042"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Azure Application Insights verileriyle özel raporları otomatikleştirin

Süreli raporlar, iş açısından kritik hizmetlerinin nasıl yaptığına dair bir ekibin bilgilendirilmesi konusunda yardımcı olur. Geliştiriciler, DevOps/SRE takımları ve yöneticileri, herkesin portalda oturum açmalarına gerek kalmadan öngörüleri güvenilir bir şekilde sunmanın yanı sıra otomatik raporlarla üretken olabilirler. Bu tür raporlar Ayrıca gecikme süreleri, yük veya hata ücretleriyle ilgili herhangi bir uyarı kuralını tetiklemeyen dereceli artışları belirlemenize yardımcı olabilir.

Her kuruluşun benzersiz raporlama ihtiyaçları vardır, örneğin:

* Belirli yüzdelik ölçüm toplamaları veya bir rapordaki özel ölçümler.
* Farklı kitlelere yönelik günlük, haftalık ve aylık veri rultaları için farklı raporlara sahip olmak.
* Bölge veya ortam gibi özel özniteliklere göre segmentleme.
* Farklı aboneliklerde veya kaynak gruplarında olsalar dahi, bazı AI kaynaklarını tek bir raporda gruplandırabilirsiniz.
* Seçmeli kitlelere gönderilen gizli ölçümleri içeren raporları ayırın.
* Portal kaynaklarına erişimi olmayan hissedarlara raporlar.

> [!NOTE] 
> Haftalık Application Insights Özet e-postası hiçbir özelleştirmeye izin vermedi ve aşağıda listelenen özel seçeneklerin yararına kullanımdan kaldırılacaktır. Son haftalık özet e-postası 11 Haziran 2018 tarihinde gönderilir. Benzer özel raporlar almak için lütfen aşağıdaki seçeneklerden birini yapılandırın (aşağıda önerilen sorguyu kullanın).

## <a name="to-automate-custom-report-emails"></a>Özel rapor e-postalarını otomatikleştirmek için

Bir zamanlamaya göre özel raporlar oluşturmak için [programlı olarak Application Insights verileri sorgulayabilirsiniz](https://dev.applicationinsights.io/) . Aşağıdaki seçenekler hızla başlamanıza yardımcı olabilir:

* [Power otomatikleştirmede raporları otomatikleştirin](../logs/logicapp-flow-connector.md)
* [Raporları Logic Apps otomatikleştirin](automate-with-logic-apps.md)
* Izleme senaryosundaki "Application Insights zamanlanmış Özet" [Azure işlev](../../azure-functions/functions-get-started.md) şablonunu kullanın. Bu işlev, e-postayı teslim etmek için SendGrid kullanır. 

    ![Azure işlev şablonu](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Haftalık Özet e-postası için örnek sorgu
Aşağıdaki sorguda, bir haftalık özet e-postası için rapor gibi birden çok veri kümesi arasında katılım gösterilmektedir. Bunu gerektiği gibi özelleştirin ve bir haftalık raporu otomatik hale getirmek için yukarıda listelenen seçeneklerden herhangi biriyle kullanın.

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

## <a name="application-insights-scheduled-digest-report"></a>Application Insights zamanlanmış özet raporu

1. Azure İşlev Uygulaması oluşturun. (Application Insights _üzerinde_ yalnızca yeni işlev uygulaması Application Insights ile izlemek istiyorsanız gereklidir)

   [İşlev uygulaması oluşturmayı](../../azure-functions/functions-get-started.md) öğrenmek Için Azure işlevleri belgelerini ziyaret edin

2. Yeni İşlev Uygulaması dağıtımı tamamladıktan sonra **Kaynağa Git**' i seçin.

3. **Yeni işlev**' ı seçin.

   ![Yeni bir Işlev ekran görüntüsü oluştur](./media/automate-custom-reports/new-function.png)

4. **_Application Insights zamanlanmış Özet şablonunu_** seçin.

     > [!NOTE]
     > Varsayılan olarak, işlev uygulamaları çalışma zamanı sürüm 3. x ile oluşturulur. Application Insights zamanlanmış Özet şablonunu kullanmak için [Azure işlevleri çalışma zamanı sürüm](../../azure-functions/set-runtime-version.md) **1. x ' i** hedeflemelidir. Çalışma zamanı sürümünü değiştirmek için yapılandırma > Işlev çalışma zamanı ayarları ' na gidin. ![çalışma zamanı ekran görüntüsü](./media/automate-custom-reports/change-runtime-v.png)

   ![Yeni Işlev Application Insights şablonu ekran görüntüsü](./media/automate-custom-reports/function-app-04.png)

5. Raporunuz için uygun bir alıcı e-posta adresi girin ve **Oluştur**' u seçin.

   ![İşlev ayarları ekran görüntüsü](./media/automate-custom-reports/scheduled-digest.png)

6. **İşlev uygulaması**  >  **platform özellikleri**  >  **yapılandırmanızı** seçin.

    ![Azure Işlevi uygulama ayarları ekran görüntüsü](./media/automate-custom-reports/config.png)

7. Karşılık gelen uygun değerlerle üç yeni uygulama ayarı oluşturun ``AI_APP_ID`` , ``AI_APP_KEY`` ve ``SendGridAPI`` . **Kaydet**’i seçin.

     ![İşlev tümleştirme arabirimi ekran görüntüsü](./media/automate-custom-reports/app-settings.png)
    
    (AI_ değerleri, raporlamak istediğiniz Application Insights kaynak için API erişimi altında bulunabilir. Application Insights API anahtarınız yoksa, **API anahtarı oluşturma** seçeneği vardır.)
    
   * AI_APP_ID = uygulama KIMLIĞI
   * AI_APP_KEY = API anahtarı
   * SendGridAPI = SendGrid API anahtarı

     > [!NOTE]
     > SendGrid hesabınız yoksa, bir tane oluşturabilirsiniz. Azure Işlevleri için SendGrid 'in belgeleri [burada](../../azure-functions/functions-bindings-sendgrid.md). Yalnızca SendGrid 'i ayarlama ve bir API anahtarı oluşturma konusunda en az bir açıklama istiyorsanız bu makalenin sonunda verilmiştir. 

8. **Tümleştirme** ' i seçin ve çıktılar altında **sendgrid ($Return)** öğesine tıklayın.

     ![Çıkış ekran görüntüsü](./media/automate-custom-reports/integrate.png)

9. **SendGridAPI anahtar uygulaması ayarı** altında, **SendGridAPI** Için yeni oluşturduğunuz uygulama ayarınızı seçin.

     ![İşlev Uygulaması ekran görüntüsü Çalıştır](./media/automate-custom-reports/sendgrid-output.png)

10. İşlev Uygulaması çalıştırın ve test edin.

     ![Test ekran görüntüsü](./media/automate-custom-reports/function-app-11.png)

11. İletinin gönderildiğini/başarıyla alındığını onaylamak için e-postanızı kontrol edin.

     ![E-posta konu satırı ekran görüntüsü](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>Azure ile SendGrid

Bu adımlar yalnızca önceden yapılandırılmış bir SendGrid hesabınız yoksa geçerlidir.

1. Azure portal **kaynak oluştur** ' u seçin > **SendGrid e-posta teslimi** Için arama yapın > **Oluştur** ' a tıklayın > SendGrid 'e özgü oluşturma yönergelerini doldurun.

     ![SendGrid kaynağı oluştur ekran görüntüsü](./media/automate-custom-reports/sendgrid.png)

2. SendGrid hesapları altında oluşturulduktan sonra **Yönet**' i seçin.

     ![Ayarlar API anahtarı ekran görüntüsü](./media/automate-custom-reports/sendgrid-manage.png)

3. Bu, SendGrid 'in sitesini başlatacaktır. **Ayarlar**  >  **API anahtarları**' nı seçin.

     ![API anahtarı uygulaması oluşturma ve görüntüleme ekran görüntüsü](./media/automate-custom-reports/function-app-15.png)

4. Bir API anahtarı oluşturun > **& görünümü oluştur**' a tıklayın. (API anahtarınıza uygun izin düzeyini belirlemek için, kısıtlı erişimle SendGrid 'in belgelerini gözden geçirin. Tam erişim burada yalnızca örnek amaçlıdır.)

   ![Tam erişim ekran görüntüsü](./media/automate-custom-reports/function-app-16.png)

5. Tüm anahtarı kopyalayın, bu değer İşlev Uygulaması ayarlarınızda, SendGridAPI için değer olarak ihtiyaç duyduğunuz şeydir.

   ![API anahtarını Kopyala ekran görüntüsü](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Analiz sorguları](../logs/get-started-queries.md)oluşturma hakkında daha fazla bilgi edinin.
* [Application Insights verileri programlı](https://dev.applicationinsights.io/) bir şekilde sorgulama hakkında daha fazla bilgi edinin
* [Logic Apps](../../logic-apps/logic-apps-overview.md) hakkında daha fazla bilgi edinin.
* [Microsoft Power otomatikleştir](https://ms.flow.microsoft.com)hakkında daha fazla bilgi edinin.
