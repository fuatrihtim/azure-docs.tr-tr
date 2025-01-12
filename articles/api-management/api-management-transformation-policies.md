---
title: Azure API Management dönüştürme ilkeleri | Microsoft Docs
description: Azure API Management 'de kullanıma sunulan dönüştürme ilkeleri hakkında bilgi edinin.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/11/2019
ms.author: apimpm
ms.openlocfilehash: c0c7a6b25c15be2e521e0985c315baf819650aa5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99491765"
---
# <a name="api-management-transformation-policies"></a>API Management dönüştürme ilkeleri
Bu konu, aşağıdaki API Management ilkelerine yönelik bir başvuru sağlar. İlke ekleme ve yapılandırma hakkında daha fazla bilgi için bkz. [API Management ilkeleri](./api-management-policies.md).

##  <a name="transformation-policies"></a><a name="TransformationPolicies"></a> Dönüştürme ilkeleri

-   [JSON 'ı XML 'e Dönüştür](api-management-transformation-policies.md#ConvertJSONtoXML) -JSON 'dan XML 'e istek veya Yanıt gövdesini dönüştürür.

-   [XML 'YI JSON 'A Dönüştür](api-management-transformation-policies.md#ConvertXMLtoJSON) -istek veya Yanıt gövdesini XML 'den JSON 'a dönüştürür.

-   [Gövdede dize bul ve Değiştir](api-management-transformation-policies.md#Findandreplacestringinbody) -bir istek veya Yanıt alt dizesini bulur ve farklı bir alt dizeyle değiştirir.

-   Yanıt gövdesindeki içerik-yazma (maske) bağlantılarında [bulunan URL 'leri](api-management-transformation-policies.md#MaskURLSContent) , ağ geçidi aracılığıyla eşdeğer bağlantıya işaret eden şekilde maskelerle.

-   [Arka uç hizmetini ayarlama](api-management-transformation-policies.md#SetBackendService) -gelen istek için arka uç hizmetini değiştirir.

-   [Gövde ayarla](api-management-transformation-policies.md#SetBody) -ileti gövdesini gelen ve giden istekler için ayarlar.

-   [Http üstbilgisini ayarla](api-management-transformation-policies.md#SetHTTPheader) -mevcut bir yanıt ve/veya istek üstbilgisine bir değer atar veya yeni bir yanıt ve/veya istek üst bilgisi ekler.

-   [Sorgu dizesi parametresini ayarla](api-management-transformation-policies.md#SetQueryStringParameter) -ekler, değerini değiştirir veya istek sorgu dizesi parametresini siler.

-   [Yeniden yazma URL 'si](api-management-transformation-policies.md#RewriteURL) -BIR istek URL 'sini ortak formdan Web hizmeti tarafından beklenen biçime dönüştürür.

-   [XSLT kullanarak XML dönüştürme](api-management-transformation-policies.md#XSLTransform) -istek veya yanıt gövdesinde XML 'e bir XSL dönüştürmesi uygular.

##  <a name="convert-json-to-xml"></a><a name="ConvertJSONtoXML"></a> JSON 'ı XML 'e Dönüştür
 `json-to-xml`İlke, JSON 'DAN XML 'e bir istek veya yanıt gövdesi dönüştürür.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<json-to-xml apply="always | content-type-json" consider-accept-header="true | false" parse-date="true | false"/>
```

### <a name="example"></a>Örnek

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <json-to-xml apply="always" consider-accept-header="false" parse-date="false"/>
    </outbound>
</policies>
```

### <a name="elements"></a>Öğeler

|Ad|Açıklama|Gerekli|
|----------|-----------------|--------------|
|JSON-XML|Kök öğe.|Yes|

### <a name="attributes"></a>Öznitelikler

|Ad|Açıklama|Gerekli|Varsayılan|
|----------|-----------------|--------------|-------------|
|apply|Özniteliğin aşağıdaki değerlerden birine ayarlanması gerekir.<br /><br /> -Always-her zaman dönüştürme uygula.<br />-Content-Type-JSON-yalnızca Response Content-Type üstbilgisi JSON varlığını gösteriyorsa Dönüştür.|Yes|Yok|
|göz önünde bulundurun-üst bilgi|Özniteliğin aşağıdaki değerlerden birine ayarlanması gerekir.<br /><br /> -true-istek kabul üst bilgisinde XML isteniyorsa dönüştürmeyi Uygula.<br />-false-dönüştürmeyi her zaman Uygula.|No|true|
|ayrıştırma tarihi|`false`Date değerleri ayarlandığında, dönüştürme sırasında yalnızca kopyalanır|No|true|

### <a name="usage"></a>Kullanım
 Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, giden, hata üzerine

-   **İlke kapsamları:** tüm kapsamlar

##  <a name="convert-xml-to-json"></a><a name="ConvertXMLtoJSON"></a> XML 'i JSON 'ye Dönüştür
 `xml-to-json`İlke bir isteği veya Yanıt GÖVDESINI XML 'den JSON 'a dönüştürür. Bu ilke, yalnızca XML arka uç Web Hizmetleri tabanlı API 'Leri modernleştirin için kullanılabilir.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<xml-to-json kind="javascript-friendly | direct" apply="always | content-type-xml" consider-accept-header="true | false"/>
```

### <a name="example"></a>Örnek

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <xml-to-json kind="direct" apply="always" consider-accept-header="false" />
    </outbound>
</policies>
```

### <a name="elements"></a>Öğeler

|Ad|Açıklama|Gerekli|
|----------|-----------------|--------------|
|XML-JSON|Kök öğe.|Yes|

### <a name="attributes"></a>Öznitelikler

|Ad|Açıklama|Gerekli|Varsayılan|
|----------|-----------------|--------------|-------------|
|denetlenmesi|Özniteliğin aşağıdaki değerlerden birine ayarlanması gerekir.<br /><br /> -JavaScript kullanımı kolay-dönüştürülen JSON, JavaScript geliştiricilerine kolay bir form içerir.<br />-Direct-dönüştürülmüş JSON orijinal XML belgesinin yapısını yansıtır.|Yes|Yok|
|apply|Özniteliğin aşağıdaki değerlerden birine ayarlanması gerekir.<br /><br /> -her zaman daima Dönüştür.<br />-Content-Type-XML-yalnızca Response Content-Type üstbilgisi XML varlığını gösteriyorsa Dönüştür.|Yes|Yok|
|göz önünde bulundurun-üst bilgi|Özniteliğin aşağıdaki değerlerden birine ayarlanması gerekir.<br /><br /> -true-istek Accept üst bilgisinde JSON isteniyorsa dönüştürme uygulayın.<br />-false-dönüştürmeyi her zaman Uygula.|No|true|

### <a name="usage"></a>Kullanım
 Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, giden, hata üzerine

-   **İlke kapsamları:** tüm kapsamlar

##  <a name="find-and-replace-string-in-body"></a><a name="Findandreplacestringinbody"></a> Gövdede dize bul ve Değiştir
 `find-and-replace`İlke bir istek veya Yanıt alt dizesi bulur ve farklı bir alt dizeyle değiştirir.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<find-and-replace from="what to replace" to="replacement" />
```

### <a name="example"></a>Örnek

```xml
<find-and-replace from="notebook" to="laptop" />
```

### <a name="elements"></a>Öğeler

|Ad|Açıklama|Gerekli|
|----------|-----------------|--------------|
|bul ve Değiştir|Kök öğe.|Yes|

### <a name="attributes"></a>Öznitelikler

|Ad|Açıklama|Gerekli|Varsayılan|
|----------|-----------------|--------------|-------------|
|Kaynak|Aranacak dize.|Yes|Yok|
|kullanıcısı|Değişim dizesi. Arama dizesini kaldırmak için sıfır uzunluğunda bir değiştirme dizesi belirtin.|Yes|Yok|

### <a name="usage"></a>Kullanım
 Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, giden, arka uç, hata durumunda

-   **İlke kapsamları:** tüm kapsamlar

##  <a name="mask-urls-in-content"></a><a name="MaskURLSContent"></a> İçerikte maske URL 'Leri
 `redirect-content-urls`Ağ Geçidi aracılığıyla eşdeğer bağlantıyı işaret ettikleri için, yanıt gövdesindeki ilke yeniden yazma (maskeleri) bağlantıları. Yanıt gövdesi bağlantılarını, ağ geçidine işaret etmek üzere yeniden yazmak için çıkış bölümünde kullanın. Ters bir efekt için gelen bölümünde kullanın.

> [!NOTE]
>  Bu ilke, üst bilgiler gibi üst bilgi değerlerini değiştirmez `Location` . Üst bilgi değerlerini değiştirmek için, [Set-Header](api-management-transformation-policies.md#SetHTTPheader) ilkesini kullanın.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<redirect-content-urls />
```

### <a name="example"></a>Örnek

```xml
<redirect-content-urls />
```

### <a name="elements"></a>Öğeler

|Ad|Açıklama|Gerekli|
|----------|-----------------|--------------|
|yeniden yönlendirme-içerik-URL 'ler|Kök öğe.|Yes|

### <a name="usage"></a>Kullanım
 Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, giden

-   **İlke kapsamları:** tüm kapsamlar

##  <a name="set-backend-service"></a><a name="SetBackendService"></a> Arka uç hizmetini ayarla
 `set-backend-service`Gelen bir isteği, bu işlem IÇIN API ayarlarında belirtilenden farklı bir arka uca yeniden yönlendirmek için ilkeyi kullanın. Bu ilke, gelen isteğin arka uç hizmeti taban URL 'sini ilkede belirtilen bir şekilde değiştirir.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<set-backend-service base-url="base URL of the backend service" />
```

veya

```xml
<set-backend-service backend-id="identifier of the backend entity specifying base URL of the backend service" />
```

> [!NOTE]
> Arka uç varlıkları [Azure Portal](how-to-configure-service-fabric-backend.md), Yönetim [API 'si](/rest/api/apimanagement)ve [PowerShell](https://www.powershellgallery.com/packages?q=apimanagement)aracılığıyla yönetilebilir.

### <a name="example"></a>Örnek

```xml
<policies>
    <inbound>
        <choose>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2013-05")">
                <set-backend-service base-url="http://contoso.com/api/8.2/" />
            </when>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2014-03")">
                <set-backend-service base-url="http://contoso.com/api/9.1/" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
Bu örnekte, arka uç hizmet ilkesi, sorgu dizesinde geçirilen sürüm değerine bağlı olarak istekleri API 'de belirtilenden farklı bir arka uç hizmetine yönlendirir.

Başlangıçta arka uç hizmeti temel URL 'si API ayarlarından türetilir. Bu nedenle, istek URL 'si `https://contoso.azure-api.net/api/partners/15?version=2013-05&subscription-key=abcdef` `http://contoso.com/api/10.4/partners/15?version=2013-05&subscription-key=abcdef` `http://contoso.com/api/10.4/` API ayarlarında arka uç hizmeti URL 'si olarak belirtilir.

<Select Policy deyimin [seçimi \> ](api-management-advanced-policies.md#choose) uygulandığında, arka uç hizmeti taban URL 'si, `http://contoso.com/api/8.2` `http://contoso.com/api/9.1` Sürüm isteği sorgu parametresinin değerine bağlı olarak ya da olarak yeniden değişebilir. Örneğin, değer `"2013-15"` son istek URL 'si ise olur `http://contoso.com/api/8.2/partners/15?version=2013-05&subscription-key=abcdef` .

İsteğin daha fazla dönüşümü isteniyorsa, diğer [dönüştürme ilkeleri](api-management-transformation-policies.md#TransformationPolicies) de kullanılabilir. Örneğin, isteğin sürüme özgü bir arka uca yönlendirildiğini artık sürüm sorgu parametresini kaldırmak için, artık gereksiz sürüm özniteliğini kaldırmak üzere  [sorgu dizesi ayarla parametresi](api-management-transformation-policies.md#SetQueryStringParameter) ilkesi kullanılabilir.

### <a name="example"></a>Örnek

```xml
<policies>
    <inbound>
        <set-backend-service backend-id="my-sf-service" sf-partition-key="@(context.Request.Url.Query.GetValueOrDefault("userId","")" sf-replica-type="primary" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
Bu örnekte ilke, bir Service Fabric arka ucuna, bölüm anahtarı olarak UserID sorgu dizesini ve bölümün birincil çoğaltmasını kullanarak isteği yönlendirir.

### <a name="elements"></a>Öğeler

|Ad|Açıklama|Gerekli|
|----------|-----------------|--------------|
|küme arka uç hizmeti|Kök öğe.|Yes|

### <a name="attributes"></a>Öznitelikler

|Ad|Açıklama|Gerekli|Varsayılan|
|----------|-----------------|--------------|-------------|
|taban URL 'si|Yeni arka uç hizmeti temel URL 'SI.|Bunlardan biri `base-url` `backend-id` mevcut olmalıdır.|Yok|
|arka uç kimliği|Yönlendirileceği arka ucun tanımlayıcısı. (Arka uç varlıkları [Azure Portal](how-to-configure-service-fabric-backend.md), [API](/rest/api/apimanagement)ve [PowerShell](https://www.powershellgallery.com/packages?q=apimanagement)aracılığıyla yönetilir.)|Bunlardan biri `base-url` `backend-id` mevcut olmalıdır.|Yok|
|BT-bölüm-anahtar|Yalnızca arka uç bir Service Fabric hizmeti olduğunda ve ' arka uç kimliği ' kullanılarak belirtildiğinde geçerlidir. Ad çözümleme hizmetinden belirli bir bölümü çözümlemek için kullanılır.|No|Yok|
|SF-Replication-Type|Yalnızca arka uç bir Service Fabric hizmeti olduğunda ve ' arka uç kimliği ' kullanılarak belirtildiğinde geçerlidir. İsteğin bir bölümün birincil veya ikincil çoğaltmaya gitmesi gerekip gerekmediğini denetler. |No|Yok|
|SF-Resolve-koşul|Yalnızca arka uç bir Service Fabric hizmeti olduğunda geçerlidir. Service Fabric arka uca çağrının yeni çözümlenerek tekrarlanması gerektiğini belirleyen koşul.|No|Yok|
|SF-Service-örnek-adı|Yalnızca arka uç bir Service Fabric hizmeti olduğunda geçerlidir. Çalışma zamanında hizmet örneklerinin değiştirilmesine izin verir. |No|Yok|
|SF-Listener-adı|Yalnızca arka uç bir Service Fabric hizmeti olduğunda ve ' arka uç kimliği ' kullanılarak belirtildiğinde geçerlidir. Service Fabric Reliable Services, bir hizmette birden çok dinleyici oluşturmanıza olanak sağlar. Bu öznitelik, bir arka uç güvenilir hizmeti birden fazla dinleyiciye sahip olduğunda belirli bir dinleyiciyi seçmek için kullanılır. Bu öznitelik belirtilmemişse API Management, ad olmadan bir dinleyici kullanmayı dener. Adı olmayan bir dinleyici, yalnızca bir dinleyici içeren Reliable Services için tipik bir addır. |No|Yok|

### <a name="usage"></a>Kullanım
 Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, arka uç

-   **İlke kapsamları:** tüm kapsamlar

##  <a name="set-body"></a><a name="SetBody"></a> Gövde ayarla
 `set-body`Gelen ve giden istekler için ileti gövdesini ayarlamak üzere ilkesini kullanın. İleti gövdesine erişmek için, `context.Request.Body` `context.Response.Body` ilkenin gelen veya giden bölümünde olup olmadığına bağlı olarak özelliğini veya ' i kullanabilirsiniz.

> [!IMPORTANT]
>  Varsayılan olarak, veya kullanarak ileti gövdesine eriştiğinizde `context.Request.Body` `context.Response.Body` , özgün ileti gövdesinin kaybedildiğini ve gövdeye geri dönerek gövde döndürülerek ayarlanması gerektiğini unutmayın. Gövde içeriğini korumak için, `preserveContent` iletiye erişirken parametresini olarak ayarlayın `true` . `preserveContent`Olarak ayarlıysa `true` ve ifade tarafından farklı bir gövde döndürülürse, döndürülen gövde kullanılır.
>
>  Lütfen ilkeyi kullanırken aşağıdaki noktalara dikkat edin `set-body` .
>
> - Yeni `set-body` veya güncelleştirilmiş bir gövdeye geri dönmek için ilkeyi kullanıyorsanız, `preserveContent` `true` Yeni gövde içeriğini açıkça tedarik ettiğiniz için ' a ayarlamanız gerekmez.
>   -   Henüz yanıt bulunmadığından, gelen işlem hattındaki bir yanıtın içeriğini korumak anlamlı değildir.
>   -   İstek bu noktada arka uca zaten gönderildiğinden, giden işlem hattındaki bir isteğin içeriğini korumak mantıklı değildir.
>   -   Bu ilke, bir ileti gövdesi olmadığında (örneğin, gelen GET içinde kullanılırsa) bir özel durum oluşturulur.

 Daha fazla bilgi için `context.Request.Body` `context.Response.Body` `IMessage` [bağlam değişkeni](api-management-policy-expressions.md#ContextVariables) tablosundaki, ve bölümlerine bakın.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<set-body>new body value as text</set-body>
```

### <a name="examples"></a>Örnekler

#### <a name="literal-text-example"></a>Değişmez değer metin örneği

```xml
<set-body>Hello world!</set-body>
```

#### <a name="example-accessing-the-body-as-a-string-note-that-we-are-preserving-the-original-request-body-so-that-we-can-access-it-later-in-the-pipeline"></a>Bir dize olarak gövdeye erişen örnek. İşlem hattında daha sonra erişebilmemiz için özgün istek gövdesini koruma yaptık.

```xml
<set-body>
@{ 
    string inBody = context.Request.Body.As<string>(preserveContent: true); 
    if (inBody[0] =='c') { 
        inBody[0] = 'm'; 
    } 
    return inBody; 
}
</set-body>
```

#### <a name="example-accessing-the-body-as-a-jobject-note-that-since-we-are-not-reserving-the-original-request-body-accessing-it-later-in-the-pipeline-will-result-in-an-exception"></a>Bir JObject olarak gövdeye erişen örnek. Özgün istek gövdesini ayırdığımızdan, daha sonra işlem hattının daha sonra erişilmesi için bir özel durumla sonuçlandığına unutmayın.

```xml
<set-body> 
@{ 
    JObject inBody = context.Request.Body.As<JObject>(); 
    if (inBody.attribute == <tag>) { 
        inBody[0] = 'm'; 
    } 
    return inBody.ToString(); 
} 
</set-body>

```

#### <a name="filter-response-based-on-product"></a>Yanıtı ürüne göre filtrele
 Bu örnek, ürünü kullanırken arka uç hizmetinden alınan yanıttan veri öğelerini kaldırarak içerik filtrelemenin nasıl gerçekleştirileceğini gösterir `Starter` . Bu ilkeyi yapılandırma ve kullanma gösterimi için bkz. [Cloud Cover bölüm 177: Vlad Vinogradsky Ile daha fazla API Management özelliği](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) ve 34:30 ile ileri sarma. Bu demo için kullanılan [koyu gök tahmini API 'sine](https://developer.forecast.io/) ilişkin bir genel bakış görmek için 31:50 adresinden başlayın.

```xml
<!-- Copy this snippet into the outbound section to remove a number of data elements from the response received from the backend service based on the name of the api product -->
<choose>
  <when condition="@(context.Response.StatusCode == 200 && context.Product.Name.Equals("Starter"))">
    <set-body>@{
        var response = context.Response.Body.As<JObject>();
        foreach (var key in new [] {"minutely", "hourly", "daily", "flags"}) {
          response.Property (key).Remove ();
        }
        return response.ToString();
      }
    </set-body>
  </when>
</choose>
```

### <a name="using-liquid-templates-with-set-body"></a>Set Body ile sıvı şablonları kullanma
`set-body`İlke, bir isteğin veya yanıtın gövdesini dönüştürmek Için [likit](https://shopify.github.io/liquid/basics/introduction/) şablon oluşturma dilini kullanacak şekilde yapılandırılabilir. İletinizin biçimini tamamen yeniden şekillendirmenizi gerektiren bu, çok etkili olabilir.

> [!IMPORTANT]
> İlkede kullanılan likit 'in uygulanması `set-body` ' C# Mode ' içinde yapılandırıldı. Filtreleme gibi şeyler yaparken bu özellikle önemlidir. Örnek olarak, bir tarih filtresi kullanmak için, Pascal büyük harfleri ve C# Tarih biçimlendirmesinin kullanılması gerekir, örneğin:
>
> {{Body. foo. startDateTime | Tarih: "yyyyMMddTHH: mm: ddZ"}}

> [!IMPORTANT]
> Sıvı şablonunu kullanarak bir XML gövdesine doğru bir şekilde bağlamak için bir `set-header` ilke kullanarak Içerik türünü Application/XML, Text/XML (veya + XML ile biten herhangi bir tür) olarak ayarlayın; BIR JSON gövdesi için uygulama/JSON, metin/JSON (veya + JSON ile biten herhangi bir tür) olmalıdır.

#### <a name="convert-json-to-soap-using-a-liquid-template"></a>Sıvı şablonu kullanarak JSON 'ı SOAP 'ye dönüştürme
```xml
<set-body template="liquid">
    <soap:Envelope xmlns="http://tempuri.org/" xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
        <soap:Body>
            <GetOpenOrders>
                <cust>{{body.getOpenOrders.cust}}</cust>
            </GetOpenOrders>
        </soap:Body>
    </soap:Envelope>
</set-body>
```

#### <a name="transform-json-using-a-liquid-template"></a>Sıvı şablonu kullanarak JSON dönüştürme
```xml
{
"order": {
    "id": "{{body.customer.purchase.identifier}}",
    "summary": "{{body.customer.purchase.orderShortDesc}}"
    }
}
```

### <a name="elements"></a>Öğeler

|Ad|Açıklama|Gerekli|
|----------|-----------------|--------------|
|gövde kümesi|Kök öğe. Gövde metnini veya gövde döndüren ifadeleri içerir.|Yes|

### <a name="properties"></a>Özellikler

|Ad|Açıklama|Gerekli|Varsayılan|
|----------|-----------------|--------------|-------------|
|şablon|Set Body ilkesinin çalışacağı şablon oluşturma modunu değiştirmek için kullanılır. Şu anda desteklenen tek değer:<br /><br />-sıvı-Set Body ilkesi likit şablon oluşturma altyapısını kullanacaktır |No||

İstek ve yanıt hakkındaki bilgilere erişmek için, sıvı şablonu aşağıdaki özelliklerle bir bağlam nesnesine bağlanabilir: <br />
<pre>context.
    Request.
        Url
        Method
        OriginalMethod
        OriginalUrl
        IpAddress
        MatchedParameters
        HasBody
        ClientCertificates
        Headers

    Response.
        StatusCode
        Method
        Headers
Url.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString

OriginalUrl.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString
</pre>



### <a name="usage"></a>Kullanım
 Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, giden, arka uç

-   **İlke kapsamları:** tüm kapsamlar

##  <a name="set-http-header"></a><a name="SetHTTPheader"></a> HTTP üstbilgisini ayarla
 `set-header`İlke, var olan bir yanıt ve/veya istek üstbilgisine bir değer atar veya yeni bir yanıt ve/veya istek üst bilgisi ekler.

 Http üstbilgilerinin bir listesini bir HTTP iletisine ekler. Gelen bir işlem hattına yerleştirildiğinde, bu ilke hedef hizmete geçirilmekte olan isteğin HTTP üst bilgilerini ayarlar. Giden bir işlem hattına yerleştirildiğinde, bu ilke ağ geçidinin istemcisine gönderilen yanıtın HTTP üst bilgilerini ayarlar.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<set-header name="header name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple headers with the same name add additional value elements-->
</set-header>
```

### <a name="examples"></a>Örnekler

#### <a name="example---adding-header-override-existing"></a>Örnek-üstbilgi ekleniyor, Varolanı geçersiz kıl

```xml
<set-header name="some header name" exists-action="override">
    <value>20</value>
</set-header>
```
#### <a name="example---removing-header"></a>Örnek-üst bilgi kaldırılıyor

```xml
 <set-header name="some header name" exists-action="delete" />
```



#### <a name="forward-context-information-to-the-backend-service"></a>Bağlam bilgilerini arka uç hizmetine ilet
 Bu örnek, arka uç hizmetine bağlam bilgilerini sağlamak üzere API düzeyinde ilkenin nasıl uygulanacağını gösterir. Bu ilkeyi yapılandırma ve kullanma gösterimi için bkz. [Cloud Cover bölüm 177: Vlad Vinogradsky Ile daha fazla API Management özelliği](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) ve 10:30 ile ileri sarma. 12:10 ' de, bir işlemi iş üzerindeki ilkeyi görebileceğiniz Geliştirici Portalında çağırma tanıtımı vardır.

```xml
<!-- Copy this snippet into the inbound element to forward some context information, user id and the region the gateway is hosted in, to the backend service for logging or evaluation -->
<set-header name="x-request-context-data" exists-action="override">
  <value>@(context.User.Id)</value>
  <value>@(context.Deployment.Region)</value>
</set-header>
```

 Daha fazla bilgi için bkz. [ilke ifadeleri](api-management-policy-expressions.md) ve [bağlam değişkeni](api-management-policy-expressions.md#ContextVariables).

> [!NOTE]
> Bir üstbilginin birden fazla değeri bir CSV dizesine birleştirilir, örneğin: `headerName: value1,value2,value3`
>
> Özel durumlar standartlaştırılmış üst bilgiler, bu değerleri içerir:
> - virgül ( `User-Agent` , `WWW-Authenticate` ,) içerebilir `Proxy-Authenticate` ,
> - Tarih içerebilir ( `Cookie` , `Set-Cookie` , `Warning` ),
> - Tarih ( `Date` ,,,, `Expires` `If-Modified-Since` `If-Unmodified-Since` `Last-Modified` ,) içerir `Retry-After` .
>
> Bu özel durumlar söz konusu olduğunda, birden çok üstbilgi değeri tek bir dizeye birleştirmeyecektir ve ayrı üstbilgiler olarak geçirilir, örneğin: `User-Agent: value1`
>`User-Agent: value2`
>`User-Agent: value3`

### <a name="elements"></a>Öğeler

|Ad|Açıklama|Gerekli|
|----------|-----------------|--------------|
|Set-Header|Kök öğe.|Yes|
|değer|Ayarlanacak üst bilginin değerini belirtir. Aynı ada sahip birden çok üstbilgi için ek `value` öğeler ekleyin.|No|

### <a name="properties"></a>Özellikler

|Ad|Açıklama|Gerekli|Varsayılan|
|----------|-----------------|--------------|-------------|
|var-eylem|Üstbilgi zaten belirtildiğinde gerçekleştirilecek eylemi belirtir. Bu öznitelik aşağıdaki değerlerden birine sahip olmalıdır.<br /><br /> -override-mevcut üstbilginin değerini değiştirir.<br />-Skip-mevcut üst bilgi değerini değiştirmez.<br />-Append-değeri varolan üst bilgi değerine ekler.<br />-Delete-üstbilgiyi istekten kaldırır.<br /><br /> `override`Aynı ada sahip birden çok girdiyi listelemek üzere ayarlandığında, üstbilgi tüm girişlere göre ayarlanmakta (birden çok kez listelenecektir), ancak sonuçlarda listelenen değerler ayarlanır.|No|override|
|name|Ayarlanacak üst bilginin adını belirtir.|Yes|Yok|

### <a name="usage"></a>Kullanım
 Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, giden, arka uç, hata durumunda

-   **İlke kapsamları:** tüm kapsamlar

##  <a name="set-query-string-parameter"></a><a name="SetQueryStringParameter"></a> Sorgu dizesi parametresini ayarla
 `set-query-parameter`İlke, değer ekler veya siler veya istek sorgu dizesi parametresini siler. İsteğe bağlı olan veya istekte hiçbir şekilde bulunmayan arka uç hizmeti tarafından beklenen sorgu parametrelerini geçirmek için kullanılabilir.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<set-query-parameter name="param name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple parameters with the same name add additional value elements-->
</set-query-parameter>
```

#### <a name="example"></a>Örnek

```xml

<set-query-parameter name="api-key" exists-action="skip">
  <value>12345678901</value>
</set-query-parameter>

```

#### <a name="forward-context-information-to-the-backend-service"></a>Bağlam bilgilerini arka uç hizmetine ilet
 Bu örnek, arka uç hizmetine bağlam bilgilerini sağlamak üzere API düzeyinde ilkenin nasıl uygulanacağını gösterir. Bu ilkeyi yapılandırma ve kullanma gösterimi için bkz. [Cloud Cover bölüm 177: Vlad Vinogradsky Ile daha fazla API Management özelliği](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) ve 10:30 ile ileri sarma. 12:10 ' de, bir işlemi iş üzerindeki ilkeyi görebileceğiniz Geliştirici Portalında çağırma tanıtımı vardır.

```xml
<!-- Copy this snippet into the inbound element to forward a piece of context, product name in this example, to the backend service for logging or evaluation -->
<set-query-parameter name="x-product-name" exists-action="override">
  <value>@(context.Product.Name)</value>
</set-query-parameter>

```

 Daha fazla bilgi için bkz. [ilke ifadeleri](api-management-policy-expressions.md) ve [bağlam değişkeni](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Öğeler

|Ad|Açıklama|Gerekli|
|----------|-----------------|--------------|
|Set-Query-Parameter|Kök öğe.|Yes|
|değer|Ayarlanacak sorgu parametresi değerini belirtir. Aynı ada sahip birden çok sorgu parametresi için ek `value` öğeler ekleyin.|Yes|

### <a name="properties"></a>Özellikler

|Ad|Açıklama|Gerekli|Varsayılan|
|----------|-----------------|--------------|-------------|
|var-eylem|Sorgu parametresi önceden belirtildiğinde gerçekleştirilecek eylemi belirtir. Bu öznitelik aşağıdaki değerlerden birine sahip olmalıdır.<br /><br /> -override-var olan parametrenin değerini değiştirir.<br />-Skip-var olan sorgu parametresi değerinin yerini almaz.<br />-Append-değeri varolan sorgu parametresi değerine ekler.<br />-Delete-sorgu parametresini istekten kaldırır.<br /><br /> `override`Aynı ada sahip birden çok girdiyi listelemek üzere ayarlandığında, sorgu parametresi tüm girişlere göre ayarlanmakta (birden çok kez listelenecektir), ancak sonuçlarda listelenen değerler ayarlanır.|No|override|
|name|Ayarlanacak sorgu parametresinin adını belirtir.|Yes|Yok|

### <a name="usage"></a>Kullanım
 Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, arka uç

-   **İlke kapsamları:** tüm kapsamlar

##  <a name="rewrite-url"></a><a name="RewriteURL"></a> Yeniden yazma URL 'SI
 `rewrite-uri`İlke, aşağıdaki örnekte gösterildiği gibi, bir Istek URL 'sini ortak formdan Web hizmeti tarafından beklenen biçime dönüştürür.

- Genel URL- `http://api.example.com/storenumber/ordernumber`

- İstek URL 'SI- `http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State`

  Bu ilke, bir insan ve/veya tarayıcı kullanımı kolay URL 'SI Web hizmeti tarafından beklenen URL biçimine dönüştürülebileceğinden kullanılabilir. Bu ilkenin yalnızca bir sorgu dizesi içermeyen ve bunun yerine yalnızca kaynağın yolunu (düzen ve yetkilinin ardından) içeren, temiz URL 'ler, yeniden oluşturma URL 'leri, Kullanıcı dostu URL 'ler veya SEO kullanımı kolay URL 'ler gibi alternatif bir URL biçimi kullanıma sunulduktan sonra uygulanması gerekir. Bu genellikle Aesthetic Characteristics, kullanılabilirlik veya arama motoru iyileştirme (SEO) amaçları için yapılır.

> [!NOTE]
>  Yalnızca ilkeyi kullanarak sorgu dizesi parametreleri ekleyebilirsiniz. Yeniden yazma URL 'sine ek şablon yolu parametreleri ekleyemezsiniz.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<rewrite-uri template="uri template" copy-unmatched-params="true | false" />
```

### <a name="example"></a>Örnek

```xml
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/v2/US/hardware/{storenumber}&{ordernumber}?City=city&State=state" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put?c=d -->
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" copy-unmatched-params="false" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put -->
```

### <a name="elements"></a>Öğeler

|Ad|Açıklama|Gerekli|
|----------|-----------------|--------------|
|yeniden yazma-URI|Kök öğe.|Yes|

### <a name="attributes"></a>Öznitelikler

|Öznitelik|Açıklama|Gerekli|Varsayılan|
|---------------|-----------------|--------------|-------------|
|şablon|Herhangi bir sorgu dizesi parametresine sahip gerçek Web hizmeti URL 'SI. İfadeler kullanılırken, tüm değer bir ifade olmalıdır.|Yes|Yok|
|kopya-eşleşmeyen-params|Gelen istekteki sorgu parametrelerinin özgün URL şablonunda mevcut değil olarak yeniden yazma şablonu tarafından tanımlanan URL 'ye eklenip eklenmeyeceğini belirtir|No|true|

### <a name="usage"></a>Kullanım
 Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen

-   **İlke kapsamları:** tüm kapsamlar

##  <a name="transform-xml-using-an-xslt"></a><a name="XSLTransform"></a> XSLT kullanarak XML dönüştürme
 `Transform XML using an XSLT`İlke, istek veya Yanıt GÖVDESINDE XML 'e BIR XSL dönüştürmesi uygular.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<xsl-transform>
    <parameter name="User-Agent">@(context.Request.Headers.GetValueOrDefault("User-Agent","non-specified"))</parameter>
    <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
        <xsl:output method="xml" indent="yes" />
        <xsl:param name="User-Agent" />
        <xsl:template match="* | @* | node()">
            <xsl:copy>
                <xsl:if test="self::* and not(parent::*)">
                    <xsl:attribute name="User-Agent">
                        <xsl:value-of select="$User-Agent" />
                    </xsl:attribute>
                </xsl:if>
                <xsl:apply-templates select="* | @* | node()" />
            </xsl:copy>
        </xsl:template>
    </xsl:stylesheet>
  </xsl-transform>
```

### <a name="example"></a>Örnek

```xml
<policies>
  <inbound>
      <base />
  </inbound>
  <outbound>
      <base />
      <xsl-transform>
          <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
            <xsl:output omit-xml-declaration="yes" method="xml" indent="yes" />
            <!-- Copy all nodes directly-->
            <xsl:template match="node()| @*|*">
                <xsl:copy>
                    <xsl:apply-templates select="@* | node()|*" />
                </xsl:copy>
            </xsl:template>
          </xsl:stylesheet>
    </xsl-transform>
  </outbound>
</policies>
```

### <a name="elements"></a>Öğeler

|Ad|Açıklama|Gerekli|
|----------|-----------------|--------------|
|XSL dönüştürme|Kök öğe.|Yes|
|parametre|Dönüşümde kullanılan değişkenleri tanımlamak için kullanılır|No|
|xsl: StyleSheet|Kök stil sayfası öğesi. İçinde tanımlanan tüm öğeler ve öznitelikler standart [XSLT belirtimine](https://www.w3.org/TR/xslt) uyar|Yes|

### <a name="usage"></a>Kullanım
 Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, giden

-   **İlke kapsamları:** tüm kapsamlar

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:

+ [API Management ilkeler](api-management-howto-policies.md)
+ İlke deyimlerinin ve ayarlarının tam listesi için [Ilke başvurusu](./api-management-policies.md)
+ [İlke örnekleri](./policy-reference.md)