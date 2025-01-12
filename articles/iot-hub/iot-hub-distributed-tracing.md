---
title: IoT iletilerine bağıntı kimlikleri ekleme w/dağıtılmış izleme (öncesi)
description: Çözümünüz tarafından kullanılan Azure hizmetleri genelinde IoT iletilerini izlemek için dağıtılmış izleme özelliğini nasıl kullanacağınızı öğrenin.
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: jlian
ms.custom:
- amqp
- mqtt
- fasttrack-edit
- iot
ms.openlocfilehash: fca5c1a0aee6e62890d734e3233177549d932672
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643670"
---
# <a name="trace-azure-iot-device-to-cloud-messages-with-distributed-tracing-preview"></a>Dağıtılmış izleme (Önizleme) ile Azure IoT cihazdan buluta iletileri izleme

Microsoft Azure IoT Hub Şu anda, bir [Önizleme özelliği](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)olarak dağıtılmış izlemeyi destekliyor.

IoT Hub, dağıtılmış izlemeyi desteklemek için ilk Azure hizmetlerinden biridir. Daha fazla Azure hizmeti dağıtılmış izlemeyi destekledikleri için, çözümünüze dahil olan Azure hizmetleri genelinde IoT iletilerini takip edebilirsiniz. Dağıtılmış izleme için bir arka plan için bkz. [Dağıtılmış izleme](../azure-monitor/app/distributed-tracing.md).

IoT Hub için dağıtılmış izlemeyi etkinleştirmek aşağıdakileri yapabilmenizi sağlar:

