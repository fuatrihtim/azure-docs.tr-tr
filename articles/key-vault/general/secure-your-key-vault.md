---
title: Anahtar kasasına erişimin güvenliğini sağlama
description: Active Directory kimlik doğrulaması ve kaynak uç noktaları da dahil olmak üzere Azure Key Vault için erişim modeli.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: sudbalas
ms.openlocfilehash: 94034edfa1a5c6ffccd022b4cbf7bae42cc0bae3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212476"
---
# <a name="secure-access-to-a-key-vault"></a>Anahtar kasasına erişimin güvenliğini sağlama

Azure Key Vault, şifreleme anahtarlarını ve sertifikalar, bağlantı dizeleri ve parolalar gibi gizli dizileri koruyan bir bulut hizmetidir. Bu veriler hassas ve iş açısından kritik olduğundan, yalnızca yetkili uygulamalara ve kullanıcılara izin vererek anahtar kasalarınıza güvenli bir şekilde erişmeniz gerekir. Bu makalede Key Vault erişim modeline genel bir bakış sunulmaktadır. Kimlik doğrulama ve yetkilendirmeyi açıklar ve anahtar kasalarınıza erişimin güvenliğini nasıl sağlayabileceğinizi açıklar.

Key Vault hakkında daha fazla bilgi için bkz. [Azure Key Vault hakkında](overview.md); anahtar kasasında nelerin depolanabileceği hakkında daha fazla bilgi için bkz. [anahtarlar, gizlilikler ve sertifikalar hakkında](about-keys-secrets-certificates.md).

## <a name="access-model-overview"></a>Erişim modeline genel bakış

Bir anahtar kasasına erişim, iki arabirim aracılığıyla denetlenir: **Yönetim düzlemi** ve **veri düzlemi**. Yönetim düzlemi Key Vault kendisini yönettiğiniz yerdir. Bu düzlemdeki işlemler, anahtar kasalarını oluşturmayı ve silmeyi, Key Vault özelliklerini almayı ve erişim ilkelerini güncelleştirmeyi içerir. Veri düzlemi, bir anahtar kasasında depolanan verilerle çalıştığınız yerdir. Anahtarlar, gizli diziler ve sertifikalar ekleyebilir, silebilir ve değiştirebilirsiniz.

Her iki düzlem de kimlik doğrulaması için [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) kullanır. Yönetim düzlemi, yetkilendirme için [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md) kullanır ve veri düzlemi, [Key Vault veri düzlemi işlemleri için](./rbac-guide.md) [Key Vault erişim ilkesi](./assign-access-policy-portal.md) ve Azure RBAC kullanır.

