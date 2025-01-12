---
title: Uygulamalarım portalı ile ilgili yardım alın-Azure Active Directory | Microsoft Docs
description: Uygulamalarım portalında oturum açma ve ortak görevleri gerçekleştirme konusunda yardım alın.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/19/2021
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 4377ed76de971f78336ea9024b59dafc5d513487
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100094976"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>Uygulamalarım portalındaki sorunları giderme

**Uygulamalarım** portalı 'nda oturum açma veya kullanma ile ilgili sorun yaşıyorsanız yardım için yardım masasına veya yöneticinize tıklamadan önce bu sorun giderme ipuçlarını deneyin.

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Uygulamalarım güvenli oturum açma uzantısını yüklerken sorun yaşıyorum

Uygulamalarım güvenli oturum açma uzantısını yüklerken sorun yaşıyorsanız:

- Aşağıdakiler de dahil olmak üzere desteklenen bir tarayıcı kullandığınızdan emin olun:

    - **Microsoft Edge.** Windows 10 yıldönümü sürümünde veya sonraki sürümlerde çalışır.

    - **Google Chrome.** Windows 7 veya üzeri sürümlerde ve macOS X veya üzeri sürümlerde çalışır.

    - **Mozilla Firefox 26,0 veya üzeri.** Windows XP SP2 veya üzeri sürümlerde ve macOS X 10,6 veya üzeri sürümlerde çalışır.

    - **Internet Explorer 11.** Windows 7 veya sonraki sürümlerde (sınırlı destek) çalışıyor.

- Tarayıcı uzantısı ayarlarınızın açık olduğundan emin olun.

- Tarayıcınızı yeniden başlatmayı ve **uygulamalar** portalında yeniden oturum açmayı deneyin.

- Tarayıcınızın tanımlama bilgilerini temizlemeyi deneyin ve sonra yeniden başlatın ve **uygulamalar** portalında yeniden oturum açın.

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>**Uygulamalarım** portalında oturum açamıyorum

**Uygulamalarım** portalında oturum açarken sorun yaşıyorsanız aşağıdakileri deneyebilirsiniz:

- Kişisel bir Microsoft hesabı oturum açarken hata görürseniz, kuruluşunuzun etki alanı adını (örneğin contoso.com) veya aşağıdaki URL 'Lerden birindeki kuruluşunuzun **KIRACı kimliğini** kullanarak oturum açabilirsiniz:

   - https://myapplications.microsoft.com?tenantId=*your_domain_name*
   - https://myapplications.microsoft.com?tenant=*your_tenant_ID*

- Doğru URL 'YI kullandığınızdan emin olun. https://myapps.microsoft.comKuruluşunuz için veya gibi özelleştirilmiş bir sayfa olmalıdır https://myapps.microsoft.com/contoso.com .

- Parolanızın doğru olduğundan ve süresinin dolmadığından emin olun. Daha fazla bilgi için bkz. [iş veya okul parolanızı sıfırlama](active-directory-passwords-update-your-own-password.md).

- Doğrulama bilgilerinizin güncel ve doğru olduğundan emin olun. Daha fazla bilgi için bkz. [Azure AD Multi-Factor Authentication bana ne anlama geliyor?](./multi-factor-authentication-end-user-first-time.md) ya da [güvenlik bilgileri yöntemlerinizi ve bilgilerinizi değiştirme](./security-info-setup-auth-app.md).

- **Uygulama** portalı URL 'Sini **Internet özellikleri > güvenlik > güvenilen siteler** ayarına ekleyin.

- Tarayıcınızın önbelleğini temizleyin ve yeniden oturum açmayı deneyin.

## <a name="my-password-isnt-working"></a>Parolam çalışmıyor

Parolanızı unuttuysanız, kuruluşunuzda hiç bir tane almadıysanız, hesabınız dışında bir hesap yoksa veya parolanızı değiştirmek istiyorsanız yardım 'a bakın. [Azure AD Parolamı unuttum](active-directory-passwords-update-your-own-password.md).

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>Kendi parolamı sıfırlayabilmek istiyorum

Kendi parolanızı sıfırlayabilmeniz için, yöneticinizin öncelikle kuruluşunuzun özelliğini açmanız ve ardından gerekli doğrulama yöntemlerinizi güncelleştirmeniz ve doğrulamanız gerekir. Doğrulama yöntemlerinizi güncelleştirme hakkında daha fazla bilgi için bkz. [self servis parola sıfırlama Için kaydolma](active-directory-passwords-reset-register.md).

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>Bir uygulamayı başlattığımda erişim engellendi iletisi alıyorum

**Uygulama** portalından bir uygulama başlattıktan sonra **erişim reddedildi** iletisi alıyorsanız, aşağıdakileri deneyebilirsiniz:

- [Uygulamalarım güvenli oturum açma uzantısını](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) yüklediğinizden ve [desteklenen bir tarayıcı](my-apps-portal-end-user-access.md#supported-browsers)kullandığınızdan emin olun.

- Uygulamanın doğru URL 'sini kullandığınızdan ve URL 'nin **Internet özellikleri > güvenlik > güvenilen siteler** listesinde olduğundan emin olun.

- Parolanızın doğru olduğundan ve süresinin dolmadığından emin olun. Daha fazla bilgi için bkz. [iş veya okul parolanızı sıfırlama](active-directory-passwords-update-your-own-password.md).

- Doğrulama bilgilerinizin güncel ve doğru olduğundan emin olun. Daha fazla bilgi için bkz. [Azure AD Multi-Factor Authentication bana ne anlama geliyor?](./multi-factor-authentication-end-user-first-time.md) ya da [güvenlik bilgileri yöntemlerinizi ve bilgilerinizi değiştirme](./security-info-setup-auth-app.md).

- Tarayıcınızın önbelleğini temizleyin ve yeniden oturum açmayı deneyin.

Uygulamanıza hala erişemiyorsanız, yardım almak için kuruluşunuzun yardım masasına başvurmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

**Uygulamalarım** portalında oturum açtıktan sonra profil ve hesap bilgilerinizi, Grup bilgilerinizi ve erişim gözden geçirme bilgilerini (izniniz varsa) güncelleştirebilirsiniz.

- [Uygulamalarım portalındaki uygulamalara erişin ve bunları kullanın](my-apps-portal-end-user-access.md).

- [Profil bilgilerinizi değiştirin](./my-account-portal-settings.md).

- [Gruplarla ilgili bilgilerinizi görüntüleyin ve güncelleştirin](my-apps-portal-end-user-groups.md).

- [Kendi erişim incelemelerinizi gerçekleştirin](my-apps-portal-end-user-access-reviews.md).
