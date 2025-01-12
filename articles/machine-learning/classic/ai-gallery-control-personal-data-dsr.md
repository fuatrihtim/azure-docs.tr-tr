---
title: 'ML Studio (klasik): Azure Yapay Zeka Galerisi verilerini yönetme-Azure'
description: Arabirim veya AI Galerisi kataloğu API 'sini kullanarak Azure Yapay Zeka Galerisi ürün içi kullanıcı verilerinizi dışa aktarabilir ve silebilirsiniz. Bu makalede nasıl yapılacağı gösterilir.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 05/25/2018
ms.reviewer: mldocs
ms.openlocfilehash: 3461b8026079d089bacca9f12aa94e13f5cb2874
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519244"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Azure Yapay Zeka Galerisi ürün içi kullanıcı verilerini görüntüleyin ve silin



Arabirim veya AI Galerisi kataloğu API 'sini kullanarak Azure Yapay Zeka Galerisi ürün içi kullanıcı verilerinizi görüntüleyebilir ve silebilirsiniz. Bu makalede nasıl yapılacağı açıklanır.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>UI ile verileri AI galerisinde görüntüleme

Azure Yapay Zeka Galerisi Web sitesi kullanıcı arabirimi aracılığıyla yayımladığınız öğeleri görüntüleyebilirsiniz. Kullanıcılar hem genel hem de listelenmeyen çözümleri, projeleri, denemeleri ve diğer yayımlanmış öğeleri görüntüleyebilir:

1.    [Azure yapay zeka Galerisi](https://gallery.azure.ai/)oturum açın.
2.    Sağ üst köşedeki profil resmine ve sonra profil sayfanızı yüklemek için hesap adına tıklayın.
3.    Profil sayfası, listelenmemiş girişler de dahil olmak üzere galeride yayınlanan tüm öğeleri görüntüler.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>Verilerinizi görüntülemek için AI Galerisi katalog API 'sini kullanın

Adresinden erişilebilen AI Galerisi katalog API 'SI aracılığıyla toplanan verileri programlı bir şekilde görüntüleyebilirsiniz https://catalog.cortanaanalytics.com/entities . Verileri görüntülemek için, yazar KIMLIĞINIZ gerekir. Listelenmemiş varlıkları katalog API 'SI aracılığıyla görüntülemek için bir erişim belirtecine ihtiyacınız vardır.

Katalog yanıtları JSON biçiminde döndürülür.

### <a name="get-an-author-id"></a>Yazar KIMLIĞI al
Yazar KIMLIĞI, Azure Yapay Zeka Galerisi yayımlarken kullanılan e-posta adresini temel alır. Değişmez:

1. [Azure yapay zeka Galerisi](https://gallery.azure.ai/)oturum açın.
2. Sağ üst köşedeki profil resmine ve sonra profil sayfanızı yüklemek için hesap adına tıklayın.
3. Adres çubuğundaki URL aşağıdaki alfasayısal KIMLIĞI görüntüler `authorId=` . Örneğin, URL için: \
    `https://gallery.azure.ai/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

    Yazar KIMLIĞI: \
    `99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>Erişim belirtecinizi alın

Listelenmemiş varlıkları katalog API 'SI aracılığıyla görüntülemek için bir erişim belirtecine ihtiyacınız vardır. Kullanıcılar, erişim belirteci olmadan ortak varlıkları ve diğer Kullanıcı bilgilerini görüntülemeye devam edebilir.

Erişim belirteci almak için, `DataLabAccessToken` oturum açıkken tarayıcının katalog API 'sine yaptığı http isteğinin üst bilgisini incelemeniz gerekir:

1.    [Azure yapay zeka Galerisi](https://gallery.azure.ai/)oturum açın.
2.    Sağ üst köşedeki profil resmine ve sonra profil sayfanızı yüklemek için hesap adına tıklayın.
3.    F12 tuşuna basarak tarayıcı Geliştirici Araçları bölmesini açın, ağ sekmesini seçin ve sayfayı yenileyin. 
4. Filtre metin kutusuna yazarak dize *kataloğunda* istekleri filtreleyin.
5.    URL 'de istekler ' de `https://catalog.cortanaanalytics.com/entities` BIR get isteği bulun ve *üstbilgiler* sekmesini seçin. aşağı kaydırarak *Istek üst bilgileri* bölümüne gidin.
6.    Üstbilginin altında `DataLabAccessToken` alfasayısal belirteç bulunur. Verilerinizin güvenliğini sağlamaya yardımcı olmak için bu belirteci paylaşmayın.

### <a name="view-user-information"></a>Kullanıcı bilgilerini görüntüle
Önceki adımlarda aldığınız yazar KIMLIĞINI kullanarak, bir kullanıcının profilindeki bilgileri `[AuthorId]` AŞAĞıDAKI URL 'de değiştirerek görüntüleyin:

`https://catalog.cortanaanalytics.com/users/[AuthorID]`

Örneğin, bu URL isteği:

`https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

Şöyle bir yanıt döndürür:

```json
{"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}
```

### <a name="view-public-entities"></a>Ortak varlıkları görüntüleme

Katalog API 'SI, yayımlanan varlıklar hakkındaki bilgileri doğrudan [AI Galerisi Web sitesinde](https://gallery.azure.ai/)görüntüleyebileceğiniz Azure yapay zeka Galerisi depolar. 

Yayınlanan varlıkları görüntülemek için aşağıdaki URL 'yi ziyaret edin ve yukarıdaki yazar kimliğini `[AuthorId]` [Al](#get-an-author-id) bölümünde elde edilen yazar kimliğiyle değiştirin.

`https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'`

Örnek:

`https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'`

### <a name="view-unlisted-and-public-entities"></a>Listelenmemiş ve ortak varlıkları görüntüleme

Bu sorgu yalnızca ortak varlıkları görüntüler. Listelenmemiş olanlar da dahil olmak üzere tüm varlıklarınızı görüntülemek için önceki bölümden elde edilen erişim belirtecini sağlayın.

1.    [Postman](https://www.getpostman.com)gibi bir araç kullanarak, [erişim belirtecinizi edinme](#get-your-access-token)bölümünde açıklandığı gibi Katalog URL 'sine bir http get isteği oluşturun.
2.    Erişim belirtecine ayarlanmış değer ile adlı bir HTTP istek üst bilgisi oluşturun `DataLabAccessToken` .
3.    HTTP isteğini gönderme.

> [!TIP]
> Listelenmemiş varlıklar katalog API 'sindeki yanıtları gösteriyorsa, kullanıcının geçersiz veya geçerliliği olmayan bir erişim belirteci olabilir. Azure Yapay Zeka Galerisi oturumunuzu kapatın ve ardından belirteci yenilemek için [erişim belirtecinizi alma](#get-your-access-token) bölümündeki adımları yineleyin. 
