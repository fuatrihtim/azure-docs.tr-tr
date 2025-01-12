---
title: Rol için Uzak Masaüstü 'Nü etkinleştirmek üzere portalı kullanma
description: Azure bulut hizmeti uygulamanızı uzak masaüstü bağlantılarına izin verecek şekilde yapılandırma
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 8fa0d3c0e29c53e6fe9cb32ddf02168686be1efe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743262"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-classic"></a>Azure Cloud Services (klasik) bir rol için Uzak Masaüstü Bağlantısı etkinleştirme

> [!IMPORTANT]
> [Azure Cloud Services (genişletilmiş destek)](../cloud-services-extended-support/overview.md) , Azure Cloud Services ürünü için yeni bir Azure Resource Manager tabanlı dağıtım modelidir.Bu değişiklik ile Azure Service Manager tabanlı dağıtım modelinde çalışan Azure Cloud Services, Cloud Services (klasik) olarak yeniden adlandırıldı ve tüm Yeni dağıtımlar [Cloud Services kullanmalıdır (genişletilmiş destek)](../cloud-services-extended-support/overview.md).

> [!div class="op_single_selector"]
> * [Azure portalı](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Uzak Masaüstü, Azure 'da çalışan bir rolün masaüstüne erişmenizi sağlar. Çalışırken uygulamanızdaki sorunları gidermek ve tanılamak için Uzak Masaüstü bağlantısı kullanabilirsiniz.

Hizmet tanımınıza uzak masaüstü modüllerini ekleyerek ve Uzak Masaüstü uzantısı aracılığıyla uzak masaüstü 'nü etkinleştirmeyi tercih ederek, rolünüzde uzak masaüstü bağlantısını etkinleştirebilirsiniz. Tercih edilen yaklaşım, uygulamayı yeniden dağıtmak zorunda kalmadan uygulama dağıtıldıktan sonra bile uzak masaüstü 'nü etkinleştirebilmeniz için Uzak Masaüstü uzantısını kullanmaktır.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Azure portal uzak masaüstünü yapılandırma

Azure portal, Uzak Masaüstü uzantısı yaklaşımını kullanır, böylece uygulama dağıtıldıktan sonra bile uzak masaüstü 'Nü etkinleştirebilirsiniz. Bulut hizmetinizin **Uzak Masaüstü** ayarları uzak masaüstünü etkinleştirmenizi, sanal makinelere bağlanmak için kullanılan yerel yönetici hesabını değiştirmenize, kimlik doğrulamasında kullanılan sertifikayı değiştirmenize ve sona erme tarihini ayarlamanıza olanak tanır.

1. **Cloud Services**' ye tıklayın, bulut hizmetinin adını seçin ve ardından **Uzak Masaüstü**' nü seçin.

    ![görüntü, Cloud Services uzak masaüstü 'nü gösterir](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Tek bir rol veya tüm roller için Uzak Masaüstü 'Nü etkinleştirmek isteyip istemediğinizi seçin, sonra değiştiricinin değerini **etkin** olarak değiştirin.

3. Kullanıcı adı, parola, süre sonu ve sertifika için gerekli alanları girin.

    ![Cloud Services uzak masaüstü](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Tüm rol örnekleri, uzak masaüstünü ilk kez etkinleştirdiğinizde yeniden başlatılır ve **Tamam** (onay işareti) seçeneğini belirleyin. Yeniden başlatmayı engellemek için, parolayı şifrelemek için kullanılan sertifikanın role yüklenmiş olması gerekir. Yeniden başlatmayı engellemek için, [bulut hizmeti için bir sertifika yükleyin](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) ve ardından bu iletişim kutusuna geri dönün.

4. **Roller**' de, güncelleştirmek istediğiniz rolü seçin veya tüm roller için **Tümünü** seçin.

5. Yapılandırma güncelleştirmelerinizi bitirdiğinizde **Kaydet**' i seçin. Rol örneklerinizin bağlantı almaya hazırlanmadan önce bu işlem birkaç dakika sürer.

## <a name="remote-into-role-instances"></a>Rol örneklerine uzaktan

Roller üzerinde Uzak Masaüstü etkinleştirildikten sonra, Azure portal doğrudan bir bağlantı başlatabilirsiniz:

1. **Örnekler ayarlarını açmak** için **örnekler** ' e tıklayın.
2. Uzak Masaüstü 'Nün yapılandırıldığı bir rol örneği seçin.
3. Rol örneği için bir RDP dosyası indirmek için **Bağlan** ' a tıklayın.

    ![Cloud Services uzak masaüstü görüntüsü](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. **Aç** ' a ve ardından **Bağlan** ' a tıklayıp uzak masaüstü bağlantısını başlatın.

>[!NOTE]
> Bulut hizmetiniz bir NSG 'nin arkasında çalışıyorsa, **3389** ve **20000** bağlantı noktalarında trafiğe izin veren kurallar oluşturmanız gerekebilir.  Uzak Masaüstü **3389** numaralı bağlantı noktasını kullanır.  Bulut hizmeti örnekleri yük dengedir, bu nedenle hangi örneğin bağlanılacağını doğrudan kontrol edebilirsiniz.  *Remoteforwarder* ve *RemoteAccess* aracıları, RDP trafiğini YÖNETIR ve istemcinin bir RDP tanımlama bilgisi göndermesini ve bağlanılacak tek bir örnek belirtmesini sağlar.  *Remoteforwarder* ve *RemoteAccess* aracıları, **20000*** bağlantı noktasının açık olmasını gerektirir ve bu, bir NSG varsa engellenmiş olabilir.

## <a name="additional-resources"></a>Ek kaynaklar

[Cloud Services’ı Yapılandırma](cloud-services-how-to-configure-portal.md)
