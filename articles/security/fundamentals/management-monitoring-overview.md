---
title: Yönetim ve izleme güvenlik özellikleri-Microsoft Azure | Microsoft Docs
description: Bu makalede, Azure 'un Azure Cloud Services ve sanal makinelerin yönetimine ve izlenmesine yardımcı olmak için Azure 'un sağladığı güvenlik özelliklerine ve hizmetlerine genel bir bakış sunulmaktadır.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: terrylan
ms.openlocfilehash: f87ea1e1c9f43de4e9e0f94d1cd855615a0a880c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101712054"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Azure Güvenlik yönetimi ve izlemeye genel bakış
Bu makalede, Azure 'un Azure Cloud Services ve sanal makinelerin yönetimine ve izlenmesine yardımcı olmak için Azure 'un sağladığı güvenlik özelliklerine ve hizmetlerine genel bir bakış sunulmaktadır.

## <a name="azure-role-based-access-control"></a>Azure rol tabanlı erişim denetimi

Azure rol tabanlı erişim denetimi (Azure RBAC), Azure kaynakları için ayrıntılı erişim yönetimi sağlar. Azure RBAC kullanarak kullanıcılara yalnızca işlerini gerçekleştirmek için ihtiyaç duydukları erişim miktarını verebilirsiniz. Azure RBAC Ayrıca kişilerin kuruluştan ayrıldıklarında buluttaki kaynaklara erişiminin kaybedildiğinden emin olmanıza da yardımcı olabilir.

Daha fazla bilgi edinin:

