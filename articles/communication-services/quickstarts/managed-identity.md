---
title: Iletişim hizmetlerinde Yönetilen kimlikler kullanma
titleSuffix: An Azure Communication Services quickstart
description: Yönetilen kimlikler, Azure Iletişim Hizmetleri 'ne Azure VM 'Leri, işlev uygulamaları ve diğer kaynaklarda çalışan uygulamalardan erişim yetkisi verir.
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: peiliu
ms.reviewer: mikben
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: e4b71cc889615fd4784f11c9edd77b44ef421d9e
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110782"
---
# <a name="use-managed-identities"></a>Yönetilen kimlikleri kullanma
Yönetilen kimlikleri kullanarak Azure Iletişim Hizmetleri ile çalışmaya başlayın. Iletişim Hizmetleri kimliği ve SMS SDK 'Ları, [Azure kaynakları için yönetilen kimliklerle](../../active-directory/managed-identities-azure-resources/overview.md)Azure Active Directory (Azure AD) kimlik doğrulamasını destekler.

Bu hızlı başlangıçta, yönetilen kimlikleri destekleyen bir Azure ortamından kimliğe ve SMS SDK 'larına erişim yetkisi verme işlemleri gösterilir. Ayrıca, kodunuzun bir geliştirme ortamında nasıl test edileceğini açıklar.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity-python.md)]
::: zone-end

## <a name="next-steps"></a>Sonraki adımlar

- [Azure rol tabanlı erişim denetimi hakkında daha fazla bilgi edinin](../../../articles/role-based-access-control/index.yml)
- [.NET için Azure kimlik Kitaplığı hakkında daha fazla bilgi edinin](/dotnet/api/overview/azure/identity-readme)
- [Kullanıcı erişim belirteçleri oluşturma](../quickstarts/access-tokens.md)
- [SMS iletisi gönderme](../quickstarts/telephony-sms/send.md)
- [SMS hakkında daha fazla bilgi](../concepts/telephony-sms/concepts.md)
