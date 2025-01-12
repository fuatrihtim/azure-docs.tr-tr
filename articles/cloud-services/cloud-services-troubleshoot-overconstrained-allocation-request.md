---
title: Azure 'a bir bulut hizmeti (klasik) dağıtımı sırasında OverconstrainedAllocationRequest sorunlarını giderme | Microsoft Docs
description: Bu makalede, Azure 'a bir bulut hizmeti (klasik) dağıtımında bir OverconstrainedAllocationRequest özel durumunun nasıl çözümleneceği gösterilmektedir.
services: cloud-services
documentationcenter: ''
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 1b50ded166b3f62b38830b4c2d18da7c4c4f0d35
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101746644"
---
# <a name="troubleshoot-overconstrainedallocationrequest-when-deploying-cloud-services-classic-to-azure"></a>Azure 'da Cloud Services (klasik) dağıtımı sırasında OverconstrainedAllocationRequest sorunlarını giderme

Bu makalede, Azure Cloud Services (klasik) dağıtımını önleyen kısıtlı ayırma hatalarıyla ilgili olarak sorun giderirsiniz.

Bir bulut hizmetine örnekler dağıtırken veya yeni Web ya da çalışan rolü örnekleri eklediğinizde Microsoft Azure işlem kaynaklarını ayırır.

Azure abonelik sınırına ulaşmadan bile bu işlemler sırasında zaman zaman bir hata alabilirsiniz.

> [!TIP]
> Ayrıca, hizmetlerinizin dağıtımını planlarken bilgiler de yararlı olabilir.

## <a name="symptom"></a>Belirti

