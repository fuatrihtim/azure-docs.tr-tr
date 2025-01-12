---
title: Azure IoT Hub uç noktalarını anlayın | Microsoft Docs
description: Geliştirici Kılavuzu-cihaza yönelik ve hizmete yönelik uç noktalar IoT Hub ile ilgili başvuru bilgileri.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: System Architecture'
ms.openlocfilehash: a58e141c6232db08b125b265e3d4ad74c784ba24
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92152186"
---
# <a name="reference---iot-hub-endpoints"></a>Başvuru IoT Hub uç noktaları

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>IoT Hub adları

Kuruluşunuzun  **genel bakış** sayfasında uç noktalarınızı barındıran IoT Hub 'ın ana bilgisayar adını bulabilirsiniz. Varsayılan olarak, bir IoT Hub 'ının DNS adı şöyle görünür: `{your iot hub name}.azure-devices.net` .

## <a name="list-of-built-in-iot-hub-endpoints"></a>Yerleşik IoT Hub uç noktaları listesi

Azure IoT Hub, işlevselliğini çeşitli aktörliklere sunan çok kiracılı bir hizmettir. Aşağıdaki diyagramda IoT Hub açığa çıkardığı çeşitli uç noktalar gösterilmektedir.

![IoT Hub uç noktaları](./media/iot-hub-devguide-endpoints/endpoints.png)

Aşağıdaki listede uç noktalar açıklanmaktadır:

* **Kaynak sağlayıcısı**. IoT Hub kaynak sağlayıcısı [Azure Resource Manager](../azure-resource-manager/management/overview.md) arabirimini kullanıma sunar. Bu arabirim, Azure aboneliği sahiplerinin IoT Hub 'ları oluşturmasına ve silmesine ve IoT Hub özelliklerini güncelleştirmesine olanak sağlar. IoT Hub özellikler, cihaz düzeyi erişim denetimi ve buluttan cihaza ve cihazdan buluta mesajlaşma için işlevsel seçeneklere karşılık olarak [hub düzeyi güvenlik ilkelerini](iot-hub-devguide-security.md#access-control-and-permissions)yönetir. IoT Hub kaynak sağlayıcısı, [cihaz kimliklerini dışarı aktarmaya](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)de olanak sağlar.

* **Cihaz kimliği yönetimi**. Her IoT Hub 'ı, cihaz kimliklerini yönetmek için bir HTTPS REST uç noktası kümesi sunar (oluşturma, alma, güncelleştirme ve silme). Cihaz [kimlikleri](iot-hub-devguide-identity-registry.md) , cihaz kimlik doğrulaması ve erişim denetimi için kullanılır.

* **Cihaz ikizi yönetimi**. Her IoT Hub 'ı, [cihaz](iot-hub-devguide-device-twins.md) ikimlerini sorgulamak ve güncelleştirmek için bir hizmet 'e YÖNELIK https REST uç noktası kümesi sunar (etiketleri ve özellikleri Güncelleştir). 

* **İş yönetimi**. Her IoT Hub 'ı, [işleri](iot-hub-devguide-jobs.md)sorgulamak ve yönetmek için hizmete YÖNELIK BIR https REST uç noktası kümesi sunar.

