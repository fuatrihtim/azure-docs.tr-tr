---
title: Azure 'da gerçek zamanlı ve akış işleme çözümü seçin
description: Uygulamanızı Azure 'da derlemek için doğru gerçek zamanlı analiz ve akış işleme teknolojisini seçme hakkında bilgi edinin.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 4c10a91971357001723adcb783253c9867cf6d87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019066"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Azure 'da gerçek zamanlı analiz ve akış işleme teknolojisini seçin

Azure 'da gerçek zamanlı analiz ve akış işleme için kullanılabilen çeşitli hizmetler vardır. Bu makale, uygulamanıza en uygun teknolojiyi belirlemek için gereken bilgileri sağlar.

## <a name="when-to-use-azure-stream-analytics"></a>Ne zaman kullanılacağı Azure Stream Analytics

Azure Stream Analytics, Azure 'da Stream Analytics için önerilen hizmettir. Aşağıdakiler dahil olmak üzere çok çeşitli senaryolar için tasarlanmıştır, ancak bunlarla sınırlı değildir:

* Veri görselleştirme için panolar
* Zamana bağlı ve uzamsal desenler veya bozukluklar için gerçek zamanlı [Uyarılar](stream-analytics-set-up-alerts.md)
* Ayıklama, Dönüştürme, Yükleme (ETL)
* [Olay kaynağını belirleme kalıbı](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

Uygulamanıza Azure Stream Analytics bir iş eklemek, zaten bildiğiniz SQL dilini kullanarak akış analizlerini Azure 'da çalışır duruma getirmenin en hızlı yoludur. Azure Stream Analytics bir iş hizmetidir, bu nedenle kümeleri yönetme süresini harcamanız gerekmez ve iş düzeyinde% 99,9 SLA ile kapalı kalma süresi konusunda endişelenmeniz gerekmez. Faturalandırma, başlangıç maliyetlerini düşük (bir akış birimi), ancak ölçeklenebilir (192 akış birimi kadar) olan iş düzeyinde de yapılır. Birkaç Stream Analytics işi çalıştırmak, bir kümeyi çalıştırmak ve sürdürmek olduğundan çok daha uygun maliyetli bir hesapdır.

Azure Stream Analytics, zengin kullanıma hazır bir deneyim içerir. Ek bir kurulum gerekmeden aşağıdaki özelliklerden hemen yararlanabilirsiniz:

* [Pencereli toplamalar](stream-analytics-window-functions.md), zamana bağlı birleşimler ve zamana bağlı analitik işlevler gibi yerleşik zamana bağlı işleçler.
* Yerel Azure [giriş](stream-analytics-add-inputs.md) ve [Çıkış](stream-analytics-define-outputs.md) bağdaştırıcıları
* Bölge sınırlaması için Jeo-uzamsal başvuru verileriyle katılım dahil, yavaş değişen [başvuru verileri](stream-analytics-use-reference-data.md) (arama tabloları olarak da bilinir) için destek.
* [Anomali algılama](stream-analytics-machine-learning-anomaly-detection.md) gibi tümleşik çözümler
* Aynı sorguda birden çok kez pencere
* Rastgele diziler halinde birden çok zamana bağlı işleç oluşturma yeteneği.
* 100-ms ' den fazla Event Hubs giriş girişi, Event Hubs ve en yüksek aktarım hızı ile Event Hubs ağ gecikmesi de dahil olmak üzere çıkış girişi ' ne kadar

## <a name="when-to-use-other-technologies"></a>Diğer teknolojilerin ne zaman kullanılacağı

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>JavaScript veya C dışında bir dilde UDF 'ler, Udalar ve özel seri hale getiriciler yazmak istiyorsunuz #

Azure Stream Analytics, bulut işleri için JavaScript 'te Kullanıcı tanımlı işlevleri (UDF) veya Kullanıcı tanımlı toplamaları (UDA) ve IoT Edge işleri Için C# ' i destekler. C# Kullanıcı tanımlı seri hale getiriciler de desteklenir. Java veya Python gibi diğer dillerde bir seri hale getirici, UDF veya UDA uygulamak istiyorsanız Spark yapılandırılmış akış kullanabilirsiniz. Ayrıca, kendi sanal makinelerinizde Event Hubs **Eventprocessorhost** ' u çalıştırarak rastgele akış işleme gerçekleştirebilirsiniz.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>Çözümünüz çoklu bulut veya şirket içi bir ortamda

Azure Stream Analytics Microsoft 'un özel teknolojisidir ve yalnızca Azure 'da kullanılabilir. Çözümünüzün bulutlarda veya şirket içinde taşınabilir olmasını istiyorsanız Spark yapılandırılmış akış veya fırtınası gibi açık kaynaklı teknolojileri göz önünde bulundurun.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure portal kullanarak Stream Analytics işi oluşturun](stream-analytics-quick-create-portal.md)
* [Azure PowerShell kullanarak Stream Analytics işi oluşturma](stream-analytics-quick-create-powershell.md)
* [Visual Studio 'Yu kullanarak Stream Analytics işi oluşturma](stream-analytics-quick-create-vs.md)
* [Visual Studio Code kullanarak Stream Analytics işi oluşturma](quick-create-visual-studio-code.md)
