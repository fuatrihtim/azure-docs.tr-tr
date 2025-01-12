---
title: Azure Time Series Insights ile tümleştirme
titleSuffix: Azure Digital Twins
description: Bkz. Azure dijital TWINS 'ten olay yollarını ayarlama Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 1/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 608f883304dbc8e1ea8b0127668125ae50ca0b11
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564951"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Azure dijital TWINS 'i Azure Time Series Insights ile tümleştirme

Bu makalede, Azure dijital TWINS 'i [Azure Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md)ile tümleştirmeyi öğreneceksiniz.

Bu makalede açıklanan çözüm, IoT çözümünüz hakkında geçmiş verileri toplayıp analiz etmenize olanak sağlayacak. Azure Digital Twins, Time Series Insights'a veri akışı yapmak için ideal bir hizmettir ve bilgilerinizi Time Series Insights'a göndermeden önce birden çok veri akışı arasında bağıntı kurup standart hale getirmenizi sağlar. 

## <a name="prerequisites"></a>Önkoşullar

Time Series Insights bir ilişki ayarlayabilmeniz için önce bir **Azure dijital TWINS örneğine** sahip olmanız gerekir. Bu örnek, verileri temel alarak dijital ikizi bilgilerini güncelleştirme özelliği ile ayarlanmalıdır, çünkü bu verileri Time Series Insights ' de izlenen şekilde görmek için ikizi bilgilerini birkaç kez güncelleştirmeniz gerekir. 

Bu ayarı zaten yoksa, Azure dijital TWINS [*öğreticisini izleyerek oluşturabilirsiniz: uçtan uca çözümü bağlama*](./tutorial-end-to-end.md). Öğreticide, dijital ikizi güncelleştirmelerini tetiklemek için bir sanal IoT cihazıyla birlikte çalışarak bir Azure dijital TWINS örneği ayarlama işleminde size yol gösterilir.

## <a name="solution-architecture"></a>Çözüm mimarisi

Aşağıdaki yoldan Azure dijital TWINS 'e Time Series Insights iliştirilecektir.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Uçtan uca bir senaryoda Azure hizmetlerinin görünümü, vurgulama Time Series Insights" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Güncelleştirme bildirimlerinin ikizini oluşturmak için rota ve filtre oluşturma

Azure dijital TWINS örnekleri, bir ikizi durumunun güncelleştirildiği her seferinde [ikizi Update olaylarını](how-to-interpret-event-data.md) yayabilir. Bu bölümde, daha fazla işleme için bu güncelleştirme olaylarını Azure [Event Hubs](../event-hubs/event-hubs-about.md) 'a yönlendiren bir Azure dijital TWINS [**olay yolu**](concepts-route-events.md) oluşturacaksınız.

Azure dijital TWINS [*öğreticisi: uçtan uca bir çözümü bağlama*](./tutorial-end-to-end.md) bir termometre 'nin bir odanın bulunduğu dijital bir ikizi üzerinde sıcaklık özniteliğini güncelleştirmek için kullanıldığı bir senaryoya yol gösterir. Bu model, Time Series Insights mantığınızı güncelleştirmeye gerek kalmadan temel alınan veri kaynağını değiştirme esnekliği sağlayan bir IoT cihazından telemetri iletmek yerine ikizi güncelleştirmelerini kullanır.

1. İlk olarak, Azure dijital TWINS örneğinden olay alacak bir olay hub 'ı ad alanı oluşturun. Aşağıdaki Azure CLı yönergelerini kullanabilir veya Azure portal kullanabilirsiniz: [*hızlı başlangıç: Azure Portal kullanarak bir olay hub 'ı oluşturma*](../event-hubs/event-hubs-create.md). Hangi bölgelerin Event Hubs desteklediğini görmek için [*bölgeye göre kullanılabilir Azure ürünlerini*](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs)ziyaret edin.

    ```azurecli-interactive
    az eventhubs namespace create --name <name for your Event Hubs namespace> --resource-group <resource group name> -l <region>
    ```

2. İkizi değişiklik olaylarını almak için ad alanı içinde bir olay hub 'ı oluşturun. Olay Hub 'ı için bir ad belirtin.

    ```azurecli-interactive
    az eventhubs eventhub create --name <name for your Twins event hub> --resource-group <resource group name> --namespace-name <Event Hubs namespace from above>
    ```

