---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/04/2020
ms.author: mimart
ms.openlocfilehash: c1628894205754c1b8f1fc954796009afbdc8ed4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994340"
---
#### <a name="app-registrations"></a>[Uygulama kayıtları](#tab/app-reg-ga/) 

1. **Uygulama kayıtları**' yi seçin ve ardından API 'ye erişmesi gereken Web uygulamasını seçin. Örneğin, *WebApp1*.
1. **Yönet** altında **API izinleri**' ni seçin.
1. **Yapılandırılan izinler** altında **izin Ekle**' yi seçin.
1. **API 'Lerim** sekmesini seçin.
1. Web uygulamasına erişim verilmesi gereken API 'yi seçin. Örneğin, *webapi1*.
1. **İzin** altında **demo**' i genişletin ve daha önce tanımladığınız kapsamları seçin. Örneğin, *demo. Read* ve *demo. Write*.
1. **Izin Ekle**' yi seçin.
1. **Yönetici onayı ver ' i (kiracı adınız)** seçin.
1. Bir hesap seçmeniz istenirse, şu anda oturum açmış olan yönetici hesabınızı seçin veya Azure AD B2C kiracınızda en azından *bulut uygulama Yöneticisi* rolüne atanan bir hesapla oturum açın.
1. **Evet**’i seçin.
1. **Yenile**' yi seçin ve ardından "verilen..." öğesini doğrulayın Her iki kapsam için **durum** altında görünür.

#### <a name="applications-legacy"></a>[Uygulamalar (eski)](#tab/applications-legacy/)

1. Uygulamalar ' ı **(eski)** seçin ve ardından API 'ye erişmesi gereken Web uygulamasını seçin. Örneğin, *WebApp1*.
1. **API erişimi**' ni seçin ve ardından **Ekle**' yi seçin.
1. **API Seç** açılan menüsünde, Web uygulamasına erişim IZNI verilecek API 'yi seçin. Örneğin, *webapi1*.
1. **Kapsamları Seç** açılan menüsünde, daha önce tanımladığınız kapsamları seçin. Örneğin, *demo. Read* ve *demo. Write*.
1. **Tamam**’ı seçin.
