---
title: Azure AD kiracı uygulaması taleplerini (PowerShell) özelleştirme
titleSuffix: Microsoft identity platform
description: Bu sayfada Azure Active Directory talep eşleştirmesi açıklanmaktadır.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 2d65889a841655fe27994d3855f30f7a7e20e1ed
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94647605"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Nasıl yapılır: bir Kiracıdaki belirli bir uygulama için belirteçlerde yayılan talepleri özelleştirme (Önizleme)

> [!NOTE]
> Bu özellik, bugün Portal üzerinden sunulan [talep özelleştirmelerinin](active-directory-saml-claims-customization.md) yerini alır ve yerini alır. Aynı uygulamada, bu belgede ayrıntılı grafik/PowerShell yöntemine ek olarak portalı kullanarak talepleri özelleştirirseniz, bu uygulama için verilen belirteçler portalda yapılandırmayı yoksayar. Bu belgede açıklanan yöntemler aracılığıyla yapılan yapılandırmaların portala yansıtılmayacak.

Bu özellik kiracı yöneticileri tarafından kiracısındaki belirli bir uygulama için belirteçlerde bulunan talepleri özelleştirmek üzere kullanılır. Talep eşleme ilkelerini kullanarak şunları yapabilirsiniz:

- Belirteçlere hangi taleplerin ekleneceğini seçin.
- Zaten mevcut olmayan talep türleri oluşturun.
- Belirli taleplerde yayılan verilerin kaynağını seçin veya değiştirin.

> [!NOTE]
> Bu özellik şu anda genel önizlemededir. Değişiklikleri geri almaya veya kaldırmaya hazırlıklı olun. Bu özellik, genel önizleme sırasında herhangi bir Azure Active Directory (Azure AD) aboneliğinde kullanılabilir. Ancak özellik genel kullanıma sunulduğunda, özelliğin bazı yönleri bir Azure AD Premium aboneliği gerektirebilir. Bu özellik, WS-beslenir, SAML, OAuth ve OpenID Connect protokolleri için talep eşleme ilkelerinin yapılandırılmasını destekler.

## <a name="claims-mapping-policy-type"></a>Talep eşleme ilkesi türü

Azure AD 'de bir **ilke** nesnesi, tek tek uygulamalarda veya bir kuruluştaki tüm uygulamalarda zorlanan bir kural kümesini temsil eder. Her ilke türünün, atandığı nesnelere uygulanan bir özellikler kümesi ile benzersiz bir yapısı vardır.

Talep eşleme ilkesi, belirli uygulamalar için verilen belirteçlerde yayılan talepleri değiştiren bir **ilke** nesnesi türüdür.

## <a name="claim-sets"></a>Talep kümeleri

Belirteçlerde nasıl ve ne zaman kullanıldığını tanımlayan belirli talepler kümesi vardır.

| Talep kümesi | Description |
|---|---|
| Çekirdek talep kümesi | , İlkeden bağımsız olarak her belirteçte bulunur. Bu talepler de kısıtlı olarak değerlendirilir ve değiştirilemez. |
| Temel talep kümesi | Belirteçleri için varsayılan olarak yayılan talepleri içerir (çekirdek talep kümesine ek olarak). Talepler eşleme ilkelerini kullanarak temel talepleri atlayabilir veya değiştirebilirsiniz. |
| Kısıtlı talep kümesi | İlke kullanılarak değiştirilemez. Veri kaynağı değiştirilemez ve bu talepler oluşturulurken hiçbir dönüştürme uygulanmaz. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Tablo 1: JSON Web Token (JWT) sınırlı talep kümesi

