---
title: 'Azure AD Connect: kesintisiz tek Sign-On-hızlı başlangıç | Microsoft Docs'
description: Bu makalede Azure Active Directory kesintisiz tek Sign-On kullanmaya nasıl başlacağınız açıklanmaktadır
services: active-directory
keywords: Azure AD Connect nedir, yükler Active Directory, Azure AD, SSO, çoklu oturum açma için gerekli bileşenler
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 349aef1bb9382eec19d9ad9c7f6d4579c82b62de
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043946"
---
# <a name="azure-active-directory-seamless-single-sign-on-quickstart"></a>Kesintisiz çoklu oturum açma Azure Active Directory: hızlı başlangıç

## <a name="deploy-seamless-single-sign-on"></a>Kesintisiz tek Sign-On dağıtma

Azure Active Directory (Azure AD) sorunsuz tek Sign-On (sorunsuz SSO), kullanıcılar şirket ağınıza bağlı olan kurumsal masaüstlerinde olduklarında otomatik olarak oturum açar. Sorunsuz SSO, kullanıcılarınıza ek şirket içi bileşenlere gerek duymadan bulut tabanlı uygulamalarınıza kolay erişim sağlar.

Sorunsuz SSO dağıtmak için aşağıdaki adımları izleyin.

## <a name="step-1-check-the-prerequisites"></a>1. Adım: önkoşulları denetleme

Aşağıdaki önkoşulların yerinde olduğundan emin olun:

