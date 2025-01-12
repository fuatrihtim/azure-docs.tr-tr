---
title: Azure AD Connect bulut eşitlemesi nedir? | Microsoft Docs
description: Azure AD Connect bulut eşitlemesini açıklar.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3b880d70baf7c160f8235bd01a5000f83825c36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98614863"
---
# <a name="what-is-azure-ad-connect-cloud-sync"></a>Azure AD Connect bulut eşitlemesi nedir?
Azure AD Connect bulut eşitleme, Microsoft 'un, kullanıcıların, grupların ve kişilerin Azure AD 'ye eşitlemesine yönelik karma kimlik hedeflerinizi karşılamak ve gerçekleştirmek üzere tasarlanan yeni bir tekliftir.  Bunu, Azure AD Connect uygulaması yerine Azure AD bulut sağlama aracısını kullanarak gerçekleştirir.  Ancak, Azure AD Connect eşitleme ile birlikte kullanılabilir ve aşağıdaki avantajları sağlar:
    
- Çok ormanlı bir Active Directory orman ortamından bir Azure AD kiracısına eşitleme desteği: genel senaryolar, elde edilen şirketin ad ormanlarının, genellikle birden çok ad ormanına sahip olduğu üst şirketin ad ormanlarından ve şirketlerinden yalıtılmış olduğu birleşme & alımı içerir.
- Hafif sağlama aracılarıyla Basitleştirilmiş Yükleme: aracılar, bulutta yönetilen tüm eşitleme yapılandırması ile Azure AD 'ye bir köprü görevi görür. 
- Çoklu sağlama aracıları, AD 'den Azure AD 'ye Parola karması eşitlemesine bağlı olan kuruluşlar için özellikle kritik olan yüksek kullanılabilirlik dağıtımlarını basitleştirmek üzere kullanılabilir.
- En fazla 50 k üye içeren büyük gruplar için destek. Büyük grupları eşitlerken yalnızca OU kapsam filtresi kullanılması önerilir.


![Azure AD Connect nedir?](media/what-is-cloud-sync/architecture-1.png)

## <a name="how-is-azure-ad-connect-cloud-sync-different-from-azure-ad-connect-sync"></a>Azure AD Connect bulut eşitlemesi Azure AD Connect eşitlemeden farklı midir?
Azure AD Connect bulut eşitlemeyle, AD 'den Azure AD 'ye sağlama, Microsoft Online Services 'ta düzenlenir. Kuruluşun, Azure AD ile AD arasında köprü görevi gören hafif bir aracı olan şirket içi ve IaaS barındırılan ortamlarında dağıtılması gerekir. Sağlama yapılandırması Azure AD 'de depolanır ve hizmetin bir parçası olarak yönetilir.

## <a name="azure-ad-connect-cloud-sync-video"></a>Azure AD Connect bulut eşitleme videosu
Aşağıdaki kısa videoda Azure AD Connect bulut eşitlemesine mükemmel bir genel bakış sunulmaktadır:

> [!VIDEO https://youtube.com/embed/mOT3ID02_YQ]


## <a name="comparison-between-azure-ad-connect-and-cloud-sync"></a>Azure AD Connect ile bulut eşitlemesi arasında karşılaştırma

Aşağıdaki tabloda Azure AD Connect ve Azure AD Connect bulut eşitlemesi arasında bir karşılaştırma sunulmaktadır:

| Özellik | Azure Active Directory Connect eşitleme| Azure Active Directory Connect bulut eşitleme |
|:--- |:---:|:---:|
|Tek şirket içi AD ormanına bağlanma|● |● |
| Birden çok şirket içi AD ormanına bağlanma |● |● |
| Birden fazla bağlantısı kesik şirket içi AD ormanına bağlanma | |● |
| Hafif aracı yükleme modeli | |● |
| Yüksek kullanılabilirlik için birden çok etkin aracı | |● |
| LDAP dizinlerine bağlanma|●| | 
| Kullanıcı nesneleri için destek |● |● |
| Grup nesneleri için destek |● |● |
| İletişim nesneleri için destek |● |● |
| Cihaz nesneleri için destek |● | |
| Öznitelik akışları için temel özelleştirmeye izin ver |● |● |
| Exchange Online özniteliklerini eşitler |● |● |
| Uzantı özniteliklerini eşitler 1-15 |● |● |
| Müşteri tanımlı AD özniteliklerini (Dizin uzantıları) eşitler |● | |
| Parola karması eşitleme desteği |●|●|
| Pass-Through kimlik doğrulaması desteği |●||
| Federasyon desteği |●|●|
| Sorunsuz Çoklu Oturum Açma|● |●|
| Etki Alanı Denetleyicisi üzerinde yüklemeyi destekler |● |● |
| Windows Server 2012 ve Windows Server 2012 R2 desteği |● |● |
| Etki alanları/OU 'Lar/gruplar üzerinde filtrele |● |● |
| Nesnelerin öznitelik değerlerini filtrele |● | |
| Minimal öznitelik kümesinin eşitlenmesine izin ver (MinSync) |● |● |
| Öznitelik kaldırma işleminin AD'den Azure AD'ye akışına izin ver |● |● |
| Öznitelik akışları için gelişmiş özelleştirmeye izin ver |● | |
| Geri yazma desteği (parolalar, cihazlar, gruplar) |● | |
| Azure AD Domain Services desteği|● | |
| [Exchange karma geri yazma](../hybrid/reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) |● | |
| AD etki alanı başına en fazla 150.000 nesne desteği |● |● |
| Büyük grup desteği-en fazla 50.000 üye içeren gruplar |● |● |
| Çapraz etki alanı başvuruları|● | |
| İsteğe bağlı sağlama| |● |

## <a name="next-steps"></a>Sonraki adımlar 

- [Sağlama nedir?](what-is-provisioning.md)
- [Bulut eşitlemesini yükler](how-to-install.md)
