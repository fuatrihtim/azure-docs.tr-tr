---
title: ADAL to MSAL geçiş kılavuzu (MSAL iOS/macOS) | Mavisi
titleSuffix: Microsoft identity platform
description: İOS/macOS için MSAL ve ObjectiveC için Azure AD kimlik doğrulama kitaplığı (ADAL) arasındaki farkları öğrenin. ObjC) ve iOS/macOS için MSAL 'e geçiş yapma.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 7dc3241198fbc6eeddba059251f28c6dc35c8a29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98754943"
---
# <a name="migrate-applications-to-msal-for-ios-and-macos"></a>İOS ve macOS için uygulamaları MSAL 'e geçirme

Azure Active Directory kimlik doğrulama kitaplığı ([adal hedefi-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc)), v 1.0 uç noktası aracılığıyla Azure Active Directory hesaplarıyla çalışacak şekilde oluşturulmuştur.

İOS ve macOS için Microsoft kimlik doğrulama kitaplığı (MSAL), Microsoft Identity platformu (resmi olarak Azure AD v 2.0 uç noktası) aracılığıyla Azure Active Directory (Azure AD) hesapları, kişisel Microsoft hesapları ve Azure AD B2C hesapları gibi tüm Microsoft kimlikleriyle birlikte çalışmak üzere oluşturulmuştur.

Microsoft Identity platformu Azure Active Directory v 1.0 ile birkaç önemli farka sahiptir. Bu makalede bu farklılıklar vurgulanmıştır ve bir uygulamayı ADAL 'dan MSAL 'e geçirmeye yönelik rehberlik sunulmaktadır.

## <a name="adal-and-msal-app-capability-differences"></a>ADAL ve MSAL App yetenek farkları

### <a name="who-can-sign-in"></a>Kimler oturum açabilir

* ADAL yalnızca Azure AD hesapları olarak da bilinen iş ve okul hesaplarını destekler.
* MSAL, Hotmail.com, Outlook.com ve Live.com gibi kişisel Microsoft hesaplarını (MSA hesapları) destekler.
* MSAL, iş ve okul hesaplarını ve Azure AD B2C hesaplarını destekler.

### <a name="standards-compliance"></a>Standartlar uyumluluğu

* Microsoft Identity platformu, OAuth 2,0 ve OpenID Connect standartlarını izler.

### <a name="incremental-and-dynamic-consent"></a>Artımlı ve dinamik onay

* Azure Active Directory v 1.0 uç noktası, uygulama kaydı sırasında tüm izinlerin önceden bildirilmesini gerektirir. Bu, izinlerin statik olduğu anlamına gelir.
* Microsoft Identity platformu izinleri dinamik olarak isteyebilmesini sağlar. Uygulamalar, izinleri yalnızca gerekli olduğu gibi isteyebilir ve uygulama onlara ihtiyaç duyarken daha fazla istekte bulunabilir.

Azure Active Directory v 1.0 ve Microsoft Identity platformu arasındaki farklılıklar hakkında daha fazla bilgi için, bkz. [neden Microsoft Identity platform güncelleştirmesi?](../azuread-dev/azure-ad-endpoint-comparison.md).

## <a name="adal-and-msal-library-differences"></a>ADAL ve MSAL kitaplığı farklılıkları

MSAL public API 'SI, Azure AD v 1.0 ile Microsoft Identity platformu arasındaki birkaç önemli farkı yansıtır.

### <a name="msalpublicclientapplication-instead-of-adauthenticationcontext"></a>ADAuthenticationContext yerine MSALPublicClientApplication

`ADAuthenticationContext` , ADAL uygulamasının oluşturduğu ilk nesnedir. ADAL örneğini temsil eder. Uygulamalar `ADAuthenticationContext` her bir Azure Active Directory bulut ve kiracı (yetkili) birleşimi için yeni bir örneğini oluşturur. Aynı, `ADAuthenticationContext` birden çok ortak istemci uygulaması için belirteçleri almak üzere kullanılabilir.

