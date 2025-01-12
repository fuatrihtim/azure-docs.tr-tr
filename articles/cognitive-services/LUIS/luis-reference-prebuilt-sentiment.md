---
title: Yaklaşım Analizi-LUSıS
titleSuffix: Azure Cognitive Services
description: Yaklaşım Analizi yapılandırılırsa, LUSıS JSON yanıtı yaklaşım analizini içerir.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/01/2020
ms.openlocfilehash: 2e8e5a127741625fde7910aaabd421836148fc35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95018676"
---
# <a name="sentiment-analysis"></a>Yaklaşım analizi
Yaklaşım Analizi yapılandırılırsa, LUSıS JSON yanıtı yaklaşım analizini içerir. [Metin analizi](../text-analytics/index.yml) belgelerindeki yaklaşım analizi hakkında daha fazla bilgi edinin.

LUSıS Metin Analizi v2 kullanır. 

## <a name="resolution-for-sentiment"></a>Yaklaşım için çözüm

Yaklaşım verileri, 1 ile 0 arasında pozitif (1 ' e yakın) veya negatif (0 ' a yakın) verilerin yaklaşımını belirten bir puandır.

#### <a name="english-language"></a>[İngilizce dili](#tab/english)

Kültür olduğunda `en-us` Yanıt şu şekilde olur:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[Diğer diller](#tab/other-languages)

Diğer tüm kültürler için yanıt şu şekilde yapılır:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>Sonraki adımlar

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.