| Talep türü (ad) |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| aktör |
| actortoken |
| AIO 'yu |
| AltSecId |
| AMR |
| app_chain |
| app_displayname |
| app_res |
| appctx |
| appctxsender |
| AppID |
| appidadcr |
| onay |
| at_hash |
| aud |
| auth_data |
| auth_time |
| authorization_code |
| AZP |
| azpacr |
| c_hash |
| ca_enf |
| cc |
| cert_token_use |
| client_id |
| cloud_graph_host_name |
| cloud_instance_name |
| CNF |
| kod |
| denetimler |
| credential_keys |
| 'nin |
| csr_type |
| DeviceID |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| e-posta |
| endpoint |
| enfpolids |
| exp |
| expires_on |
| grant_type |
| çıkarılamıyor |
| group_sids |
| gruplar |
| hasgroups |
| hash_alg |
| home_oid |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expired` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` |
| IAT |
| IdentityProvider |
| IDP |
| in_corp |
| örnek |
| ipaddr |
| isbrowserhostedapp |
| ğe |
| jwk |
| key_id |
| key_type |
| mam_compliance_url |
| mam_enrollment_url |
| mam_terms_of_use_url |
| mdm_compliance_url |
| mdm_enrollment_url |
| mdm_terms_of_use_url |
| NameID |
| NBF |
| netbios_name |
| nonce |
| id |
| on_prem_id |
| onprem_sam_account_name |
| onprem_sid |
| openid2_id |
| password |
| siyalar |
| pop_jwk |
| preferred_username |
| previous_refresh_token |
| primary_sid |
| 'i |
| pwd_exp |
| pwd_url |
| redirect_uri |
| refresh_token |
| refreshtoken |
| request_nonce |
| kaynak |
| rol |
| roller |
| scope |
| 'yi |
| SID |
| imza |
| signin_state |
| src1 |
| src2 |
| alt |
| tbıd |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| değeri |
| Tokenautosize etkin |
| trustedfortemsilciyi |
| unique_name |
| 'le |
| user_setting_sync_url |
| username |
| UTI |
| ver |
| verified_primary_email |
| verified_secondary_email |
| WDS |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>Tablo 2: SAML kısıtlı talep kümesi

| Talep türü (URI) |
| ----- |
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expired`|
|`http://schemas.microsoft.com/identity/claims/accesstoken`|
|`http://schemas.microsoft.com/identity/claims/openid2_id`|
|`http://schemas.microsoft.com/identity/claims/identityprovider`|
|`http://schemas.microsoft.com/identity/claims/objectidentifier`|
|`http://schemas.microsoft.com/identity/claims/puid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier [MR1]`|
|`http://schemas.microsoft.com/identity/claims/tenantid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod`|
|`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`|
|`http://schemas.microsoft.com/claims/groups.link`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/wids`|
|`http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant`|
|`http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown`|
|`http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged`|
|`http://schemas.microsoft.com/2014/03/psso`|
|`http://schemas.microsoft.com/claims/authnmethodsreferences`|
|`http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier`|
|`http://schemas.microsoft.com/identity/claims/scope`|

## <a name="claims-mapping-policy-properties"></a>Talep eşleme ilkesi özellikleri

Hangi taleplerin yayıldığını ve verilerin nereden geldiğini denetlemek için, bir talep eşleme ilkesinin özelliklerini kullanın. Bir ilke ayarlanmamışsa sistem, çekirdek talep kümesi, temel talep kümesi ve uygulamanın almak üzere seçtiği tüm [isteğe bağlı talepler](active-directory-optional-claims.md) içeren belirteçler yayınlar.

> [!NOTE]
> Çekirdek talep kümesindeki talepler, bu özelliğin ne şekilde ayarlandığına bakılmaksızın her belirteçte mevcuttur.

### <a name="include-basic-claim-set"></a>Temel talep kümesini dahil et

**Dize:** Includebasicclaimset

**Veri türü:** Boole (true veya false)

**Özet:** Bu özellik, temel talep kümesinin bu ilkeden etkilenen belirteçlere dahil edilip edilmeyeceğini belirler.

- True olarak ayarlanırsa, temel talep kümesindeki tüm talepler, ilkeden etkilenen belirteçlerde dağıtılır.
- False olarak ayarlanırsa, temel talep kümesindeki talepler, aynı ilkenin talep şeması özelliğine tek eklenmedikleri takdirde, belirteçlerde değildir.



### <a name="claims-schema"></a>Talep şeması

**Dize:** ClaimsSchema

**Veri türü:** Bir veya daha fazla talep şeması girişi içeren JSON blobu

**Özet:** Bu özellik, temel talep kümesine ve çekirdek talep kümesine ek olarak, ilkeden etkilenen belirteçlerde hangi taleplerin mevcut olduğunu tanımlar.
Bu özellikte tanımlanan her talep şeması girişi için bazı bilgiler gereklidir. Verilerin (**değer**, **kaynak/kimlik çiftinin** veya **kaynak/extensionID çiftinin**) nereye geldiğini ve verilerin hangi talebe göre (**talep türü**) yayınlandığını belirtin.

### <a name="claim-schema-entry-elements"></a>Talep şeması giriş öğeleri

