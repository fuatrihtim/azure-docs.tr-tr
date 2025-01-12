---
title: Azure Iletişim hizmetlerindeki bildirimler
titleSuffix: An Azure Communication Services concept document
description: Azure Iletişim Hizmetleri üzerinde oluşturulmuş uygulamaların kullanıcılarına bildirimler gönderin.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 9bf3067737ee1f4e15deaf31677513e5ac99cbd1
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108137"
---
# <a name="communication-services-notifications"></a>İletişim Hizmetleri bildirimleri

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


Azure Iletişim Hizmetleri sohbeti ve çağrı SDK 'Ları, sinyal iletilerinin bağlı istemcilere verimli ve güvenilir bir şekilde gönderilmesine izin veren gerçek zamanlı bir mesajlaşma kanalı oluşturur. Bu, karmaşık HTTP yoklama mantığını uygulamaya gerek kalmadan uygulamalarınıza zengin ve gerçek zamanlı iletişim işlevleri oluşturmanıza olanak sağlar. Ancak, mobil uygulamalarda, bu sinyal kanalı yalnızca uygulamanız ön planda etkin olduğunda bağlı kalır. Uygulamanız arka planda olduğunda kullanıcılarınızın gelen çağrıları veya sohbet iletilerini almasını istiyorsanız anında iletme bildirimleri kullanmanız gerekir.

Anında iletme bildirimleri uygulamanızdan kullanıcıların mobil cihazlarına bilgi göndermenize olanak tanır. Bir iletişim kutusu göstermek, ses çalmak veya gelen çağrı Kullanıcı arabirimini görüntülemek için anında iletme bildirimlerini kullanabilirsiniz. Azure Iletişim Hizmetleri, uygulamalarınıza anında iletme bildirimleri eklemenize olanak tanıyan [Azure Event Grid](../../event-grid/overview.md) ve [Azure Notification Hubs](../../notification-hubs/notification-hubs-push-notification-overview.md) tümleştirmelerini sağlar.

## <a name="trigger-push-notifications-via-azure-event-grid"></a>Azure Event Grid aracılığıyla anında iletme bildirimlerini tetikleme

Azure Iletişim Hizmetleri, gerçek zamanlı olay bildirimlerini güvenilir, ölçeklenebilir ve güvenli bir şekilde sunmak için [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) ile tümleşir. [Azure işlevini](../../azure-functions/functions-overview.md) veya Web kancasını tetikleyen bir olay Kılavuzu aboneliği oluşturarak kullanıcılarınıza mobil anında iletme bildirimleri sağlayan bir bildirim hizmeti oluşturmak için bu tümleştirmeden yararlanabilirsiniz.

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="Iletişim hizmetlerinin Event Grid ile nasıl tümleştirildiğini gösteren diyagram.":::

[Azure Iletişim hizmetlerinde olay işleme](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services)hakkında daha fazla bilgi edinin.

## <a name="deliver-push-notifications-via-azure-notification-hubs"></a>Azure Notification Hubs aracılığıyla anında iletme bildirimleri sunun

Bir Azure Notification Hub 'ını, bir kullanıcının mobil cihazına gelen bir çağrı aldıklarında otomatik olarak anında iletme bildirimleri göndermek için Iletişim Hizmetleri kaynağına bağlayabilirsiniz. Bu anında iletme bildirimlerini kullanarak uygulamanızı arka planda uyandırır ve kullanıcının çağrıyı kabul etmesini veya reddetmesine imkan tanıyan Kullanıcı ARABIRIMINI görüntüleyebilirsiniz.

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="İletişim hizmetlerinin Azure Notification Hubs ile nasıl tümleştirildiğini gösteren diyagram.":::

İletişim Hizmetleri, [doğrudan gönderme](/rest/api/notificationhubs/direct-send) API 'sini kullanarak platforma özgü çeşitli anında iletme bildirimi hizmetleriyle iletişim kurmak Için Azure Notification Hub 'ını doğrudan bir geçiş hizmeti olarak kullanır. Bu, uygulamalarınıza düşük gecikme süresi ve güvenilir çağrı bildirimleri sunmak için mevcut Azure Notification Hub kaynaklarınızı ve yapılandırmanızı yeniden kullanmanıza olanak tanır.

> [!NOTE]
> Şu anda yalnızca çağırma anında iletme bildirimleri destekleniyor.

### <a name="notification-hub-provisioning"></a>Notification Hub 'ı sağlama

