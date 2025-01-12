---
title: Web sayfalarıyla Gerçek Kullanıcı Ölçümleri-Azure Traffic Manager
description: Bu makalede, Azure Traffic Manager 'a Gerçek Kullanıcı Ölçümleri göndermek üzere Web sayfalarınızı ayarlamayı öğrenin.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: duau
ms.custom: ''
ms.openlocfilehash: 2bb104fd9cfc3c4ffddb82e4cf442e94c4650550
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98184567"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Web sayfalarını kullanarak Azure Traffic Manager 'a Gerçek Kullanıcı Ölçümleri gönderme

Web sayfalarınızı, bir Gerçek Kullanıcı Ölçümleri (RUM) anahtarı alarak ve oluşturulan kodu Web sayfasına ekleyerek Traffic Manager Gerçek Kullanıcı Ölçümleri gönderecek şekilde yapılandırabilirsiniz.

## <a name="obtain-a-real-user-measurements-key"></a>Gerçek Kullanıcı Ölçümleri anahtarı edinin

İstemci uygulamanızdan Traffic Manager alıp gönderen ölçümler, **gerçek Kullanıcı ölçümleri (Rum) anahtarı** olarak adlandırılan benzersiz bir dize kullanılarak hizmet tarafından tanımlanır. Azure portal, REST API veya PowerShell veya Azure CLı kullanarak bir RUM anahtarı alabilirsiniz.

Azure portal kullanarak RUM anahtarını almak için:
1. Bir tarayıcıdan Azure portalında oturum açın. Henüz bir hesabınız yoksa, bir aylık ücretsiz denemeye kaydolabilirsiniz.
2. Portalın arama çubuğunda, değiştirmek istediğiniz Traffic Manager profili adını arayın ve ardından gösterilen sonuçlardaki Traffic Manager profiline tıklayın.
3. Traffic Manager profili dikey penceresinde **Ayarlar**' ın altında **gerçek Kullanıcı ölçümleri** ' a tıklayın.
4. Yeni bir RUM anahtarı oluşturmak için **anahtar oluştur** ' a tıklayın.
 
   ![Gerçek Kullanıcı Ölçümleri anahtar oluştur](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Şekil 1: anahtar oluşturmayı Gerçek Kullanıcı Ölçümleri**

5. Dikey pencere artık oluşturulan RUM anahtarını ve HTML sayfanıza katıştırılması gereken bir JavaScript kod parçacığını görüntüler.
 
    ![Gerçek Kullanıcı Ölçümleri anahtarı için JavaScript kodu](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **Şekil 2: anahtar ve ölçüm JavaScript Gerçek Kullanıcı Ölçümleri**
 
6. JavaScript kodunu kopyalamak için **Kopyala** düğmesine tıklayın. 

>[!IMPORTANT]
> Gerçek Kullanıcı Ölçümleri özelliği için üretilen JavaScript 'ı kullanarak düzgün şekilde çalışır. Bu betikteki veya Gerçek Kullanıcı Ölçümleri tarafından kullanılan betiklerdeki değişiklikler öngörülemeyen davranışlara neden olabilir.

## <a name="embed-the-code-to-an-html-web-page"></a>Kodu bir HTML Web sayfasına ekleme

RUM anahtarını aldıktan sonra, bir sonraki adım, bu kopyalanmış JavaScript 'ı son kullanıcılarınızın ziyaret ettiği bir HTML sayfasına katıştırmanız gerekir. HTML 'nin düzenlenmesine birçok şekilde ve farklı araçlar ve iş akışları kullanılarak yapılabilir. Bu örnek, bu betiği eklemek için bir HTML sayfasının nasıl güncelleştirilmesini gösterir. Bu kılavuzu, HTML kaynak yönetimi iş akışınıza uyarlamak için kullanabilirsiniz.

1.  HTML sayfasını bir metin düzenleyicisinde açma
2.  Önceki adımda kopyaladığınız JavaScript kodunu HTML 'nin gövde bölümüne yapıştırın (kopyalanmış kod 8. satır, & 8. satır, bkz. Şekil 3).
 
    ![Gerçek Kullanıcı Ölçümleri için JavaScript kodunu Web sayfasına ekleme](./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png)  

    **Şekil 3: katıştırılmış Gerçek Kullanıcı Ölçümleri JavaScript ile basit HTML**

3.  HTML dosyasını kaydedin ve internet 'e bağlı bir Web sunucusu üzerinde barındırın. 
4. Bu sayfa bir Web tarayıcısında işlendiğinde, başvurulan JavaScript indirilir ve betik ölçüm ve raporlama işlemlerini yürütür.


## <a name="next-steps"></a>Sonraki adımlar
- [Gerçek Kullanıcı ölçümleri](traffic-manager-rum-overview.md) hakkında daha fazla bilgi edinin
- [Traffic Manager nasıl çalıştığını](traffic-manager-overview.md) öğrenin
- Traffic Manager tarafından desteklenen [trafik yönlendirme yöntemleri](traffic-manager-routing-methods.md) hakkında daha fazla bilgi edinin
- [Traffic Manager profili oluşturmayı](./quickstart-create-traffic-manager-profile.md) öğrenin