3. Gönderme ve alma izinleriyle bir [Yetkilendirme kuralı](/cli/azure/eventhubs/eventhub/authorization-rule#az-eventhubs-eventhub-authorization-rule-create) oluşturun. Kural için bir ad belirtin.

    ```azurecli-interactive
        az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from above> --eventhub-name <Twins event hub name from above> --name <name for your Twins auth rule>
    ```

4. Olay Hub 'ınızı Azure dijital TWINS örneğinize bağlayan bir Azure dijital TWINS [uç noktası](concepts-route-events.md#create-an-endpoint) oluşturun.

    ```azurecli-interactive
    az dt endpoint create eventhub -n <your Azure Digital Twins instance name> --endpoint-name <name for your Event Hubs endpoint> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace from above> --eventhub <Twins event hub name from above> --eventhub-policy <Twins auth rule from above>
    ```

5. İkiz güncelleştirme olaylarını uç noktanıza göndermek için Azure Digital Twins'de bir [rota](concepts-route-events.md#create-an-event-route) oluşturun. Bu rotadaki filtre yalnızca ikizi güncelleştirme iletilerinin uç noktanıza geçirilmesine izin verir.

    >[!NOTE]
    >Şu anda Cloud Shell'de şu komut gruplarını etkileyen **bilinen bir sorun** vardır: `az dt route`, `az dt model`, `az dt twin`.
    >
    >Çözüm için bu komutları çalıştırmadan önce Cloud Shell'de `az login` komutunu çalıştırın veya Cloud Shell yerine [local CLI](/cli/azure/install-azure-cli) ortamını kullanın. Bunun hakkında daha fazla bilgi için bkz. [*sorun giderme: Azure dijital TWINS 'de bilinen sorunlar*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).

    ```azurecli-interactive
    az dt route create -n <your Azure Digital Twins instance name> --endpoint-name <Event Hub endpoint from above> --route-name <name for your route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

Üzerinde geçiş yapmadan önce, bu makalenin ilerleyen kısımlarında daha sonra başka bir olay hub 'ı oluşturmak üzere kullanacağınız için, *Event Hubs ad alanı* ve *kaynak grubunuzu* göz atın.

## <a name="create-a-function-in-azure"></a>Azure’da işlev oluşturma

Ardından, bir işlev uygulaması içinde **Event Hubs tetiklenen bir işlev** oluşturmak Için Azure işlevleri 'ni kullanacaksınız. Uçtan uca öğreticide oluşturulan işlev uygulamasını kullanabilirsiniz ([*öğretici: uçtan uca bir çözümü bağlama*](./tutorial-end-to-end.md)) veya kendi kendinize. 

Bu işlev, bu ikizi Update olaylarını kendi özgün formdan JSON yaması belgeleri olarak JSON nesnelerine dönüştürür. Bu, yalnızca, TWINS 'inizden yalnızca güncelleştirilmiş ve eklenen değerleri içerir.

Azure Işlevleri ile Event Hubs kullanma hakkında daha fazla bilgi için bkz. Azure [*için azure Event Hubs tetikleyicisi işlevleri*](../azure-functions/functions-bindings-event-hubs-trigger.md).

Yayınlanan işlev uygulamanızın içinde, aşağıdaki kodla **ProcessDTUpdatetoTSI** adlı yeni bir işlev ekleyin.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

>[!NOTE]
>Bu `dotnet add package` komutu veya Visual Studio NuGet Paket Yöneticisi 'ni kullanarak paketleri projenize eklemeniz gerekebilir.

Sonra, yeni Azure işlevini **yayımlayın** . Bunun nasıl yapılacağı hakkında yönergeler için bkz. [*nasıl yapılır: verileri işlemek için bir Azure Işlevi ayarlama*](how-to-create-azure-function.md#publish-the-function-app-to-azure).

Bu işlev, bu işlevin oluşturduğu JSON nesnelerini, Time Series Insights bağlanacak ikinci bir olay hub 'ına gönderir. Sonraki bölümde bu olay hub 'ını oluşturacaksınız.

Daha sonra, bu işlevin kendi olay hub 'larınız ile bağlantı kurmak için kullanacağı bazı ortam değişkenlerini de ayarlayacaksınız.

## <a name="send-telemetry-to-an-event-hub"></a>Olay hub'ına telemetri gönderme

Şimdi ikinci bir olay hub 'ı oluşturacak ve işlevinizi bu olay hub 'ına akışını sağlayacak şekilde yapılandıracaksınız. Bu olay hub'ı da Time Series Insights'a bağlanacak.

### <a name="create-an-event-hub"></a>Olay hub’ı oluşturma

İkinci olay hub 'ını oluşturmak için aşağıdaki Azure CLı yönergelerini kullanabilir veya Azure portal: [*hızlı başlangıç: Azure Portal kullanarak bir olay hub 'ı oluşturun*](../event-hubs/event-hubs-create.md).

1. *Event Hubs ad* alanınızı ve *kaynak grubu* adınızı Bu makalenin önceki kısımlarında hazırlayın

2. Yeni bir olay hub 'ı oluşturun. Olay Hub 'ı için bir ad belirtin.

    ```azurecli-interactive
    az eventhubs eventhub create --name <name for your TSI event hub> --resource-group <resource group name from earlier> --namespace-name <Event Hubs namespace from earlier>
    ```
3. Gönderme ve alma izinleriyle bir [Yetkilendirme kuralı](/cli/azure/eventhubs/eventhub/authorization-rule#az-eventhubs-eventhub-authorization-rule-create) oluşturun. Kural için bir ad belirtin.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from earlier> --eventhub-name <TSI event hub name from above> --name <name for your TSI auth rule>
    ```

