---
title: Microsoft Azure otomatik ölçeklendirme
description: Microsoft Azure otomatik ölçeklendirme
ms.subservice: autoscale
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 4727d562e21b92e58c8091f1161cf53198ff0b26
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726011"
---
# <a name="overview-of-autoscale-in-microsoft-azure"></a>Microsoft Azure otomatik ölçeklendirmeyi genel bakış
Bu makalede, otomatik ölçeklendirme Microsoft Azure, avantajları ve kullanmaya nasıl başladıklarından bazıları açıklanmaktadır.  

Azure Izleyici otomatik ölçeklendirme yalnızca [Sanal Makine Ölçek Kümeleri](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service-Web Apps](https://azure.microsoft.com/services/app-service/web/), [API Management Hizmetleri](../../api-management/api-management-key-concepts.md)ve [Azure Veri Gezgini kümeleri](/azure/data-explorer/)için geçerlidir.

> [!NOTE]
> Azure 'da iki otomatik ölçeklendirme yöntemi vardır. Otomatik ölçek 'in daha eski bir sürümü, sanal makineler (kullanılabilirlik kümeleri) için geçerlidir. Bu özellik sınırlı desteğe sahiptir ve daha hızlı ve daha güvenilir otomatik ölçeklendirme desteği için sanal makine ölçek kümelerine geçiş yapmanızı öneririz. Bu makaleye, eski teknolojinin kullanımına ilişkin bir bağlantı eklenmiştir.  
>

## <a name="what-is-autoscale"></a>Otomatik ölçeklendirme nedir?
Otomatik ölçeklendirme, uygulamanızdaki yükü işlemek için çalışan kaynakların doğru miktarına sahip olmasını sağlar. Yük arttıkça idare edilecek kaynakları eklemenize ve ayrıca boştaki kaynakları kaldırarak paradan tasarruf etmenize olanak tanır. Çalıştırılacak olan minimum ve maksimum örnek sayısını ve bir kural kümesine göre VM 'Leri otomatik olarak ekleyip kaldırmanız gerekir. En azından, uygulamanızın hiçbir yük olmasa da her zaman çalıştığından emin olur. Maksimum, saatlik maliyetlerinizi en fazla sınırlar. Oluşturduğunuz kuralları kullanarak bu iki aşırı uç arasında otomatik olarak ölçeklendirin.

 ![Otomatik ölçeklendirme açıklanmıştı. VM’leri ekleme ve kaldırma](./media/autoscale-overview/AutoscaleConcept.png)

Kural koşulları karşılandığında bir veya daha fazla otomatik ölçeklendirme eylemi tetiklenir. VM 'Ler ekleyebilir ve kaldırabilir ya da başka işlemler yapabilirsiniz. Aşağıdaki kavramsal diyagramda bu işlem gösterilmektedir.  

 ![Otomatik ölçeklendirme akış diyagramı](./media/autoscale-overview/Autoscale_Overview_v4.png)

Aşağıdaki açıklama, önceki diyagramın parçaları için geçerlidir.   

## <a name="resource-metrics"></a>Kaynak Ölçümleri
Kaynaklar ölçümleri yayarak bu ölçümler daha sonra kurallar tarafından işlenir. Ölçümler farklı yöntemler aracılığıyla gelir.
Sanal Makine Ölçek Kümeleri, Azure tanılama aracılarından telemetri verilerini kullanır, ancak Web Apps ve bulut hizmetleri için telemetri doğrudan Azure altyapısından gelir. Yaygın olarak kullanılan bazı istatistikler CPU kullanımı, bellek kullanımı, iş parçacığı sayısı, kuyruk uzunluğu ve disk kullanımı içerir. Kullanabileceğiniz Telemetri verilerinin bir listesi için bkz. [Otomatik ölçeklendirme ortak ölçümleri](autoscale-common-metrics.md).

## <a name="custom-metrics"></a>Özel ölçümler
Ayrıca, uygulamanızın yayılacağı özel ölçümlerinizi de kullanabilirsiniz. Uygulamanızı, Application Insights ölçümleri gönderecek şekilde yapılandırdıysanız, ölçeklendirip ölçeklenmeyeceğini kararlamak için bu ölçülerden yararlanabilirsiniz.

## <a name="time"></a>Saat
Zamanlama tabanlı kurallar UTC 'yi temel alır. Kurallarınızı ayarlarken saat diliminizi doğru ayarlamanız gerekir.  

## <a name="rules"></a>Kurallar
Diyagramda yalnızca bir otomatik ölçeklendirme kuralı gösterilir, ancak bunlardan birçoğuna sahip olabilirsiniz. Durumunuz için gereken karmaşık çakışan kuralları oluşturabilirsiniz.  Kural türleri şunlardır  

* **Ölçüm** tabanlı-ÖRNEĞIN, CPU kullanımı %50 üzerinde olduğunda bu eylemi yapın.
* **Zaman tabanlı** -Örneğin, belirli bir saat diliminde Cumartesi üzerinde her 8 dakikada bir Web kancası tetikleyin.

Ölçüm tabanlı kurallar, uygulama yükünü ölçer ve bu yüklemeye göre VM 'Leri ekler veya kaldırır. Zamanlama tabanlı kurallar, yükinizdeki zaman düzenlerini gördüğünüzde ve olası bir yük artışı veya azalmadan önce ölçeklendirmek istediğinizde ölçeklendirmenize olanak tanır.  

## <a name="actions-and-automation"></a>Eylemler ve otomasyon
Kurallar, bir veya daha fazla eylem türünü tetikleyebilir.

* Sanal makineleri **genişleme** veya dışarı ölçeklendirme
* **E-posta** -e-postayı abonelik yöneticilerine, ortak yöneticilere ve/veya belirttiğiniz ek e-posta adresine gönderin
* **Web kancaları aracılığıyla otomatikleştirin** -Azure içinde veya dışında birden çok karmaşık eylemi tetikleyebilen Web kancalarını çağırın. Azure 'da Azure Otomasyonu runbook, Azure Işlevi veya Azure mantıksal uygulaması başlatabilirsiniz. Azure dışındaki örnek üçüncü taraf URL 'SI, bolluk ve Twilio gibi hizmetleri içerir.

## <a name="autoscale-settings"></a>Otomatik Ölçeklendirme Ayarları
Otomatik ölçeklendirme aşağıdaki terminolojiyi ve yapıyı kullanır.

- Otomatik ölçeklendirme **ayarı** , ölçeği büyütme veya küçültme yapılıp yapılmayacağını anlamak için otomatik ölçeklendirme motoru tarafından okundu. Bir veya daha fazla profil, hedef kaynakla ilgili bilgiler ve bildirim ayarları içerir.

  - **Otomatik ölçeklendirme profili** bir birleşimidir:

    - **kapasite ayarı**, örnek sayısı için en düşük, en yüksek ve varsayılan değerleri gösterir.
    - her biri bir tetikleyici (saat veya ölçüm) ve bir ölçeklendirme eylemi (yukarı veya aşağı) içeren **kurallar kümesi**.
    - Otomatik ölçeklendirmeyi bu profili ne zaman yürürlüğe koymalıdır belirten **yinelenme**.

      Farklı çakışan gereksinimlerle ilgilenmesini sağlayan birden çok profiliniz olabilir. Örneğin, günün farklı saatleri veya haftanın günleri için farklı otomatik ölçeklendirme profillerine sahip olabilirsiniz.

  - Bir **bildirim ayarı** , bir otomatik ölçeklendirme olayının, otomatik ölçeklendirme ayarının profillerinin bir ölçütünden birine göre gerçekleştiği durumlarda hangi bildirimlerin gerçekleşeceğini tanımlar. Otomatik ölçeklendirme bir veya daha fazla e-posta adresine bildirimde bulunabilir veya bir veya daha fazla Web kancalarına çağrı yapabilir


![Azure otomatik ölçeklendirme ayarı, profili ve kural yapısı](./media/autoscale-overview/AzureResourceManagerRuleStructure3.png)

Yapılandırılabilir alanlar ve açıklamaların tam listesi [Otomatik ölçeklendirme REST API](/rest/api/monitor/autoscalesettings)kullanılabilir.

Kod örnekleri için bkz.

* [VM Ölçek Kümeleri için Kaynak Yöneticisi şablonları kullanarak gelişmiş otomatik ölçeklendirme yapılandırması](autoscale-virtual-machine-scale-sets.md)  
* [Otomatik ölçeklendirme REST API](/rest/api/monitor/autoscalesettings)

## <a name="horizontal-vs-vertical-scaling"></a>Yatay vs dikey ölçekleme
Otomatik ölçeklendirme yalnızca, sanal makine örneği sayısında artış ("Out") veya azalma ("ın") olan yatay olarak ölçeklendirilir.  Yükü işlemek için büyük olasılıkla binlerce VM çalıştırmanızı sağlayan bir bulut durumunda yatay daha esnektir.

Buna karşılık dikey ölçeklendirme farklıdır. Aynı sayıda VM 'yi tutar, ancak VM 'Leri daha fazla ("yukarı") veya daha az ("aşağı") güçlü hale getirir. Güç, bellek, CPU hızı, disk alanı vb. ile ölçülür.  Dikey ölçeklendirmenin daha fazla sınırlaması vardır. Daha büyük bir donanımın kullanılabilirliğine bağımlıdır ve bu, üst sınıra hızla ve bölgeye göre farklılık gösterebilir. Dikey ölçekleme Ayrıca genellikle bir VM 'nin durdurulup yeniden başlatılmasını gerektirir.

## <a name="methods-of-access"></a>Erişim yöntemleri
Otomatik ölçeklendirmeyi kullanarak ayarlama

* [Azure portalı](autoscale-get-started.md)
* [PowerShell](../powershell-samples.md#create-and-manage-autoscale-settings)
* [Platformlar arası Komut Satırı Arabirimi (CLI)](../cli-samples.md#autoscale)
* [Azure İzleyici REST API'si](/rest/api/monitor/autoscalesettings)

## <a name="supported-services-for-autoscale"></a>Otomatik ölçeklendirme için desteklenen hizmetler
| Hizmet | Şema & belgeleri |
| --- | --- |
| Web Apps |[Ölçeklendirme Web Apps](autoscale-get-started.md) |
| Cloud Services |[Bulut hizmetini otomatik ölçeklendirme](../../cloud-services/cloud-services-how-to-scale-portal.md) |
| Sanal makineler: klasik |[Klasik sanal makine kullanılabilirlik kümelerini ölçeklendirme](/archive/blogs/kaevans/autoscaling-azurevirtual-machines) |
| Sanal makineler: Windows ölçek kümeleri |[Windows 'da sanal makine ölçek kümelerini ölçeklendirme](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) |
| Sanal makineler: Linux ölçek kümeleri |[Linux 'ta sanal makine ölçek kümelerini ölçeklendirme](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) |
| Sanal makineler: Windows örneği |[VM Ölçek Kümeleri için Kaynak Yöneticisi şablonları kullanarak gelişmiş otomatik ölçeklendirme yapılandırması](autoscale-virtual-machine-scale-sets.md) |
| Azure App Service |[Azure App Service 'te bir uygulamayı ölçeklendirme](../../app-service/manage-scale-up.md)|
| API Management hizmeti|[Bir Azure API Management örneğini otomatik olarak ölçeklendirme](../../api-management/api-management-howto-autoscale.md)
| Azure Veri Gezgini kümeleri|[Değişen talebe uyum sağlamak için Azure Veri Gezgini kümeleri ölçeklendirmeyi yönetme](/azure/data-explorer/manage-cluster-horizontal-scaling)|
| Logic Apps |[Tümleştirme hizmeti ortamı (ıSE) kapasitesi ekleniyor](../../logic-apps/ise-manage-integration-service-environment.md#add-ise-capacity)|
| Spring Cloud |[Mikro hizmet uygulamaları için otomatik ölçeklendirmeyi ayarlama](../../spring-cloud/spring-cloud-tutorial-setup-autoscale.md)|
| Service Bus |[Azure Service Bus bir ad alanının mesajlaşma birimlerini otomatik olarak güncelleştir](../../service-bus-messaging/automate-update-messaging-units.md)|

## <a name="next-steps"></a>Sonraki adımlar
Otomatik ölçeklendirme hakkında daha fazla bilgi edinmek için, daha önce listelenen otomatik ölçeklendirme Izlenecek yolları kullanın veya aşağıdaki kaynaklara bakın:

* [Azure Izleyici otomatik ölçeklendirme ortak ölçümleri](autoscale-common-metrics.md)
* [En iyi Azure İzleyici otomatik ölçeklendirme yöntemleri](autoscale-best-practices.md)
* [E-posta ve Web kancası uyarı bildirimleri göndermek için otomatik ölçeklendirme eylemlerini kullanma](autoscale-webhook-email.md)
* [Otomatik ölçeklendirme REST API](/rest/api/monitor/autoscalesettings)
* [Sanal Makine Ölçek Kümeleri otomatik ölçeklendirme sorunlarını giderme](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)