---
title: Kavramlar-Azure Kubernetes hizmetlerinde güvenlik (AKS)
description: Ana ve düğüm iletişimi, ağ ilkeleri ve Kubernetes gizli dizileri dahil olmak üzere Azure Kubernetes hizmeti 'nde (AKS) güvenlik hakkında bilgi edinin.
services: container-service
author: mlearned
ms.topic: conceptual
ms.date: 07/01/2020
ms.author: mlearned
ms.openlocfilehash: 6c69e46ea3510476089cd932b1cd1bdf14254021
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122383"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service’teki (AKS) uygulamalar ve kümeler için güvenlik kavramları

Azure Kubernetes Service (AKS) içinde uygulama iş yüklerini çalıştırırken müşteri verilerinizi korumak için, kümenizin güvenliği önemli bir konudur. Kubernetes, *ağ ilkeleri* ve *gizli* dizileri gibi güvenlik bileşenleri içerir. Daha sonra Azure, ağ güvenlik grupları ve düzenlenmiş küme yükseltmeleri gibi bileşenlere eklenir. Bu güvenlik bileşenleri, AKS kümenizi en son işletim sistemi güvenlik güncelleştirmelerini ve Kubernetes yayınlarını çalıştıran, güvenli Pod trafiği ve hassas kimlik bilgilerine erişim sağlamak için birleştirilir.

Bu makalede, AKS 'deki uygulamalarınızın güvenliğini sağlayan temel kavramlar tanıtılmaktadır:

