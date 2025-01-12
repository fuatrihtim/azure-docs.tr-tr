---
title: Azure depolama 'yı izleme, tanılama ve sorun giderme | Microsoft Docs
description: Azure depolama ile ilgili sorunları tanımlamak, tanılamak ve sorunlarını gidermek için depolama analizi, istemci tarafı günlüğü ve diğer üçüncü taraf araçları gibi özellikleri kullanın.
author: normesta
ms.service: storage
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 5943a65d61dee4fcf610b8cc45139674362995cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600726"
---
# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Microsoft Azure Storage izleme, tanılama ve sorun giderme
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Genel Bakış
Bulut ortamında barındırılan dağıtılmış bir uygulamada tanılama ve sorun giderme sorunları geleneksel ortamlarından daha karmaşık olabilir. Uygulamalar PaaS veya IaaS altyapısına, şirket içinde, mobil cihaza veya bu ortamların bir birleşimine dağıtılabilir. Genellikle, uygulamanızın ağ trafiği ortak ve özel ağlarla geçiş gösterebilir ve uygulamanız, ilişkisel ve belge veritabanları gibi diğer veri depolarına ek olarak Microsoft Azure Depolama tabloları, Bloblar, kuyruklar veya dosyalar gibi birden çok depolama teknolojilerini kullanabilir.

Bu tür uygulamaları başarılı bir şekilde yönetmek için bunları önceden izlemeniz ve bunların tüm yönlerini ve bağımlı teknolojilerini nasıl tanılamanıza ve giderebileceğinizi anlamanız gerekir. Azure depolama hizmetleri 'nin bir kullanıcısı olarak, uygulamanızda beklenmedik değişiklikler (örneğin, olağan yanıt süreleriyle daha yavaş) için uygulamanızın kullandığı depolama hizmetlerini sürekli olarak izlemeniz ve daha ayrıntılı veriler toplamak ve bir sorunu ayrıntılı olarak çözümlemek için günlüğe kaydetme özelliğini kullanmanız gerekir. Hem izleme hem de günlüğe kaydetme işleminden elde ettiğiniz tanılama bilgileri, uygulamanızın karşılaştığı sorunun temel nedenini belirlemenize yardımcı olur. Daha sonra sorunu giderebilmeniz ve düzeltmek için uygulayabileceğiniz uygun adımları belirleyebilirsiniz. Azure depolama, temel bir Azure hizmetidir ve müşterilerin Azure altyapısına dağıtmaları çözümlerinin önemli bir bölümünü oluşturur. Azure depolama, bulut tabanlı uygulamalarınızda depolama sorunlarını izleme, tanılama ve sorun giderme işlemlerini basitleştirmeye yönelik yetenekler içerir.

* [Giriş]
  * [Bu kılavuz nasıl düzenlenir]
* [Depolama hizmetinizi izleme]
  * [İzleme hizmeti durumu]
  * [İzleme kapasitesi]
  * [Kullanılabilirliği izleme]
  * [Performansı izleme]
* [Depolama sorunlarını tanılama]
  * [Hizmet durumu sorunları]
  * [Performans sorunları]
  * [Hataları tanılama]
  * [Depolama öykünücüsü sorunları]
  * [Depolama günlük araçları]
  * [Ağ günlüğü araçlarını kullanma]
* [Uçtan uca izleme]
  * [Günlük verilerini ilişkilendirme]
  * [İstemci istek kimliği]
  * [Sunucu istek KIMLIĞI]
  * [Zaman damgaları]
