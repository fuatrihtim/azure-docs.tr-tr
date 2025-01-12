---
title: Event Grid için Azure SignalR hizmeti olaylarını gönderme
description: SignalR hizmetiniz için Event Grid olaylarını nasıl etkinleştireceğinizi ve ardından istemci bağlantısı bağlı/bağlantısı kesilen olayları örnek bir uygulamaya göndermenizi gösteren bir kılavuz.
services: signalr
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: chenyl
ms.openlocfilehash: 84b83c1dd541418c446a89a6f51be668cb41e54e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94562653"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Azure SignalR Hizmeti'nden Event Grid'e olayları gönderme

Azure Event Grid, bir yayın-alt modeli kullanarak Tekdüzen olay tüketimi sağlayan, tam olarak yönetilen bir olay yönlendirme hizmetidir. Bu kılavuzda, Azure CLı 'yi kullanarak Azure SignalR hizmeti oluşturun, bağlantı olaylarına abone olur ve olayları almak için örnek bir Web uygulaması dağıtabilirsiniz. Son olarak, bağlanıp bağlantısını kesebilir ve örnek uygulamada olay yükünü görebilirsiniz.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Bu makaledeki Azure CLı komutları **Bash** kabuğu için biçimlendirilir. PowerShell veya komut Istemi gibi farklı bir kabuk kullanıyorsanız, satır devamlılık karakterlerini veya değişken atama satırlarını uygun şekilde ayarlamanız gerekebilir. Bu makale, gerekli komut düzenlemesini en aza indirmek için değişkenleri kullanır.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure Kaynak grubu, Azure kaynaklarınızı dağıttığınız ve yönettiğiniz bir mantıksal kapsayıcıdır. Aşağıdaki [az Group Create][az-group-create] komutu, *Eastus* bölgesinde *myresourcegroup* adlı bir kaynak grubu oluşturur. Kaynak grubunuz için farklı bir ad kullanmak istiyorsanız, `RESOURCE_GROUP_NAME` farklı bir değere ayarlayın.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>SignalR Hizmeti oluşturma

Ardından, aşağıdaki komutlarla kaynak grubuna bir Azure SignalR hizmeti dağıtın.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

SignalR hizmeti oluşturulduktan sonra Azure CLı, aşağıdakine benzer bir çıktı döndürür:

```json
{
  "externalIp": "13.76.156.152",
  "hostName": "clitest.servicedev.signalr.net",
  "hostNamePrefix": "clitest",
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/clitest1/providers/Microsoft.SignalRService/SignalR/clitest",
  "location": "southeastasia",
  "name": "clitest",
  "provisioningState": "Succeeded",
  "publicPort": 443,
  "resourceGroup": "clitest1",
  "serverPort": 443,
  "sku": {
    "capacity": 1,
    "family": null,
    "name": "Free_F1",
    "size": "F1",
    "tier": "Free"
  },
  "tags": null,
  "type": "Microsoft.SignalRService/SignalR",
  "version": "1.0"
}

```

## <a name="create-an-event-endpoint"></a>Olay uç noktası oluşturma

Bu bölümde, önceden oluşturulmuş örnek bir Web uygulamasını Azure App Service dağıtmak için GitHub deposunda bulunan Kaynak Yöneticisi şablonunu kullanırsınız. Daha sonra, kayıt defterinizin Event Grid olaylarına abone olur ve bu uygulamayı olayların gönderildiği uç nokta olarak belirtirsiniz.

Örnek uygulamayı dağıtmak için, `SITE_NAME` Web uygulamanız için benzersiz bir ad ayarlayın ve aşağıdaki komutları yürütün. Site adı, Web uygulamasının tam etki alanı adının (FQDN) bir kısmını oluşturduğundan Azure içinde benzersiz olmalıdır. Daha sonraki bir bölümde, kayıt defterinizin olaylarını görüntülemek için bir Web tarayıcısında uygulamanın FQDN 'sine gidebilirsiniz.

```azurecli-interactive
SITE_NAME=<your-site-name>

az deployment group create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Dağıtım başarılı olduktan sonra (birkaç dakika sürebilir), bir tarayıcı açın ve çalıştığından emin olmak için Web uygulamanıza gidin:

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Kayıt defteri olaylarına abone olma

Event Grid, izlemek istediğiniz olayları ve nereden gönderileceğini söylemek için bir *konuya* abone olursunuz. Aşağıdaki [az eventgrid olay-abonelik oluştur][az-eventgrid-event-subscription-create] komutu oluşturduğunuz Azure SignalR hizmetine abone olur ve Web uygulamanızın URL 'sini, olayların gönderileceği uç nokta olarak belirtir. Önceki bölümlerde doldurmuş olduğunuz ortam değişkenleri burada yeniden kullanılır, bu nedenle hiçbir düzenleme gerekmez.

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

Abonelik tamamlandığında aşağıdakine benzer bir çıktı görmeniz gerekir:

```JSON
{
  "deadLetterDestination": null,
  "destination": {
    "endpointBaseUrl": "https://$SITE_NAME.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "Microsoft.SignalRService.ClientConnectionConnected",
      "Microsoft.SignalRService.ClientConnectionDisconnected"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/Microsoft.SignalRService/SignalR/SignalRTestSvc/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-signalr",
  "labels": null,
  "name": "event-sub-signalr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "retryPolicy": {
    "eventTimeToLiveInMinutes": 1440,
    "maxDeliveryAttempts": 30
  },
  "topic": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/microsoft.signalrservice/signalr/SignalRTestSvc",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Kayıt defteri olaylarını tetikleme

Hizmet moduna geçin `Serverless Mode` ve SignalR hizmetine bir istemci bağlantısı kurun. Bir başvuru olarak [sunucusuz örnek](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless) gerçekleştirebilirsiniz.

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a separate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>Kayıt defteri olaylarını görüntüleme

Artık bir istemciyi SignalR hizmetine bağladınız. Event Grid Viewer Web uygulamanıza gidin ve bir olay görmeniz gerekir `ClientConnectionConnected` . İstemciyi sona erdirirsiniz, bir olay da görürsünüz `ClientConnectionDisconnected` .

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
