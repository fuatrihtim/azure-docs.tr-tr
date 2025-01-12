---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 007c19a10db5e000770c8c80189453d4a80edec2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "82204519"
---
Aşağıdaki tabloda, S1, S2, S3 ve F1 farklı hizmet katmanları ile ilişkili sınırlar listelenmektedir. Her katmandaki her bir *birimin* maliyeti hakkında daha fazla bilgi için bkz. [Azure IoT Hub fiyatlandırması](https://azure.microsoft.com/pricing/details/iot-hub/).

| Kaynak | S1 Standart | S2 Standart | S3 Standart | F1 Ücretsiz |
| --- | --- | --- | --- | --- |
| İleti/gün |400,000 |6,000,000 |300,000,000 |8,000 |
| En fazla birim |200 |200 |10 |1 |

> [!NOTE]
> S1 veya S2 katman hub ile 200 ' den fazla birim kullanmayı tahmin ediyorsanız veya S3 katmanı hub 'ı ile 10 birim kullanıyorsanız, Microsoft Desteği başvurun.
> 
> 

Aşağıdaki tabloda IoT Hub kaynakları için uygulanan sınırlar listelenmektedir.

| Kaynak | Sınır |
| --- | --- |
| Azure aboneliği başına en fazla ücretli IoT hub’ı sayısı |50 |
| Azure aboneliği başına en fazla ücretsiz IoT hub’ı sayısı |1 |
| Bir cihaz KIMLIĞINDEKI en fazla karakter sayısı | 128 |
| En fazla cihaz kimliği sayısı<br/> (tek bir çağrıda döndürülen) |1.000 |
| Cihazdan buluta iletiler için IoT Hub en fazla ileti bekletme süresi |7 gün |
| Cihazdan bulut iletinin en büyük boyutu |256 KB |
| Cihazdan buluta toplu işin en büyük boyutu |Tüm Batch için AMQP ve HTTP: 256 KB <br/>Her ileti için MQTT: 256 KB |
| Cihazdan buluta toplu işte en fazla ileti sayısı |500 |
| Buluttan cihaza iletinin en büyük boyutu |64 KB |
| Buluttan cihaza iletiler için en büyük TTL |2 gün |
| Buluttan cihaza iletiler için en fazla teslim <br/> sayısı |100 |
| Cihaz başına en fazla buluttan cihaza sıra derinliği |50 |
| Geri bildirim iletileri için en fazla teslim sayısı <br/> (buluttan cihaza iletiye yanıt olarak) |100 |
| Buluttan cihaza iletiye yanıt olarak <br/> geri bildirim iletileri için en fazla TTL |2 gün |
| [En büyük cihaz ikizi boyutu](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) | Etiketler için 8 KB ve istenen ve rapor edilen özellikler bölümleri için 32 KB |
| Maksimum cihaz ikizi dize anahtarı uzunluğu | 1 KB |
| Maksimum cihaz ikizi dize değeri uzunluğu | 4 KB |
| [Cihaz ikizindeki en büyük nesne derinliği](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 10 |
| Doğrudan yöntem yükünün en büyük boyutu | 128 KB |
| En büyük iş geçmişi bekletme süresi | 30 gün |
| En fazla eşzamanlı iş sayısı | 10 (S3 için), 5 (S2 için), 1 (S1 için) |
| En fazla ek uç nokta sayısı | 10 (S1, S2 ve S3 için) |
| En fazla ileti yönlendirme kuralı sayısı | 100 (S1, S2 ve S3 için) |
| Eşzamanlı bağlanan en fazla cihaz akışı sayısı | 50 (yalnızca S1, S2, S3 ve F1 için) |
| Maksimum cihaz akışı veri aktarımı | 300 MB/gün (yalnızca S1, S2, S3 ve F1 için) |

> [!NOTE]
> Bir Azure aboneliğinde 50 ' den fazla ücretli IoT Hub 'ı gerekiyorsa Microsoft Desteği başvurun.

> [!NOTE]
> Şu anda, tek bir IoT Hub 'ına kaydedilenebilir cihazların toplam sayısı ve modülleri 1.000.000 adresinden alınabilir. Bu sınırı artırmak istiyorsanız [Microsoft desteği](https://azure.microsoft.com/support/options/)başvurun.

Aşağıdaki kotalar aşıldığında istekleri kısıtlar IoT Hub.

| Kısıtlama | Hub başına değer |
| --- | --- |
| Kimlik kayıt defteri işlemleri <br/> (oluşturma, alma, listeleme, güncelleştirme ve silme), <br/> tek tek veya toplu içeri/dışarı aktarma |83.33/sn/birim (5000/dk/birim) (S3 için). <br/> 1.67/sn/birim (100/dak/Unit) (S1 ve S2 için). |
| Cihaz bağlantıları |6000/sn/birim (S3 için), 120/sn/birim (S2 için), 12/sn/birim (S1 için). <br/>En az 100/sn. |
| Cihazdan buluta gönderim |6000/sn/birim (S3 için), 120/sn/birim (S2 için), 12/sn/birim (S1 için). <br/>En az 100/sn. |
| Buluttan cihaza gönderim | 83.33/sn/birim (5000/dk/birim) (S3 için), 1.67/sn/birim (S1 ve S2 için). |
| Buluttan cihaza alım |833.33/sn/birim (50000/dk/birim) (S3 için), 16.67/sn/birim (örneğin, S1 ve S2 için). |
| Dosya karşıya yükleme işlemleri |83,33 dosya yükleme başlatma/sn/birim (5000/dk/birim) (S3 için), 1,67 dosya yükleme başlatma/sn/birim (S1 ve S2 için). <br/> 10.000 SAS URI 'Leri bir Azure depolama hesabı için aynı anda kullanılabilir.<br/> Tek seferde 10 SAS URI’si/cihaz çıkarılabilir. |
| Doğrudan yöntemler | 24 MB/sn/birim (S3 için), 480 KB/sn/birim (S2 için), 160 KB/sn/birim (S1 için).<br/> 8 KB 'lık azaltma ölçer boyutunu temel alır. |
| Cihaz ikizi okumaları | 500/sn/birim (S3 için), en fazla 100/sn veya 10/sn/birim (S2 için), 100/sn (S1 için) |
| Cihaz ikizi güncelleştirmeleri | 250/sn/birim (S3 için), en fazla 50/sn veya 5/sn/birim (S2 için), 50/sn (S1 için) |
| İş işlemleri <br/> (oluşturma, güncelleştirme, listeleme ve silme) | 83.33/sn/birim (5000/dk/birim) (S3 için), 1.67/sn/birim (100/dk/birim) (S2 için), 1.67/sn/birim (S1 için). |
| İşler işlemleri için cihaz başına aktarım hızı | 50/sn/birim (S3 için), en fazla 10/sn veya 1/sn/birim (S2 için), 10/sn (S1 için). |
| Cihaz akışı başlatma oranı | 5 yeni akış/sn (yalnızca S1, S2, S3 ve F1 için). |
