---
title: Azure AD'ye uygulamalar nasıl ve neden eklenir?
titleSuffix: Microsoft identity platform
description: Uygulamanın Azure AD 'ye eklenmesi ne anlama geliyor?
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/01/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: 1f6fd0160988802e198ff9388cfeb3232b34b100
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861128"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Azure AD 'ye uygulamaların nasıl ve neden eklendiği

Azure AD 'de uygulamaların iki gösterimi vardır:

* [Uygulama nesneleri](app-objects-and-service-principals.md#application-object) - [özel durumlar](#notes-and-exceptions)olsa da, uygulama nesneleri bir uygulamanın tanımı olarak düşünülebilir.
* [Hizmet sorumluları](app-objects-and-service-principals.md#service-principal-object) , bir uygulamanın örneği olarak düşünülebilir. Hizmet sorumluları genellikle bir uygulama nesnesine başvurur ve tek bir uygulama nesnesine dizinler arasında birden çok hizmet sorumlusu tarafından başvurulabilir.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>Uygulama nesneleri nedir ve nereden geliyor?

Uygulama [kayıt](https://aka.ms/appregistrations) deneyimi aracılığıyla Azure Portal [uygulama nesnelerini](app-objects-and-service-principals.md#application-object) yönetebilirsiniz. Uygulama nesneleri, uygulamayı Azure AD 'ye anlatmaktadır ve uygulamanın tanımı olarak düşünülebilir ve bu da hizmetin, ayarlarına bağlı olarak uygulamaya belirteç verme konusunda bilgi sahibi olabilir. Uygulama nesnesi, diğer dizinlerde hizmet sorumlularını destekleyen çok kiracılı bir uygulama olsa bile, yalnızca kendi giriş dizininde bulunur. Uygulama nesnesi aşağıdakilerden herhangi birini (ve burada açıklanmayan ek bilgileri) içerebilir:

* Ad, logo ve Yayımcı
* Yeniden yönlendirme URI 'Leri
* Gizlilikler (uygulamanın kimliğini doğrulamak için kullanılan simetrik ve/veya asimetrik anahtarlar)
* API bağımlılıkları (OAuth)
* Yayımlanan API 'Ler/kaynaklar/kapsamlar (OAuth)
* Uygulama rolleri (RBAC)
* SSO meta verileri ve yapılandırması
* Kullanıcı sağlama meta verileri ve yapılandırması
* Proxy meta verileri ve yapılandırması

Uygulama nesneleri, aşağıdakiler dahil olmak üzere birden çok yol yoluyla oluşturulabilir:

* Azure portal uygulama kayıtları
* Visual Studio kullanarak yeni bir uygulama oluşturma ve bunu Azure AD kimlik doğrulaması kullanacak şekilde yapılandırma
* Yönetici, uygulama galerisinden bir uygulama eklediğinde (bir hizmet sorumlusu de oluşturur)
* Yeni bir uygulama oluşturmak için Microsoft Graph API veya PowerShell kullanma
* Birçok başka geliştirici deneyimi de dahil olmak üzere Azure 'da ve API Explorer deneyimlerindeki çeşitli geliştiriciler

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>Hizmet sorumlusu nedir ve nereden geliyor?

[Kurumsal uygulamalar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) deneyimi aracılığıyla Azure Portal [hizmet sorumlularını](app-objects-and-service-principals.md#service-principal-object) yönetebilirsiniz. Hizmet sorumluları, Azure AD 'ye bağlanan bir uygulamayı yönetme ve dizininizde uygulamanın örneği olarak kabul edilebilir. Belirli bir uygulama için, en fazla bir uygulama nesnesi ("ana" dizinde kayıtlı) ve üzerinde çalıştığı her dizinde uygulamanın örneklerini temsil eden bir veya daha fazla hizmet sorumlusu nesnesi olabilir. 

Hizmet sorumlusu şunlar olabilir:

* Uygulama KIMLIĞI özelliği aracılığıyla bir uygulama nesnesine geri başvuru
* Yerel Kullanıcı ve Grup uygulaması-rol atamalarının kayıtları
* Uygulamaya verilen yerel kullanıcı ve yönetici izinlerinin kayıtları
  * Örneğin: uygulamanın belirli bir kullanıcının e-postasına erişmesi için izin
* Koşullu erişim ilkesi dahil olmak üzere yerel ilkelerin kayıtları
* Bir uygulama için alternatif yerel ayarların kayıtları
  * Talep dönüştürme kuralları
  * Öznitelik eşlemeleri (Kullanıcı hazırlama)
  * Dizine özgü uygulama rolleri (uygulama özel rolleri destekliyorsa)
  * Dizine özgü ad veya logo

Uygulama nesneleri gibi, hizmet sorumluları da dahil olmak üzere birden çok yol aracılığıyla oluşturulabilir:

* Kullanıcılar Azure AD ile tümleştirilmiş bir üçüncü taraf uygulamasında oturum açtığında
  * Oturum açma sırasında kullanıcılardan, profiline ve diğer izinlerine erişmesi için uygulamaya izin vermesi istenir. İzin veren ilk kişi, dizine eklenecek uygulamayı temsil eden bir hizmet sorumlusuna neden olur.
* Kullanıcılar [Microsoft 365](https://products.office.com/) gibi Microsoft Çevrimiçi Hizmetler oturum açtığında
  * Microsoft 365 abone olduğunuzda veya bir denemeye başladığınızda, dizin içinde, Microsoft 365 ilişkili tüm işlevleri teslim etmek için kullanılan çeşitli hizmetleri temsil eden bir veya daha fazla hizmet sorumlusu oluşturulur.
  * SharePoint gibi bazı Microsoft 365 Hizmetleri, iş akışları da dahil olmak üzere bileşenler arasında güvenli iletişime izin vermek için sürekli olarak hizmet sorumluları oluşturur.
* Yönetici, uygulama galerisinden bir uygulama eklediğinde (Ayrıca bu, temel bir uygulama nesnesi oluşturur)
* [Azure AD uygulama ara sunucusu](../manage-apps/application-proxy.md) kullanmak için uygulama ekleme
* SAML veya parola kullanarak çoklu oturum açma için bir uygulamayı bağlama çoklu oturum açma (SSO)
* Microsoft Graph API veya PowerShell aracılığıyla programlama yoluyla

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Uygulama nesneleri ve hizmet sorumluları birbirleriyle nasıl ilişkilidir?

Bir uygulamanın, çalıştığı her dizindeki bir veya daha fazla hizmet sorumlusu (uygulamanın giriş dizini dahil) tarafından başvurulan kendi giriş dizininde bir uygulama nesnesi vardır.

![Uygulama nesneleri ve hizmet sorumluları arasındaki ilişkiyi gösterir][apps_service_principals_directory]

Yukarıdaki diyagramda, Microsoft, uygulamaları yayımlamak için kullandığı iki dizini dahili olarak (sol tarafta gösterilir) tutar:

* Bir adet Microsoft uygulamaları (Microsoft hizmetleri dizini)
* Biri önceden tümleştirilmiş üçüncü taraf uygulamalar (App Gallery dizini) için

Azure AD ile tümleştirilen uygulama yayıncıları/satıcıların bir yayımlama dizinine sahip olması gerekir ("bazı SaaS dizini" olarak doğru gösterilir).

Kendinize eklediğiniz uygulamalar (diyagramda **uygulama (sizinkiler)** olarak gösterilir) şunları içerir:

* Geliştirmiş olduğunuz uygulamalar (Azure AD ile tümleşik)
* Çoklu oturum açma için bağladığınız uygulamalar
* Azure AD uygulama proxy 'sini kullanarak yayımladığınız uygulamalar

### <a name="notes-and-exceptions"></a>Notlar ve özel durumlar

* Tüm hizmet sorumluları bir uygulama nesnesine geri işaret etmez. Azure AD ilk olarak yapılandırıldığında, uygulamalara sunulan hizmetler daha sınırlıdır ve hizmet sorumlusu bir uygulama kimliği oluşturmak için yeterlidir. Özgün hizmet sorumlusu Windows Server Active Directory hizmet hesabına göre daha yakın. Bu nedenle, önce bir uygulama nesnesi oluşturmadan Azure AD PowerShell kullanma gibi farklı yol yollarla hizmet sorumluları oluşturmak da mümkündür. Microsoft Graph API 'SI bir hizmet sorumlusu oluşturmadan önce bir uygulama nesnesi gerektirir.
* Yukarıda açıklanan bilgilerin hepsi programlı olarak kullanıma sunulmamaktadır. Aşağıdakiler yalnızca Kullanıcı arabiriminde kullanılabilir:
  * Talep dönüştürme kuralları
  * Öznitelik eşlemeleri (Kullanıcı hazırlama)
* Hizmet sorumlusu ve uygulama nesneleri hakkında daha ayrıntılı bilgi için bkz. Microsoft Graph API başvuru belgeleri:
  * [Uygulama](/graph/api/resources/application)
  * [Hizmet Sorumlusu](/graph/api/resources/serviceprincipal?view=graph-rest-beta)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Uygulamalar neden Azure AD ile tümleştirçalışıyor?

Uygulamalar, aşağıdakileri içeren bir veya daha fazla hizmetten yararlanmak için Azure AD 'ye eklenir:

* Uygulama kimlik doğrulaması ve yetkilendirme
* Kullanıcı kimlik doğrulaması ve yetkilendirme
* Federasyon veya parola kullanan SSO
* Kullanıcı hazırlama ve eşitleme
* Rol tabanlı erişim denetimi-bir uygulamada rol tabanlı yetkilendirme denetimleri gerçekleştirmek için uygulama rollerini tanımlamak üzere dizini kullanın
* OAuth yetkilendirme Hizmetleri-API/kaynaklara erişim yetkisi vermek için Microsoft 365 ve diğer Microsoft uygulamaları tarafından kullanılır
* Uygulama yayımlama ve proxy-özel bir ağdan internet 'e uygulama yayımlama
* Dizin şeması uzantı öznitelikleri-Azure AD 'de ek verileri depolamak için [hizmet sorumlusu ve Kullanıcı nesnelerinin şemasını genişletme](active-directory-schema-extensions.md) 

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Azure AD örneğinize uygulama ekleme izni var mı?

Yalnızca genel yöneticilerin yapabilecekleri bazı görevler vardır (uygulama galerisinden uygulama ekleme ve uygulama proxy 'sini kullanmak üzere uygulama yapılandırma gibi), dizininizdeki tüm kullanıcıların, kendi geliştirdikleri uygulama nesnelerini kaydetme ve izin aracılığıyla kurumsal verilerine erişim izni verme hakları vardır. Bir kişi, bir uygulamada oturum açmak ve izin vermek için dizininizdeki ilk kullanıcı ise kiracınızda bir hizmet sorumlusu oluşturur; Aksi takdirde, izin verme bilgileri mevcut hizmet sorumlusu üzerinde depolanır.

Kullanıcıların uygulamalara kaydolmasına ve izin vermesini sağlamak, başlangıçta bununla ilgili olarak devam edebilir, ancak şunları aklınızda tutun:


* Uygulamalar, uygulamanın dizine kaydedilmesini veya dizinde kaydedilmesini gerektirmeden birçok yıl boyunca Kullanıcı kimlik doğrulaması için Windows Server Active Directory 'ten faydalanabilir. Artık kuruluş, dizini ve ne amaçla çok sayıda uygulamanın kullandığını tam olarak görünürlüğe sahip olacaktır.
* Bu sorumlulukların kullanıcılara temsilci seçme, Yönetici odaklı uygulama kaydı ve yayımlama süreci gereksinimini geçersiz kılar. Active Directory Federasyon Hizmetleri (AD FS) (ADFS) ile, bir yöneticinin geliştiriciler adına bağlı olan taraf olarak bir uygulama eklemesi gerekiyordu. Artık geliştiriciler self servis verebilir.
* İş amaçlarına yönelik kuruluş hesaplarını kullanan uygulamalarda oturum açan kullanıcılar iyi bir şeydir. Bundan sonra kuruluştan ayrılırsanız, kullandıkları uygulamadaki hesabına erişimi otomatik olarak kaybedecektir.
* Hangi verilerin hangi uygulamayla paylaşıldığını bir kayda sahip olma. Veriler şimdiye kadar çok daha aktarılabilir ve hangi uygulamaların hangi verilerle paylaşıldığını açık bir kaydına sahip olmak faydalı olur.
* OAuth için Azure AD kullanan API sahipleri, kullanıcıların uygulamalara hangi izinlerin izin vereceğine ve bir yöneticinin kabul etmesi gereken izinlere tam olarak karar verir. Yalnızca Yöneticiler daha büyük kapsamları ve daha önemli izinleri kabul edebilir ve kullanıcı onayı kullanıcıların kendi veri ve özelliklerine göre kapsamlandırılır.
* Bir Kullanıcı bir uygulamanın verilerine erişmesini eklediğinde veya izin verdiği zaman, bir uygulamanın dizine nasıl eklendiğini öğrenmek için Azure portal içindeki denetim raporlarını görüntüleyebilmeniz için olay denetlenebilir.

Hala dizininizdeki kullanıcıların uygulama kaydetmesini ve yönetici onayı olmadan uygulamalarda oturum açmasını engellemek istiyorsanız, bu özellikleri kapatmak için değiştirebileceğiniz iki ayar vardır:

* Kullanıcıların kendi adına uygulamalara yarışmasını engellemek için:
  1. Azure portal, kurumsal uygulamalar altındaki [Kullanıcı ayarları](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) bölümüne gidin.
  2. **Kullanıcıları, adına şirket verilerine erişen uygulamalara izin verebilir** **.**
     
     > [!NOTE]
     > Kullanıcı onayını devre dışı bırakmak isterseniz, bir yöneticinin kullanması gereken yeni bir uygulamaya onay verilmesi gerekecektir.

* Kullanıcıların kendi uygulamalarını kaydetmesini engellemek için:
  1. Azure portal, altındaki [Kullanıcı ayarları](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) bölümüne gidin Azure Active Directory
  2. **Kullanıcıları değiştir, uygulamaları** **Hayır**'a kaydedebilir.

> [!NOTE]
> Microsoft, kullanıcıların uygulamaları ve onay uygulamalarını kendi adına kaydedebilmeleri için varsayılan yapılandırmayı kullanır.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