- Her iletinin akışını, [izleme bağlamını](https://github.com/w3c/trace-context)kullanarak IoT Hub aracılığıyla tam olarak izleyin. Bu izleme bağlamı, bir bileşenden olayları diğer bir bileşenden ilişkilendirmenize olanak tanıyan bağıntı kimliklerini içerir. Bu, [cihaz ikizi](iot-hub-devguide-device-twins.md)kullanan bir alt küme veya tüm IoT cihaz iletileri için uygulanabilir.
- İzleme bağlamını [Azure Izleyici günlüklerine](monitor-iot-hub.md)otomatik olarak kaydet.
- IoT Hub ve yönlendirme uç noktalarına kadar cihazdan ileti akışını ve gecikme süresini ölçün ve anlayın.
- IoT çözümünüzdeki Azure dışı hizmetler için nasıl dağıtılmış izleme uygulamak istediğinizi göz önünde bulundurarak başlayın.

Bu makalede, dağıtılmış izleme ile [C Için Azure IoT cihaz SDK 'sını](iot-hub-device-sdk-c-intro.md) kullanırsınız. Diğer SDK 'lar için dağıtılmış izleme desteği hala devam ediyor.

## <a name="prerequisites"></a>Önkoşullar

- Dağıtılmış izlemenin önizlemesi Şu anda yalnızca şu bölgelerde oluşturulan IoT Hub 'Lar için desteklenir:

  - **Kuzey Avrupa**
  - **Güneydoğu Asya**
  - **Batı ABD 2**

- Bu makalede, IoT Hub 'ınıza telemetri iletileri gönderme hakkında bilgi sahibi olduğunuz varsayılır. [Telemetriyi Gönder C hızlı](quickstart-send-telemetry-c.md)başlangıcı ' nı tamamladığınızdan emin olun.

- IoT Hub 'ınıza bir cihaz kaydedin (her hızlı başlangıçta kullanılabilen adımlar) ve bağlantı dizesini aklınızda edin.

- En son [Git](https://git-scm.com/download/) sürümünü yükleyin.

## <a name="configure-iot-hub"></a>IoT Hub Yapılandır

Bu bölümde, dağıtılmış izleme özniteliklerini (bağıntı kimlikleri ve zaman damgaları) günlüğe kaydetmek için bir IoT Hub yapılandırırsınız.

1. [Azure Portal](https://portal.azure.com/)IoT Hub 'ınıza gidin.

1. IoT Hub 'ınızın sol bölmesinde, **izleme** bölümüne gidin ve **Tanılama ayarları**' na tıklayın.

1. **Tanılama ayarı Ekle**' ye tıklayın.

1. **Ad** alanına, yeni bir tanılama ayarı için bir ad girin. Örneğin, **Distributedtracingsettings**.

1. Günlüğün nereye gönderileceğini belirlemek için aşağıdaki seçeneklerden birini veya daha fazlasını seçin:

    - **Depolama hesabına Arşivle**: bir depolama hesabını günlüğe kaydetme bilgilerini içerecek şekilde yapılandırın.
    - **Bir olay hub 'ına akış**: Olay Hub 'ını günlüğe kaydetme bilgilerini içerecek şekilde yapılandırın.
    - **Log Analytics gönder**: Günlük Analizi çalışma alanını günlüğe kaydetme bilgilerini içerecek şekilde yapılandırın.

1. **Günlük** bölümünde, bilgilerini günlüğe kaydetmek istediğiniz işlemleri seçin.

    **Distributedizlemeyi** dahil ettiğinizden emin olun ve günlüğe kaydetmenin kaç gün boyunca **bekletilmesini** istediğinizi bir bekletme yapılandırın. Günlük tutma, depolama maliyetlerini etkiler.

    ![Distributedizleme kategorisinin IoT Tanılama ayarları için nerede olduğunu gösteren ekran görüntüsü](./media/iot-hub-distributed-tracing/diag-logs.png)

1. Yeni ayar için **Kaydet** ' e tıklayın.

1. Seçim İletileri farklı yerlere akışı görmek için, [yönlendirme kurallarını en az iki farklı uç](iot-hub-devguide-messages-d2c.md)noktaya ayarlayın.

Günlüğe kaydetme açıldıktan sonra, aşağıdaki durumlardan birinde geçerli izleme özellikleri içeren bir iletiye rastlana IoT Hub bir günlüğü kaydeder:

- İletiler IoT Hub ağ geçidine ulaşır.
- İleti IoT Hub tarafından işlenir.
- İleti özel uç noktalara yönlendirilir. Yönlendirmenin etkinleştirilmesi gerekir.

Bu Günlükler ve şemaları hakkında daha fazla bilgi edinmek için bkz. [IoT Hub kaynak günlüklerinde](monitor-iot-hub-reference.md#distributed-tracing-preview) [Izleyici IoT Hub](monitor-iot-hub.md) ve dağıtılmış izleme.

## <a name="set-up-device"></a>Cihazı ayarlama

Bu bölümde, [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)ile kullanım için bir geliştirme ortamı hazırlarsınız. Ardından, cihazınızın telemetri iletilerinde dağıtılmış izlemeyi etkinleştirmek için örneklerden birini değiştirirsiniz.

Bu yönergeler, Windows üzerinde örnek oluşturmak içindir. Diğer ortamlar için bkz. platforma özgü geliştirme için [c SDK 'sını](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compile) veya [önceden paketlenmiş c SDK 'sını](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#prepackaged-c-sdk-for-platform-specific-development)derleme.

### <a name="clone-the-source-code-and-initialize"></a>Kaynak kodunu kopyala ve Başlat

1. Visual Studio 2019 için ["C++ Ile masaüstü geliştirme" iş yükünü](/cpp/build/vscpp-step-0-installation?view=vs-2019&preserve-view=true) yükler. Visual Studio 2017 ve 2015 de desteklenir.

1. [CMake](https://cmake.org/)'i yükler. `PATH`Bir komut isteminden yazarak olduğunuzdan emin olun `cmake -version` .

1. Komut istemini veya Git Bash kabuğunu açın. [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub deposunun en son sürümünü kopyalamak için aşağıdaki komutları çalıştırın:

    ```cmd
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Bu işlemin tamamlanması için birkaç dakika beklemeniz gerekebilir.

1. Git deposunun kök dizininde bir `cmake` alt dizini oluşturun ve o klasöre gidin. Dizininden aşağıdaki komutları çalıştırın `azure-iot-sdk-c` :

    ```cmd
    mkdir cmake
    cd cmake
    cmake ..
    ```

    `cmake`C++ derleyicinizi bulamazsa, yukarıdaki komutu çalıştırırken derleme hataları alabilirsiniz. Bu durumda bu komutu [Visual Studio komut isteminde](/dotnet/framework/tools/developer-command-prompt-for-vs) çalıştırmayı deneyin. 

    Derleme başarılı olduktan sonra, son birkaç çıkış satırı aşağıdaki çıkışa benzer olacaktır:

    ```cmd
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

### <a name="edit-the-send-telemetry-sample-to-enable-distributed-tracing"></a>Dağıtılmış izlemeyi etkinleştirmek için telemetri gönder örneğini düzenleyin

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/azure-iot-distributed-tracing-sample/blob/master/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c" target="_blank">GitHub 'da örneği alın</a>

1. Kaynak dosyayı açmak için bir düzenleyici kullanın `azure-iot-sdk-c/iothub_client/samples/iothub_ll_telemetry_sample/iothub_ll_telemetry_sample.c` .

1. `connectionString` sabitinin bildirimini bulun:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=2)]

    `connectionString`Sabitin değerini, [telemetri C gönder hızlı](./quickstart-send-telemetry-c.md)başlangıcı ' nın [bir cihaz kaydetme](./quickstart-send-telemetry-c.md#register-a-device) bölümünde bir değişiklik yaptığınız cihaz bağlantı dizesiyle değiştirin.

1. Tanımla öğesini şu `MESSAGE_COUNT` şekilde değiştirin `5000` :

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=3)]

1. `IoTHubDeviceClient_LL_SetConnectionStatusCallback`İleti gönder döngüsünden önce bir bağlantı durumu geri çağırma işlevini kaydetmek için çağıran kod satırını bulun. Cihaz için dağıtılmış izlemeyi etkinleştirme çağrısı yapmak üzere aşağıda gösterildiği gibi bu satırın altına kod ekleyin `IoTHubDeviceClient_LL_EnablePolicyConfiguration` :

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_tracing&highlight=5)]

    `IoTHubDeviceClient_LL_EnablePolicyConfiguration`İşlevi, [Device TWINS](./iot-hub-devguide-device-twins.md)aracılığıyla yapılandırılan belirli ıothub özellikleri için ilkeler sunar. `POLICY_CONFIGURATION_DISTRIBUTED_TRACING`Yukarıdaki kod satırıyla etkinleştirildikten sonra, cihazın izleme davranışı cihaz ikizi yapılan dağıtılmış izleme değişikliklerini yansıtır.

1. Tüm kotayı kullanmadan örnek uygulamayı çalışır durumda tutmak için ileti gönder döngüsünün sonuna ikinci bir gecikme ekleyin:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_sleep&highlight=8)]

