---
title: Eşitlemenin nasıl çalıştığı Azure AD Domain Services | Microsoft Docs
description: Eşitleme işleminin, bir Azure AD kiracısından veya şirket içi Active Directory Domain Services ortamından Azure Active Directory Domain Services yönetilen bir etki alanına yönelik nesneler ve kimlik bilgileri için nasıl çalıştığını öğrenin.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 41ba337765b4a0a93be52f08ae6656707cf7aa73
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96618816"
---
# <a name="how-objects-and-credentials-are-synchronized-in-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services yönetilen bir etki alanında nesneleri ve kimlik bilgilerini eşitleme

Azure Active Directory Domain Services (Azure AD DS) tarafından yönetilen etki alanındaki nesneler ve kimlik bilgileri, etki alanı içinde yerel olarak veya Azure Active Directory (Azure AD) kiracısından eşitlenebilir. Azure AD DS ilk kez dağıttığınızda, Azure AD 'den nesneleri çoğaltmaya yönelik otomatik tek yönlü bir eşitleme yapılandırılır ve başlatılır. Bu tek yönlü eşitleme arka planda çalışmaya devam eder ve Azure AD DS yönetilen etki alanını Azure AD 'deki değişikliklerle güncel tutar. Azure AD DS 'den Azure AD 'ye geri eşitleme gerçekleşmez.

Karma bir ortamda, şirket içi AD DS etki alanındaki nesneler ve kimlik bilgileri Azure AD Connect kullanılarak Azure AD ile eşitlenebilir. Bu nesneler Azure AD 'ye başarıyla eşitlendiğinde, otomatik arka plan eşitleme, bu nesneleri ve kimlik bilgilerini yönetilen etki alanını kullanan uygulamalar için kullanılabilir hale getirir.

Şirket içi AD DS ve Azure AD ADFS kullanılarak federal kimlik doğrulaması için yapılandırılırsa, Azure DS 'de kullanılabilir (geçerli/geçerli) Parola karması yoktur. Beslenen kimlik doğrulaması uygulanmadan önce oluşturulan Azure AD Kullanıcı hesaplarının eski bir parola karması olabilir, ancak bu durum büyük olasılıkla şirket içi parolasının bir karmasıyla eşleşmez. Bu nedenle Azure AD DS, kullanıcıların kimlik bilgilerini doğrulayamayacak.

Aşağıdaki diyagramda, eşitlemenin Azure AD DS, Azure AD ve isteğe bağlı bir şirket içi AD DS ortamı arasında nasıl çalıştığı gösterilmektedir:

