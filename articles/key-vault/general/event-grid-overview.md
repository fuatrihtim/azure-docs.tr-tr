---
title: Azure Event Grid ile Key Vault izleme
description: Key Vault olaylarına abone olmak için Azure Event Grid kullanma
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 1ee38196f0b1a6e00d385dc0d2c88d45a4291d3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90087445"
---
# <a name="monitoring-key-vault-with-azure-event-grid"></a>Azure Event Grid ile Key Vault izleme

Event Grid ile tümleştirme Key Vault, Anahtar Kasası 'nda depolanan bir gizli dizinin durumu değiştiğinde kullanıcılara bildirim gönderilmesini sağlar. Bir durum değişikliği, süresi dolacak (süresi dolmadan 30 gün önce), süresi dolan gizli anahtar veya yeni sürümü bulunan gizli anahtar olarak tanımlanır. Üç gizli dizi türü (anahtar, sertifika ve gizli) için bildirimler desteklenir.

Uygulamalar, karmaşık kod veya pahalı ve verimsiz yoklama Hizmetleri gerekmeden modern sunucusuz mimariler kullanarak bu olaylara tepki verebilir. Olaylar, [Azure işlevleri](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), hatta kendi web kancasına dahil olmak üzere olay işleyicisine [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) gönderilir ve yalnızca kullandığınız kadar ödersiniz. Fiyatlandırma hakkında bilgi için bkz. [Event Grid fiyatlandırması](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="key-vault-events-and-schemas"></a>Key Vault olayları ve şemaları

Olay Kılavuzu, olay iletilerini abonelere yönlendirmek için [olay abonelikleri](../../event-grid/concepts.md#event-subscriptions) kullanır. Key Vault olaylar, verilerdeki değişikliklere yanıt vermek için gereken tüm bilgileri içerir. EventType özelliği "Microsoft. Keykasası" ile başladığı için bir Key Vault olayını tanımlayabilirsiniz.

Daha fazla bilgi için bkz. [Key Vault olay şeması](../../event-grid/event-schema-key-vault.md).

> [!WARNING]
> Bildirim olayları yalnızca yeni parolalar, anahtarlar ve sertifikalar sürümlerinde tetiklenir ve bu bildirimleri almak için öncelikle anahtar kasasındaki olaya abone olmanız gerekir.

## <a name="practices-for-consuming-events"></a>Olayları tüketen uygulamalar

Key Vault olaylarını işleyen uygulamalar, önerilen birkaç uygulamayı izlemelidir:

* Aynı olay işleyicisine olayları yönlendirmek için birden çok abonelik yapılandırılabilir. Olayların belirli bir kaynaktan olduğunu varsaymamak, ancak beklediğiniz anahtar kasasından geldiğinden emin olmak için iletinin konusunu denetlemek önemlidir.
* Benzer şekilde, eventType için hazırlanmakta olan bir olay olduğunu ve aldığınız tüm olayların istediğiniz tür olacağını kabul edin.
* Anladığınızı alanları yoksayın.  Bu uygulama, gelecekte eklenebilecek yeni özelliklere dayanıklı tutmaya yardımcı olur.
* Olayları belirli bir olayla sınırlamak için "konu" önekini ve sonek eşleşmelerini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Key Vault genel bakış](overview.md)
- [Azure Event Grid’e genel bakış](../../event-grid/overview.md)
- Nasıl yapılır: [Key Vault olaylarını Otomasyon Runbook 'A yönlendirme](event-grid-tutorial.md).
- Nasıl yapılır: [Anahtar Kasası gizli anahtarı değiştiğinde e-posta alma](event-grid-logicapps.md)
- [Azure Key Vault için Azure Event Grid olay şeması](../../event-grid/event-schema-key-vault.md)
- [Azure Otomasyonu’na genel bakış](../../automation/index.yml)
