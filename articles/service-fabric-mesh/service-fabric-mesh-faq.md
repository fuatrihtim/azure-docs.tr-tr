---
title: Azure Service Fabric ağı için sık sorulan sorular
description: Azure Service Fabric ağı hakkında sık sorulan sorular ve yanıtlar hakkında bilgi edinin.
ms.author: pepogors
ms.date: 4/23/2019
ms.topic: troubleshooting
ms.openlocfilehash: 8e53ab0ae4cc463bea8a6a8cb6d339f94fdcac6d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626045"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Sık sorulan Service Fabric kafes soruları

> [!IMPORTANT]
> Azure Service Fabric ağı önizlemesi devre dışı bırakıldı. Yeni dağıtımlar Service Fabric kafes API 'SI aracılığıyla artık izin verilmeyecektir. Mevcut dağıtımlar için destek 28 Nisan 2021 ' den devam edecektir.
> 
> Ayrıntılar için bkz. [Azure Service Fabric kafes önizleme kullanımdan](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)kaldırma.

Azure Service Fabric Mesh, geliştiricilerin sanal makineleri, depolama alanını veya ağ bileşenlerini yönetmeden mikro hizmet uygulamaları dağıtmasını sağlayan tam olarak yönetilen bir hizmettir. Bu makalede, sık sorulan soruların yanıtları bulunur.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Nasıl yaparım? bir sorun bildirin veya soru sorun musunuz?

