---
title: Azure portal içinde Azure Stack Edge Mini R cihazının cihaz, güncelleştirme, saatini ayarlama öğreticisi
description: Azure Stack Edge Mini 'i dağıtma öğreticisi, fiziksel cihazınız için cihaz, güncelleştirme ve zaman ayarlarını ayarlamanıza olanak sağlar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Mini R  so I can use it to transfer data to Azure.
ms.openlocfilehash: e9f4d4f4ad27081bb105cdb1698438837fc2fe02
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100546678"
---
# <a name="tutorial-configure-the-device-settings-for-azure-stack-edge-mini-r"></a>Öğretici: Azure Stack Edge Mini R için cihaz ayarlarını yapılandırma

Bu öğreticide, yerleşik GPU ile Azure Stack Edge Mini R cihazınız için cihazla ilgili ayarları nasıl yapılandıracağınız açıklanmaktadır. Yerel Web Kullanıcı arabirimi aracılığıyla cihaz adınızı, güncelleştirme sunucusunu ve zaman sunucusunu ayarlayabilirsiniz.

Cihaz ayarlarının tamamlanması yaklaşık 5-7 dakika sürebilir.

Bu öğreticide şunları öğrenirsiniz:

> [!div class="checklist"]
>
> * Önkoşullar
> * Cihaz ayarlarını yapılandırma
> * Güncelleştirmeyi yapılandır 
> * Saati Yapılandır

## <a name="prerequisites"></a>Önkoşullar

GPU ile Azure Stack Edge Mini R cihazınızda cihazla ilgili ayarları yapılandırmadan önce şunları yaptığınızdan emin olun:

* Fiziksel cihazınız için:

    - Fiziksel cihazı [yükleme Azure Stack Edge Mini R ](azure-stack-edge-mini-r-deploy-install.md)' de ayrıntılı olarak yüklediniz.
    - Eğitim bölümünde açıklandığı gibi cihazınızda ağ ve etkinleştirilmiş ve yapılandırılmış işlem ağını [yapılandırın: GPU ile Azure Stack Edge Mini R için ağı yapılandırma](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md).


## <a name="configure-device-settings"></a>Cihaz ayarlarını yapılandırma

Cihazla ilgili ayarları yapılandırmak için aşağıdaki adımları izleyin:

1. **Cihaz** sayfasında, aşağıdaki adımları uygulayın:

    1. Cihazınız için kolay bir ad girin. Kolay ad 1 ile 13 karakter arasında olmalıdır ve harf, rakam ve kısa çizgi içerebilir.

    2. Cihazınız için bir **DNS etki alanı** sağlayın. Bu etki alanı, cihazı bir dosya sunucusu olarak kurmak için kullanılır.

    3. Yapılandırılan cihaz ayarlarını doğrulamak ve uygulamak için **Uygula**' yı seçin.

        ![Yerel Web Kullanıcı arabirimi "cihaz" sayfa 1](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/set-up-device-1.png)

        Cihaz adını ve DNS etki alanını değiştirdiyseniz, cihazda var olan otomatik olarak imzalanan sertifikalar çalışmaz. 

        ![Yerel Web Kullanıcı arabirimi "cihaz" sayfa 2](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/set-up-device-2.png)

        Sertifikaları yapılandırırken aşağıdaki seçeneklerden birini belirlemeniz gerekir: 
        
        - Cihaz sertifikaları oluşturun ve indirin. 
        - İmzalama zinciri dahil olmak üzere cihaz için kendi sertifikalarınızı getirin.
    

    4. Cihaz adı ve DNS etki alanı değiştirildiğinde, SMB uç noktası oluşturulur.  

        ![Yerel Web Kullanıcı arabirimi "cihaz" sayfa 3](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/set-up-device-3.png)

    5. Ayarlar uygulandıktan sonra **İleri: güncelleştirme sunucusu**' nu seçin.


## <a name="configure-update"></a>Güncelleştirmeyi yapılandır

