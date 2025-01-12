---
title: Özel ilkeleri Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C özel ilkeler hakkında bilgi edinin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/06/2019
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 043cf83d804803e1b5b47d3ac51bbccaa06e4e87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87116420"
---
# <a name="custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C özel ilkeler

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Özel ilkeler, Azure Active Directory B2C (Azure AD B2C) kiracınızın davranışlarını tanımlayan yapılandırma dosyalarıdır. AAD B2C portalında en yaygın kimlik görevleri için kullanıcı akışları önceden tanımlanmıştır. Kimlik geliştiriciler özel ilkeleri tamamen düzenleyerek birçok farklı görevi tamamlamaya uygun hale getirebilirler.

## <a name="comparing-user-flows-and-custom-policies"></a>Kullanıcı akışlarını ve özel ilkeleri karşılaştırma

| Bağlam | Kullanıcı akışları | Özel ilkeler |
|-|-------------------|-----------------|
| Hedef kullanıcılar | Kimlik uzmanlığına sahip veya olmayan tüm uygulama geliştiricileri. | Kimlik uzmanları, sistem tümleştiricileri, danışmanları ve şirket içi kimlik takımları. OpenID Connect akışlarıyla rahat ve kimlik sağlayıcılarını ve talep tabanlı kimlik doğrulamasını anlayın. |
| Yapılandırma yöntemi | Kullanıcı dostu Kullanıcı arabirimi (UI) ile Azure portal. | XML dosyalarını doğrudan düzenleyip Azure portal karşıya yükleyin. |
| UI özelleştirmesi | HTML, CSS ve JavaScript gibi tam UI özelleştirmesi.<br><br>Özel dizelerle çok dilli destek. | Aynı |
| Öznitelik özelleştirmesi | Standart ve özel öznitelikler. | Aynı |
| Belirteç ve oturum yönetimi | Özel belirteç ve çoklu oturum seçenekleri. | Aynı |
| Kimlik Sağlayıcıları | Önceden tanımlanmış yerel veya sosyal sağlayıcı ve Azure Active Directory kiracılar ile Federasyon gibi çoğu OıDC kimlik sağlayıcısı. | Standartlara dayalı OıDC, OAUTH ve SAML.  REST API 'lerle tümleştirme kullanılarak da kimlik doğrulaması mümkündür. |
| Kimlik görevleri | Yerel veya çok sayıda sosyal hesapla kaydolma veya oturum açma.<br><br>Self servis parola sıfırlama.<br><br>Profil düzenleme.<br><br>Multi-Factor Authentication.<br><br>Belirteçleri ve oturumları özelleştirin.<br><br>Belirteç akışlarına erişin. | Özel kimlik sağlayıcılarını kullanarak Kullanıcı akışlarıyla aynı görevleri tamamlayıp özel kapsamlar kullanın.<br><br>Kayıt sırasında başka bir sistemde bir kullanıcı hesabı sağlayın.<br><br>Kendi e-posta hizmet sağlayıcınızı kullanarak bir hoş geldiniz e-postası gönderin.<br><br>Azure AD B2C dışında bir kullanıcı deposu kullanın.<br><br>Bir API kullanarak güvenilir bir sistemle Kullanıcı tarafından sağlanmış bilgileri doğrulayın. |

## <a name="policy-files"></a>İlke dosyaları

Bu üç tür ilke dosyası kullanılır:

- **Temel dosya** -tanımların çoğunu içerir. Sorun gidermenize yardımcı olması için bu dosyada en az sayıda değişiklik yapmanız ve ilkeleriniz için uzun süreli bakım yapmanız önerilir.
- **Uzantılar dosyası** -kiracınız için benzersiz yapılandırma değişiklikleri tutar.
- **Bağlı olan taraf (RP) dosyası** -uygulama veya hizmet tarafından doğrudan çağrılan tek görev odaklı dosya (Ayrıca, bağlı olan taraf olarak da bilinir). Her benzersiz görev kendi RP gerektirir ve marka gereksinimlerine bağlı olarak, sayı "Toplam uygulama sayısı x Toplam kullanım örneği sayısı" olabilir.

