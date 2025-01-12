---
title: OPC Ikizi-Azure nedir | Microsoft Docs
description: Bu makalede OPC Ikizi 'e genel bakış sunulmaktadır. OPC Ikizi, REST API 'Leri aracılığıyla endüstriyel cihazların keşif, kayıt ve uzaktan denetimini sağlar.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 9825e5e85c8da27b0f3fc2b9d78fcc9ca3513389
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646954"
---
# <a name="what-is-opc-twin"></a>OPC Ikizi nedir?

> [!IMPORTANT]
> Bu makaleyi güncelleştirdiğimiz sürece, en güncel içerik için bkz. [Azure endüstriyel IoT](https://azure.github.io/Industrial-IoT/) .

OPC Ikizi, bulutu ve fabrika ağını bağlamak için Azure IoT Edge ve IoT Hub kullanan mikro hizmetlerden oluşur. OPC Ikizi, REST API 'Leri aracılığıyla endüstriyel cihazların keşif, kayıt ve uzaktan denetimini sağlar. OPC Ikizi, OPC Birleşik mimarisi (OPC UA) SDK 'Sı gerektirmez, programlama dili belirsiz değildir ve sunucusuz bir iş akışına dahil edilebilir. Bu makalede çeşitli OPC Ikizi kullanım durumları açıklanmaktadır.

## <a name="discovery-and-control"></a>Bulma ve denetim
Bulma ve kayıt için basit için OPC Ikizi kullanabilirsiniz.

### <a name="simple-discovery-and-registration"></a>Basit bulma ve kayıt
OPC Ikizi, fabrika İşletmenleri 'nin fabrika ağını taramasını sağlar, böylece OPC UA sunucuları keşfedilebilir ve kaydedilebilir. Alternatif olarak, fabrika işletmenleri, OPC UA cihazlarını bilinen bir bulgu URL 'SI kullanarak el ile kaydedebilir. Örneğin, fabrika katında OPC Ikizi modülü olan IoT Edge ağ geçidi yüklendikten sonra tüm OPC UA cihazlarına bağlanmak için, Factory operatörü uzaktan ağ taramasını tetikleyebilir ve tüm OPC UA sunucularını görsel olarak görebilir. 

### <a name="simple-control"></a>Basit denetim
OPC Ikizi, fabrika İşletmenleri 'nin olaylara tepki vermesini ve fabrika katlarını otomatik olarak ya da el ile el ile yeniden yapılandırmasını sağlar. OPC Ikizi, OPC UA sunucusunda Hizmetleri çağırmak için REST API 'Ler sağlar, kendi adres alanına gözatabilir, değişkenleri okuma/yazma ve çalıştırma yöntemleri de sağlar. Örneğin, bir Boiler üretim satırını denetlemek için sıcaklık KPI 'sını kullanır. Sıcaklık algılayıcısı OPC yayımcısı kullanarak verilerde değişiklik yayımlar. Factory işleci, sıcaklığın eşiğe ulaştığı uyarıyı alır. Üretim satırı OPC Ikizi aracılığıyla otomatik olarak aşağı doğru yapılır. Fabrika işlecine soğuk bir şekilde bildirilir.

## <a name="authentication"></a>Kimlik Doğrulaması
Basit kimlik doğrulaması ve basit bir geliştirici deneyimi için OPC Ikizi kullanabilirsiniz.

### <a name="simple-authentication"></a>Basit kimlik doğrulaması 
OPC Ikizi, uçtan uca Azure Active Directory (AAD) tabanlı kimlik doğrulaması ve denetim kullanır. Örneğin OPC Ikizi, bir makinede bir işlecin ne yaptığını belirlemek için uygulamanın OPC Ikizi üzerine derlenmesini sağlar. Makine tarafında, OPC UA denetimi aracılığıyla yapılır. Bulut tarafında, REST API sabit bir istemci denetim günlüğünü ve AAD kimlik doğrulamasını depolarsınız.

### <a name="simple-developer-experience"></a>Basit geliştirici deneyimi 
OPC Ikizi, REST API 'Leri aracılığıyla herhangi bir programlama dilinde yazılmış uygulamalarla birlikte kullanılabilir. Geliştiriciler OPC UA istemcisini bir çözümle tümleştirdikleri için OPC UA SDK bilgisi gerekli değildir. OPC Ikizi, durumsuz, sunucusuz mimarilerde sorunsuz bir şekilde tümleştirilebilir. Örneğin, bir alarm ve olay panosu için uygulama geliştiren bir tam yığın Web geliştiricisi, C, C# veya tam OPC UA Stack uygulaması olmadan OPC Ikizi kullanarak JavaScript veya TypeScript 'teki olaylara yanıt vermek için mantığı yazabilir. 

## <a name="next-steps"></a>Sonraki adımlar

Artık OPC Ikizi ve kullanımları hakkında bilgi edindiğinize göre, önerilen sonraki adım aşağıda verilmiştir:

[OPC Vault nedir?](overview-opc-vault.md)