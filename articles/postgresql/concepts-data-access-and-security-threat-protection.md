---
title: Gelişmiş tehdit koruması-PostgreSQL için Azure veritabanı-tek sunucu
description: Veritabanına yönelik olası güvenlik tehditlerini gösteren anormal veritabanı etkinliklerini algılamak için Gelişmiş tehdit koruması kullanma hakkında bilgi edinin.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: fa136a15e4fa034307e16ef50c2b16060c5af0d2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92490212"
---
# <a name="advanced-threat-protection-in-azure-database-for-postgresql---single-server"></a>PostgreSQL için Azure veritabanı 'nda Gelişmiş tehdit koruması-tek sunucu

PostgreSQL için Azure Veritabanı Gelişmiş Tehdit Koruması, veritabanlarınıza erişme veya bunları kullanma konusunda olağandışı ve potansiyel olarak zararlı girişimleri gösteren anormal etkinlikleri tespit eder.

> [!NOTE]
> Gelişmiş tehdit koruması genel önizlemede.

Tehdit koruması, gelişmiş güvenlik özelliklerine yönelik Birleşik bir paket olan Gelişmiş tehdit koruması (ATP) sunumunun bir parçasıdır. Gelişmiş tehdit korumasına [Azure Portal](https://portal.azure.com) veya [REST API](/rest/api/postgresql)aracılığıyla erişilebilir ve yönetilebilir. Özelliği, Genel Amaçlı ve bellek için Iyileştirilmiş sunucular için kullanılabilir.

> [!NOTE]
> Gelişmiş tehdit koruması özelliği şu Azure Kamu ve bağımsız bulut **bölgelerinde kullanılamaz:** US gov Teksas, US gov Arizona, US gov Iowa, US, gov Virginia, US DOD Doğu, US DOD orta, Almanya Orta, Almanya Kuzey, Çin Doğu, Çin Doğu 2. Genel ürün kullanılabilirliği için lütfen [bölgeye göre sunulan ürünleri](https://azure.microsoft.com/global-infrastructure/services/) ziyaret edin.

## <a name="what-is-advanced-threat-protection"></a>Gelişmiş tehdit koruması nedir?

PostgreSQL için Azure veritabanı için Gelişmiş tehdit koruması, müşterilerin anormal etkinliklerde güvenlik uyarıları sunarak meydana gelebilecek olası tehditleri algılamasına ve yanıt vermesine olanak tanıyan yeni bir güvenlik katmanı sağlar. Kullanıcılar şüpheli veritabanı etkinliklerine ve potansiyel güvenlik açıklarına ek olarak anormal veritabanı erişimi ve sorgu düzenlerine bir uyarı alırlar. PostgreSQL için Azure veritabanı için Gelişmiş tehdit koruması, şüpheli etkinliklerin ayrıntılarını içeren ve tehdidi araştırıp azaltmaya yönelik eylem öneren [Azure Güvenlik Merkezi](https://azure.microsoft.com/services/security-center/)ile ilgili uyarıları tümleştirir. PostgreSQL için Azure veritabanı için Gelişmiş tehdit koruması, bir güvenlik uzmanı veya gelişmiş güvenlik izleme sistemlerini yönetmek zorunda kalmadan, olası tehditleri veritabanına kolayca adresleyebilirsiniz. 

:::image type="content" source="media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png" alt-text="Gelişmiş tehdit koruması kavramı":::

## <a name="advanced-threat-protection-alerts"></a>Gelişmiş tehdit koruması uyarıları 
PostgreSQL için Azure veritabanı için Gelişmiş tehdit koruması, veritabanları için olağandışı ve potansiyel olarak zararlı girişimleri gösteren anormal etkinlikleri algılar ve aşağıdaki uyarıları tetikleyebilirler:
- **Olağan olmayan konumdan erişim**: Bu uyarı, bir kullanıcının beklenmedik bir coğrafi konumdan PostgreSQL Için Azure veritabanı sunucusuna oturum açtığı PostgreSQL Için Azure veritabanı sunucusuna erişim modelinde bir değişiklik olduğunda tetiklenir. Bazı durumlarda uyarı güvenli işlemleri (yeni bir uygulama veya geliştirici bakımı gibi) de algılar. Diğer durumlarda, uyarı kötü amaçlı işlemleri (önceki çalışan ve şirket dışı saldırgan gibi) algılar.
- **Olağan dışı Azure veri merkezi 'Nden erişim**: Bu uyarı, son dönemde bu sunucuda görülen olağan dışı bir Azure veri merkezinden sunucuda oturum açmış olan PostgreSQL Için Azure veritabanı sunucusuna erişim modelinde bir değişiklik olduğunda tetiklenir. Bazı durumlarda, uyarı yasal bir eylem (Azure 'da yeni uygulamanız Power BI, PostgreSQL için Azure veritabanı sorgu Düzenleyicisi) algılar. Diğer durumlarda, uyarı Azure kaynağı/hizmetinden kaynaklanan kötü amaçlı işlemleri (önceki çalışan ve şirket dışı saldırgan gibi) algılar.
- **Bilmediğiniz sorumludan erişim**: Bu uyarı, kullanıcının olağan dışı bir sorumlu (PostgreSQL Için Azure veritabanı kullanıcısı) kullanarak sunucuda oturum açtığı PostgreSQL Için Azure veritabanı sunucusuna erişim modelinde bir değişiklik olduğunda tetiklenir. Bazı durumlarda uyarı güvenli işlemleri (yeni uygulama ve geliştirici bakımı gibi) de algılar. Diğer durumlarda, uyarı kötü amaçlı işlemleri (önceki çalışan ve şirket dışı saldırgan gibi) algılar.
- **Zararlı olabilecek bir uygulamadan erişim**: Bu uyarı, zararlı olabilecek bir uygulama veritabanına erişmeye çalıştığında tetiklenir. Bazı durumlarda, uyarı güvenlik testlerini algılar. Diğer durumlarda, yarı yaygın saldırı araçlarının kullandığı saldırıları algılar.
- **Deneme yanılma, PostgreSQL Için Azure veritabanı kimlik bilgilerini zorla**: Bu uyarı, farklı kimlik bilgileri ile olağan dışı yüksek sayıda başarısız oturum açma işlemi olduğunda tetiklenir. Bazı durumlarda, uyarı güvenlik testlerini algılar. Diğer durumlarda, uyarı deneme yanılma saldırılarını algılar.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Güvenlik Merkezi](../security-center/security-center-introduction.md) hakkında daha fazla bilgi edinin
* Fiyatlandırma hakkında daha fazla bilgi için bkz. [PostgreSQL Için Azure veritabanı fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/postgresql/) 
* Azure portal kullanarak [PostgreSQL Için Azure veritabanı Gelişmiş tehdit koruması](howto-database-threat-protection-portal.md) yapılandırma