---
title: Azure Media Services V2 ve v3 arasındaki özellik boşlukları
description: Bu makalede, Azure Media Services V2 ile v3 arasındaki özellik boşlukları açıklanmaktadır.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 07e43bdb0969c26120231013cc0c0354e6968ceb
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567959"
---
# <a name="feature-gaps-between-azure-media-services-v2-and-v3"></a>Azure Media Services V2 ve v3 arasındaki özellik boşlukları

![Geçiş Kılavuzu logosu](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![geçiş adımları 2](./media/migration-guide/steps-2.svg)

Geçiş kılavuzunun bu bölümü, v2 ve v3 API 'Leri arasındaki farklılıklar hakkında ayrıntılı bilgi sağlar.

## <a name="feature-gaps-between-v2-and-v3-apis"></a>V2 ve v3 API 'Ler arasındaki özellik boşlukları

V3 API, v2 API 'SI ile aşağıdaki özellik boşluklarını içerir. V2 API 'Lerinde Media Encoder Standard gelişmiş özellikler Şu anda v3 'de kullanılamaz:

- Artık Azure Media Player için gerekli olmadığı için, girişte ses olmadığında sessiz ses izi ekleme.

- Girişte video olmadığında video izlemesi ekleme.

- Transkodlamaya sahip canlı olaylar şu anda, API çağrısı aracılığıyla kurşun ekleme orta akış ve ad işaretçisi ekleme işlemini desteklemez.

- Azure Media Premium Kodlayıcısı artık v2 sürümünde desteklenmeyecektir. Bunu 8 bit HEVC kodlaması için kullanıyorsanız, standart Kodlayıcıdaki yeni HEVC desteğini kullanın. 
    - Standart kodlayıcıya ses kanalı eşleme desteği ekledik.  [Media Services kodlaması Swagger belgelerindeki ses '](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json)i inceleyin.
    - MXF veya ProRes gibi üçüncü taraf lisanslı ürünün gelişmiş özelliklerini veya çıkış biçimlerini kullanıyorsanız, Telestream 'den Azure Iş ortağı çözümünü kullanarak v2 emekliliğin zamanına göre işlem yapılır. Alternatif olarak, Imagine Communications veya [Bitmovin](http://bitmovin.com)kullanabilirsiniz.

- V2 'deki akış uç noktasındaki "kullanılabilirlik kümesi" özelliği artık desteklenmiyor. V3 API 'sindeki [yüksek kullanılabilirliğe sahip VOD](./media-services-high-availability-encoding.md) teslimi için örnek projeye ve kılavuza bakın.

- Media Services v3 'de, FairPlay IV belirtilemez. Hem paketleme hem de lisans teslimi için Media Services kullanan müşterileri etkilemediğinden, FairPlay lisanslarını (karma mod) sunmak için bir üçüncü taraf DRM sistemi kullanılırken bir sorun olabilir.

- Rest 'teki varlıkların korunması için istemci tarafı depolama şifrelemesi v3 API 'sinde kaldırılmıştır ve bekleyen veriler için depolama hizmeti şifrelemesi ile değiştirilmiştir. V3 API 'Leri mevcut depolama şifrelenmiş varlıklarla çalışmaya devam eder, ancak yenilerini oluşturulmasına izin vermez.

## <a name="terminology-and-entity-changes"></a>Terminoloji ve varlık değişiklikleri

API üzerinde ek değişiklikler için bkz. [terminoloji ve varlık](migrate-v-2-v-3-differences-terminology.md) değişiklikleri.