MSAL ' de, ana etkileşim, `MSALPublicClientApplication` [OAuth 2,0 genel istemcisinden](https://tools.ietf.org/html/rfc6749#section-2.1)sonra Modellenen bir nesne üzerinden yapılır. Bir örneği, `MSALPublicClientApplication` her bir yetkili için yeni bir örnek oluşturmaya gerek kalmadan birden çok AAD bulutları ve kiracılar ile etkileşim kurmak için kullanılabilir. Çoğu uygulama için, bir `MSALPublicClientApplication` örnek yeterlidir.

### <a name="scopes-instead-of-resources"></a>Kaynaklar yerine kapsamlar

ADAL 'da, bir uygulamanın  `https://graph.microsoft.com` Azure Active Directory v 1.0 uç noktasından belirteçleri almak için bir kaynak tanımlayıcısı sağlaması gerekiyordu. Bir kaynak, bir dizi kapsam veya uygulama bildiriminde oAuth2Permissions, anladığı şekilde tanımlayabilir. Bu izin verilen istemci uygulamaları, uygulama kaydı sırasında önceden tanımlanmış belirli bir kapsam kümesi için bu kaynaktan belirteç ister.

MSAL ' de, tek bir kaynak tanımlayıcısı yerine, uygulamalar istek başına bir kapsam kümesi sağlar. Kapsam, kaynak tanımlayıcısı ve ardından kaynak/izin biçiminde bir izin adı gelir. Örneğin, `https://graph.microsoft.com/user.read`

MSAL içinde kapsam sağlamanın iki yolu vardır:

* Uygulamalarınızın ihtiyaç duyacağı tüm izinlerin bir listesini sağlayın. Örnek: 

    `@[@"https://graph.microsoft.com/directory.read", @"https://graph.microsoft.com/directory.write"]`

    Bu durumda, uygulama `directory.read` ve `directory.write` izinlerini ister. Bu uygulama için önceden kabul etmedikleri takdirde, kullanıcıdan bu izinleri onaylaması istenir. Uygulama, kullanıcının uygulama için zaten onay aldığı ek izinler de alabilir. Kullanıcılardan yalnızca yeni izinleri veya verilmemiş izinleri onaylaması istenir.

* `/.default`Kapsam.

Bu, her uygulama için yerleşik kapsamdır. Uygulama kaydedildiğinde yapılandırılan izinlerin statik listesine başvurur. Davranışı ile benzerdir `resource` . Bu, geçiş sırasında, benzer bir kapsam ve Kullanıcı deneyimi kümesinin korunmasını sağlamak için yararlı olabilir.

Kapsamı kullanmak için `/.default` `/.default` kaynak tanımlayıcısına ekleyin. Örneğin: `https://graph.microsoft.com/.default`. Kaynağınız eğik çizgi () ile sonlanıyorsa, `/` `/.default` baştaki eğik çizgi da dahil olmak üzere yine de, içinde çift eğik çizgi () içeren bir kapsam elde etmeniz gerekir `//` .