**Değer:** Value öğesi, bir statik değeri, talepteki veri olarak tanımlar.

**Kaynak/kimlik çifti:** Kaynak ve KIMLIK öğeleri, talepteki verilerin kaynağını belirler.

**Kaynak/extensionID çifti:** Kaynak ve extensionID öğeleri, talepteki verilerin kaynağı olan dizin şeması uzantısı özniteliğini tanımlar. Daha fazla bilgi için bkz. [taleplerde Dizin şeması uzantısı özniteliklerini kullanma](active-directory-schema-extensions.md).

Kaynak öğeyi aşağıdaki değerlerden birine ayarlayın:

- "Kullanıcı": talepteki veriler, kullanıcı nesnesindeki bir özelliktir.
- "uygulama": talepteki veriler, uygulama (istemci) hizmet sorumlusu üzerindeki bir özelliktir.
- "kaynak": talepteki veriler, kaynak hizmeti sorumlusu üzerindeki bir özelliktir.
- "hedef kitle": talepteki veriler, belirtecin hedef kitlesi olan hizmet sorumlusu üzerindeki bir özelliktir (istemci ya da kaynak hizmet sorumlusu).
- "Şirket": talepteki veriler, kaynak kiracının şirket nesnesindeki bir özelliktir.
- "dönüşüm": talepteki veriler talep dönüşümden (Bu makalenin ilerleyen kısımlarında "talep dönüştürme" bölümüne bakın).

Kaynak dönüşümde ise, dönüştürme işlemi **kimliği** öğesi bu talep tanımına da dahil olmalıdır.

ID öğesi, kaynak üzerinde hangi özelliğin talep için değer sağladığını tanımlar. Aşağıdaki tabloda her kaynak değeri için geçerli olan KIMLIK değerleri listelenmektedir.

#### <a name="table-3-valid-id-values-per-source"></a>Tablo 3: kaynak başına geçerli KIMLIK değerleri

| Kaynak | ID | Description |
|-----|-----|-----|
| Kullanıcı | surname | Aile adı |
| Kullanıcı | givenname | Verilen Ad |
| Kullanıcı | DisplayName | Görünen Ad |
| Kullanıcı | uzantının | ObjectID |
| Kullanıcı | posta | E-posta Adresi |
| Kullanıcı | userPrincipalName | Kullanıcı Asıl Adı |
| Kullanıcı | bölüm|Bölüm|
| Kullanıcı | onpremisessamaccountname | Şirket içi SAM hesap adı |
| Kullanıcı | NetbiosName| NetBIOS adı |
| Kullanıcı | DN | DNS Etki Alanı Adı |
| Kullanıcı | onpremisesecurityidentifier | Şirket içi güvenlik tanımlayıcısı |
| Kullanıcı | tadı| Organizasyon Adı |
| Kullanıcı | StreetAddress | Adres |
| Kullanıcı | PostalCode | Posta Kodu |
| Kullanıcı | PreferredLanguage | Tercih edilen dil |
| Kullanıcı | onpremisesuserprincipalname | Şirket içi UPN |*
| Kullanıcı | mailNickname | Posta takma adı |
| Kullanıcı | extensionattribute1 | Uzantı özniteliği 1 |
| Kullanıcı | extensionattribute2 | Uzantı özniteliği 2 |
| Kullanıcı | extensionattribute3 | Uzantı özniteliği 3 |
| Kullanıcı | extensionattribute4 | Uzantı özniteliği 4 |
| Kullanıcı | extensionattribute5 | Uzantı özniteliği 5 |
| Kullanıcı | extensionattribute6 | Uzantı özniteliği 6 |
| Kullanıcı | extensionattribute7 | Uzantı özniteliği 7 |
| Kullanıcı | extensionattribute8 | Uzantı özniteliği 8 |
| Kullanıcı | extensionattribute9 | Uzantı özniteliği 9 |
| Kullanıcı | extensionattribute10 | Uzantı özniteliği 10 |
| Kullanıcı | extensionattribute11 | Uzantı özniteliği 11 |
| Kullanıcı | extensionattribute12 | Uzantı özniteliği 12 |
| Kullanıcı | extensionattribute13 | Uzantı özniteliği 13 |
| Kullanıcı | extensionattribute14 | Uzantı özniteliği 14 |
| Kullanıcı | extensionattribute15 | Uzantı özniteliği 15 |
| Kullanıcı | diğer posta | Diğer posta |
| Kullanıcı | ülke | Ülke/Bölge |
| Kullanıcı | city | Şehir |
| Kullanıcı | state | Durum |
| Kullanıcı | JobTitle | İş Unvanı |
| Kullanıcı | çalışan | Çalışan Numarası |
| Kullanıcı | facsimileTelephoneNumber 'dir | Facsıle telefon numarası |
| Kullanıcı | atanan | kullanıcıya atanan uygulama rollerinin listesi|
| uygulama, kaynak, hedef kitle | DisplayName | Görünen Ad |
| uygulama, kaynak, hedef kitle | uzantının | ObjectID |
| uygulama, kaynak, hedef kitle | etiketler | Hizmet sorumlusu etiketi |
| Şirket | tenantcountry | Kiracının ülkesi/bölgesi |

