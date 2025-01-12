---
title: Kubernetes 'e IoT Edge yüklemesi | Microsoft Docs
description: Yerel bir geliştirme kümesi ortamı kullanarak Kubernetes 'e IoT Edge nasıl yükleneceğinizi öğrenin
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d5fc7b6b480056b56a2776cebd0fa87a5b96f9f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201664"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Kubernetes 'e IoT Edge nasıl yüklenir (Önizleme)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge, Kubernetes ile, dayanıklı ve yüksek oranda kullanılabilir bir altyapı katmanı olarak yararlanarak tümleştirilebilir. Aşağıda, bu desteğin yüksek düzeyde bir IoT Edge çözümüne uygun olduğu yer verilmiştir:

![k8s girişi](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>Bu tümleştirme için iyi bir işlem, Kubernetes 'in Linux ve Windows 'un yanı sıra IoT Edge uygulamalar üzerinde çalıştırılabilirler.

## <a name="architecture"></a>Mimari 
Kubernetes 'de, IoT Edge Edge iş yükü dağıtımları için *özel kaynak tanımı* (CRD) sağlar. IoT Edge Aracısı, bulut tarafından yönetilen istenen durumu yerel küme durumuyla bağdaştıran bir  *CRD denetleyicisinin* rolünü varsayar.

Modül ömrü, modül kullanılabilirliğini tutan ve yerleştirmesini seçen Kubernetes Zamanlayıcı tarafından yönetilir. IoT Edge, en üstte çalışan Edge uygulaması platformunu yönetir ve IoT Hub belirtilen istenen durumu, uç kümedeki durumuyla sürekli olarak mutabık kılma. Uygulama modeli, IoT Edge modüller ve rotalar temel alınarak tanıdık bir modeldir. IoT Edge aracısı denetleyicisi, *Otomatik* çeviri IoT Edge uygulama modelini pods, dağıtımlar, hizmetler vb. Kubernetes yerel yapılarına uygular.

Üst düzey bir mimari diyagramı aşağıda verilmiştir:

![Kubernetes yay](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

Edge dağıtımının her bileşeni, cihaza özel bir Kubernetes ad alanı kapsamına alınır ve aynı küme kaynaklarının birden çok uç cihaz ve dağıtımları arasında paylaşılmasını mümkün kılar.

>[!NOTE]
>Kubernetes üzerinde IoT Edge [genel önizlemede](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="tutorials-and-references"></a>Öğreticiler ve başvurular 

Derinlemesine öğreticiler ve başvurular da dahil olmak üzere daha fazla bilgi için lütfen [Kubernetes Preview docs mini sitesindeki IoT Edge](https://aka.ms/edgek8sdoc) bakın.