- [Azure Kubernetes Service’teki (AKS) uygulamalar ve kümeler için güvenlik kavramları](#security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks)
  - [Ana güvenlik](#master-security)
  - [Düğüm güvenliği](#node-security)
    - [İşlem yalıtımı](#compute-isolation)
  - [Küme yükseltmeleri](#cluster-upgrades)
    - [Cordon ve boşalt](#cordon-and-drain)
  - [Ağ güvenliği](#network-security)
    - [Azure ağ güvenlik grupları](#azure-network-security-groups)
  - [Kubernetes gizli dizileri](#kubernetes-secrets)
  - [Sonraki adımlar](#next-steps)

## <a name="master-security"></a>Ana güvenlik

AKS 'de Kubernetes ana bileşenleri, Microsoft tarafından sunulan yönetilen hizmetin bir parçasıdır. Her bir AKS kümesi, API sunucusu, Zamanlayıcı vb. sağlamak için kendi tek kiracılı, adanmış Kubernetes ana öğesine sahiptir. Bu ana, Microsoft tarafından yönetilir ve korunur.

Varsayılan olarak, Kubernetes API sunucusu genel bir IP adresi ve tam etki alanı adı (FQDN) kullanır. [Yetkılı IP aralıklarını][authorized-ip-ranges]kullanarak API sunucusu uç noktasına erişimi sınırlayabilirsiniz. Ayrıca, API sunucusu erişimini sanal ağınıza sınırlamak için tam bir [özel küme][private-clusters] da oluşturabilirsiniz.

Kubernetes rol tabanlı erişim denetimi (Kubernetes RBAC) ve Azure RBAC kullanarak API sunucusuna erişimi denetleyebilirsiniz. Daha fazla bilgi için bkz. [AKS Ile Azure AD tümleştirmesi][aks-aad].

## <a name="node-security"></a>Düğüm güvenliği

AKS düğümleri, yönettiğiniz ve tuttuğunuz Azure sanal makinelerdir. Linux düğümleri `containerd` veya Moby kapsayıcı çalışma zamanını kullanarak iyileştirilmiş bir Ubuntu dağıtımı çalıştırır. Windows Server düğümleri, iyileştirilmiş bir Windows Server 2019 sürümü çalıştırır ve ayrıca `containerd` ya da Moby kapsayıcı çalışma zamanını kullanır. Bir AKS kümesi oluşturulduğunda veya ölçeklendirildiğinde, düğümler en son işletim sistemi güvenlik güncelleştirmeleri ve yapılandırmalarına göre otomatik olarak dağıtılır.

> [!NOTE]
> Kubernetes sürüm 1,19 düğüm havuzlarını ve daha büyük kullanımını `containerd` kapsayıcı çalışma zamanı olarak kullanan AKS kümeleri. For node havuzları için v 1.19 öncesinde Kubernetes kullanan AKS kümeleri, kapsayıcı çalışma zamanı olarak [Moby](https://mobyproject.org/) (yukarı akış Docker) kullanır.

Azure platformu, gece temelinde Linux düğümlerine işletim sistemi güvenlik düzeltme eklerini otomatik olarak uygular. Bir Linux işletim sistemi güvenlik güncelleştirmesi konağın yeniden başlatılmasını gerektiriyorsa, bu yeniden başlatma otomatik olarak gerçekleştirilmez. Linux düğümlerini el ile yeniden başlatabilirsiniz veya bir yaygın yaklaşım, Kubernetes için açık kaynaklı bir yeniden başlatma cini olan [Kured][kured]'yi kullanmaktır. Kured bir [DaemonSet][aks-daemonsets] olarak çalışır ve her düğümü, bir yeniden başlatmanın gerekli olduğunu belirten bir dosyanın varlığına izler. Yeniden başlatmalar, küme yükseltmesi olarak aynı [Cordon ve boşalt işlemini](#cordon-and-drain) kullanarak küme genelinde yönetilir.

Windows Server düğümleri için Windows Update otomatik olarak çalıştırılmaz ve en son güncelleştirmeleri uygulamaz. Windows Update yayın döngüsünün ve kendi doğrulama işleminizin etrafında düzenli bir zamanlamaya göre, AKS kümenizdeki Windows Server düğüm havuzunda bir yükseltme gerçekleştirmeniz gerekir. Bu yükseltme işlemi, en son Windows Server görüntüsünü ve düzeltme eklerini çalıştıran düğümleri oluşturur, daha sonra eski düğümleri kaldırır. Bu işlemle ilgili daha fazla bilgi için bkz. [AKS 'de düğüm havuzunu yükseltme][nodepool-upgrade].

Düğümler, genel IP adresleri atanmadan özel bir sanal ağ alt ağına dağıtılır. Sorun giderme ve yönetim amaçlarıyla, SSH varsayılan olarak etkindir. Bu SSH erişimi yalnızca iç IP adresi kullanılarak kullanılabilir.

Depolama sağlamak için düğümler Azure yönetilen diskleri kullanır. Çoğu VM düğüm boyutu için bunlar, yüksek performanslı SSD 'Ler tarafından desteklenen Premium disklerdir. Yönetilen disklerde depolanan veriler, Azure platformunda Rest 'de otomatik olarak şifrelenir. Bu diskler, artıklığı artırmak için Azure veri merkezi 'nde de güvenli bir şekilde çoğaltılır.

Kubernetes ortamları, AKS veya başka bir yerde, şu anda çok kiracılı olmayan kullanım için tamamen güvenli değildir. Temel bileşenler için *Pod güvenlik ilkeleri* veya daha hassas rol tabanlı erişim denetimi (KUBERNETES RBAC) gibi ek güvenlik özellikleri, daha çok daha zor hale getirir. Ancak, çok kiracılı çoklu kiracı iş yüklerini çalıştırırken doğru güvenlik için bir hiper yönetici, güvenmeniz gereken tek güvenlik düzeyidir. Kubernetes güvenlik etki alanı, tek bir düğüm değil, tüm küme haline gelir. Bu tür çok kiracılı iş yükleri için, fiziksel olarak yalıtılmış kümeler kullanmanız gerekir. İş yüklerini yalıtma yolları hakkında daha fazla bilgi için bkz. [AKS 'de küme yalıtımı Için en iyi uygulamalar][cluster-isolation].

### <a name="compute-isolation"></a>İşlem yalıtımı

 Belirli iş yükleri, uyumluluk ve mevzuat gereksinimleri nedeniyle diğer müşteri iş yüklerinden yüksek ölçüde yalıtım gerektirebilir. Azure, bu iş yükleri için bir AKS kümesinde aracı düğümleri olarak kullanılabilecek [yalıtılmış sanal makineler](../virtual-machines/isolation.md)sağlar. Bu yalıtılmış sanal makineler belirli bir donanım türüne ayrılmıştır ve tek bir müşteriye atanır. 

 Bu yalıtılmış sanal makineleri bir AKS kümesiyle birlikte kullanmak için, bir AKS kümesi oluştururken **veya düğüm havuzu** eklerken [burada](../virtual-machines/isolation.md) listelenen yalıtılmış sanal makine boyutlarından birini seçin.


## <a name="cluster-upgrades"></a>Küme yükseltmeleri

Güvenlik ve uyumluluk için veya en son özellikleri kullanabilmeniz için Azure, bir AKS kümesini ve bileşenlerini yükseltmek için araçlar sağlar. Bu yükseltme düzenlemesi hem Kubernetes Master hem de Agent bileşenlerini içerir. AKS kümeniz için [kullanılabilir Kubernetes sürümlerinin bir listesini](supported-kubernetes-versions.md) görüntüleyebilirsiniz. Yükseltme işlemini başlatmak için, kullanılabilir sürümlerden birini belirtirsiniz. Daha sonra Azure, her bir AKS düğümünü güvenle ve yükseltme işlemini gerçekleştirir.

### <a name="cordon-and-drain"></a>Cordon ve boşalt

Yükseltme işlemi sırasında, AKS düğümleri, yeni yığınların zamanlanmaması için kümeden tek tek bir şekilde ele alınır. Düğümler daha sonra aşağıdaki şekilde dağıtılır ve yükseltilir:

- Düğüm havuzuna yeni bir düğüm dağıtılır. Bu düğüm, en son işletim sistemi görüntüsünü ve düzeltme eklerini çalıştırır.
- Mevcut düğümlerden biri yükseltme için tanımlandı. Bu düğümdeki düğüm, normal şekilde sonlandırılır ve düğüm havuzundaki diğer düğümlerde zamanlanır.
- Bu var olan düğüm AKS kümesinden silinir.
- Kümedeki sonraki düğüm, tüm düğümler yükseltme işleminin bir parçası olarak başarıyla değiştirilene kadar aynı işlemle aynı işlem kullanılarak işlenir.

Daha fazla bilgi için bkz. [AKS kümesini yükseltme][aks-upgrade-cluster].

## <a name="network-security"></a>Ağ güvenliği

Şirket içi ağlarla bağlantı ve güvenlik için, AKS kümenizi mevcut Azure sanal ağ alt ağlarına dağıtabilirsiniz. Bu sanal ağlarda, şirket içi ağınıza bir Azure siteden siteye VPN veya Express Route bağlantısı olabilir. Kubernetes giriş denetleyicileri özel, iç IP adresleriyle tanımlanabilir, bu nedenle hizmetlere yalnızca bu iç ağ bağlantısından erişilebilir.

### <a name="azure-network-security-groups"></a>Azure ağ güvenlik grupları

Azure, sanal ağlardaki trafik akışını filtrelemek için ağ güvenlik grubu kurallarını kullanır. Bu kurallar, kaynaklara erişim izni verilen veya reddedilen kaynak ve hedef IP aralıklarını, bağlantı noktalarını ve protokolleri tanımlar. Kubernetes API sunucusuna TLS trafiğine izin vermek için varsayılan kurallar oluşturulur. Yük dengeleyiciler, bağlantı noktası eşleştirmeleri veya giriş rotaları ile hizmetler oluştururken, AKS, trafiği uygun şekilde akışa almak için ağ güvenlik grubunu otomatik olarak değiştirir.

AKS kümeniz için kendi alt ağınızı sağladığınız ve trafik akışını değiştirmek istediğiniz durumlarda, AKS tarafından yönetilen alt ağ düzeyinde ağ güvenlik grubunu değiştirmeyin. Yük dengeleyici erişimi, denetim düzleğiyle iletişim ve [Çıkış][aks-limit-egress-traffic]gibi, küme yönetimi için gereken trafikle müdahale olmadıkları sürece trafik akışını değiştirmek için ek alt ağ düzeyi ağ güvenlik grupları oluşturabilirsiniz.

### <a name="kubernetes-network-policy"></a>Kubernetes ağ ilkesi

Kümenizdeki düğüm arasındaki ağ trafiğini sınırlandırmak için AKS 'ler, [Kubernetes ağ ilkeleri][network-policy]için destek sunar. Ağ ilkeleriyle, ad alanları ve etiket seçicileri temelinde küme içindeki belirli ağ yollarına izin vermeyi veya reddetme seçeneğini belirleyebilirsiniz.

## <a name="kubernetes-secrets"></a>Kubernetes Gizli Dizileri

Bir Kubernetes *gizli anahtarı* , erişim kimlik bilgileri veya anahtarlar gibi önemli verileri pods 'ye eklemek için kullanılır. İlk olarak Kubernetes API 'sini kullanarak bir gizli dizi oluşturursunuz. Pod veya dağıtımınızı tanımladığınızda, belirli bir gizli dizi istenebilir. Gizli diziler yalnızca, bir zamanlanmış Pod içeren düğümlere sağlanır ve gizli dizi diske yazılmadı ve *tmpfs*'de depolanır. Gizli anahtar gerektiren bir düğümdeki son Pod silindiğinde, parola, düğümün tmpfs 'den silinir. Gizli diziler belirli bir ad alanı içinde depolanır ve yalnızca aynı ad alanında yer alan Pod tarafından erişilebilir.

Gizli dizi kullanımı, Pod veya hizmet YAML bildiriminde tanımlanan hassas bilgileri azaltır. Bunun yerine, Kubernetes API sunucusunda depolanan gizli anahtarı YAML bildiriminizde bir parçası olarak istemeniz gerekir. Bu yaklaşım yalnızca gizli dizi için özel Pod erişimi sağlar. Lütfen unutmayın: ham gizli bilgi bildirimi dosyaları gizli verileri Base64 biçiminde içerir (daha fazla ayrıntı için [resmi belgelere][secret-risks] bakın). Bu nedenle, bu dosya hassas bilgiler olarak kabul edilmelidir ve kaynak denetimine hiçbir şekilde uygulanmamalıdır.

Kubernetes gizli dizileri, dağıtılmış anahtar-değer deposu olarak etcd 'de depolanır. Etcd deposu AKS tarafından tam olarak yönetilir ve [veriler Azure platformunda geri kalanında şifrelenir][encryption-atrest]. 

## <a name="next-steps"></a>Sonraki adımlar

AKS kümelerinizi korumaya başlamak için bkz. [aks kümesini yükseltme][aks-upgrade-cluster].

İlişkili en iyi uygulamalar için bkz. [AKS 'de küme güvenliği ve yükseltmeleri Için en iyi uygulamalar][operator-best-practices-cluster-security] ve [aks 'de Pod güvenliği için en iyi uygulamalar][developer-best-practices-pod-security].

Temel Kubernetes ve AKS kavramları hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Kubernetes/AKS kümeleri ve iş yükleri][aks-concepts-clusters-workloads]
- [Kubernetes/AKS kimliği][aks-concepts-identity]
- [Kubernetes/AKS sanal ağları][aks-concepts-network]
- [Kubernetes/AKS depolaması][aks-concepts-storage]
- [Kubernetes/AKS ölçeği][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks
[encryption-atrest]: ../security/fundamentals/encryption-atrest.md

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: ./managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[aks-limit-egress-traffic]: limit-egress-traffic.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[authorized-ip-ranges]: api-server-authorized-ip-ranges.md
[private-clusters]: private-clusters.md
[network-policy]: use-network-policies.md