**Dönüştürme kimliği:** Dönüşümtionıd öğesi yalnızca kaynak öğe "dönüşüm" olarak ayarlandıysa sağlanmalıdır.

- Bu öğe, bu talep için verilerin nasıl oluşturulduğunu tanımlayan **Claimstrans,** özelliğindeki dönüştürme girişinin ID öğesiyle aynı olmalıdır.

**Talep türü:** **Jwtclaimtype** ve **samlclaimtype** öğeleri, bu talep şeması girişinin hangi talebe başvurduğunu tanımlar.

- JwtClaimType, JWTs 'de yayınlankullanılacak talebin adını içermelidir.
- SamlClaimType, SAML belirteçlerine yayınlaneklenecek talebin URI 'sini içermelidir.

* **onPremisesUserPrincipalName özniteliği:** Alternatif KIMLIK kullanılırken, şirket içi öznitelik userPrincipalName, onPremisesUserPrincipalName Azure AD özniteliğiyle eşitlenir. Bu öznitelik yalnızca alternatif KIMLIK yapılandırıldığında kullanılabilir ancak MS Graph Beta ile de kullanılabilir: https://graph.microsoft.com/beta/me/ .

> [!NOTE]
> Kısıtlanmış talep kümesindeki taleplerin adları ve URI 'Leri talep türü öğeleri için kullanılamaz. Daha fazla bilgi için, bu makalenin devamındaki "özel durumlar ve kısıtlamalar" bölümüne bakın.

### <a name="claims-transformation"></a>Talepleri dönüştürme

**Dize:** Claimstranssize

**Veri türü:** JSON blobu, bir veya daha fazla dönüştürme girdisi

**Özet:** Bu özelliği, talep şemasında belirtilen talepler için çıkış verilerini oluşturmak üzere kaynak verilere ortak dönüşümler uygulamak için kullanın.

**Kimliği:** Transformation Tionıd talep şeması girişinde bu dönüşüm girişine başvurmak için ID öğesini kullanın. Bu değer, bu ilkedeki her bir dönüştürme girişi için benzersiz olmalıdır.

**Dönüştürme yöntemi:** Dönüştürme Tionmethod öğesi, talep için verileri oluşturmak üzere hangi işlemin gerçekleştirildiğini tanımlar.

Seçilen yönteme bağlı olarak bir dizi giriş ve çıkış beklenmektedir. Giriş ve çıkışları **ınputclaim**, **InputParameters** ve **outputclaim** öğelerini kullanarak tanımlayın.

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Tablo 4: dönüştürme yöntemleri ve beklenen girişler ve çıktılar

|Dönüştürme Tionmethod|Beklenen giriş|Beklenen çıkış|Description|
|-----|-----|-----|-----|
|Katılın|dize1, dize2, ayırıcı|outputClaim|Arasında bir ayırıcı kullanarak girdi dizelerini birleştirir. Örneğin: Dize1: " foo@bar.com ", dize2: "Sandbox", ayırıcı: "." outputClaim 'de sonuçlar: " foo@bar.com.sandbox "|
|ExtractMailPrefix|E-posta veya UPN|ayıklanan dize|ExtensionAttributes 1-15 veya Kullanıcı için bir UPN ya da e-posta adresi değeri depolayan diğer şema uzantıları gibi johndoe@contoso.com . Bir e-posta adresinin yerel bölümünü ayıklar. Örneğin: posta: " foo@bar.com " outputClaim sonucu: "foo". Hiçbir \@ işaret yoksa, özgün giriş dizesi olduğu gibi döndürülür.|

