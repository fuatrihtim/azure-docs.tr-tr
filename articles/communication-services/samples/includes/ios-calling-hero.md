---
title: include dosyası
description: include dosyası
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: f1d49aa0583f34327afff229b46d7a366202aded
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105152826"
---
[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

**İOS Için Hero örneğini çağıran** Azure Iletişim Hizmetleri grubu, IOS SDK 'Sını çağıran iletişim hizmetlerinin, ses ve video içeren bir grup çağırma deneyimi oluşturmak için nasıl kullanılabileceğini gösterir. Bu örnek hızlı başlangıçta, örneği ayarlamayı ve çalıştırmayı öğreneceksiniz. Bağlam için örneğe genel bir bakış sağlanır.

## <a name="download-code"></a>Kodu indirin

Bu hızlı başlangıç için [GitHub](https://github.com/Azure-Samples/communication-services-ios-calling-hero)'da son kodu bulun.

## <a name="overview"></a>Genel Bakış

Örnek, hem ses hem de video çağrılmasını sağlayan bir arama deneyimi oluşturmak için Azure Iletişim Hizmetleri iOS SDK 'larını kullanan yerel bir iOS uygulamasıdır. Uygulama, daha sonra Azure Iletişim Hizmetleri SDK 'sını başlatmak için kullanılan erişim belirteçlerini sağlamak için bir sunucu tarafı bileşeni kullanır. Bu sunucu tarafı bileşeni yapılandırmak için, [Azure işlevleri öğreticisiyle güvenilir hizmeti](../../tutorials/trusted-service-tutorial.md) takip edin.

Örnek şöyle görünür:

:::image type="content" source="../media/calling/landing-page-ios.png" alt-text="Örnek uygulamanın giriş sayfasını gösteren ekran görüntüsü.":::

"Yeni çağrıya başla" düğmesine bastığınızda iOS uygulaması yeni bir çağrı oluşturur ve bu dosyayı birleştirir. Uygulama, var olan çağrının KIMLIĞINI belirterek mevcut bir Azure Iletişim Hizmetleri çağrısına katılırsanız izin verir. Ayrıca, toplantının davetine de bulunan JOIN bağlantısını sağlayarak bir takımlar Toplantısı ' na de katılabilir.  (JOIN bağlantısı şu biçimdedir: `https://teams.microsoft.com/l/meetup-join/` ). Takımlar birlikte çalışma hakkında daha fazla bilgi için [takımlar birlikte çalışma kavramsal belgelerini](../../concepts/teams-interop.md)ziyaret edin.

Bir çağrıya katıldıktan sonra, bilgisayarınıza kameranıza ve mikrofona erişim izni vermeniz istenir. Ayrıca, bir görünen ad girmeniz istenir.

:::image type="content" source="../media/calling/pre-call-ios.png" alt-text="Örnek uygulamanın çağrı öncesi ekranını gösteren ekran görüntüsü.":::

Görüntü adınızı ve cihazlarınızı yapılandırdıktan sonra, çağrıya katabilirsiniz. Çekirdek çağırma deneyiminin yaşadığı ana çağrı tuvalini görürsünüz.

:::image type="content" source="../media/calling/main-app-ios.png" alt-text="Örnek uygulamanın ana ekranını gösteren ekran görüntüsü.":::

Ana arama ekranının bileşenleri:

- **Medya Galerisi**: katılımcıların gösterildiği ana aşama. Bir katılımcının Kamerası etkinse, video akışları burada gösterilir. Her katılımcının, görünen adını ve video akışını (bir tane olduğunda) gösteren bir tek kutucuğu vardır. Galeri birden çok katılımcıyı destekler ve katılımcılar eklendiğinde veya çağrıya kaldırıldığında güncelleştirilir.
- **Eylem çubuğu**: Bu, birincil çağrı denetimlerinin bulunduğu yerdir. Bu denetimler, videonuzu ve mikrofonunuzu açmanıza/kapatmanızı, ekranınızı paylaşmanıza ve çağrıdan ayrılmasına olanak tanır.

Aşağıda, önkoşulları ve örneği ayarlama adımlarını hakkında daha fazla bilgi bulacaksınız.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. Ayrıntılar için bkz. [ücretsiz hesap oluşturma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Anahtarınıza yüklenmiş geçerli bir geliştirici sertifikasıyla birlikte [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532)çalıştıran bir Mac.
- Bir Azure Iletişim Hizmetleri kaynağı. Ayrıntılar için bkz. [Azure Iletişim kaynağı oluşturma](../../quickstarts/create-communication-resource.md).
- Erişim belirteçleri getirmek için [kimlik doğrulama uç noktası](../../tutorials/trusted-service-tutorial.md) çalıştıran bir Azure işlevi.

## <a name="running-sample-locally"></a>Örneği yerel olarak çalıştırma

Grup çağırma örneği, XCode kullanılarak yerel olarak çalıştırılabilir. Geliştiriciler, uygulamayı test etmek için fiziksel cihazlarını ya da öykünücüyü kullanabilirler.

### <a name="before-running-the-sample-for-the-first-time"></a>Örneği ilk kez çalıştırmadan önce

1. Çalıştırarak bağımlılıkları yükler `pod install` .
2. `AzureCalling.xcworkspace`XCode 'da açın.
3. Güncelleştirin `AppSettings.plist` . Anahtar için değeri `communicationTokenFetchUrl` kimlik doğrulama uç noktanızın URL 'si olacak şekilde ayarlayın.

### <a name="run-sample"></a>Örnek Çalıştır

Örneği XCode 'da derleyin ve çalıştırın.

## <a name="optional-securing-an-authentication-endpoint"></a>Seçim Kimlik doğrulama uç noktasının güvenliğini sağlama

Tanıtım amacıyla bu örnek, varsayılan olarak bir Azure Iletişim Hizmetleri erişim belirteci getirmek için genel olarak erişilebilen bir uç nokta kullanır. Üretim senaryolarında kendi belirteçlerinizi sağlamak için kendi güvenli uç noktanızı kullanmanızı öneririz.

Ek yapılandırma ile bu örnek, uygulamanın bir Azure Iletişim Hizmetleri erişim belirteci getirmesi için Kullanıcı oturum açması gereken **Azure Active Directory** (Azure AD) korumalı bir uç noktaya bağlanmayı destekler. Aşağıdaki adımlara bakın:

1. Uygulamanızda Azure Active Directory kimlik doğrulamasını etkinleştirin.  
   - [Uygulamanızı Azure Active Directory altına kaydetme (iOS/macOS platform ayarlarını kullanarak)](../../../active-directory/develop/tutorial-v2-ios.md) 
    - [App Service veya Azure Işlevleri uygulamanızı Azure AD oturum açma bilgilerini kullanacak şekilde yapılandırma](../../../app-service/configure-authentication-provider-aad.md)
2. Azure Active Directory uygulama kayıtları altında kayıtlı uygulamanıza genel bakış sayfasına gidin. , `Application (client) ID` ,, `Directory (tenant) ID``Application ID URI`

:::image type="content" source="../media/calling/aad-overview.png" alt-text="Azure portal Yapılandırma Azure Active Directory.":::

3. `AppSettings.plist`Xcode 'da açın, aşağıdaki anahtar değerlerini ekleyin:
   - `communicationTokenFetchUrl`: Azure Iletişim Hizmetleri belirteci istemek için URL 
   - `isAADAuthEnabled`: Azure Iletişim Hizmetleri belirtecinin kimlik doğrulamasının gerekli olup olmadığını belirten bir Boole değeri
   - `aadClientId`: Uygulamanızın (istemci) KIMLIĞI
   - `aadTenantId`: Dizininiz (kiracı) KIMLIĞINIZ
   - `aadRedirectURI`: Yeniden yönlendirme URI 'SI şu biçimde olmalıdır: `msauth.<app_bundle_id>://auth`
   - `aadScopes`: Kullanıcılardan yetkilendirme için istenen izin kapsamları dizisi. `<Application ID URI>/user_impersonation`Kimlik doğrulama uç noktasına erişim vermek için diziye ekleyin

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir Iletişim Hizmetleri aboneliğini temizleyip kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler. [Kaynakları Temizleme](../../quickstarts/create-communication-resource.md#clean-up-resources)hakkında daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

>[!div class="nextstepaction"]
>[GitHub 'dan örneği indirin](https://github.com/Azure-Samples/communication-services-ios-calling-hero)

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

- [Çağıran SDK 'yı kullanma](../../quickstarts/voice-video-calling/calling-client-samples.md) hakkında bilgi edinin
- [Nasıl çalıştığını çağırma](../../concepts/voice-video-calling/about-call-types.md) hakkında daha fazla bilgi edinin

### <a name="additional-reading"></a>Ek okuma

- [Azure Iletişim GitHub](https://github.com/Azure/communication) -resmi GitHub sayfasında daha fazla örnek ve bilgi bulun
- [Örnekler](./../overview.md) -örneklere genel bakış sayfamızda daha fazla örnek ve örnek bulun.
- [Azure Iletişim çağırma özellikleri](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features) -çağıran iOS SDK 'sı hakkında daha fazla bilgi edinmek Için[Azure COMMUNICATION iOS çağıran SDK](https://github.com/Azure/Communication/releases/)
