---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 2324e8d2689272b2548188ade0c8eca8d1d680e5
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105033534"
---
|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Bot hizmeti uç noktası geçerli bir HTTPS URI 'SI olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |İletim sırasında verilerin üzerinde oynanabilir. Kötüye kullanım ve üzerinde oynama sorunlarını çözmek için şifreleme sağlayan protokoller vardır. Botlarınızın yalnızca şifreli kanallar üzerinde iletişim kurmasını sağlamak için, uç noktasını geçerli bir HTTPS URI 'SI olarak ayarlayın. Bu, HTTPS protokolünün, verileri geçişte şifrelemek için kullanılmasını ve ayrıca genellikle mevzuata veya sektör standartlarıyla uyumluluk gereksinimini sağlar. Lütfen şu adresi ziyaret edin: [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines) . |Denetim, reddetme, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_ValidEndpoint_Audit.json) |
|[Bot hizmeti, müşteri tarafından yönetilen bir anahtarla şifrelenmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F51522a96-0869-4791-82f3-981000c2c67f) |Azure bot hizmeti, verilerinizi korumak ve kurumsal güvenlik ve uyumluluk taahhütlerini karşılamak için kaynağı otomatik olarak şifreler. Varsayılan olarak, Microsoft tarafından yönetilen şifreleme anahtarları kullanılır. Anahtarları yönetme veya aboneliğinize erişimi denetleme konusunda daha fazla esneklik için, kendi anahtarını getir (BYOK) olarak da bilinen müşteri tarafından yönetilen anahtarlar ' ı seçin. Azure bot hizmeti şifrelemesi hakkında daha fazla bilgi edinin: [https://docs.microsoft.com/azure/bot-service/bot-service-encryption](https://docs.microsoft.com/azure/bot-service/bot-service-encryption) . |Denetim, reddetme, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_CMKEnabled_Audit.json) |