**Inputclaim:** Bir talep şeması girdisinden bir dönüşüme veri geçirmek için ınputclaim öğesi kullanın. İki özniteliğe sahiptir: **ClaimTypeReferenceId** ve **dönüştürülebilir tionclaimtype**.

- **ClaimTypeReferenceId** , uygun giriş talebini bulmak için talep ŞEMASı girişinin ID öğesiyle birleştirilir.
- Bu girişe benzersiz bir ad vermek için **dönüştürme Işlemi ClaimType** kullanılır. Bu ad, dönüşüm yöntemi için beklenen girdilerden biriyle eşleşmelidir.

**InputParameters:** Bir dönüşüme sabit değer geçirmek için InputParameters öğesi kullanın. İki özniteliğe sahiptir: **değer** ve **kimlik**.

- **Değer** geçirilecek gerçek sabit değerdir.
- Girişe benzersiz bir ad vermek için **kimlik** kullanılır. Ad, dönüşüm yöntemi için beklenen girdilerden biriyle eşleşmelidir.

**Outputclaim:** Bir dönüştürme tarafından oluşturulan verileri tutmak ve bir talep şeması girişine bağlamak için bir Outputclaim öğesi kullanın. İki özniteliğe sahiptir: **ClaimTypeReferenceId** ve **dönüştürülebilir tionclaimtype**.

- **ClaimTypeReferenceId** , uygun çıkış talebini bulmak için talep ŞEMASı girişinin kimliğiyle birleştirilir.
- **Dönüştürme** , çıkışa benzersiz bir ad vermek için kullanılır. Ad, dönüştürme yöntemi için beklenen çıktılardan biriyle eşleşmelidir.

### <a name="exceptions-and-restrictions"></a>Özel durumlar ve kısıtlamalar

**SAML NameID ve UPN:** NameID ve UPN değerlerini ve izin verilen talep dönüştürmelerini kaynak olarak belirten öznitelikler sınırlıdır. İzin verilen değerleri görmek için bkz. Tablo 5 ve tablo 6.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Tablo 5: SAML NameID için veri kaynağı olarak izin verilen öznitelikler

|Kaynak|ID|Description|
|-----|-----|-----|
| Kullanıcı | posta|E-posta Adresi|
| Kullanıcı | userPrincipalName|Kullanıcı Asıl Adı|
| Kullanıcı | onpremisessamaccountname|Şirket Içi Sam hesap adı|
| Kullanıcı | çalışan|Çalışan Numarası|
| Kullanıcı | extensionattribute1 | Uzantı özniteliği 1 |
| Kullanıcı | extensionattribute2 | Uzantı özniteliği 2 |
| Kullanıcı | extensionattribute3 | Uzantı özniteliği 3 |
| Kullanıcı | extensionattribute4 | Uzantı özniteliği 4 |
| Kullanıcı | extensionattribute5 | Uzantı özniteliği 5 |
| Kullanıcı | extensionattribute6 | Uzantı özniteliği 6 |
| Kullanıcı | extensionattribute7 | Uzantı özniteliği 7 |
| Kullanıcı | extensionattribute8 | Uzantı özniteliği 8 |
| Kullanıcı | extensionattribute9 | Uzantı özniteliği 9 |
| Kullanıcı | extensionattribute10 | Uzantı özniteliği 10 |
| Kullanıcı | extensionattribute11 | Uzantı özniteliği 11 |
| Kullanıcı | extensionattribute12 | Uzantı özniteliği 12 |
| Kullanıcı | extensionattribute13 | Uzantı özniteliği 13 |
| Kullanıcı | extensionattribute14 | Uzantı özniteliği 14 |
| Kullanıcı | extensionattribute15 | Uzantı özniteliği 15 |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Tablo 6: SAML NameID için izin verilen dönüştürme yöntemleri

| Dönüştürme Tionmethod | Kısıtlamalar |
| ----- | ----- |
| ExtractMailPrefix | Yok |
| Katılın | Katılmakta olan sonekin, kaynak kiracının doğrulanmış bir etki alanı olması gerekir. |

### <a name="custom-signing-key"></a>Özel imzalama anahtarı