## <a name="configure-your-function"></a>İşlevinizi yapılandırma

Daha sonra, oluşturduğunuz Olay Hub 'ları için bağlantı dizelerini içeren işlev uygulamanızda ortam değişkenlerini ayarlamanız gerekir.

### <a name="set-the-twins-event-hub-connection-string"></a>Twins olay hub'ı bağlantı dizesini ayarlama

1. TWINS hub 'ı için yukarıda oluşturduğunuz yetkilendirme kurallarını kullanarak TWINS [Olay Hub 'ı bağlantı dizesini](../event-hubs/event-hubs-get-connection-string.md)alın.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <Twins event hub name from earlier> --name <Twins auth rule from earlier>
    ```

2. İşlev uygulamanızda, Bağlantı dizenizi içeren bir uygulama ayarı oluşturmak için sonuçtan *Primaryconnectionstring* değerini kullanın:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<Twins event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="set-the-time-series-insights-event-hub-connection-string"></a>Time Series Insights olay hub'ı bağlantı dizesini ayarlama

1. Time Series Insights hub 'ı için yukarıda oluşturduğunuz yetkilendirme kurallarını kullanarak, TSI [Olay Hub 'ı bağlantı dizesini](../event-hubs/event-hubs-get-connection-string.md)alın:

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. İşlev uygulamanızda, Bağlantı dizenizi içeren bir uygulama ayarı oluşturmak için sonuçtan *Primaryconnectionstring* değerini kullanın:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<TSI event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Time Series Insights örneği oluşturma ve bağlantı kurma

Sonra, ikinci (TSI) Olay Hub 'ından verileri almak için bir Time Series Insights örneği ayarlayacaksınız. Aşağıdaki adımları izleyin ve bu işlemle ilgili daha fazla ayrıntı için bkz. [*öğretici: Azure Time Series Insights Gen2 PAYG ortamı ayarlama*](../time-series-insights/tutorial-set-up-environment.md).

1. Azure portal, bir Time Series Insights ortamı oluşturmaya başlayın. 
    1. **Gen2 (L1)** fiyatlandırma katmanını seçin.
    2. Bu ortam için bir **zaman SERISI kimliği** seçmeniz gerekir. Zaman serisi KIMLIĞINIZ, Time Series Insights verilerinizi aramak için kullanacağınız üç değerden fazla olabilir. Bu öğretici için **$dtId** kullanabilirsiniz. [*Bir zaman SERISI kimliği seçmek Için en iyi yöntemler*](../time-series-insights/how-to-select-tsid.md)bölümünde kimlik değeri seçme hakkında daha fazla bilgi edinin.
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="Time Series Insights ortamı için oluşturma portalı UX. Aboneliğiniz, kaynak grubunuz ve ilgili açılan kutudan konumunuzu seçin ve ortamınız için bir ad seçin." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="Time Series Insights ortamı için oluşturma portalı UX. Gen2 (L1) Fiyatlandırma Katmanı seçilidir ve zaman serisi KIMLIĞI Özellik adı $dtId" lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::

2. **İleri: olay kaynağı** ' nı seçin ve daha önce, daha önce gelen TSI Olay Hub 'ınızı seçin Ayrıca, yeni bir Event Hubs Tüketici grubu oluşturmanız gerekir.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="Time Series Insights ortamı olay kaynağı için oluşturma portalı UX. Yukarıdaki olay hub 'ı bilgileriyle bir olay kaynağı oluşturuyorsunuz. Ayrıca yeni bir tüketici grubu oluşturuyorsunuz." lightbox="media/how-to-integrate-time-series-insights/event-source-twins.png":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>Azure dijital TWINS 'e IoT verileri göndermeye başlama

Time Series Insights veri göndermeye başlamak için Azure Digital TWINS 'te değişen veri değerleriyle Digital ikizi özelliklerini güncelleştirmeye başlamanız gerekir. [Az DT ikizi Update](/cli/azure/ext/azure-iot/dt/twin#ext-azure-iot-az-dt-twin-update) komutunu kullanın.

Uçtan uca öğreticiyi kullanıyorsanız ([*öğretici: uçtan uca çözümü bağlama*](tutorial-end-to-end.md)) ortam kurulumuna yardımcı olması için, örnekten *devicesimülatör* projesini çalıştırarak sanal IoT verilerini göndermeye başlayabilirsiniz. Yönergeler, öğreticinin [*benzetimini yapılandırın ve çalıştırın*](tutorial-end-to-end.md#configure-and-run-the-simulation) bölümünde bulunur.

## <a name="visualize-your-data-in-time-series-insights"></a>Time Series Insights verilerini görselleştirme

Şimdi, verilerin çözümlenmeye hazır Time Series Insights Örneğinizde akışı yapılmalıdır. İçinde gelen verileri araştırmak için aşağıdaki adımları izleyin.

1. Time Series Insights ortamınızı [Azure Portal](https://portal.azure.com) açın (portal arama çubuğunda ortamınızın adını arayabilirsiniz). Örneğe genel bakış bölümünde gösterilen *Time Series Insights gezgin URL 'sini* ziyaret edin.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Time Series Insights ortamınızın Genel Bakış sekmesinde Time Series Insights Explorer URL 'sini seçin":::

2. Gezgin 'de, sol tarafta gösterilen Azure dijital TWINS 'den üç TWINS 'nizi görürsünüz. _**Thermostat67**_ öğesini seçin, **sıcaklık**' ı seçin ve **Ekle**' ye basın.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="* * Thermostat67 * * seçin, * * sıcaklık * * öğesini seçin ve * * Ekle * * ' ye basın":::

3. Artık aşağıda gösterildiği gibi, termostat 'daki ilk sıcaklık ayarlarını görmeniz gerekir. *Room21* ve *Floor1* için aynı sıcaklık okuma güncellenir ve bu veri akışlarını kademeli olarak görselleştirebilirsiniz.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="İlk sıcaklık verileri, TSI Gezgininde grafiği oluşturulur. 68 ile 85 arasındaki rastgele değerlerin bir satırdır":::

4. Simülasyonun çok daha uzun süre çalışmasına izin verirseniz görselleştirmeniz şuna benzer şekilde görünür:
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Her bir ikizi için sıcaklık verileri, farklı renklerin üç paralel satırı halinde grafiği oluşturulur.":::

## <a name="next-steps"></a>Sonraki adımlar

Dijital TWINS, varsayılan olarak Time Series Insights düz bir hiyerarşi olarak saklanır, ancak model bilgileri ve kuruluş için çok düzeyli bir hiyerarşiyle zenginleştirilebilir. Bu işlem hakkında daha fazla bilgi edinmek için şunu okuyun: 

* [*Öğretici: model tanımlama ve uygulama*](../time-series-insights/tutorial-set-up-environment.md#define-and-apply-a-model) 

Azure dijital TWINS 'te zaten depolanmış olan model ve grafik verilerini kullanarak bu bilgileri otomatik olarak sağlamak için özel mantık yazabilirsiniz. TWINS grafiğindeki bilgileri yönetme, yükseltme ve alma hakkında daha fazla bilgi edinmek için aşağıdaki başvurulara bakın:

* [*Nasıl yapılır: dijital ikizi yönetme*](./how-to-manage-twin.md)
* [*Nasıl yapılır: ikizi grafiğini sorgulama*](./how-to-query-graph.md)