---
title: Anahtar İfade Ayıklama kapsayıcısı için Docker Pull
titleSuffix: Azure Cognitive Services
description: Anahtar İfade Ayıklama kapsayıcısı için Docker Pull komutu
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 02dde8a27b9687e58bf1a09c1a951f306937f0d6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90906055"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Anahtar İfade Ayıklama kapsayıcısı için Docker Pull

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)Microsoft Container Registry bir kapsayıcı görüntüsünü indirmek için komutunu kullanın.

Metin Analizi kapsayıcıları için kullanılabilir etiketlerin tam açıklaması için, Docker Hub 'ındaki [anahtar ifade ayıklama](https://go.microsoft.com/fwlink/?linkid=2018757) kapsayıcısına bakın.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase:latest
```
