---
title: Event Grid ile Azure Media Services olaylarını izleme
description: Bu makalede, Azure CLı kullanarak Azure Media Services olaylarını izlemek için Event Grid nasıl abone olunacağı gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 03/17/2021
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 47d079aa5038a5ef09df30f0561c258bfbf6a9f7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104610074"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Azure CLı kullanarak Event Grid Media Services olaylar oluşturma ve izleme

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

Azure Event Grid, bulut için bir olay oluşturma hizmetidir. Bu hizmet, olay iletilerini abonelere yönlendirmek için [olay abonelikleri](../../../event-grid/concepts.md#event-subscriptions) kullanır. Media Services olaylar, verilerdeki değişikliklere yanıt vermek için gereken tüm bilgileri içerir. EventType özelliği "Microsoft. Media." ile başladığı için bir Media Services olayı tanımlayabilirsiniz. Daha fazla bilgi için bkz. [Media Services olay şemaları](media-services-event-schemas.md).

Bu makalede, Azure CLı kullanarak Azure Media Services hesabınıza yönelik olaylara abone olabilirsiniz. Ardından, sonucu görüntülemek için olayları tetiklersiniz. Normalde olayları, olay verilerini işleyen ve eylemler gerçekleştiren bir uç noktaya gönderirsiniz. Bu makalede, olayları toplayıp görüntüleyen bir Web uygulamasına gönderirsiniz.

## <a name="prerequisites"></a>Önkoşullar

- Etkin bir Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) oluşturun.
- CLı 'yi yerel olarak yükleyip kullanın, bu makale için Azure CLı 2,0 veya sonraki bir sürümü gerekir. Kullandığınız sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme](/cli/azure/install-azure-cli).

    Şu anda, tüm [Media Services v3 CLI](/cli/azure/ams) komutları Azure Cloud Shell çalışmaz. CLı 'nın yerel olarak kullanılması önerilir.

- [Media Services hesabı oluşturun](../create-account-howto.md).

    Kaynak grubu adı ve Media Services hesap adı için kullandığınız değerleri anımsadığınızdan emin olun.

## <a name="create-a-message-endpoint"></a>İleti uç noktası oluşturma

Media Services hesabı için olaylara abone olmadan önce olay iletisi için uç noktayı oluşturalım. Normalde, olay verileri temelinde uç nokta eylemleri gerçekleştirir. Bu makalede, olay iletilerini görüntüleyen [önceden oluşturulmuş bir Web uygulaması](https://github.com/Azure-Samples/azure-event-grid-viewer) dağıtırsınız. Dağıtılan çözüm bir App Service planı, App Service web uygulaması ve GitHub'dan kaynak kod içerir.

1. Çözümü aboneliğinize dağıtmak için **Azure'a Dağıt**'ı seçin. Azure portalında parametre değerlerini girin.

   [!["Azure 'a dağıt" etiketli bir düğmeyi gösteren resim.](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)

1. Dağıtımın tamamlanması birkaç dakika sürebilir. Dağıtım başarıyla gerçekleştirildikten sonra, web uygulamanızı görüntüleyip çalıştığından emin olun. Web tarayıcısında şu adrese gidin: `https://<your-site-name>.azurewebsites.net`

"Azure Event Grid Görüntüleyici" sitesine geçerseniz, henüz hiç olayına sahip olduğunu görürsünüz.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../../includes/event-grid-register-provider-portal.md)]

## <a name="set-the-azure-subscription"></a>Azure aboneliğini ayarlama

Aşağıdaki komutta, Media Services hesabı için kullanmak istediğiniz Azure abonelik kimliğini sağlayın. [Abonelikler](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)’e giderek erişimine sahip olduğunuz aboneliklerin listesini görebilirsiniz.

```azurecli
az account set --subscription mySubscriptionId
```

## <a name="subscribe-to-media-services-events"></a>Media Services olaylarına abone olma

İzlemek istediğiniz olayları Event Grid söylemek için bir makaleye abone olursunuz. Aşağıdaki örnek, oluşturduğunuz Media Services hesabına abone olur ve olay bildirimi için uç nokta olarak oluşturduğunuz Web sitesinden URL 'YI geçirir. 

`<event_subscription_name>`Olay aboneliğiniz için benzersiz bir adla değiştirin. `<resource_group_name>`Ve için `<ams_account_name>` , Media Services hesabını oluştururken kullandığınız değerleri kullanın. İçin, `<endpoint_URL>` Web UYGULAMANıZıN URL 'sini sağlayın ve `api/updates` GIRIŞ sayfası URL 'sine ekleyin. Abone olurken bitiş noktasını belirterek, Event Grid olayların bu uç noktaya yönlendirilmesini işler. 

1. Kaynak kimliğini al

    ```azurecli
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    Örnek:

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. Olaylara abone olma

    ```azurecli
    az eventgrid event-subscription create \
    --source-resource-id $amsResourceId \
    --name <event_subscription_name> \
    --endpoint <endpoint_URL>
    ```

    Örnek:

    ```
    az eventgrid event-subscription create --source-resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > Doğrulama el sıkışma uyarısı alabilirsiniz. Birkaç dakika bekleyin ve el sıkışma doğrulaması gerekir.

Şimdi, Event Grid iletiyi uç noktanıza nasıl dağıttığını görmek için olayları tetikleyelim.

## <a name="send-an-event-to-your-endpoint"></a>Uç noktanıza olay gönderme

Bir kodlama işi çalıştırarak Media Services hesabı için olayları tetikleyebilirsiniz. Bir dosya kodlamak ve olay göndermeye başlamak için [Bu hızlı](../stream-files-dotnet-quickstart.md) başlangıcı izleyebilirsiniz. 

Web uygulamanızı yeniden görüntüleyin ve buna bir abonelik doğrulama olayının gönderildiğine dikkat edin. Uç noktanın olay verilerini almak istediğini doğrulayabilmesi için Event Grid doğrulama olayını gönderir. Uç noktanın `validationResponse` olarak ayarlanması `validationCode` . Daha fazla bilgi için bkz. [güvenlik ve kimlik doğrulaması Event Grid](../../../event-grid/security-authentication.md). Aboneliği nasıl doğrulayacağını görmek için Web uygulaması kodunu görüntüleyebilirsiniz.

> [!TIP]
> Göz simgesini seçerek olay verilerini genişletin. Tüm olayları görüntülemek istiyorsanız, sayfayı yenilemeyin.

![Abonelik olayını görüntüleme](../media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Sonraki adımlar

[Karşıya yükleme, kodlama ve akışla aktarma](../stream-files-tutorial-with-api.md)
