---
title: Media Services işlemler REST API genel bakış | Microsoft Docs
description: Media Services Işlemler REST API, bir Media Services hesabındaki Işleri, varlıkları, canlı kanalları ve diğer kaynakları oluşturmak için kullanılır. Bu makalede bir Azure Media Services V2 REST API genel bakış sunulmaktadır.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: a5f1c5e7-ec52-4e26-9a44-d9ea699f68d9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.reviewer: johndeu
ms.openlocfilehash: 9f147e333e4d1b95a14dd3121d7ab304b6166248
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103010057"
---
# <a name="media-services-operations-rest-api-overview"></a>Media Services işlemler REST API genel bakış

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>[V3 Media Services](../latest/index.yml)en son sürüme göz atın. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-v-2-v-3-migration-introduction.md)

**Media Services OPERATIONS Rest** API 'si, bir Media Services hesabında Işler, varlıklar, Canlı Kanallar ve diğer kaynakları oluşturmak için kullanılır. Daha fazla bilgi için bkz. [Media Services Operations REST API başvurusu](/rest/api/media/operations/azure-media-services-rest-api-reference).

Media Services hem JSON hem de atom + pub XML biçimini kabul eden bir REST API sağlar. Media Services REST API, her istemcinin Media Services bağlantı kurulurken gönderilmesi gereken belirli HTTP üstbilgileri ve isteğe bağlı üstbilgiler kümesi gerektirir. Aşağıdaki bölümlerde, istek oluştururken ve Media Services yanıtlarını alırken kullanabileceğiniz üst bilgiler ve HTTP fiilleri açıklanır.