Bir talep eşleme ilkesinin etkili olması için hizmet sorumlusu nesnesine özel bir imzalama anahtarı atanmalıdır. Bu, belirteçlerin talep eşleme ilkesinin Oluşturucusu tarafından değiştirildiğini ve uygulamaların kötü amaçlı aktörler tarafından oluşturulan talep eşleme ilkelerine karşı korunmasını sağlar. Özel bir imzalama anahtarı eklemek için, [`New-AzureADApplicationKeyCredential`](/powerShell/module/Azuread/New-AzureADApplicationKeyCredential) uygulama nesneniz için bir sertifika anahtarı kimlik bilgisi oluşturmak üzere Azure PowerShell cmdlet 'ini kullanabilirsiniz.

Talep eşlemesi etkin olan uygulamalar `appid={client_id}` , kendi [OpenID Connect meta veri isteklerine](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document)ekleyerek belirteç imzalama anahtarlarını doğrulamalıdır. Aşağıda, kullanmanız gereken OpenID Connect meta veri belgesinin biçimi verilmiştir:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="cross-tenant-scenarios"></a>Çapraz kiracı senaryoları

Talep eşleme ilkeleri Konuk kullanıcılar için uygulanmaz. Konuk Kullanıcı, hizmet sorumlusuna atanmış bir talep eşleme ilkesiyle bir uygulamaya erişmeyi denediğinde, varsayılan belirteç verilir (ilkenin hiçbir etkisi yoktur).

## <a name="claims-mapping-policy-assignment"></a>Talep eşleme ilkesi ataması

Talep eşleme ilkeleri, yalnızca hizmet sorumlusu nesnelerine atanabilir.

### <a name="example-claims-mapping-policies"></a>Örnek talep eşleme ilkeleri

Azure AD 'de, belirli hizmet sorumluları için belirteçlerde yayılan talepleri özelleştirebilmeniz için birçok senaryo mümkündür. Bu bölümde, talep eşleme ilkesi türünü nasıl kullanacağınızı belirlemenize yardımcı olabilecek birkaç yaygın senaryoya kılavuzluk ederiz.

Bir talep eşleme ilkesi oluştururken, belirteçlerdeki Dizin şeması uzantısı özniteliğinden bir talep da oluşturabilirsiniz. Öğesinde *ID* yerine Extension özniteliği Için *extensionID* kullanın `ClaimsSchema` .  Uzantı öznitelikleri hakkında daha fazla bilgi için bkz. [Dizin şeması uzantısı özniteliklerini kullanma](active-directory-schema-extensions.md).

#### <a name="prerequisites"></a>Önkoşullar

Aşağıdaki örneklerde, hizmet sorumluları için ilkeleri oluşturur, güncelleştirir, bağlar ve silebilirsiniz. Azure AD 'de yeni başladıysanız, bu örneklere geçmeden önce [bir Azure AD kiracısı alma hakkında bilgi](quickstart-create-new-tenant.md) almanızı öneririz.

Başlamak için aşağıdaki adımları uygulayın:

