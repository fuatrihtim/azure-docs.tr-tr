---
title: Azure nesne tutturucularının genel bakış
description: Azure nesne bağlantıları 'nın fiziksel dünyadaki nesneleri algılamaya nasıl yardımcı olduğunu öğrenin.
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: b4284411fbe09a0d0ce8412e6e5a5df464f35564
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462873"
---
# <a name="azure-object-anchors-overview"></a>Azure nesne tutturucularının genel bakış

Azure nesne bağlantıları, bir uygulamanın bir 3B modeli kullanarak fiziksel dünyadaki bir nesneyi algılamasını ve 6DoF pozlarını tahmin etmesini sağlar. 6DoF (6 derece serbestlik) pozu bir 3B model ile fiziksel karşılığı, gerçek nesne arasında bir döndürme ve çeviri olarak tanımlanır.

Azure nesne bağlantıları, model dönüştürmesi için bir hizmetten ve HoloLens için bir çalışma zamanı istemci SDK 'sına sahiptir. Hizmet bir 3B nesne modeli kabul eder ve bir Azure nesne çıpası modeli verir. Azure nesne bağlantıları modeli, HoloLens uygulamasının fiziksel dünyadaki bu modelin örnek örneklerini algılamasına, algılamasına ve izlemesine olanak tanımak için çalışma zamanı SDK 'Sı ile birlikte kullanılır.

:::image type="content" source="./media/aoa-overview.jpg" alt-text="Azure nesne bağlayıcıları işlem sürüyor":::

## <a name="examples"></a>Örnekler

Azure nesne bağlantıları tarafından etkinleştirilen bazı örnek kullanım örnekleri şunlardır:

- **Eğitim**. Hologram hizalamasını el ile ayarlamak zorunda kalmadan, çalışanlarınız için karma gerçeklik eğitimi deneyimleri oluşturun. Karma Gerçeklik eğitim deneyimlerinizi otomatik algılama ve izleme ile genişletmek istiyorsanız, modelinizi Azure nesne bağlayıcıları hizmetine alarak bir Markerless deneyimine yaklaşarak bir adım daha görürsünüz.

- **Görev Kılavuzu**. Karma Gerçeklik kullanılırken, bir dizi görev aracılığıyla yürüyen çalışanlar büyük ölçüde basitleştirilir. Fiziksel nesnenin en üstünde bulunan dijital yönergeler ve en iyi yöntemler, bir fabrika katında makine parçası veya ekip mutfak 'de bir kahve Oluşturucu olmak üzere, görev kümesini tamamlama zorluğunu önemli ölçüde azaltabilir. Bu deneyimlerin tetiklenmesi genellikle bazı işaretleyici veya el ile hizalı bir biçim gerektirir, ancak Azure nesne bağlantıları ile, el ile görevle ilgili nesneyi otomatik olarak algılayan bir deneyim oluşturabilirsiniz. Daha sonra, işaretçiler veya el ile hizalama olmadan karışık gerçeklik kılavuzumuzu kullanarak sorunsuzca Flow.

- **Varlık bulma**. Fiziksel alanınızda bir nesne için bir 3B modeliniz zaten varsa, Azure nesne bağlayıcıları fiziksel ortamınızda o nesnenin örneklerini bulmanıza ve izlemenize olanak sağlayabilir.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki bölümlerde, Azure nesne bağlayıcılarını kullanarak uygulamaları kullanmaya ve oluşturmaya başlama hakkında bilgi sağlanmaktadır.

> [!div class="nextstepaction"]
> [Model alımı](quickstarts/get-started-model-conversion.md)

> [!div class="nextstepaction"]
> [Unity HoloLens](quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [MRTK ile Unity HoloLens](quickstarts/get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [HoloLens DirectX](quickstarts/get-started-hololens-directx.md)