### <a name="compile-and-run"></a>Derle ve Çalıştır

1. Daha önce oluşturduğunuz CMake dizininden () *iothub_ll_telemetry_sample* projesi dizinine gidin `azure-iot-sdk-c/cmake` ve örneği derleyin:

    ```cmd
    cd iothub_client/samples/iothub_ll_telemetry_sample
    cmake --build . --target iothub_ll_telemetry_sample --config Debug
    ```

1. Uygulamayı çalıştırın. Cihaz, dağıtılmış izlemeyi destekleyen telemetri gönderir.

    ```cmd
    Debug/iothub_ll_telemetry_sample.exe
    ```

1. Uygulamayı çalışır durumda tutun. İsteğe bağlı olarak, konsol penceresine bakarak IoT Hub gönderilen iletiyi gözlemleyin.

<!-- For a client app that can receive sampling decisions from the cloud, check out [this sample](https://aka.ms/iottracingCsample).  -->

### <a name="workaround-for-third-party-clients"></a>Üçüncü taraf istemciler için geçici çözüm

C SDK kullanılmadan dağıtılmış izleme özelliğinin önizlenmesi **önemsiz değildir** . Bu nedenle, bu yaklaşım önerilmez.

İlk olarak, [IoT Hub Iletileri oluşturma ve okuma](iot-hub-devguide-messages-construct.md)için geliştirme Kılavuzu ' nu izleyerek iletilerinize tüm IoT Hub Protokolü temel öğelerini uygulamanız gerekir. Daha sonra, `tracestate` **System özelliği** olarak eklemek için MQTT/AMQP iletilerindeki protokol özelliklerini düzenleyin. Daha ayrıntılı belirtmek gerekirse:

* MQTT için, `%24.tracestate=timestamp%3d1539243209` , `1539243209` UNIX zaman damgası biçimindeki ileti oluşturma zamanına göre değiştirilmeli ileti konusuna ekleyin. Örnek olarak, [C SDK 'sindeki](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/iothubtransport_mqtt_common.c#L761) uygulamaya başvurun
* AMQP için `key("tracestate")` `value("timestamp=1539243209")` ileti ek açıklaması ekleyin. Başvuru uygulama için [buraya](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/uamqp_messaging.c#L527)bakın.

Bu özelliği içeren iletilerin yüzdesini denetlemek için, ikizi güncelleştirmeleri gibi bulut tarafından başlatılan olayları dinlemek için mantığı uygulayın.

## <a name="update-sampling-options"></a>Örnekleme seçeneklerini Güncelleştir 

Buluttan izlenecek ileti yüzdesini değiştirmek için cihaz ikizi güncelleştirmeniz gerekir. Portal 'da JSON Düzenleyicisi ve IoT Hub hizmeti SDK 'Sı dahil olmak üzere bu birden çok yöntemi gerçekleştirebilirsiniz. Aşağıdaki alt bölümlerde örnekler sağlanmaktadır.

### <a name="update-using-the-portal"></a>Portalı kullanarak güncelleştirme

1. [Azure Portal](https://portal.azure.com/)' de IoT Hub 'ınıza giderek **IoT cihazları**' na tıklayın.

1. Cihazınıza tıklayın.

1. **Dağıtılmış Izlemeyi etkinleştir (Önizleme)** bölümüne bakın ve **Etkinleştir**' i seçin.

    ![Azure portal 'da dağıtılmış izlemeyi etkinleştir](./media/iot-hub-distributed-tracing/azure-portal.png)

1. %0 ile %100 arasında bir **örnekleme oranı** seçin.

1. **Kaydet**’e tıklayın.

1. Birkaç saniye bekleyin ve **yenileme**' ye basın, sonra cihaz tarafından başarıyla onaylandıysanız, onay işareti içeren bir eşitleme simgesi belirir.

1. Telemetri iletisi uygulamasının konsol penceresine geri dönün. İle gönderilen iletileri `tracestate` uygulama özelliklerinde görürsünüz.

    ![İzleme durumu](./media/iot-hub-distributed-tracing/MicrosoftTeams-image.png)

1. Seçim Örnekleme hızını farklı bir değere değiştirin ve iletilerin uygulama özelliklerine dahil olduğu sıklığa göre değişikliği gözlemleyin `tracestate` .

### <a name="update-using-azure-iot-hub-for-vs-code"></a>VS Code için Azure IoT Hub kullanarak güncelleştir

1. VS Code yükleyip VS Code için Azure IoT Hub en son sürümünü [buradan](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)yüklersiniz.

1. VS Code açın ve [IoT Hub bağlantı dizesi ayarlayın](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit#user-content-prerequisites).

1. Cihazı genişletin ve **Dağıtılmış Izleme ayarını (Önizleme)** arayın. Altında, alt düğümün **Dağıtılmış Izleme ayarını (Önizleme) Güncelleştir** ' e tıklayın.

    ![Azure IoT Hub uzantısı 'nda dağıtılmış izlemeyi etkinleştir](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-1.png)

1. Açılan pencerede **Etkinleştir**' i seçin ve ardından ENTER tuşuna basarak örnekleme oranı olarak 100 ' i doğrulayın.

    ![Örnekleme modunu Güncelleştir](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-2.png)

    ![Örnekleme hızını Güncelleştir](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-3.png)

### <a name="bulk-update-for-multiple-devices"></a>Birden çok cihaz için toplu güncelleştirme

Birden çok cihaz için dağıtılmış izleme örnekleme yapılandırmasını güncelleştirmek için [otomatik cihaz yapılandırması](./iot-hub-automatic-device-management.md)'nı kullanın. Bu ikizi şemasını izlediğinizden emin olun:

```json
{
    "properties": {
        "desired": {
            "azureiot*com^dtracing^1": {
                "sampling_mode": 1,
                "sampling_rate": 100
            }
        }
    }
}
```

| Öğe adı | Gerekli | Tür | Açıklama |
|-----------------|----------|---------|-----------------------------------------------------|
| `sampling_mode` | Evet | Tamsayı | Örneklemeyi açmak ve kapatmak için şu anda iki mod değeri desteklenir. `1` ve üzerinde `2` . |
| `sampling_rate` | Yes | Tamsayı | Bu değer bir yüzde değeridir. Yalnızca ' den `0` `100` (kapsamlı) değerlere izin verilir.  |

## <a name="query-and-visualize"></a>Sorgulama ve görselleştirme

Bir IoT Hub tarafından günlüğe kaydedilen tüm izlemeleri görmek için Tanılama ayarları 'nda seçtiğiniz günlük deposunu sorgulayın. Bu bölümde, birkaç farklı seçenek gösterilmektedir.

### <a name="query-using-log-analytics"></a>Log Analytics kullanarak sorgulama

[Kaynak günlükleriyle Log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)ayarladıysanız, kategoride Günlükler arayarak sorgulayın `DistributedTracing` . Örneğin, bu sorgu günlüğe kaydedilen tüm izlemeleri gösterir:

```Kusto
// All distributed traces 
AzureDiagnostics 
| where Category == "DistributedTracing" 
| project TimeGenerated, Category, OperationName, Level, CorrelationId, DurationMs, properties_s 
| order by TimeGenerated asc  
```

Log Analytics gösterildiği gibi örnek Günlükler:

| TimeGenerated | OperationName | Kategori | Level | CorrelationId | DurationMs | Özellikler |
|--------------------------|---------------|--------------------|---------------|---------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-02-22T03:28:28.633 Z | DiagnosticIoTHubD2C | Distributedizleme | Bilgilendirici | 00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01 |  | {"DeviceID": "AZ3166", "messageSize": "96", "callerLocalTimeUtc": "2018-02-22T03:27:28.633 Z", "calleeLocalTimeUtc": "2018-02-22T03:27:28.687 Z"} |
| 2018-02-22T03:28:38.633 Z | DiagnosticIoTHubIngress | Distributedizleme | Bilgilendirici | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 20 | {"isRoutingEnabled": "false", "Parentspanıd": "0144d2590aacd909"} |
| 2018-02-22T03:28:48.633 Z | Diagnosticıthubegress | Distributedizleme | Bilgilendirici | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 23 | {"endpointType": "EventHub", "endpointName": "myEventHub", "Parentspanıd": "0144d2590aacd909"} |

Farklı türlerdeki günlükleri anlamak için bkz. [Azure IoT Hub dağıtılmış izleme günlükleri](monitor-iot-hub-reference.md#distributed-tracing-preview).

### <a name="application-map"></a>Uygulama Eşlemesi

IoT iletilerinin akışını görselleştirmek için uygulama Haritası örnek uygulamasını ayarlayın. Örnek uygulama, dağıtılmış izleme günlüklerini bir Azure Işlevi ve bir olay hub 'ı kullanarak [uygulama haritasına](../azure-monitor/app/app-map.md) gönderir.

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/e2e-diagnostic-provision-cli" target="_blank">GitHub 'da örneği alın</a>

Aşağıdaki görüntüde, üç yönlendirme uç noktası ile uygulama haritasında dağıtılmış izleme gösterilmektedir:

![Uygulama haritasında IoT dağıtılmış izleme](./media/iot-hub-distributed-tracing/app-map.png)

## <a name="understand-azure-iot-distributed-tracing"></a>Azure IoT dağıtılmış izlemeyi anlama

### <a name="context"></a>Bağlam

Kendi [başvuru mimarimiz](/azure/architecture/reference-architectures/iot) (yalnızca İngilizce) dahil olmak üzere birçok IoT çözümü, genellikle [mikro hizmet mimarisinin](/azure/architecture/microservices/)bir türevini izler. IoT çözümü daha karmaşık bir şekilde artdıkça, bir düzine veya daha fazla mikro hizmet kullanarak sonlandırın. Bu mikro hizmetler Azure 'dan gelebilir veya olmayabilir. IoT iletilerinin bırakılmasını veya yavaşlamasını zor hale gelebileceği işaret. Örneğin, 5 farklı Azure hizmeti ve 1500 etkin cihaz kullanan bir IoT çözümünüz vardır. Her cihaz 10 cihazdan buluta ileti/saniye gönderir (Toplam 15.000 ileti/saniye), ancak Web uygulamanızın yalnızca 10.000 mesaj/saniye gördüğü fark edersiniz. Sorun nerede? Fiprimi nasıl bulabilirim?

### <a name="distributed-tracing-pattern-in-microservice-architecture"></a>Mikro hizmet mimarisinde dağıtılmış izleme deseninin

Farklı hizmetlerde bir IoT iletisinin akışını yeniden oluşturmak için, her hizmet iletiyi benzersiz bir şekilde tanımlayan bir *BAĞıNTı kimliği* yaymalıdır. Merkezi bir sistemde toplandıktan sonra bağıntı kimlikleri ileti akışını görmenizi sağlar. Bu yönteme [Dağıtılmış izleme deseninin](/azure/architecture/microservices/logging-monitoring#distributed-tracing)adı verilir.

Microsoft, dağıtılmış izlemenin daha geniş benimsemesini desteklemek için [Dağıtılmış izleme Için W3C standart teklifine](https://w3c.github.io/trace-context/)katkıda bulunur.

### <a name="iot-hub-support"></a>IoT Hub desteği

Etkinleştirildikten sonra, IoT Hub için dağıtılmış izleme desteği şu akışı izler:

1. IoT cihazında bir ileti oluşturulur.
1. IoT cihazı, bu iletinin bir izleme bağlamı ile atanması gerektiğini (buluttan yardım ile) belirler.
1. SDK ileti `tracestate` özelliğine ileti oluşturma zaman damgasını içeren bir ekler.
1. IoT cihazı IoT Hub ileti gönderir.
1. İleti, IoT Hub ağ geçidine ulaşır.
1. IoT Hub `tracestate` ileti özelliklerinde öğesine bakar ve doğru biçimde olup olmadığını denetler.
1. Bu durumda IoT Hub, `trace-id` "atlama" için bir genel olarak benzersiz bir ileti üretir `span-id` ve bu dosyaları, işlem altında [IoT Hub dağıtılmış izleme günlüklerine](monitor-iot-hub-reference.md#distributed-tracing-preview) kaydeder `DiagnosticIoTHubD2C` .
1. İleti işleme tamamlandıktan sonra, IoT Hub bir tane oluşturur `span-id` ve işlem altında var olan ile birlikte günlüğe kaydeder `trace-id` `DiagnosticIoTHubIngress` .
1. İleti için yönlendirme etkinleştirildiyse, IoT Hub özel uç noktaya yazar ve `span-id` Kategori altında aynı olan başka bir oturum açar `trace-id` `DiagnosticIoTHubEgress` .
1. Yukarıdaki adımlar, oluşturulan her ileti için yinelenir.

## <a name="public-preview-limits-and-considerations"></a>Genel Önizleme sınırları ve konuları

- W3C Trace bağlam standardı için öneri şu anda çalışan bir tasladır.
- Şu anda, istemci SDK 'Sı tarafından desteklenen tek geliştirme dili C 'dir.
- Buluttan cihaza ikizi özelliği [IoT Hub temel katman](iot-hub-scaling.md#basic-and-standard-tiers)için kullanılamaz. Ancak, IoT Hub düzgün bir şekilde oluşturulmuş izleme bağlamı üst bilgisi görürseniz Azure Izleyici 'ye oturum açacak.
- Verimli bir işlem sağlamak için IoT Hub, dağıtılmış izlemenin bir parçası olarak gerçekleşebileceğini belirten bir kısıtlama getirmeyecektir.

## <a name="next-steps"></a>Sonraki adımlar

- Mikro hizmetlerde genel dağıtılmış izleme deseninin daha fazla bilgi edinmek için bkz. [mikro hizmet mimarisi deseninin: dağıtılmış izleme](https://microservices.io/patterns/observability/distributed-tracing.html).
- Dağıtılmış izleme ayarlarını çok sayıda cihaza uygulayacak şekilde yapılandırmayı ayarlamak için bkz. [IoT cihazlarını ölçeklendirerek yapılandırma ve izleme](./iot-hub-automatic-device-management.md).
- Azure Izleyici hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici nedir?](../azure-monitor/overview.md).
- Azure Izleyici 'yi IoT HUb ile kullanma hakkında daha fazla bilgi için bkz. [Monitor IoT Hub](monitor-iot-hub.md)