---
title: Azure VMware çözümünü şirket içi ortamınıza bağlama
description: Azure VMware çözümünü şirket içi ortamınıza bağlamayı öğrenin.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 0b26dc4756cb37544c2b2f8c5a75df0ac1a9d629
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491801"
---
# <a name="connect-azure-vmware-solution-to-your-on-premises-environment"></a>Azure VMware çözümünü şirket içi ortamınıza bağlama

Bu makalede, Azure VMware çözümünü şirket içi ortamınıza bağlamayı [planlama sırasında toplanan bilgileri](production-ready-deployment-steps.md) kullanmaya devam edersiniz.

Başlamadan önce, Azure VMware çözümünü şirket içi ortamınıza bağlamak için iki önkoşul vardır:

- Şirket içi ortamınızdan Azure 'a bir ExpressRoute devresi.
- [Planlama aşamasının](production-ready-deployment-steps.md)bir parçası olarak tanımladığınız expressroute Global Reach eşleme için çakışmayan bir/29 çakışmayan CIDR ağ adresi bloğu.

>[!NOTE]
> VPN üzerinden bağlanabilirsiniz, ancak bu hızlı başlangıç belgesi için kapsam dışında olabilir.

## <a name="establish-an-expressroute-global-reach-connection"></a>ExpressRoute Global Reach bağlantısı kurma

ExpressRoute Global Reach kullanarak Azure VMware Çözüm özel bulutunuz ile şirket içi bağlantı kurmak için, Şirket [içi ortamları özel bir bulut](tutorial-expressroute-global-reach-private-cloud.md) öğreticisine izleyin.

Bu öğreticide, diyagramda gösterildiği gibi bir bağlantı oluşur.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" alt-text="ExpressRoute, şirket içi ağ bağlantı diyagramı Global Reach." lightbox="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" border="false":::

## <a name="verify-on-premises-network-connectivity"></a>Şirket içi ağ bağlantısını doğrulama

Artık ExpressRoute 'un NSX-T ağ kesimlerini ve Azure VMware Çözüm Yönetimi segmentlerini bağlandığı **Şirket içi kenar yönlendiricinizde** görmeniz gerekir.

>[!IMPORTANT]
>Herkes farklı bir ortama sahiptir ve bu yolların şirket içi ağa geri yayılmasına izin vermeniz gerekir.  

Bazı ortamlarda ExpressRoute devresini koruyan güvenlik duvarları vardır.  Güvenlik duvarı yoksa ve yol ayıklama gerçekleşmezseniz, Azure VMware Çözüm vCenter Server veya şirket içi ortamınızdan [NSX-T segmentindeki bir VM 'ye](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment) ping gönderin. Ayrıca, NSX-T segmentindeki VM 'den şirket içi ortamınızdaki kaynaklara ulaşabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

VMware HCX 'i dağıtmak ve yapılandırmak için sonraki bölüme geçin

> [!div class="nextstepaction"]
> [VMware HCX’i dağıtma ve yapılandırma](tutorial-deploy-vmware-hcx.md)