İstemci cihazlarına Notification Hubs kullanarak anında iletme bildirimleri göndermek için, Iletişim Hizmetleri kaynağınız ile aynı abonelikte [bir Bildirim Hub 'ı oluşturun](../../notification-hubs/create-notification-hub-portal.md) . Kullanmak istediğiniz Platform Bildirim Sistemi için Azure Notification Hub 'ı yapılandırmanız gerekir. İstemci uygulamanızda Notification Hubs anında iletme bildirimleri alma hakkında bilgi edinmek için bkz. [Notification Hubs kullanmaya](../../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md) başlama ve sayfanın üst kısmındaki açılan listeden hedef istemci platformunuzu seçme.

> [!NOTE]
> APNs ve FCM platformları şu anda desteklenmektedir.
APNs platformunun belirteç kimlik doğrulama moduyla yapılandırılması gerekir. Sertifika kimlik doğrulama modu şu anda desteklenmiyor.

Bildirim Hub 'ı yapılandırıldıktan sonra, Azure Resource Manager Istemcisini kullanarak hub için bağlantı dizesi sağlayarak Iletişim Hizmetleri kaynağıyla ilişkilendirebilirsiniz ve Azure portal aracılığıyla ilişkilendirebilirsiniz. Bağlantı dizesi `Send` izinleri içermelidir. `Send`Yalnızca Hub 'ınız için özel izinlere sahip başka bir erişim ilkesi oluşturmanız önerilir. [Notification Hubs güvenlik ve erişim ilkeleri](../../notification-hubs/notification-hubs-push-notification-security.md) hakkında daha fazla bilgi edinin

#### <a name="using-the-azure-resource-manager-client-to-link-your-notification-hub"></a>Bildirim Hub 'ınızı bağlamak için Azure Resource Manager istemcisini kullanma

Azure Resource Manager oturum açmak için aşağıdakini yürütün ve kimlik bilgilerinizi kullanarak oturum açın.

```console
armclient login
```

 Başarıyla oturum açıldıktan sonra Bildirim Hub 'ını sağlamak için aşağıdakini yürütün:

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### <a name="using-the-azure-portal-to-link-your-notification-hub"></a>Bildirim Hub 'ınızı bağlamak için Azure portal kullanma

Portalda Azure Communication Services kaynağınız ' ne gidin. Iletişim Hizmetleri kaynağı içinde Iletişim Hizmetleri sayfasının sol menüsünde anında Iletme bildirimleri ' ni seçin ve daha önce sağladığınız Bildirim Hub 'ını bağlayın. Bağlantı dizenizi ve ResourceId 'nı buradan sağlamanız gerekir:

:::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="Azure portal içinde anında Iletme bildirimleri ayarlarını gösteren ekran görüntüsü.":::

> [!NOTE]
> Azure Bildirim Hub 'ı bağlantı dizesi güncelleştirilirse, Iletişim Hizmetleri kaynağının de güncellenmesi gerekiyor.
Hub 'ın bağlantılı olduğu herhangi bir değişiklik, veri düzlemine (bir bildirim gönderilirken) en uzun süre içinde yansıtılır ``10`` . Bu, daha önce gönderilen bildirimler **varsa** hub ilk kez bağlandığında da geçerlidir.

### <a name="device-registration"></a>Cihaz kaydı

Cihaz işleyicinizi Iletişim hizmetleriyle nasıl kaydedeceğinizi öğrenmek için [sesli arama hızlı başlangıç](../quickstarts/voice-video-calling/getting-started-with-calling.md) bölümüne bakın.

### <a name="troubleshooting-guide-for-push-notifications"></a>Anında iletme bildirimleri için sorun giderme kılavuzu

Cihazınızda anında iletme bildirimleri görmüyorsanız, bildirimlerin bırakılmış olduğu üç yer vardır:

- Azure Notification Hubs, Azure Iletişim hizmetlerinden gelen bildirimi kabul etmedi
- Platform Bildirim Sistemi (örneğin APNs ve FCM) Azure Notification Hubs bildirimini kabul etmedi
- Platform Bildirim Sistemi cihaza bildirim teslim etmedi.

Bir bildirimin bırakılbileceği ilk yer (Azure Notification Hubs Azure Iletişim hizmetlerinden gelen bildirimleri kabul etmedi) aşağıda ele alınmıştır. Diğer iki konum için bkz. [Azure Notification Hubs 'da bırakılan bildirimleri tanılayın](../../notification-hubs/notification-hubs-push-notification-fixer.md).

Iletişim Hizmetleri kaynağınızın Azure Notification Hubs 'a bildirim gönderip göndermediğini görmenin bir yolu, `incoming messages` bağlantılı [Azure Notification Hub ölçümlerinden](../../azure-monitor/essentials/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs)alınan ölçüme bakar.

Aşağıda, Azure Notification Hub 'ının Iletişim Hizmetleri kaynağınızın bildirimlerini kabul etmemesine neden olabilecek bazı yaygın yapılandırma hataları yer alabilir.

#### <a name="azure-notification-hub-not-linked-to-the-communication-services-resource"></a>Azure Notification Hub, Iletişim Hizmetleri kaynağına bağlanmadı

Azure Notification Hub 'ınızı Iletişim Hizmetleri kaynağınız ile bağlantıdığınız durum olabilir. [Bildirim Hub 'ı sağlama bölümüne](#notification-hub-provisioning) bakarak bunları nasıl bağlayabileceğinizi görebilirsiniz.

#### <a name="the-linked-azure-notification-hub-isnt-configured"></a>Bağlı Azure Bildirim Hub 'ı yapılandırılmadı

Bağlantılı Bildirim Hub 'ını kullanmak istediğiniz platformun (örneğin iOS veya Android) Platform Bildirim Sistemi kimlik bilgileri ile yapılandırmanız gerekir. Bunun nasıl yapılacağı hakkında daha fazla bilgi için [Bildirim Hub 'ında anında iletme bildirimleri ayarlama](../../notification-hubs/configure-notification-hub-portal-pns-settings.md)bölümüne göz atabilirsiniz.

#### <a name="the-linked-azure-notification-hub-doesnt-exist"></a>Bağlı Azure Bildirim Hub 'ı yok

Iletişim Hizmetleri kaynağına bağlı Azure Bildirim Hub 'ı artık yok. Bağlantılı Bildirim Hub 'ının hala mevcut olup olmadığını denetleyin.

#### <a name="the-azure-notification-hub-apns-platform-is-configured-with-certificate-authentication-mode"></a>Azure Notification Hub APNs platformu, sertifika kimlik doğrulama moduyla yapılandırıldı

APNs platformunu sertifika kimlik doğrulama moduyla kullanmak istiyorsanız, şu anda desteklenmemektedir. [Bir Bildirim Hub 'ında anında iletme bildirimleri ayarlama](../../notification-hubs/configure-notification-hub-portal-pns-settings.md)bölümünde belirtildiği gibi, APNs platformunu belirteç kimlik doğrulama modu ile yapılandırmanız gerekir.

#### <a name="the-linked-connection-string-doesnt-have-send-permission"></a>Bağlantılı bağlantı dizesinde `Send` izin yok

Bildirim Hub 'ınızı Iletişim Hizmetleri kaynağına bağlamak için kullandığınız bağlantı dizesinin izni olması gerekir `Send` . Yeni bir bağlantı dizesi oluşturma veya Azure Notification Hub 'ınızdaki geçerli bağlantı dizesini görme hakkında daha fazla ayrıntı için [Notification Hubs güvenlik ve erişim ilkelerine](../../notification-hubs/notification-hubs-push-notification-security.md) göz atabilirsiniz

#### <a name="the-linked-connection-string-or-azure-notification-hub-resourceid-arent-valid"></a>Bağlantılı bağlantı dizesi veya Azure Notification Hub RESOURCEID geçerli değil

Iletişim Hizmetleri kaynağını doğru bağlantı dizesi ve Azure Notification Hub RESOURCEID ile yapılandırdığınızdan emin olun

#### <a name="the-linked-connection-string-is-regenerated"></a>Bağlantılı bağlantı dizesi yeniden üretildi

Bağlı Azure Notification Hub 'ınızın bağlantı dizesini yeniden oluşturmanız durumunda, [Bildirim Hub 'ını yeniden bağlayarak](#notification-hub-provisioning)bağlantı dizesini iletişim hizmetleri kaynağınızın yeni bir ile güncelleştirmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

* Azure Event Grid giriş için bkz. [Event Grid nedir?](../../event-grid/overview.md)
* Azure Notification Hub 'ı kavramları hakkında daha fazla bilgi edinmek için bkz. [azure Notification Hubs belgeleri](../../notification-hubs/index.yml)
