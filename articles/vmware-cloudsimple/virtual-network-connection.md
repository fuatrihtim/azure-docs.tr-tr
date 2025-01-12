---
title: Azure sanal ağını ExpressRoute ile Azure VMware çözümü kullanarak CloudSimple 'a bağlama-CloudSimple
description: Azure sanal ağı ile CloudSimple ortamınız arasındaki bir bağlantı için eşleme bilgilerinin nasıl alınacağını açıklar
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3fedfbe55fd8ea3d2b4cc910df631e40bc74e210
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899074"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>ExpressRoute kullanarak Azure sanal ağını CloudSimple 'a bağlama

Özel bulut ağınızı Azure sanal ağınıza ve Azure kaynaklarınıza genişletebilirsiniz. ExpressRoute bağlantısı, özel bulutunuzda Azure aboneliğinizde çalışan kaynaklara erişmenize olanak tanır.

## <a name="request-authorization-key"></a>İstek yetkilendirme anahtarı

Özel bulutunuz ve Azure sanal ağı arasındaki ExpressRoute bağlantısı için bir yetkilendirme anahtarı gereklidir. Anahtar almak için, <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">destek</a>ile bir bilet dosyası yapın.  İstekte aşağıdaki bilgileri kullanın:

* Sorun türü: **Teknik**
* Abonelik: **CloudSimple hizmetinin dağıtıldığı aboneliği seçin**
* Hizmet: **CloudSimple tarafından VMware çözümü**
* Sorun türü: **hizmet isteği**
* Sorun alt türü: **Azure VNet bağlantısı Için yetkilendirme anahtarı**
* Subject: **Azure VNet bağlantısı için yetkilendirme anahtarı isteği**

## <a name="get-peering-information-from-cloudsimple-portal"></a>CloudSimple portalından eşleme bilgilerini al

Bağlantıyı kurmak için, Azure sanal ağı ile CloudSimple ortamınız arasında bir bağlantı kurmanız gerekir.  Yordamın bir parçası olarak, eşdüzey devre URI 'sini ve yetkilendirme anahtarını sağlamalısınız. [Cloudsimple PORTALıNDAN](access-cloudsimple-portal.md)URI ve yetkilendirme anahtarını edinin.  Yan menüden **ağ** ' ı seçin ve ardından **Azure ağ bağlantısı**' nı seçin. Veya yan menüden **Hesap** ' ı seçin ve ardından **Azure ağ bağlantısı**' nı seçin.

*Kopya* simgesini kullanarak her bir bölgenin yetkilendirme anahtarını ve eş devre URI 'sini kopyalayın. Bağlamak istediğiniz her CloudSimple bölgesi için:

1. URI 'yi kopyalamak için **Kopyala** ' ya tıklayın. Azure portal eklemek için kullanılabilecek bir dosyaya yapıştırın.  
2. Yetkilendirme anahtarını kopyalamak için **Kopyala** ' ya tıklayın ve dosyayı da dosyaya yapıştırın.

**Kullanılabilir** durumda olan yetkilendirme anahtarını ve eş devre dışı URI 'yi kopyalayın.  **Kullanılan** durum, anahtarın zaten bir sanal ağ bağlantısı oluşturmak için kullanıldığını gösterir.

![Sanal ağ bağlantısı sayfası](media/virtual-network-connection.png)

Azure sanal ağını CloudSimple bağlantısına ayarlama hakkında daha fazla bilgi için bkz. [ExpressRoute kullanarak Cloudsimple özel bulut ortamınızı Azure sanal ağına bağlama](azure-expressroute-connection.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Özel buluta Azure sanal ağ bağlantısı](azure-expressroute-connection.md)
* [Azure ExpressRoute kullanarak şirket içi ağa bağlanma](on-premises-connection.md)
