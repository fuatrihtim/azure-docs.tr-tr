---
title: Azure AD 'de eski kimlik doğrulama protokollerini engelleme
description: Kuruluşların eski kimlik doğrulama protokollerini nasıl ve neden engelleyeceğinizi öğrenin
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 24640254f32270b8c96c790dca7db31e285cc27f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895297"
---
# <a name="blocking-legacy-authentication"></a>Eski kimlik doğrulaması engelleniyor
 
Kullanıcılarınıza bulut uygulamalarınıza kolay erişim sağlamak için Azure Active Directory (Azure AD) eski kimlik doğrulaması dahil olmak üzere çok çeşitli kimlik doğrulama protokollerini destekler. Eski kimlik doğrulaması, tarafından yapılan bir kimlik doğrulama isteğine başvuran bir terimdir:

- Modern kimlik doğrulaması kullanmayan eski Office istemcileri (örneğin, Office 2010 istemcisi)
- IMAP/SMTP/POP3 gibi eski posta protokollerini kullanan istemciler

Günümüzde, tüm güvenliği tehlikeye alınan oturum açma girişimlerinin çoğu eski kimlik doğrulamasından geliyor. Eski kimlik doğrulaması Multi-Factor Authentication 'ı (MFA) desteklemiyor. Dizininizde bir MFA ilkesi etkinleştirilmiş olsa bile, kötü bir aktör eski bir protokolü kullanarak kimlik doğrulaması yapabilir ve MFA 'yı atlayabilir. Eski protokoller tarafından yapılan kötü amaçlı kimlik doğrulama isteklerinden hesabınızı korumanın en iyi yolu, bu denemeleri tamamen engellenemez.

## <a name="identify-legacy-authentication-use"></a>Eski kimlik doğrulama kullanımını tanımla

Dizininizde eski kimlik doğrulamasını engelleyebilmeniz için önce, kullanıcılarınızın eski kimlik doğrulaması kullanan uygulamalar olup olmadığını ve bunun genel dizininizi nasıl etkileyeceğini anlamanız gerekir. Azure AD oturum açma günlükleri, eski kimlik doğrulaması kullanıp kullandığınızı anlamak için kullanılabilir.

1. **Azure Portal**  >  **Azure Active Directory**  >  **oturum açma** işlemleri ' ne gidin.
1.  **Sütunlar** istemci uygulaması ' na tıklanarak gösterilmezse, **istemci uygulaması** sütununu ekleyin   >  ****.
1. **Istemci uygulamasına** göre filtrele > sunulan tüm **eski kimlik doğrulama istemcileri** seçeneklerini denetleyin.
1. **Durum**  >  **başarısına** göre filtreleyin. 
1. **Tarih** filtresini kullanarak gerekirse tarih aralığınızı genişletin.
1. [Yeni oturum açma etkinliği raporları önizlemesini](../reports-monitoring/concept-all-sign-ins.md)etkinleştirdiyseniz, yukarıdaki adımları **Kullanıcı oturum açma işlemleri (etkileşimli olmayan)** sekmesinde da yineleyin.

Filtreleme işlemi yalnızca seçili eski kimlik doğrulama protokolleri tarafından yapılan başarılı oturum açma girişimlerini gösterir. Her bir bireysel oturum açma girişimine tıkladığınızda ek ayrıntılar gösterilecektir. Tek bir veri satırı seçildikten sonra temel bilgiler sekmesinin altındaki Istemci uygulaması sütunu veya Istemci uygulaması alanı, hangi eski kimlik doğrulama protokolünün kullanıldığını gösterir. Bu Günlükler, hangi kullanıcıların eski kimlik doğrulamasına bağlı olduğunu ve hangi uygulamaların kimlik doğrulama isteklerini yapmak için eski protokolleri kullandığını gösterir. Bu günlüklerde görünmeyen ve eski kimlik doğrulaması kullanmayan kullanıcılar için, koşullu erişim ilkesi uygulayın veya temel ilkeyi etkinleştirin: yalnızca bu kullanıcılar için eski kimlik doğrulamasını engelle.

## <a name="moving-away-from-legacy-authentication"></a>Eski kimlik doğrulamasından uzaklaşmak 

Dizininizde eski kimlik doğrulaması kullanan kim ve bu uygulamaya bağımlı uygulamalar hakkında daha iyi bir fikir sahibi olduktan sonra, bir sonraki adım, kullanıcılarınızı modern kimlik doğrulaması kullanacak şekilde yükseltiyor. Modern kimlik doğrulaması, daha güvenli Kullanıcı kimlik doğrulaması ve yetkilendirmesi sunan bir kimlik yönetimi yöntemidir. Dizininizde bir MFA ilkeniz varsa, modern kimlik doğrulaması, kullanıcıdan gerektiğinde MFA için istemde bulunulmasını sağlar. Eski kimlik doğrulama protokollerine daha güvenli bir alternatiftir.