Azure AD B2C Kullanıcı akışları yukarıda gösterilen dosya modelini izler, ancak geliştirici RP dosyasını yalnızca, Azure portal arka planda uzantılar dosyasına değişiklikler yapar.

Üç tür ilke dosyası olsa da, yalnızca üç dosya ile sınırlı değilsiniz. Her dosya türünde birden çok dosya olabilir. Örneğin, uzantılar dosyanızda değişiklik yapmak istemiyorsanız, uzantılar dosyasını daha fazla genişletmek için bir Extensions2 dosyası oluşturabilirsiniz.

## <a name="custom-policy-core-concepts"></a>Özel ilke temel kavramları

Azure 'daki müşteri kimliği ve erişim yönetimi (CıHAR) hizmeti şunları içerir:

- Microsoft Graph kullanılarak erişilebilen ve hem yerel hesaplar hem de Federasyon hesapları için Kullanıcı verilerini tutan bir kullanıcı dizini.
- Kullanıcılar ve varlıklar arasında güveni düzenleyen ve kimlik veya erişim yönetimi görevini tamamlayan talepler arasındaki talepleri ileten **kimlik deneyimi çerçevesine** erişin.
- KIMLIK belirteçleri veren bir güvenlik belirteci hizmeti (STS), belirteçleri yeniler ve belirteçleri (ve eşdeğer SAML onayları) ve kaynakları koruyacak şekilde doğrular.

Kimlik sağlayıcıları, kullanıcılar, diğer sistemlerle ve bir kimlik görevi elde etmek için sırasıyla Yerel Kullanıcı diziniyle etkileşime Azure AD B2C. Örneğin, bir kullanıcı için oturum açın, yeni bir Kullanıcı kaydedin veya parolayı sıfırlayın. Kimlik deneyimi çerçevesi ve bir ilke (Kullanıcı yolculuğu veya güven çerçevesi ilkesi olarak da bilinir), çok taraflara sahip güven oluşturur ve açıkça aktörleri, eylemleri, protokolleri ve tamamlanacak adımların sırasını tanımlar.

Kimlik deneyimi çerçevesi, OpenID Connect, OAuth, SAML ve standart olmayan bazı standart olmayan bir şekilde, örneğin REST API tabanlı sistemden sisteme talep alışverişi gibi standart protokol biçimlerindeki varlıklar arasında güveni düzenleyen, tümüyle yapılandırılabilir, ilke odaklı, bulut tabanlı bir Azure platformudur. Framework, HTML ve CSS 'yi destekleyen, Kullanıcı dostu, beyaz etiketli deneyimler oluşturur.

Özel ilke bir veya hiyerarşi zincirinde birbirine başvuran daha fazla XML biçimindeki dosyadan oluşabilir. XML öğeleri, diğer öğelerin yanı sıra talep şemasını, talep dönüştürmelerini, içerik tanımlarını, talep sağlayıcılarını, teknik profilleri ve Kullanıcı yolculuğu düzenleme adımlarını tanımlar. Özel bir ilke, bağlı olan taraf tarafından çağrıldığında kimlik deneyimi çerçevesi tarafından yürütülen bir veya birkaç XML dosyası olarak erişilebilir. Özel ilkeleri yapılandıran geliştiriciler, her bir kimlik sağlayıcısı için gerektiğinde meta veri uç noktalarını, tam talep istekleri Exchange tanımlarını ve parolaları, anahtarları ve sertifikaları yapılandırmak için güvenilir ilişkileri dikkatle tanımlamalıdır.

### <a name="inheritance-model"></a>Devralma modeli

Bir uygulama RP ilke dosyasını çağırdığında, Azure AD B2C ' deki kimlik deneyimi çerçevesi temel dosyadaki tüm öğeleri uzantı dosyasından, sonra da geçerli ilkeyi uygulamak için RP ilke dosyasından ekler.  RP dosyasındaki aynı tür ve ad öğeleri Uzantılardaki geçersiz kılınır ve uzantılar temeli geçersiz kılar.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Özel ilkeleri kullanmaya başlama](custom-policy-get-started.md)
