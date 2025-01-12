---
title: Self servis parola sıfırlamayı Azure Active Directory etkinleştirme
description: Bu öğreticide, bir Kullanıcı grubu için self servis parola sıfırlamayı Azure Active Directory etkinleştirmeyi ve parola sıfırlama işlemini test yapmayı öğreneceksiniz.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 253aa080b9c160141a274c57e0895291c78d2048
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104887776"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Öğretici: kullanıcıların Self servis parola sıfırlama Azure Active Directory kullanarak hesaplarının kilidini açma veya parolaları sıfırlamalarını sağlama

Azure Active Directory (Azure AD) self servis parola sıfırlama (SSPR), kullanıcılara yönetici veya yardım masası katılımı olmadan parolasını değiştirme veya sıfırlama yeteneği sağlar. Bir kullanıcının hesabı kilitliyse veya parolalarını unutduklarında, kendi kendilerini engellemeyi kaldırmak ve çalışmaya geri dönmek için istemleri izleyebilir. Bu özellik, bir Kullanıcı cihazlarındaki veya bir uygulamada oturum açarken yardım masası çağrılarını ve üretkenlik kaybını azaltır. [Kiracınızda self servis parola sıfırlamayı yapılandırma ve etkinleştirme](https://www.youtube.com/watch?v=rA8TvhNcCvQ) hakkında bir video aşağıda verilmiştir (**önerilir**). Ayrıca [, SSPR ile en yaygın altı Son Kullanıcı hata iletilerini çözümlemeden](https://www.youtube.com/watch?v=9RPrNVLzT8I)BT yöneticileri için bir video sunuyoruz.

> [!IMPORTANT]
> Bu öğreticide, self servis parola sıfırlamayı etkinleştirme Yöneticisi gösterilmektedir. Self servis parola sıfırlama için zaten kayıtlı bir son kullanıcı varsa ve hesabınıza geri dönmek istiyorsanız adresine gidin https://aka.ms/sspr .
>
> BT ekibiniz kendi parolanızı sıfırlama özelliğini etkinleştirmediyseniz, ek yardım için yardım masasına ulaşın.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Bir Azure AD kullanıcısı grubu için self servis parola sıfırlamayı etkinleştirme
> * Kimlik doğrulama yöntemlerini ve kayıt seçeneklerini yapılandırma
> * SSPR işlemini Kullanıcı olarak test etme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* En az bir Azure AD Ücretsiz veya deneme lisansı etkin çalışan bir Azure AD kiracısı. Ücretsiz katmanda SSPR yalnızca Azure AD 'deki bulut kullanıcıları için geçerlidir.
    * Bu serideki sonraki öğreticiler için, şirket içi parola geri yazma için Azure AD Premium P1 veya deneme lisansı gerekir.
    * Gerekirse, [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* *Genel yönetici* ayrıcalıklarına sahip bir hesap.
* Yönetici olmayan ve Kullanıcı tarafından bildiğiniz, *testuser* gibi bir parola. Son Kullanıcı SSPR deneyimini Bu öğreticide bu hesabı kullanarak test edersiniz.
    * Bir kullanıcı oluşturmanız gerekiyorsa bkz. [hızlı başlangıç: Azure Active Directory yeni kullanıcı ekleme](../fundamentals/add-users-azure-active-directory.md).
* Yönetici olmayan kullanıcının *SSPR-test-Group* gibi üyesi olduğu bir grup. Bu öğreticide bu grup için SSPR 'yi etkinleştirirsiniz.
    * Bir grup oluşturmanız gerekiyorsa bkz. [Grup oluşturma ve Azure Active Directory üye ekleme](../fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Kendi kendine parola sıfırlamayı etkinleştirme

Azure AD, SSPR 'yi *hiçbiri*, *Seçili* veya *Tüm* kullanıcılar için etkinleştirmenizi sağlar. Bu ayrıntılı özellik, SSPR kayıt işlemini ve iş akışını test etmek için kullanıcıların bir alt kümesini seçmenize olanak sağlar. İşlemi rahat hale getiriyorsanız ve gereksinimleri daha geniş bir Kullanıcı kümesiyle iletişim kurabiliyorsanız, SSPR için etkinleştirilecek kullanıcı grubunu seçebilirsiniz. Ya da, SSPR 'yi Azure AD kiracısındaki herkes için etkinleştirebilirsiniz.

> [!NOTE]
>
> Şu anda yalnızca bir Azure AD grubu Azure portal kullanılarak SSPR için etkinleştirilebilir. SSPR 'nin daha geniş dağıtımının bir parçası olarak, iç içe gruplar desteklenir. Seçtiğiniz gruptaki (ler) kullanıcılara uygun lisansların atandığından emin olun. Şu anda bu lisans gereksinimlerinden herhangi bir doğrulama işlemi yoktur.

Bu öğreticide, bir test grubundaki bir kullanıcı kümesi için SSPR 'yi yapılandırın. Aşağıdaki örnekte, *SSPR-test-Group* grubu kullanılır. Gerektiğinde kendi Azure AD grubunuzu sağlayın:

1. *Genel yönetici* izinlerine sahip bir hesap kullanarak [Azure Portal](https://portal.azure.com) oturum açın.
1. **Azure Active Directory** bulun ve seçin ve ardından sol taraftaki menüden **parola sıfırlama** ' yı seçin.
1. **Özellikler** sayfasında, *self servis parola sıfırlama etkin* seçeneği altında **Grup Seç** ' i seçin.
1. *SSPR-test-Group* gıbı Azure AD grubunuza gözatıp seçin ve ardından *Seç*' i seçin.

    [![Self servis parola sıfırlama ](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png) için etkinleştirmek üzere Azure Portal bir grup seçin](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

1. Select kullanıcıları için SSPR 'yi etkinleştirmek üzere **Kaydet**' i seçin.

## <a name="select-authentication-methods-and-registration-options"></a>Kimlik doğrulama yöntemlerini ve kayıt seçeneklerini belirleyin

Kullanıcıların hesaplarının kilidini açmak veya parolalarını sıfırlaması gerektiğinde, bunlara ek bir onay yöntemi sorulur. Bu ek kimlik doğrulama faktörü yalnızca onaylanan SSPR olaylarının tamamlandığından emin olmanızı sağlar. Kullanıcının sağladığı kayıt bilgilerine göre hangi kimlik doğrulama yöntemlerinin izin verdiğini seçebilirsiniz.

1. Sol taraftaki menüden **kimlik doğrulama yöntemleri** sayfasında, **sıfırlamak Için gereken yöntem sayısını** *1* olarak ayarlayın.

    Güvenliği artırmak için SSPR için gereken kimlik doğrulama yöntemlerinin sayısını artırabilirsiniz.

1. Kuruluşunuzun izin vermek istediği **kullanıcılara sunulan yöntemleri** seçin. Bu öğretici için aşağıdaki yöntemleri etkinleştirmek üzere kutulara göz atın:

    * *Mobil uygulama bildirimi*
    * *Mobil uygulama kodu*
    * *E-posta*
    * *Cep telefonu*

    *Ofis telefonu* veya *güvenlik soruları* gibi ek kimlik doğrulama yöntemleri, iş gereksinimlerinize uyacak şekilde etkinleştirilebilir.

1. Kimlik doğrulama yöntemlerini uygulamak için **Kaydet**' i seçin.

Kullanıcıların hesaplarının kilidini açmak veya bir parolayı sıfırlayabilmeleri için, bunların iletişim bilgilerini kaydetmesi gerekir. Bu iletişim bilgileri, önceki adımlarda yapılandırılmış farklı kimlik doğrulama yöntemleri için kullanılır.

Bir yönetici bu iletişim bilgilerini el ile sağlayabilir veya kullanıcıların bilgileri sağlaması için bir kayıt portalına gidebilir. Bu öğreticide, kullanıcıları bir sonraki oturum açtıklarında kayıt yapması istenecek şekilde yapılandırın.

1. Sol taraftaki menüdeki **kayıt** sayfasında, **kullanıcıların oturum açarken kaydolmasını gerektir** için *Evet* ' i seçin.
1. İletişim bilgilerinin güncel tutulması önemlidir. Bir SSPR olayı başlatıldığında iletişim bilgileri güncel değilse, Kullanıcı hesabının kilidini açmayabilir veya parolalarını sıfırlayamayabilir.

    **Kullanıcıların kimlik doğrulaması bilgilerini yeniden onaylamasını istemeden önce geçen gün sayısı** ayarını *180* olarak belirleyin.
1. Kayıt ayarlarını uygulamak için **Kaydet**' i seçin.

## <a name="configure-notifications-and-customizations"></a>Bildirimleri ve özelleştirmeleri yapılandırma

Kullanıcılara hesap etkinlikleri hakkında bilgi sahibi olmak için, bir SSPR olayı gerçekleştiğinde e-posta bildirimleri gönderilmesini yapılandırabilirsiniz. Bu bildirimler, hem normal kullanıcı hesaplarını hem de yönetici hesaplarını kapsayabilir. Yönetici hesaplarında, bu bildirim, bir ayrıcalıklı yönetici hesabı parolası SSPR kullanılarak sıfırlandığında ek bir tanıma katmanı sağlar. Bir yönetici hesabında SSPR kullanıldığında tüm genel yöneticilere bildirim gönderilir.

1. Sol taraftaki menüden **Bildirimler** sayfasında, aşağıdaki seçenekleri yapılandırın:

   * **Parola sıfırlamayı kullanıcılara bildirme** seçeneğini *Evet* olarak ayarlayın.
   * **Diğer yöneticiler parolalarını sıfırladığında tüm yöneticilere bildirme** seçeneğini *Evet* olarak ayarlayın.

1. Bildirim tercihlerini uygulamak için **Kaydet**' i seçin.

Kullanıcıların SSPR işlemiyle ilgili ek yardıma ihtiyacı varsa, "yöneticinize başvurun" bağlantısını özelleştirebilirsiniz. Bu bağlantı SSPR kayıt işleminde ve Kullanıcı hesaplarının kilidini açtığında veya parolalarını sıfırladığında kullanılır. Kullanıcılarınızın gerekli desteği almasını sağlamak için, özel bir yardım masası e-postası veya URL 'SI sağlamanız önemle önerilir.

1. Sol taraftaki menüden **Özelleştirme** sayfasında, *Özelleştir yardım masası* ' nı **Evet** olarak ayarlayın.
1. **Özel yardım masası e-postası veya URL 'si** alanında, kullanıcılarınızın kuruluşunuza ait Ek yardım alabileceğiniz bir e-posta adresi veya Web sayfası URL 'si sağlayın (örneğin,*`https://support.contoso.com/`*
1. Özel bağlantıyı uygulamak için **Kaydet**' i seçin.

## <a name="test-self-service-password-reset"></a>Self servis parola sıfırlamayı test etme

SSPR 'yi etkin ve yapılandırılmış olarak, SSPR sürecini *Test-SSPR-Group* gibi önceki bölümde seçtiğiniz grubun parçası olan bir kullanıcıyla test edin. Aşağıdaki örnekte, *testuser* hesabı kullanılır. Bu öğreticinin ilk bölümünde SSPR için etkinleştirdiğiniz grubun parçası olan kendi kullanıcı hesabınızı sağlayın.

> [!NOTE]
> Self servis parola sıfırlamayı test ettiğinizde yönetici olmayan bir hesap kullanın. Varsayılan olarak, Yöneticiler Self servis parola sıfırlama için etkinleştirilmiştir ve parolasını sıfırlamak için iki kimlik doğrulama yöntemi kullanmak zorundadır. Daha fazla bilgi için bkz. [yönetici sıfırlama ilkesi farklılıkları](concept-sspr-policy.md#administrator-reset-policy-differences).

1. El ile kayıt işlemini görmek için, InPrivate veya ınbilito modunda yeni bir tarayıcı penceresi açın ve konumuna gidin [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) . Kullanıcılar bir sonraki oturum açtıklarında bu kayıt portalına yönlendirilmelidir.
1. *Testuser* gibi yönetici olmayan bir test kullanıcısı ile oturum açın ve kimlik doğrulama yöntemlerinin iletişim bilgilerini kaydedin.
1. Tamamlandıktan **sonra, işaretlenmiş düğmeyi** seçin ve tarayıcı penceresini kapatın.
1. InPrivate veya ınbilito modunda yeni bir tarayıcı penceresi açın ve konumuna gidin [https://aka.ms/sspr](https://aka.ms/sspr) .
1. Yönetici olmayan test kullanıcılarınızın hesap bilgilerini ( *testuser* gibi), CAPTCHA ' dan karakterler ' i girin ve ardından **İleri**' yi seçin.

    ![Parolayı sıfırlamak için Kullanıcı hesabı bilgilerini girin](media/tutorial-enable-sspr/password-reset-page.png)

1. Parolanızı sıfırlamak için doğrulama adımlarını izleyin. Bu tamamlandığında, parolanızın sıfırlandığını belirten bir e-posta bildirimi almalısınız.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu serideki aşağıdaki öğreticide, parola geri yazma özelliğini yapılandırırsınız. Bu özellik, Azure AD SSPR 'den şirket içi AD ortamına kadar olan parola değişikliklerini yazar. Parola geri yazma özelliğini yapılandırmak için bu öğretici serisine devam etmek istiyorsanız, SSPR 'yi şimdi devre dışı bırakın.

Bu öğreticinin bir parçası olarak yapılandırdığınız SSPR işlevini artık kullanmak istemiyorsanız, aşağıdaki adımları uygulayarak SSPR durumunu **none** olarak ayarlayın:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. **Azure Active Directory** bulun ve seçin ve ardından sol taraftaki menüden **parola sıfırlama** ' yı seçin.
1. **Özellikler** sayfasında, *self servis parola sıfırlama etkin* seçeneği altında **hiçbiri**' ni seçin.
1. SSPR değişikliğini uygulamak için **Kaydet**' i seçin.

## <a name="faqs"></a>SSS

Bu bölümde, SSPR 'yi deneyen Yöneticiler ve son kullanıcılar hakkında sık sorulan sorular açıklanmaktadır:

- Şirket içinden eşitlenen parolaları kullanabilmeniz için, bir **parolanızın sıfırlandıktan** sonra, Federasyon kullanıcıları neden 2 dakikaya kadar bekler?

  Parolaları eşitlenen Federasyon kullanıcıları için, parolalar için yetki kaynağı şirket içinde olur. Sonuç olarak, SSPR yalnızca şirket içi parolaları güncelleştirir. Azure AD 'ye geri Parola karması eşitlemesi, her 2 dakikada bir zamanlanır.

- Telefon ve e-posta gibi SSPR verileriyle önceden doldurulan yeni oluşturulan bir Kullanıcı, SSPR kayıt sayfasını ziyaret ettiğinde **hesabınıza erişimi kaybetmeyin!** sayfanın başlığı olarak görünür. SSPR verileri önceden doldurulmadığı diğer kullanıcılar neden iletiyi görmez?

  ' İ görebilen Kullanıcı **hesabınıza erişimi kaybetmez!** , kiracı için yapılandırılmış SSPR/Birleşik kayıt gruplarının üyesidir. Görmediği kullanıcılar **hesabınıza erişimi kaybetmez!** SSPR/Birleşik kayıt gruplarının bir parçası değildi.

- Bazı kullanıcılar SSPR işlemini ilerlerse ve parolalarını sıfırlarsa, neden parola gücü göstergesi görmez?

  Zayıf/güçlü parola gücünü görmeyen kullanıcılar eşitlenmiş parola geri yazma özelliği etkinleştirilmiştir. SSPR, müşterinin Şirket içi ortamının parola ilkesini belirleyelemediğinden, parola gücünü veya zayıf düzeyini doğrulayamaz. 

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, seçili bir Kullanıcı grubu için Azure AD self servis parola sıfırlamayı etkinleştirdiniz. Şunları öğrendiniz:

> [!div class="checklist"]
> * Bir Azure AD kullanıcısı grubu için self servis parola sıfırlamayı etkinleştirme
> * Kimlik doğrulama yöntemlerini ve kayıt seçeneklerini yapılandırma
> * SSPR işlemini Kullanıcı olarak test etme

> [!div class="nextstepaction"]
> [Azure AD Multi-Factor Authentication’ı etkinleştirme](./tutorial-enable-azure-mfa.md)