* [Azure RBAC üzerinde ekip blogu Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Kötü amaçlı yazılımdan koruma

Azure ile Microsoft, Symantec, Trend Micro, McAfee ve Kaspersky gibi önemli güvenlik satıcılarından kötü amaçlı yazılımdan koruma yazılımı kullanabilirsiniz. Bu yazılım, sanal makinelerinizi kötü amaçlı dosyalardan, reklam yazılımlarından ve diğer tehditlerden korumanıza yardımcı olur.

Azure Cloud Services için Microsoft kötü amaçlı yazılımdan koruma ve sanal makineler, hem PaaS rolleri hem de sanal makineler için bir kötü amaçlı yazılımdan koruma Aracısı yükleme olanağı sunar. Bu özellik System Center Endpoint Protection temel alınarak, kendini kanıtlamış şirket içi güvenlik teknolojisini buluta getirir.

Ayrıca, Azure platformunda eğilimin [derin güvenliğine ve güvenlik](https://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) açısından [yüksek](https://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) ürünlere yönelik derin tümleştirme sunuyoruz. Derin güvenlik bir virüsten koruma çözümüdür ve Securecyüksek bir şifreleme çözümüdür. Derin güvenlik, sanal makinelerin içinde bir uzantı modeli aracılığıyla dağıtılır. Azure portal Kullanıcı arabirimi ve PowerShell 'i kullanarak, daha önce dağıtılmış olan yeni VM 'Lerde veya zaten dağıtılmış olan mevcut VM 'Ler içinde derin güvenlik kullanmayı tercih edebilirsiniz.

Symantec Endpoint Protection (SEP) Azure 'da da desteklenir. Portal tümleştirmesi sayesinde, bir VM 'de SEP kullanmayı planladığınız bir sanal makineyi belirtebilirsiniz. SEP, Azure portal aracılığıyla yeni bir VM 'ye yüklenebilir veya PowerShell aracılığıyla var olan bir VM 'ye yüklenebilir.

Daha fazla bilgi edinin:

* [Azure Sanal Makinelerinde Kötü Amaçlı Yazılıma Karşı Koruma Çözümleri Dağıtma](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Azure Cloud Services ve sanal makineler için Microsoft kötü amaçlı yazılımdan koruma](antimalware.md)
* [Windows VM 'de bir hizmet olarak Trend mikro derin güvenliği 'ni yüklemek ve yapılandırmak](/previous-versions/azure/virtual-machines/extensions/trend)
* [Windows VM 'de Symantec Endpoint Protection 'i yüklemek ve yapılandırmak](../../virtual-machines/extensions/symantec.md)
* [Azure sanal makinelerini korumaya yönelik yeni kötü amaçlı yazılımdan koruma seçenekleri](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Azure AD Multi-Factor Authentication, birden fazla doğrulama yönteminin kullanılmasını gerektiren bir kimlik doğrulama yöntemidir. Kullanıcı oturum açma işlemlerine ve işlemlerine kritik bir ikinci güvenlik katmanı ekler.

Multi-Factor Authentication, kullanıcıların basit bir oturum açma işlemi taleplerini karşılarken veri ve uygulamalara erişimi korumaya yardımcı olur. Bir dizi doğrulama seçeneği (telefon araması, SMS mesajı veya mobil uygulama bildirimi veya doğrulama kodu) ve üçüncü taraf OATH belirteçleri aracılığıyla güçlü kimlik doğrulaması sağlar.

Daha fazla bilgi edinin:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Azure AD Multi-Factor Authentication nedir?](../../active-directory/authentication/concept-mfa-howitworks.md)
* [Azure AD Multi-Factor Authentication nasıl çalışır?](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute

Azure ExpressRoute kullanarak, şirket içi ağlarınızı bir bağlantı sağlayıcısı tarafından kolaylaştırarak adanmış bir özel bağlantı üzerinden Microsoft Bulut genişletebilirsiniz. ExpressRoute ile Azure, Microsoft 365 ve CRM Online gibi Microsoft bulut hizmetleriyle bağlantı kurabilirsiniz. Bağlantı şunları yapabilir:

* Bir any (IP VPN) ağı.
* Noktadan noktaya Ethernet ağı.
* Bir ortak konum tesisinde bağlantı sağlayıcısı üzerinden sanal çapraz bağlantı.

ExpressRoute bağlantıları, genel İnternet üzerinden geçmiyor. İnternet üzerinden tipik bağlantılardan daha fazla güvenilirlik, daha fazla hız, daha düşük gecikme süresi ve daha yüksek güvenlik sunabiliyor.

Daha fazla bilgi edinin:

* [ExpressRoute teknik genel bakış](../../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Sanal ağ geçitleri

Azure sanal ağ geçitleri olarak da bilinen VPN ağ geçitleri, sanal ağlar ve şirket içi konumlar arasında ağ trafiği göndermek için kullanılır. Ayrıca, Azure 'daki birden çok sanal ağ arasında trafik göndermek için de kullanılır (ağ üzerinden ağ). VPN ağ geçitleri, Azure ile altyapınız arasında güvenli şirketler arası bağlantı sağlar.

Daha fazla bilgi edinin:

* [VPN ağ geçitleri hakkında](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Azure ile ağ güvenliğine genel bakış](network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Bazen kullanıcıların Azure kaynaklarında veya diğer SaaS uygulamalarında ayrıcalıklı işlemler gerçekleştirmesi gerekir. Bu genellikle kuruluşların Azure Active Directory (Azure AD) ' de kalıcı ayrıcalıklı erişim hakkı vermesi anlamına gelir.

Kuruluşlar bu kullanıcıların ayrıcalıklı erişimiyle ne yaptığını yeterince izleyemediği için bulutta barındırılan kaynaklar için büyüyen bir güvenlik riskidir. Ayrıca, ayrıcalıklı erişimi olan bir kullanıcı hesabı tehlikeye atılırsa, bir ihlal bir kuruluşun genel bulut güvenliğini etkileyebilir. Azure AD Privileged Identity Management, ayrıcalıkların etkilenme süresini azaltarak ve görünürlüğü kullanım halinde artırarak bu riski çözmeye yardımcı olur.  

Privileged Identity Management, bir rol veya "tam zamanında" yönetici erişimi için geçici yönetici kavramını tanıtır. Bu tür bir yönetici, atanmış rol için bir etkinleştirme işlemini tamamlaması gereken bir kullanıcı. Etkinleştirme işlemi, belirli bir süre boyunca Kullanıcı atamasını Azure AD 'deki bir role etkin değil etkin olarak değiştirir.

Daha fazla bilgi edinin:

* [Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
* [Azure AD Privileged Identity Management ile çalışmaya başlama](../../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>Kimlik Koruması

Azure AD Kimlik Koruması, işletmenizin korunmasına yardımcı olmak için şüpheli oturum açma etkinliklerinin ve olası güvenlik açıklarının birleştirilmiş bir görünümünü sağlar. Kimlik koruması, kullanıcılar ve ayrıcalıklı (yönetici) kimlikleri için şüpheli etkinlikleri algılar ve şunun gibi sinyallere dayanır:

* Deneme yanılma saldırıları.
* Sızdırılan kimlik bilgileri.
* Bilmediğiniz konumlardan ve virüs bulaşmış cihazlardan oturum açma işlemleri.

Kimlik koruması, bildirimler ve önerilen düzeltme sağlayarak riskleri gerçek zamanlı olarak azaltmaya yardımcı olur. Kullanıcı risk önem derecesini hesaplar. Gelecekteki tehditlere karşı uygulama erişimini otomatik olarak korumaya yardımcı olmak için risk tabanlı ilkeler yapılandırabilirsiniz.

Daha fazla bilgi edinin:

* [Azure Active Directory Kimlik Koruması](../../active-directory/identity-protection/overview-identity-protection.md)
* [Channel 9: Azure AD ve kimlik Show: kimlik koruması önizlemesi](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Güvenlik Merkezi

Azure Güvenlik Merkezi, tehditleri önlemenize, algılamanıza ve yanıtlamanıza yardımcı olur. Güvenlik Merkezi, Azure kaynaklarınızın yanı sıra hibrit bulut ortamınızdan daha fazla görünürlük ve denetim sahibi olmanızı sağlar. 

Güvenlik Merkezi, bağlantılı kaynaklarınızın sürekli güvenlik değerlendirmelerini gerçekleştirir ve ortamınız için uygun olan ayrıntılı güvenlik önerileri sağlamak üzere yapılandırma ve dağıtımını [Azure Güvenlik kıyaslaması](../benchmarks/introduction.md) ile karşılaştırır.

Güvenlik Merkezi, Azure kaynaklarınızın güvenliğini iyileştirebilmenizi ve bunları izlemenize yardımcı olur:

- Azure abonelik kaynaklarınızın ilkelerini şunlara göre tanımlamanızı sağlama:
    - Kuruluşunuzun güvenlik ihtiyaçları.
    - Her abonelikteki verilerin ya da veri hassasiyetini.
    - Abonelikleriniz için uyguladığınız tüm sektör veya yasal standartlar veya kıyaslamalar. 
- Azure sanal makinelerinizin, ağ ve uygulamalarınızın durumunu izleme.
- Tümleşik iş ortağı çözümlerinden gelen uyarılar da dahil olmak üzere, öncelikli güvenlik uyarılarının bir listesini sağlama. Ayrıca, bir saldırının hızlı bir şekilde araştırılması için ihtiyaç duyduğunuz bilgileri ve nasıl düzeltileceğine ilişkin önerileri de sağlar.

Daha fazla bilgi edinin:

* [Azure Güvenlik Merkezi'ne Giriş](../../security-center/security-center-introduction.md)
* [Azure Güvenlik Merkezi 'nde güvenli puanınızı geliştirme](../../security-center/secure-score-security-controls.md)

## <a name="intelligent-security-graph"></a>Intelligent Security Graph

Intelligent Security Graph, Microsoft ürün ve hizmetlerinde gerçek zamanlı tehdit koruması sağlar. Kurumsal güvenliği güçlendirin öngörüleri sağlamak üzere büyük miktarda tehdit bilgileri ve güvenlik verisi bağlayan gelişmiş çözümlemeler kullanır. Microsoft, daha zengin Öngörüler sunmak için ayda 450.000.000.000 ' den fazla kimlik doğrulamasını işleme, kötü amaçlı yazılım ve kimlik avı için 400.000.000.000 e-postaları taramayı ve 1.000.000.000 cihazlarını güncellemeyi kullanır. Bu Öngörüler, kuruluşunuzun saldırıları hızla algılamasına ve yanıt vermesine yardımcı olabilir.

* [Intelligent Security Graph](https://www.microsoft.com/security/intelligence)

## <a name="next-steps"></a>Sonraki Adımlar
[Paylaşılan sorumluluk modeli](shared-responsibility.md) ve Microsoft tarafından hangi güvenlik görevlerinin işlendiği ve sizin tarafınızdan hangi görevlerin işlendiği hakkında bilgi edinin.

Güvenlik yönetimi hakkında daha fazla bilgi için bkz. [Azure 'Da güvenlik yönetimi](management.md).