* **Cihaz uç noktaları**. Kimlik kayıt defterindeki her bir cihaz için IoT Hub bir uç nokta kümesi kullanıma sunar. Aksi belirtilmedikçe, bu uç noktaların [MQTT v 3.1.1](https://mqtt.org/), https 1,1 ve [AMQP 1,0](https://www.amqp.org/) protokolleri kullanılarak sunulmasıdır. AMQP ve MQTT, 443 numaralı bağlantı noktasında de [WebSockets](https://tools.ietf.org/html/rfc6455) üzerinden kullanılabilir.

  * *Cihazdan buluta Iletiler gönderme*. Cihaz, [cihazdan buluta iletileri göndermek](iot-hub-devguide-messages-d2c.md)için bu uç noktayı kullanır.

  * *Buluttan cihaza Iletileri alın*. Bir cihaz, hedeflenen [buluttan cihaza iletileri](iot-hub-devguide-messages-c2d.md)almak için bu uç noktayı kullanır.

  * *Karşıya dosya yükleme Işlemini başlatın*. Bir cihaz, [bir dosyayı karşıya yüklemek](iot-hub-devguide-file-upload.md)için IoT Hub 'Den bir Azure Storage SAS URI 'si almak üzere bu uç noktayı kullanır.

  * *Device ikizi özelliklerini alın ve güncelleştirin*. Cihaz, bu uç noktayı [Device ikizi](iot-hub-devguide-device-twins.md)'in özelliklerine erişmek için kullanır. HTTPS desteklenmez.

  * *Doğrudan yöntem Istekleri alın*. Bir cihaz, [doğrudan metodun](iot-hub-devguide-direct-methods.md)isteklerini dinlemek için bu uç noktayı kullanır. HTTPS desteklenmez.

  [!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

* **Hizmet uç noktaları**. Her IoT Hub 'ı, çözüm arka ucunun cihazlarınızla iletişim kurması için bir uç nokta kümesi sunar. Tek bir istisna ile bu uç noktalar yalnızca [AMQP](https://www.amqp.org/) ve AMQP Over WebSockets protokolleri kullanılarak sunulur. Doğrudan yöntem çağırma uç noktası, HTTPS protokolü üzerinden sunulur.
  
  * *Cihazdan buluta Iletileri alma*. Bu uç nokta [Azure Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/)ile uyumludur. Bir arka uç hizmeti, cihazlarınız tarafından gönderilen [cihazdan buluta iletileri](iot-hub-devguide-messages-d2c.md) okumak için bunu kullanabilir. IoT Hub 'ınızda bu yerleşik uç noktaya ek olarak özel uç noktalar oluşturabilirsiniz.
  
  * *Buluttan cihaza Iletiler gönderin ve teslim bildirimleri alın*. Bu uç noktalar, çözüm arka ucunun güvenilir [buluttan cihaza iletiler](iot-hub-devguide-messages-c2d.md)göndermesini ve ilgili teslim veya süre sonu bildirimleri almasını sağlar.
  
  * *Dosya bildirimleri alın*. Bu mesajlaşma uç noktası, cihazlarınızın bir dosyayı başarıyla karşıya yüklemesi sırasında bildirimleri almanızı sağlar. 
  
  * *Doğrudan yöntem çağırma*. Bu uç nokta, bir arka uç hizmetinin bir cihazda [doğrudan yöntem](iot-hub-devguide-direct-methods.md) çağırmasına izin verir.
  
  * *İşlem izleme olaylarını alma*. Bu uç nokta, IoT Hub 'ınız bunları yaymak üzere yapılandırılmışsa işlem izleme olaylarını almanızı sağlar. Daha fazla bilgi için bkz. [IoT Hub işlemler izleme](iot-hub-operations-monitoring.md).

[Azure IoT SDK 'ları](iot-hub-devguide-sdks.md) makalesinde, bu uç noktalara erişmenin çeşitli yolları açıklanmaktadır.

Tüm IoT Hub uç noktaları [TLS](https://tools.ietf.org/html/rfc5246) protokolünü kullanır ve herhangi bir uç nokta şifrelenmemiş/güvenli olmayan kanallar üzerinde kullanıma sunulmaz.

## <a name="custom-endpoints"></a>Özel uç noktalar

İleti yönlendirme için uç noktalar olarak davranacak şekilde, aboneliğinizdeki mevcut Azure hizmetlerini IoT Hub 'ınıza bağlayabilirsiniz. Bu uç noktalar hizmet uç noktaları gibi davranır ve ileti yollarının havuzları olarak kullanılır. Cihazlar, ek uç noktalara doğrudan yazamaz. [İleti yönlendirme](../iot-hub/iot-hub-devguide-messages-d2c.md)hakkında daha fazla bilgi edinin.

IoT Hub Şu anda ek uç noktalar olarak aşağıdaki Azure hizmetlerini desteklemektedir:

* Azure depolama kapsayıcıları
* Event Hubs
* Service Bus Kuyrukları
* Service Bus Konuları

Ekleyebileceğiniz uç nokta sayısıyla ilgili sınırlar için bkz. [Kotalar ve azaltma](iot-hub-devguide-quotas-throttling.md).

## <a name="endpoint-health"></a>Uç nokta sistem durumu

[!INCLUDE [iot-hub-endpoint-health](../../includes/iot-hub-include-endpoint-health.md)]

## <a name="field-gateways"></a>Alan ağ geçitleri

Bir IoT çözümünde, bir *alan ağ geçidi* cihazlarınız ve IoT Hub uç noktalarınız arasında yer alır. Genellikle cihazlarınıza yakın bir şekilde bulunur. Cihazlarınız, cihazlar tarafından desteklenen bir protokolü kullanarak alan ağ geçidiyle doğrudan iletişim kurar. Alan ağ geçidi, IoT Hub tarafından desteklenen bir protokolü kullanarak bir IoT Hub uç noktasına bağlanır. Alan ağ geçidi, özel ağ geçidi yazılımını çalıştıran bir ayrılmış donanım aygıtı veya düşük güç lı bir bilgisayar olabilir.

Bir alan ağ geçidi uygulamak için [Azure IoT Edge](../iot-edge/index.yml) kullanabilirsiniz. IoT Edge, birden fazla cihazdan aynı IoT Hub bağlantı üzerinde çoğullama iletişimleri gibi işlevler sunar.

## <a name="next-steps"></a>Sonraki adımlar

Bu IoT Hub geliştirici kılavuzundaki diğer başvuru konuları şunları içerir:

* [Cihaz TWINS, işler ve ileti yönlendirme için sorgu dili IoT Hub](iot-hub-devguide-query-language.md)
* [Kotalar ve azaltma](iot-hub-devguide-quotas-throttling.md)
* [IoT Hub MQTT desteği](iot-hub-mqtt-support.md)
* [IoT Hub IP adresinizi anlayın](iot-hub-understand-ip-address.md)