1. En son [Azure AD PowerShell modülü genel önizleme sürümünü](https://www.powershellgallery.com/packages/AzureADPreview)indirin.
1. Azure AD yönetici hesabınızda oturum açmak için Connect komutunu çalıştırın. Her yeni oturumu başlattığınızda bu komutu çalıştırın.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. Kuruluşunuzda oluşturulan tüm ilkeleri görmek için aşağıdaki komutu çalıştırın. İlkelerinizin beklenen şekilde oluşturulduğunu denetlemek için, aşağıdaki senaryolarda işlemlerden en çok işlem sonrasında bu komutu çalıştırmanızı öneririz.

   ``` powershell
   Get-AzureADPolicy
   ```

#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Örnek: bir hizmet sorumlusuna verilen belirteçlerden temel talepleri atlamak için bir ilke oluşturun ve atayın

Bu örnekte, bağlı hizmet sorumlularına verilen belirteçlerden temel talep kümesini kaldıran bir ilke oluşturursunuz.

1. Talep eşleme ilkesi oluşturun. Belirli hizmet sorumlularına bağlı olan bu ilke, temel talep kümesini belirteçlerden kaldırır.
   1. İlkeyi oluşturmak için şu komutu çalıştırın:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Yeni ilkenize bakmak ve ilke ObjectID 'yi almak için aşağıdaki komutu çalıştırın:

      ``` powershell
      Get-AzureADPolicy
      ```
1. İlkeyi hizmet sorumlusuna atayın. Ayrıca hizmet sorumlunun ObjectID 'sini almanız gerekir.
   1. Tüm kuruluşunuzun hizmet sorumlularını görmek için [MICROSOFT Graph API 'sini sorgulayabilirsiniz](/graph/traverse-the-graph). Veya [Microsoft Graph Gezgini](https://developer.microsoft.com/graph/graph-explorer)' nde Azure AD hesabınızda oturum açın.
   2. Hizmet sorumlunuz ObjectID 'niz varsa, aşağıdaki komutu çalıştırın:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Örnek: bir hizmet sorumlusu tarafından verilen belirteçlere talepler olarak EmployeeID ve TenantCountry dahil etmek için bir ilke oluşturun ve atayın

Bu örnekte, EmployeeID ve TenantCountry ' ı bağlı hizmet sorumlularına verilen belirteçlere ekleyen bir ilke oluşturacaksınız. ÇalışanNo, hem SAML belirteçlerinde hem de JWTs 'de ad talep türü olarak yayınlanır. TenantCountry, hem SAML belirteçlerinde hem de JWTs 'de ülke/bölge talep türü olarak yayınlanır. Bu örnekte, belirteçlere temel talepler kümesini eklemeye devam ediyoruz.

1. Talep eşleme ilkesi oluşturun. Bu ilke, belirli hizmet sorumlularıyla bağlantılı olarak, ÇalışanNo ve TenantCountry taleplerini belirteçlere ekler.
   1. İlkeyi oluşturmak için aşağıdaki komutu çalıştırın:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Yeni ilkenize bakmak ve ilke ObjectID 'yi almak için aşağıdaki komutu çalıştırın:

      ``` powershell
      Get-AzureADPolicy
      ```
1. İlkeyi hizmet sorumlusuna atayın. Ayrıca hizmet sorumlunun ObjectID 'sini almanız gerekir.
   1. Tüm kuruluşunuzun hizmet sorumlularını görmek için [MICROSOFT Graph API 'sini sorgulayabilirsiniz](/graph/traverse-the-graph). Veya [Microsoft Graph Gezgini](https://developer.microsoft.com/graph/graph-explorer)' nde Azure AD hesabınızda oturum açın.
   2. Hizmet sorumlunuz ObjectID 'niz varsa, aşağıdaki komutu çalıştırın:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Örnek: bir hizmet sorumlusuna verilen belirteçlerde talep dönüştürmesi kullanan bir ilke oluşturma ve atama

Bu örnekte, bağlantılı hizmet sorumlularına verilen JWTs 'e "JoinedData" özel talebi yayan bir ilke oluşturacaksınız. Bu talep, kullanıcı nesnesindeki extensionAttribute1 özniteliğinde depolanan verileri ". Sandbox" ile birleştirerek oluşturulmuş bir değer içerir. Bu örnekte, belirteçlerde ayarlanan temel talepler hariç tutuyoruz.

1. Talep eşleme ilkesi oluşturun. Bu ilke, belirli hizmet sorumlularıyla bağlantılı olarak, ÇalışanNo ve TenantCountry taleplerini belirteçlere ekler.
   1. İlkeyi oluşturmak için aşağıdaki komutu çalıştırın:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Yeni ilkenize bakmak ve ilke ObjectID 'yi almak için aşağıdaki komutu çalıştırın:

      ``` powershell
      Get-AzureADPolicy
      ```
1. İlkeyi hizmet sorumlusuna atayın. Ayrıca hizmet sorumlunun ObjectID 'sini almanız gerekir.
   1. Tüm kuruluşunuzun hizmet sorumlularını görmek için [MICROSOFT Graph API 'sini sorgulayabilirsiniz](/graph/traverse-the-graph). Veya [Microsoft Graph Gezgini](https://developer.microsoft.com/graph/graph-explorer)' nde Azure AD hesabınızda oturum açın.
   2. Hizmet sorumlunuz ObjectID 'niz varsa, aşağıdaki komutu çalıştırın:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="see-also"></a>Ayrıca bkz.

- SAML belirtecinde verilen talepleri Azure portal aracılığıyla özelleştirmeyi öğrenmek için bkz. [nasıl yapılır: kurumsal uygulamalar IÇIN SAML belirtecinde verilen talepleri özelleştirme](active-directory-saml-claims-customization.md)
- Uzantı öznitelikleri hakkında daha fazla bilgi edinmek için bkz. [taleplerde Dizin şeması uzantısı özniteliklerini kullanma](active-directory-schema-extensions.md).
