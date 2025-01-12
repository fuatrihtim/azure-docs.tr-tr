---
title: Azure Cloud Services etkileyen bir Azure hizmet kesintisini işleme (klasik)
description: Azure Cloud Services etkileyen bir Azure hizmet kesintisi durumunda yapmanız gerekenler hakkında bilgi edinin.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: cdd6c9da5a1895d4aadd73133734cd4c8204ecf1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98742174"
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services-classic"></a>Azure Cloud Services etkileyen bir Azure hizmet kesintisi durumunda yapılacaklar (klasik)

> [!IMPORTANT]
> [Azure Cloud Services (genişletilmiş destek)](../cloud-services-extended-support/overview.md) , Azure Cloud Services ürünü için yeni bir Azure Resource Manager tabanlı dağıtım modelidir.Bu değişiklik ile Azure Service Manager tabanlı dağıtım modelinde çalışan Azure Cloud Services, Cloud Services (klasik) olarak yeniden adlandırıldı ve tüm Yeni dağıtımlar [Cloud Services kullanmalıdır (genişletilmiş destek)](../cloud-services-extended-support/overview.md).

Microsoft 'ta, hizmetlerimizin ihtiyacınız olduğunda her zaman sizin için kullanılabilir olduğundan emin olmak için çok çalıştık. Denetiimizin ötesine geçmeye, planlanmamış hizmet kesintilerine neden olacak şekilde bizi etkilemekte yarar vardır.

Microsoft, hizmet için çalışma süresi ve bağlantı taahhüdünde bir Hizmet Düzeyi Sözleşmesi (SLA) sağlar. Bireysel Azure hizmetleri için SLA, [Azure hizmet düzeyi sözleşmeleri](https://azure.microsoft.com/support/legal/sla/)' nde bulunabilir.

Azure 'da, yüksek oranda kullanılabilir uygulamaları destekleyen birçok yerleşik platform özelliği zaten var. Bu hizmetler hakkında daha fazla bilgi edinmek için bkz. [Azure uygulamaları Için olağanüstü durum kurtarma ve yüksek kullanılabilirlik](/azure/architecture/framework/resiliency/backup-and-recovery).

Bu makalede, bir bütün bölge ana doğal olağanüstü durum veya geniş çaplı hizmet kesintisi nedeniyle bir kesinti yaşandığında, doğru bir olağanüstü durum kurtarma senaryosu ele alınmaktadır. Bunlar nadir oluşumlardır, ancak bir bölgenin tamamı için bir kesinti olması olasılığa hazırlanmanız gerekir. Bir bölgenin tamamı bir hizmet kesintisi yaşıyorsa, verilerinizin yerel olarak yedekli kopyaları geçici olarak devre dışı olur. Coğrafi çoğaltmayı etkinleştirdiyseniz, Azure Storage bloblarınızın ve tablolarının üç ek kopyası farklı bir bölgede depolanır. Tam bir bölgesel kesinti veya birincil bölgenin kurtarılabilir olmadığı bir olağanüstü durum durumunda Azure, tüm DNS girdilerini coğrafi olarak çoğaltılan bölgeye yeniden eşler.

> [!NOTE]
> Bu işlem üzerinde herhangi bir denetiminiz olmadığı ve yalnızca veri merkezi genelindeki hizmet kesintileri için gerçekleşmeyeceği hakkında dikkat edin. Bu nedenle, en yüksek kullanılabilirlik düzeyini elde etmek için uygulamaya özgü diğer yedekleme stratejilerine de güvenmelidir. Daha fazla bilgi için bkz. [Microsoft Azure oluşturulan uygulamalar Için olağanüstü durum kurtarma ve yüksek kullanılabilirlik](/azure/architecture/framework/resiliency/backup-and-recovery). Kendi yük devretmesini etkileyebilmek istiyorsanız, farklı bir bölgedeki verilerinizin salt okunurdur kopyasını oluşturan [Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS)](../storage/common/storage-redundancy.md)kullanımını göz önünde bulundurmanız gerekebilir.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>Seçenek 1: Azure Traffic Manager aracılığıyla bir yedekleme dağıtımı kullanma
En güçlü olağanüstü durum kurtarma çözümü, farklı bölgelerde uygulamanızın birden çok dağıtımını, sonra da aralarında trafiği yönlendirmek için [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) kullanmayı içerir. Azure Traffic Manager birden çok [yönlendirme yöntemi](../traffic-manager/traffic-manager-routing-methods.md)sağlar; bu nedenle, dağıtımlarınızı birincil/yedekleme modeli kullanarak yönetmeyi veya aralarındaki trafiği bölüyi seçebilirsiniz.

![Azure Traffic Manager bölgeler arasında Azure Cloud Services Dengeleme](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

Bir bölgenin kaybedilmesine en hızlı yanıt için Traffic Manager [uç nokta izlemeyi](../traffic-manager/traffic-manager-monitoring.md)yapılandırmanız önemlidir.

## <a name="option-2-deploy-your-application-to-a-new-region"></a>2. seçenek: uygulamanızı yeni bir bölgeye dağıtma
Önceki seçenekte açıklandığı gibi birden çok etkin dağıtımı sürdürmek devam eden ek maliyetler doğurur. Kurtarma zamanı hedefiniz (RTO) yeterince esnektir ve özgün kodunuz veya derlenmiş Cloud Services paketiniz varsa, başka bir bölgede uygulamanızın yeni bir örneğini oluşturabilir ve DNS kayıtlarınızı yeni dağıtıma işaret etmek üzere güncelleştirebilirsiniz.

Bulut hizmeti uygulaması oluşturma ve dağıtma hakkında daha fazla ayrıntı için bkz. [bulut hizmeti oluşturma ve dağıtma](cloud-services-how-to-create-deploy-portal.md).

Uygulama veri kaynaklarınıza bağlı olarak, uygulama veri kaynağınız için kurtarma yordamlarını denetlemeniz gerekebilir.

* Azure depolama veri kaynakları için bkz. [Azure depolama yedekliliği](../storage/common/storage-redundancy.md) , uygulamanız için seçilen artıklık modeline göre kullanılabilir seçenekleri denetlemek için kullanılır.
* SQL veritabanı kaynakları için bkz. genel bakış: uygulamanıza yönelik seçili çoğaltma modeline göre kullanılabilir seçenekleri denetlemek için [SQL veritabanı Ile bulut iş sürekliliği ve veritabanı olağanüstü durum kurtarma](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md) .


## <a name="option-3-wait-for-recovery"></a>Seçenek 3: kurtarma için bekle
Bu durumda, sizin bölüminizdeki hiçbir işlem yapmanız gerekmez, ancak bölge geri yüklenene kadar hizmetiniz kullanılamaz. Geçerli hizmet durumunu [Azure hizmet durumu panosu](https://azure.microsoft.com/status/)'nda görebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Olağanüstü durum kurtarma ve yüksek kullanılabilirlik stratejisi uygulama hakkında daha fazla bilgi edinmek için bkz. [Azure uygulamaları Için olağanüstü durum kurtarma ve yüksek kullanılabilirlik](/azure/architecture/framework/resiliency/backup-and-recovery).

Bulut platformunun yeteneklerini ayrıntılı bir şekilde anlamak için bkz. [Azure dayanıklılığı teknik kılavuzu](/azure/architecture/checklist/resiliency-per-service).