Media Services REST API kimlik doğrulaması, [Azure MEDIA SERVICES API 'SINE Rest ile erişmek Için Azure AD kimlik doğrulamasını kullanma](media-services-rest-connect-with-aad.md) makalesinde özetlenen Azure Active Directory kimlik doğrulaması aracılığıyla yapılır

## <a name="considerations"></a>Dikkat edilmesi gerekenler

REST kullanılırken aşağıdaki noktalar geçerlidir.

* Varlıkları sorgularken, genel REST v2 sorgu sonuçlarını 1000 sonuçla sınırladığından, tek seferde döndürülen 1000 varlıkların bir sınırı vardır. [Bu .net örneğinde](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) ve [Bu REST API örnekte](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities)açıklandığı gibi **Atla** ve **Al** (.net)/ **top** (REST) kullanmanız gerekir. 
* JSON kullanırken ve istekte **__metadata** anahtar sözcüğünü kullanmak için belirtirken (örneğin, bağlantılı bir nesneye başvurmak Için) **Accept** üst bilgisini [JSON verbose biçimine](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) ayarlamanız gerekir (aşağıdaki örneğe bakın). OData, istek içinde **__metadata** özelliğini ayrıntılı olarak ayarlamadığınız müddetçe anlamaz.  

    ```console
    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

    {
        "Name" : "NewTestJob", 
        "InputMediaAssets" : 
            [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
    . . . 
   ```

## <a name="standard-http-request-headers-supported-by-media-services"></a>Media Services tarafından desteklenen standart HTTP istek üstbilgileri
Media Services yaptığınız her çağrı için, isteğinize dahil etmeniz gereken bir başlık kümesi ve ayrıca dahil etmek isteyebileceğiniz bir isteğe bağlı üst bilgi kümesi vardır. Aşağıdaki tabloda gereken üstbilgiler listelenmiştir:

| Üst bilgi | Tür | Değer |
| --- | --- | --- |
| Yetkilendirme |Taşıyıcı |Taşıyıcı tek kabul edilen yetkilendirme mekanizmasıdır. Değer, Azure Active Directory tarafından sağlanmış erişim belirtecini de içermelidir. |
| x-MS-sürümü |Ondalık |2,17 (veya en son sürüm)|
| DataServiceVersion |Ondalık |3.0 |
| MaxDataServiceVersion |Ondalık |3.0 |

> [!NOTE]
> Media Services, REST API 'Lerini kullanıma sunmak için OData kullandığından, DataServiceVersion ve MaxDataServiceVersion üstbilgileri tüm isteklere eklenmelidir; Ancak, yoksa şu anda Media Services, kullanılmakta olan DataServiceVersion değerinin 3,0 olduğunu varsayar.
> 
> 

Aşağıda, isteğe bağlı bir başlık kümesi verilmiştir:

| Üst bilgi | Tür | Değer |
| --- | --- | --- |
| Tarih |RFC 1123 tarihi |İsteğin zaman damgası |
| Kabul Et |İçerik türü |Aşağıdaki gibi yanıt için istenen içerik türü:<p> -Application/JSON; OData = verbose<p> -Application/atom + XML<p> Yanıtlar, blob getirme gibi farklı bir içerik türüne sahip olabilir, burada başarılı bir yanıt yük olarak blob akışını içerir. |
| Accept-Encoding |Gzip, söndür |Uygun olduğunda GZIP ve söndür kodlaması. Note: büyük kaynaklar Için Media Services bu üstbilgiyi yoksayabilir ve sıkıştırılmış olmayan verileri döndürebilir. |
| Accept-Language |"en", "es" vb. |Yanıt için tercih edilen dili belirtir. |
| Accept-Charset |"UTF-8" gibi karakter kümesi türü |Varsayılan UTF-8 ' dir. |
| X-HTTP-yöntemi |HTTP yöntemi |Bu yöntemleri kullanmak için PUT veya DELETE gibi HTTP yöntemlerini desteklemeyen istemcilerin veya güvenlik duvarlarının, bir GET çağrısıyla tünelde kullanılmasına izin verir. |
| İçerik Türü |İçerik türü |İstek gövdesinin PUT veya POST isteklerinde içerik türü. |
| istemci-istek kimliği |Dize |Belirtilen isteği tanımlayan, arayan tarafından tanımlanan bir değer. Belirtilmişse bu değer, isteği eşlemek için bir yol olarak yanıt iletisine dahil edilir. <p><p>**Önemli**<p>Değerler 20 96b 'de (2k) alınmalıdır. |

## <a name="standard-http-response-headers-supported-by-media-services"></a>Media Services tarafından desteklenen standart HTTP yanıt üst bilgileri
Aşağıda, talep ettiğiniz kaynağa ve gerçekleştirmek istediğiniz eyleme bağlı olarak size döndürülebilecek bir üst bilgi kümesi verilmiştir.

| Üst bilgi | Tür | Değer |
| --- | --- | --- |
| istek kimliği |Dize |Geçerli işlem için benzersiz bir tanımlayıcı, hizmet oluşturuldu. |
| istemci-istek kimliği |Dize |Varsa, özgün istekte çağıran tarafından belirtilen bir tanımlayıcı. |
| Tarih |RFC 1123 tarihi |İsteğin işlendiği tarih/saat. |
| İçerik Türü |Değişir |Yanıt gövdesinin içerik türü. |
| İçerik kodlama |Değişir |Uygun şekilde gzip veya söndür. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Media Services tarafından desteklenen standart HTTP fiilleri
Aşağıda HTTP istekleri yapılırken kullanılabilecek HTTP fiillerinin kapsamlı bir listesi verilmiştir:

| Fiil | Açıklama |
| --- | --- |
| GET |Bir nesnenin geçerli değerini döndürür. |
| POST |Belirtilen verileri temel alan bir nesne oluşturur veya bir komut gönderir. |
| PUT |Bir nesnenin yerini alır veya bir adlandırılmış nesne (varsa) oluşturur. |
| DELETE |Bir nesneyi siler. |
| BIRLEÞTIRMEK |Varolan bir nesneyi adlandırılmış özellik değişiklikleriyle güncelleştirir. |
| HEAD |GET yanıtı için bir nesnenin meta verilerini döndürür. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>Media Services varlık modelini bul ve araştır
Media Services varlıkların daha bulunabilir olmasını sağlamak için $metadata işlemi kullanılabilir. Tüm geçerli varlık türlerini, varlık özelliklerini, ilişkilendirmeleri, işlevleri, eylemleri vb. almanızı sağlar. $Metadata işlemini Media Services REST API uç noktanızın sonuna ekleyerek, bu bulma hizmetine erişebilirsiniz.

 /api/$metadata.

Meta verileri bir tarayıcıda görüntülemek veya isteğinize x-MS-Version üst bilgisini dahil etmek istiyorsanız, "? api-Version = 2. x" değerini URI 'nin sonuna ekleyin.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>Azure Active Directory kullanarak Media Services REST ile kimlik doğrulama

REST API kimlik doğrulaması Azure Active Directory (AAD) aracılığıyla yapılır.
Azure portalından Media Services hesabınız için gerekli kimlik doğrulama ayrıntılarını alma hakkında daha fazla bilgi için bkz. [Azure AD kimlik doğrulamasıyla Azure Media Services API 'Sine erişme](media-services-use-aad-auth-to-access-ams-api.md).

Azure AD kimlik doğrulaması kullanarak REST API bağlanan kod yazma hakkında ayrıntılı bilgi için bkz. [Azure AD kimlik doğrulamasını kullanarak Azure MEDIA SERVICES API 'SINE Rest ile erişme](media-services-rest-connect-with-aad.md).

## <a name="next-steps"></a>Sonraki adımlar
Azure AD kimlik doğrulamasını Media Services REST API ile nasıl kullanacağınızı öğrenmek için bkz. [Azure MEDIA SERVICES API 'SINE Rest ile erişmek Için Azure AD kimlik doğrulamasını kullanma](media-services-rest-connect-with-aad.md).

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