Her iki düzlemde bir anahtar kasasına erişmek için, tüm çağıranların (kullanıcılar veya uygulamalar) uygun kimlik doğrulaması ve yetkilendirmesi olması gerekir. Kimlik doğrulama, arayanın kimliğini belirler. Yetkilendirme, çağıranın hangi işlemleri yürütebileceğini belirler. Key Vault kimlik doğrulaması, herhangi bir **güvenlik sorumlusunun** kimliğini kimlik doğrulamasından getirmekten sorumlu olan [Azure ACTIVE DIRECTORY (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md)ile birlikte çalışıyor.

Güvenlik sorumlusu, Azure kaynaklarına erişim isteyen bir Kullanıcı, Grup, hizmet veya uygulamayı temsil eden bir nesnedir. Azure her güvenlik sorumlusuna benzersiz bir **nesne kimliği** atar.

* Bir **Kullanıcı** güvenlik sorumlusu, Azure Active Directory bir profili olan bir bireyi tanımlar.

* Bir **Grup** güvenlik sorumlusu Azure Active Directory içinde oluşturulan bir kullanıcı kümesini tanımlar. Gruba atanan tüm roller veya izinler, Grup içindeki tüm kullanıcılara verilir.

* **Hizmet sorumlusu** , bir kullanıcı veya Grup yerine bir kod parçası olan bir uygulamayı veya hizmeti tanımlayan bir güvenlik sorumlusu türüdür. Hizmet sorumlusunun nesne KIMLIĞI, **ISTEMCI kimliği** olarak bilinir ve Kullanıcı adı gibi davranır. Hizmet sorumlusunun **istemci gizli** dizisi veya **sertifikası** , parolası gibi davranır. Birçok Azure hizmeti, [yönetilen kimliğin](../../active-directory/managed-identities-azure-resources/overview.md) **istemci kimliği** ve **sertifika** otomatik yönetimiyle atanmasını destekler. Yönetilen kimlik, Azure 'da kimlik doğrulaması için en güvenli ve önerilen seçenektir.

Key Vault kimlik doğrulaması hakkında daha fazla bilgi için bkz. [Azure Key Vault kimlik](authentication.md) doğrulama

## <a name="key-vault-authentication-options"></a>Key Vault kimlik doğrulama seçenekleri

Bir Azure aboneliğinde bir Anahtar Kasası oluşturduğunuzda, bu, aboneliğin Azure AD kiracısı ile otomatik olarak ilişkilendirilir. Her iki düzlemdeki tüm çağıranlar bu kiracıya kaydolmalıdır ve anahtar kasasına erişmek için kimliğini doğrular. Her iki durumda da, uygulamalar üç şekilde Key Vault erişebilir:

- **Yalnızca uygulama**: uygulama bir hizmet sorumlusunu veya yönetilen kimliği temsil eder. Bu kimlik, anahtar kasasından düzenli olarak sertifikalara, anahtarlara veya gizli uygulamalara erişmesi gereken uygulamalar için en yaygın senaryolardır. Bu senaryonun çalışması için, `objectId` uygulamanın erişim ilkesinde belirtilmesi gerekir ve `applicationId` belirtilmemelidir veya olması gerekir  `null` .
- **Yalnızca Kullanıcı**: Kullanıcı, kiracıda kayıtlı herhangi bir uygulamadan anahtar kasasına erişir. Bu tür erişimin örnekleri Azure PowerShell ve Azure portal içerir. Bu senaryonun çalışması için, `objectId` kullanıcının erişim ilkesinde belirtilmesi ve `applicationId` belirtilmemelidir veya olması _gerekir_ `null` .
- **Uygulama-Plus-Kullanıcı** (bazen _bileşik kimlik_ olarak adlandırılır): kullanıcının belirli bir uygulamadan anahtar kasasına erişmesi _ve_ uygulamanın, kullanıcının kimliğine bürünmek için, Kullanıcı adına kimlik doğrulaması (OBO) akışı kullanması gerekir. Bu senaryonun çalışması için, her ikisi `applicationId` de `objectId` erişim ilkesinde belirtilmelidir. , `applicationId` Gerekli uygulamayı tanımlar ve `objectId` kullanıcıyı tanımlar. Şu anda bu seçenek, Azure RBAC (Önizleme) veri düzlemi için kullanılamaz.

Tüm erişim türlerinde, uygulama Azure AD ile kimlik doğrulaması yapar. Uygulama, uygulama türüne göre desteklenen herhangi bir [kimlik doğrulama yöntemini](../../active-directory/develop/authentication-vs-authorization.md) kullanır. Uygulama, erişim izni vermek için düzlemdeki bir kaynak için bir belirteç alır. Kaynak, Azure ortamına göre yönetim veya veri düzleminde bir uç noktadır. Uygulama belirteci kullanır ve Key Vault bir REST API isteği gönderir. Daha fazla bilgi edinmek için [tüm kimlik doğrulama akışını](../../active-directory/develop/v2-oauth2-auth-code-flow.md)gözden geçirin.

Her iki düzlemde kimlik doğrulama için tek bir mekanizmanın çeşitli avantajları vardır:

- Kuruluşlar, kuruluştaki tüm anahtar kasaları için merkezi olarak erişimi denetleyebilir.
- Bir Kullanıcı ayrılsa bile, kuruluştaki tüm anahtar kasalarına erişimi anında kaybeder.
- Kuruluşlar, ek güvenlik için Multi-Factor Authentication 'ı etkinleştirmek gibi Azure AD 'deki seçenekleri kullanarak kimlik doğrulamasını özelleştirebilir.

## <a name="resource-endpoints"></a>Kaynak uç noktaları

Uygulamalar, uç noktalar aracılığıyla düzlemleri erişir. İki düzlemi için erişim denetimleri bağımsız olarak çalışır. Bir uygulamaya anahtar kasasındaki anahtarları kullanma izni vermek için, Key Vault erişim ilkesi veya Azure RBAC (Önizleme) kullanarak veri düzlemi erişimi verirsiniz. Kullanıcıya Key Vault özelliklerine ve etiketlere yönelik okuma erişimi vermek, ancak verilere (anahtarlar, gizlilikler veya sertifikalar) erişmek için Azure RBAC ile yönetim düzlemi erişimi verirsiniz.

Aşağıdaki tabloda yönetim ve veri düzlemleri için uç noktalar gösterilmektedir.

| Erişim &nbsp; düzlemi | Erişim uç noktaları | Operations | Erişim &nbsp; denetimi mekanizması |
| --- | --- | --- | --- |
| Yönetim düzlemi | **Genel**<br> management.azure.com:443<br><br> **Azure Çin 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure ABD kamu:**<br> management.usgovcloudapi.net:443<br><br> **Azure Almanya:**<br> management.microsoftazure.de:443 | Anahtar kasaları oluşturun, okuyun, güncelleştirin ve silin<br><br>Key Vault erişim ilkelerini ayarlama<br><br>Key Vault etiketlerini ayarla | Azure RBAC |
| Veri düzlemi | **Genel**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure Çin 21Vianet:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure ABD kamu:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Almanya:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | Anahtarlar: şifreleme, şifre çözme, wrapKey, unwrapKey, imzala, doğrula, alma, listeleme, oluşturma, güncelleştirme, içeri aktarma, silme, kurtarma, yedekleme, geri yükleme, Temizleme<br><br> Sertifikalar: managecontacts, getısers, listissuers, setısers, silme, yönetim verenler, alma, listeleme, oluşturma, içeri aktarma, güncelleştirme, silme, kurtarma, yedekleme, geri yükleme, Temizleme<br><br>  Gizlilikler: Al, Listele, ayarla, Sil, kurtar, Yedekle, geri yükle, temizle | Key Vault erişim ilkesi veya Azure RBAC (Önizleme)|

## <a name="management-plane-and-azure-rbac"></a>Yönetim düzlemi ve Azure RBAC

Yönetim düzleminde, bir çağıranın yürütebileceği işlemleri yetkilendirmek için [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md) kullanırsınız. Azure RBAC modelinde, her Azure aboneliğinin bir Azure AD örneği vardır. Bu dizinden kullanıcılara, gruplara ve uygulamalara erişim izni verirsiniz. Azure aboneliğindeki Azure Resource Manager dağıtım modelini kullanan kaynakları yönetmek için erişim izni verilir.

Bir kaynak grubunda bir Anahtar Kasası oluşturup Azure AD 'yi kullanarak erişimi yönetebilirsiniz. Kullanıcılara veya gruplara bir kaynak grubundaki anahtar kasalarını yönetme yeteneği vermiş olursunuz. Uygun Azure rolleri atayarak erişimi belirli bir kapsam düzeyinde verirsiniz. Anahtar kasalarını yönetmek üzere bir kullanıcıya erişim izni vermek için, belirli bir kapsamdaki kullanıcıya önceden tanımlı bir [Key Vault katılımcısı](../../role-based-access-control/built-in-roles.md#key-vault-contributor) rolü atarsınız. Aşağıdaki kapsamlar düzeyleri bir Azure rolüne atanabilir:

- **Abonelik**: abonelik düzeyinde atanan bir Azure rolü, bu aboneliğin içindeki tüm kaynak grupları ve kaynaklar için geçerlidir.
- **Kaynak grubu**: kaynak grubu düzeyinde atanan bir Azure rolü, bu kaynak grubundaki tüm kaynaklar için geçerlidir.
- **Belirli kaynak**: belirli bir kaynak için atanan bir Azure rolü bu kaynak için geçerlidir. Bu durumda, kaynak belirli bir Anahtar Kasası olur.

Önceden tanımlanmış birkaç rol vardır. Önceden tanımlanmış bir rol gereksinimlerinize uygun değilse, kendi rolünüzü tanımlayabilirsiniz. Daha fazla bilgi için bkz. [Azure yerleşik rolleri](../../role-based-access-control/built-in-roles.md). 

`Microsoft.Authorization/roleAssignments/write` `Microsoft.Authorization/roleAssignments/delete` [Kullanıcı erişimi Yöneticisi](../../role-based-access-control/built-in-roles.md#user-access-administrator) veya [sahibi](../../role-based-access-control/built-in-roles.md#owner) gibi izinlerinizin ve izninizin olması gerekir

> [!IMPORTANT]
> Bir kullanıcının bir `Contributor` Anahtar Kasası yönetim düzlemine izinleri varsa, kullanıcı Key Vault erişim ilkesi ayarlayarak kendilerine veri düzlemine erişim izni verebilir. `Contributor`Anahtar kasalarınıza kimin rol erişimi olduğunu sıkı bir şekilde denetleyebilirsiniz. Anahtar kasalarınızı, anahtarlarınızı, sırları ve sertifikalarınızı yalnızca yetkili kişilerin erişebildiğinden ve yönetebilmesi için emin olun.
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>Veri düzlemi ve erişim ilkeleri

Bir Anahtar Kasası için Key Vault erişim ilkeleri ayarlayarak veri düzlemi erişimi verebilirsiniz. Bu erişim ilkelerini ayarlamak için, bir Kullanıcı, Grup veya uygulamanın `Key Vault Contributor` Bu anahtar kasası için yönetim düzlemi için izinleri olması gerekir.

Bir anahtar kasasındaki anahtarlar veya gizlilikler için belirli işlemleri yürütmek üzere bir Kullanıcı, Grup veya uygulama erişimi verirsiniz. Key Vault, bir Anahtar Kasası için en fazla 1.024 erişim ilkesi girişini destekler. Birkaç kullanıcıya veri düzlemi erişimi sağlamak için bir Azure AD güvenlik grubu oluşturun ve bu gruba kullanıcı ekleyin.

Kasa ve gizli dizi işlemlerinin tam listesini şurada görebilirsiniz: [Key Vault Işlem başvurusu](/rest/api/keyvault/#vault-operations)

<a id="key-vault-access-policies"></a> Key Vault erişim ilkeleri, izinleri anahtarlar, gizlilikler ve sertifikalara ayrı olarak verir.  Anahtarlar, gizli diziler ve sertifikalar için erişim izinleri kasa düzeyindedir. 

Anahtar Kasası erişim ilkeleri kullanma hakkında daha fazla bilgi için bkz. [Key Vault erişim Ilkesi atama](assign-access-policy-portal.md)

> [!IMPORTANT]
> Key Vault erişim ilkeleri kasa düzeyinde geçerlidir. Bir kullanıcıya anahtar oluşturma ve silme izni verildiğinde, bu işlemleri ilgili anahtar kasasındaki tüm anahtarlar üzerinde gerçekleştirebilirler.
Key Vault erişim ilkeleri, belirli bir anahtar, gizli dizi ya da sertifika gibi ayrıntılı, nesne düzeyindeki izinleri desteklemez. 
>

## <a name="data-plane-and-azure-rbac-preview"></a>Veri düzlemi ve Azure RBAC (Önizleme)

Azure rol tabanlı erişim denetimi, tek tek anahtar kasaları üzerinde etkinleştirilebilen Azure Key Vault veri düzlemine erişimi denetlemek için alternatif bir izin modelidir. Azure RBAC izin modeli dışlamalı ve ayarlanmış bir kez kasa erişim ilkeleri devre dışı duruma gelmiştir. Azure Key Vault anahtarlar, gizlilikler veya sertifikalara erişmek için kullanılan ortak izin kümelerini çevreleyen bir dizi Azure yerleşik rol tanımlar.

Azure AD güvenlik sorumlusuna bir Azure rolü atandığında Azure, bu güvenlik sorumlusu için bu kaynaklara erişim izni verir. Erişim, aboneliğin düzeyi, kaynak grubu, Anahtar Kasası veya tek bir anahtar, gizli dizi ya da sertifika kapsamına eklenebilir. Azure AD güvenlik sorumlusu, bir Kullanıcı, Grup, uygulama hizmeti sorumlusu veya [Azure kaynakları için yönetilen bir kimlik](../../active-directory/managed-identities-azure-resources/overview.md)olabilir.

Kasa erişim ilkeleri üzerinde Azure RBAC iznini kullanmanın önemli avantajları, merkezi erişim denetimi yönetimi ve [Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md)ile Tümleştirmesidir. Privileged Identity Management, önem verdiğiniz kaynaklarda aşırı, gereksiz veya kötüye erişim izinlerinin riskini azaltmak için zamana dayalı ve onay tabanlı rol etkinleştirmesi sağlar.

Azure RBAC ile Key Vault veri düzlemi hakkında daha fazla bilgi için bkz. [Azure rol tabanlı erişim denetimi ile Key Vault anahtarlar, sertifikalar ve gizli](rbac-guide.md) diziler

## <a name="firewalls-and-virtual-networks"></a>Güvenlik duvarları ve sanal ağlar

Ek bir güvenlik katmanı için güvenlik duvarlarını ve sanal ağ kurallarını yapılandırabilirsiniz. Key Vault güvenlik duvarlarını ve sanal ağları, varsayılan olarak tüm ağlardan gelen trafiğe (internet trafiği dahil) erişimi reddedecek şekilde yapılandırabilirsiniz. Belirli [Azure sanal ağlarından](../../virtual-network/virtual-networks-overview.md) ve genel İnternet IP adresi aralıklarından trafiğe erişim izni vererek uygulamalarınız için güvenli bir ağ sınırı oluşturabilirsiniz.

Hizmet uç noktalarını nasıl kullanabileceğinizi gösteren bazı örnekler şunlardır:

* Şifreleme anahtarlarını, uygulama gizli dizilerini ve sertifikaları depolamak için Key Vault kullanıyorsunuz ve genel İnternet 'ten anahtar kasanıza erişimi engellemek istiyorsunuz.
* Anahtar kasanıza yalnızca uygulamanızın veya kısa bir ana bilgisayar listesinin bir listesini, anahtar kasanıza bağlanabilmesi için, anahtar kasanıza erişimi kilitlemek istiyorsunuz.
* Azure sanal ağınızda çalışan bir uygulamanız var ve tüm gelen ve giden trafik için bu sanal ağ kilitli. Uygulamanızın parolaları veya sertifikaları getirmek veya şifreleme anahtarlarını kullanmak için Key Vault bağlanması gerekir.

Key Vault güvenlik duvarı ve sanal ağlar hakkında daha fazla bilgi için bkz. [Azure Key Vault güvenlik duvarlarını ve sanal ağları yapılandırma](network-security.md)

> [!NOTE]
> Key Vault güvenlik duvarları ve sanal ağ kuralları yalnızca Key Vault veri düzlemine uygulanır. Key Vault denetim düzlemi işlemleri (oluşturma, silme ve değiştirme işlemleri, erişim ilkelerini ayarlama, güvenlik duvarlarını ayarlama ve sanal ağ kuralları), güvenlik duvarları ve sanal ağ kurallarından etkilenmez.

## <a name="private-endpoint-connection"></a>Özel uç nokta bağlantısı

Genel olarak Key Vault açığa çıkmasına tamamen engel olmak için bir [Azure özel uç noktası](../../private-link/private-endpoint-overview.md) kullanılabilir. Azure özel uç noktası, Azure özel bağlantısı tarafından desteklenen bir hizmete özel ve güvenli bir şekilde bağlanan bir ağ arabirimidir. Özel uç nokta, sanal ağınızdan bir özel IP adresi kullanarak hizmeti sanal ağınıza etkin bir şekilde getiriyor. Hizmete giden tüm trafik özel uç nokta aracılığıyla yönlendirilebilir, bu nedenle ağ geçitleri, NAT cihazları, ExpressRoute veya VPN bağlantıları ya da genel IP adresleri gerekmez. Sanal ağınız ve hizmet arasındaki trafik, Microsoft omurga ağı üzerinden geçer ve genel İnternet’ten etkilenme olasılığı ortadan kaldırılır. Bir Azure kaynağı örneğine bağlanarak, erişim denetimi için en yüksek düzeyde ayrıntı düzeyi sağlayabilirsiniz.

Azure hizmetleri için özel bağlantı kullanımı için genel senaryolar:

- **Azure platformunda özel olarak erişim Hizmetleri**: Sanal ağınızı, kaynak veya hedef üzerinde genel bir IP adresi olmadan Azure 'daki hizmetlere bağlayın. Hizmet sağlayıcıları kendi sanal ağındaki hizmetlerini işleyebilir ve tüketiciler kendi yerel sanal ağında bu hizmetlere erişebilir. Özel bağlantı platformu, Azure omurga ağı üzerinden tüketici ve hizmetler arasındaki bağlantıyı işleymeyecektir. 
 
- Şirket **içi ve eşlenmiş ağlar**: Azure 'Da, ExpressRoute özel EŞLEMESI, VPN tünelleri ve eşlenmiş sanal ağlar üzerinden şirket içinden çalışan hizmetlere özel uç noktalar kullanarak erişin. Hizmete ulaşmak için genel eşlemeyi ayarlama veya internet 'e çapraz geçiş yapma gereksinimi yoktur. Özel bağlantı, iş yüklerini Azure 'a geçirmek için güvenli bir yol sağlar.
 
- **Veri sızıntılarına karşı koruma**: özel bir uç nokta, tüm hizmet yerine bir PaaS kaynağı örneğine eşlenir. Tüketiciler yalnızca belirli bir kaynağa bağlanabilir. Hizmette başka bir kaynağa erişim engellenir. Bu mekanizma, veri sızıntısı risklerine karşı koruma sağlar. 
 
- **Küresel erişim**: diğer bölgelerde çalışan hizmetlere özel olarak bağlanın. Tüketicinin sanal ağı A bölgesinde olabilir ve B bölgesinde özel bağlantı arkasındaki hizmetlere bağlanabilir.  
 
- **Kendi hizmetlerinizi genişletin**: hizmetinizi Azure 'daki tüketicilere özel olarak işlemek için aynı deneyimi ve işlevselliği etkinleştirin. Hizmetinizi standart bir Azure Load Balancer arkasına yerleştirerek özel bağlantı için etkinleştirebilirsiniz. Tüketici daha sonra kendi sanal ağında özel bir uç nokta kullanarak doğrudan hizmetinize bağlanabilir. Bağlantı isteklerini bir onay çağrı akışı kullanarak yönetebilirsiniz. Azure özel bağlantısı, farklı Azure Active Directory kiracılarına ait tüketiciler ve hizmetler için geçerlidir. 

Özel uç noktalar hakkında daha fazla bilgi için bkz. [Azure özel bağlantısı ile Key Vault](./private-link-service.md)

## <a name="example"></a>Örnek

Bu örnekte, TLS/SSL için bir sertifika, verileri depolamak için Azure depolama ve Azure Storage 'daki verileri şifrelemek için RSA 2.048 bitlik bir anahtar kullanan bir uygulama geliştiriyoruz. Uygulamamız bir Azure sanal makinesinde (VM) (veya bir sanal makine ölçek kümesi) çalışır. Uygulama gizli dizileri depolamak için bir Anahtar Kasası kullanabiliriz. Azure AD ile kimlik doğrulaması yapmak için uygulama tarafından kullanılan önyükleme sertifikasını depolayabiliriz.

Aşağıdaki depolanmış anahtarlar ve gizli anahtarlara erişmeniz gerekir:
- **TLS/SSL sertifikası**: TLS/SSL için kullanılır.
- **Depolama anahtarı**: depolama hesabına erişmek için kullanılır.
- **RSA 2.048 bit anahtarı**: Azure depolama tarafından sarmalama/sarmalama verileri şifreleme anahtarı için kullanılır.
- **Uygulama tarafından yönetilen kimlik**: Azure AD kimlik doğrulaması için kullanılır. Key Vault Erişimi verildikten sonra, uygulama depolama anahtarını ve sertifikayı getirebilir.

Uygulamamızı kimin yönetebileceğini, dağıtabileceğinizi ve denetleyeceğinizi belirlemek için aşağıdaki rolleri tanımlamanız gerekir:
- **Güvenlik ekibi**: CSO (Güvenlik Müdürü) veya benzer katkıda bulunanlar ofisindeki BT personeli. Güvenlik ekibi, gizli dizileri doğru bir şekilde ping işlemi yapmaktan sorumludur. Gizlilikler, TLS/SSL sertifikaları, şifreleme için RSA anahtarları, bağlantı dizeleri ve depolama hesabı anahtarları içerebilir.
- **Geliştiriciler ve işleçler**: uygulamayı geliştiren ve Azure 'da dağıtan personel. Bu ekibin üyeleri güvenlik personelinin bir parçası değildir. Bunlar TLS/SSL sertifikaları ve RSA anahtarları gibi hassas verilere erişemez. Yalnızca dağıttıkları uygulamanın gizli verilere erişimi olmalıdır.
- **Denetçiler**: Bu rol, geliştirme veya genel BT personelinin üyesi olmayan katkıda bulunanlar içindir. Güvenlik standartlarıyla uyumluluğu sağlamak için sertifikaların, anahtarların ve parolaların kullanımını ve bakımını gözden geçirir.

Uygulamamızın kapsamı dışında başka bir rol var: abonelik (veya kaynak grubu) Yöneticisi. Abonelik Yöneticisi güvenlik ekibi için ilk erişim izinlerini ayarlar. Uygulama için gerekli kaynaklara sahip bir kaynak grubunu kullanarak güvenlik ekibine erişim izni verir.

Rollerimiz için aşağıdaki işlemleri yetkilendirmemiz gerekir:

**Güvenlik ekibi**
- Anahtar kasaları oluşturun.
- Key Vault günlüğünü açın.
- Anahtar ve gizli dizileri ekleyin.
- Olağanüstü durum kurtarma için anahtarların yedeklerini oluşturun.
- Belirli işlemler için kullanıcılara ve uygulamalara izin vermek üzere Key Vault erişim ilkeleri ayarlayın ve roller atayın.
- Anahtarları ve gizli dizileri düzenli olarak alın.

**Geliştiriciler ve operatörler**
- Hem önyükleme hem TLS/SSL sertifikaları (parmak izleri), depolama anahtarı (gizli URI) ve sarmalama/sarmalama için RSA anahtarı (anahtar URI) için Güvenlik ekibinden başvuru alın.
- Sertifikaları ve gizli dizileri programlı bir şekilde erişmek için uygulamayı geliştirin ve dağıtın.

**Denetçiler**
- Anahtarların ve parolaların doğru kullanımını ve veri güvenliği standartlarıyla uyumluluğunu onaylamak için Key Vault günlüklerini gözden geçirin.

Aşağıdaki tabloda rollerimiz ve uygulamamız için erişim izinleri özetlenmektedir.

| Rol | Yönetim düzlemi izinleri | Veri düzlemi izinleri-kasa erişim ilkeleri | Veri düzlemi izinleri-Azure RBAC  |
| --- | --- | --- | --- |
| Güvenlik ekibi | [Katkıda bulunan Key Vault](../../role-based-access-control/built-in-roles.md#key-vault-contributor) | Sertifikalar: tüm işlemler <br> Anahtarlar: tüm işlemler <br> Gizlilikler: tüm işlemler | [Key Vault Yöneticisi](../../role-based-access-control/built-in-roles.md#key-vault-administrator) |
| Geliştiriciler ve &nbsp; işleçler | Key Vault dağıtma izni<br><br> **Note**: Bu izin, dağıtılan VM 'lerin bir anahtar kasasından gizli dizileri almasına izin verir. | Yok | Yok |
| Denetçiler | Yok | Sertifikalar: liste <br> Anahtarlar: listeleme<br>Parolalar: listeleme<br><br> **Not**: Bu izin, denetçilerin, günlüklere yayılmayan anahtarlar ve gizli diziler için öznitelikleri (Etiketler, etkinleştirme tarihleri, sona erme tarihleri) incelemeye olanak sağlar. | [Key Vault okuyucu](../../role-based-access-control/built-in-roles.md#key-vault-reader) |
| Azure Depolama Hesabı | Yok | Anahtarlar: get, List, wrapKey, unwrapKey <br> | [Key Vault şifreleme hizmeti şifreleme kullanıcısı](../../role-based-access-control/built-in-roles.md#key-vault-crypto-service-encryption-user) |
| Uygulama | Yok | Gizlilikler: get, List <br> Sertifikalar: get, List | [Key Vault okuyucu](../../role-based-access-control/built-in-roles.md#key-vault-reader), [Key Vault gizli Kullanıcı](../../role-based-access-control/built-in-roles.md#key-vault-secrets-user) |

Üç takım rolünün, Key Vault izinlerle birlikte diğer kaynaklara erişmesi gerekir. VM 'Leri (veya Azure App Service Web Apps özelliğini) dağıtmak için, geliştiricilere ve operatörlere erişim dağıtımı gerekir. Denetçilerin Key Vault günlüklerinin depolandığı depolama hesabına okuma erişimi olması gerekir.

Örneğimizde basit bir senaryo açıklanmaktadır. Gerçek yaşam senaryoları daha karmaşık olabilir. Gereksinimlerinize göre anahtar kasanıza yönelik izinleri ayarlayabilirsiniz. Güvenlik ekibinin, uygulamalarında DevOps personeli tarafından kullanılan anahtar ve gizli başvuruları (URI 'Ler ve parmak izleri) sağladığını kabul ediyoruz. Geliştiricilere ve işleçlere herhangi bir veri düzlemi erişimi gerekmez. Anahtar kasanızın güvenliğini sağlama konusunda odaklandık.

> [!NOTE]
> Bu örnek, Key Vault erişimin üretimde nasıl kilitli olduğunu gösterir. Geliştiricilerin, kasalarını, VM 'Leri ve uygulamayı geliştirdikleri depolama hesabını yönetmek için tam izinlerle kendi aboneliğine veya kaynak grubuna sahip olmaları gerekir.

## <a name="resources"></a>Kaynaklar

- [Azure Key Vault hakkında](overview.md)
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
- [Azure RBAC](../../role-based-access-control/overview.md)
- [Özel Bağlantı](../../private-link/private-link-overview.md)

## <a name="next-steps"></a>Sonraki adımlar

[Azure Key Vault'ta kimliği doğrulama](authentication.md)

[Key Vault erişim ilkesi atama](assign-access-policy-portal.md)

[Anahtarlar, gizlilikler ve sertifikalara erişim için Azure rolü atama](rbac-guide.md)

[Key Vault güvenlik duvarlarını ve sanal ağları yapılandırma](network-security.md)

[Key Vault özel bağlantı bağlantısı oluşturma](private-link-service.md)