* **Azure AD Connect sunucunuzu ayarlama**: [geçiş kimlik doğrulamasını](how-to-connect-pta.md) oturum açma yönteminiz olarak kullanırsanız, ek önkoşul denetimi gerekli değildir. Oturum açma yönteminiz olarak [Parola karması eşitlemesi](how-to-connect-password-hash-synchronization.md) kullanıyorsanız ve Azure AD Connect Ile Azure AD arasında bir güvenlik duvarı varsa, aşağıdakileri doğrulayın:
   - Azure AD Connect sürüm 1.1.644.0 veya üstünü kullanıyorsunuz. 
   - Güvenlik duvarınız veya ara sunucunuz izin veriyorsa, bağlantı noktası 443 üzerinden **\* . Msappproxy.net** URL 'leri için izin verilen listeye bağlantı ekleyin. Proxy yapılandırması için bir joker karakter yerine belirli bir URL gerekiyorsa, **tenantid.Registration.msappproxy.net** yapılandırabilirsiniz; burada tenantıd, özelliği yapılandırdığınız KIRACıNıN GUID 'sidir. Kuruluşunuzda URL tabanlı ara sunucu özel durumları mümkün değilse, bunun yerine haftalık olarak güncellenen [Azure veri MERKEZI IP aralıklarına](https://www.microsoft.com/download/details.aspx?id=41653)erişime izin verebilirsiniz. Bu önkoşul yalnızca özelliği etkinleştirdiğinizde geçerlidir. Bu, gerçek Kullanıcı oturum açma işlemleri için gerekli değildir.

    >[!NOTE]
    >Azure AD Connect sürümleri 1.1.557.0, 1.1.558.0, 1.1.561.0 ve 1.1.614.0, Parola karması eşitlemeyle ilgili bir sorun var. Parola karması eşitlemesini doğrudan kimlik doğrulamasıyla birlikte _kullanmayı düşünmüyorsanız,_ daha fazla bilgi edinmek için [Azure AD Connect sürüm notlarını](./reference-connect-version-history.md) okuyun.
    
    >[!NOTE]
    >Giden bir HTTP proxy 'si varsa, bu URL 'nin autologon.microsoftazuread-sso.com, izin verilenler listesinde olduğundan emin olun. Joker karakter kabul edilmedikleri için bu URL 'YI açıkça belirtmeniz gerekir. 

* **Desteklenen bir Azure AD Connect topolojisi kullanın**: [burada](plan-connect-topologies.md)açıklanan Azure AD Connect desteklenen topolojilerden birini kullandığınızdan emin olun.

    >[!NOTE]
    >Sorunsuz SSO, aralarında AD güvenleri olup olmadığı gibi birden çok AD ormanını destekler.

* **Etki alanı yöneticisi kimlik bilgilerini ayarlama**: her bir Active Directory ormanı için etki alanı yöneticisi kimlik bilgilerine sahip olmanız gerekir:
    * Azure AD Connect aracılığıyla Azure AD 'ye eşitliyorsanız.
    * Sorunsuz SSO için etkinleştirmek istediğiniz kullanıcıları içerir.
    
* **Modern kimlik doğrulamasını etkinleştir**: Bu özelliğin çalışması için kiracınızda [modern kimlik doğrulamayı](/office365/enterprise/modern-auth-for-office-2013-and-2016) etkinleştirmeniz gerekir.

* **Microsoft 365 istemcilerinin en son sürümlerini kullanın**: Microsoft 365 Istemcileri (Outlook, Word, Excel ve diğerleri) ile sessiz bir oturum açma deneyimi almak için, kullanıcılarınızın 16.0.8730. xxxx veya üzeri sürümlerini kullanması gerekir.

## <a name="step-2-enable-the-feature"></a>2. Adım: özelliği etkinleştirme

[Azure AD Connect](whatis-hybrid-identity.md)aracılığıyla sorunsuz SSO 'yu etkinleştirin.

>[!NOTE]
> Gereksinimlerinizi karşılamıyorsa, [PowerShell kullanarak sorunsuz SSO](tshoot-connect-sso.md#manual-reset-of-the-feature) 'yu de etkinleştirebilirsiniz Azure AD Connect. Active Directory ormanı başına birden fazla etki alanınız varsa ve için sorunsuz SSO 'yu etkinleştirmek istediğiniz etki alanı hakkında daha fazla bilgi almak istiyorsanız bu seçeneği kullanın.

Azure AD Connect yeni bir yüklemesini gerçekleştiriyorsanız, [özel yükleme yolunu](how-to-connect-install-custom.md)seçin. **Kullanıcı oturum açma** sayfasında **Çoklu oturum açmayı etkinleştir** seçeneğini belirleyin.

>[!NOTE]
> Seçenek yalnızca oturum açma yöntemi **Parola karması eşitleme** veya **geçişli kimlik doğrulama** olduğunda seçilebilir.

![Azure AD Connect: Kullanıcı oturumu açma](./media/how-to-connect-sso-quick-start/sso8.png)

Zaten bir Azure AD Connect kurulumunuz varsa, Azure AD Connect **Kullanıcı oturum açma sayfasını Değiştir** ' i seçin ve ardından **İleri**' yi seçin. Azure AD Connect sürümleri 1.1.880.0 veya üzerini kullanıyorsanız, **Çoklu oturum açmayı etkinleştir** seçeneği varsayılan olarak seçilir. Azure AD Connect eski sürümlerini kullanıyorsanız **Çoklu oturum açmayı etkinleştir** seçeneğini belirleyin.

![Azure AD Connect: Kullanıcı oturumunu değiştirme](./media/how-to-connect-sso-quick-start/changeusersignin.png)

**Çoklu oturum açmayı etkinleştir** sayfasına ulaşana kadar sihirbaza devam edin. Her bir Active Directory ormanı için etki alanı yöneticisi kimlik bilgilerini sağlayın:

* Azure AD Connect aracılığıyla Azure AD 'ye eşitliyorsanız.
* Sorunsuz SSO için etkinleştirmek istediğiniz kullanıcıları içerir.

Sihirbazın tamamlanmasından sonra, kiracınızda sorunsuz SSO etkinleştirilmiştir.

>[!NOTE]
> Etki alanı yöneticisi kimlik bilgileri Azure AD Connect veya Azure AD 'de depolanmaz. Yalnızca özelliği etkinleştirmek için kullanılır.

Sorunsuz SSO 'yu doğru şekilde etkinleştirdiğinizi doğrulamak için aşağıdaki yönergeleri izleyin:

1. [Azure Active Directory Yönetim merkezinde](https://aad.portal.azure.com) kiracınızın genel yönetici kimlik bilgileriyle oturum açın.
2. Sol bölmedeki **Azure Active Directory** seçin.
3. **Azure AD Connect** seçin.
4. **Sorunsuz çoklu oturum açma** özelliğinin **etkin** olarak göründüğünü doğrulayın.

![Azure portal: Azure AD Connect bölmesi](./media/how-to-connect-sso-quick-start/sso10.png)

>[!IMPORTANT]
> Sorunsuz SSO, `AZUREADSSOACC` her ad ormanında şirket içi Active Directory (ad) adında bir bilgisayar hesabı oluşturur. `AZUREADSSOACC`Güvenlik nedenleriyle bilgisayar hesabının güçlü korunması gerekir. Bilgisayar hesabını yalnızca etki alanı yöneticileri yönetebilmelidir. Bilgisayar hesabında Kerberos temsilcisinin devre dışı bırakıldığından ve Active Directory ' deki başka hiçbir hesabın bilgisayar hesabında temsilciliizin izinlerine sahip olmadığından emin olun `AZUREADSSOACC` . Bilgisayar hesabını, yanlışlıkla silinmelerden güvenli oldukları ve yalnızca etki alanı yöneticilerinin erişimi olan bir kuruluş biriminde (OU) depolayın.

>[!NOTE]
> Şirket içi ortamınızda, karma ve kimlik bilgisi hırsızlığı azaltma mimarilerini kullanıyorsanız, `AZUREADSSOACC` bilgisayar hesabının karantina kapsayıcısında bitmediğinden emin olmak için uygun değişiklikleri yapın. 

## <a name="step-3-roll-out-the-feature"></a>3. Adım: özelliği kullanıma alma

Aşağıda belirtilen yönergeleri kullanarak kullanıcılarınıza sorunsuz SSO 'yu yavaş bir şekilde dağıtabilirsiniz. Aşağıdaki Azure AD URL 'sini, Active Directory grup ilkesi kullanarak, tüm kullanıcıların Intranet bölgesi ayarlarına ekleyerek başlayabilirsiniz:

- `https://autologon.microsoftazuread-sso.com`

Ayrıca, grup ilkesi aracılığıyla **betik aracılığıyla durum çubuğuna güncelleştirmelere Izin ver** adlı bir Intranet bölgesi ilkesi ayarını etkinleştirmeniz gerekir. 

>[!NOTE]
> Aşağıdaki yönergeler yalnızca Windows 'da Internet Explorer, Microsoft Edge ve Google Chrome için geçerlidir (Internet Explorer ile bir güvenilen site URL 'si kümesini paylaşıyorsa). MacOS 'ta Mozilla Firefox ve Google Chrome ayarlama yönergeleri için sonraki bölümü okuyun.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Kullanıcıların Intranet bölgesi ayarlarını neden değiştirmeniz gerekiyor?

Varsayılan olarak tarayıcı, doğru bölgeyi Internet veya Intranet ' i belirli bir URL 'den otomatik olarak hesaplar. Örneğin, `http://contoso/` Intranet bölgesine eşlenir, öte yandan `http://intranet.contoso.com/` Internet bölgesiyle EŞLENIR (URL bir nokta içereceğinden). URL 'YI tarayıcının Intranet bölgesine açıkça eklemediğiniz takdirde, tarayıcılar Azure AD URL 'SI gibi bir bulut uç noktasına Kerberos bileti göndermez.

Kullanıcıların Intranet bölgesi ayarlarını değiştirmek için iki yol vardır:

| Seçenek | Yönetici değerlendirmesi | Kullanıcı deneyimi |
| --- | --- | --- |
| Grup İlkesi | Yönetici, Intranet bölgesi ayarlarının düzenlemesini kilitler | Kullanıcılar kendi ayarlarını değiştiremezler |
| Grup İlkesi tercihi |  Yönetici, Intranet bölgesi ayarlarında düzenlenmesine izin veriyor | Kullanıcılar kendi ayarlarını değiştirebilir |

### <a name="group-policy-option---detailed-steps"></a>"Grup İlkesi" seçeneği-ayrıntılı adımlar

1. Grup İlkesi Yönetimi Düzenleyicisi aracını açın.
2. Kullanıcılarınıza veya tümüne uygulanan Grup ilkesini düzenleyin. Bu örnek **varsayılan etki alanı ilkesi** kullanır.
3.   >    >    >  **Windows bileşenleri**  >  **Internet Explorer**  >  **Internet Denetim Masası**  >  **Güvenlik sayfası** Yönetim Şablonları Kullanıcı yapılandırma ilkelerine gidin. Ardından **siteden bölgeye atama listesi**' ni seçin.
    !["Güvenlik sayfası" nı "Site-bölge atama listesi" seçiliyken gösteren ekran görüntüsü.](./media/how-to-connect-sso-quick-start/sso6.png)
4. İlkeyi etkinleştirin ve iletişim kutusuna aşağıdaki değerleri girin:
   - **Değer adı**: Kerberos biletleri Iletileceği Azure AD URL 'si.
   - **Değer** (veri): **1** Intranet bölgesini gösterir.

     Sonuç şuna benzer:

     Değer adı: `https://autologon.microsoftazuread-sso.com`
  
     Değer (veri): 1

   >[!NOTE]
   > Bazı kullanıcıların sorunsuz SSO 'yu kullanmasını engellemek istiyorsanız (örneğin, bu kullanıcılar paylaşılan kiler 'de oturum açtığında), önceki değerleri **4** olarak ayarlayın. Bu eylem, Azure AD URL 'sini kısıtlanmış bölgeye ekler ve her seferinde sorunsuz SSO başarısız olur.
   >

5. **Tamam**’ı ve ardından tekrar **Tamam**’ı seçin.

    ![Bir bölge atamasının seçili olduğu "Içeriği göster" penceresini gösteren ekran görüntüsü.](./media/how-to-connect-sso-quick-start/sso7.png)

6.   >    >    >  **Windows bileşenleri**  >  **Internet Explorer**  >  **Internet Denetim Masası**  >  **Güvenlik sayfası**  >  **Intranet bölgesi** Yönetim Şablonları Kullanıcı yapılandırma ilkelerine gidin. Ardından **betik aracılığıyla durum çubuğunda güncelleştirmelere Izin ver**' i seçin.

    !["Intranet bölgesi" sayfasını, "komut dosyası aracılığıyla durum çubuğuna güncelleştirmeler Izin ver" seçiliyken gösteren ekran görüntüsü.](./media/how-to-connect-sso-quick-start/sso11.png)

7. İlke ayarını etkinleştirin ve ardından **Tamam**' ı seçin.

    ![İlke ayarı etkinken "komut dosyası aracılığıyla durum çubuğunda güncelleştirmelere Izin ver" penceresinin gösterildiği ekran görüntüsü.](./media/how-to-connect-sso-quick-start/sso12.png)

### <a name="group-policy-preference-option---detailed-steps"></a>"Grup İlkesi tercihi" seçeneği-ayrıntılı adımlar

1. Grup İlkesi Yönetimi Düzenleyicisi aracını açın.
2. Kullanıcılarınıza veya tümüne uygulanan Grup ilkesini düzenleyin. Bu örnek **varsayılan etki alanı ilkesi** kullanır.
3. **Kullanıcı yapılandırma**  >  **tercihleri**  >  **Windows ayarları**  >  **kayıt defteri**  >  **Yeni**  >  **kayıt defteri öğesine** gidin.

    !["Kayıt defteri" ve "kayıt defteri öğesi" öğesinin seçili olduğunu gösteren ekran görüntüsü.](./media/how-to-connect-sso-quick-start/sso15.png)

4. Uygun alanlara aşağıdaki değerleri girin ve **Tamam**' a tıklayın.
   - **Anahtar yolu**: **_SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap\Domains\microsoftazuread-SSO.com\autologon_**
   - **Değer adı**: **_https_**
   - **Değer türü**: **_REG_DWORD_**
   - **Değer verisi**: **_00000001_**
 
     !["Yeni kayıt defteri özellikleri" penceresini gösteren ekran görüntüsü.](./media/how-to-connect-sso-quick-start/sso16.png)
 
     ![Çoklu oturum açma](./media/how-to-connect-sso-quick-start/sso17.png)

### <a name="browser-considerations"></a>Tarayıcı konuları

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (tüm platformlar)

Mozilla Firefox, Kerberos kimlik doğrulamasını otomatik olarak kullanmaz. Her kullanıcının aşağıdaki adımları kullanarak, Azure AD URL 'sini Firefox ayarlarına el ile eklemesi gerekir:
1. Firefox 'u çalıştırın ve `about:config` Adres çubuğuna girin. Gördüğünüz tüm bildirimleri kapatın.
2. **Network. Negotiate-Auth. Trusted-uris** tercihini arayın. Bu tercih edilecek Kerberos kimlik doğrulaması için Firefox 'un güvenilen siteleri listelenir.
3. Sağ tıklayın ve **Değiştir**' i seçin.
4. `https://autologon.microsoftazuread-sso.com`Alana girin.
5. **Tamam** ' ı seçin ve ardından tarayıcıyı yeniden açın.

#### <a name="safari-macos"></a>Safari (macOS)

MacOS çalıştıran makinenin AD 'ye katılmış olduğundan emin olun. MacOS cihazınızı AD ile birleştirme yönergeleri Bu makalenin kapsamı dışındadır.

#### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Kmıum temelinde Microsoft Edge (tüm platformlar)

Ortamınızdaki [Authnegotiatedelegateallowlist](/DeployEdge/microsoft-edge-policies#authnegotiatedelegateallowlist) veya [authserverallowlist](/DeployEdge/microsoft-edge-policies#authserverallowlist) ilke ayarlarını geçersiz KıLDıYSANıZ, Azure AD 'nin URL 'sini () da eklemediğinizden emin olun `https://autologon.microsoftazuread-sso.com` .

#### <a name="microsoft-edge-based-on-chromium-macos-and-other-non-windows-platforms"></a>Kmıum temelinde Microsoft Edge (macOS ve diğer Windows dışı platformlar)

MacOS ve diğer Windows dışı platformlarda Kmıum 'u temel alan Microsoft Edge için, tümleşik kimlik listesine yönelik Azure AD URL 'sini izin verilenler listenize ekleme hakkında bilgi için, [Kmıum Ilke listesini temel alan Microsoft Edge](/DeployEdge/microsoft-edge-policies#authserverallowlist) 'e bakın.

#### <a name="google-chrome-all-platforms"></a>Google Chrome (tüm platformlar)

Ortamınızdaki [Authnegotiatedelegatewhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) veya [authserverwhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) ilke ayarlarını geçersiz KıLDıYSANıZ, Azure AD 'nin URL 'sini () da eklemediğinizden emin olun `https://autologon.microsoftazuread-sso.com` .

#### <a name="google-chrome-macos-and-other-non-windows-platforms"></a>Google Chrome (macOS ve diğer Windows dışı platformlar)

MacOS ve diğer Windows dışı platformlardaki Google Chrome için, tümleşik kimlik doğrulaması için Azure AD URL 'SI için izin verilenler listesini denetleme hakkında bilgi için, [Kmıum proje Ilkesi listesine](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) bakın.

Mac kullanıcıları için Azure AD URL 'sini Firefox ve Google Chrome 'a aktarmak için üçüncü taraf Active Directory grup ilkesi uzantılarının kullanımı Bu makalenin kapsamı dışındadır.

#### <a name="known-browser-limitations"></a>Bilinen tarayıcı sınırlamaları

Sorunsuz SSO, Firefox ve Microsoft Edge (eski) tarayıcılarında özel göz atma modunda çalışmaz. Tarayıcı Gelişmiş korumalı modda çalışıyorsa Internet Explorer 'da da çalışmaz. Sorunsuz SSO, Microsoft Edge 'in bir sonraki sürümünü, Kmıum 'a göre destekler ve tasarım tarafından InPrivate ve konuk modunda çalışmaktadır.

## <a name="step-4-test-the-feature"></a>4. Adım: özelliği test etme

Belirli bir kullanıcı için özelliği test etmek için aşağıdaki koşulların tümünün yerinde olduğundan emin olun:
  - Kullanıcı bir kurumsal cihazda oturum açar.
  - Cihaz Active Directory etki alanına katıldı. Cihazın [Azure AD 'ye katılmış](../devices/overview.md) _olması gerekmez_ .
  - Cihazın, şirket kablolu veya kablosuz ağ ya da VPN bağlantısı gibi bir uzaktan erişim bağlantısı aracılığıyla etki alanı denetleyicinize (DC) doğrudan bağlantısı vardır.
  - Bu kullanıcıya grup ilkesi aracılığıyla [özelliği kullanıma](#step-3-roll-out-the-feature) sunulaştınız.

Kullanıcının yalnızca Kullanıcı adını girdiği ancak parolayı değil, senaryoyu test etmek için:
   - ' Üzerinde oturum açın https://myapps.microsoft.com/ . Tarayıcı önbelleğini temizlediğinizden ya da desteklenen tarayıcıların herhangi biriyle özel modda yeni bir özel tarayıcı oturumu kullandığınızdan emin olun.

Kullanıcının Kullanıcı adı veya parola girmesi gereken senaryoyu test etmek için şu adımlardan birini kullanın: 
   - `https://myapps.microsoft.com/contoso.onmicrosoft.com`Tarayıcı önbelleğini temizlediğinizden emin olmak için oturum açın ya da desteklenen tarayıcıların herhangi biriyle özel modda yeni bir özel tarayıcı oturumu kullanın. *Contoso* değerini kiracınızın adıyla değiştirin.
   - `https://myapps.microsoft.com/contoso.com`Yeni bir özel tarayıcı oturumunda oturum açın. *Contoso.com* değerini, kiracınızda doğrulanmış bir etki alanıyla (Federasyon etki alanı değil) değiştirin.

## <a name="step-5-roll-over-keys"></a>5. Adım: anahtarları atla

2. adımda, Azure AD Connect sorunsuz SSO 'yu etkinleştirdiğiniz tüm Active Directory ormanlarda bilgisayar hesapları (Azure AD 'yi temsil eder) oluşturur. Daha fazla bilgi edinmek için bkz. [Azure Active Directory kesintisiz çoklu oturum açma: teknik kapsamlı](how-to-connect-sso-how-it-works.md)bakış.

>[!IMPORTANT]
>Bir bilgisayar hesabındaki Kerberos şifre çözme anahtarı, sızmış ise, AD ormanındaki herhangi bir kullanıcı için Kerberos biletleri oluşturmak üzere kullanılabilir. Kötü amaçlı aktörler daha sonra güvenliği aşılmış kullanıcılar için Azure AD oturum açma işlemlerini taklit edebilir. Bu Kerberos şifre çözme anahtarlarını her 30 günde bir en az bir kez düzenli olarak almanızı önemle tavsiye ederiz.

Anahtarların nasıl alınacağı hakkında yönergeler için bkz. [Azure Active Directory kesintisiz çoklu oturum açma: sık sorulan sorular](how-to-connect-sso-faq.md). Anahtarları otomatik olarak almak için bir özellik üzerinde çalışıyoruz.

>[!IMPORTANT]
>Özelliği etkinleştirdikten _hemen_ sonra bu adımı uygulamanız gerekmez. Kerberos şifre çözme anahtarlarını en az 30 günde bir alın.

## <a name="next-steps"></a>Sonraki adımlar

- [Teknik derinlemesine](how-to-connect-sso-how-it-works.md)bakış: sorunsuz tek Sign-On özelliğinin nasıl çalıştığını anlayın.
- [Sık sorulan sorular](how-to-connect-sso-faq.md): sorunsuz çoklu oturum açma hakkında sık sorulan soruların yanıtlarını alın.
- [Sorun giderme](tshoot-connect-sso.md): sorunsuz tek Sign-On özelliğiyle yaygın sorunları çözmeyi öğrenin.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): yeni özellik isteklerini dosya olarak yüklemek Için Azure Active Directory forumunu kullanın.