![Görüntü, Işlem günlüğü (klasik) dikey penceresini gösterir.](./media/cloud-services-troubleshoot-overconstrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

|Özel durum türü  |Hata İletisi  |
|---------|---------|
|OverconstrainedAllocationRequest |Bu dağıtım için gerekli VM boyutu (veya VM boyutlarının birleşimi), dağıtım isteği kısıtlamaları nedeniyle sağlanamıyor. Mümkünse, sanal ağ bağlamaları gibi kısıtlamaları yeniden deneyin, içinde başka bir dağıtım olmadan barındırılan bir hizmete dağıtıp, farklı bir benzeşim grubuna veya benzeşim grubu olmadan veya farklı bir bölgeye dağıtım yapmayı deneyin.|

## <a name="cause"></a>Nedeni

Kök nedeni, bulut hizmetinin **sabitlendiği** veya **sabitlenmemiş** olmasına göre değişir.

- [**Sabitlenmiş değil:** Yeni bir bulut hizmetinin ilk dağıtımından oluşan başarısızlık (klasik)](#not-pinned-to-a-cluster)
- [**Sabitlenmiş:** Var olan bir bulut hizmetinden alınan sorunlar (klasik)](#pinned-to-a-cluster)

> [!NOTE]
> İlk örnek bir bulut hizmetine dağıtıldığında (herhangi bir hazırlama veya üretimde), bu bulut hizmeti bir kümeye sabitlenir.
>
> Zaman içinde, kümedeki kaynaklar tam olarak kullanılabilir hale gelebilir. Bir bulut hizmeti (klasik), sabitlenmiş kümede yeterli kaynak yoksa, ek kaynaklar için bir ayırma isteği yapıyorsa, istek bir [ayırma hatasına](cloud-services-allocation-failures.md)neden olur.

## <a name="solution"></a>Çözüm

Aşağıdaki senaryolarda ayırma hatalarıyla ilgili yönergeleri izleyin.

### <a name="not-pinned-to-a-cluster"></a>Bir kümeye sabitlenmedi

Bir bulut hizmeti (klasik) ilk kez dağıttığınızda, küme henüz seçilmemiştir, bu nedenle bulut hizmeti *sabitlenmez*. Azure 'un bir dağıtım hatası olabilir, nedeni:

- Bölgede kullanılamayan belirli bir boyutu seçtiniz.
- Farklı roller genelinde gereken boyutların birleşimi bölgede kullanılamaz.

Bu senaryoda bir ayırma hatası yaşarsanız, önerilen eylem, bölgedeki kullanılabilir boyutları denet, daha önce belirttiğiniz boyutu değiştirecek.

1. [Bulut hizmeti (klasik) ürünleri](https://azure.microsoft.com/global-infrastructure/services/?products=cloud-services) sayfasındaki bir bölgede kullanılabilir olan boyutları kontrol edebilirsiniz.

    > [!NOTE]
    > *Ürünler* sayfası kullanılabilir kapasiteyi göstermez. Yeni bir ayırma için, Azure 'un bölgede en uygun kümeyi bu noktada seçebilmelidir.

1. Bölgenizde farklı bir [Ürün boyutu](cloud-services-sizes-specs.md#configure-sizes-for-cloud-services) belirtmek için bulut hizmetiniz (klasik) hizmet tanım dosyasını güncelleştirin.

### <a name="pinned-to-a-cluster"></a>Bir kümeye sabitlenmiş

Mevcut bulut hizmetleri bir kümeye *sabitlenmiştir* . Bulut hizmeti (klasik) için başka dağıtımlar da aynı kümede olur.

Bu senaryoda bir ayırma hatası yaşarsanız, önerilen eylem kursu yeni bir bulut hizmetine (klasik) yeniden dağıtmanız (ve *CNAME*'i güncelleştirmeniz).

> [!TIP]
> Platformun o bölgedeki tüm kümelerden seçmesine olanak tanıdığı için büyük ihtimalle en başarılı çözüm bu olacaktır.

> [!NOTE]
> Bu çözümün sıfır kapalı kalma süresi olması gerekir.

1. İş yükünü yeni bir bulut hizmetine (klasik) dağıtın.
    - Daha fazla yönerge için bkz. [bulut hizmeti oluşturma ve dağıtma (klasik)](cloud-services-how-to-create-deploy-portal.md) Kılavuzu.

    > [!WARNING]
    > Bu dağıtım yuvasıyla ilişkili IP adresini kaybetmek istemiyorsanız, çözüm 3 ' ü kullanabilirsiniz [-IP adresini saklayın](cloud-services-allocation-failures.md#solutions).

1. *CNAME* veya *bir* kaydı, trafiği yeni bulut hizmetine (klasik) işaret etmek üzere güncelleştirin.
    - Daha fazla yönerge için bkz. [Azure bulut hizmeti için özel etki alanı adı yapılandırma (klasik)](cloud-services-custom-domain-name-portal.md#understand-cname-and-a-records) Kılavuzu.

1. Sıfır trafik eski siteye gittikten sonra eski bulut hizmetini (klasik) silebilirsiniz.
    - Daha fazla yönerge için [dağıtımları silme ve bulut hizmeti (klasik)](cloud-services-how-to-manage-portal.md#delete-deployments-and-a-cloud-service) kılavuzuna bakın.
    - Bulut hizmetinizdeki (klasik) ağ trafiğini görmek için bkz. [bulut hizmeti 'Ne giriş (klasik) izleme](cloud-services-how-to-monitor.md).

Bkz. [bulut hizmeti (klasik) ayırma hatalarında sorun giderme | ](cloud-services-allocation-failures.md#common-issues) Daha fazla düzeltme adımları için Microsoft docs.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla ayırma hatası çözümü ve arka plan bilgileri için:

> [!div class="nextstepaction"]
> [Ayırma arızaları-bulut hizmeti (klasik)](cloud-services-allocation-failures.md)

Azure sorununuz bu makalede giderilmemişse [MSDN ve Stack Overflow](https://azure.microsoft.com/support/forums/)Azure forumlarını ziyaret edin. Sorununuzu bu forumlara gönderebilir veya [ @AzureSupport Twitter 'da](https://twitter.com/AzureSupport)ilan edebilirsiniz. Ayrıca, bir Azure destek isteği gönderebilirsiniz. Destek isteği göndermek için [Azure desteği](https://azure.microsoft.com/support/options/) sayfasında *Destek Al*' ı seçin.
