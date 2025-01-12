---
title: Proxy Azure AD Uygulaması bağlantıları ve URL 'Leri çevirme | Microsoft Docs
description: Azure AD uygulama proxy 'si ile yayımlanan uygulamalar için sabit kodlanmış bağlantıları yeniden yönlendirmeyi öğrenin.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/15/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb1935242790333a91b47ccecc19d934b8145085
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101688340"
---
# <a name="redirect-hard-coded-links-for-apps-published-with-azure-ad-application-proxy"></a>Azure AD Uygulama Ara Sunucusu yayımlanan uygulamalar için sabit kodlanmış bağlantıları yeniden yönlendirme

Azure AD Uygulama Ara Sunucusu, şirket içi uygulamalarınızı uzak veya kendi cihazlarındaki kullanıcılar için kullanılabilir hale getirir. Ancak, bazı uygulamalar HTML 'ye katıştırılmış yerel bağlantılarla geliştirilmiştir. Bu bağlantılar, uygulama uzaktan kullanıldığında düzgün çalışmaz. Birden çok şirket içi uygulamanız varsa, kullanıcılarınız ofis dışında olduklarında çalışmaya devam etmek için bağlantıları bekler. 

Bağlantıların şirket ağınızın hem içinde hem de dışında çalıştığından emin olmanın en iyi yolu, uygulamalarınızın dış URL 'Lerinin iç URL 'Leriyle aynı olacak şekilde yapılandırmaktır. Dış URL 'nizin varsayılan uygulama proxy 'si etki alanı yerine kurumsal etki alanı adına sahip olacak şekilde yapılandırılması için [özel etki alanlarını](application-proxy-configure-custom-domain.md) kullanın.


Kiracınızda özel etki alanlarını kullanmıyorsanız, bu işlevselliği sağlamak için birkaç başka seçenek vardır. Bunların hepsi aynı zamanda özel etki alanları ve birbirleriyle uyumludur, böylece özel etki alanlarını ve gerekirse diğer çözümleri yapılandırabilirsiniz.

> [!NOTE]
> JavaScript kullanılarak oluşturulan sabit kodlanmış iç URL 'Ler için bağlantı çevirisi desteklenmez.

**Seçenek 1: Microsoft Edge kullanın** – bu çözüm yalnızca, kullanıcıların Microsoft Edge tarayıcısı aracılığıyla uygulamaya erişmelerini önermeye veya gerektirmeye planlandıysanız geçerlidir. Yayımlanan tüm URL 'Leri işleyecek. 

**Seçenek 2: Uygulamaps uzantısını kullanın** – bu çözüm, kullanıcıların bir istemci tarafı tarayıcı uzantısı yüklemesini gerektirir, ancak yayımlanan tüm URL 'leri işleyecek ve en popüler tarayıcılarla çalışır. 

**Seçenek 3: bağlantı çevirisi ayarını kullanın** – bu, kullanıcılar için görünmeyen bir yönetim tarafı ayarıdır. Ancak, URL 'Leri yalnızca HTML ve CSS 'de işleymeyecektir.   

Bu üç özellik, kullanıcılarınızın nerede olduğuna bakılmaksızın bağlantılarınızı çalışır halde tutar. Doğrudan iç uç noktalara veya bağlantı noktalarına işaret eden uygulamalarınız varsa, bu iç URL 'Leri yayımlanmış dış uygulama proxy 'Si URL 'Lerine eşleyebilirsiniz. 

 
> [!NOTE]
> Son seçenek yalnızca, her nedenden dolayı, uygulamaları için aynı iç ve dış URL 'Lere sahip olan özel etki alanlarını kullanmıyorum kiracılar içindir. Bu özelliği etkinleştirmeden önce, bkz. [Azure AD uygulama ara sunucusu özel etki alanları](application-proxy-configure-custom-domain.md) sizin için çalışmayabilir. 
> 
> Ya da bağlantı çevirisi ile yapılandırmanız gereken uygulama SharePoint ise, bağlantıları eşleştirmeye yönelik başka bir yaklaşım için bkz. [sharepoint 2013 için alternatif erişim eşlemelerini yapılandırma](/SharePoint/administration/configure-alternate-access-mappings) . 

 
### <a name="option-1-microsoft-edge-integration"></a>Seçenek 1: Microsoft Edge tümleştirmesi 