Soru sorun, Microsoft mühendislerinden yanıt alın ve [Service-Fabric-kafes-Preview GitHub](https://aka.ms/sfmeshissues)depolarında sorun bildirin.

## <a name="quota-and-cost"></a>Kota ve maliyet

### <a name="what-is-the-cost-of-participating-in-the-preview"></a>Önizlemeye katılma maliyeti nedir?

Şu anda ağ önizlemesine uygulama veya kapsayıcılar dağıtmaya yönelik ücret alınmaz. Faturalandırma için etkinleştirme için lütfen ' de güncelleştirmeler ' i izleyin. Ancak, dağıttığınız kaynakları silmenizi ve bunları etkin bir şekilde test etmediğiniz müddetçe çalışır durumda bırakmanızı öneririz.

### <a name="is-there-a-quota-limit-of-the-number-of-cores-and-ram"></a>Çekirdek ve RAM sayısı için kota sınırı var mı?

Evet. Her abonelik için kotalar şunlardır:

- Uygulama sayısı: 5
- Uygulama başına çekirdek sayısı: 12
- Uygulama başına toplam RAM: 48 GB
- Ağ ve giriş bitiş noktaları: 5
- İliştirebilmeniz gereken Azure birimleri: 10
- Hizmet çoğaltmaları sayısı: 3
- Dağıtabileceğiniz en büyük kapsayıcı 4 çekirdek ve 16GB RAM ile sınırlıdır.
- Kapsayıcılarınıza kısmi çekirdekleri, en fazla 6 çekirdeğe kadar 0,5 çekirdekli artışlarla ayırabilirsiniz.

### <a name="how-long-can-i-leave-my-application-deployed"></a>Uygulamamın ne kadar süreyle dağıtıldığını nasıl bırakırım?

Şu anda bir uygulamanın yaşam süresini iki güne sınırlandırdık. Bu, önizlemeye ayrılan ücretsiz çekirdekler kullanımını en üst düzeye çıkarmak için kullanılır. Sonuç olarak, belirli bir dağıtımı yalnızca 48 saat boyunca sürekli olarak çalıştırmaya izin verilir ve bu süre sonra kapatılacak.

Bu durumla karşılaşırsanız, `az mesh app show` Azure CLI 'de komutunu çalıştırarak sistemin bunu kapatmasını doğrulayabilirsiniz. Döndüğünü görmek için işaretleyin `"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` 

Örnek: 

```azurecli
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

```output
{
  "debugParams": null,
  "description": "Service Fabric Mesh HelloWorld Application!",
  "diagnostics": null,
  "healthState": "Ok",
  "id": "/subscriptions/1134234-b756-4979-84re-09d671c0c345/resourcegroups/myResourceGroup/providers/Microsoft.ServiceFabricMesh/applications/helloWorldApp",
  "location": "eastus",
  "name": "helloWorldApp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "serviceNames": [
    "helloWorldService"
  ],
  "services": null,
  "status": "Failed",
  "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue.",
  "tags": {},
  "type": "Microsoft.ServiceFabricMesh/applications",
  "unhealthyEvaluation": null
}
```

Kaynak grubunu silmek için `az group delete <nameOfResourceGroup>` komutunu kullanın.

## <a name="deployments"></a>Dağıtımlar

### <a name="what-container-images-are-supported"></a>Hangi kapsayıcı görüntüleri destekleniyor?

Windows Fall Creators Update (sürüm 1709) makinesi üzerinde geliştiriyorsanız, yalnızca Windows sürümü 1709 Docker görüntülerini kullanabilirsiniz.

Windows 10 Nisan 2018 Güncelleştirmesi (sürüm 1803) makinesinde geliştiriyorsanız, Windows sürüm 1709 veya Windows sürümü 1803 Docker görüntülerini kullanabilirsiniz.

Aşağıdaki kapsayıcı işletim sistemi görüntüleri hizmetleri dağıtmak için kullanılabilir:
- Windows-windowsservercore ve nanoserver
    - Windows Server 1709
    - Windows Server 1803
    - Windows Server 1809
    - Windows Server 2019 LTSC
- Linux
    - Bilinen kısıtlama yok

> [!NOTE]
> Ağ için Visual Studio Araçları, Windows Server 2019 ve 1809 kapsayıcılarına dağıtımı henüz desteklememektedir.

### <a name="what-types-of-applications-can-i-deploy"></a>Hangi tür uygulamaları dağıtabilirim? 

Bir uygulama kaynağına yerleştirilmiş kısıtlamalara uyan kapsayıcılar içinde çalışan her şeyi dağıtabilirsiniz (kotalar hakkında daha fazla bilgi için yukarıdaki bölümüne bakın). Geçersiz iş yüklerini çalıştırmak veya sistemi (örneğin, araştırma) kullanmak için ağ kullandığınızı tespit ediyorsanız, dağıtımlarınızı sonlandırma ve abonelik listenizi hizmette çalışmaya sonlandırma hakkını saklı tutarız. Belirli bir iş yükünü çalıştırmaya ilişkin sorularınız varsa lütfen bize ulaşın. 

## <a name="developer-experience-issues"></a>Geliştirici deneyimi sorunları

### <a name="dns-resolution-from-a-container-doesnt-work"></a>Bir kapsayıcıdan DNS çözümlemesi çalışmıyor

Bir kapsayıcıdan Service Fabric DNS hizmetine giden DNS sorguları bazı koşullarda başarısız olabilir. Bu araştırılmakta. Azaltmak için:

- Temel kapsayıcı görüntünüz olarak Windows Fall Creators Update (sürüm 1709) veya üstünü kullanın.
- Hizmet adı tek başına işe yaramazsa, tam adı şu şekilde deneyin: ServiceName. ApplicationName.
- Hizmetiniz için Docker dosyasında, `EXPOSE <port>` bağlantı noktasının hizmetinizi kullanıma sunuyoruz bağlantı noktası olduğu yere ekleyin. Örnek:

```Dockerfile
EXPOSE 80
```

### <a name="dns-does-not-work-the-same-as-it-does-for-service-fabric-development-clusters-and-in-mesh"></a>DNS Service Fabric geliştirme kümeleri ve ağ içinde olduğu gibi çalışmaz

Hizmetleri yerel geliştirme kümenizde Azure ağı 'ndan farklı şekilde başvuru yapmanız gerekebilir.

Yerel geliştirme kümenizin kullanımı `{serviceName}.{applicationName}` . Azure Service Fabric Ağı ' nda kullanın `{servicename}` . 

Azure ağı, şu anda uygulamalar genelinde DNS çözümlemesini desteklemez.

Windows 10 ' da Service Fabric geliştirme kümesi çalıştırmaya yönelik diğer bilinen DNS sorunları için bkz. [Windows kapsayıcılarında hata ayıklama](../service-fabric/service-fabric-how-to-debug-windows-containers.md) ve [bilinen DNS sorunları](../service-fabric/service-fabric-dnsservice.md#known-issues).

### <a name="networking"></a>Ağ

Service Fabric ağ NAT, uygulamanızı yerel makinenizde çalıştırmak kullanılırken kaybolabilir. Bunun yapılıp yapılmayacağını tanılamak için komut isteminden aşağıdaki komutu çalıştırın:

`docker network ls` ve listelenmiş olup olmadığını aklınızda edin `servicefabric_nat` .  Aksi takdirde, aşağıdaki komutu çalıştırın: `docker network create -d=nat --subnet 10.128.0.0/24 --gateway 10.128.0.1 servicefabric_nat`

Bu, uygulama zaten yerel olarak dağıtılmış ve sağlıksız bir durumda olsa bile sorunu ele alacak.

### <a name="issues-running-multiple-apps"></a>Birden çok uygulama çalıştıran sorunlar

CPU kullanılabilirliği ve limitlerin tüm uygulamalarda düzeltilmesi ile karşılaşabilirsiniz. Azaltmak için:
- Beş düğümlü bir küme oluşturun.
- Dağıtılan uygulama genelinde hizmetlerde CPU kullanımını azaltın. Örneğin, hizmetinizin Service. YAML dosyasında, `cpu: 1.0` olarak değiştirin `cpu: 0.5`

Birden çok uygulama tek düğümlü bir kümeye dağıtılamaz. Azaltmak için:
- Birden çok uygulamayı yerel bir kümeye dağıttığınızda beş düğümlü bir küme kullanın.
- Test etmekte olduğunuz uygulamaları kaldırın.

### <a name="vs-tooling-has-limited-support-for-windows-containers"></a>VS Tooling, Windows kapsayıcıları için sınırlı desteğe sahiptir

Visual Studio Araçları yalnızca Windows Server 1709 ve 1803 ' nin temel işletim sistemi sürümüne sahip Windows kapsayıcıları 'nın dağıtılmasını destekler. 

## <a name="feature-gaps-and-other-known-issues"></a>Özellik boşlukları ve bilinen diğer sorunlar

### <a name="after-deploying-my-application-the-network-resource-associated-with-it-does-not-have-an-ip-address"></a>Uygulamamı dağıttıktan sonra, onunla ilişkili ağ kaynağının bir IP adresi yok

IP adresinin hemen kullanılamadığı bilinen bir sorun vardır. İlişkili IP adresini görmek için birkaç dakika içinde ağ kaynağının durumunu denetleyin.

### <a name="my-application-fails-to-access-the-right-networkvolume-resource"></a>Uygulamamın doğru ağ/birim kaynağına erişimi başarısız oldu

Uygulama modelinizde, ilişkili kaynağa erişebilmek için ağların ve birimlerin tam kaynak KIMLIĞINI kullanın. Hızlı başlangıç örneğinden bir örnek aşağıda verilmiştir:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

### <a name="when-i-scale-out-all-of-my-containers-are-affected-including-running-ones"></a>Ölçeği ölçeklendirdiğimde, çalışma alanım da dahil olmak üzere tüm kapsayıcılarım etkilendi

Bu bir hatadır ve bir düzelme uygulanmaktadır.

## <a name="next-steps"></a>Sonraki adımlar

Service Fabric ağ hakkında daha fazla bilgi edinmek için [genel bakış](service-fabric-mesh-overview.md)makalesini okuyun.
