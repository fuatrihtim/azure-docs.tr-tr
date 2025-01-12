---
title: Mevcut bir yürütülebiliri Azure 'da paketleyin Service Fabric
description: Mevcut bir uygulamayı Konuk yürütülebilir dosyası olarak paketleme hakkında bilgi edinin. bu nedenle bir Service Fabric kümesine dağıtılabilir.
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 8b808d092001196a4d2150e44d508e031db95554
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017755"
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>Service Fabric için mevcut bir yürütülebiliri dağıtın
Azure Service Fabric hizmet olarak Node.js, Java veya C++ gibi herhangi bir tür kodu çalıştırabilirsiniz. Service Fabric Konuk yürütülebilir dosyaları olan bu hizmet türlerine başvurur.

Konuk yürütülebilir dosyaları, durum bilgisi olmayan hizmetler gibi Service Fabric tarafından işlenir. Sonuç olarak, kullanılabilirlik ve diğer ölçümlere göre bir kümedeki düğümlere yerleştirilir. Bu makalede, Visual Studio veya komut satırı yardımcı programını kullanarak bir Service Fabric kümesine Konuk yürütülebilir dosyası paketleme ve dağıtma açıklanır.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Service Fabric bir konuk yürütülebilir dosyası çalıştırmanın avantajları
Service Fabric kümesinde Konuk yürütülebilir dosyası çalıştırmanın çeşitli avantajları vardır:

* Yüksek kullanılabilirlik. Service Fabric çalışan uygulamalar yüksek oranda kullanılabilir hale getirilir. Service Fabric, bir uygulamanın örneklerinin çalıştırılmasını sağlar.
* Sistem durumu izleme. Service Fabric sistem durumu izleme, bir uygulamanın çalışıp çalışmadığını algılar ve bir hata varsa tanılama bilgileri sağlar.   
* Uygulama yaşam döngüsü yönetimi. Kapalı kalma süresi olmadan yükseltmeler sağlamanın yanı sıra, yükseltme sırasında hatalı bir sistem durumu olayı oluşursa, Service Fabric önceki sürüme otomatik geri alma olanağı sağlar.    
* Yoğunluklu. Birden çok uygulamayı bir kümede çalıştırabilirsiniz ve bu, her uygulamanın kendi donanımında çalışması gereksinimini ortadan kaldırır.
* Keşfedilebilir: REST kullanarak kümedeki diğer hizmetleri bulmak için Service Fabric adlandırma hizmetini çağırabilirsiniz. 

## <a name="samples"></a>Örnekler
* [Konuk yürütülebilir dosyası paketleme ve dağıtma örneği](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [REST kullanarak adlandırma hizmeti üzerinden iletişim kuran iki konuk yürütülebilir dosya (C# ve NodeJS) örneği](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Uygulama ve hizmet bildirim dosyalarına genel bakış
Konuk yürütülebiliri dağıtmanın bir parçası olarak, Service Fabric paketleme ve dağıtım modelinin [uygulama modeli](service-fabric-application-model.md)bölümünde açıklandığı gibi anlaşılması yararlı olur. Service Fabric paketleme modeli iki XML dosyasına dayanır: uygulama ve hizmet bildirimleri. ApplicationManifest.xml ve ServiceManifest.xml dosyaları için şema tanımı, Service Fabric SDK 'Sı ile *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*'e yüklenir.

* **Uygulama bildirimi** Uygulama bildirimi, uygulamayı tanımlamakta kullanılır. Bu, oluşturan Hizmetleri ve örnek sayısı gibi bir veya daha fazla hizmetin nasıl dağıtılması gerektiğini tanımlamak için kullanılan diğer parametreleri listeler.

  Service Fabric, uygulama bir dağıtım ve yükseltme birimidir. Bir uygulama, olası hataların ve olası geri göndermeler yönetildiği tek bir birim olarak yükseltilebilir. Service Fabric yükseltme işleminin başarılı olmasını güvence altına alır veya yükseltme başarısız olursa, uygulamayı bilinmeyen veya kararsız durumda bırakmaz.
* **Hizmet bildirimi** Hizmet bildirimi, bir hizmetin bileşenlerini açıklar. Bu, hizmetin adı ve türü ile kodu ve yapılandırması gibi verileri içerir. Hizmet bildirimi aynı zamanda hizmeti dağıtıldıktan sonra yapılandırmak için kullanılabilecek bazı ek parametreler de içerir.

## <a name="application-package-file-structure"></a>Uygulama paketi dosya yapısı
Bir uygulamayı Service Fabric dağıtmak için uygulama önceden tanımlanmış bir dizin yapısını izlemelidir. Aşağıda bu yapıya bir örnek verilmiştir.

```
|-- ApplicationPackageRoot
    |-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```

ApplicationPackageRoot, uygulamayı tanımlayan ApplicationManifest.xml dosyasını içerir. Uygulamanın içerdiği her hizmet için bir alt dizin, hizmetin gerektirdiği tüm yapıtları içermesi için kullanılır. Bu alt dizinler ServiceManifest.xml ve genellikle aşağıdakiler şunlardır:

* *Kod*. Bu dizin, hizmet kodunu içerir.
* *Yapılandırma*. Bu dizin, belirli yapılandırma ayarlarını almak için hizmetin çalışma zamanında erişebileceği bir Settings.xml dosyası (ve gerekirse diğer dosyaları) içerir.
* *Veri*. Bu, hizmetin ihtiyacı olabilecek ek yerel verileri depolamak için ek bir dizindir. Verilerin yalnızca kısa ömürlü verileri depolamak için kullanılması gerekir. Service Fabric, hizmetin yeniden konumlandırılması gerekiyorsa (örneğin, yük devretme sırasında) veri dizinine değişiklikleri kopyalamaz veya çoğaltmaz.

> [!NOTE]
> İhtiyacınız yoksa ve dizinleri oluşturmanız gerekmez `config` `data` .
>
>

## <a name="next-steps"></a>Sonraki adımlar
İlgili bilgi ve görevler için aşağıdaki makalelere bakın.
* [Konuk tarafından yürütülebilir bir uygulama dağıtma](service-fabric-deploy-existing-app.md)
* [Konuk tarafından yürütülebilir birden çok uygulama dağıtma](./service-fabric-deploy-existing-app.md)
* [Visual Studio kullanarak ilk Konuk yürütülebilir uygulamanızı oluşturma](quickstart-guest-app.md)
* Paketleme aracının ön sürümüne bir bağlantı dahil olmak üzere, [Konuk yürütülebilir dosyası paketleme ve dağıtma örneği](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [REST kullanarak adlandırma hizmeti üzerinden iletişim kuran iki konuk yürütülebilir dosya (C# ve NodeJS) örneği](https://github.com/Azure-Samples/service-fabric-containers)