Uygulamanızı ve içeriğinizi daha fazla korumak için Microsoft Edge 'i kullanabilirsiniz. Bu çözümü kullanmak için, kullanıcıların Microsoft Edge aracılığıyla uygulamaya erişmesi gerekir/önerilir. Uygulama proxy 'Si ile yayınlanan tüm iç URL 'Ler, Edge tarafından tanınır ve karşılık gelen dış URL 'ye yönlendirilir. Bu, tüm sabit kodlanmış iç URL 'Lerin çalışmasını sağlar ve bir kullanıcı tarayıcıya gider ve doğrudan iç URL 'yi yazdığında, kullanıcı uzakta olsa bile çalışır.  

Bu seçeneği yapılandırma hakkında daha fazla bilgi için, lütfen [iOS ve Android Için Edge kullanarak Web erişimini yönetme Microsoft Intune](/mem/intune/apps/manage-microsoft-edge) belgelerine bakın.  

### <a name="option-2-myapps-browser-extension"></a>Seçenek 2: Uygps tarayıcı uzantısı 

Uygps tarayıcı uzantısıyla, uygulama proxy 'Si ile yayınlanan tüm iç URL 'Ler uzantı tarafından tanınır ve ilgili dış URL 'ye yeniden yönlendirilir. Bu, tüm sabit kodlanmış iç URL 'Lerin çalışmasını sağlar ve bir kullanıcı tarayıcının adres çubuğuna gider ve dahili URL 'yi doğrudan türse, kullanıcı uzakta olsa bile çalışır.  

Bu özelliği kullanmak için kullanıcının uzantıyı indirmesi ve oturum açması gerekir. Yöneticiler veya kullanıcılar için başka yapılandırma gerekmez. 

Bu seçeneği yapılandırma hakkında daha fazla bilgi edinmek için lütfen [Uygps tarayıcı uzantısı](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) belgelerine bakın.

> [!NOTE]
> Uygps tarayıcı uzantısı joker karakter URL 'Leri için bağlantı çevirisini desteklemez.

### <a name="option-3-link-translation-setting"></a>Seçenek 3: bağlantı çevirisi ayarı 

Bağlantı çevirisi etkinleştirildiğinde, uygulama proxy hizmeti yayınlanmış iç bağlantılar için HTML ve CSS aracılığıyla arar ve kullanıcılarınızın kesintisiz bir deneyim almasını sağlayacak şekilde çevirir. Kullanıcılar için daha iyi bir deneyim sunduğundan, Uygps tarayıcı uzantısının kullanılması, bağlantı çevirisi ayarında tercih edilir.

> [!NOTE]
> Seçenek 2 veya 3 ' ü kullanıyorsanız, bunlardan yalnızca biri aynı anda etkinleştirilmelidir.

## <a name="how-link-translation-works"></a>Bağlantı çevirisinin nasıl çalıştığı

Kimlik doğrulamasından sonra, proxy sunucusu uygulama verilerini kullanıcıya geçirdiğinde, uygulama proxy 'Si uygulamayı sabit kodlanmış bağlantılar için tarar ve bunları ilgili, yayımlanan dış URL 'Ler ile değiştirir.

Uygulama proxy 'Si, uygulamaların UTF-8 ile kodlandığını varsayar. Böyle bir durum söz konusu değilse, kodlama türünü bir HTTP yanıt üst bilgisinde (gibi) belirtin `Content-Type:text/html;charset=utf-8` .

### <a name="which-links-are-affected"></a>Hangi bağlantılar etkileniyor?

Bağlantı çevirisi özelliği yalnızca bir uygulamanın gövdesinde kod etiketlerinde olan bağlantıları arar. Uygulama proxy 'Si, üst bilgilerdeki tanımlama bilgilerini veya URL 'Leri çevirmek için ayrı bir özelliğe sahiptir. 

Şirket içi uygulamalarda iki ortak iç bağlantı türü vardır:

- Paylaşılan bir kaynağa, gibi yerel bir dosya yapısında işaret eden **göreli iç bağlantılar** `/claims/claims.html` . Bu bağlantılar, uygulama proxy 'Si aracılığıyla yayımlanan uygulamalarda otomatik olarak çalışır ve bağlantı çevirisi ile veya bu olmadan çalışmaya devam eder. 
- Gibi diğer şirket içi uygulamalara yönelik olarak **sabit kodlanmış iç bağlantılar** `http://expenses` veya gibi yayımlanmış dosyalar `http://expenses/logo.jpg` . Bağlantı çevirisi özelliği, sabit kodlanmış iç bağlantılar üzerinde çalışarak, uzak kullanıcıların gitmesi gereken dış URL 'lere işaret etmek üzere değişiklik halleder.

