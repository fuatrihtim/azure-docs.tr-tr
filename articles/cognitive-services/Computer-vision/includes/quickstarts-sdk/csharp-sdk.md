---
title: 'Hızlı başlangıç: .NET için Görüntü İşleme istemci kitaplığı'
description: Bu hızlı başlangıçta, .NET için Görüntü İşleme istemci kitaplığı ile çalışmaya başlayın.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 0509ba61e21fa38daf1747124000c8d1270cc4db
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622105"
---
<a name="HOLTop"></a>

Görüntü İşleme istemci kitaplığını kullanarak şunları yapın:

* Etiketler, metin açıklaması, yüzeyler, yetişkinlere yönelik içerik ve daha fazlası için bir görüntüyü çözümleyin.
* Okuma API 'SI ile yazdırılmış ve el yazısı metin okuyun.

[Başvuru belgeleri](/dotnet/api/overview/azure/cognitiveservices/client/computervision)  |  [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision)  |  [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/)  |  [Örnekler](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/)
* [Visual STUDIO IDE](https://visualstudio.microsoft.com/vs/) veya [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)'un geçerli sürümü.
* Azure aboneliğiniz olduktan sonra, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" "  target="_blank"> </a> anahtarınızı ve uç noktanızı almak için Azure Portal bir Görüntü İşleme kaynağı oluşturun görüntü işleme bir kaynak oluşturun. Dağıtıldıktan sonra **Kaynağa Git ' e** tıklayın.
    * Uygulamanızı Görüntü İşleme hizmetine bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
    * `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-c-application"></a>Yeni bir C# uygulaması oluşturma

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Visual Studio 'yu kullanarak yeni bir .NET Core uygulaması oluşturun. 

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler 

Yeni bir proje oluşturduktan sonra, **Çözüm Gezgini** proje çözümüne sağ tıklayıp **NuGet Paketlerini Yönet**' i seçerek istemci kitaplığını yükleyebilirsiniz. Açılan paket yöneticisinde, Seç ' i seçin, **ön sürümü dahil** **et ' i** işaretleyin ve arama yapın `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` . Sürüm `6.0.0-preview.1` ' ü ve ardından **öğesini seçin**. 

#### <a name="cli"></a>[CLI](#tab/cli)

Konsol penceresinde (cmd, PowerShell veya Bash gibi), `dotnet new` adıyla yeni bir konsol uygulaması oluşturmak için komutunu kullanın `computer-vision-quickstart` . Bu komut, tek bir kaynak dosyası olan basit bir "Merhaba Dünya" C# projesi oluşturur: *program.cs*.

```console
dotnet new console -n computer-vision-quickstart
```

Dizininizi yeni oluşturulan uygulama klasörüyle değiştirin. Uygulamayı ile oluşturabilirsiniz:

```console
dotnet build
```

Derleme çıktısı hiçbir uyarı veya hata içermemelidir. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler

Uygulama dizini içinde, aşağıdaki komutla .NET için Görüntü İşleme istemci Kitaplığı ' nı yüklemelisiniz:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0
```

---

> [!TIP]
> Tüm hızlı başlangıç kodu dosyasını aynı anda görüntülemek mi istiyorsunuz? Bu hızlı başlangıçta kod örneklerini içeren [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs)'da bulabilirsiniz.

Proje dizininden, *program.cs* dosyasını tercih ettiğiniz DÜZENLEYICIDE veya IDE 'de açın.

### <a name="find-the-subscription-key-and-endpoint"></a>Abonelik anahtarını ve uç noktayı bulma

Azure portala gidin. **Önkoşullar** bölümünde oluşturduğunuz görüntü işleme kaynak başarıyla dağıtılırsa, **sonraki adımlar** altında **Kaynağa Git** düğmesine tıklayın. Kaynak **yönetimi** altında, kaynağın **anahtar ve uç** nokta sayfasında abonelik anahtarınızı ve uç noktanızı bulabilirsiniz. 

Uygulamanın **Program** sınıfında görüntü işleme abonelik anahtarınız ve uç noktanız için değişkenler oluşturun. Abonelik anahtarınızı ve uç noktanızı, belirtilen yerde aşağıdaki koda yapıştırın. Görüntü İşleme uç noktanız formu vardır `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using_and_vars)]

> [!IMPORTANT]
> İşiniz bittiğinde kodunuzda abonelik anahtarını kaldırmayı unutmayın ve hiçbir zaman herkese açık bir şekilde nakletmeyin. Üretim için, kimlik bilgilerinizi depolamak ve bunlara erişmek için güvenli bir yol kullanmayı düşünün. Örneğin, [Azure Anahtar Kasası](../../../../key-vault/general/overview.md).

Uygulamanın `Main` yönteminde, bu hızlı başlangıçta kullanılan yöntemlere çağrılar ekleyin. Bunları daha sonra oluşturacaksınız.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyzeinmain)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extracttextinmain)]

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=set-up-client) [istemciyi ayarladım](?success=set-up-client#object-model)

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar ve arabirimler, Görüntü İşleme .NET SDK 'sının bazı önemli özelliklerini işler.

|Ad|Açıklama|
|---|---|
| [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | Bu sınıf tüm Görüntü İşleme işlevleri için gereklidir. Bunu Abonelik bilgileriniz ile birlikte başlatır ve birçok görüntü işlemini yapmak için kullanırsınız.|
|[ComputerVisionClientExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions)| Bu sınıf, **ComputerVisionClient** için ek yöntemler içerir.|
|[VisualFeatureTypes](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| Bu Enum, standart bir çözümle işleminde yapılabilecek farklı görüntü analizi türlerini tanımlar. İhtiyaçlarınıza bağlı olarak bir VisualFeatureTypes değeri kümesi belirtirsiniz. |

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, .NET için Görüntü İşleme istemci kitaplığı ile aşağıdaki görevlerin nasıl yapılacağını gösterir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Resim çözümleme](#analyze-an-image)
* [Yazdırılmış ve el yazısı metin oku](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

**Program** sınıfındaki yeni bir yöntemde, uç nokta ve abonelik anahtarınızı içeren bir istemci örneği oluşturun. Abonelik anahtarınızla bir **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials)** nesnesi oluşturun ve bir **[ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient)** nesnesi oluşturmak için bunu uç noktanızla birlikte kullanın.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=authenticate-client) [istemciyi doğrulıyorum](?success=authenticate-client#analyze-an-image)

## <a name="analyze-an-image"></a>Resim çözümleme

Aşağıdaki kod, `AnalyzeImageUrl` uzak bir görüntüyü çözümlemek ve sonuçları yazdırmak için istemci nesnesini kullanan yöntemini tanımlar. Yöntemi bir metin açıklaması, kategori, etiket listesi, algılanan yüzeyler, yetişkinlere yönelik içerik bayrakları, ana renkler ve görüntü türü döndürür.

> [!TIP]
> Yerel bir görüntüyü de analiz edebilirsiniz. Bkz. **analiz Zeımageınstreamasync** gibi [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) yöntemleri. Ya da, yerel görüntüleri içeren senaryolar için [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) 'daki örnek koda bakın.

### <a name="set-up-test-image"></a>Test görüntüsünü ayarla

**Program** sınıfınıza bir başvuruyu, çözümlemek ISTEDIĞINIZ görüntünün URL 'sine kaydedin.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_url)]

### <a name="specify-visual-features"></a>Görsel özellikleri belirtin

Görüntü analizi için yeni yönteminizi tanımlayın. Analizinizden ayıklamak istediğiniz görsel özellikleri belirten aşağıdaki kodu ekleyin. Listenin tamamı için **[Visualfeaturetypes](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)** numaralandırması bölümüne bakın.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_visualfeatures)]

Özelliklerini uygulamak için, aşağıdaki kod bloklarını **analiz Zeımageurl** yöntemine ekleyin. Sonuna bir kapanış ayracı eklemeyi unutmayın.

```csharp
}
```

### <a name="analyze"></a>Analiz

Analysis **Zeımageasync** yöntemi, ayıklanan tüm bilgileri Içeren bir **ımageanalysis** nesnesi döndürür.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_call)]

Aşağıdaki bölümlerde bu bilgilerin ayrıntılı olarak nasıl ayrıştırılacak gösterilmektedir.

### <a name="get-image-description"></a>Görüntü açıklamasını al

Aşağıdaki kod, görüntü için oluşturulan açıklamalı alt yazıların listesini alır. Daha fazla ayrıntı için bkz. [görüntüleri açıkla](../../concept-describing-images.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_describe)]

### <a name="get-image-category"></a>Görüntü kategorisini al

Aşağıdaki kod görüntünün algılanan kategorisini alır. Daha fazla ayrıntı için bkz. [görüntüleri kategorilere ayırma](../../concept-categorizing-images.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_categorize)]

### <a name="get-image-tags"></a>Görüntü etiketlerini al

Aşağıdaki kod görüntüde algılanan etiketlerin kümesini alır. Daha fazla ayrıntı için [içerik etiketlerine](../../concept-tagging-images.md) bakın.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_tags)]

### <a name="detect-objects"></a>Nesneleri Algıla

Aşağıdaki kod görüntüdeki ortak nesneleri algılar ve konsola yazdırır. Daha fazla ayrıntı için bkz. [nesne algılama](../../concept-object-detection.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_objects)]

### <a name="detect-brands"></a>Markalar Algıla

Aşağıdaki kod görüntüde kurumsal markaların ve logoları algılar ve bunları konsola yazdırır. Daha ayrıntılı bilgi için bkz. [marka algılama](../../concept-brand-detection.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_brands)]

### <a name="detect-faces"></a>Yüz algılama

Aşağıdaki kod görüntüde dikdörtgen koordinatlarıyla algılanan yüzeyleri döndürür ve yüz niteliklerini seçer. Daha fazla ayrıntı için bkz. [yüz algılama](../../concept-detecting-faces.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Yetişkin, kcy veya Gori içeriğini algılama

Aşağıdaki kod görüntüde yetişkinlere yönelik içeriğin algılanan varlığını yazdırır. Daha fazla ayrıntı için bkz. [yetişkin, korcy, Gori içeriği](../../concept-detecting-adult-content.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Görüntü renk düzenini al

Aşağıdaki kod görüntüde, baskın renkler ve vurgu rengi gibi algılanan renk özniteliklerini yazdırır. Daha fazla ayrıntı için bkz. [renk şemaları](../../concept-detecting-color-schemes.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Etki alanına özgü içerik al

Görüntü İşleme, görüntülerde daha fazla analiz yapmak için özel modeller kullanabilir. Daha fazla ayrıntı için bkz. [etki alanına özgü içerik](../../concept-detecting-domain-content.md) . 

Aşağıdaki kod görüntüde algılanan ünlüler hakkında verileri ayrıştırır.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_celebs)]

Aşağıdaki kod görüntüde algılanan yer işaretleriyle ilgili verileri ayrıştırır.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Görüntü türünü al

Aşağıdaki kod, &mdash; küçük resim veya çizgi çizme gibi görüntü türü hakkında bilgi yazdırır.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_type)]

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=analyze-image) [bir görüntüyü çözümleytim](?success=analyze-image#read-printed-and-handwritten-text)

## <a name="read-printed-and-handwritten-text"></a>Yazdırılmış ve el yazısı metin oku

Görüntü İşleme görüntüdeki görünür metni okuyabilir ve bunu bir karakter akışına dönüştürebilir. Metin tanıma hakkında daha fazla bilgi için bkz. [optik karakter tanıma (OCR)](../../concept-recognizing-text.md#read-api) kavramsal belgesi. Bu bölümdeki kod, [okuma 3,0 için en son görüntü işleme SDK sürümünü](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/6.0.0-preview.1) kullanır ve bir yöntemi tanımlar, bu, `BatchReadFileUrl` görüntüdeki metni algılamak ve ayıklamak için istemci nesnesini kullanır.

> [!TIP]
> Ayrıca, yerel görüntüden metin de ayıklayabilirsiniz. **Readınstreamasync** gibi [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) yöntemlerine bakın. Ya da, yerel görüntüleri içeren senaryolar için [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) 'daki örnek koda bakın.

### <a name="set-up-test-image"></a>Test görüntüsünü ayarla

**Program** sınıfınıza, metin ayıklamak ISTEDIĞINIZ görüntünün URL 'sine bir başvuru kaydedin. Bu kod parçacığı hem yazdırılmış hem de el yazısı metin için örnek görüntüler içerir.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readtext_url)]

### <a name="call-the-read-api"></a>Okuma API 'sini çağırma

Metni okumak için yeni bir yöntem tanımlayın. Verilen görüntü için **ReadAsync** yöntemini çağıran aşağıdaki kodu ekleyin. Bu işlem KIMLIĞI döndürür ve görüntünün içeriğini okumak için zaman uyumsuz bir işlem başlatır.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_1)]

### <a name="get-read-results"></a>Okuma sonuçları al

Ardından, **ReadAsync** çağrısından döndürülen işlem kimliğini alın ve bunu işlem sonuçları için hizmeti sorgulamak üzere kullanın. Aşağıdaki kod, sonuçlar döndürülünceye kadar işlemi denetler. Daha sonra ayıklanan metin verilerini konsola yazdırır.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_2)]

### <a name="display-read-results"></a>Okuma sonuçlarını görüntüle

Alınan metin verilerini ayrıştırmak ve göstermek için aşağıdaki kodu ekleyin ve yöntem tanımını sona erdirin.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_3)]

## <a name="run-the-application"></a>Uygulamayı çalıştırma

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

IDE penceresinin en üstündeki **Hata Ayıkla** düğmesine tıklayarak uygulamayı çalıştırın.

#### <a name="cli"></a>[CLI](#tab/cli)

Uygulamayı komut ile uygulama dizininizden çalıştırın `dotnet run` .

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler aboneliğini temizlemek ve kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
>[Görüntü İşleme API'si Başvurusu (.NET)](/dotnet/api/overview/azure/cognitiveservices/client/computervision)

* [Görüntü İşleme nedir?](../../overview.md)
* Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs)' da bulunabilir.