Bu bölüm, ortamınızı modern kimlik doğrulamaya güncelleştirme hakkında bir adım adım genel bakış sunar. Kuruluşunuzda eski bir kimlik doğrulama Engelleme ilkesini etkinleştirmeden önce aşağıdaki adımları okuyun.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>1. Adım: dizininizde modern kimlik doğrulamayı etkinleştirme

Modern kimlik doğrulamayı etkinleştirmenin ilk adımı, dizininizin modern kimlik doğrulamasını desteklediğinden emin olmanızı sağlamak. Modern kimlik doğrulaması, 1 Ağustos 2017 ' de veya sonrasında oluşturulan dizinler için varsayılan olarak etkindir. Bu tarihten önce dizininiz oluşturulduysa, aşağıdaki adımları kullanarak dizininiz için modern kimlik doğrulamayı el ile etkinleştirmeniz gerekir:

1.  `Get-CsOAuthConfiguration`   Skype Kurumsal çevrimiçi çalışma [PowerShell modülünü](/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell)çalıştırarak dizininizin modern kimlik doğrulamasını zaten destekleyip desteklemediğini denetleyin.
1. Komutunuz boş bir özellik döndürürse  `OAuthServers`   , modern kimlik doğrulaması devre dışı bırakılır. Kullanarak modern kimlik doğrulamayı etkinleştirmek için ayarı güncelleştirin  `Set-CsOAuthConfiguration` .  `OAuthServers`   Özelliği bir giriş içeriyorsa, gittiğiniz kadar iyidir.

İleri ' ye geçmeden önce bu adımı tamamladığınızdan emin olun. Tüm Office istemcileri tarafından hangi protokolün kullanılacağını dikte ettiğinden, Dizin yapılandırmalarınızın ilk önce değiştirilmesi önemlidir. Modern kimlik doğrulamasını destekleyen Office istemcileri kullanıyor olsanız bile, dizininizde modern kimlik doğrulaması devre dışıysa eski protokolleri kullanmak varsayılan olur.

### <a name="step-2-office-applications"></a>2. Adım: Office uygulamaları

Dizininizde modern kimlik doğrulamasını etkinleştirdikten sonra, Office istemcileri için modern kimlik doğrulamayı etkinleştirerek uygulamaları güncelleştirmeye başlayabilirsiniz. Office 2016 veya üzeri istemciler, varsayılan olarak modern kimlik doğrulamasını destekler. Ek adım gerekmez.

Office 2013 Windows istemcileri veya daha eski bir sürümü kullanıyorsanız, Office 2016 veya sonraki sürümlere yükseltmeniz önerilir. Dizininizde modern kimlik doğrulamasını etkinleştirmenin önceki adımını tamamladıktan sonra bile eski Office uygulamaları eski kimlik doğrulama protokollerini kullanmaya devam edecektir. Office 2013 istemcilerini kullanıyorsanız ve Office 2016 veya sonraki bir sürüme hemen yükseltirsiniz, [Windows cihazlarında office 2013 Için modern kimlik doğrulamayı etkinleştirmek](/office365/admin/security-and-compliance/enable-modern-authentication)üzere aşağıdaki makaledeki adımları izleyin. Eski kimlik doğrulaması kullanılırken hesabınızın korunmasına yardımcı olmak için, dizininiz genelinde güçlü parolalar kullanmanızı öneririz. Dizininizde zayıf parolalar sağlamak için [Azure AD parola koruması](../authentication/concept-password-ban-bad.md)' na göz atın   .

Office 2010, modern kimlik doğrulamasını desteklemez. Office 2010 ile herhangi bir kullanıcıyı Office 'in daha yeni bir sürümüne yükseltmeniz gerekir. Varsayılan olarak eski kimlik doğrulamasını engellediği için Office 2016 veya sonraki bir sürüme yükseltmeniz önerilir.

MacOS kullanıyorsanız Mac 2016 veya üzeri için Office 'e yükseltmeniz önerilir. Yerel posta istemcisi kullanıyorsanız, tüm cihazlarda macOS sürüm 10,14 veya sonraki bir sürüme sahip olmanız gerekir.

### <a name="step-3-exchange-and-sharepoint"></a>3. Adım: Exchange ve SharePoint

Windows tabanlı Outlook istemcilerinin modern kimlik doğrulamasını kullanabilmesi için Exchange Online 'ın de modern kimlik doğrulaması etkinleştirilmiş olması gerekir. Exchange Online için modern kimlik doğrulaması devre dışıysa, modern kimlik doğrulamayı (Outlook 2013 veya üzeri) destekleyen Windows tabanlı Outlook istemcileri, Exchange Online posta kutularına bağlanmak için temel kimlik doğrulamasını kullanacaktır.

