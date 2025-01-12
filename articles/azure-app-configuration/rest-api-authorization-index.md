---
title: Azure uygulama yapılandırma REST API-yetkilendirme
description: Azure Uygulama yapılandırması ile yetkilendirme için başvuru sayfaları REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 70f05799ce2856ad490937a17b456e78789088f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932651"
---
# <a name="authorization"></a>Yetkilendirme

Yetkilendirme, bir çağıranın istek yaparken sahip olduğu izinleri belirlemede kullanılan yordamı ifade eder. Birden çok yetkilendirme modeli vardır. Bir istek için kullanılan yetkilendirme modeli, kullanılan [kimlik doğrulama](./rest-api-authentication-index.md) yöntemine bağlıdır. Yetkilendirme modelleri aşağıda listelenmiştir.

## <a name="hmac"></a>HMAC

HMAC kimlik doğrulamasıyla ilişkili [yetkilendirme modeli](./rest-api-authorization-hmac.md) modeli, izinleri salt okunurdur veya okuma-yazma olarak böler. Ayrıntılar için [HMAC yetkilendirme](./rest-api-authorization-hmac.md) sayfasına bakın.

## <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD) kimlik doğrulamasıyla ilişkili [yetkilendirme modeli](./rest-api-authorization-azure-ad.md) , izinleri denetlemek IÇIN Azure RBAC kullanır. Ayrıntılar için bkz. [Azure AD yetkilendirme](./rest-api-authorization-azure-ad.md) sayfası.
