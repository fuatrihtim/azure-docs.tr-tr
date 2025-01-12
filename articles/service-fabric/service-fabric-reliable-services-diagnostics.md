---
title: Azure Service Fabric durum bilgisi Reliable Services tanılaması
description: Azure Service Fabric durum bilgisi olan Reliable Services için tanılama işlevi
ms.topic: conceptual
ms.date: 8/24/2018
ms.openlocfilehash: 774a771d0c9701076a5d6c070963bf6224a571dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98789340"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Durum Bilgisi Olan Reliable Services için tanılama işlevi
Azure Service Fabric durum bilgisi olan Reliable Services StatefulServiceBase sınıfı, hizmette hata ayıklamak, çalışma zamanının nasıl çalıştığı hakkında Öngörüler sağlamak ve sorun gidermeye yardımcı olmak için kullanılan [EventSource](/dotnet/api/system.diagnostics.tracing.eventsource) olaylarını yayar.

## <a name="eventsource-events"></a>EventSource olayları
Durum bilgisi olan Reliable Services StatefulServiceBase sınıfının EventSource adı "Microsoft-ServiceFabric-Services" dır. Bu olay kaynağının olayları, hizmetin [Visual Studio 'da hata ayıklaması](service-fabric-debugging-your-application.md)yapıldığında [Tanılama olayları](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) penceresinde görüntülenir.

EventSource olaylarını toplama ve/veya görüntüleme konusunda yardımcı olan araç ve teknolojilerin örnekleri, [PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [Azure tanılama](../cloud-services/cloud-services-dotnet-diagnostics.md)ve [Microsoft TraceEvent Library](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent)' dir.

## <a name="events"></a>Ekinlikler
| Olay adı | Olay Kimliği | Level | Olay açıklaması |
| --- | --- | --- | --- |
| Statefulrunasyncınvocation |1 |Bilgilendirici |Service RunAsync görevi başlatıldığında yayılır |
| Statefulrunasynciptali |2 |Bilgilendirici |Service RunAsync görevi iptal edildiğinde yayılır |
| StatefulRunAsyncCompletion |3 |Bilgilendirici |Service RunAsync görevi tamamlandığında yayılır |
| Statefulrunasyncslowiptali |4 |Uyarı |Service RunAsync görevi iptali tamamlanamayacak kadar uzun sürerse yayılır |
| StatefulRunAsyncFailure |5 |Hata |Service RunAsync görevi bir özel durum oluşturduğunda yayılır |

## <a name="interpret-events"></a>Olayları yorumlama
Statefulrunasyncınvoıng, StatefulRunAsyncCompletion ve Statefulrunasynciptali olayları, bir hizmetin yaşam döngüsünü ve bir hizmetin ne zaman başlatılacağını, iptal edileceğini veya tamamlandığını anlamak için hizmet yazıcısı için yararlıdır. Bu bilgiler, hizmet sorunlarını ayıkladığınızda veya hizmet yaşam döngüsünü anlamak için yararlı olabilir.

Hizmet yazarları, hizmetle ilgili sorunları gösterdiklerinden, Statefulrunasyncslowiptali ve StatefulRunAsyncFailure olaylarına yakın bir ilgi ödemelidir.

Service RunAsync () görevi bir özel durum oluşturduğunda StatefulRunAsyncFailure yayınlanır. Genellikle, oluşan bir özel durum hizmette bir hata veya hata olduğunu gösterir. Ayrıca, özel durum hizmetin başarısız olmasına neden olur, bu nedenle farklı bir düğüme taşınır. Bu işlem pahalı olabilir ve hizmet taşındığında gelen istekleri geciktirebilirler. Hizmet yazarları özel durumun nedenini belirlemelidir ve mümkünse bunun etkisini azaltır.

RunAsync görevi için bir iptal isteği dört saniyeden uzun sürdiğinde Statefulrunasyncslowiptali yayınlanır. Bir hizmetin iptali tamamlaması çok uzun sürerse, hizmetin başka bir düğümde hızlı bir şekilde yeniden başlatılması yeteneğini etkiler. Bu senaryo hizmetin genel kullanılabilirliğini etkileyebilir.

## <a name="performance-counters"></a>Performans sayaçları
Reliable Services çalışma zamanı aşağıdaki performans sayacı kategorilerini tanımlar:

| Kategori | Açıklama |
| --- | --- |
| Işlem çoğaltıcı Service Fabric |Azure Service Fabric Işlemsel çoğaltıcıya özel sayaçlar |
| Service Fabric TStore |Azure Service Fabric TStore 'a özel sayaçlar |

Service Fabric Işlem Çoğaltıcısı, [güvenilir durum Yöneticisi](./service-fabric-reliable-services-introduction.md) tarafından, belirli bir [çoğaltmalar](service-fabric-concepts-replica-lifecycle.md)kümesi içindeki işlemleri çoğaltmak için kullanılır.

Service Fabric TStore, anahtar-değer çiftlerini depolamak ve almak için [güvenilir koleksiyonlarda](./service-fabric-reliable-services-introduction.md) kullanılan bir bileşendir.

Windows işletim sisteminde varsayılan olarak kullanılabilir olan [Windows Performans İzleyicisi](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749249(v=ws.11)) uygulaması, performans sayacı verilerini toplamak ve görüntülemek için kullanılabilir. [Azure tanılama](../cloud-services/cloud-services-dotnet-diagnostics.md) , performans sayacı verilerini toplamaya ve Azure tablolarına yüklemeye yönelik başka bir seçenektir.

### <a name="performance-counter-instance-names"></a>Performans sayacı örnek adları
Çok sayıda güvenilir hizmet veya güvenilir hizmet bölümü olan bir küme, çok sayıda işlem çoğaltıcı performans sayacı örneğine sahip olacaktır. Bu Ayrıca, TStore performans sayaçları için de kullanılır, ancak aynı zamanda kullanılan güvenilir sözlüklerin ve güvenilir sıraların sayısıyla çarpılır. Performans sayacı örneği adları, TStore durumunda performans sayacı örneğinin ilişkilendirildiği belirli [bölümü](service-fabric-concepts-partitioning.md), hizmet çoğaltmasını ve durum sağlayıcısını tanımlamaya yardımcı olabilir.

#### <a name="service-fabric-transactional-replicator-category"></a>Işlem çoğaltıcı kategorisini Service Fabric
Kategori için `Service Fabric Transactional Replicator` , sayaç örneği adları aşağıdaki biçimdedir:

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*Servicefabricpartitionıd* , performans sayacı örneğinin ilişkilendirildiği SERVICE fabrıc bölüm kimliğinin dize gösterimidir. Bölüm KIMLIĞI bir GUID 'dir ve dize temsili [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) "D" biçim belirticisi ile birlikte oluşturulur.

*Servicefabricreplicaıd* , güvenilir bir hizmetin belirli bir çoğaltmasından ILIŞKILI olan kimliğidir. Benzersizlik sağlamak ve aynı bölüm tarafından oluşturulan diğer performans sayacı örnekleriyle çakışmadan kaçınmak için, çoğaltma KIMLIĞI performans sayacı örneği adına dahil edilir. Çoğaltmalarla ilgili daha fazla ayrıntı ve güvenilir hizmetlerde rolü [burada](service-fabric-concepts-replica-lifecycle.md)bulabilirsiniz.

Aşağıdaki sayaç örneği adı, kategori altındaki bir sayaç için tipik olarak verilmiştir `Service Fabric Transactional Replicator` :

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

Yukarıdaki örnekte, `00d0126d-3e36-4d68-98da-cc4f7195d85e` Service Fabric bölüm kimliğinin dize gösterimidir ve `131652217797162571` çoğaltma kimliği olur.

#### <a name="service-fabric-tstore-category"></a>Service Fabric TStore kategorisi
Kategori için `Service Fabric TStore` , sayaç örneği adları aşağıdaki biçimdedir:

`ServiceFabricPartitionId:ServiceFabricReplicaId:StateProviderId_PerformanceCounterInstanceDifferentiator_StateProviderName`

*Servicefabricpartitionıd* , performans sayacı örneğinin ilişkilendirildiği SERVICE fabrıc bölüm kimliğinin dize gösterimidir. Bölüm KIMLIĞI bir GUID 'dir ve dize temsili [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) "D" biçim belirticisi ile birlikte oluşturulur.

*Servicefabricreplicaıd* , güvenilir bir hizmetin belirli bir çoğaltmasından ILIŞKILI olan kimliğidir. Benzersizlik sağlamak ve aynı bölüm tarafından oluşturulan diğer performans sayacı örnekleriyle çakışmadan kaçınmak için, çoğaltma KIMLIĞI performans sayacı örneği adına dahil edilir. Çoğaltmalarla ilgili daha fazla ayrıntı ve güvenilir hizmetlerde rolü [burada](service-fabric-concepts-replica-lifecycle.md)bulabilirsiniz.

*Stateproviderıd* , güvenilir bir hizmet içindeki bir durum SAĞLAYıCıSıYLA ilişkili kimliğidir. Durum sağlayıcısı KIMLIĞI, bir TStore 'ı diğerinden ayırt etmek için performans sayacı örneği adına dahil edilir.

*PerformanceCounterInstanceDifferentiator* , bir durum sağlayıcısı içindeki bir performans sayacı örneğiyle ilişkili bir FARKLıLAŞTıRıCı kimliğidir. Bu fark eden, benzersizlik sağlamak ve aynı durum sağlayıcısı tarafından oluşturulan diğer performans sayacı örnekleriyle çakışmamak için performans sayacı örneği adına dahildir.

*Stateprovidername* , güvenilir bir hizmet içindeki bir durum sağlayıcısıyla ilişkili addır. Durum sağlayıcısı adı, kullanıcıların hangi durumu sağladığını kolayca belirlemek için performans sayacı örnek adına dahil edilir.

Aşağıdaki sayaç örneği adı, kategori altındaki bir sayaç için tipik olarak verilmiştir `Service Fabric TStore` :

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571:142652217797162571_1337_urn:MyReliableDictionary/dataStore`

Yukarıdaki örnekte, `00d0126d-3e36-4d68-98da-cc4f7195d85e` Service Fabric bölüm kimliğinin dize gösterimidir, `131652217797162571` çoğaltma kimliği, `142652217797162571` durum sağlayıcı kimliğidir ve `1337` performans sayacı örneği farklıdır. `urn:MyReliableDictionary/dataStore` , adlı koleksiyon için veri depolayan durum sağlayıcısının adıdır `urn:MyReliableDictionary` .

### <a name="transactional-replicator-performance-counters"></a>İşlem çoğaltıcı performans sayaçları

Reliable Services Runtime, kategori altında aşağıdaki olayları yayar `Service Fabric Transactional Replicator`

 Sayaç adı | Description |
| --- | --- |
| Başlangıç TXN Işlemi/sn | Saniye başına oluşturulan yeni yazma işlemi sayısı.|
| TXN Işlemi/sn | Saniye başına güvenilir koleksiyonlar üzerinde gerçekleştirilen ekleme/güncelleştirme/silme işlemlerinin sayısı.|
| Günlük Temizleme bayt/sn | Saniye başına Işlem çoğaltıcı tarafından diske temizlenen bayt sayısı |
| Kısıtlanmış Işlem/sn | Azaltma nedeniyle Işlem Çoğaltıcısı tarafından her saniye reddedilen işlem sayısı. |
| Ort. Işlem MS/Işleme | İşlem başına ortalama işleme gecikme süresi (milisaniye) |
| Ort. Temizleme gecikmesi (MS) | Milisaniye cinsinden Işlem Çoğaltıcısı tarafından başlatılan disk temizleme işlemlerinin ortalama süresi |

### <a name="tstore-performance-counters"></a>TStore performans sayaçları

Reliable Services Runtime, kategori altında aşağıdaki olayları yayar `Service Fabric TStore`

 Sayaç adı | Description |
| --- | --- |
| Öğe sayısı | Depodaki öğelerin sayısı.|
| Disk Boyutu | Mağaza için denetim noktası dosyalarının bayt cinsinden toplam disk boyutu.|
| Kontrol noktası dosya yazma bayt/sn | En son denetim noktası dosyası için saniye başına yazılan bayt sayısı.|
| Kopyalama disk aktarımı bayt/sn | Bir mağaza kopyası sırasında okunan disk bayt sayısı (birincil çoğaltmadaki) veya saniye başına yazılan (ikincil bir çoğaltmada).|

## <a name="next-steps"></a>Sonraki adımlar
[PerfView 'da EventSource sağlayıcıları](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource)