SharePoint Online, modern kimlik doğrulama Varsayılanı için etkinleştirilmiştir. 1 Ağustos 2017 ' den sonra oluşturulan dizinler için, Exchange Online 'da modern kimlik doğrulaması varsayılan olarak etkindir. Ancak, daha önce modern kimlik doğrulamayı devre dışı bırakmış veya bu tarihten önce oluşturulmuş bir dizin kullanıyorsanız, [Exchange Online 'da modern kimlik doğrulamayı etkinleştirmek](/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)için aşağıdaki makaledeki adımları izleyin.

### <a name="step-4-skype-for-business"></a>4. Adım: Skype Kurumsal

Skype Kurumsal tarafından yapılan eski kimlik doğrulama isteklerini engellemek için, Skype Kurumsal Çevrimiçi için modern kimlik doğrulamayı etkinleştirmeniz gerekir. 1 Ağustos 2017 ' den sonra oluşturulan dizinler için, Skype Kurumsal için modern kimlik doğrulaması varsayılan olarak etkindir.

Varsayılan olarak modern kimlik doğrulamasını destekleyen Microsoft ekiplerine geçiş yapmanız önerilir. Ancak, şu anda geçiş yapmak istemiyorsanız, Skype Kurumsal Çevrimiçi için modern kimlik doğrulamayı etkinleştirerek Skype Kurumsal istemcilerinin modern kimlik doğrulaması kullanmaya başlamasını sağlayabilirsiniz. Skype Kurumsal için modern kimlik doğrulamayı etkinleştirmek üzere [modern kimlik doğrulamasıyla desteklenen Skype Kurumsal topolojilerinin](/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)bu makalesindeki adımları izleyin.

Skype Kurumsal Çevrimiçi için modern kimlik doğrulamayı etkinleştirmenin yanı sıra, Skype Kurumsal için modern kimlik doğrulamasını etkinleştirirken Exchange Online için modern kimlik doğrulamayı etkinleştirmenizi öneririz. Bu işlem, Exchange Online ve Skype Kurumsal Çevrimiçi ortamda modern kimlik doğrulamasının durumunu eşitlemeye yardımcı olur ve Skype Kurumsal istemcileri için birden çok oturum açma istemini engeller.

### <a name="step-5-using-mobile-devices"></a>5. Adım: mobil cihazları kullanma

Mobil cihazınızdaki uygulamaların eski kimlik doğrulamasını da engellemesi gerekir. Mobil için Outlook 'U kullanmanızı öneririz. Mobil için Outlook, modern kimlik doğrulamayı varsayılan olarak destekler ve diğer MFA temel koruma ilkelerini karşılar.

Yerel iOS posta istemcisini kullanabilmeniz için, posta istemcisinin eski kimlik doğrulamasını engelleyecek şekilde güncelleştirildiğinden emin olmak için iOS sürüm 11,0 veya üzerini çalıştırıyor olmanız gerekir.

### <a name="step-6-on-premises-clients"></a>6. Adım: şirket içi istemciler

Şirket içi Exchange Server ve şirket içi Skype Kurumsal kullanan bir hibrit müşterisiyseniz, modern kimlik doğrulamayı etkinleştirmek için her iki hizmetin de güncelleştirilmeleri gerekir. Bir karma ortamda modern kimlik doğrulaması kullanırken, hala şirket içi kullanıcıların kimliğini doğrulama işlemini sürdürmektedir. Kaynaklara (dosya veya e-posta) erişimi yetkilendirmenin öyküsü.

Şirket içinde modern kimlik doğrulamayı etkinleştirmeye başlayabilmeniz için önce lütfen önkoşulların karşılandığından emin olun. Artık şirket içinde modern kimlik doğrulamayı etkinleştirmeye hazırsınız.

Modern kimlik doğrulamasını etkinleştirme adımları aşağıdaki makalelerde bulunabilir:

* [Şirket içi Exchange Server 'ı karma modern kimlik doğrulamasını kullanacak şekilde yapılandırma](/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [Skype Kurumsal ile modern kimlik doğrulaması (ADAL) kullanma](/skypeforbusiness/manage/authentication/use-adal)

## <a name="next-steps"></a>Sonraki adımlar

- [Şirket içi Exchange Server 'ı karma modern kimlik doğrulamasını kullanacak şekilde yapılandırma](/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
- [Skype Kurumsal ile modern kimlik doğrulaması (ADAL) kullanma](/skypeforbusiness/manage/authentication/use-adal)
- [Eski kimlik doğrulamasını engelle](../conditional-access/block-legacy-authentication.md)