* [Sorun giderme kılavuzu]
  * [Ölçümler yüksek AverageE2ELatency ve düşük AverageServerLatency gösteriyor]
  * [Ölçümler yüksek AverageE2ELatency ve düşük AverageServerLatency gösteriyor, ancak istemci yüksek gecikme durumu yaşıyor]
  * [Ölçümler yüksek AverageServerLatency gösteriyor]
  * [Kuyrukta ileti tesliminde beklenmedik gecikmeler yaşıyorsunuz]
  * [Ölçümler PercentThrottlingError’da artış gösteriyor]
  * [Ölçümler PercentTimeoutError’da artış gösteriyor]
  * [Ölçümler PercentNetworkError’da artış gösteriyor]
  * [İstemci HTTP 403 (Yasak) iletileri alıyor]
  * [İstemci HTTP 404 (Bulunamadı) iletileri alıyor]
  * [İstemci HTTP 409 (Çakışma) iletileri alıyor]
  * [Ölçümler düşük PercentSuccess veya Analytics günlük girişlerinde, Clienentothererrors işlem durumu ile işlemler var]
  * [Kapasite ölçümleri, depolama kapasitesi kullanımında beklenmedik bir artış gösterir]
  * [Sorununuz geliştirme veya test için depolama öykünücüsünün kullanılması durumunda]
  * [.NET için Azure SDK 'Yı yüklerken sorunlarla karşılaşdığınızda]
  * [Depolama hizmeti ile farklı bir sorununuz var]
  * [Windows sanal makinelerinde VHD 'ler sorunlarını giderme](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)   
  * [Linux sanal makinelerinde VHD 'Lerde sorun giderme](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)
  * [Windows ile Azure dosyaları sorunlarını giderme](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [Linux ile Azure dosyaları sorunlarını giderme](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [Ekler]
  * [Ek 1: HTTP ve HTTPS trafiğini yakalamak için Fiddler kullanma]
  * [Ek 2: ağ trafiğini yakalamak için Wireshark kullanma]
  * [Ek 4: ölçümleri ve günlük verilerini görüntülemek için Excel kullanma]
  * [Ek 5: Azure DevOps için Application Insights ile Izleme]

## <a name="introduction"></a><a name="introduction"></a>Giriş
Bu kılavuzda, Azure depolama Istemci kitaplığı 'nda Azure Depolama Analizi, istemci tarafı günlüğe kaydetme gibi özelliklerin yanı sıra Azure depolama ile ilgili sorunları tanımlamak, tanılamak ve gidermek için diğer üçüncü taraf araçları nasıl kullanabileceğiniz gösterilmektedir.

![İstemci uygulamaları ile Azure depolama hizmetleri arasındaki bilgi akışını gösteren diyagram.][1]

Bu kılavuz, birincil olarak Azure depolama hizmetleri ve bu tür çevrimiçi hizmetler yönetmekten sorumlu BT profesyonelleri kullanan çevrimiçi hizmetler geliştiricileri tarafından okunmalıdır. Bu kılavuzun amaçları şunlardır:

* Azure depolama hesaplarınızın sistem durumunu ve performansını korumanıza yardımcı olur.
* Bir uygulamadaki bir sorunun veya sorunun Azure depolama ile ilgili olduğuna karar vermenize yardımcı olacak gerekli süreçler ve araçlar sağlamak için.
* Azure depolama ile ilgili sorunları çözmeye yönelik eyleme dönüştürülebilir bir kılavuz sağlamak için.

### <a name="how-this-guide-is-organized"></a><a name="how-this-guide-is-organized"></a>Bu kılavuz nasıl düzenlenir
"[Depolama hizmetinizi izleme]" bölümünde, Azure depolama Analizi ölçümleri (depolama ölçümleri) kullanarak Azure depolama hizmetlerinizin sistem durumunun ve performansının nasıl izleneceği açıklanır.

"[Depolama sorunlarını tanılama]" bölümünde, Azure depolama Analizi günlüğe kaydetme (depolama günlüğü) kullanılarak sorunların nasıl tanılanması açıklanmaktadır. Ayrıca, .NET için depolama Istemci kitaplığı ya da Java için Azure SDK gibi istemci kitaplıklarından birindeki tesislerin kullanıldığı istemci tarafı günlüğe kaydetmenin nasıl etkinleştirileceği açıklanır.

"[Uçtan uca izleme]" bölümünde çeşitli günlük dosyalarında ve ölçüm verilerinde bulunan bilgilerin nasıl ilişkilendiribileceği açıklanmaktadır.

"[Sorun giderme kılavuzu]" bölümü, karşılaşabileceğiniz bazı yaygın depolama ile ilgili sorunlar için sorun giderme kılavuzu sağlar.

"[Appendıces]", ağ paketi verilerini çözümlemek için Wireshark ve NetMon gibi diğer araçları kullanma hakkında BILGI ve http/https iletilerini çözümlemek Için Fiddler bilgilerini içerir.

## <a name="monitoring-your-storage-service"></a><a name="monitoring-your-storage-service"></a>Depolama hizmetinizi izleme
Windows performans izleme hakkında bilgi sahibiyseniz, depolama ölçümlerini Windows performans Izleyicisi sayaçlarından oluşan bir Azure depolama ile eşdeğer olarak düşünebilirsiniz. Depolama ölçümleri ' nde, hizmet kullanılabilirliği, hizmete gönderilen toplam istek sayısı veya hizmete yönelik başarılı isteklerin yüzdesi gibi kapsamlı bir ölçüm kümesi (Windows performans Izleyicisi terminolojisinde sayaçlar) bulacaksınız. Kullanılabilir ölçümlerin tam listesi için bkz. [ölçüm tablosu şeması depolama Analizi](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema). Depolama hizmetinin ölçümleri her saat veya dakikada toplamasını isteyip istemediğinizi belirtebilirsiniz. Ölçümleri etkinleştirme ve depolama hesaplarınızı izleme hakkında daha fazla bilgi için bkz. [depolama ölçümlerini etkinleştirme ve ölçüm verilerini görüntüleme](../blobs/monitor-blob-storage.md).

[Azure Portal](https://portal.azure.com) hangi saatlik ölçümleri göstermek istediğinizi seçebilir ve saatlik bir ölçüm belirli bir eşiği aştığında yöneticilere e-posta ile bildirimde bulunan kuralları yapılandırabilirsiniz. Daha fazla bilgi için bkz. [uyarı bildirimleri alma](../../azure-monitor/alerts/alerts-overview.md).

[Depolama Için Azure Izleyicisi 'ni](../../azure-monitor/insights/storage-insights-overview.md) (Önizleme) incelemenizi öneririz. Azure Storage hizmetlerinizin performansı, kapasitesi ve kullanılabilirliğinden oluşan Birleşik bir görünüm sunarak Azure depolama hesaplarınızın kapsamlı bir şekilde izlenmesini sağlayan bir Azure Izleyici özelliğidir. Her şeyi etkinleştirmenizi veya yapılandırmanızı gerektirmez ve bu ölçümleri önceden tanımlı etkileşimli grafiklerden ve diğer Görselleştirmelerde hemen görüntüleyebilirsiniz.

Depolama hizmeti en iyi çabayı kullanarak ölçümleri toplar, ancak her depolama işlemini kaydedemeyebilir.

Azure portal, bir depolama hesabı için kullanılabilirlik, toplam istek ve ortalama gecikme sayısı gibi ölçümleri görüntüleyebilirsiniz. Bir bildirim kuralı, kullanılabilirliği belirli bir düzeyin altına düşerse, yöneticiye uyarı verecek şekilde de ayarlanır. Bu verileri görüntülerken, araştırma için olası bir alan tablo hizmeti başarı yüzdesi %100 ' dır (daha fazla bilgi için "[ölçümler düşük PercentSuccess veya Analytics günlük girişleri, Clienentothererrors işlem durumu ile birlikte işlemler olduğunu gösterir]" bölümüne bakın).

Sağlıklı olduklarından ve beklendiği gibi çalıştığından emin olmak için Azure uygulamalarınızı sürekli olarak izlemeniz gerekir:

* Geçerli verileri karşılaştırmanızı ve Azure depolama ve uygulamanız davranışlarındaki önemli değişiklikleri tanımlamanızı sağlayacak uygulama için bazı temel ölçümler oluşturma. Temel ölçümlerinizin değerleri birçok durumda uygulamaya özgü olmalıdır ve uygulamanızı performans testi yaparken bunları oluşturmanız gerekir.
* Dakika ölçümlerini kaydetme ve hata sayımlarında veya istek hızlarındaki ani artışlar gibi beklenmedik hatalar ve bozukluklar için etkin bir şekilde izlemek üzere bunları kullanma.
* Saatlik ölçümleri kaydetme ve ortalama hata sayıları ve istek oranları gibi ortalama değerleri izlemek için bunları kullanma.
* Daha sonra "[depolama sorunlarını tanılama]" bölümünde açıklanan tanılama araçlarını kullanarak olası sorunları araştırma.

Aşağıdaki görüntüde yer alan grafikler, saatlik ölçümler için gerçekleşen ortalama, etkinlikteki ani artışları nasıl gizleyebileceğinizi gösterir. Saatlik ölçümler, sabit bir istek oranını göstermek üzere görünür, ancak dakika ölçümleri gerçekten gerçekleşirken dalgalanmalar ortaya çıkar.

![Saatlik ölçümler için gerçekleşen ortalama ortalamasını gösteren grafikler, etkinlikteki ani artışları gizleyebilir.][3]

Bu bölümün geri kalanında, izlemeniz gereken ölçümler ve nedenleri açıklanmaktadır.

### <a name="monitoring-service-health"></a><a name="monitoring-service-health"></a>İzleme hizmeti durumu
Dünyanın dört bir yanındaki tüm Azure bölgelerindeki depolama hizmeti (ve diğer Azure Hizmetleri) durumunu görüntülemek için [Azure Portal](https://portal.azure.com) kullanabilirsiniz. İzleme, denetiminiz dışında bir sorun uygulamanız için kullandığınız bölgedeki depolama hizmetini etkileirse hemen görmenizi sağlar.

[Azure Portal](https://portal.azure.com) , çeşitli Azure hizmetlerini etkileyen olayların bildirimlerini de verebilir.
Note: Bu bilgiler, [Azure hizmet panosundaki](https://status.azure.com)geçmiş verilerle birlikte daha önce sunulmuştur.
Azure DevOps için Application Insights hakkında daha fazla bilgi için bkz. ek "[ek 5: Azure DevOps için Application Insights Ile izleme](#appendix-5)."

### <a name="monitoring-capacity"></a><a name="monitoring-capacity"></a>İzleme kapasitesi
Depolama ölçümleri yalnızca blob hizmeti için kapasite ölçümlerini depolar çünkü Bloblar genellikle depolanan verilerin en büyük oranını tutar (yazma sırasında, tablolarınızın ve kuyrukların kapasitesini izlemek için depolama ölçümlerini kullanmak mümkün değildir). Blob hizmeti için izlemeyi etkinleştirdiyseniz, bu verileri **$MetricsCapacityBlob** tablosunda bulabilirsiniz. Depolama ölçümleri, bu verileri günde bir kez kaydeder ve satırın kullanıcı verileriyle (değer **verileri**) veya analiz verileriyle (değer **Analizi**) ilişkili bir varlık Içerip Içermediğini anlamak için **rowkey** değerini kullanabilirsiniz. Depolanan her varlık, depolama hesabında kullanılan depolama alanı (bayt cinsinden ölçülen **Kapasite** ) ve geçerli kapsayıcı sayısı (**containercount**) ve Blobların (**objectCount**) hakkında bilgiler içerir. **$MetricsCapacityBlob** tablosunda depolanan kapasite ölçümleri hakkında daha fazla bilgi için, bkz. [depolama Analizi ölçüm tablosu şeması](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema).

> [!NOTE]
> Depolama hesabınızın kapasite sınırlarına yaklaştığı erken bir uyarı için bu değerleri izlemeniz gerekir. Azure portal, toplu depolama kullanımı belirttiğiniz eşikleri aşarsa veya altına düşerse size bildirimde bulunan uyarı kuralları ekleyebilirsiniz.
>
>

Blob 'lar gibi çeşitli depolama nesnelerinin boyutunu tahmin etmeye yönelik yardım için bkz. [Azure depolama faturalandırmasını anlama – bant genişliği, işlemler ve kapasite](/archive/blogs/patrick_butler_monterde/azure-storage-understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity).

### <a name="monitoring-availability"></a><a name="monitoring-availability"></a>Kullanılabilirliği izleme
Saatlik veya dakikalık ölçüm tablolarında ( **$MetricsHourPrimaryTransactionsBlob**, **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob**, **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob** **kullanılabilirlik** sütunundaki değeri izleyerek Depolama hesabınızdaki depolama hizmetlerinin kullanılabilirliğini izlemeniz gerekir. **Kullanılabilirlik** sütunu, hizmetin veya satır tarafından temsıl edilen API işleminin kullanılabilirliğini gösteren bir yüzde değeri Içerir ( **rowkey** , satırın bir bütün olarak veya belirli bir API işlemi olarak hizmet için ölçümler içerip içermediğini gösterir).

%100 ' den küçük bir değer, bazı depolama isteklerinin başarısız olduğunu gösterir. Ölçüm verilerinde, **Servertimeouterror** gibi farklı hata türleriyle isteklerin sayısını gösteren diğer sütunları inceleyerek neden başarısız olduğunu görebilirsiniz. Hizmetin bölümleri daha iyi yük dengeleme isteğine taşıdığı sırada geçici sunucu zaman aşımları gibi nedenlerle **kullanılabilirliği** geçici 100 olarak görmeniz beklenir; istemci uygulamanızdaki yeniden deneme mantığı, bu tür aralıklı koşulları işlemelidir. [Günlüğe kaydedilen işlemler ve durum iletileri depolama Analizi](/rest/api/storageservices/Storage-Analytics-Logged-Operations-and-Status-Messages) makale, depolama ölçümlerinin **kullanılabilirlik** hesaplamasına dahil edilen işlem türlerini listeler.

[Azure Portal](https://portal.azure.com), bir hizmetin **kullanılabilirliği** belirttiğiniz eşiğin altına düşerse sizi bilgilendirmek için uyarı kuralları ekleyebilirsiniz.

Bu kılavuzun "[sorun giderme kılavuzu]" bölümünde kullanılabilirliğiyle ilgili bazı yaygın depolama hizmeti sorunları açıklanmaktadır.

### <a name="monitoring-performance"></a><a name="monitoring-performance"></a>Performansı izleme
Depolama hizmetlerinin performansını izlemek için, saatlik ve dakikalık ölçüm tablolarından aşağıdaki ölçümleri kullanabilirsiniz.

* **AverageE2ELatency** ve **averageserverlatency** sütunlarındaki değerler, depolama hizmetinin veya API işlem türünün işleme isteklerini geçen ortalama süreyi gösterir. **AverageE2ELatency** , isteği okumak için geçen süreyi ve isteği işlemek için geçen süreyi içeren uçtan uca gecikme süresinin bir ölçüsüdür (Bu nedenle, istek depolama hizmetine ulaştığında ağ gecikmesini içerir); **Averageserverlatency** yalnızca işlem zamanının bir ölçüsüdür ve bu nedenle istemciyle iletişim ile ilgili tüm ağ gecikmesini dışlar. Bu iki değer arasındaki önemli bir fark neden olabileceğini öğrenmek için bu kılavuzun devamındaki "[ölçümler yüksek AverageE2ELatency ve düşük AverageServerLatency göster]" bölümüne bakın.
* **Totalingress** ve **TotalEgress** sütunlarındaki değerler, depolama HIZMETINIZE veya belirli bir API işlem türüne gelen ve çıkan toplam veri miktarını bayt cinsinden gösterir.
* **Totalrequests** sütunundaki değerler, API işleminin depolama hizmetinin aldığı toplam istek sayısını gösterir. **Totalrequests** , depolama hizmetinin aldığı isteklerin toplam sayısıdır.

Genellikle, bu değerlerin hiçbirinde, araştırma gerektiren bir sorun olduğunu belirten bir gösterge olarak beklenmeyen değişiklikler izlenir.

[Azure Portal](https://portal.azure.com), bu hizmetin performans ölçümlerinden herhangi biri aşağıda yer alıyorsa veya belirttiğiniz eşiği aşarsa sizi bilgilendirmek üzere uyarı kuralları ekleyebilirsiniz.

Bu kılavuzun "[sorun giderme kılavuzu]" bölümünde performansla ilgili bazı yaygın depolama hizmeti sorunları açıklanmaktadır.

## <a name="diagnosing-storage-issues"></a><a name="diagnosing-storage-issues"></a>Depolama sorunlarını tanılama
Uygulamanızdaki bir sorunu veya sorunu nasıl fark edebilmeniz gereken birkaç yol vardır:

* Uygulamanın kilitlenmesine veya çalışmayı durdurmasına neden olan önemli bir hata.
* Önceki "[depolama hizmetinizi izleme]" bölümünde açıklandığı gibi izlemekte olduğunuz ölçümlerde taban çizgisi değerlerinden önemli değişiklikler.
* Uygulamanızın kullanıcılarından gelen, belirli bir işlemin beklendiği şekilde tamamlanmamış olduğunu veya bazı özelliklerin çalışmadığını bildirir.
* Uygulamanızda oluşturulan, günlük dosyalarında veya başka bir bildirim yöntemiyle görüntülenen hatalar.

Genellikle, Azure depolama hizmetleriyle ilgili sorunlar dört geniş kategoriden birinde yer almalıdır:

* Uygulamanızda, kullanıcılarınız tarafından bildirilen veya performans ölçümlerinde değişikliklere göre ortaya çıkan bir performans sorunu vardır.
* Bir veya daha fazla bölgede Azure Storage altyapısına ilişkin bir sorun var.
* Uygulamanız, kullanıcılarınız tarafından bildirilen bir hatayla karşılaşıyor ya da izlediğiniz hata sayısı ölçümlerinden birinde bir artış ile gösteriliyor.
* Geliştirme ve test sırasında yerel depolama öykünücüsünü kullanıyor olabilirsiniz; özellikle depolama öykünücüsünün kullanımıyla ilgili bazı sorunlarla karşılaşabilirsiniz.

Aşağıdaki bölümlerde, bu dört kategorinin her birinde sorunları tanılamak ve gidermek için izlemeniz gereken adımlar ana hatlarıyla verilmiştir. Bu kılavuzun ilerleyen bölümlerindeki "[sorun giderme kılavuzu]" bölümü, karşılaşabileceğiniz bazı yaygın sorunlar için daha fazla ayrıntı sağlar.

### <a name="service-health-issues"></a><a name="service-health-issues"></a>Hizmet durumu sorunları
Hizmet durumu sorunları genellikle denetiminizin dışındadır. [Azure Portal](https://portal.azure.com) , depolama hizmetleri dahil olmak üzere Azure hizmetleriyle ilgili devam eden sorunlar hakkında bilgi sağlar. Depolama hesabınızı oluştururken Read-Access Geo-Redundant depolamayı tercih ettiyseniz, verileriniz birincil konumda kullanılamaz hale gelirse, uygulamanız geçici olarak ikincil konumdaki Salt-okunabilir kopyaya geçiş yapabilir. İkincilden okumak için, uygulamanız birincil ve ikincil depolama konumlarını kullanarak geçiş yapabilmelidir ve Salt okunabilir verilerle sınırlı işlevsellik modunda çalışabilebilmelidir. Azure Storage Istemci kitaplıkları, birincil depolamadan okuma başarısız olursa ikincil depolamadan okuyabilen bir yeniden deneme ilkesi tanımlamanızı sağlar. Uygulamanızın, ikincil konumdaki verilerin sonunda tutarlı olduğunu da unutmayın. Daha fazla bilgi için bkz. [Azure depolama yedekliliği seçenekleri ve Okuma Erişimli Coğrafi olarak yedekli depolama](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

### <a name="performance-issues"></a><a name="performance-issues"></a>Performans sorunları
Bir uygulamanın performansı, özellikle kullanıcının bakış açısıyla, öznel olabilir. Bu nedenle, bir performans sorunu olabilecek yerleri belirlemek için temel ölçümlere sahip olmak önemlidir. Birçok etken, bir Azure depolama hizmetinin performansını istemci uygulama perspektifinden etkileyebilir. Bu faktörler, depolama hizmetinde, istemcisinde veya ağ altyapısında çalışmayabilir; Bu nedenle, performans sorununun kaynağını belirlemek için bir stratejinin olması önemlidir.

Ölçümden performans sorununun nedeninin olası konumunu belirledikten sonra, sorunu tanılamak ve gidermek için ayrıntılı bilgileri bulmak üzere günlük dosyalarını kullanabilirsiniz.

Bu kılavuzun devamındaki "[sorun giderme kılavuzu]" bölümünde karşılaşabileceğiniz bazı yaygın performansla ilgili sorunlar hakkında daha fazla bilgi verilmektedir.

### <a name="diagnosing-errors"></a><a name="diagnosing-errors"></a>Hataları tanılama
Uygulamanızın kullanıcıları, istemci uygulaması tarafından bildirilen hataları bilgilendirebilir. Depolama ölçümleri Ayrıca, **networkerror**, **clienttimeouterror** veya **authorizationerror** gibi depolama hizmetlerinizin farklı hata türleri sayılarını kaydeder. Depolama ölçümleri yalnızca farklı hata türlerinin sayısını kaydetirken, sunucu tarafı, istemci tarafı ve ağ günlüklerini inceleyerek tek tek istekler hakkında daha fazla ayrıntı elde edebilirsiniz. Genellikle, depolama hizmeti tarafından döndürülen HTTP durum kodu, isteğin neden başarısız olduğuna dair bir gösterge verecektir.

> [!NOTE]
> Bazı aralıklı hataları görmeyi beklemeniz gerektiğini unutmayın: Örneğin, geçici ağ koşullarından veya uygulama hatalarından kaynaklanan hatalar.
>
>

Aşağıdaki kaynaklar, depolamayla ilgili durum ve hata kodlarının anlaşılması konusunda yararlıdır:

* [Ortak REST API hata kodları](/rest/api/storageservices/Common-REST-API-Error-Codes)
* [Blob hizmeti hata kodları](/rest/api/storageservices/Blob-Service-Error-Codes)
* [Kuyruk hizmeti hata kodları](/rest/api/storageservices/Queue-Service-Error-Codes)
* [Tablo hizmeti hata kodları](/rest/api/storageservices/Table-Service-Error-Codes)
* [Dosya hizmeti hata kodları](/rest/api/storageservices/File-Service-Error-Codes)

### <a name="storage-emulator-issues"></a><a name="storage-emulator-issues"></a>Depolama öykünücüsü sorunları
Azure SDK, bir geliştirme iş istasyonunda çalıştırabileceğiniz bir depolama öykünücüsü içerir. Bu öykünücü, Azure depolama hizmetleri davranışının çoğunu taklit eder ve geliştirme ve test sırasında yararlı olur ve Azure depolama hizmetleri 'ni kullanan uygulamaları Azure aboneliğine ve bir Azure depolama hesabına gerek kalmadan çalıştırmanızı sağlar.

Bu kılavuzun "[sorun giderme kılavuzu]" bölümünde, depolama öykünücüsünün kullanılmasıyla karşılaşılan bazı yaygın sorunlar açıklanmaktadır.

### <a name="storage-logging-tools"></a><a name="storage-logging-tools"></a>Depolama günlük araçları
Depolama günlüğü, Azure Depolama hesabınızdaki depolama isteklerinin sunucu tarafında günlüğe kaydedilmesini sağlar. Sunucu tarafı günlüğe kaydetmenin nasıl etkinleştirileceği ve günlük verilerine erişme hakkında daha fazla bilgi için bkz. [depolama günlüğe kaydetmeyi etkinleştirme ve günlük verilerine erişme](./storage-analytics-logging.md).

.NET için depolama Istemci kitaplığı, uygulamanız tarafından gerçekleştirilen depolama işlemleriyle ilgili istemci tarafı günlük verilerini toplamanıza olanak sağlar. Daha fazla bilgi için bkz. [.NET Depolama İstemci Kitaplığı ile İstemci Tarafı Günlük Kaydı](/rest/api/storageservices/Client-side-Logging-with-the-.NET-Storage-Client-Library).

> [!NOTE]
> Bazı durumlarda (SAS Yetkilendirme hataları gibi), bir Kullanıcı, sunucu tarafı depolama günlüklerinde istek verisi bulamadıysanız bir hata bildirebilir. Sorunun nedeninin istemcide olup olmadığını araştırmak için depolama Istemci kitaplığının günlüğe kaydetme yeteneklerini kullanabilir veya ağı araştırmak için ağ izleme araçları ' nı kullanabilirsiniz.
>
>

### <a name="using-network-logging-tools"></a><a name="using-network-logging-tools"></a>Ağ günlüğü araçlarını kullanma
İstemci ve sunucunun değiş tokuş olduğu veriler ve temel alınan ağ koşulları hakkında ayrıntılı bilgi sağlamak için istemci ile sunucu arasındaki trafiği yakalayabilirsiniz. Faydalı ağ günlüğü araçları şunları içerir:

* [Fiddler](https://www.telerik.com/fiddler) , http ve https istek ve yanıt iletilerinin üst bilgilerini ve yük verilerini incelemenize olanak tanıyan, ücretsiz bir Web hata ayıklama proxy 'si. Daha fazla bilgi için bkz. [ek 1: http ve HTTPS trafiğini yakalamak Için Fiddler kullanma](#appendix-1).
* [Microsoft Ağ İzleyicisi (Netmon)](https://download.cnet.com/s/network-monitor/) ve [Wireshark](https://www.wireshark.org/) , çok çeşitli ağ protokolleri için ayrıntılı paket bilgilerini görüntülemenize olanak tanıyan ücretsiz ağ protokol çözümleyicileri vardır. Wireshark hakkında daha fazla bilgi için bkz. "[ek 2: ağ trafiğini yakalamak Için Wireshark kullanma](#appendix-2)".
* İstemci Makinenizin ağ üzerinden Azure depolama hizmetine bağlanıp bağlanamamasını denetlemek için temel bir bağlantı testi gerçekleştirmek istiyorsanız, bu işlemi istemcisinde standart **ping** aracını kullanarak yapamazsınız. Ancak, bağlantı denetlemek için [ **tcpıng** aracını](https://www.elifulkerson.com/projects/tcping.php) kullanabilirsiniz.

Çoğu durumda, depolama günlüğü ve depolama Istemci kitaplığı 'ndaki günlük verileri bir sorunu tanılamak için yeterli olacaktır, ancak bazı senaryolarda bu ağ günlüğü araçlarının sağlayabilecek daha ayrıntılı bilgilere ihtiyacınız olabilir. Örneğin, HTTP ve HTTPS iletilerini görüntülemek için Fiddler kullanılması, depolama hizmetlerinden ve istemci uygulamasının depolama işlemlerini nasıl yeniden deneyeceğini incelemenize olanak tanıyan üst bilgi ve yük verilerini görüntülemenize olanak sağlar. Wireshark gibi protokol çözümleyicileri, paket düzeyinde çalışır ve kayıp paketleri ve bağlantı sorunlarını gidermenize olanak sağlayan TCP verilerini görüntülemenize olanak sağlar. 

## <a name="end-to-end-tracing"></a><a name="end-to-end-tracing"></a>Uçtan uca izleme
Çeşitli günlük dosyalarını kullanarak uçtan uca izleme, olası sorunları araştırmak için kullanışlı bir tekniktir. Sorun gidermenize yardımcı olacak ayrıntılı bilgiler için günlük dosyalarında nerede başlayacağımızı belirten bir gösterge olarak ölçüm verilerinizin tarih/saat bilgilerini kullanabilirsiniz.

### <a name="correlating-log-data"></a><a name="correlating-log-data"></a>Günlük verilerini ilişkilendirme
İstemci uygulamalarından Günlükler görüntülenirken, ağ izlemeleri ve sunucu tarafı depolama günlüğü, istekleri farklı günlük dosyaları genelinde ilişkilendirileyebilmek önemlidir. Günlük dosyaları, bağıntı tanımlayıcıları olarak yararlı olan bir dizi farklı alanı içerir. İstemci istek KIMLIĞI, farklı günlüklerdeki girişleri ilişkilendirmek için kullanılan en faydalı alandır. Ancak bazen sunucu istek KIMLIĞI veya zaman damgaları kullanmak yararlı olabilir. Aşağıdaki bölümler, bu seçenekler hakkında daha fazla ayrıntı sağlar.

### <a name="client-request-id"></a><a name="client-request-id"></a>İstemci istek kimliği
Depolama Istemci kitaplığı her istek için otomatik olarak benzersiz bir istemci istek KIMLIĞI oluşturur.

* Depolama Istemci kitaplığının oluşturduğu istemci tarafı günlüğünde, istemci istek KIMLIĞI istekle ilgili her günlük girişinin **Istemci Istek kimliği** alanında görüntülenir.
* Fiddler tarafından yakalanan gibi bir ağ izlemede, istemci istek KIMLIĞI istek iletilerinde **x-MS-Client-Request-ID** http üst bilgisi değeri olarak görünür.
* Sunucu tarafı depolama günlüğü günlüğünde istemci istek kimliği, Istemci istek KIMLIĞI sütununda görünür.

> [!NOTE]
> İstemci bu değeri atayabildiğinden (depolama Istemci kitaplığı otomatik olarak yeni bir değer atabilse de), birden çok isteğin aynı istemci isteği KIMLIĞINI paylaşması mümkündür. İstemci yeniden çalıştığında, tüm denemeler aynı istemci isteği KIMLIĞINI paylaşır. İstemciden bir toplu işlem söz konusu olduğunda, toplu işin tek bir istemci istek KIMLIĞI vardır.
>
>

### <a name="server-request-id"></a><a name="server-request-id"></a>Sunucu istek KIMLIĞI
Depolama hizmeti otomatik olarak sunucu istek kimlikleri oluşturur.

* Sunucu tarafı depolama günlüğü günlüğünde, sunucu istek KIMLIĞI, **Istek kimliği üst bilgisi** sütununu görünür.
* Fiddler tarafından yakalanan gibi bir ağ izlemede, sunucu istek KIMLIĞI yanıt iletilerinde **x-MS-Request-ID** http üst bilgisi değeri olarak görünür.
* Depolama Istemci kitaplığının oluşturduğu istemci tarafı günlüğünde, sunucu isteğinin ayrıntılarını gösteren günlük girişi için **Işlem metni** sütununda sunucu istek kimliği görüntülenir.

> [!NOTE]
> Depolama hizmeti her zaman aldığı her istek için benzersiz bir sunucu istek KIMLIĞI atar, bu nedenle istemciden her yeniden deneme denemesi ve bir toplu işte bulunan her işlem benzersiz bir sunucu isteği KIMLIĞINE sahip olur.
>
>

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Aşağıdaki kod örneği, özel bir istemci istek KIMLIĞININ nasıl kullanılacağını göstermektedir. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Monitoring.cs" id="Snippet_UseCustomRequestID":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Depolama Istemci kitaplığı istemcide bir **Storageexception** oluşturursa, **RequestInformation** özelliği bir **ServiceRequestID** özelliği içeren bir **RequestResult** nesnesi içerir. Bir **OperationContext** örneğinden **RequestResult** nesnesine de erişebilirsiniz.

Aşağıdaki kod örneği, depolama hizmetine bir **OperationContext** nesnesi ekleyerek özel bir **clientrequestıd** değerinin nasıl ayarlanacağını göstermektedir. Ayrıca, yanıt iletisinden **Serverrequestıd** değerinin nasıl alınacağını gösterir.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid.
OperationContext oc = new OperationContext();
oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

try
{
    CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
    ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
    var downloadToPath = string.Format("./{0}", blob.Name);
    using (var fs = File.OpenWrite(downloadToPath))
    {
        blob.DownloadToStream(fs, null, null, oc);
        Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
    }
}
catch (StorageException storageException)
{
    Console.WriteLine("Storage exception {0} occurred", storageException.Message);
    // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
    foreach (var result in oc.RequestResults)
    {
            Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID);
    }
}
```

---

### <a name="timestamps"></a><a name="timestamps"></a>Zaman damgaları
Ayrıca ilgili günlük girişlerini bulmak için zaman damgasını da kullanabilirsiniz, ancak mevcut olabilecek istemci ve sunucu arasındaki herhangi bir saat eğmaya dikkat etmeniz gerekir. İstemci üzerindeki zaman damgasına göre eşleşen sunucu tarafı girdileri için artı veya eksi 15 dakika arama yapın. Ölçüm içeren Blobların blob meta verilerinin, blob 'da depolanan ölçümler için zaman aralığını olduğunu unutmayın. Bu zaman aralığı, aynı dakika veya saat için çok sayıda ölçüm blobunuz varsa yararlıdır.

## <a name="troubleshooting-guidance"></a><a name="troubleshooting-guidance"></a>Sorun giderme kılavuzu
Bu bölüm, Azure depolama hizmetleri 'ni kullanırken uygulamanızın karşılaşabileceği bazı yaygın sorunları tanılamada ve bunlarla ilgili sorunları gidermenize yardımcı olur. Belirli sorununuzla ilgili bilgileri bulmak için aşağıdaki listeyi kullanın.

**Karar ağacı sorunlarını giderme**

---
Sorununuz, depolama hizmetlerinden birinin performansıyla ilgilidir mi?

* [Ölçümler yüksek AverageE2ELatency ve düşük AverageServerLatency gösteriyor]
* [Ölçümler yüksek AverageE2ELatency ve düşük AverageServerLatency gösteriyor, ancak istemci yüksek gecikme durumu yaşıyor]
* [Ölçümler yüksek AverageServerLatency gösteriyor]
* [Kuyrukta ileti tesliminde beklenmedik gecikmeler yaşıyorsunuz]

---
Sorununuz, depolama hizmetlerinden birinin kullanılabilirliğiyle bağlantılı mı?

* [Ölçümler PercentThrottlingError’da artış gösteriyor]
* [Ölçümler PercentTimeoutError’da artış gösteriyor]
* [Ölçümler PercentNetworkError’da artış gösteriyor]

---
 İstemci uygulamanız bir depolama hizmetinden HTTP 4XX (404) yanıtı alıyor mu?

* [İstemci HTTP 403 (Yasak) iletileri alıyor]
* [İstemci HTTP 404 (Bulunamadı) iletileri alıyor]
* [İstemci HTTP 409 (Çakışma) iletileri alıyor]

---
[Ölçümler düşük PercentSuccess veya Analytics günlük girişlerinde, Clienentothererrors işlem durumu ile işlemler var]

---
[Kapasite ölçümleri, depolama kapasitesi kullanımında beklenmedik bir artış gösterir]

---
[Çok sayıda ekli VHD 'ye sahip sanal makinelerin beklenmedik şekilde yeniden başlatmaları yaşanıyor]

---
[Sorununuz geliştirme veya test için depolama öykünücüsünün kullanılması durumunda]

---
[.NET için Azure SDK 'Yı yüklerken sorunlarla karşılaşdığınızda]

---
[Depolama hizmeti ile farklı bir sorununuz var]

---
### <a name="metrics-show-high-averagee2elatency-and-low-averageserverlatency"></a><a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Ölçümler yüksek AverageE2ELatency ve düşük AverageServerLatency gösteriyor
[Azure Portal](https://portal.azure.com) izleme aracından aşağıdaki çizimde, **AverageE2ELatency** 'in **averageserverlatency**'ın önemli ölçüde yüksek olduğu bir örnek gösterilir.

![Azure portal, AverageE2ELatency 'in AverageServerLatency 'den önemli ölçüde daha yüksek olduğu bir örnek gösteren bir örnektir.][4]

Depolama hizmeti yalnızca başarılı istekler için **AverageE2ELatency** ölçüsünü hesaplar ve **averageserverlatency**'ın aksine istemcinin, verileri göndermek ve depolama hizmetinden onay almak için geçen süreyi de içerir. Bu nedenle, **AverageE2ELatency** ve **averageserverlatency** arasındaki fark, istemci uygulamanın yavaş yanıt vermesi ya da ağdaki koşullar nedeniyle olabilir.

> [!NOTE]
> Depolama günlüğü günlük verilerinde ayrı depolama işlemleri için **E2ELatency** ve **serverlatency** ' i de görüntüleyebilirsiniz.
>
>

#### <a name="investigating-client-performance-issues"></a>İstemci performans sorunlarını araştırma
İstemci yavaş yanıt vermeye yönelik olası nedenler arasında sınırlı sayıda bağlantı veya iş parçacığı olması veya CPU, bellek veya ağ bant genişliği gibi kaynaklar yetersiz olabilir. İstemci kodunu daha verimli (örneğin, depolama hizmetine zaman uyumsuz çağrılar kullanarak) veya daha büyük bir sanal makine kullanarak (daha fazla çekirdek ve daha fazla bellekle) değiştirerek sorunu çözebilirsiniz.

Tablo ve kuyruk Hizmetleri için, Nagle algoritması, **Averageserverlatency** ile karşılaştırıldığında yüksek **AverageE2ELatency** de olabilir: daha fazla bilgi için, [Nagle 'In algoritmasına, küçük isteklere yaklaşmayı](/archive/blogs/windowsazurestorage/nagles-algorithm-is-not-friendly-towards-small-requests)öğrenin. **System.net** ad alanındaki **ServicePointManager** sınıfını kullanarak koddaki Nagle algoritmasını devre dışı bırakabilirsiniz. Bu, zaten açık olan bağlantıları etkilemediğinden uygulamanızdaki tablo veya kuyruk hizmetlerine çağrı yapmadan önce bunu yapmalısınız. Aşağıdaki örnek, bir çalışan rolündeki **Application_Start** yönteminden gelir.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Monitoring.cs" id="Snippet_DisableNagle":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

---

İstemci uygulamanızın kaç istek kullandığını görmek için istemci tarafı günlüklerini denetlemeniz ve istemcinizdeki CPU, .NET atık toplama, ağ kullanımı veya bellek gibi genel .NET ile ilgili performans sorunlarını kontrol etmeniz gerekir. .NET istemci uygulamalarında sorun giderme için bir başlangıç noktası olarak bkz. [hata ayıklama, izleme ve profil oluşturma](/dotnet/framework/debug-trace-profile/).

#### <a name="investigating-network-latency-issues"></a>Ağ gecikme sorunlarını araştırma
Genellikle, ağ nedeniyle oluşan yüksek uçtan uca gecikme süresi geçici koşullardır. Wireshark gibi araçları kullanarak, bırakılan paketler gibi geçici ve kalıcı ağ sorunlarını araştırabilirsiniz.

Ağ sorunlarını gidermek için Wireshark kullanma hakkında daha fazla bilgi için bkz. "[ek 2: ağ trafiğini yakalamak Için Wireshark kullanma]."

### <a name="metrics-show-low-averagee2elatency-and-low-averageserverlatency-but-the-client-is-experiencing-high-latency"></a><a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Ölçümler yüksek AverageE2ELatency ve düşük AverageServerLatency gösteriyor, ancak istemci yüksek gecikme durumu yaşıyor
Bu senaryoda, en olası neden depolama hizmetine ulaşan depolama isteklerindeki bir gecikme olur. İstemciden gelen isteklerin neden blob hizmetine bunu yapmadığına ilişkin araştırma yapmanız gerekir.

İstemci geciktirilmesi isteklerinin olası bir nedeni, sınırlı sayıda bağlantı veya iş parçacığının olması olabilir.

Ayrıca, istemcinin birden çok yeniden deneme gerçekleştirip gerçekleştirmediğini denetleyin ve nerede olduğunu araştırın. İstemcinin birden çok yeniden deneme yapıp gerçekleştirmediğini anlamak için şunları yapabilirsiniz:

* Depolama Analizi günlüklerini inceleyin. Birden çok yeniden deneme gerçekleşmekte ise, aynı istemci isteği KIMLIĞIYLE ancak farklı sunucu isteği kimliklerine sahip birden çok işlem görürsünüz.
* İstemci günlüklerini inceleyin. Ayrıntılı günlük kaydı, yeniden deneme gerçekleştiğini gösterir.
* Kodunuzda hata ayıklayın ve istekle ilişkili **OperationContext** nesnesinin özelliklerini kontrol edin. İşlem yeniden denenmışsa **Requestresults** özelliği birden çok benzersiz sunucu istek kimliği içerir. Her isteğin başlangıç ve bitiş zamanlarını da denetleyebilirsiniz. Daha fazla bilgi için, bölüm [sunucusu Istek kimliği]içindeki kod örneğine bakın.

İstemcide herhangi bir sorun yoksa, paket kaybı gibi olası ağ sorunlarını araştırmanız gerekir. Wireshark gibi araçları, ağ sorunlarını araştırmak için kullanabilirsiniz.

Ağ sorunlarını gidermek için Wireshark kullanma hakkında daha fazla bilgi için bkz. "[ek 2: ağ trafiğini yakalamak Için Wireshark kullanma]."

### <a name="metrics-show-high-averageserverlatency"></a><a name="metrics-show-high-AverageServerLatency"></a>Ölçümler yüksek AverageServerLatency gösteriyor
Blob indirme istekleri için yüksek **Averageserverlatency** durumunda, aynı blob (veya blob kümesi) için yinelenen istekler olup olmadığını görmek Için depolama günlüğü günlüklerini kullanmanız gerekir. Blob yükleme istekleri için, istemcinin kullandığı blok boyutunu araştırmanız gerekir (örneğin, boyutu 64 K 'den az olan bloklar, okumalar 64 K öbekten daha az yer içermedikleri ve birden çok istemci, blokları aynı Blobun aynı blob 'a yüklüyorsanız aşırı kafa oluşmasına neden olabilir. Ayrıca, saniye başına ölçeklenebilirlik hedeflerinin aşılmaya neden olan istek sayısı için dakika başına ölçümleri denetlemeniz gerekir: Ayrıca bkz. "[ölçümler PercentTimeoutError 'da artış göster]."

Aynı blob veya blob kümesi için yinelenen istekler olduğunda blob indirme istekleri için yüksek **Averageserverlatency** görüyorsanız, bu Blobları Azure önbelleği veya Azure Content DELIVERY Network (CDN) kullanarak önbelleğe almayı düşünmelisiniz. Karşıya yükleme istekleri için, daha büyük bir blok boyutu kullanarak aktarım hızını artırabilirsiniz. Tablolara yönelik sorgular için aynı sorgu işlemlerini gerçekleştiren ve verilerin sıklıkla değişmeyen istemcilerde istemci tarafında önbelleğe alma uygulamak da mümkündür.

Yüksek **Averageserverlatency** değerleri, tarama işlemlerine neden olan veya ekleme/Prepend Anti-model ' i izleyen kötü tasarlanmış tablo veya sorguların bir belirtisi de olabilir. Daha fazla bilgi için bkz. "[ölçümler, Percentkısıtıngerror 'da artış gösterir]".

> [!NOTE]
> Kapsamlı bir denetim listesi performans denetim listesi bulabilirsiniz: [Microsoft Azure depolama performans ve ölçeklenebilirlik denetim listesi](../blobs/storage-performance-checklist.md).
>
>

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery-on-a-queue"></a><a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Kuyrukta ileti tesliminde beklenmedik gecikmeler yaşıyorsunuz
Bir uygulamanın bir sıraya ileti eklemesi ve kuyruktan okunabilmesi için kullanabileceği zaman arasında bir gecikme yaşıyorsanız, sorunu tanılamak için aşağıdaki adımları uygulamanız gerekir:

* Uygulamanın iletileri sıraya başarıyla eklediğini doğrulayın. Uygulamanın başarılı bir şekilde **AddMessage** metodunu birkaç kez yeniden denemediğinden emin olun. Depolama Istemci kitaplığı günlükleri, depolama işlemlerinin yinelenen yeniden denemelerini gösterir.
* İletiyi kuyruğa ekleyen çalışan rolü ve işlem sırasında bir gecikme gibi görünen sıradan iletiyi okuyan çalışan rolü arasında bir saat çarpıklığı olmadığını doğrulayın.
* Sıradaki iletileri okuyan çalışan rolünün başarısız olup olmadığını denetleyin. Bir kuyruk istemcisi **GetMessage** yöntemini çağırırsa ancak bir bildirim ile yanıt vermezse, **ınvisibilitytimeout** süresi doluncaya kadar ileti kuyrukta görünmez olarak kalır. Bu noktada ileti tekrar işlenmek üzere kullanılabilir hale gelir.
* Sıra uzunluğunun zaman içinde büyüdüğünü denetleyin. Diğer çalışanların sıraya yerleştirmekte olduğu tüm iletileri işlemek için yeterli çalışan yoksa bu durum oluşabilir. Ayrıca, silme isteklerinin başarısız olup olmadığını ve iletilerde sıradan çıkarma sayısını görmek için ölçümleri kontrol edin. bu durum, iletiyi silme için yinelenen başarısız denemeleri gösterebilir.
* Beklenenden daha uzun bir süre boyunca beklenen **E2ELatency** ve **serverlatency** değerlerinden daha yüksek bir sıra işlemi için depolama günlüğü günlüklerini inceleyin.

### <a name="metrics-show-an-increase-in-percentthrottlingerror"></a><a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Ölçümler PercentThrottlingError’da artış gösteriyor
Bir depolama hizmetinin ölçeklenebilirlik hedeflerini aşarsanız azaltma hataları oluşur. Depolama hizmeti, tek bir istemcinin veya kiracının hizmeti diğer harcamanıza karşı kullanmazlarını güvence altına almak için kısıtlar. Daha fazla bilgi için bkz. [Standart depolama hesapları Için ölçeklenebilirlik ve performans hedefleri](scalability-targets-standard-account.md) , depolama hesapları için ölçeklenebilirlik hedefleri ve depolama hesapları içindeki bölümlerin performans hedefleri hakkında ayrıntılar için.

**Percentkısıtlar Lingerror** ölçümü, azaltma hatasıyla başarısız olan isteklerin yüzdesinde bir artış gösterip, iki senaryodan birini araştırmanız gerekir:

* [Percentkısıtlar Lingerror 'da geçici artış]
* [Percentkısıtlar Lingerror hatası içinde kalıcı artış]

**Percentkısıtlar Lingerror** 'da genellikle depolama istekleri sayısında bir artış ile aynı zamanda veya uygulamanızda başlangıçta yük testi yaptığınız zaman bir artış meydana gelir. Bu Ayrıca, depolama işlemlerinden "503 sunucu meşgul" veya "500 Işlem zaman aşımı" HTTP durum iletileri olarak istemcide de bildirimde bulunabilir.

#### <a name="transient-increase-in-percentthrottlingerror"></a><a name="transient-increase-in-PercentThrottlingError"></a>Percentkısıtlar Lingerror 'da geçici artış
Uygulama için yüksek etkinlik dönemleriyle aynı olan **Percentkısıtlar Lingerror** değerinde ani artışlar görüyorsanız, istemcinizdeki yeniden denemeler için üstel (doğrusal değil) bir arka kapalı strateji uygulayın. Yeniden denemeler, bölümdeki anlık yükün azalmasını ve uygulamanızın trafikte ani artışlar yumuşatmasına yardımcı olur. Depolama Istemci kitaplığını kullanarak yeniden deneme ilkelerini uygulama hakkında daha fazla bilgi için bkz. [Microsoft. Azure. Storage. retrypolicies ad alanı](/dotnet/api/microsoft.azure.storage.retrypolicies).

> [!NOTE]
> Ayrıca, uygulama için yüksek etkinlik dönemleriyle aynı olmayan **Percentkısıtlar Lingerror** değerinde ani artışlar de görebilirsiniz: burada en olası neden, yük dengelemeyi geliştirmek için bölümleri taşıyan depolama hizmetidir.
>
>

#### <a name="permanent-increase-in-percentthrottlingerror-error"></a><a name="permanent-increase-in-PercentThrottlingError"></a>Percentkısıtlar Lingerror hatası içinde kalıcı artış
İşlem birimlerinizde kalıcı bir artışı takip eden, **Percentkısıtıngerror** için tutarlı bir yüksek değer görüyorsanız veya uygulamanızda ilk yük testlerini gerçekleştirirken, uygulamanızın depolama bölümlerini nasıl kullandığını ve bir depolama hesabı için ölçeklenebilirlik hedeflerine yaklaşıp yaklaşmadığını değerlendirmeniz gerekir. Örneğin, bir kuyrukta (tek bir bölüm olarak sayılır) azaltma hataları görüyorsanız, işlemleri birden çok bölüme yaymak için ek kuyruklar kullanmayı göz önünde bulundurmanız gerekir. Bir tabloda azaltma hatalarını görüyorsanız, daha geniş bir bölüm anahtarı değerleri kullanarak işlemlerinizi birden çok bölüme yaymak için farklı bölümleme düzeni kullanmayı göz önünde bulundurmanız gerekir. Bu sorunun yaygın bir nedeni, bölüm anahtarı olarak tarihi seçtiğiniz ve ardından belirli bir gündeki tüm verilerin tek bir bölüme yazıldığı bir açık/ön ek önleme modelidir. Bu, yükleme altında bu, bir yazma sorunu oluşmasına neden olabilir. Farklı bir bölümlendirme tasarımını düşünün veya blob depolamanın kullanılması daha iyi bir çözüm olup olmadığını değerlendirin. Ayrıca, azaltmanın, trafikte ani artışlar nedeniyle oluşup oluşmadığını denetleyin ve isteklerin örüntüklerini yumuştırabilmenin yollarını araştırın.

İşlemlerinizi birden çok bölüme dağıtırsanız, depolama hesabı için ayarlanan ölçeklenebilirlik limitlerinin farkında olmanız gerekir. Örneğin, her biri saniyede en fazla 2.000 KB ileti kullanan on sıra kullandıysanız, depolama hesabı için saniyede 20.000 ileti genel sınırında olursunuz. Saniyede 20.000 ' den fazla varlık işlemek istiyorsanız, birden çok depolama hesabı kullanmayı göz önünde bulundurmanız gerekir. Ayrıca, isteklerinizin ve varlıklarınızın boyutunun, depolama hizmeti istemcilerinizi azaltarak bir etkisi olduğunu da göz önünde bulundurmanız gerekir: daha büyük istekleriniz ve varlıklarınız varsa, daha erken kısıtlanıyor olabilirsiniz.

Verimsiz sorgu tasarımı, tablo bölümlerinin ölçeklenebilirlik sınırlarına vurmasına da neden olabilir. Örneğin, bir filtre içeren, bir bölümdeki varlıkların yalnızca bir yüzdesini seçen, ancak bir bölümdeki tüm varlıkların taranmasını sağlayan bir sorgu, her varlığa erişebiliyor. Okunan her varlık, bu bölümdeki toplam işlem sayısına doğru sayılır; Bu nedenle, ölçeklenebilirlik hedeflerine kolayca ulaşabilirsiniz.

> [!NOTE]
> Performans testiniz uygulamanızdaki verimsiz sorgu tasarımlarını açığa çıkarmalıdır.
>
>

### <a name="metrics-show-an-increase-in-percenttimeouterror"></a><a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Ölçümler PercentTimeoutError’da artış gösteriyor
Ölçümleriniz, depolama hizmetlerinizin biri için **Percenttimeouterror** ' de bir artış gösterir. Aynı zamanda, istemci depolama işlemlerinden yüksek hacimli "500 Işlem zaman aşımı" HTTP durum iletilerini alır.

> [!NOTE]
> Bir bölümü yeni bir sunucuya taşıyarak depolama hizmeti yükü bakiyesi istekleri olarak geçici olarak zaman aşımı hataları görebilirsiniz.
>
>

**Percenttimeouterror** ölçümü, şu ölçümlerin bir toplamadır: **clienttimeouterror**, **anonymousclienttimeouterror**, **sasclienttimeouterror**, **Servertimeouterror**, **anonymousservertimeouterror** ve **sasservertimeouterror**.

Sunucu zaman aşımları sunucuda bir hata nedeniyle oluşur. Sunucu üzerindeki bir işlem istemci tarafından belirtilen zaman aşımını aştığından istemci zaman aşımları meydana gelir; Örneğin, depolama Istemci kitaplığını kullanan bir istemci, **Queuerequestoptions** sınıfının **servertimeout** özelliğini kullanarak bir işlem için zaman aşımı ayarlayabilir.

Sunucu zaman aşımları, daha fazla araştırma gerektiren depolama hizmetiyle ilgili bir sorun olduğunu gösterir. Hizmet için ölçeklenebilirlik limitlerini vurarak, bu soruna neden olabilecek trafikte herhangi bir ani artışlar belirlemek için ölçümleri kullanabilirsiniz. Sorun kesintili ise, hizmette yük dengeleme etkinliği olabilir. Sorun kalıcıdır ve uygulamanız hizmetin ölçeklenebilirlik sınırlarına ulaşmasından kaynaklanmadığında, bir destek sorunu oluşturmalısınız. İstemci zaman aşımları için, zaman aşımının istemcide uygun bir değere ayarlanmış olup olmadığını ve istemcide ayarlanan zaman aşımı değerini değiştirip, örneğin tablo sorgularınızı en iyi duruma getirerek veya İletilerinizin boyutunu azaltarak depolama hizmetindeki işlemlerin performansını nasıl geliştirebileceğinizi araştırmanız gerekir.

### <a name="metrics-show-an-increase-in-percentnetworkerror"></a><a name="metrics-show-an-increase-in-PercentNetworkError"></a>Ölçümler PercentNetworkError’da artış gösteriyor
Ölçümleriniz, depolama hizmetlerinizin biri için **Percentnetworkerror** 'da artış gösterir. **Percentnetworkerror** ölçümü, şu ölçümlerin bir toplamadır: **networkerror**, **anonymousnetworkerror** ve **sasnetworkerror**. Bu durum, istemci bir depolama isteği yaptığında depolama hizmeti bir ağ hatası algıladığında meydana gelir.

Bu hatanın en yaygın nedeni, depolama hizmetindeki zaman aşımı süresi dolmadan önce bir istemci bağlantısının kesilmesi olur. İstemcinin depolama hizmetinden ne zaman ve ne zaman bağlantısını kesmediğini anlamak için, istemcinizdeki kodu araştırın. Ayrıca, Wireshark veya Tcping kullanarak istemciden gelen ağ bağlantısı sorunlarını araştırabilirsiniz. Bu araçlar, [Appendıces]' de açıklanmıştır.

### <a name="the-client-is-receiving-http-403-forbidden-messages"></a><a name="the-client-is-receiving-403-messages"></a>İstemci HTTP 403 (Yasak) iletileri alıyor
İstemci uygulamanızda HTTP 403 (Yasak) hataları oluşuyorsa büyük ihtimalle istemci depolama isteği gönderirken süresi dolmuş bir Paylaşılan Erişim İmzası kullandığı içindir (saat sapması, geçersiz anahtarlar ve boş üst bilgiler gibi başka olası nedenler de vardır). Sorun süresi dolmuş bir SAS anahtarından kaynaklanıyorsa sunucu tarafı Depolama Günlük Kaydı günlük verilerinde herhangi bir giriş görmezsiniz. Aşağıdaki tabloda, oluşan bu sorunu gösteren depolama Istemci kitaplığı tarafından oluşturulan istemci tarafı günlüğünden bir örnek gösterilmektedir:

| Kaynak | Ayrıntı Düzeyi | Ayrıntı Düzeyi | İstemci istek kimliği | İşlem metni |
| --- | --- | --- | --- | --- |
| Microsoft. Azure. Storage |Bilgi |3 |85d077ab-... |Yalnızca konum modu Primarylocation 'a göre konum birincil olan işlem başlatılıyor. |
| Microsoft. Azure. Storage |Bilgi |3 |85d077ab-... |İçin zaman uyumlu istek başlatılıyor <https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/Synchronous_and_Asynchronous_Requests#Synchronous_request> |
| Microsoft. Azure. Storage |Bilgi |3 |85d077ab-... |Yanıt bekleniyor. |
| Microsoft. Azure. Storage |Uyarı |2 |85d077ab-... |Yanıt beklenirken özel durum oluştu: uzak sunucu bir hata döndürdü: (403) yasak. |
| Microsoft. Azure. Storage |Bilgi |3 |85d077ab-... |Yanıt alındı. Durum kodu = 403, Istek KIMLIĞI = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, Content-MD5 =, ETag =. |
| Microsoft. Azure. Storage |Uyarı |2 |85d077ab-... |İşlem sırasında özel durum oluştu: uzak sunucu bir hata döndürdü: (403) yasak.. |
| Microsoft. Azure. Storage |Bilgi |3 |85d077ab-... |İşlemin yeniden denenip denenmeyeceğini denetleme. Yeniden deneme sayısı = 0, HTTP durum kodu = 403, özel durum = uzak sunucu bir hata döndürdü: (403) yasak.. |
| Microsoft. Azure. Storage |Bilgi |3 |85d077ab-... |Sonraki konum, konum moduna bağlı olarak birincil olarak ayarlanmıştır. |
| Microsoft. Azure. Storage |Hata |1 |85d077ab-... |Yeniden deneme ilkesi, yeniden deneme için izin vermedi. Uzak sunucu ile başarısız olma bir hata döndürdü: (403) yasak. |

Bu senaryoda, istemci belirteci sunucuya göndermeden önce SAS belirtecinin ne kadar süre doldığına ilişkin araştırma yapmalısınız:

* Genellikle bir istemcinin hemen kullanması için SAS oluştururken başlangıç zamanı ayarlamamalısınız. Geçerli zamanı kullanarak SAS belirtecini oluşturan konak ile depolama hizmeti arasında küçük saat farklılıkları varsa depolama hizmeti henüz geçerli olmayan bir SAS alabilir.
* Bir SAS belirtecinin sona erme süresini çık kısa ayarlamayın. Ayrıca, SAS belirtecini oluşturan konakla depolama hizmeti arasındaki küçük saat farklılıkları, bir SAS belirtecinin süresinin beklenenden erken dolmuş gibi görünmesine de neden olabilir.
* SAS anahtarındaki sürüm parametresi (örneğin, **ZF = 2015-04-05**), kullanmakta olduğunuz depolama istemci kitaplığının sürümüyle eşleşiyor mu? Her zaman [depolama Istemci kitaplığı](https://www.nuget.org/packages/WindowsAzure.Storage/)'nın en son sürümünü kullanmanızı öneririz.
* Depolama erişim anahtarlarınızı yeniden oluşturursanız mevcut SAS belirteçleri geçerliliğini kaybedebilir. İstemci uygulamalarının önbelleğe alması için sona erme süresi uzun olan SAS belirteçleri oluşturursanız bu sorunla karşılaşabilirsiniz.

SAS belirteçleri oluşturmak için Depolama İstemcisi Kitaplığı’nı kullanıyorsanız geçerli bir belirteç oluşturmak kolaydır. Ancak, depolama REST API kullanıyorsanız ve SAS belirteçlerini el ile oluşturuyorsanız, bkz. [paylaşılan erişim Imzasıyla erişim yetkisi verme](/rest/api/storageservices/delegate-access-with-shared-access-signature).

### <a name="the-client-is-receiving-http-404-not-found-messages"></a><a name="the-client-is-receiving-404-messages"></a>İstemci HTTP 404 (Bulunamadı) iletileri alıyor
İstemci uygulaması sunucudan HTTP 404 (Bulunamadı) iletisi alırsa istemcinin kullanmaya çalıştığı nesne (bir varlık, tablo, blob, kapsayıcı veya kuyruk gibi) depolama hizmetinde mevcut değil demektir. Bunun aşağıdaki gibi çeşitli olası nedenleri vardır:

* [İstemci veya başka bir işlem daha önce nesneyi silmiş]
* [Paylaşılan Erişim İmzası (SAS) yetkilendirme sorunu]
* [İstemci tarafı JavaScript kodunun nesneye erişim izni yok]
* [Ağ hatası]

#### <a name="the-client-or-another-process-previously-deleted-the-object"></a><a name="client-previously-deleted-the-object"></a>İstemci veya başka bir işlem daha önce nesneyi sildi
İstemcinin bir depolama hizmetindeki verileri okumaya, güncelleştirmeye veya silmeye çalıştığı senaryolarda, bu durumda, depolama hizmetinden söz konusu nesneyi silen önceki bir işlemin sunucu tarafı günlüklerinde tanımlanması daha kolay olur. Günlük verileri genellikle başka bir kullanıcının veya işlemin nesneyi sildiği gösterir. Sunucu tarafı depolama günlüğü günlüğünde, işlem türü ve istenen-nesne-anahtar sütunları, bir istemcinin bir nesneyi sildiği zamanı gösterir.

İstemcinin bir nesne eklemeye çalıştığı senaryoda, bu, istemcinin yeni bir nesne oluşturduğu için bir HTTP 404 (bulunamadı) yanıtı ile sonuçlanmasının hemen açık olmaması olabilir. Ancak, istemci bir blob oluşturuyor ise blob kapsayıcısını bulabilmelidir, istemci bir ileti oluşturuyor, bir kuyruk bulabilmelidir ve istemci bir satır ekliyor ve bu tabloyu bulabilmelidir.

İstemcinin depolama hizmetine belirli istekleri ne zaman göndereceğini daha ayrıntılı bir şekilde anlamak için depolama Istemci kitaplığındaki istemci tarafı günlüğünü kullanabilirsiniz.

Depolama Istemci kitaplığı tarafından oluşturulan aşağıdaki istemci tarafı günlüğü, istemci oluşturduğu blob için kapsayıcıyı bulamadığında sorun olduğunu gösterir. Bu günlük aşağıdaki depolama işlemlerinin ayrıntılarını içerir:

| Request ID | İşlem |
| --- | --- |
| 07b26a5d-... |Blob kapsayıcısını silmek için **Deleteifexists** yöntemi. Bu işlemin kapsayıcının varlığını denetlemek için bir **Head** isteği içerdiğine unutmayın. |
| e2d06d78... |Blob kapsayıcısını oluşturmak için **Createifnotexists** yöntemi. Bu işlemin kapsayıcının varlığını denetleyen bir **baş** isteği içerdiğine unutmayın. **Baş** bir 404 iletisi döndürür, ancak devam eder. |
| de8b1c3c-... |Blobu oluşturmak için **Uploadfromstream** yöntemi. **PUT** isteği bir 404 iletisiyle başarısız oluyor |

Günlük girişleri:

| Request ID | İşlem metni |
| --- | --- |
| 07b26a5d-... |İçin zaman uyumlu istek başlatılıyor `https://domemaildist.blob.core.windows.net/azuremmblobcontainer` . |
| 07b26a5d-... |StringToSign = HEAD............ x-MS-Client-Request-id: 07b26a5d-.... x-MS-Date: Tue, 03 Haz 2014 10:33:11 GMT. x-MS-Version: 2014-02-14./domemaildist/azuremmblobcontainer. ResType: Container. |
| 07b26a5d-... |Yanıt bekleniyor. |
| 07b26a5d-... |Yanıt alındı. Durum kodu = 200, Istek KIMLIĞI = eeead849-... Content-MD5 =, ETag =    &quot; 0x8d14d2dc63d059b &quot; . |
| 07b26a5d-... |Yanıt üst bilgileri başarıyla işlendi ve işlemin geri kalanı ile devam ediliyor. |
| 07b26a5d-... |Yanıt gövdesi indiriliyor. |
| 07b26a5d-... |İşlem başarıyla tamamlandı. |
| 07b26a5d-... |İçin zaman uyumlu istek başlatılıyor `https://domemaildist.blob.core.windows.net/azuremmblobcontainer` . |
| 07b26a5d-... |StringToSign = SIL............ x-MS-Client-Request-id: 07b26a5d-.... x-MS-Date: Tue, 03 Haz 2014 10:33:12 GMT. x-MS-Version: 2014-02-14./domemaildist/azuremmblobcontainer. ResType: Container. |
| 07b26a5d-... |Yanıt bekleniyor. |
| 07b26a5d-... |Yanıt alındı. Durum kodu = 202, Istek KIMLIĞI = 6ab2a4cf-..., Content-MD5 =, ETag =. |
| 07b26a5d-... |Yanıt üst bilgileri başarıyla işlendi ve işlemin geri kalanı ile devam ediliyor. |
| 07b26a5d-... |Yanıt gövdesi indiriliyor. |
| 07b26a5d-... |İşlem başarıyla tamamlandı. |
| e2d06d78-... |İçin zaman uyumsuz istek başlatılıyor `https://domemaildist.blob.core.windows.net/azuremmblobcontainer` .</td> |
| e2d06d78-... |StringToSign = HEAD............ x-MS-Client-Request-id: e2d06d78-.... x-MS-Date: Tue, 03 Haz 2014 10:33:12 GMT. x-MS-Version: 2014-02-14./domemaildist/azuremmblobcontainer. ResType: Container. |
| e2d06d78-... |Yanıt bekleniyor. |
| de8b1c3c-... |İçin zaman uyumlu istek başlatılıyor `https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt` . |
| de8b1c3c-... |StringToSign = PUT... 64. qCmF + Tsqlphq/YYK50mP9ZQ = =........ x-MS-blob-türü: BlockBlob. x-MS-Client-Request-ID: de8b1c3c-.... x-MS-Date: Tue, 03 Haz 2014 10:33:12 GMT. x-MS-Version: 2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |İstek verileri yazma hazırlığı yapılıyor. |
| e2d06d78-... |Yanıt beklenirken özel durum oluştu: uzak sunucu bir hata döndürdü: (404) bulunamadı.. |
| e2d06d78-... |Yanıt alındı. Durum kodu = 404, Istek KIMLIĞI = 353ae3bc-..., Content-MD5 =, ETag =. |
| e2d06d78-... |Yanıt üst bilgileri başarıyla işlendi ve işlemin geri kalanı ile devam ediliyor. |
| e2d06d78-... |Yanıt gövdesi indiriliyor. |
| e2d06d78-... |İşlem başarıyla tamamlandı. |
| e2d06d78-... |İçin zaman uyumsuz istek başlatılıyor `https://domemaildist.blob.core.windows.net/azuremmblobcontainer` . |
| e2d06d78-... |StringToSign = PUT... 0........: x-MS-Client-Request-id: e2d06d78-.... x-MS-Date: Tue, 03 Haz 2014 10:33:12 GMT. x-MS-Version: 2014-02-14./domemaildist/azuremmblobcontainer. ResType: Container. |
| e2d06d78-... |Yanıt bekleniyor. |
| de8b1c3c-... |İstek verileri yazılıyor. |
| de8b1c3c-... |Yanıt bekleniyor. |
| e2d06d78-... |Yanıt beklenirken özel durum oluştu: uzak sunucu bir hata döndürdü: (409) çakışma.. |
| e2d06d78-... |Yanıt alındı. Durum kodu = 409, Istek KIMLIĞI = c27da20e-..., Content-MD5 =, ETag =. |
| e2d06d78-... |Hata yanıtı gövdesi indiriliyor. |
| de8b1c3c-... |Yanıt beklenirken özel durum oluştu: uzak sunucu bir hata döndürdü: (404) bulunamadı.. |
| de8b1c3c-... |Yanıt alındı. Durum kodu = 404, Istek KIMLIĞI = 0eaeab3e-..., Content-MD5 =, ETag =. |
| de8b1c3c-... |İşlem sırasında özel durum oluştu: uzak sunucu bir hata döndürdü: (404) bulunmadı.. |
| de8b1c3c-... |Yeniden deneme ilkesi, yeniden deneme için izin vermedi. Uzak sunucu ile başarısız olma bir hata döndürdü: (404) bulunamadı.. |
| e2d06d78-... |Yeniden deneme ilkesi, yeniden deneme için izin vermedi. Uzak sunucu ile başarısız olma bir hata döndürdü: (409) çakışma.. |

Bu örnekte, günlük istemcisinin, **Uploadfromstream** yönteminden (de8b1c3c-...) Isteklerle **Createifnotexists** yönteminden (istek kimliği e2d06d78...) istek olduğunu gösterir. Bu araya ekleme, istemci uygulaması bu yöntemleri zaman uyumsuz olarak çağırdığından oluşur. Bu kapsayıcıdaki bir blob 'a herhangi bir veriyi karşıya yüklemeyi denemeden önce kapsayıcıyı oluşturduğundan emin olmak için istemcideki zaman uyumsuz kodu değiştirin. En ideal olarak, tüm Kapsayıcılarınızı önceden oluşturmalısınız.

#### <a name="a-shared-access-signature-sas-authorization-issue"></a><a name="SAS-authorization-issue"></a>Paylaşılan erişim Imzası (SAS) yetkilendirme sorunu
İstemci uygulaması, işlem için gerekli izinleri içermeyen bir SAS anahtarı kullanmayı denerse, depolama hizmeti istemciye bir HTTP 404 (bulunamadı) iletisi döndürür. Aynı zamanda, ölçümlerde **Sasauthorizationerror** için sıfır olmayan bir değer görürsünüz.

Aşağıdaki tabloda, depolama günlüğü günlük dosyasından örnek bir sunucu tarafı günlüğü iletisi gösterilmektedir:

| Name | Değer |
| --- | --- |
| İstek başlangıç saati | 2014-05-30T06:17:48.4473697 Z |
| İşlem türü     | GetBlobProperties            |
| İstek durumu     | SASAuthorizationError        |
| HTTP durum kodu   | 404                            |
| Kimlik doğrulaması türü| 'Larının                          |
| Hizmet türü       | Blob                         |
| İstek URL’si         | `https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt` |
| &nbsp;                 |   ? ZF = 2014-02-14&SR = c&sı = mypolicy&SIG = XXXXX &; api-Version = 2014-02-14 |
| İstek KIMLIĞI üstbilgisi  | a1f348d5-8032-4912-93ef-b393e5252a3b |
| İstemci istek kimliği  | 2d064953-8436-4ee0-aa0c-65cb874f7929 |


İstemci uygulamanızın izin verilmeyen bir işlem gerçekleştirmeye çalıştığını araştırın.

#### <a name="client-side-javascript-code-does-not-have-permission-to-access-the-object"></a><a name="JavaScript-code-does-not-have-permission"></a>İstemci tarafı JavaScript kodunun nesnesine erişim izni yok
Bir JavaScript istemcisi kullanıyorsanız ve depolama hizmeti HTTP 404 iletileri döndürüyorsa, tarayıcıda aşağıdaki JavaScript hatalarını kontrol edersiniz:

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> İstemci tarafı JavaScript sorunlarını giderirken tarayıcı ve depolama hizmeti arasında değiş tokuş edilen iletileri izlemek için Internet Explorer 'daki F12 Geliştirici Araçları kullanabilirsiniz.
>
>

Web tarayıcısı, bir Web sayfasının, sayfanın geldiği etki alanından farklı bir etki alanındaki API 'YI aramasını engelleyen [kaynak ilkesi](https://www.w3.org/Security/wiki/Same_Origin_Policy) güvenlik kısıtlamasını uyguladığı için bu hatalar oluşur.

JavaScript sorununu çözmek için, istemcinin eriştiği depolama hizmeti için çıkış noktası kaynak paylaşımını (CORS) yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Azure depolama hizmetleri Için çıkış noktaları arası kaynak paylaşımı (CORS) desteği](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).

Aşağıdaki kod örneğinde, blob hizmeti, contoso etki alanında çalışan JavaScript 'in blob Storage hizmetinizdeki bir blob 'a erişmesine izin verecek şekilde nasıl yapılandırılacağı gösterilmektedir:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Monitoring.cs" id="Snippet_ConfigureCORS":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
// Set the service properties.
ServiceProperties sp = client.GetServiceProperties();
sp.DefaultServiceVersion = "2013-08-15";
CorsRule cr = new CorsRule();
cr.AllowedHeaders.Add("*");
cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
cr.AllowedOrigins.Add("http://www.contoso.com");
cr.ExposedHeaders.Add("x-ms-*");
cr.MaxAgeInSeconds = 5;
sp.Cors.CorsRules.Clear();
sp.Cors.CorsRules.Add(cr);
client.SetServiceProperties(sp);
```

---

#### <a name="network-failure"></a><a name="network-failure"></a>Ağ hatası
Bazı durumlarda, kayıp ağ paketleri istemciye HTTP 404 iletileri döndüren depolama hizmetine yol açabilir. Örneğin, istemci uygulamanız bir varlığı tablo hizmetinden silerken, istemcinin tablo hizmetinden bir "HTTP 404 (bulunamadı)" durum iletisini bildiren bir depolama özel durumu oluşturmasını görürsünüz. Tabloyu Tablo depolama hizmetinde araştırdığınızda, hizmetin varlığı istendiği gibi sildiğine bakabilirsiniz.

İstemcideki özel durum ayrıntıları, istek için tablo hizmeti tarafından atanan istek KIMLIĞINI (7e84f12d...) içerir: günlük dosyasında **istek kimliği-üst bilgi** sütununda arayarak, istek ayrıntılarını sunucu tarafı depolama günlüklerinde bulmak için bu bilgileri kullanabilirsiniz. Ayrıca, bu gibi hataların ne zaman yapılacağını belirlemek için ölçümleri de kullanabilir ve ardından günlük dosyalarında ölçümler bu hatayı kaydetti zamanına göre arama yapın. Bu günlük girdisi, silmenin "HTTP (404) Istemci diğer hatası" durum iletisiyle başarısız olduğunu gösterir. Aynı günlük girdisi, istemci tarafından **istemci-istek kimliği** sütununda oluşturulan istek kimliğini de içerir (813ea74f...).

Sunucu tarafı günlüğü aynı varlık için başarılı bir silme işlemi için aynı **istemci-istek kimliği** değerine (813ea74f...) sahip başka bir giriş de içerir ve aynı istemciden. Bu başarılı silme işlemi, başarısız silme isteğinden çok kısa bir süre önce gerçekleşti.

Bu senaryonun en olası nedeni, istemcinin tablo hizmetine varlık için bir silme isteği göndermesinin başarılı olduğunu, ancak sunucudan bir bildirim almamasının (Belki de geçici bir ağ sorunundan kaynaklanıyor olması nedeniyle). İstemci daha sonra işlemi otomatik olarak yeniden dener (aynı **istemci-istek kimliği** kullanılarak) ve varlık zaten silindiğinden bu yeniden deneme başarısız oldu.

Bu sorun sık sık oluşuyorsa, istemcinin tablo hizmetinden gelen bildirimleri almamasının neden başarısız olduğunu araştırmanız gerekir. Sorun kesintili ise, "HTTP (404) bulunamadı" hatasını yakalayıp istemcide günlüğe kaydedin, ancak istemcinin devam etmesine izin verin.

### <a name="the-client-is-receiving-http-409-conflict-messages"></a><a name="the-client-is-receiving-409-messages"></a>İstemci HTTP 409 (çakışma) iletilerini alıyor
Aşağıdaki tabloda iki istemci işlemi için sunucu tarafı günlüğünden ayıklama gösterilmektedir: **Deleteifexists** , aynı blob kapsayıcısı adı kullanılarak **Createifnotexists** tarafından hemen izlenir. Her istemci işlemi, sunucuya iki istekle gönderilir, ilk olarak bir **Getcontainerproperties** , kapsayıcının var olup olmadığını kontrol etmek için, ardından **DeleteContainer** veya **CreateContainer** isteği ile sonuçlanır.

| Timestamp | Çalışma | Sonuç | Kapsayıcı adı | İstemci istek kimliği |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |GetContainerProperties |200 |mmcont |c9f52c89-... |
| 05:10:13.8167325 |DeleteContainer |202 |mmcont |c9f52c89-... |
| 05:10:13.8987407 |GetContainerProperties |404 |mmcont |bc881924-... |
| 05:10:14.2147723 |CreateContainer |409 |mmcont |bc881924-... |

İstemci uygulamasındaki kod, aynı adı kullanarak bir blob kapsayıcısını siler ve sonra hemen yeniden oluşturur: **Createifnotexists** yöntemi (ISTEMCI istek kimliği bc881924-...), sonunda http 409 (çakışma) hatası ile başarısız olur. Bir istemci blob kapsayıcılarını, tabloları veya kuyrukları sildiğinde adın yeniden kullanılabilir hale gelmesi biraz zaman alır.

Silme/yeniden oluşturma düzeni genelse istemci uygulaması yeni kapsayıcı oluştururken benzersiz kapsayıcı adları kullanmalıdır.

### <a name="metrics-show-low-percentsuccess-or-analytics-log-entries-have-operations-with-transaction-status-of-clientothererrors"></a><a name="metrics-show-low-percent-success"></a>Ölçümler düşük PercentSuccess veya Analytics günlük girişlerinde, Clienentothererrors işlem durumu ile işlemler var
**Percentsuccess** ölçümü, http durum koduna göre başarılı olan işlemlerin yüzdesini yakalar. 3XX, 4XX ve 5XX aralığında durum kodları olan işlemler başarısız olarak sayılır ve **Percentsuccess** ölçüm değeri düşüktür. Sunucu tarafı depolama günlük dosyalarında, bu işlemler **Clienentothererrors** işlem durumuyla kaydedilir.

Bu işlemlerin başarıyla tamamlandığını ve bu nedenle kullanılabilirlik gibi diğer ölçümleri etkilemediğini unutmayın. Başarılı bir şekilde yürütülen ancak başarısız HTTP durum kodlarına neden olan işlemlere ilişkin bazı örnekler şunlardır:

* **Resourcenotfound** (404 bulunamadı), örneğin, var olmayan bir blob 'A yönelik Get isteğinden.
* **Resourcealreadyexists** (çakışma 409), örneğin, kaynağın zaten bulunduğu bir **Createifnotexist** işlemi.
* **Conditionnotmet** (değiştirilmedi 304), örneğin, bir Istemcinin bir **ETag** değeri göndermesi ve yalnızca son işlemden sonra GÜNCELLEŞTIRILMIŞ olması halinde bir http **If-None-Match** üst bilgisi göndermesi gibi koşullu bir işlemden.

Depolama hizmetlerinin sayfa [ortak REST API hata kodlarında](/rest/api/storageservices/Common-REST-API-Error-Codes)döndürdüğü ortak REST API hata kodlarının bir listesini bulabilirsiniz.

### <a name="capacity-metrics-show-an-unexpected-increase-in-storage-capacity-usage"></a><a name="capacity-metrics-show-an-unexpected-increase"></a>Kapasite ölçümleri, depolama kapasitesi kullanımında beklenmedik bir artış gösterir
Depolama hesabınızdaki kapasite kullanımında ani, beklenmedik değişiklikler görürseniz, öncelikle kullanılabilirlik ölçümlerinize bakarak bu sorunları araştırabilirsiniz. Örneğin, başarısız silme isteklerinin sayısında bir artış, uygulamaya özgü temizleme işlemleri olarak kullandığınız BLOB depolama alanındaki bir artışla sonuçlanmasına neden olabilir. alan boşaltmanız beklenen bir şekilde çalışmayabilir (örneğin, alan boşaltma için kullanılan SAS belirteçlerinin Kullanım zamanı dolmuşsa).

### <a name="your-issue-arises-from-using-the-storage-emulator-for-development-or-test"></a><a name="your-issue-arises-from-using-the-storage-emulator"></a>Sorununuz geliştirme veya test için depolama öykünücüsünün kullanılması durumunda
Genellikle geliştirme ve test sırasında depolama öykünücüsünü kullanarak bir Azure depolama hesabı gereksinimini ortadan kaldırmak için kullanılır. Depolama öykünücüsünü kullanırken oluşabilecek yaygın sorunlar şunlardır:

* ["X" özelliği depolama öykünücüsünde çalışmıyor]
* ["Depolama öykünücüsü kullanılırken HTTP başlıklarındaki bir değer doğru biçimde değil" hatası]
* [Depolama öykünücüsünü çalıştırmak için yönetici ayrıcalıkları gerekir]

#### <a name="feature-x-is-not-working-in-the-storage-emulator"></a><a name="feature-X-is-not-working"></a>"X" özelliği depolama öykünücüsünde çalışmıyor
Depolama öykünücüsü, dosya hizmeti gibi Azure Depolama hizmetlerinin tüm özelliklerini desteklemez. Daha fazla bilgi için bkz. [geliştirme ve test Için Azure depolama öykünücüsünü kullanma](storage-use-emulator.md).

Depolama öykünücüsünün desteklemediği özellikler için, Bulutta Azure Storage hizmetini kullanın.

#### <a name="error-the-value-for-one-of-the-http-headers-is-not-in-the-correct-format-when-using-the-storage-emulator"></a><a name="error-HTTP-header-not-correct-format"></a>"Depolama öykünücüsü kullanılırken HTTP başlıklarındaki bir değer doğru biçimde değil" hatası
Depolama Istemci kitaplığını kullanan uygulamanızı yerel depolama öykünücüsüyle ve **Createifnotexists** gibi yöntem çağrılarının "hata iletisiyle başarısız oldu" http başlıklarından birinin değeri doğru biçimde değil. Bu, kullandığınız depolama öykünücüsünün sürümünün kullandığınız depolama istemci kitaplığının sürümünü desteklemediğini belirtir. Depolama Istemci kitaplığı, **x-MS-Version** üst bilgisini yaptığı tüm isteklere ekler. Depolama öykünücüsü **x-MS-Version** üstbilgisindeki değeri tanımazsa, isteği reddeder.

Depolama Kitaplığı Istemci günlüklerini, gönderdiği **x-MS-Version üst bilgisinin** değerini görmek için kullanabilirsiniz. İstemci uygulamanızdan gelen istekleri izlemek için Fiddler kullanırsanız, **x-MS-Version üst bilgisinin** değerini de görebilirsiniz.

Bu senaryo, genellikle depolama öykünücüsünü güncelleştirmeden depolama Istemci kitaplığının en son sürümünü yükleyip kullandığınızda oluşur. Depolama öykünücüsünün en son sürümünü yüklemeli veya geliştirme ve test öykünücüsü yerine bulut depolaması kullanmanız gerekir.

#### <a name="running-the-storage-emulator-requires-administrative-privileges"></a><a name="storage-emulator-requires-administrative-privileges"></a>Depolama öykünücüsünü çalıştırmak için yönetici ayrıcalıkları gerekir
Depolama öykünücüsünü çalıştırdığınızda yönetici kimlik bilgileri istenir. Bu yalnızca depolama öykünücüsünü ilk kez başlatırken oluşur. Depolama öykünücüsünü başlattıktan sonra, yeniden çalıştırmak için yönetici ayrıcalıklarına sahip olmanız gerekmez.

Daha fazla bilgi için bkz. [geliştirme ve test Için Azure depolama öykünücüsünü kullanma](storage-use-emulator.md). Ayrıca, Visual Studio 'daki depolama öykünücüsünü başlatabilir ve bu da yönetici ayrıcalıkları gerektirir.

### <a name="you-are-encountering-problems-installing-the-azure-sdk-for-net"></a><a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>.NET için Azure SDK 'Yı yüklerken sorunlarla karşılaşdığınızda
SDK 'yı yüklemeye çalıştığınızda, depolama öykünücüsünü yerel makinenize yüklemeye çalışırken başarısız olur. Yükleme günlüğü aşağıdaki iletilerden birini içerir:

* Catietexec: hata: SQL örneğine erişilemiyor
* Catietexec: hata: veritabanı oluşturulamıyor

Bunun nedeni, var olan LocalDB yüklemesinde bir sorundur. Varsayılan olarak, depolama öykünücüsü, Azure depolama hizmetleri 'nde benzetim yaparken verileri kalıcı hale getirmek için LocalDB 'yi kullanır. SDK 'Yı yüklemeyi denemeden önce bir komut istemi penceresinde aşağıdaki komutları çalıştırarak LocalDB örneğinizi sıfırlayabilirsiniz.

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

**Sil** komutu, eski veritabanı dosyalarını depolama öykünücüsünün önceki yüklemelerinden kaldırır.

### <a name="you-have-a-different-issue-with-a-storage-service"></a><a name="you-have-a-different-issue-with-a-storage-service"></a>Depolama hizmeti ile farklı bir sorununuz var
Önceki sorun giderme bölümleri bir depolama hizmeti ile yaşadığınız sorunu içermiyorsa, sorununuzu tanılamak ve gidermek için aşağıdaki yaklaşımı benimsemelisiniz.

* Beklenen taban çizgisi davranışınızdan herhangi bir değişiklik olup olmadığını görmek için ölçülerinizi kontrol edin. Ölçülerden, sorunun geçici veya kalıcı olduğunu ve sorunun hangi depolama işlemlerini etkilediğini belirleyebilirsiniz.
* Oluşan hatalar hakkında daha ayrıntılı bilgi için, sunucu tarafı günlük verilerinizi aramanıza yardımcı olması için ölçüm bilgilerini kullanabilirsiniz. Bu bilgiler sorunu gidermenize ve çözmenize yardımcı olabilir.
* Sunucu tarafı günlüklerindeki bilgiler sorunu başarıyla gidermek için yeterli değilse, istemci uygulamanızın davranışını araştırmak için depolama Istemci kitaplığı istemci tarafı günlüklerini ve Wireshark gibi araçları, ağınızı araştırmak için de kullanabilirsiniz.

Fiddler kullanma hakkında daha fazla bilgi için bkz. "[ek 1: http ve HTTPS trafiğini yakalamak Için Fiddler kullanma]."

Wireshark kullanma hakkında daha fazla bilgi için bkz. "[ek 2: ağ trafiğini yakalamak Için Wireshark kullanma]."

## <a name="appendices"></a><a name="appendices"></a>Ekler
Appendıces, Azure depolama (ve diğer hizmetler) ile ilgili sorunları tanılarken ve sorunlarını giderirken yararlı bulabileceğiniz çeşitli araçları betimlebiliriz. Bu araçlar Azure Storage 'ın bir parçası değildir ve bazıları üçüncü taraf ürünlerdir. Bu nedenle, bu appendıces ' de açıklanan araçlar, Microsoft Azure veya Azure depolama ile sahip olduğunuz herhangi bir destek sözleşmesi kapsamında değildir ve bu nedenle değerlendirme işleminizin bir parçası olarak, bu araçların sağlayıcılarından sunulan lisanslama ve destek seçeneklerini incelemeniz gerekir.

### <a name="appendix-1-using-fiddler-to-capture-http-and-https-traffic"></a><a name="appendix-1"></a>Ek 1: HTTP ve HTTPS trafiğini yakalamak için Fiddler kullanma
[Fiddler](https://www.telerik.com/fiddler) , istemci uygulamanız Ile kullandığınız Azure depolama HIZMETI arasındaki http ve HTTPS trafiğini çözümlemek için kullanışlı bir araçtır.

> [!NOTE]
> Fiddler, HTTPS trafiğinin kodunu çözebilir; Bunu nasıl yaptığını anlamak ve güvenlik etkilerini anlamak için Fiddler belgelerini dikkatle okumalısınız.
>
>

Bu ek, Fiddler 'ın, Fiddler 'i yüklediğiniz yerel makine ile Azure depolama hizmetleri arasında trafiği yakalamak üzere nasıl yapılandırılacağına ilişkin kısa bir yol sunar.

Fiddler 'ı başlattıktan sonra, yerel makinenizde HTTP ve HTTPS trafiği yakalamaya başlayacaktır. Aşağıda, Fiddler 'ın denetlenmesi için bazı yararlı komutlar verilmiştir:

* Durdur ve trafiği yakalamaya başla. Ana menüde, **Dosya** ' ya gidin ve sonra yakalamayı açıp kapatmak Için **trafiği yakala** ' ya tıklayın.
* Yakalanan trafik verilerini kaydedin. Ana menüde **Dosya**' ya gidin, **Kaydet**' e tıklayın ve ardından **tüm oturumlar**' a tıklayın. Bu, trafiği bir oturum Arşivi dosyasına kaydetmenizi sağlar. Daha sonra analiz için bir oturum arşivi yeniden yükleyebilir veya Microsoft desteği 'ne istenirse gönderebilirsiniz.

Fiddler 'in yakaladığı trafik miktarını sınırlamak için **Filtreler** sekmesinde yapılandırdığınız filtreleri kullanabilirsiniz. Aşağıdaki ekran görüntüsünde, yalnızca **contosoemaildist.Table.Core.Windows.net** depolama uç noktasına gönderilen trafiği yakalayan bir filtre gösterilmektedir:

![Yalnızca contosoemaildist.table.core.windows.net depolama uç noktasına gönderilen trafiği yakalayan bir filtre gösteren ekran görüntüsü.][5]

### <a name="appendix-2-using-wireshark-to-capture-network-traffic"></a><a name="appendix-2"></a>Ek 2: ağ trafiğini yakalamak için Wireshark kullanma
[Wireshark](https://www.wireshark.org/) , çok çeşitli ağ protokolleri için ayrıntılı paket bilgilerini görüntülemenize olanak tanıyan bir ağ protokol çözümleyicisidir.

Aşağıdaki yordamda, Azure Depolama hesabınızdaki tablo hizmetine Wireshark yüklediğiniz yerel makineden gelen trafik için ayrıntılı paket bilgilerinin nasıl yakalanacağı gösterilmektedir.

1. Yerel makinenizde Wireshark başlatın.
2. **Başlat** bölümünde, internet 'e bağlı olan yerel ağ arabirimini veya arabirimlerini seçin.
3. **Yakalama seçenekleri**' ne tıklayın.
4. **Yakalama filtresi** metin kutusuna bir filtre ekleyin. Örneğin, **host contosoemaildist.Table.Core.Windows.net** , Wireshark ' yi yalnızca **contosoemaildist** depolama hesabındaki tablo hizmeti uç noktasına veya sunucudan gönderilen paketleri yakalamaya alacak şekilde yapılandırır. [Yakalama filtrelerinin tüm listesini](https://wiki.wireshark.org/CaptureFilters)inceleyin.

   ![Yakalama filtresi metin kutusuna bir filtrenin nasıl ekleneceğini gösteren ekran görüntüsü.][6]
5. **Başlat**'a tıklayın. Wireshark şimdi, yerel makinenizde istemci uygulamanızı kullanırken tablo hizmeti uç noktasına gönderilen tüm paketleri yakalar.
6. İşiniz bittiğinde, ana menüden **yakala** ' ya ve ardından **Durdur**' a tıklayın.
7. Yakalanan verileri bir Wireshark yakalama dosyasına kaydetmek için, ana menüdeki **Dosya** ' ya ve ardından **Kaydet**' e tıklayın.

WireShark, **packetlist** penceresinde var olan tüm hataları vurgulayacaktır. Ayrıca, hataların ve uyarıların özetini görüntülemek için **uzman bilgisi** penceresini ( **Çözümle** ve ardından **uzman bilgileri**' ne tıklayın) kullanabilirsiniz.

![Hataların ve uyarıların özetini görüntüleyebileceğiniz uzman bilgisi penceresini gösteren ekran görüntüsü.][7]

Ayrıca, TCP verilerini, TCP verilerine sağ tıklayıp **TCP akışını izle**' yi seçerek Inceleyerek, TCP verilerini görüntülemeyi tercih edebilirsiniz. Bu, döküm dosyanızı yakalama filtresi olmadan yakaladıysanız yararlı olur. Daha fazla bilgi için bkz. [TCP akışları](https://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).

![Uygulama katmanı tarafından görülen TCP verilerinin nasıl görüntüleneceğini gösteren ekran görüntüsü.][8]

> [!NOTE]
> Wireshark kullanma hakkında daha fazla bilgi için bkz. [Wireshark Users Guide](https://www.wireshark.org/docs/wsug_html_chunked).
>
>

### <a name="appendix-4-using-excel-to-view-metrics-and-log-data"></a><a name="appendix-4"></a>Ek 4: ölçümleri ve günlük verilerini görüntülemek için Excel kullanma
Birçok araç, Azure Tablo depolamadan depolama ölçümleri verilerini, görüntüleme ve analiz amacıyla Excel 'e yüklemeyi kolaylaştıran ayrılmış bir biçimde indirmenizi sağlar. Azure Blob Storage 'dan alınan depolama günlüğü verileri, Excel 'e yükleyebildiğiniz ayrılmış bir biçimde zaten vardır. Ancak, [depolama Analizi günlük biçimi](/rest/api/storageservices/Storage-Analytics-Log-Format) ve [depolama Analizi ölçüm tablosu şeması](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema)bilgilerine göre uygun sütun başlıkları eklemeniz gerekir.

Depolama günlüğü verilerinizi blob depolamadan indirdikten sonra Excel 'e aktarmak için:

* **Veri** menüsünde, **metin**' e tıklayın.
* Görüntülemek istediğiniz günlük dosyasına gidin ve **Içeri aktar**' a tıklayın.
* **Metin Içeri aktarma Sihirbazı**'nın 1. adımında, **sınırlandırılmış**' ı seçin.

**Metin Içeri aktarma Sihirbazı**'nın 1. adımında, tek sınırlayıcı olarak **noktalı virgül** ' i seçin ve **metin niteleyicisi** olarak çift tırnak işareti seçin. Ardından **son** ' a tıklayın ve verilerin çalışma kitabınıza yerleştirileceği yeri seçin.

### <a name="appendix-5-monitoring-with-application-insights-for-azure-devops"></a><a name="appendix-5"></a>Ek 5: Azure DevOps için Application Insights ile Izleme
Ayrıca, performans ve kullanılabilirlik izlemenin bir parçası olarak Azure DevOps için Application Insights özelliğini de kullanabilirsiniz. Bu araç şunları yapabilir:

* Web hizmetinizin kullanılabilir ve yanıt verdiğini doğrulayın. Uygulamanız Web hizmeti kullanan bir Web sitesi veya bir cihaz uygulaması olsa da, dünyanın dört bir yanındaki konumlardan birkaç dakikada bir URL 'YI test edebilir ve bir sorun olup olmadığını bilmenizi sağlayabilir.
* Web hizmetinizde performans sorunlarını veya özel durumları hızlıca tanılayın. CPU veya diğer kaynakların uzatılmakta olup olmadığını öğrenin, özel durumların yığın izlemelerini alın ve günlük izlemelerinde kolayca arama yapın. Uygulamanın performansı kabul edilebilir limitlerin altına düşerse, Microsoft size e-posta gönderebilir. Hem .NET hem de Java Web Hizmetleri 'ni izleyebilirsiniz.

[Application Insights ne olduğunu](../../azure-monitor/app/app-insights-overview.md)öğrenmek için daha fazla bilgi bulabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Azure depolama 'da analiz hakkında daha fazla bilgi için şu kaynaklara bakın:

* [Azure portalında depolama hesabını izleme](./manage-storage-analytics-logs.md)
* [Depolama Analizi](storage-analytics.md)
* [Depolama Analizi ölçümleri](storage-analytics-metrics.md)
* [Storage Analytics ölçüm tablosu şeması](/rest/api/storageservices/storage-analytics-metrics-table-schema)
* [Depolama analizi günlükleri](storage-analytics-logging.md)
* [Depolama Analizi günlük biçimi](/rest/api/storageservices/storage-analytics-log-format)

<!--Anchors-->
[Giriş]: #introduction
[Bu kılavuz nasıl düzenlenir]: #how-this-guide-is-organized

[Depolama hizmetinizi izleme]: #monitoring-your-storage-service
[İzleme hizmeti durumu]: #monitoring-service-health
[İzleme kapasitesi]: #monitoring-capacity
[Kullanılabilirliği izleme]: #monitoring-availability
[Performansı izleme]: #monitoring-performance

[Depolama sorunlarını tanılama]: #diagnosing-storage-issues
[Hizmet durumu sorunları]: #service-health-issues
[Performans sorunları]: #performance-issues
[Hataları tanılama]: #diagnosing-errors
[Depolama öykünücüsü sorunları]: #storage-emulator-issues
[Depolama günlük araçları]: #storage-logging-tools
[Ağ günlüğü araçlarını kullanma]: #using-network-logging-tools

[Uçtan uca izleme]: #end-to-end-tracing
[Günlük verilerini ilişkilendirme]: #correlating-log-data
[İstemci istek kimliği]: #client-request-id
[Sunucu istek KIMLIĞI]: #server-request-id
[Zaman damgaları]: #timestamps

[Sorun giderme kılavuzu]: #troubleshooting-guidance
[Ölçümler yüksek AverageE2ELatency ve düşük AverageServerLatency gösteriyor]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[Ölçümler yüksek AverageE2ELatency ve düşük AverageServerLatency gösteriyor, ancak istemci yüksek gecikme durumu yaşıyor]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[Ölçümler yüksek AverageServerLatency gösteriyor]: #metrics-show-high-AverageServerLatency
[Kuyrukta ileti tesliminde beklenmedik gecikmeler yaşıyorsunuz]: #you-are-experiencing-unexpected-delays-in-message-delivery

[Ölçümler PercentThrottlingError’da artış gösteriyor]: #metrics-show-an-increase-in-PercentThrottlingError
[Percentkısıtlar Lingerror 'da geçici artış]: #transient-increase-in-PercentThrottlingError
[Percentkısıtlar Lingerror hatası içinde kalıcı artış]: #permanent-increase-in-PercentThrottlingError
[Ölçümler PercentTimeoutError’da artış gösteriyor]: #metrics-show-an-increase-in-PercentTimeoutError
[Ölçümler PercentNetworkError’da artış gösteriyor]: #metrics-show-an-increase-in-PercentNetworkError

[İstemci HTTP 403 (Yasak) iletileri alıyor]: #the-client-is-receiving-403-messages
[İstemci HTTP 404 (Bulunamadı) iletileri alıyor]: #the-client-is-receiving-404-messages
[İstemci veya başka bir işlem daha önce nesneyi silmiş]: #client-previously-deleted-the-object
[Paylaşılan Erişim İmzası (SAS) yetkilendirme sorunu]: #SAS-authorization-issue
[İstemci tarafı JavaScript kodunun nesneye erişim izni yok]: #JavaScript-code-does-not-have-permission
[Ağ hatası]: #network-failure
[İstemci HTTP 409 (Çakışma) iletileri alıyor]: #the-client-is-receiving-409-messages

[Ölçümler düşük PercentSuccess veya Analytics günlük girişlerinde, Clienentothererrors işlem durumu ile işlemler var]: #metrics-show-low-percent-success
[Kapasite ölçümleri, depolama kapasitesi kullanımında beklenmedik bir artış gösterir]: #capacity-metrics-show-an-unexpected-increase
[Sorununuz geliştirme veya test için depolama öykünücüsünün kullanılması durumunda]: #your-issue-arises-from-using-the-storage-emulator
["X" özelliği depolama öykünücüsünde çalışmıyor]: #feature-X-is-not-working
["Depolama öykünücüsü kullanılırken HTTP başlıklarındaki bir değer doğru biçimde değil" hatası]: #error-HTTP-header-not-correct-format
[Depolama öykünücüsünü çalıştırmak için yönetici ayrıcalıkları gerekir]: #storage-emulator-requires-administrative-privileges
[.NET için Azure SDK 'Yı yüklerken sorunlarla karşılaşdığınızda]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Depolama hizmeti ile farklı bir sorununuz var]: #you-have-a-different-issue-with-a-storage-service

[Ekler]: #appendices
[Ek 1: HTTP ve HTTPS trafiğini yakalamak için Fiddler kullanma]: #appendix-1
[Ek 2: ağ trafiğini yakalamak için Wireshark kullanma]: #appendix-2
[Ek 4: ölçümleri ve günlük verilerini görüntülemek için Excel kullanma]: #appendix-4
[Ek 5: Azure DevOps için Application Insights ile Izleme]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting/overview.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-2.png