"/PST varsayılan" kapsamını kullanma hakkında daha fazla bilgiyi [burada](./v2-permissions-and-consent.md#the-default-scope) bulabilirsiniz

### <a name="supporting-different-webview-types--browsers"></a>Farklı WebView türlerini destekleme & tarayıcılar

ADAL yalnızca iOS için UIWebView/WKWebView ve macOS için WebView destekler. İOS için MSAL, bir yetkilendirme kodu isteğinde bulunduğunda ve artık desteklerken Web içeriğini görüntülemeye yönelik daha fazla seçeneği destekler `UIWebView` ; Bu da Kullanıcı deneyimini ve güvenliğini iyileştirebilir.

Varsayılan olarak, iOS üzerinde MSAL, iOS 12 + cihazlarında kimlik doğrulaması için önerilen Web bileşeni olan [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)kullanır. Uygulamalar ve Safari tarayıcısı arasında tanımlama bilgisi paylaşımı aracılığıyla tek Sign-On (SSO) avantajları sağlar.

Uygulama gereksinimlerine ve istediğiniz son kullanıcı deneyimine bağlı olarak farklı bir Web bileşeni kullanmayı tercih edebilirsiniz. Daha fazla seçenek için [desteklenen Web görünümü türleri](customize-webviews.md) bölümüne bakın.

ADAL 'dan MSAL 'e geçiş yaparken, `WKWebView` iOS ve macOS 'TA adal 'a benzer kullanıcı deneyimini sağlar. `ASWebAuthenticationSession`Mümkünse, iOS 'ta uygulamasına geçiş yapmanız önerilir. MacOS için, ' yi kullanmanızı öneririz `WKWebView` .

### <a name="account-management-api-differences"></a>Hesap yönetimi API 'SI farkları

ADAL yöntemlerini `acquireToken()` veya ' i çağırdığınızda, `acquireTokenSilent()` `ADUserInformation` `id_token` kimliği doğrulanmış hesabı temsil eden öğesinden gelen taleplerin listesini içeren bir nesnesi alırsınız. Ayrıca, `ADUserInformation` `userId` talebe bağlı olarak bir döndürür `upn` . İlk etkileşimli belirteç alma işleminden sonra ADAL, geliştiricinin `userId` tüm sessiz çağrılar için sağlamasını bekler.

ADAL bilinen Kullanıcı kimliklerini almak için bir API sağlamıyor. Bu hesapları kaydetmek ve yönetmek için uygulamayı kullanır.

MSAL, belirteç almak zorunda kalmadan MSAL tarafından bilinen tüm hesapları listelemek için bir API kümesi sağlar.

ADAL gibi MSAL, öğesinden talepler listesini tutan hesap bilgilerini döndürür `id_token` . Nesnenin içindeki nesnesinin bir parçasıdır `MSALAccount` `MSALResult` .

MSAL, hesapları kaldırmak için bir dizi API sağlar ve kaldırılan hesapları uygulama için erişilemez hale getirir. Hesap kaldırıldıktan sonra, daha sonra belirteç alma çağrıları kullanıcıdan etkileşimli belirteç alımı yapması için istemde bulunur. Hesap kaldırma yalnızca onu başlatan istemci uygulaması için geçerlidir ve hesabı cihazda veya sistem tarayıcısından çalışan diğer uygulamalardan kaldırmaz. Bu, kullanıcının, tek bir uygulamanın oturumunu kapattıktan sonra bile, cihazda SSO deneyimine sahip olmaya devam etmesini sağlar.

Ayrıca, MSAL daha sonra sessizce bir belirteç istemek için kullanılabilecek bir hesap tanımlayıcısı da döndürür. Ancak, hesap tanımlayıcısı ( `identifier` nesne içindeki özelliği aracılığıyla erişilebilir `MSALAccount` ) görüntülenebilir değildir ve hangi biçim içinde olduğunu ya da yorumlamaya veya ayrıştırmaya çalışırsınız.

### <a name="migrating-the-account-cache"></a>Hesap önbelleğini geçirme

ADAL 'dan geçiş yaparken, uygulamalar normalde `userId` msal tarafından gerekli olmayan Adal ' ı depolar `identifier` . Bir kerelik geçiş adımı olarak bir uygulama, aşağıdaki API ile ADAL Kullanıcı kimliğini kullanarak bir MSAL hesabını sorgulayabilir:

`- (nullable MSALAccount *)accountForUsername:(nonnull NSString *)username error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Bu API, ADAL Kullanıcı kimliği (UPN) tarafından hesabı bulmak için hem MSAL hem de ADAL önbelleğini okur.

Hesap bulunursa, geliştirici sessiz belirteç alımı yapmak için hesabı kullanmalıdır. İlk sessiz belirteç alma, hesabı etkin bir şekilde yükseltir ve geliştirici MSAL Result () içinde MSAL uyumlu bir hesap tanımlayıcısı alır `identifier` . Bundan sonra, yalnızca `identifier` AŞAĞıDAKI API kullanılarak hesap aramaları için kullanılmalıdır:

`- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

MSAL ' deki tüm işlemler için ADAL ' i kullanmaya devam etmek mümkün olsa da, `userId` `userId` UPN 'yi temel alan, kötü bir kullanıcı deneyimine neden olan birden çok sınırlamalara tabidir. Örneğin, UPN değişirse kullanıcının yeniden oturum açması gerekir. Tüm uygulamaların tüm işlemler için görüntülenebilen olmayan hesabı kullanmasını öneririz `identifier` .

[Önbellek durumu geçişi](sso-between-adal-msal-apps-macos-ios.md)hakkında daha fazla bilgi edinin.

### <a name="token-acquisition-changes"></a>Belirteç alma değişiklikleri

MSAL, bazı belirteç alma çağrısı değişikliklerini tanıtır:

* ADAL gibi `acquireTokenSilent` her zaman sessiz bir istekle sonuçlanır.
* ADAL 'un aksine, `acquireToken` her zaman kullanıcının Web görünümü veya Microsoft Authenticator uygulaması aracılığıyla işlem yapılabilir Kullanıcı arabirimine neden olur. WebView/Microsoft Authenticator içindeki SSO durumuna bağlı olarak, kullanıcıdan kimlik bilgilerini girmesi istenebilir.
* ADAL içinde, `acquireToken` `AD_PROMPT_AUTO` ilk olarak sessiz belirteç alımı dener ve yalnızca sessiz istek başarısız olursa Kullanıcı arabirimini gösterir. MSAL ' de, bu mantık ilk kez çağırarak `acquireTokenSilent` ve yalnızca `acquireToken` sessiz Alım başarısız olursa elde edilebilir. Bu, geliştiricilerin etkileşimli belirteç alımı başlatmadan önce Kullanıcı deneyimini özelleştirmesini sağlar.

### <a name="error-handling-differences"></a>Hata işleme farklılıkları

MSAL, uygulamanız tarafından işlenebilen ve Kullanıcı tarafından müdahale gerektiren hatalar arasında daha fazla açıklık sağlar. Geliştiricilerin işlemesi gereken sınırlı sayıda hata var:

* `MSALErrorInteractionRequired`: Kullanıcının etkileşimli bir istek yapması gerekir. Bu, süresi geçen bir kimlik doğrulama oturumu gibi çeşitli nedenlerden kaynaklanabilir, koşullu erişim ilkesi değiştirilmiştir, yenileme belirtecinin süresi dolduğunda veya iptal edildiğinde önbellekte geçerli bir belirteç yoktur ve bu şekilde devam eder.
* `MSALErrorServerDeclinedScopes`: İstek tam olarak tamamlanmadı ve bazı kapsamlara erişim verilmedi. Bu, bir veya daha fazla kapsam için bir kullanıcının izin reddetmesinin nedeni olabilir.

[ `MSALError` Listedeki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128) diğer tüm hataların yönetilmesi isteğe bağlıdır. Kullanıcı deneyimini geliştirmek için bu hataların bilgilerini kullanabilirsiniz.

MSAL hata işleme hakkında daha fazla bilgi için bkz. [msal kullanarak özel durumları ve hataları işleme](msal-error-handling-ios.md) .

### <a name="broker-support"></a>Aracı desteği

Sürüm 0.3.0 ile başlayarak, Microsoft Authenticator uygulamasını kullanarak aracılı kimlik doğrulaması için destek sağlar. Microsoft Authenticator Ayrıca koşullu erişim senaryoları için desteğe izin verebilir. Koşullu erişim senaryolarına örnek olarak, kullanıcının cihazı Intune 'dan kaydetmesini veya bir belirteç almak için AAD 'ye kaydolmasını gerektiren cihaz uyumluluk ilkeleri bulunur. Ve mobil uygulama yönetimi (MAM) koşullu erişim ilkeleri, uygulamanız belirteç almadan önce uyumluluk kanıtı gerektirir.

Uygulamanız için aracıyı etkinleştirmek üzere:

1. Uygulama için bir aracı uyumlu yeniden yönlendirme URI 'SI biçimi kaydedin. Aracı uyumlu yeniden yönlendirme URI 'SI biçimi `msauth.<app.bundle.id>://auth` . `<app.bundle.id>`Uygulamanızın paket kimliğiyle değiştirin. ADAL 'dan geçiş yapıyorsanız ve uygulamanız zaten Broker özellikli ise, yapmanız gereken ek bir şey yoktur. Önceki yeniden yönlendirme URI 'SI MSAL ile tamamen uyumludur, bu sayede adım 3 ' e atlayabilirsiniz.

2. Uygulamanızın yeniden yönlendirme URI düzenini Info. plist dosyanıza ekleyin. Varsayılan MSAL yeniden yönlendirme URI 'SI için biçim olur `msauth.<app.bundle.id>` . Örnek:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

3. Uygulamanızın Info. plist komutuna Lsapplicationqueriesdüzenlerinin altında aşağıdaki şemaları ekleyin:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

4. Geri çağırmaları işlemek için AppDelegate. m dosyanıza aşağıdakini ekleyin: hedef-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    SWIFT
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```

### <a name="business-to-business-b2b"></a>İşletmeden işletmeye (B2B)

ADAL 'da, `ADAuthenticationContext` uygulamanın belirteçleri istediği her kiracı için ayrı örnekler oluşturursunuz. Bu artık MSAL içinde bir gereklilik değildir. MSAL ' de, tek bir örneği oluşturabilir `MSALPublicClientApplication` ve acquireToken ve acquireTokenSilent çağrıları için farklı bir yetkili belirterek bunu herhangi BIR AAD bulutu ve kuruluş için kullanabilirsiniz.

## <a name="sso-in-partnership-with-other-sdks"></a>Diğer SDK 'lar ile iş ortaklığında SSO

İOS için MSAL, aşağıdaki SDK 'lara sahip birleştirilmiş bir önbellek aracılığıyla SSO elde edebilir:

- ADAL hedefi-C 2.7. x +
- Xamarin 2,4. x + için MSAL.NET
- Xamarin 4.4. x + için ADAL.NET

SSO, iOS Anahtarlık paylaşımı aracılığıyla sağlanır ve yalnızca aynı Apple geliştirici hesabından yayımlanan uygulamalar arasında kullanılabilir.

İOS anahtar zinciri paylaşımı aracılığıyla SSO yalnızca sessiz SSO türüdür.

MacOS 'ta MSAL, iOS ve macOS tabanlı uygulamalar ve ADAL hedefi-C tabanlı uygulamalar için diğer MSAL ile SSO elde edebilir.

İOS üzerinde MSAL Ayrıca iki tür SSO türünü de destekler:

* Web tarayıcısı aracılığıyla SSO. İOS için MSAL destekler `ASWebAuthenticationSession` , bu, cihazdaki diğer uygulamalar arasında paylaşılan tanımlama bilgileri aracılığıyla SSO ve özellikle Safari tarayıcısı sağlar.
* Kimlik doğrulama Aracısı aracılığıyla SSO. Bir iOS cihazında Microsoft Authenticator, kimlik doğrulama Aracısı olarak davranır. Uyumlu bir cihaz gerektirme gibi koşullu erişim ilkelerini izleyebilir ve kayıtlı cihazlar için SSO sağlar. Sürüm 0.3.0 ile başlayan MSAL SDK 'Ları varsayılan olarak bir aracıyı destekler.

## <a name="intune-mam-sdk"></a>Intune MAM SDK 'Sı

[INTUNE mam SDK](/intune/app-sdk-get-started) , sürüm [11.1.2](https://github.com/msintuneappsdk/ms-intune-app-sdk-ios/releases/tag/11.1.2) Ile başlayarak iOS için msal 'i destekler

## <a name="msal-and-adal-in-the-same-app"></a>Aynı uygulamada MSAL ve ADAL

ADAL sürüm 2.7.0 ve üzeri, aynı uygulamada MSAL ile birlikte bulunamaz. Ana neden, paylaşılan alt modül ortak kodunun nedenidir. Amaç-C ad alanlarını desteklemediğinden, uygulamanıza hem ADAL hem de MSAL çerçeveleri eklerseniz, aynı sınıfın iki örneği olacaktır. Çalışma zamanında ne zaman çekilmek için bir garanti yoktur. Her iki SDK aynı sürümle aynı sürümü kullanıyorsa, uygulamanız çalışmaya devam edebilir. Ancak, bu farklı bir sürümse, uygulamanız tanılanması zor olan beklenmedik kilitlenmelere neden olabilir.

Aynı üretim uygulamasında ADAL ve MSAL çalıştırmak desteklenmez. Ancak, kullanıcılarınızı iOS ve macOS için yalnızca ADAL hedefi-C ' den MSAL ' ye geçiriyorsanız, [adal hedefi-c 2.6.10](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases/tag/2.6.10)kullanmaya devam edebilirsiniz. Aynı uygulamada MSAL ile birlikte çalışarak tek sürümdür. Bu ADAL sürümü için yeni bir özellik güncelleştirmesi olmayacak, bu nedenle yalnızca geçiş ve test amacıyla kullanılmalıdır. Uygulamanızın ADAL ve MSAL çok kullanımı uzun süreli olması gerekmez.

Aynı uygulamadaki ADAL ve MSAL birlikte bulunma desteklenmez.
Birden çok uygulama arasındaki ADAL ve MSAL bir arada kullanımı tam olarak desteklenmektedir.

## <a name="practical-migration-steps"></a>Pratik geçiş adımları

### <a name="app-registration-migration"></a>Uygulama kaydı geçişi

MSAL 'e geçmek ve AAD hesaplarını etkinleştirmek için mevcut AAD uygulamanızı değiştirmeniz gerekmez. Ancak, ADAL tabanlı uygulamanız aracılı kimlik doğrulamasını desteklemiyorsa, MSAL 'e geçiş yapabilmeniz için önce uygulama için yeni bir yeniden yönlendirme URI 'SI kaydetmeniz gerekir.

Yeniden yönlendirme URI 'SI şu biçimde olmalıdır: `msauth.<app.bundle.id>://auth` . `<app.bundle.id>`Uygulamanızın paket kimliğiyle değiştirin. [Azure Portal](https://aka.ms/MobileAppReg)YENIDEN yönlendirme URI 'sini belirtin.

Yalnızca iOS için, sertifika tabanlı kimlik doğrulamasını desteklemek üzere, ek bir yeniden yönlendirme URI 'sinin uygulamanıza kaydedilmesi ve Azure portal aşağıdaki biçimde olması gerekir: `msauth://code/<broker-redirect-uri-in-url-encoded-form>` . Örneğin, `msauth://code/msauth.com.microsoft.mybundleId%3A%2F%2Fauth`

Tüm uygulamaların her iki yeniden yönlendirme URI 'si kaydetmesini öneririz.

Artımlı izin desteği eklemek istiyorsanız, uygulamanızın **API izinleri** sekmesi altında uygulama kaydıza erişim isteyecek şekilde yapılandırıldığı API 'leri ve izinleri seçin.

ADAL 'dan geçiş yapıyorsanız ve hem AAD hem de MSA hesaplarını desteklemek istiyorsanız, mevcut uygulama kaydınızdan her ikisini desteklemek için güncelleştirilmeleri gerekir. Mevcut üretim uygulamanızı hem AAD hem de MSA 'yi hemen destekleyecek şekilde güncelleştirmenizi önermiyoruz. Bunun yerine, test için hem AAD hem de MSA destekleyen başka bir istemci KIMLIĞI oluşturun ve tüm senaryoların çalıştığını doğruladıktan sonra mevcut uygulamayı güncelleştirin.

### <a name="add-msal-to-your-app"></a>Uygulamanıza MSAL ekleme

Tercih ettiğiniz paket yönetim aracını kullanarak uygulamanıza MSAL SDK ekleyebilirsiniz. [Ayrıntılı yönergelere buradan](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Installation)bakın.

### <a name="update-your-apps-infoplist-file"></a>Uygulamanızın Info. plist dosyasını güncelleştirin

Yalnızca iOS için, uygulamanızın yeniden yönlendirme URI düzenini Info. plist dosyanıza ekleyin. ADAL broker ile uyumlu uygulamalar için zaten olmalıdır. Varsayılan MSAL yeniden yönlendirme URI şeması şu biçimde olacaktır: `msauth.<app.bundle.id>` .  

```xml
<key>CFBundleURLSchemes</key>
<array>
    <string>msauth.<app.bundle.id></string>
</array>
```

Aşağıdaki düzenleri uygulamanızın Info. plist öğesine ekleyin `LSApplicationQueriesSchemes` .

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```

### <a name="update-your-appdelegate-code"></a>AppDelegate kodunuzu güncelleştirme

Yalnızca iOS için AppDelegate. d dosyanıza aşağıdakileri ekleyin:

Amaç-C:

```objc
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
{
    return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

SWIFT

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```

**Xcode 11** kullanıyorsanız, bunun yerine msal geri çağırma işlemini dosyaya yerleştirmeniz gerekir `SceneDelegate` .
Daha eski iOS ile uyumluluk için hem UISceneDelegate hem de Uıapplicationdelegate 'i destekediyorsanız, MSAL geri çağrısının her iki dosyaya da yerleştirilmesi gerekir.

Amaç-C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

SWIFT

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

Bu, MSAL 'in aracı ve Web bileşeninden gelen yanıtları işlemesini sağlar.
"Swizzled" uygulama temsilcisi yöntemleri otomatik olarak bu yana ADAL içinde gerekli değildi. El ile ekleme daha az hataya açıktır ve uygulamaya daha fazla denetim sağlar.

### <a name="enable-token-caching"></a>Belirteç önbelleğe almayı etkinleştir

Varsayılan olarak, MSAL, uygulamanızın belirteçlerini iOS veya macOS anahtarlığınızdan önbelleğe alır. 

Belirteç önbelleğe almayı etkinleştirmek için:
1. Uygulamanızın düzgün şekilde imzalandığından emin olun
2. Xcode proje ayarlarınıza gidin > **Yetenekler sekmesi**  >  **Anahtarlık paylaşımını etkinleştir**
3. Öğesine tıklayın **+** ve aşağıdaki **Anahtarlık grupları** girişini girin: 3. a IOS Için, `com.microsoft.adalcache` MacOS için 3. b girin `com.microsoft.identity.universalstorage`

### <a name="create-msalpublicclientapplication-and-switch-to-its-acquiretoken-and-acquiretokesilent-calls"></a>MSALPublicClientApplication oluşturma ve acquireToken ve acquireTokeSilent çağrılarına geçiş yapma

`MSALPublicClientApplication`Aşağıdaki kodu kullanarak oluşturabilirsiniz:

Amaç-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
    
MSALPublicClientApplication *application =
[[MSALPublicClientApplication alloc] initWithConfiguration:configuration
                                                     error:&error];
```

SWIFT

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
            
} catch let error as NSError {
  // handle error here
}
```

Ardından, önbellekte herhangi bir hesap olup olmadığını görmek için hesap yönetimi API 'sini çağırın:

Amaç-C:

```objc
NSString *accountIdentifier = nil /*previously saved MSAL account identifier */;
NSError *error = nil;
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:&error];
```

SWIFT

```swift
// definitions that need to be initialized
let application: MSALPublicClientApplication!
let accountIdentifier: String! /*previously saved MSAL account identifier */