![Azure AD Domain Services yönetilen bir etki alanı için eşitlemeye genel bakış](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-azure-ad-to-azure-ad-ds"></a>Azure AD 'den Azure AD DS eşitleme

Kullanıcı hesapları, grup üyelikleri ve kimlik bilgisi karmaları Azure AD 'den Azure AD DS bir şekilde eşitlenir. Bu eşitleme işlemi otomatiktir. Bu eşitleme işlemini yapılandırmanız, izlemeniz veya yönetmeniz gerekmez. İlk eşitleme, Azure AD dizinindeki nesne sayısına bağlı olarak birkaç güne kadar birkaç saat sürebilir. İlk eşitleme tamamlandıktan sonra, Azure AD 'de parola veya öznitelik değişiklikleri gibi yapılan değişiklikler otomatik olarak Azure AD DS eşitlenir.

Azure AD 'de bir kullanıcı oluşturulduğunda, Azure AD 'de parolalarını değiştirene kadar Azure AD DS eşitlenmez. Bu parola değiştirme işlemi, Kerberos ve NTLM kimlik doğrulamasının parola karmalarının Azure AD 'de oluşturulmasına ve depolanmasına neden olur. Parola karmaları, Azure AD DS 'de bir kullanıcının kimliğini başarıyla doğrulamak için gereklidir.

Eşitleme işlemi tasarım tarafından tek yönlü/tek yönlü olur. Azure AD DS 'den Azure AD 'ye geri yapılan değişikliklerin geriye doğru eşitlenmesi yoktur. Yönetilen bir etki alanı, oluşturabileceğiniz özel OU 'Lar hariç büyük ölçüde salt okunurdur. Yönetilen bir etki alanı içindeki Kullanıcı öznitelikleri, kullanıcı parolaları veya grup üyelikleri üzerinde değişiklik yapamazsınız.

## <a name="attribute-synchronization-and-mapping-to-azure-ad-ds"></a>Öznitelik eşitleme ve Azure AD DS eşleme

Aşağıdaki tabloda bazı yaygın öznitelikler ve bunların Azure AD DS nasıl eşitlendikleri listelenmektedir.

| Azure AD DS özniteliği | Kaynak | Notlar |
|:--- |:--- |:--- |
| UPN | Azure AD kiracısında kullanıcının *UPN* özniteliği | Azure AD kiracısındaki UPN özniteliği Azure AD DS olarak eşitlenir. Yönetilen bir etki alanında oturum açmak için en güvenilir yol UPN 'yi kullanmaktır. |
| Hesap | Azure AD kiracısında kullanıcının *Mailrumuz* özniteliği veya otomatik olarak oluşturulur | *SAMAccountName* ÖZNITELIĞI Azure AD kiracısındaki *mailrumuz* özniteliğinden kaynaklıdır. Birden çok kullanıcı hesabının *Mailrumuz* özniteliği varsa, *sAMAccountName* otomatik olarak oluşturulur. Kullanıcının *Mailrumuz* veya *UPN* ön eki 20 karakterden uzunsa, *sAMAccountName öznitelikleri üzerinde* 20 karakter sınırını karşılamak için *sAMAccountName* otomatik olarak oluşturulur. |
| Parolalar | Azure AD kiracısından kullanıcının parolası | NTLM veya Kerberos kimlik doğrulaması için gereken eski parola karmaları Azure AD kiracısından eşitlenir. Azure AD kiracısı Azure AD Connect kullanılarak karma eşitleme için yapılandırılırsa, bu parola karmaları şirket içi AD DS ortamından kaynaklıdır. |
| Birincil Kullanıcı/Grup SID 'SI | Otomatik olarak oluşturulan | Kullanıcı/Grup hesapları için birincil SID Azure AD DS otomatik olarak oluşturulur. Bu öznitelik, bir şirket içi AD DS ortamındaki nesnenin birincil Kullanıcı/Grup SID 'SI ile eşleşmiyor. Bu uyuşmazlık, yönetilen etki alanının şirket içi AD DS etki alanından farklı bir SID Ad alanına sahip olmasından kaynaklanır. |
| Kullanıcılar ve gruplar için SID geçmişi | Şirket içi birincil kullanıcı ve Grup SID 'SI | Azure AD DS içindeki kullanıcılar ve gruplar için *SIDHistory* özniteliği, şirket içi AD DS ortamındaki karşılık gelen birincil kullanıcı veya Grup SID 'si ile eşleşecek şekilde ayarlanır. Bu özellik, kaynakları yeniden ACL 'lere ihtiyaç duymazsanız, şirket içi uygulamaların Azure AD DS üzerinde geçiş ve kaydırma işlemlerini daha kolay hale getirmeye yardımcı olur. |

> [!TIP]
> **Yönetilen etki alanında UPN biçimini kullanarak oturum açın** Gibi *sAMAccountName* özniteliği, `AADDSCONTOSO\driley` yönetilen bir etki alanındaki bazı Kullanıcı hesapları için otomatik olarak oluşturulabilir. Kullanıcıların otomatik olarak oluşturulan *sAMAccountName* 'i UPN öneklerinden farklı olabilir, bu nedenle oturum açmak için her zaman güvenilir bir yol yoktur.
>
> Örneğin, birden çok kullanıcının aynı *Mailrumuz* özniteliği varsa veya kullanıcılar AŞıRı uzun UPN öneklerine sahip ise, bu kullanıcılara ait *sAMAccountName* otomatik olarak oluşturulabilir. `driley@aaddscontoso.com`Yönetilen bir etki alanında güvenilir bir şekilde oturum açmak için gıbı UPN biçimini kullanın.

### <a name="attribute-mapping-for-user-accounts"></a>Kullanıcı hesapları için öznitelik eşlemesi

Aşağıdaki tabloda, Azure AD 'deki Kullanıcı nesneleri için belirli özniteliklerin Azure AD DS ilgili özniteliklerle nasıl eşitlendiği gösterilmektedir.

| Azure AD 'de Kullanıcı özniteliği | Azure AD DS kullanıcı özniteliği |
|:--- |:--- |
| accountEnabled |userAccountControl (ACCOUNT_DISABLED bitini ayarlar veya temizler) |
| city |l |
| ülke |co |
| bölüm |bölüm |
| displayName |displayName |
| Employeeditıd |Çalışan |
| facsimileTelephoneNumber 'dir |facsimileTelephoneNumber 'dir |
| givenName |givenName |
| jobTitle |başlık |
| posta |posta |
| mailNickname |msDS-Azureadmailtakma ad |
| mailNickname |SAMAccountName (bazen otomatik olarak oluşturulabilir) |
| manager |manager |
| mobil |mobil |
| uzantının |msDS-Azureadobjectıd |
| onPremiseSecurityIdentifier |Sıdhistory |
| passwordPolicies |userAccountControl (DONT_EXPIRE_PASSWORD bitini ayarlar veya temizler) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| proxyAddresses | proxyAddresses |
| state |st |
| streetAddress |streetAddress |
| surname |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Gruplar için öznitelik eşlemesi

Aşağıdaki tabloda, Azure AD 'deki grup nesnelerinin belirli özniteliklerinin Azure AD DS ilgili özniteliklerle nasıl eşitlendiği gösterilmektedir.

| Azure AD 'de Grup özniteliği | Azure AD DS 'de Grup özniteliği |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (bazen otomatik olarak oluşturulabilir) |
| posta |posta |
| mailNickname |msDS-Azureadmailtakma ad |
| uzantının |msDS-Azureadobjectıd |
| onPremiseSecurityIdentifier |Sıdhistory |
| proxyAddresses | proxyAddresses |
| securityEnabled |groupType |

## <a name="synchronization-from-on-premises-ad-ds-to-azure-ad-and-azure-ad-ds"></a>Şirket içi AD DS Azure AD 'ye ve Azure AD DS eşitleme

Azure AD Connect, şirket içi AD DS ortamından Azure AD 'ye Kullanıcı hesaplarını, grup üyeliklerini ve kimlik bilgisi karmalarını eşitlemeye yönelik olarak kullanılır. UPN ve şirket içi güvenlik tanımlayıcısı (SID) gibi kullanıcı hesaplarının öznitelikleri eşitlenir. Azure AD DS kullanarak oturum açmak için, NTLM ve Kerberos kimlik doğrulaması için gereken eski parola karmaları da Azure AD 'ye eşitlenir.

> [!IMPORTANT]
> Azure AD Connect yalnızca şirket içi AD DS ortamları ile eşitleme için yüklenmeli ve yapılandırılmalıdır. Nesneleri Azure AD 'ye geri eşitlemeniz için yönetilen bir etki alanında Azure AD Connect yüklemek desteklenmez.

Geri yazma 'yı yapılandırırsanız Azure AD 'den yapılan değişiklikler şirket içi AD DS ortamına eşitlenir. Örneğin, bir Kullanıcı Azure AD self servis parola yönetimi kullanarak parolasını değiştirirse, parola şirket içi AD DS ortamında geri güncelleştirilir.

> [!NOTE]
> Bilinen tüm hatalara yönelik düzeltmelerinizi sağlamak için her zaman Azure AD Connect en son sürümünü kullanın.

### <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Çok ormanlı şirket içi ortamdan eşitleme

Birçok kuruluşun birden çok orman içeren oldukça karmaşık bir şirket içi AD DS ortamı vardır. Azure AD Connect, çok ormanlı ortamlardan Azure AD 'ye kullanıcıların, grupların ve kimlik bilgisi karmalarının eşitlenmesini destekler.

Azure AD daha basit ve düz bir ad alanına sahiptir. Kullanıcıların Azure AD tarafından güvenli hale getirilmiş uygulamalara güvenilir bir şekilde erişmesini sağlamak için, farklı ormanlardaki Kullanıcı hesapları genelinde UPN çakışmalarını çözün. Yönetilen etki alanları, Azure AD 'ye benzer bir düz OU yapısı kullanır. Şirket içinde hiyerarşik bir OU yapısını yapılandırmış olsanız bile, tüm Kullanıcı hesapları ve grupları *Aaddc kullanıcıları* kapsayıcısında depolanır. Bu, şirket içinde hiyerarşik bir OU yapısını yapılandırsanız bile, farklı şirket içi etki alanlarından veya ormanlardan eşitlenmemesine karşın. Yönetilen etki alanı tüm hiyerarşik OU yapılarını düzleştirir.

Daha önce açıklandığı gibi Azure AD DS 'den Azure AD 'ye geri eşitleme yoktur. Azure AD DS 'de [özel bir kuruluş birimi (OU)](create-ou.md) ve ardından bu özel OU 'lar içindeki kullanıcılar, gruplar veya hizmet hesapları oluşturabilirsiniz. Özel OU 'Larda oluşturulan nesnelerden hiçbiri Azure AD 'ye geri eşitlenir. Bu nesneler yalnızca yönetilen etki alanı içinde kullanılabilir ve Azure AD PowerShell cmdlet 'leri, Microsoft Graph API veya Azure AD Yönetim kullanıcı arabirimi kullanılarak görülemez.

## <a name="what-isnt-synchronized-to-azure-ad-ds"></a>Azure ile eşitlenmemiş AD DS

Aşağıdaki nesneler veya öznitelikler, şirket içi AD DS ortamından Azure AD veya Azure AD DS arasında eşitlenmez:

* **Dışlanan öznitelikler:** Azure AD Connect kullanarak belirli özniteliklerin Azure AD 'ye eşitlenmesini, şirket içi AD DS ortamından dışlamayı seçebilirsiniz. Bu Dışlanan öznitelikler daha sonra Azure AD DS 'de kullanılamaz.
* **Grup ilkeleri:** Şirket içi AD DS ortamda yapılandırılan Grup Ilkeleri Azure AD DS ile eşitlenmez.
* **SYSVOL klasörü:** Şirket içi AD DS ortamındaki *SYSVOL* klasörünün içeriği Azure AD DS ile eşitlenmez.
* **Bilgisayar nesneleri:** Şirket içi AD DS ortamına katılmış bilgisayarlar için bilgisayar nesneleri Azure AD DS ile eşitlenmez. Bu bilgisayarlar, yönetilen etki alanı ile bir güven ilişkisine sahip değildir ve yalnızca şirket içi AD DS ortamına ait değildir. Azure AD DS 'de yalnızca yönetilen etki alanına açıkça etki alanına katılmış bilgisayarlar için bilgisayar nesneleri gösterilir.
* **Kullanıcılar ve gruplar Için Sıdhistory öznitelikleri:** Şirket içi AD DS ortamından birincil kullanıcı ve birincil Grup SID 'Leri Azure AD DS ile eşitlenir. Ancak, kullanıcılar ve gruplar için mevcut *SIDHistory* öznitelikleri, şirket içi AD DS ortamından Azure AD DS ile eşitlenmez.
* **Kuruluş birimleri (OU) yapıları:** Şirket içi AD DS ortamda tanımlanan kuruluş birimleri Azure AD DS ile eşitlenmez. Azure AD DS, biri kullanıcılar ve diğeri bilgisayarlar için olmak üzere iki yerleşik OU vardır. Yönetilen etki alanının düz bir OU yapısı vardır. [Yönetilen etki alanında özel bır OU oluşturmayı](create-ou.md)tercih edebilirsiniz.

## <a name="password-hash-synchronization-and-security-considerations"></a>Parola karması eşitleme ve güvenlik konuları

Azure AD DS etkinleştirdiğinizde, NTLM + Kerberos kimlik doğrulaması için eski parola karmaları gereklidir. Azure AD, şifresiz metin parolalarını depolamaz, bu nedenle mevcut kullanıcı hesapları için bu karmalar otomatik olarak oluşturulamaz. Oluşturulup depolandıktan sonra NTLM ve Kerberos uyumlu parola karmaları her zaman Azure AD 'de şifreli bir şekilde depolanır.

Şifreleme anahtarları her bir Azure AD kiracısına özeldir. Bu karmalar, yalnızca Azure AD DS şifre çözme anahtarlarına erişime sahip olacak şekilde şifrelenir. Azure AD 'de başka bir hizmet veya bileşen şifre çözme anahtarlarına erişemez.

Eski parola karmaları daha sonra Azure AD 'den yönetilen bir etki alanı için etki alanı denetleyicilerine eşitlenir. Azure AD DS içindeki bu yönetilen etki alanı denetleyicileri için diskler, bekleyen olarak şifrelenir. Bu parola karmaları, parolaların şirket içi AD DS ortamında nasıl depolandığı ve güvenliğinin sağlandığı gibi bu etki alanı denetleyicilerinde saklanır ve güvenlik altına alınır.

Yalnızca bulut Azure AD ortamlarında, gerekli parola karmalarının Azure AD 'de oluşturulup depolanması için [Kullanıcıların parolalarını sıfırlaması/değiştirmesi gerekir](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) . Azure AD Domain Services etkinleştirildikten sonra Azure AD 'de oluşturulan herhangi bir bulut Kullanıcı hesabı için, parola karmaları oluşturulup NTLM ve Kerberos uyumlu biçimlerinde depolanır. Tüm bulut Kullanıcı hesaplarının, Azure AD DS eşitlenmeden önce parolalarını değiştirmesi gerekir.

Azure AD Connect kullanılarak şirket içi AD DS ortamından eşitlenen hibrit Kullanıcı hesapları için, [NTLM ve Kerberos uyumlu biçimlerdeki parola karmalarını eşitlenecek Azure AD Connect yapılandırmanız](tutorial-configure-password-hash-sync.md)gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Parola eşitlemesinin ayrıntıları hakkında daha fazla bilgi için bkz. [Parola karması eşitlemesi Azure AD Connect ile çalışma](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md?context=/azure/active-directory-domain-services/context/azure-ad-ds-context).

Azure AD DS kullanmaya başlamak için, [yönetilen bir etki alanı oluşturun](tutorial-create-instance.md).
