---
title: Azure geçişi için Azure Güvenlik temeli
description: Azure geçişi güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e10413987a12b32a5f1ca507aa272bab01b20797
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101094818"
---
# <a name="azure-security-baseline-for-azure-migrate"></a>Azure geçişi için Azure Güvenlik temeli

Bu güvenlik temeli, [Azure Güvenlik kıyaslama sürümü 1,0](../security/benchmarks/overview-v1.md) ' dan Azure geçişi 'ne kılavuzluk uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar.
İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve Azure geçişi için geçerli olan ilgili kılavuza göre gruplandırılır. Azure geçişi için geçerli olmayan **denetimler** dışlandı.

 
Azure 'un Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, [tam Azure geçişi güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="identity-management"></a>Kimlik Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Kimlik Yönetimi](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Azure Active Directory'yi standart merkezi kimlik ve kimlik doğrulaması sistemi haline getirin

**Kılavuz**: Azure geçişi, varsayılan kimlik ve erişim yönetimi hizmeti olarak Azure Active Directory (Azure AD) kullanır. Kuruluşunuzun kimlik ve erişim yönetimini yönetmek için Azure AD 'yi standartlaşmanız gerekir:

- Azure portal, Azure depolama, Azure sanal makinesi (Linux ve Windows), Azure Key Vault, PaaS ve SaaS uygulamaları gibi Microsoft Bulut kaynaklar.

- Kuruluşunuzun Azure veya kurumsal ağ kaynaklarınızın uygulamaları gibi kaynakları.

Azure AD'nin güvenliğini sağlamak, kuruluşunuzun güvenlik uygulamalarının yüksek öncelikli bileşenlerinden biri olmalıdır. Azure AD, kimlik güvenliği duruşunu Microsoft'un en iyi deneyim önerilerine göre değerlendirmenize yardımcı olmak için bir kimlik güvenliği puanı sağlar. Bu puanı kullanarak yapılandırmanızın en iyi deneyim önerileriyle ne kadar uyumlu olduğunu görebilir ve güvenlik duruşunuzda geliştirmeler yapabilirsiniz.

Azure AD, Microsoft hesabı olmayan kullanıcıların kendi uygulamalarına ve kaynaklarına kendi dış kimlik bilgileriyle oturum açmasına olanak tanıyan dış kimliği destekler.

