---
title: ExpressRoute kullanarak CloudSimple-on-şirket içi bağlantı tarafından Azure VMware çözümü
description: CloudSimple bölgesi ağından ExpressRoute kullanarak şirket içi bir bağlantının nasıl isteneceğini açıklar.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 27d1d9e54838d9f45a28d634fa1c24fb8785aee1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899244"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>ExpressRoute kullanarak şirket içinden CloudSimple 'a bağlanma

Zaten bir dış konumdan (örneğin, şirket içi) Azure 'da Azure ExpressRoute bağlantınız varsa, bunu CloudSimple ortamınıza bağlayabilirsiniz. Bunu, iki ExpressRoute devresine bağlanmasına izin veren bir Azure özelliği aracılığıyla yapabilirsiniz. Bu yöntem, iki ortam arasında güvenli, özel, yüksek bant genişliği, düşük gecikme süreli bir bağlantı oluşturur.

[![Şirket içi ExpressRoute bağlantısı-Global Reach](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>Başlamadan önce

Şirket içinden Global Reach bağlantı kurmak için bir **/29** ağ adresi bloğu gerekir.  /29 adres alanı, ExpressRoute devreleri arasında transit ağı için kullanılır.  Transit ağı, Azure sanal ağlarınızdan, şirket içi ağlardan veya CloudSimple özel bulut ağlarınızla çakışmamalıdır.

## <a name="prerequisites"></a>Önkoşullar

* Devre ve CloudSimple özel bulut ağları arasında bağlantı kurmadan önce bir Azure ExpressRoute devresi gerekir.
* Bir ExpressRoute devresinde yetkilendirme anahtarları oluşturma ayrıcalıklarına sahip bir kullanıcı gereklidir.

## <a name="scenarios"></a>Senaryolar

Şirket içi ağınızı özel bulut ağınıza bağlamak, aşağıdaki senaryolar da dahil olmak üzere çeşitli yollarla özel bulutu kullanmanıza olanak sağlar:

* Özel bulut ağınıza, siteden siteye VPN bağlantısı oluşturmadan erişin.
* Şirket içi Active Directory özel bulutunuzda kimlik kaynağı olarak kullanın.
* Şirket içinde çalışan sanal makineleri özel bulutunuzda geçirin.
* Özel bulutunuzu olağanüstü durum kurtarma çözümünün bir parçası olarak kullanın.
* Özel bulut iş yükü sanal makinelerinizdeki şirket içi kaynakları kullanın.

## <a name="connecting-expressroute-circuits"></a>ExpressRoute devrelerini bağlama

ExpressRoute bağlantısı kurmak için ExpressRoute bağlantı hattınızı bir yetkilendirme oluşturmanız ve CloudSimple yetkilendirme bilgilerini sağlamanız gerekir.


### <a name="create-expressroute-authorization"></a>ExpressRoute yetkilendirmesi oluşturma

1. Azure portalında oturum açın.

2. Üst arama çubuğundan **ExpressRoute** bağlantı hattını arayın ve **Hizmetler** altında **ExpressRoute devreleri** ' na tıklayın.
    [![ExpressRoute devreleri](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. CloudSimple ağınıza bağlanmayı düşündüğünüz ExpressRoute bağlantı hattını seçin.

4. ExpressRoute sayfasında, **yetkilendirmeler**' e tıklayın, yetkilendirme için bir ad girin ve **Kaydet**' e tıklayın.
    [![ExpressRoute devre yetkilendirmesi](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Kopyala simgesine tıklayarak kaynak KIMLIĞI ve yetkilendirme anahtarı ' nı kopyalayın. KIMLIĞI ve anahtarı bir metin dosyasına yapıştırın.
    [![ExpressRoute bağlantı hattı yetkilendirme kopyası](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **Kaynak kimliği** , kullanıcı arabiriminden kopyalanmalı ve ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` desteklemek için sağladığınız biçimde olmalıdır.

6. Oluşturulacak bağlantı <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">desteğiyle</a> bir bilet dosyası oluşturma.
    * Sorun türü: **Teknik**
    * Abonelik: **CloudSimple hizmetinin dağıtıldığı abonelik**
    * Hizmet: **CloudSimple tarafından VMware çözümü**
    * Sorun türü: **hizmet isteği**
    * Sorun alt türü: **Şirket içi Için ExpressRoute bağlantısı oluşturma**
    * Ayrıntılar bölmesinde kopyaladığınız ve kaydettiğiniz kaynak KIMLIĞI ve yetkilendirme anahtarını sağlayın.
    * Transit ağı için bir/29 ağ adresi alanı belirtin.
    * ExpressRoute aracılığıyla varsayılan yol gönderiyor musunuz?
    * Özel bulut trafiği ExpressRoute üzerinden gönderilen varsayılan yolu kullanmalıdır mi?

    > [!IMPORTANT]
    > Varsayılan yol gönderme, şirket içi internet bağlantınızı kullanarak özel buluttan tüm internet trafiğini göndermenizi sağlar.  Özel bulutta yapılandırılmış varsayılan yolu devre dışı bırakmak ve şirket içi bağlantı varsayılan yolunu kullanmak için destek bileti içindeki ayrıntıları sağlayın.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure ağ bağlantıları hakkında daha fazla bilgi](cloudsimple-azure-network-connection.md)  
