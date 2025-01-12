---
title: Gelişmiş tehdit koruması-MySQL için Azure veritabanı
description: Veritabanı için olası güvenlik tehditlerini gösteren anormal veritabanı etkinliklerini algılayan Gelişmiş tehdit koruması hakkında kavramlar öğrenin.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c9e884d153e85e7b68dee38494ac5d6f4271978a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542584"
---
# <a name="azure-database-for-mysql-advanced-threat-protection"></a>MySQL için Azure veritabanı Gelişmiş tehdit koruması

MySQL için Azure Veritabanı Gelişmiş Tehdit Koruması, veritabanlarınıza erişme veya bunları kullanma konusunda olağandışı ve potansiyel olarak zararlı girişimleri gösteren anormal etkinlikleri tespit eder.

> [!NOTE]
> Gelişmiş tehdit koruması genel önizlemede.

Gelişmiş tehdit koruması, gelişmiş güvenlik özelliklerine yönelik Birleşik bir paket olan gelişmiş veri güvenliği sunumunun bir parçasıdır. Gelişmiş tehdit korumasına [Azure Portal](https://portal.azure.com) veya [REST API](/rest/api/mysql)aracılığıyla erişilebilir ve yönetilebilir. Özelliği, Genel Amaçlı ve bellek için Iyileştirilmiş sunucular için kullanılabilir.

> [!NOTE]
> Gelişmiş tehdit koruması özelliği şu Azure Kamu ve bağımsız bulut **bölgelerinde kullanılamaz:** US gov Teksas, US gov Arizona, US gov Iowa, US, gov Virginia, US DOD Doğu, US DOD orta, Almanya Orta, Almanya Kuzey, Çin Doğu, Çin Doğu 2. Genel ürün kullanılabilirliği için lütfen [bölgeye göre sunulan ürünleri](https://azure.microsoft.com/global-infrastructure/services/) ziyaret edin.


## <a name="what-is-advanced-threat-protection"></a>Gelişmiş tehdit koruması nedir?

MySQL için Azure veritabanı için Gelişmiş tehdit koruması, müşterilerin anormal etkinliklerde güvenlik uyarıları sunarak meydana gelebilecek olası tehditleri algılamasına ve yanıt vermesine olanak tanıyan yeni bir güvenlik katmanı sağlar. Kullanıcılar şüpheli veritabanı etkinliklerine ve potansiyel güvenlik açıklarına ek olarak anormal veritabanı erişimi ve sorgu düzenlerine bir uyarı alırlar. MySQL için Azure veritabanı için Gelişmiş tehdit koruması, şüpheli etkinliklerin ayrıntılarını içeren ve tehdidi araştırıp azaltmaya yönelik eylem öneren [Azure Güvenlik Merkezi](https://azure.microsoft.com/services/security-center/)ile ilgili uyarıları tümleştirir. MySQL için Azure veritabanı için Gelişmiş tehdit koruması, bir güvenlik uzmanı veya gelişmiş güvenlik izleme sistemlerini yönetmek zorunda kalmadan, olası tehditleri veritabanına kolayca adreslamanızı sağlar. 

:::image type="content" source="media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png" alt-text="Gelişmiş tehdit koruması kavramı":::

## <a name="advanced-threat-protection-alerts"></a>Gelişmiş tehdit koruması uyarıları 
MySQL için Azure veritabanı için Gelişmiş tehdit koruması, veritabanları için olağandışı ve potansiyel olarak zararlı olan girişimleri belirten anormal etkinlikleri algılar ve aşağıdaki uyarıları tetikleyebilirler:
- **Olağan olmayan konumdan erişim**: Bu uyarı, bir kişinin Azure veritabanı için Azure veritabanı sunucusunda, olağan dışı bir coğrafi konumdan oturum açtığı, bu uyarı tetiklenir. Bazı durumlarda uyarı güvenli işlemleri (yeni bir uygulama veya geliştirici bakımı gibi) de algılar. Diğer durumlarda, uyarı kötü amaçlı işlemleri (önceki çalışan ve şirket dışı saldırgan gibi) algılar.
- **Olağan dışı Azure veri merkezi 'Nden erişim**: Bu uyarı, bir kullanıcının sunucuda, son dönemde bu sunucuda görülen olağan dışı bir Azure veri merkezinden oturum açtığı, MySQL Için Azure veritabanı sunucusuna erişim modelinde bir değişiklik olduğunda tetiklenir. Bazı durumlarda, uyarı meşru bir eylem (Azure 'da yeni uygulamanız Power BI, MySQL için Azure veritabanı sorgu Düzenleyicisi) algılar. Diğer durumlarda, uyarı Azure kaynağı/hizmetinden kaynaklanan kötü amaçlı işlemleri (önceki çalışan ve şirket dışı saldırgan gibi) algılar.
- **Bilmediğiniz sorumludan erişim**: Bu uyarı, bir kullanıcının olağan dışı bir sorumlu (MySQL Için Azure veritabanı kullanıcısı) kullanarak sunucuda oturum açtığı MySQL Için Azure veritabanı sunucusuna erişim modelinde bir değişiklik olduğunda tetiklenir. Bazı durumlarda uyarı güvenli işlemleri (yeni uygulama ve geliştirici bakımı gibi) de algılar. Diğer durumlarda, uyarı kötü amaçlı işlemleri (önceki çalışan ve şirket dışı saldırgan gibi) algılar.
- **Zararlı olabilecek bir uygulamadan erişim**: Bu uyarı, zararlı olabilecek bir uygulama veritabanına erişmeye çalıştığında tetiklenir. Bazı durumlarda, uyarı güvenlik testlerini algılar. Diğer durumlarda, yarı yaygın saldırı araçlarının kullandığı saldırıları algılar.
- **Deneme sürümü MySQL Için Azure veritabanı kimlik bilgilerini zorla**: Bu uyarı, farklı kimlik bilgileri ile olağan dışı yüksek sayıda başarısız oturum açma işlemi olduğunda tetiklenir. Bazı durumlarda, uyarı güvenlik testlerini algılar. Diğer durumlarda, uyarı deneme yanılma saldırılarını algılar.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Güvenlik Merkezi](../security-center/security-center-introduction.md) hakkında daha fazla bilgi edinin
* Fiyatlandırma hakkında daha fazla bilgi için bkz. [MySQL Için Azure veritabanı fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/mysql/) 
* Azure portal kullanarak [MySQL Için Azure veritabanı Gelişmiş tehdit koruması](howto-database-threat-protection-portal.md) yapılandırma