1. **Güncelleştirme** sayfasında, bundan böyle, cihazınızın güncelleştirmelerinin indirileceği konumu yapılandırabilirsiniz.  

    - Güncelleştirmeleri doğrudan **Microsoft Update sunucusundan** alabilirsiniz.

        ![Yerel Web Kullanıcı arabirimi "güncelleştirme sunucusu" sayfası](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/update-server-1.png)

        Güncelleştirmeleri **Windows Server Update Services** 'dan (WSUS) dağıtmayı da tercih edebilirsiniz. WSUS sunucusunun yolunu belirtin.
        
        ![Yerel Web Kullanıcı arabirimi "güncelleştirme sunucusu" sayfa 2](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/update-server-2.png)

        > [!NOTE] 
        > Ayrı bir Windows Update sunucusu yapılandırıldıysa ve *https* üzerinden bağlanmayı seçerseniz ( *http* yerine), güncelleştirme sunucusuna bağlanmak için gereken imzalama zinciri sertifikaları gereklidir. Sertifika oluşturma ve karşıya yükleme hakkında daha fazla bilgi için, [sertifikaları Yönet](azure-stack-edge-gpu-manage-certificates.md)' e gidin. Azure Stack Edge cihazınız, modüler veri merkezine katmanlama gibi bağlantısı kesik bir modda çalışmak için WSUS seçeneğini etkinleştirin. Etkinleştirme sırasında cihaz güncelleştirmeleri tarar ve sunucu ayarlanmamışsa, etkinleştirme başarısız olur. 

2. **Uygula**’yı seçin.
3. Güncelleştirme sunucusu yapılandırıldıktan sonra, **İleri: Saat**' i seçin.
    

## <a name="configure-time"></a>Saati Yapılandır

Cihazınızda saat ayarlarını yapılandırmak için bu adımları izleyin. 

> [!IMPORTANT]
> Zaman ayarları isteğe bağlı olsa da, cihazınız için yerel ağ üzerinde birincil bir NTP ve ikincil bir NTP sunucusu yapılandırmanızı önemle öneririz. Yerel sunucu kullanılabilir değilse, genel NTP sunucuları yapılandırılabilir.

Cihazınızın bulut hizmeti sağlayıcılarınız ile kimlik doğrulaması yapabilmesi için zaman eşitlemesini gerektiğinden NTP sunucuları gereklidir.

1. **Zaman** sayfasında, cihazınız için saat dilimini ve birincil ve ikincil NTP sunucularını seçebilirsiniz.  
    
    1. **Saat dilimi** açılan listesinde, cihazın dağıtıldığı coğrafi konuma karşılık gelen saat dilimini seçin.
        Cihazınızın varsayılan saat dilimi PST ' dir. Cihazınız zamanlanan tüm işlemler için bu saat dilimini kullanır.

    2. **BIRINCIL NTP sunucusu** kutusunda, cihazınızın birincil sunucusunu girin veya Time.Windows.com varsayılan değerini kabul edin.  
        Ağınızda NTP trafiğinin veri merkezinizden internet 'e geçmesine izin verdiğinden emin olun.

    3. İsteğe bağlı olarak, **IKINCIL NTP sunucusu** kutusuna cihazınız için bir ikincil sunucu girin.

    4. Yapılandırılan saat ayarlarını doğrulamak ve uygulamak için **Uygula**' yı seçin.

        ![Yerel Web Kullanıcı arabirimi "saat" sayfası](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/time-settings-1.png)

2. Ayarlar uygulandıktan sonra **İleri: sertifikalar**' ı seçin.


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları öğrenirsiniz:

> [!div class="checklist"]
>
> * Önkoşullar
> * Cihaz ayarlarını yapılandırma
> * Güncelleştirmeyi yapılandır 
> * Saati Yapılandır

Azure Stack Edge Mini R cihazınız için sertifikaların nasıl yapılandırılacağını öğrenmek için bkz.:

> [!div class="nextstepaction"]
> [Sertifikaları yapılandırma](./azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md)
