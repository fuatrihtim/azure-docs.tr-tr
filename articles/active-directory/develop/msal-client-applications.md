---
title: Ortak ve gizli istemci uygulamaları (MSAL) | Mavisi
titleSuffix: Microsoft identity platform
description: Microsoft kimlik doğrulama kitaplığı 'nda (MSAL) ortak istemci ve gizli istemci uygulamaları hakkında bilgi edinin.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 2ef29f6fe3403809d01fcea382474c514319b7c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98063765"
---
# <a name="public-client-and-confidential-client-applications"></a>Ortak istemci ve gizli istemci uygulamaları
Microsoft kimlik doğrulama kitaplığı (MSAL) iki tür istemci tanımlar: genel istemciler ve gizli istemciler. İki istemci türü, yetkilendirme sunucusuyla güvenli kimlik doğrulaması yapma ve istemci kimlik bilgilerinin gizliliğini koruma özelliklerine göre ayırt edilir. Buna karşılık, Azure AD kimlik doğrulama kitaplığı (ADAL), *kimlik doğrulama bağlamını* (Azure AD ile bağlantı olan) kullanır.

- **Gizli istemci uygulamaları** sunucular üzerinde çalışan uygulamalardır (Web Apps, Web API Apps, hatta hizmet/Daemon uygulamaları). Erişimin zor olduğu kabul edilir ve bu nedenle bir uygulama gizli dizisi bulundurmaktan sorumludur. Gizli istemciler yapılandırma zamanı gizli dizilerini tutabilir. İstemcinin her örneği ayrı bir yapılandırmaya sahiptir (istemci KIMLIĞI ve istemci parolası dahil). Bu değerler, son kullanıcıların ayıklanmaları zordur. En yaygın gizli istemci bir Web uygulamasıdır. İstemci KIMLIĞI Web tarayıcısı aracılığıyla sunulur, ancak gizli dizi yalnızca arka kanalda geçirilir ve hiçbir şekilde doğrudan gösterilmez.

    Gizli istemci uygulamaları: <BR>
    ![Web uygulaması ](media/msal-client-applications/web-app.png) ![ Web API ](media/msal-client-applications/web-api.png) ![ cini/hizmeti](media/msal-client-applications/daemon-service.png)

- **Ortak istemci uygulamaları** , cihazlarda veya masaüstü bilgisayarlarda veya bir Web tarayıcısında çalışan uygulamalardır. Uygulama gizliliklerine güvenli bir şekilde devam etmek güvenli değildir, bu nedenle yalnızca Kullanıcı adına Web API 'Lerine erişir. (Yalnızca ortak istemci akışlarını destekler.) Ortak istemciler yapılandırma zamanı gizli dizilerini tutamaz, bu nedenle istemci gizli dizileri yok.

    Ortak istemci uygulamaları: <BR>
    ![Masaüstü uygulama ](media/msal-client-applications/desktop-app.png) ![ TARAYıCıSıZ API ](media/msal-client-applications/browserless-app.png) ![ mobil uygulaması](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> MSAL.js, ortak ve gizli istemci uygulamalarının bir ayrımı yoktur.  MSAL.js, Kullanıcı Aracısı tabanlı uygulamalar, istemci kodunun bir Web tarayıcısı gibi bir Kullanıcı aracısında yürütüldüğü ortak istemciler gibi istemci uygulamalarını temsil eder. Tarayıcı bağlamı düzgün şekilde erişilebilir olduğundan, bu istemciler gizli dizileri depolamaz.

## <a name="comparing-the-client-types"></a>İstemci türlerini karşılaştırma
Ortak istemci ve gizli istemci uygulamaları arasında bazı benzerlikler ve farklılıklar aşağıda verilmiştir:

- Her iki tür uygulama da bir kullanıcı belirteci önbelleğini korur ve sessizce bir belirteç alabilir (belirteç zaten belirteç önbelleğinde olduğunda). Gizli istemci uygulamalarının, uygulamanın kendisi için olan belirteçler için bir uygulama belirteci önbelleği de vardır.
- Her iki uygulama türü de Kullanıcı hesabını yönetir ve kullanıcı belirteci önbelleğinden bir hesap alabilir, tanımlayıcısından bir hesap alabilir veya bir hesabı kaldırabilir.
- Ortak istemci uygulamalarında bir belirteç edinmenin dört yolu vardır (dört kimlik doğrulama akışı). Gizli istemci uygulamalarında belirteç edinmenin üç yolu vardır (ve kimlik sağlayıcısı yetkilendirme uç noktası URL 'sini hesaplamak için bir yol). Daha fazla bilgi için bkz. [belirteçleri](msal-acquire-cache-tokens.md)alma.

ADAL kullandıysanız, ADAL 'nin kimlik doğrulama bağlamından farklı olarak, MSAL ' de istemci KIMLIĞI ( *uygulama kimliği* veya uygulama *kimliği* olarak da bilinir) uygulamanın oluşturulmasından bir kez geçirildiğine dikkat edin. Uygulama bir belirteç aldığında yeniden geçirilmesi gerekmez. Bu, hem genel hem de gizli istemci uygulamaları için geçerlidir. Gizli istemci uygulamalarının oluşturucuları, istemci kimlik bilgileri: kimlik sağlayıcısıyla paylaştığı gizli dizi.

## <a name="next-steps"></a>Sonraki adımlar
Şunları öğrenin:
- [İstemci uygulama yapılandırma seçenekleri](msal-client-application-configuration.md)
- [MSAL.NET kullanarak istemci uygulamaları örnekleme](msal-net-initializing-client-applications.md)
- [MSAL.jskullanarak istemci uygulamalarını örnekleme ](msal-js-initializing-client-applications.md)