do {
  let account = try application.account(forIdentifier: accountIdentifier)
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



ya da tüm hesapları okuyun:

Amaç-C:

```objc
NSError *error = nil;
NSArray<MSALAccount *> *accounts = [application allAccounts:&error];
```

SWIFT

```swift
let application: MSALPublicClientApplication!
do {
  let accounts = try application.allAccounts()
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



Bir hesap bulunursa, MSAL API 'sini çağırın `acquireTokenSilent` :

Amaç-C:

```objc
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"<your-resource-here>/.default"] account:account];
    
[application acquireTokenSilentWithParameters:silentParameters
                              completionBlock:^(MSALResult *result, NSError *error)
{
    if (result)
    {
        NSString *accessToken = result.accessToken;
        // Use your token
    }
    else
    {
        // Check the error
        if ([error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
        {
            // Interactive auth will be required
        }
            
        // Other errors may require trying again later, or reporting authentication problems to the user
    }
}];
```

SWIFT

```swift
let application: MSALPublicClientApplication!
let account: MSALAccount!
        
let silentParameters = MSALSilentTokenParameters(scopes: ["<your-resource-here>/.default"], 
                                                 account: account)
application.acquireTokenSilent(with: silentParameters) {
  (result: MSALResult?, error: Error?) in
  if let accessToken = result?.accessToken {
     // use accessToken
  }
  else {
    // Check the error
    guard let error = error else {
      assert(true, "callback should contain a valid result or error")
      return
    }
    
    let nsError = error as NSError
    if (nsError.domain == MSALErrorDomain
        && nsError.code == MSALError.interactionRequired.rawValue) {
      // Interactive auth will be required
    }
                
    // Other errors may require trying again later, or reporting authentication problems to the user
  }
}
```



## <a name="next-steps"></a>Sonraki adımlar

[Kimlik doğrulama akışları ve uygulama senaryoları](authentication-flows-app-scenarios.md) hakkında daha fazla bilgi edinin