- [Azure Active Directory'deki kiracılar](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Azure AD örneği oluşturma ve yapılandırma](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Uygulama için dış kimlik sağlayıcılarını kullanma](../active-directory/external-identities/identity-providers.md) 

- [Azure Active Directory'deki kimlik güvenliği puanı nedir?](../active-directory/fundamentals/identity-secure-score.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Uygulama erişimi için Azure AD çoklu oturum açma (SSO) özelliğini kullanın

**Rehberlik**: Azure geçişi, Azure kaynaklarına, bulut uygulamalarına ve şirket içi uygulamalara kimlik ve erişim yönetimi sağlamak için Azure Active Directory kullanır. Buna çalışanlar gibi kuruluş kimliklerinin yanı sıra iş ortakları, satıcılar ve tedarikçiler gibi dış kimlikler de dahildir. Bu seçenek, kuruluşunuzun şirket içi ortamda ve bulutta bulunan verilerini ve kaynaklarını yönetip bunlara güvenli erişim sağlamak için çoklu oturum açma (SSO) özelliğinin kullanılmasını sağlar. Tüm kullanıcılarınızı, uygulamalarınızı ve cihazlarınızı Azure AD'ye bağlayarak sorunsuz ve güvenli erişimin yanı sıra daha fazla görünürlük ve denetim elde edebilirsiniz.

- [Azure AD ile uygulamalar için SSO yaklaşımını anlama](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Tüm Azure Active Directory tabanlı erişim girişimleri için güçlü kimlik doğrulaması denetimleri kullanın

**Kılavuz**: Azure geçişi, çok faktörlü kimlik doğrulaması aracılığıyla güçlü kimlik doğrulama denetimlerini destekleyen Azure Active Directory kullanır ve güçlü parolasız yöntemler kullanır.

Çok faktörlü Authentication-Azure AD çok faktörlü kimlik doğrulamasını etkinleştirin ve çok faktörlü kimlik doğrulama kuruluminizdeki bazı en iyi uygulamalar için Azure Güvenlik Merkezi kimlik ve erişim yönetimi önerilerini izleyin. Çok faktörlü kimlik doğrulaması, tüm kullanıcılara veya oturum açma koşullarını ve risk etmenlerine göre Kullanıcı başına düzeyinde uygulanabilir.

Parolasız kimlik doğrulaması: Üç farklı parolasız kimlik doğrulaması seçeneği vardır: İş İçin Windows Hello, Microsoft Authenticator uygulaması ve akıllı kart gibi şirket içi kimlik doğrulama yöntemleri.

Yönetici ve ayrıcalıklı kullanıcılar için, güçlü kimlik doğrulama yönteminin en yüksek düzeyinin kullanıldığından emin olun ve ardından uygun güçlü kimlik doğrulama ilkesini diğer kullanıcılara teslim edin.

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Active Directory için parolasız kimlik doğrulaması seçeneklerine giriş](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Azure AD varsayılan parola ilkesi](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts) 

- [Azure AD Parola Koruması kullanarak hatalı parolaları ortadan kaldırma](../active-directory/authentication/concept-password-ban-bad.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Hesap anomalilerini izleyin ve uyarı oluşturun

**Kılavuz**: Azure geçişi, aşağıdaki veri kaynaklarını sağlayan Azure Active Directory ile tümleşiktir:

- Oturum açma bilgileri - Oturum açma bilgileri raporu yönetilen uygulamaların kullanımı ve kullanıcıların oturum açma etkinlikleri hakkında bilgi sağlar.

- Denetim günlükleri - Azure AD içindeki çeşitli özellikler tarafından yapılan tüm değişiklikler için günlükler aracılığıyla izlenebilirlik sağlar. Azure AD içindeki herhangi bir kaynakta yapılan kullanıcı, uygulama, grup, rol ve ilkeleri ekleme veya kaldırma işlemleri gibi değişiklikler, denetim günlüklerine örnek gösterilebilir.

- Riskli oturum açma işlemleri-riskli oturum açma, bir kullanıcı hesabının meşru sahibi olmayan birisi tarafından gerçekleştirilmiş olabilecek oturum açma girişimine yönelik bir göstergedir.

- Riskli oldukları belirlenen kullanıcılar - Riskli kullanıcı, güvenliği tehlikeye girmiş olabilecek bir kullanıcı hesabının göstergesidir.

Bu veri kaynakları Azure Izleyici, Azure Sentinel veya üçüncü taraf SıEM sistemleriyle tümleştirilebilir.

Azure Güvenlik Merkezi aşırı fazla sayıda başarısız kimlik doğrulaması girişimi, abonelikte kullanım dışı kalan hesaplar gibi bazı şüpheli etkinliklerde de uyarı verebilir.

Azure Gelişmiş Tehdit Koruması (ATP) gelişmiş tehditleri, risk altındaki kimlikleri ve içeriden kötü amaçlı eylemleri belirlemek, algılamak ve araştırmak için Active Directory sinyallerini kullanabilen bir güvenlik çözümüdür.

- [Azure Active Directory’de denetim etkinliği raporları](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure AD riskli oturum açma işlemlerini görüntüleme](../active-directory/identity-protection/overview-identity-protection.md) 

- [Riskli etkinlik bayrağıyla işaretlenen Azure AD kullanıcılarını belirleme](../active-directory/identity-protection/overview-identity-protection.md) 

- [Azure Güvenlik Merkezi’nde kullanıcıların kimliğini ve erişim etkinliğini izleme](../security-center/security-center-identity-access.md) 

- [Azure Güvenlik Merkezi’nin tehdit analizi koruma modülündeki uyarılar](../security-center/alerts-reference.md) 

- [Azure Etkinlik Günlüklerini Azure İzleyici ile tümleştirme](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

## <a name="privileged-access"></a>Ayrıcalıklı Erişim

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ayrıcalıklı Erişim](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: İş açısından kritik sistemlere yönetici erişimini kısıtlayın

**Kılavuz**: Azure geçişi, iş açısından kritik sistemlere erişimi yalıtmak IÇIN Azure RBAC kullanır. bu hesaplar, içinde bulundukları abonelikler ve yönetim gruplarına ayrıcalıklı erişim izni verildiğini kısıtlayarak.

Active Directory Etki Alanı Denetleyicileri (DC 'Ler), güvenlik araçları ve iş açısından kritik sistemlerde yüklü aracılarla sistem yönetimi araçları gibi iş açısından kritik erişimlerinize yönetim erişimi olan yönetim, kimlik ve güvenlik sistemlerine erişimi de kısıtlatığınızdan emin olun. Bu yönetim ve güvenlik sistemlerine zarar veren saldırganlar, iş açısından kritik varlıkların güvenliğini tehlikeye atabilir.

Tutarlı erişim denetimi sağlamak için, tüm erişim denetimi türleri kurumsal segmentasyon stratejinize hizalanmalıdır.

- [Azure bileşenleri ve başvuru modeli](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Yönetim grubu erişimi](../governance/management-groups/overview.md#management-group-access) 

- [Azure abonelik yöneticileri](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Kullanıcı erişimini düzenli olarak gözden geçirin ve mutabık kılın

**Kılavuz**: Azure geçişi, kaynaklarını yönetmek için Azure Active Directory hesapları kullanır, hesapların ve erişiminin geçerli olduğundan emin olmak için Kullanıcı hesaplarını ve erişim atamasını düzenli olarak gözden geçirin. Azure AD erişim incelemelerini, grup üyeliklerini ve kurumsal uygulamalara erişimi ve rol atamalarını gözden geçirmek için kullanabilirsiniz. Azure AD raporlama, eski hesapların keşfedilmesine yardımcı olmak için Günlükler sağlayabilir. Gözden geçirme sürecini kolaylaştırmak için, erişim gözden geçirme raporu iş akışı oluşturmak için Azure AD Privileged Identity Management de kullanabilirsiniz.

Ayrıca, Azure Privileged Identity Management, çok fazla sayıda yönetici hesabı oluşturulduğunda ve eski veya yanlış yapılandırılmış yönetici hesaplarını belirlemek için de yapılandırılabilir.

Bazı Azure Hizmetleri, Azure AD aracılığıyla yönetilmeyen yerel kullanıcıları ve rolleri destekler. Bu kullanıcıları ayrı ayrı yönetmeniz gerekir.

- [Privileged Identity Management (PıM) içinde Azure Kaynak rolleri için erişim gözden geçirmesi oluşturma](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Azure AD kimlik ve erişim gözden geçirmelerini kullanma](../active-directory/governance/access-reviews-overview.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Azure AD'de acil durum erişimini ayarlayın

**Kılavuz**: Azure geçişi, kaynaklarını yönetmek için Azure Active Directory kullanır. Yanlışlıkla Azure AD kuruluşunuzun dışına kilitlenmesini engellemek için, normal yönetim hesapları kullanılmazsa erişim için bir acil durum erişim hesabı ayarlayın. Acil durum erişim hesapları genellikle yüksek ayrıcalığa sahiptir ve herhangi bir kişiye atanmamalıdır. Acil durum erişim hesaplarının kullanım alanı, normal yönetici hesaplarının kullanılamadığı acil veya "camı kırın" senaryolarıyla sınırlıdır.

Acil durum erişim hesaplarının kimlik bilgilerinin (parola, sertifika veya akıllı kart) güvenli bir şekilde saklandığından ve yalnızca acil bir durumda bunları kullanma yetkisine sahip olan kullanıcılar tarafından bilindiğinden emin olmanız gerekir.

- [Azure AD'deki acil durum erişim hesaplarını yönetme](../active-directory/roles/security-emergency-access.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="pa-5-automate-entitlement-management"></a>PA-5: yetkilendirme yönetimini otomatikleştirme 

**Rehberlik**: Azure geçişi, kaynaklarını yönetmek için Azure Active Directory ile tümleşiktir. Erişim atamaları, incelemeler ve süre sonu dahil olmak üzere, erişim isteği iş akışlarını otomatikleştirmek için Azure AD yetkilendirme yönetimi özelliklerini kullanın. Çift veya çok aşamalı onay de desteklenir.

- [Azure AD erişim gözden geçirmeleri nelerdir?](../active-directory/governance/access-reviews-overview.md) 
- [Azure AD yetkilendirme yönetimi nedir?](../active-directory/governance/entitlement-management-overview.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Ayrıcalıklı erişim iş istasyonlarını kullanın

**Rehberlik**: Güvenli ve yalıtılmış iş istasyonları yöneticiler, geliştiriciler ve kritik hizmet operatörleri gibi hassas rollerin güvenliği açısından kritik öneme sahiptir. Yönetim görevleri için yüksek düzeyde güvenli Kullanıcı iş istasyonları ve/veya Azure savunma kullanın. Yönetim görevlerine yönelik güvenli ve yönetilen bir kullanıcı iş istasyonu dağıtmak için Azure Active Directory, Microsoft Defender Gelişmiş Tehdit Koruması (ATP) ve/veya Microsoft Intune hizmetlerini kullanın. Güvenli iş istasyonları güçlü kimlik doğrulaması, yazılım ve donanım temelleri, kısıtlı mantıksal erişim ve ağ erişimi gibi güvenli yapılandırma özelliklerinin uygulanması için merkezi olarak yönetilebilir.

- [Ayrıcalıklı erişim iş istasyonlarını anlama](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Ayrıcalıklı erişim iş istasyonu dağıtma](/security/compass/privileged-access-deployment)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>AE-7: Tam yetecek kadar yönetim uygulama (en düşük ayrıcalık ilkesi) 

**Kılavuz**: Azure geçişi, kaynaklarını yönetmek için Azure rol tabanlı erişim denetımı (RBAC) ile tümleşiktir. Azure RBAC ağ atamaları aracılığıyla Azure kaynak erişimini yönetmenize olanak tanır. Bu rolleri kullanıcılara atayabilir, hizmet sorumlularını ve yönetilen kimlikleri gruplara atayabilirsiniz. Belirli kaynaklar için önceden tanımlanmış yerleşik roller vardır ve Azure CLI, Azure PowerShell veya Azure portal gibi araçlardan yararlanarak bu rollerin envanteri çıkarılabilir ve bunlar sorgulanabilir. Azure RBAC aracılığıyla kaynaklara atadığınız ayrıcalıklar her zaman yalnızca söz konusu rollere gerekenlerle sınırlanmalıdır. Bu yöntem Azure AD Privileged Identity Management’ın (PIM) tam zamanında (JIT) yaklaşımını tamamlayıcı niteliktedir ve düzenli aralıklarla gözden geçirilmelidir.

Yerleşik rolleri kullanarak izin verin ve yalnızca gerektiğinde özel rol oluşturun.

- [Azure rol tabanlı erişim denetimi nedir (Azure RBAC)](../role-based-access-control/overview.md)

- [Azure RBAC 'yi yapılandırma](../role-based-access-control/role-assignments-portal.md) 

- [Azure AD kimlik ve erişim gözden geçirmelerini kullanma](../active-directory/governance/access-reviews-overview.md)

**Azure Güvenlik Merkezi izlemesi**: Uygulanamaz

**Sorumluluk**: Müşteri

## <a name="data-protection"></a>Veri Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Veri Koruma](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Hassas verileri koruyun

**Kılavuz**: Azure geçişi 'Ndeki sunucu geçiş aracı, Azure aboneliğinizdeki depolama hesaplarına ve yönetilen disklere geçirilmekte olan sunucuların disklerinden verileri çoğaltır. Veri işleme, aboneliğin depolama hesaplarına veya yönetilen disklere yazıldığı ve Azure geçişi 'nde kalıcı olmadığı kadar geçicidir.

Azure rol tabanlı Access Control (Azure RBAC) ve ağ tabanlı erişim denetimleri (yönetilen diskler için Depolama Hizmeti Şifrelemesi gibi) kullanarak erişimi kısıtlayarak depolama hesaplarında ve yönetilen disklerde depolanan hassas verileri koruyun. 

Microsoft tarafından yönetilen temel platform açısından, Microsoft tüm müşteri içeriğini hassas olarak değerlendirir ve müşteri verilerinin kaybına ve açığa çıkmasına karşı koruma sağlar. Azure içindeki müşteri verilerinin güvenlik altında kalmasını sağlamak için Microsoft bazı varsayılan veri koruma denetimleri ve özellikleri uygulamıştır.

- [Azure Rol Tabanlı Erişim Denetimi (RBAC)](../role-based-access-control/overview.md) 

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Azure Güvenlik Merkezi izlemesi**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Bekleyen hassas verileri şifreleyin

**Kılavuz**: Azure geçişi, hassas verileri kalıcı olarak tutmaz. Azure geçişi 'nde kalıcı olan tüm veriler, Microsoft tarafından yönetilen anahtarlarla Rest 'te şifrelenir.

Azure geçişi 'ndeki sunucu geçiş aracı, Azure aboneliğinizdeki depolama hesaplarına ve yönetilen disklere geçirilmekte olan sunucuların disklerinden verileri çoğaltır. Diskler hassas veriler içerebilir veya içermeyebilir. Veri işleme, aboneliğin depolama hesaplarına veya yönetilen disklere yazıldığı kadar geçicidir ve Azure geçişi 'nde kalıcı değildir. Depolama hesabı ve yönetilen diskler üzerindeki çoğaltılan veriler, Microsoft tarafından yönetilen anahtarlarla Rest 'te şifrelenir. Son derece hassas veriler için, depolama hesabında ve yönetilen disklerde müşteri tarafından yönetilen anahtarlarla, bekleyen ek şifreleme uygulama seçenekleriniz vardır.

- [Sunucu tarafı şifreleme (SSE) ve müşteri tarafından yönetilen anahtarlarla VMware sanal makinelerini Azure 'a geçirme (CMK)](how-to-migrate-vmware-vms-with-cmk-disks.md)

- [Müşteri tarafından yönetilen anahtarlarla depolama hesabı şifrelemesini yapılandırma](../storage/common/customer-managed-keys-configure-key-vault.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Paylaşılan

## <a name="asset-management"></a>Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Varlık Yönetimi](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>VY-1: Güvenlik ekibinin varlıklara yönelik risklerde görünürlük elde etmesini sağlama

**Rehber**: Güvenlik ekiplerinin Azure Güvenlik Merkezi’ni kullanarak güvenlik risklerini izlemesini sağlamak için bu ekiplere Azure kiracınız ve abonelikleriniz üzerinde Güvenlik Okuyucusu izinleri verildiğinden emin olun. 

Güvenlik ekibi sorumluluklarının yapılandırmasına bağlı olarak, güvenlik risklerinin izlenmesi merkezi güvenlik ekibinin veya yerel bir ekibin sorumluluğunda olabilir. Gerçi güvenlik içgörüleri ve risklerinin kuruluş içinde her zaman merkezi bir konumda toplanması gerekir. 

Güvenlik Okuyucusu izinleri kiracının tamamına (Kök Yönetim Grubu) geniş kapsamlı olarak uygulanabileceği gibi, izinlerin kapsamı yönetim gruplarıyla veya belirli aboneliklerle de sınırlanabilir. 

İş yüklerinin ve hizmetlerin görünürlüğünü almak için ek izinler gerekebilir. 

- [Güvenlik Okuyucusu Rolüne Genel Bakış](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure Yönetim Gruplarına Genel Bakış](../governance/management-groups/overview.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Güvenlik ekibinin varlık envanterine ve meta verilerine erişebildiğinden emin olun

**Rehberlik**: Azure kaynaklarınıza, kaynak gruplarına ve aboneliklerinize mantıksal olarak bunları bir taksonomi halinde düzenlemek için Etiketler uygulayın. Her etiket bir ad ve bir değer çiftinden oluşur. Örneğin, "Ortam" adını ve "Üretim" değerini üretimdeki tüm kaynaklara uygulayabilirsiniz.

Azure geçişi, bir uygulamanın veya yazılımın kaynaklarına yüklenmesine izin vermez. 

- [Azure Kaynak Grafı Gezgini ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md) 

- [Azure Güvenlik Merkezi varlık envanteri yönetimi](../security-center/asset-inventory.md) 

- [Kaynak adlandırma ve etiketleme kararı kılavuzu](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Yalnızca onaylı Azure hizmetlerini kullanın

**Rehberlik**: kullanıcıların ortamınızda hangi hizmetleri sağlayabileceğini denetlemek ve kısıtlamak Için Azure İlkesi ' ni kullanın. Abonelikler içindeki kaynakları sorgulamak ve bulmak için Azure Kaynak Grafı'nı kullanın. Ayrıca Azure İzleyici'yi kullanarak onaylanmamış hizmetler algılandığında uyarı tetikleme amacıyla kurallar oluşturabilirsiniz.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Ilkesiyle belirli bir kaynak türünü reddetme](../governance/policy/samples/built-in-policies.md#general) 

- [Azure Kaynak Grafı Gezgini ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>ÖÖ-5: kullanıcıların Azure Resource Manager etkileşime geçme yeteneğini sınırlayın

**Rehberlik**: "Microsoft Azure yönetimi" uygulaması için "erişimi engelle" özelliğini yapılandırarak kullanıcıların Azure Resource Manager etkileşime geçmesini sınırlamak Için Azure koşullu erişimi kullanın.

Yapılandırma
- [Azure Resource Manager erişimi engellemek için koşullu erişim](../role-based-access-control/conditional-access-azure-management.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

## <a name="logging-and-threat-detection"></a>Günlüğe Kaydetme ve Tehdit Algılama

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Günlüğe Kaydetme ve Tehdit Algılama](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>GT-2: Azure kimlik ve erişim yönetimi için tehdit algılamayı etkinleştirme

**Rehber**: Azure AD’nin sağladığı aşağıdaki kullanıcı günlükleri Azure AD raporlamasında görüntülenebilir veya daha gelişmiş izleme ve analiz kullanım örnekleri için Azure İzleyici, Azure Sentinel veya diğer SIEM/izleme araçlarıyla tümleştirilebilir:

- Oturum açma bilgileri – Oturum açma bilgileri raporu, yönetilen uygulamaların kullanımı ve kullanıcı oturum açma etkinlikleri hakkında bilgi sağlar.

Denetim günlükleri - Azure AD içindeki çeşitli özellikler tarafından yapılan tüm değişiklikler için günlükler aracılığıyla izlenebilirlik sağlar. Azure AD içindeki herhangi bir kaynakta yapılan kullanıcı, uygulama, grup, rol ve ilkeleri ekleme veya kaldırma işlemleri gibi değişiklikler, denetim günlüklerine örnek gösterilebilir.

- Riskli oturum açma işlemleri - Riskli oturum açma işlemi bir kullanıcı hesabının meşru sahibi olmayan bir kişi tarafından gerçekleştirilmiş olabilecek oturum açma girişiminin göstergesidir.

- Riskli oldukları belirlenen kullanıcılar - Riskli kullanıcı, güvenliği tehlikeye girmiş olabilecek bir kullanıcı hesabının göstergesidir.

Azure Güvenlik Merkezi aşırı fazla sayıda başarısız kimlik doğrulaması girişimi, abonelikte kullanım dışı kalan hesaplar gibi bazı şüpheli etkinliklerde de uyarı verebilir. Temel güvenlik durumunu izlemeye ek olarak, Azure Güvenlik Merkezi’nin Tehdit Koruması modülü tek tek Azure işlem kaynaklarından (sanal makineler, kapsayıcılar, uygulama hizmeti), veri kaynaklarından (SQL DB ve depolama) ve Azure hizmet katmanlarından daha kapsamlı güvenlik uyarıları da toplayabilir. Bu özellik, bireysel kaynakların içindeki hesap bozukluklarını görünürlüğe sahip etmenize olanak tanır.

- [Azure Active Directory’de denetim etkinliği raporları](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure Kimlik Koruması’nı etkinleştirme](../active-directory/identity-protection/overview-identity-protection.md) 

- [Azure Güvenlik Merkezi’nde tehdit koruması](../security-center/azure-defender.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Azure kaynakları için günlüğe kaydetmeyi etkinleştirin

**Kılavuz**: Azure geçişi Şu anda Azure Kaynak günlükleri oluşturmaz.

Otomatik olarak kullanılabilen etkinlik günlükleri, okuma işlemleri (GET) dışında Azure geçiş kaynaklarınız için tüm yazma işlemlerini (PUT, POST, DELETE) içerir. Etkinlik günlükleri, sorun giderirken bir hata bulmak veya kuruluşunuzdaki bir kullanıcının bir kaynağı nasıl değiştirdiğinizi izlemek için kullanılabilir.

- [Azure Izleyici ile platform günlükleri ve ölçümleri toplama](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure 'da günlüğe kaydetme ve farklı günlük türlerini anlama](../azure-monitor/essentials/platform-logs-overview.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Güvenlik günlüğü yönetim ve analiz süreçlerini merkezileştirin

**Rehberlik**: bağıntı sağlamak için günlük depolamayı ve çözümlemeyi merkezileştirme. Her günlük kaynağı için bir veri sahibi, erişim Kılavuzu, depolama konumu, verileri işlemek ve erişmek için kullanılan araçları ve veri saklama gereksinimlerini atadığınızdan emin olun.

Azure etkinlik günlüklerini merkezi günlüklerinizi tümleştirdiğinizden emin olun. Endpoint cihazları, ağ kaynakları ve diğer güvenlik sistemleri tarafından oluşturulan güvenlik verilerini toplamak için Azure Izleyici aracılığıyla günlük alma. Azure Izleyici 'de analiz sorgulamak ve gerçekleştirmek için Log Analytics çalışma alanlarını kullanın ve uzun süreli ve arşiv depolama için Azure depolama hesaplarını kullanın.

Ayrıca, Azure Sentinel 'e veya bir üçüncü taraf SıEM 'e verileri etkinleştirin ve ekleyin.

Birçok kuruluş, daha az sıklıkta kullanılan "soğuk" verileri için sık kullanılan ve Azure depolama için Azure Sentinel ' i "etkin" veriler için kullanmayı seçer.

- [Azure Izleyici ile platform günlükleri ve ölçümleri toplama](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

## <a name="incident-response"></a>Olay Yanıtı

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Olay Yanıtı](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Hazırlık: Azure için olay yanıtı sürecini güncelleştirin

**Rehberlik**: Kuruluşunuzda güvenlik olaylarına yanıt vermeye yönelik süreçler bulunduğundan, bu süreçlerin Azure için güncelleştirildiğinden ve hazır olduğunuzdan emin olmak için bu süreçlerin düzenli olarak uygulandığından emin olun.

- [Güvenliği kuruluş genelinde uygulama](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Olay yanıtı başvuru kılavuzu](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Hazırlık: Olay bildirimini ayarlayın

**Rehberlik**: Azure Güvenlik Merkezi'nde güvenlik olayı iletişim bilgilerini ayarlayın. Microsoft, Microsoft Güvenlik Yanıt Merkezi'nin (MSRC) verilerinize kanuna aykırı veya yetkisiz erişim sağlanmasını keşfetmesi durumunda sizinle iletişime geçmek için bu iletişim bilgilerini kullanır. İsterseniz farklı Azure hizmetlerindeki olay uyarılarını ve bildirimlerini olay yanıt gereksinimlerinize göre özelleştirebilirsiniz. 

- [Azure Güvenlik Merkezi güvenlik iletişimini ayarlama](../security-center/security-center-provide-security-contact-details.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Algılama ve analiz: Yüksek kaliteli uyarıları temel alan olaylar oluşturun

**Rehberlik**: Yüksek kaliteli uyarılar oluşturmaya ve uyarıların kalitesini ölçmeye yönelik bir sürece sahip olduğunuzdan emin olun. Bu sayede önceki olaylardan dersler çıkarabilir ve analistler için uyarıları önceliklendirebilirsiniz. Bu sayede hatalı pozitif sonuçlarla zaman kaybetmelerini önlemiş olursunuz. 

Yüksek kaliteli uyarılar önceki olaylardan alınan dersler, doğrulanmış topluluk kaynakları ve farklı sinyal kaynaklarını birleştirip bağlantı oluşturarak uyarı oluşturmaya ve temizlemeye yönelik araçlar kullanılarak geliştirilebilir. 

Azure Güvenlik Merkezi, birçok Azure varlığı arasında yüksek kaliteli uyarılar sağlar. ASC veri bağlayıcısını kullanarak uyarıların Azure Sentinel’e akışını yapabilirsiniz. Azure Sentinel bir araştırmaya yönelik olayların otomatik olarak oluşturulması için gelişmiş uyarı kuralları oluşturur. 

Azure kaynaklarına yönelik riskleri tanımlamaya yardımcı olmak için, dışarı aktarma özelliğini kullanarak Azure Güvenlik Merkezi uyarılarınızı ve önerilerinizi dışarı aktarın. Uyarıları ve önerileri el ile veya sürekli bir biçimde dışarı aktarabilirsiniz.

- [Dışarı aktarma işlemini yapılandırma](../security-center/continuous-export.md)

- [Uyarıların Azure Sentinel’e akışını yapma](../sentinel/connect-azure-security-center.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>OY-4: Algılama ve analiz – olayı araştırma

**Rehber**: Analistlerin olası olayları araştırırken neler olup bittiğini tam olarak görmek için çeşitli veri kaynaklarını sorgulayabildiğinden ve kullanabildiğinden emin olun. Sonlandırma zinciri boyunca olası saldırganın etkinliklerinin izlenebilmesi için çeşitli günlükler toplanmalı, kör noktaların olması önlenmelidir.  Ayrıca diğer analistler için ve gelecekte başvurmak üzere içgörülerin ve çıkarılan derslerin yakalandığından da emin olmalısınız.  

Araştırmaya yönelik veri kaynakları kapsam içindeki hizmetlerden ve çalışan sistemlerden zaten toplanmakta olan merkezi günlük kaynaklarının yanı sıra şunları da içerebilir:

- Ağ verileri – ağ akış günlüklerini ve diğer analiz bilgilerini yakalamak için ağ güvenlik gruplarının akış günlüklerini, Azure Ağ İzleyicisi’ni ve Azure İzleyici’ni kullanın. 

- Çalışan sistemlerin anlık görüntüleri: 

    - Çalışan sistemin diskinin anlık görüntüsünü oluşturmak için Azure sanal makinesinin anlık görüntü özelliğini kullanın. 

    - Çalışan sistemin belleğinin anlık görüntüsünü oluşturmak için işletim sisteminin yerel bellek dökümü özelliğini kullanın.

    - Çalışan sistemlerin anlık görüntülerini oluşturmak için Azure hizmetlerinin anlık görüntü özelliğini veya yazılımınızın kendi özelliğini kullanın.

Azure Sentinel hemen her veri kaynağı üzerinde kapsamlı veri analizi gerçekleştirdiği gibi, olayların tüm yaşam döngüsünü yöneten bir olay yönetim portalı da sağlar. Araştırma sırasında elde edilen inceleme bilgileri izleme ve raporlama amacıyla bir olayla ilişkilendirilebilir. 

- [Windows makinesi diskinin anlık görüntüsü](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux makinesi diskinin anlık görüntüsü](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure Desteği tanılama bilgileri ve bellek dökümü toplama](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Azure Sentinel ile olayları araştırma](../sentinel/tutorial-investigate-cases.md)

**Azure Güvenlik Merkezi izlemesi**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Algılama ve analiz: Olayları önceliklendirin

**Rehber**: Uyarı önem derecesi ve uyarı hassasiyeti temelinde analistlere önce hangi olaylara odaklanılacağı konusunda bağlam sağlayın. 

Azure Güvenlik Merkezi önce hangi uyarıların araştırılması gerektiğini belirlemenize yardımcı olmak için her uyarıya bir önem derecesi atar. Önem derecesi, güvenlik merkezi 'nin uyarıyı vermek için kullanılan bulma veya analizde ne kadar önemli olduğunu ve uyarıya yönelik etkinliğin arkasında kötü amaçlı bir amaç olduğunu bilmenin yanı sıra güven düzeyini temel alır.

Buna ek olarak Azure kaynaklarını, özellikle hassas verileri işleyen kaynakları belirlemek ve kategorilere ayırmak için etiketleri kullanarak kaynakları işaretleyin ve bir adlandırma sistemi oluşturun.  Azure kaynaklarının önem düzeyine ve olayın oluştuğu ortama bağlı olarak uyarıların çözümünde önceliği belirlemek sizin sorumluluğunuzdadır.

- [Azure Güvenlik Merkezi'nde güvenlik uyarıları](../security-center/security-center-alerts-overview.md)

- [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](../azure-resource-manager/management/tag-resources.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Kapsama, ortadan kaldırma ve kurtarma: Olay işleme sürecini otomatikleştirin

**Rehberlik**: Yanıt süresini kısaltmak ve analistlerin yükünü hafifletmek için el ile yapılan yinelenen görevleri otomatikleştirin. El ile gerçekleştirilen görevlerin yürütülmesi daha uzun sürer ve olay yanıt sürecini yavaşlatarak analistlerin işleyebileceği olay sayısını azaltır. Ayrıca el ile gerçekleştirilen görevler analistler açısından daha yorucudur. Dolayısıyla gecikmelere neden olan insan hatası riskini artırır ve analistlerin karmaşık görevlere etkili bir şekilde odaklanma becerisini azaltır. Eylemleri otomatik olarak tetiklemek veya gelen güvenlik uyarılarını yanıtlamak üzere bir playbook çalıştırmak için Azure Güvenlik Merkezi’nin ve Azure Sentinel’in iş akışı otomasyonu özelliklerini kullanın. Playbook bildirimleri gönderme, hesapları devre dışı bırakma ve sorunlu ağları yalıtma gibi eylemleri gerçekleştirir. 

- [Güvenlik Merkezi’nde iş akışı otomasyonunu yapılandırma](../security-center/workflow-automation.md)

- [Azure Güvenlik Merkezi’nde otomatik tehdit yanıtlarını ayarlama](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Azure Sentinel’de otomatik tehdit yanıtlarını ayarlama](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

## <a name="posture-and-vulnerability-management"></a>Duruş ve Güvenlik Açığı Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Duruş ve Güvenlik Açığı Yönetimi](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Düzenli aralıklarla saldırı simülasyonları yapın

**Rehberlik**: Gerektiğinde Azure kaynaklarınızda sızma testi veya kırmızı takım etkinlikleri gerçekleştirerek tüm kritik güvenlik bulgularının düzeltilmesini sağlayın.
Sızma testlerinizin Microsoft ilkelerini ihlal etmediğinden emin olmak için Microsoft Bulut Sızma Testi Etkileşim Kuralları'na uygun hareket edin. Microsoft tarafından yönetilen bulut altyapısına, hizmetlere ve uygulamalara yönelik kırmızı takım ve canlı site sızma testi gerçekleştirmek için Microsoft'un stratejisini ve yürütme sürecini kullanın.

- [Azure'da sızma testi yapma](../security/fundamentals/pen-testing.md)

- [Sızma Testi Etkileşim Kuralları](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Bulut ile Kırmızı Takım Oluşturma](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Paylaşımlı

## <a name="governance-and-strategy"></a>İdare ve Strateji

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: İdare ve Strateji](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Varlık yönetimi ve veri koruma stratejisi tanımlayın 

**Rehberlik**: Sistemlerin ve verilerin sürekli izlenmesine ve korunmasına yönelik net bir strateji belgelendirdiğinizden ve paylaştığınızdan emin olun. İş açısından kritik verilerin ve sistemlerin bulma, değerlendirme, koruma ve izleme süreçleri için öncelik belirleyin. 

Bu strateji kapsamında aşağıdaki öğeler için rehberlik, ilkeler ve standartlar belgelenmiş olmalıdır: 

-   İş risklerine göre veri sınıflandırma standardı

-   Riskler ve varlık envanteriyle ilgili güvenlik kuruluşu görünürlüğü 

-   Güvenlik kuruluşunun kullanılmasını onayladığı Azure hizmetleri 

-   Varlıkların yaşam döngüsü boyunca güvenliği

-   Kuruluşun veri sınıflandırmasına uygun gerekli erişim denetimi stratejisi

-   Yerel Azure ve üçüncü taraf veri koruma özelliklerinin kullanılması

-   Taşıma durumundaki ve bekleyen veriler için şifreleme gereksinimleri

-   Uygun şifreleme standartları

Daha fazla bilgi için aşağıdaki başvuruları inceleyin:
- [Azure Güvenlik Mimarisi Önerileri: Depolama, veri ve şifreleme](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Azure Güvenliği Temelleri: Azure'da veri güvenliği, şifreleme ve depolama](../security/fundamentals/encryption-overview.md)

- [Bulut Benimseme Çerçevesi: Azure'da veri güvenliği ve şifreleme için en iyi deneyimler](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Güvenlik Karşılaştırması: Varlık yönetimi](../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure Güvenlik Karşılaştırması: Veri koruma](../security/benchmarks/security-controls-v2-data-protection.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Kurumsal segmentasyon stratejisini tanımlayın 

**Rehberlik**: Kimlik, ağ, uygulama, abonelik, yönetim grubu ve diğer denetimleri kullanarak varlık erişimi segmentasyonuna yönelik kuruluş genelinde kullanılacak bir strateji belirleyin.

Güvenlik ayrımı gereksinimiyle birbirleriyle iletişim kurma ve verilere erişme gereksinimine sahip olan sistemlerin günlük çalışmasını etkinleştirme gereksinimi arasında bir denge kurun.

Segmentasyon stratejisinin ağ güvenliği, kimlik ve erişim modelleri, uygulama izinleri/erişim modelleri ve insanlar tarafından gerçekleştirilen süreç denetimleri gibi farklı denetim türlerinde tutarlı bir şekilde uygulandığından emin olun.

- [Azure için segmentasyon stratejisi rehberi (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure için segmentasyon stratejisi rehberi (belge)](/security/compass/governance#enterprise-segmentation-strategy)

- [Ağ segmentasyonunu kurumsal segmentasyon stratejisiyle uyumlu hale getirme](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Güvenlik duruşu yönetim stratejisini tanımlayın

**Rehberlik**: Varlıklarınız ve içinde bulundukları ortamla ilgili riskleri sürekli olarak ölçün ve ortadan kaldırın. Yayımlanmış uygulamalar, ağ giriş ve çıkış noktaları, kullanıcı ve yönetici uç noktaları gibi değeri yüksek varlıkları ve kullanıma açık olan saldırı yüzeylerini önceliklendirin.

- [Azure Güvenlik Karşılaştırması: Duruş ve güvenlik açığı yönetimi](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Kuruluş genelindeki rolleri, sorumlulukları ve hesap verilebilirlik durumlarını uyumlu hale getirin

**Rehberlik**: Güvenlik kuruluşunuzdaki roller ve sorumluluklar için net bir strateji oluşturup bunu belgelendirdiğinizden ve paylaştığınızdan emin olun. Güvenlik kararları için net bir hesap verilebilirlik süreci oluşturmayı önceliklendirin, herkesi paylaşılan sorumluluk modeli hakkında eğitin ve teknik ekiplere bulut ortamının güvenliğini sağlamaya yönelik eğitimler verin.

- [Azure Güvenliği En İyi Deneyimi 1 - İnsanlar: Ekipleri Bulut Güvenliği Yolculuğu Hakkında Eğitin](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure Güvenliği En İyi Deneyimi 2 - İnsanlar: Ekipleri Bulut Güvenliği Teknolojileri Hakkında Eğitin](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure Güvenliği En İyi Deneyimi 3 - Süreç: Bulut Güvenliği Kararları için Hesap Verilebilirlik Ataması Yapın](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Ağ güvenlik stratejisini tanımlayın

**Rehberlik**: Kuruluşunuzun genel güvenlik erişimi denetimi stratejisi kapsamında Azure ağ güvenliği için bir yaklaşım belirleyin.  

Bu strateji kapsamında aşağıdaki öğeler için rehberlik, ilkeler ve standartlar belgelenmiş olmalıdır: 

-   Merkezi ağ yönetimi ve güvenlik sorumluluğu

-   Kurumsal segmentasyon stratejisiyle uyumlu sanal ağ segmentasyon modeli

-   Farklı tehdit ve saldırı senaryolarına yönelik düzeltme stratejisi

-   İnternet uç düğümü ile giriş ve çıkış stratejisi

-   Hibrit bulut ve şirket içi bağlantı stratejisi

-   Güncel ağ güvenliği yapıtları (ağ diyagramları, başvuru amaçlı ağ mimarisi vb.)

Daha fazla bilgi için aşağıdaki başvuruları inceleyin:
- [Azure Güvenliği En İyi Deneyimi 11 - Mimari. Tek ve birleşik güvenlik stratejisi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-controls-v2-network-security.md)

- [Azure ile ağ güvenliğine genel bakış](../security/fundamentals/network-overview.md)

- [Kurumsal ağ mimarisi stratejisi](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Kimlik ve ayrıcalıklı erişim stratejisini tanımlayın

**Rehberlik**: Kuruluşunuzun genel güvenlik erişimi denetimi stratejisi kapsamında Azure kimlik ve ayrıcalıklı erişim yaklaşımları belirleyin.  

Bu strateji kapsamında aşağıdaki öğeler için rehberlik, ilkeler ve standartlar belgelenmiş olmalıdır: 

-   Merkezi bir kimlik ve kimlik doğrulaması sistemi ile diğer iç ve dış kimlik sistemleriyle olan bağlantısı

-   Farklı kullanım örneklerine ve koşullara yönelik güçlü kimlik doğrulama yöntemleri

-   Yüksek ayrıcalıklı kullanıcıların korunması

-   Kullanıcı etkinlikleri için anomali izleme ve işleme  

-   Kullanıcı kimliği ve erişim gözden geçirmesi ile uzlaştırma süreci

Daha fazla bilgi için aşağıdaki başvuruları inceleyin:

- [Azure Güvenlik Karşılaştırması: Kimlik yönetimi](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure Güvenlik Karşılaştırması: Ayrıcalıklı erişim](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Azure Güvenliği En İyi Deneyimi 11 - Mimari. Tek ve birleşik güvenlik stratejisi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure'da kimlik yönetim güvenliğine genel bakış](../security/fundamentals/identity-management-overview.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Günlüğe kaydetme ve tehdit yanıtı stratejisi tanımlayın

**Rehberlik**: Uyumluluk gereksinimleri kapsamında tehditleri hızlı bir şekilde algılamak ve düzeltmek için bir günlüğe kaydetme ve tehdit yanıtı stratejisi oluşturun. Tümleştirme ve el ile gerçekleştirilen adımlar yerine tehditlere odaklanabilmeleri için analistlere yüksek kaliteli uyarılar ve sorunsuz deneyimler sunmayı önceliklendirin. 

Bu strateji kapsamında aşağıdaki öğeler için rehberlik, ilkeler ve standartlar belgelenmiş olmalıdır: 

-   Güvenlik operasyonları (SecOps) organizasyonunun rolü ve sorumlulukları 

-   NIST veya diğer sektör çerçeveleriyle uyumlu iyi tanımlanmış bir olay yanıt süreci 

-   Tehdit algılama, olay yanıtı ve uyumluluk gereksinimlerini karşılamak için günlük yakalama ve saklama

-   SIEM, yerel Azure özellikleri ve diğer kaynaklar aracılığıyla merkezi görünürlük ve bilgi bağıntısı 

-   Planı müşterilerinize, tedarikçilerinize ve diğer proje katılımcılarına iletme ve onları bilgilendirme

-   Günlüğe kaydetme ve tehdit algılama, adli araştırma, saldırı önleme ve ortadan kaldırma gibi olay işleme adımları için yerel Azure ve üçüncü taraf platformları kullanma

-   Olayları ve çıkarılan dersler ile kanıt koruma gibi olay sonrası etkinlikleri işlemeye yönelik süreçler

Daha fazla bilgi için aşağıdaki başvuruları inceleyin:

- [Azure Güvenlik Karşılaştırması: Günlüğe kaydetme ve tehdit algılama](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Güvenlik Karşılaştırması: Olay yanıtı](../security/benchmarks/security-controls-v2-incident-response.md)

- [Azure Güvenliği En İyi Deneyimi 4 - Süreç. Bulut için Olay Yanıt Sürecini Güncelleştirme](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Benimseme Çerçevesi, günlüğe kaydetme ve raporlama kararı rehberi](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure ile kurumsal ölçek, yönetim ve izleme](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Azure Güvenlik Karşılaştırması 2.0 sürümüne genel bakış](../security/benchmarks/overview.md)
- [Azure güvenlik temelleri](../security/benchmarks/security-baselines-overview.md) hakkında daha fazla bilgi edinin
