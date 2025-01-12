---
title: AKS iş sürekliliği ve olağanüstü durum kurtarma için en iyi uygulamalar
description: Azure Kubernetes Service (AKS) ' de yüksek kullanılabilirlik ve olağanüstü durum kurtarma için hazırlık olanağı sunan bir küme işlecinin en iyi uygulamalarını öğrenin.
services: container-service
author: lastcoolnameleft
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: 3ff8406a3634fa946ab8ce7aca694bbc57d556a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97976410"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) ' de iş sürekliliği ve olağanüstü durum kurtarma için en iyi uygulamalar

Azure Kubernetes Service (AKS) içindeki kümeleri yönetirken, uygulama çalışma süresi önemli hale gelir. Varsayılan olarak, AKS, [sanal makine ölçek kümesinde (VMSS)](../virtual-machine-scale-sets/overview.md)birden çok düğüm kullanarak yüksek kullanılabilirlik sağlar. Ancak bu birden çok düğüm, sisteminizi bir bölge hatasından korumaz. Çalışma alanınızı en üst düzeye çıkarmak için iş sürekliliği ve olağanüstü durum kurtarma için hazırlanma konusuna planlayın.

Bu makalede, AKS 'de iş sürekliliği ve olağanüstü durum kurtarmayı planlamaya odaklanılır. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Birden çok bölgede AKS kümelerini planlayın.
> * Azure Traffic Manager kullanarak trafiği birden çok küme arasında yönlendirin.
> * Kapsayıcı görüntü kayıt defterleri için coğrafi çoğaltma kullanın.
> * Birden çok küme genelinde uygulama durumunu planlayın.
> * Depolama alanını birden çok bölgede çoğaltın.

## <a name="plan-for-multiregion-deployment"></a>Çok bölgeli dağıtımı için plan

**En iyi yöntem**: birden çok aks kümesi dağıttığınızda, aks 'in kullanılabildiği bölgeleri seçin ve eşleştirilmiş bölgeleri kullanın.

AKS kümesi tek bir bölgeye dağıtılır. Sisteminizi bölge arızasına karşı korumak için, uygulamanızı farklı bölgelerde birden çok AKS kümesine dağıtın. AKS kümenizi nereye dağıtacağınızı planlarken şunları göz önünde bulundurun:

* [**Aks bölgesi kullanılabilirliği**](./quotas-skus-regions.md#region-availability): kullanıcılarınıza yakın bölgeleri seçin. AKS sürekli yeni bölgelere genişletilir.
* [**Azure eşlenmiş bölgeler**](../best-practices-availability-paired-regions.md): coğrafi bölgenizde, birbirleriyle eşleştirilmiş iki bölge seçin. Eşleştirilmiş bölgeler platform güncelleştirmelerini koordine edin ve gerektiğinde kurtarma çalışmalarını önceliklendirin.
* **Hizmet kullanılabilirliği**: eşleştirilmiş bölgelerin etkin/sık erişimli, etkin/sıcak veya sık/soğuk olması gerekip gerekmediğine karar verin. Her iki bölgeyi de aynı anda çalıştırmak istiyor musunuz, tek bir bölge, trafiğe hizmet vermeye *başlamaya hazırlanıyor* mi? Ya da bir bölgenin, trafiği sunmaya hazırlamak için zaman almasını ister misiniz?

AKS bölgesi kullanılabilirliği ve eşleştirilmiş bölgeler, bir eklem noktadır. AKS kümelerinizi, bölge olağanüstü durum kurtarmayı birlikte yönetmek için tasarlanan eşleştirilmiş bölgelere dağıtın. Örneğin, AKS Doğu ABD ve Batı ABD kullanılabilir. Bu bölgeler eşlenilmiştir. AKS BC/DR stratejisi oluştururken bu iki bölgeyi seçin.

Uygulamanızı dağıtırken, bu çoklu AKS kümelerine dağıtmak için CI/CD işlem hattınızla başka bir adım ekleyin. Dağıtım işlem hatlarınızı güncelleştirmemeniz halinde uygulamalar, Bölgelerinizdeki ve AKS kümelerinden yalnızca birine dağıtılabilir. Bir ikincil bölgeye yöneltilen müşteri trafiği, en son kod güncelleştirmelerini almaz.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Trafiği yönlendirmek için Azure Traffic Manager kullanma

**En iyi yöntem**: Azure Traffic Manager, müşterileri en yakın aks kümesine ve uygulama örneğine yönlendirebilir. En iyi performans ve artıklık için, tüm uygulama trafiğini AKS kümenize geçmeden önce Traffic Manager aracılığıyla doğrudan yönlendirin.

Farklı bölgelerde birden fazla AKS kümeniz varsa, trafiğin her kümede çalışan uygulamalara nasıl akacağını denetlemek için Traffic Manager kullanın. [Azure Traffic Manager](../traffic-manager/index.yml) , bölgeler arasında ağ trafiği DAĞıTABILECEK DNS tabanlı bir trafik yük dengeleyicidir. Kullanıcıları küme yanıt süresine göre veya Coğrafya temelinde yönlendirmek için Traffic Manager kullanın.

![Traffic Manager ile AKS](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Tek bir AKS kümesi olan müşteriler tipik olarak belirli bir uygulamanın hizmet IP 'sini veya DNS adına bağlanır. Birden çok Luster dağıtımında, müşteriler her bir AKS kümesindeki hizmetlere işaret eden bir Traffic Manager DNS adına bağlanmalıdır. Bu hizmetleri Traffic Manager uç noktaları kullanarak tanımlayın. Her uç nokta *hizmet yük DENGELEYICI IP*'dir. Ağ trafiğini bir bölgedeki Traffic Manager uç noktasından farklı bir bölgedeki uç noktaya yönlendirmek için bu yapılandırmayı kullanın.

![Traffic Manager aracılığıyla coğrafi yönlendirme](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Traffic Manager DNS aramalarını gerçekleştirir ve kullanıcının en uygun uç noktasını döndürür. İç içe profiller birincil bir konuma öncelik verebilir. Örneğin, kullanıcılar genellikle en yakın coğrafi bölgelere bağlanmalıdır. Bu bölgede bir sorun varsa Traffic Manager bunun yerine kullanıcıları ikincil bir bölgeye yönlendirir. Bu yaklaşım, en yakın coğrafi bölgesi kullanılamaz olsa bile müşterilerin bir uygulama örneğine bağlanabilmelerini sağlar.

Uç noktaları ve yönlendirmeyi ayarlama hakkında daha fazla bilgi için, bkz. [Traffic Manager kullanarak coğrafi trafik yönlendirme yöntemini yapılandırma](../traffic-manager/traffic-manager-configure-geographic-routing-method.md).

### <a name="application-routing-with-azure-front-door-service"></a>Azure ön kapı hizmeti ile uygulama yönlendirme

Bölünmüş TCP tabanlı her noktaya yayın protokolünü kullanarak [Azure ön kapı hizmeti](../frontdoor/front-door-overview.md) , son kullanıcılarınızın en yakın ön kapı pop 'Una (varlık noktası) daha önce bağlanmasını sağlar. Azure ön kapı hizmeti 'nin ek özellikleri TLS sonlandırma, özel etki alanı, Web uygulaması güvenlik duvarı, URL yeniden yazma ve oturum benzeşimi içerir. En uygun çözümü anlamak için uygulama trafiğinizin ihtiyaçlarını gözden geçirin.

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>Genel sanal ağ eşlemesi ile Interconnect bölgeleri

Kümelerin birbirleriyle iletişim kurmasına ihtiyacı varsa, her iki sanal ağı birbirine bağlamak [sanal ağ eşlemesi](../virtual-network/virtual-network-peering-overview.md)aracılığıyla elde edilebilir. Bu teknoloji, farklı coğrafi bölgelerde bile Microsoft 'un omurga ağı genelinde yüksek bant genişliğine sahip birbirlerine sanal ağları birbirine bağlar.

AKS kümelerinin çalıştığı sanal ağların eşi için bir önkoşul, AKS kümenizdeki standart Load Balancer kullanmaktır, böylece Kubernetes Hizmetleri sanal ağ eşlemesi genelinde erişilebilir olur.

## <a name="enable-geo-replication-for-container-images"></a>Kapsayıcı görüntüleri için Coğrafi çoğaltmayı etkinleştirme

**En iyi yöntem**: kapsayıcı görüntülerinizi Azure Container Registry ve coğrafi olarak kayıt defteri her BIR aks bölgesine depolayın.

Uygulamalarınızı AKS 'de dağıtmak ve çalıştırmak için kapsayıcı görüntülerini depolamak ve çekmek için bir yol gerekir. Container Registry, AKS ile tümleşir, bu nedenle kapsayıcı görüntülerinizi veya Held grafiklerini güvenli bir şekilde depolayabilirler. Container Registry, görüntülerinizi dünyanın dört bir yanındaki Azure bölgelerine otomatik olarak çoğaltmak için çok kaynaklı Coğrafi çoğaltmayı destekler. 

Performansı ve kullanılabilirliği artırmak için, AKS kümeniz olan her bölgede bir kayıt defteri oluşturmak üzere coğrafi çoğaltma Container Registry kullanın. Her bir AKS kümesi daha sonra aynı bölgedeki yerel kapsayıcı kayıt defterinden kapsayıcı görüntülerini çeker:

![Kapsayıcı görüntüleri için coğrafi çoğaltma Container Registry](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Aynı bölgedeki görüntüleri çekmek için coğrafi çoğaltma Container Registry kullandığınızda sonuçlar şunlardır:

* **Daha hızlı**: aynı Azure bölgesindeki yüksek hızlı, düşük gecikmeli ağ bağlantılarından görüntü çekebilirsiniz.
* **Daha güvenilir**: bir bölge kullanılamıyorsa aks kümeniz görüntüleri kullanılabilir bir kapsayıcı kayıt defterinden çeker.
* **Kimaper**: veri merkezleri arasında ağ çıkış ücreti alınmaz.

Coğrafi çoğaltma, *Premium* SKU kapsayıcısı kayıt defterlerinin bir özelliğidir. Coğrafi çoğaltmanın nasıl yapılandırılacağı hakkında bilgi için bkz. [Container Registry coğrafi çoğaltma](../container-registry/container-registry-geo-replication.md).

## <a name="remove-service-state-from-inside-containers"></a>Hizmet durumunu kapsayıcıların içinden kaldır

**En iyi yöntem**: mümkün olduğunda, hizmet durumunu kapsayıcı içinde depolamayın. Bunun yerine, çok bölgeli çoğaltmasını destekleyen bir hizmet olarak Azure platformu (PaaS) kullanın.

*Hizmet durumu* , bir hizmetin çalışması için gereken bellek içi veya disk üzerindeki verilere başvurur. Durum, hizmetin okuduğu ve yazdığı veri yapılarını ve üye değişkenlerini içerir. Hizmetin nasıl mimari olduğuna bağlı olarak, durum, diskte depolanan dosyaları veya diğer kaynakları da içerebilir. Örneğin, durum, bir veritabanının veri ve işlem günlüklerini depolamak için kullandığı dosyaları içerebilir.

Durum externalized veya durumu işleyen kodla eklenebilir. Genellikle, bir veritabanı veya ağ üzerinden çalışan farklı makinelerde çalışan veya aynı makinede işlem dışında çalışan başka bir veri deposu kullanarak externalize.

Kapsayıcılar ve mikro hizmetler, içinde çalışan işlemlerin durumu korumaları durumunda en esnektir. Uygulamalar neredeyse her zaman bir durum içerdiğinden Azure Cosmos DB, PostgreSQL için Azure veritabanı, MySQL için Azure veritabanı veya Azure SQL veritabanı gibi bir PaaS çözümü kullanın.

Taşınabilir uygulamalar oluşturmak için aşağıdaki yönergelere bakın:

* [12 öğeli uygulama yöntemi](https://12factor.net/)
* [Bir Web uygulamasını birden çok Azure bölgesinde çalıştırma](/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Depolama geçiş planı oluşturma

**En iyi yöntem**: Azure Storage kullanıyorsanız, depolama alanınızı birincil bölgeden yedekleme bölgesine nasıl geçirebileceğiniz hazırlayın ve test edin.

Uygulamalarınız, verileri için Azure Storage kullanıyor olabilirler. Uygulamalarınız farklı bölgelerdeki birden çok AKS kümesine yayıldığından, depolamayı eşitlenmiş tutmanız gerekir. Depolama çoğaltması için iki yaygın yol aşağıda verilmiştir:

* Altyapı tabanlı zaman uyumsuz çoğaltma
* Uygulama tabanlı zaman uyumsuz çoğaltma

### <a name="infrastructure-based-asynchronous-replication"></a>Altyapı tabanlı zaman uyumsuz çoğaltma

Bir pod silindikten sonra bile uygulamalarınız kalıcı depolama gerekebilir. Kubernetes 'de, veri depolamayı kalıcı hale getirmek için kalıcı birimleri kullanabilirsiniz. Kalıcı birimler bir düğüm sanal makinesine bağlanır ve sonra da pods 'leri kullanıma sunulur. Kalıcı birimler, düğüm aynı kümedeki farklı bir düğüme taşınsa bile, Pod 'ler izler.

Kullandığınız çoğaltma stratejisi, depolama çözümünüze bağlıdır. [Gluster](https://docs.gluster.org/en/latest/Administrator-Guide/Geo-Replication/), [Ceph](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/), [Rook](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html)ve [portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) gibi yaygın depolama çözümleri olağanüstü durum kurtarma ve çoğaltma hakkında kendi kılavuzunu sağlar.

Tipik strateji, uygulamaların verilerini yazabilmesi için ortak bir depolama noktası sağlamaktır. Bu veriler daha sonra bölgeler arasında çoğaltılır ve sonra yerel olarak erişilir.

![Altyapı tabanlı zaman uyumsuz çoğaltma](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Azure yönetilen diskler kullanıyorsanız, çoğaltma ve olağanüstü durum kurtarmayı işlemek için kullanabileceğiniz birkaç seçenek vardır. Azure ve [kasten][kasten] [üzerinde Velero][velero] , Kubernetes 'e yerel çözümler yedekler ancak desteklenmez.

### <a name="application-based-asynchronous-replication"></a>Uygulama tabanlı zaman uyumsuz çoğaltma

Kubernetes Şu anda uygulama tabanlı zaman uyumsuz çoğaltma için yerel uygulama sunmamaktadır. Kapsayıcılar ve Kubernetes gevşek olarak bağlanmış olduğundan, geleneksel uygulama veya dil yaklaşımının çalışması gerekir. Genellikle, uygulamalar depolama isteklerini çoğaltarak her kümenin temel alınan veri depolamaya yazılır.

![Uygulama tabanlı zaman uyumsuz çoğaltma](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, AKS kümelerine yönelik iş sürekliliği ve olağanüstü durum kurtarma konuları ele alınmaktadır. AKS 'deki küme işlemleri hakkında daha fazla bilgi için en iyi uygulamalar hakkında aşağıdaki makalelere bakın:

* [Çok kiracılı ve küme yalıtımı][aks-best-practices-cluster-isolation]
* [Temel Kubernetes Zamanlayıcı özellikleri][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md

[velero]: https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md
[kasten]: https://www.kasten.io/