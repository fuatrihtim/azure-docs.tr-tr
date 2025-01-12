---
title: Yüksek Sesle Okumayı yapılandırma
titleSuffix: Azure Cognitive Services
description: Bu makalede çeşitli seçenekleri sesli okuma için nasıl yapılandıracağınız gösterilir.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 8b45fe07b4bd42059199197bc5b99f20f6b2a8cf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608725"
---
# <a name="how-to-configure-read-aloud"></a>Okumayı sesli olarak yapılandırma

Bu makalede, tam ekran okuyucusu 'nda yüksek sesle okunan çeşitli seçeneklerin nasıl yapılandırılacağı gösterilmektedir.

## <a name="automatically-start-read-aloud"></a>Sesli okumayı otomatik olarak Başlat

`options`Parametresi, okumayı yüksek sesle yapılandırmak için kullanılabilen tüm bayrakları içerir. `autoplay` `true` Derinlikli okuyucu başlatıldıktan sonra otomatik olarak okumayı otomatik olarak başlatmak için olarak ayarlayın.

```typescript
const options = {
    readAloudOptions: {
        autoplay: true
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

> [!NOTE]
> Tarayıcı sınırlamaları nedeniyle, Safari 'de otomatik kayıttan yürütme desteklenmez.

## <a name="configure-the-voice"></a>Sesi yapılandırma

`voice`Ya da olarak `male` ayarlayın `female` . Dillerin hepsi her iki sesi de desteklemez. Daha fazla bilgi için bkz. [dil desteği](./language-support.md) sayfası.

```typescript
const options = {
    readAloudOptions: {
        voice: 'female'
    }
};
```

## <a name="configure-playback-speed"></a>Kayıttan yürütme hızını yapılandırma

`speed`Arasında bir sayıya ayarla `0.5` (%50) ve `2.5` (%250%) belirtin. Bu aralığın dışındaki değerler 0,5 veya 2,5 ' e kadar çakışacaktır.

```typescript
const options = {
    readAloudOptions: {
        speed: 1.5
    }
};
```

## <a name="next-steps"></a>Sonraki adımlar

* [Tam ekran okuyucu SDK başvurusunu](./reference.md) keşfet