Uygulama proxy 'Sinin, ekleme için bağlantı çevirisini desteklediği HTML kod etiketlerindeki özniteliklerin tamamı listesi:
* a (href)
* Ses (src)
* taban (href)
* düğme (formaction)
* div (Data-background, Style, Data-src)
* Ekle (src)
* Form (eylem)
* çerçeve (src)
* baş (profil)
* HTML (bildirim)
* iframe (longdesc, src)
* img (longdesc, src)
* Giriş (formaction, src, değer)
* bağlantı (href)
* MenuItem (simge)
* meta (içerik)
* nesne (Arşiv, veri, kod temeli)
* betik (src)
* Kaynak (src)
* izle (src)
* video (src, poster)

Ayrıca, CSS içinde URL özniteliği de çevrilir.

### <a name="how-do-apps-link-to-each-other"></a>Uygulamalar birbirlerine nasıl bağlanır?

Her uygulama için bağlantı çevirisi etkinleştirilir, böylece uygulama başına düzeyinde kullanıcı deneyimi üzerinde denetime sahip olursunuz. Uygulamanın bağlantılarını değil *, bu* *uygulamadaki bağlantıların çevrilmesini* istediğinizde bir uygulama için bağlantı çevirisini açın. 

Örneğin, uygulama proxy 'Si aracılığıyla yayımlanmış üç uygulamanız olduğunu varsayalım: avantajlar, giderler ve seyahat. Uygulama proxy 'Si aracılığıyla yayımlanmamış bir dördüncü uygulama, geri bildirim var.

Avantajlar uygulaması için bağlantı çevirisini etkinleştirdiğinizde, gider ve seyahat bağlantıları bu uygulamalar için dış URL 'lere yönlendirilir, ancak dış URL olmadığından geri bildirimde bağlantı yeniden yönlendirilmez. Bu iki uygulama için bağlantı çevirisi etkinleştirilmemiş olduğundan, giderlerin avantajlara geri dönüş bağlantıları çalışmaz.

![Bağlantı çevirisi etkinken avantajlardan diğer uygulamalara bağlantılar](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Hangi bağlantılar çevrilmedi?

Performansı ve güvenliği artırmak için bazı bağlantılar çevrilmez:

- Bağlantılar kod etiketlerinin içinde değil. 
- HTML veya CSS 'de bulunmayan bağlantılar. 
- URL kodlu biçimdeki bağlantılar.
- Diğer programlardan açılan iç bağlantılar. E-posta veya anlık ileti aracılığıyla gönderilen veya diğer belgelere eklenen bağlantılar çevrilmeyecektir. Kullanıcıların dış URL 'ye gitmek için bilmeleri gerekir.

Bu iki senaryonun birini desteklemeniz gerekiyorsa, bağlantı çevirisi yerine aynı iç ve dış URL 'Leri kullanın.  

## <a name="enable-link-translation"></a>Bağlantı çevirisini etkinleştir

Bağlantı çevirisi ile çalışmaya başlamak, bir düğmeye tıklanması kadar kolaydır:

1. [Azure Portal](https://portal.azure.com)’da yönetici olarak oturum açın.
2. **Azure Active Directory**  >  **Kurumsal uygulamalar**  >  **tüm uygulamalar** ' a gidin > > **uygulama proxy 'sini** yönetmek istediğiniz uygulamayı seçin.
3. **Uygulama gövdesindeki URL 'Leri** **Evet** olarak çevir seçeneğini açın.

   ![Uygulama gövdesinde URL 'Leri çevirmek için Evet ' i seçin](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)
4. Değişikliklerinizi uygulamak için **Kaydet**’i seçin.

Artık kullanıcılarınız bu uygulamaya erişirken, proxy, kiracınızda uygulama proxy 'Si aracılığıyla yayınlanan iç URL 'Leri otomatik olarak tarar.

## <a name="send-feedback"></a>Geri bildirim gönder

Bu özelliği tüm uygulamalarınız için çalışır hale getirmenize yardımcı olmak istiyoruz. HTML ve CSS 'de 30 etiket üzerinde arama yaptık. Çevrilmemiş bağlantıların oluşturulmuş bir örneğine sahipseniz, [uygulama proxy 'Sine geri bildirimde](mailto:aadapfeedback@microsoft.com)bir kod parçacığı gönderin. 

## <a name="next-steps"></a>Sonraki adımlar
Aynı iç ve dış URL 'ye sahip olmak için [Azure AD uygulama ara sunucusu ile özel etki alanları kullanın](application-proxy-configure-custom-domain.md)

[SharePoint 2013 için alternatif erişim eşlemelerini yapılandırma](/SharePoint/administration/configure-alternate-access-mappings)
