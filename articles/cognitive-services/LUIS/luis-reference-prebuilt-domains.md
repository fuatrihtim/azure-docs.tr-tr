---
title: Önceden oluşturulmuş etki alanı başvurusu-LUSıS
titleSuffix: Azure Cognitive Services
description: Language Understanding Intelligent Services (LUSıS) ' den önceden oluşturulmuş amaç ve varlık koleksiyonları olan önceden oluşturulmuş etki alanları için başvuru.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 73b279f98011181b329cdb010041022ab0da57f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95018642"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>LUSıS uygulamanız için önceden oluşturulmuş etki alanı başvurusu
Bu başvuru [önceden oluşturulan etki alanları](./howto-add-prebuilt-models.md)hakkında bilgi sağlar. Bu, önceden oluşturulmuş amaç ve diğer varlıkların sunduğu varlıklardır.

[Özel etki alanları](luis-how-to-start-new-app.md), aksine, amaç ve modeller olmadan başlar. Önceden oluşturulmuş herhangi bir etki alanı amacını ve varlıklarını özel bir modele ekleyebilirsiniz.

## <a name="prebuilt-domains-per-language"></a>Dil başına önceden oluşturulmuş etki alanları

Aşağıdaki tabloda, şu anda desteklenen etki alanları özetlenmektedir. Ingilizce desteği genellikle diğerlerinden daha tamamdır.

| Varlık türü       | EN-US      | ZH-CN   | DE    | GS     | ES    | BT      | PT-BR |  JP  |      DILI |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:|
| Takvim  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
|İletişim  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| E-posta     | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| HomeAutomation          | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Notlar     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Yerler   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Restoran Antreservatıon  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| ToDo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Yardımcı Programlar      | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Hava Durumu        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Web    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

Önceden oluşturulmuş etki alanları ' de **desteklenmez** :

* Kanada Fransızcası
* Hintçe
* İspanyolca Meksika

## <a name="next-steps"></a>Sonraki adımlar

[Basit varlığı](reference-entity-simple.md)öğrenin.