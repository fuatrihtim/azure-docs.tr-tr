---
title: 'Hızlı başlangıç: Python için Görüntü İşleme istemci kitaplığı'
description: Bu hızlı başlangıç ile Python için Görüntü İşleme istemci kitaplığı ile çalışmaya başlayın.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 1b17173c95f26c622a0021d30eb678fc27b60c2f
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103621936"
---
<a name="HOLTop"></a>

Görüntü İşleme istemci kitaplığını kullanarak şunları yapın:

* Etiketler, metin açıklaması, yüzeyler, yetişkinlere yönelik içerik ve daha fazlası için bir görüntüyü çözümleyin.
* Okuma API 'SI ile yazdırılmış ve el yazısı metin okuyun.

[Başvuru belgeleri](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)  |  [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-computervision)  |  [Paket (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-computervision/)  |  [Örnekler](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * Python yüklemeniz [PIP](https://pip.pypa.io/en/stable/)'yi içermelidir. Komut satırında komutunu çalıştırarak PIP 'nin yüklenip yüklenmediğini kontrol edebilirsiniz `pip --version` . Python 'un en son sürümünü yükleyerek PIP 'yi alın.
* Azure aboneliğiniz olduktan sonra, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" "  target="_blank"> </a> anahtarınızı ve uç noktanızı almak için Azure Portal bir Görüntü İşleme kaynağı oluşturun görüntü işleme bir kaynak oluşturun. Dağıtıldıktan sonra **Kaynağa Git ' e** tıklayın.

    * Uygulamanızı Görüntü İşleme hizmetine bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
    * `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler

İstemci kitaplığını ile yükleyebilirsiniz:

```console
pip install --upgrade azure-cognitiveservices-vision-computervision
```

Ayrıca, Pillow kitaplığı 'nı da yükler.

```console
pip install pillow
```

### <a name="create-a-new-python-application"></a>Yeni Python uygulaması oluşturma

> [!TIP]
> Tüm hızlı başlangıç kodu dosyasını aynı anda görüntülemek mi istiyorsunuz? Bu hızlı başlangıçta kod örneklerini içeren [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py)'da bulabilirsiniz.

Örneğin, yeni bir Python dosyası oluşturun &mdash; *QuickStart-File.py*. Ardından bunu tercih ettiğiniz düzenleyicide veya IDE 'de açın.

### <a name="find-the-subscription-key-and-endpoint"></a>Abonelik anahtarını ve uç noktayı bulma

Azure portala gidin. **Önkoşullar** bölümünde oluşturduğunuz görüntü işleme kaynak başarıyla dağıtılırsa, **sonraki adımlar** altında **Kaynağa Git** düğmesine tıklayın. Kaynak **yönetimi** altında, kaynağın **anahtar ve uç** nokta sayfasında abonelik anahtarınızı ve uç noktanızı bulabilirsiniz. 

Görüntü İşleme abonelik anahtarınız ve uç noktanız için değişkenler oluşturun. Abonelik anahtarınızı ve uç noktanızı, belirtilen yerde aşağıdaki koda yapıştırın. Görüntü İşleme uç noktanız formu vardır `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> İşiniz bittiğinde kodunuzda abonelik anahtarını kaldırmayı unutmayın ve hiçbir zaman herkese açık bir şekilde nakletmeyin. Üretim için, kimlik bilgilerinizi depolamak ve bunlara erişmek için güvenli bir yol kullanmayı düşünün. Örneğin, [Azure Anahtar Kasası](../../../../key-vault/general/overview.md).

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=set-up-client) [istemciyi ayarladım](?success=set-up-client#object-model)

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar ve arabirimler Görüntü İşleme Python SDK 'sının önemli özelliklerinden bazılarını işler.

|Ad|Açıklama|
|---|---|
|[ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin)| Bu sınıf, görüntü analizi, metin algılama ve küçük resim oluşturma gibi tüm görüntü işlemlerini doğrudan işler.|
| [ComputerVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient) | Bu sınıf tüm Görüntü İşleme işlevleri için gereklidir. Bunu Abonelik bilgileriniz ile birlikte başlatır ve diğer sınıfların örneklerini oluşturmak için kullanırsınız. **ComputerVisionClientOperationsMixin** uygular.|
|[VisualFeatureTypes](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| Bu Enum, standart bir çözümle işleminde yapılabilecek farklı görüntü analizi türlerini tanımlar. İhtiyaçlarınıza bağlı olarak bir **Visualfeaturetypes** değeri kümesi belirtirsiniz. |

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, Python için Görüntü İşleme istemci kitaplığı ile aşağıdaki görevlerin nasıl yapılacağını gösterir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Resim çözümleme](#analyze-an-image)
* [Yazdırılmış ve el yazısı metin oku](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

Uç noktanız ve anahtarınızla bir istemci örneği oluşturun. Anahtarınızla bir [Biliveservicescredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) nesnesi oluşturun ve bir [ComputerVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient) nesnesi oluşturmak için bunu uç noktanızla birlikte kullanın.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_client)]

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=authenticate-client) [istemciyi doğrulıyorum](?success=authenticate-client#analyze-an-image)

## <a name="analyze-an-image"></a>Resim çözümleme

Uzak bir görüntünün görsel özelliklerini çözümlemek için istemci nesneniz ' nı kullanın. Önce, çözümlemek istediğiniz bir görüntünün URL 'sine bir başvuru kaydedin.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_remoteimage)]

> [!TIP]
> Yerel bir görüntüyü de analiz edebilirsiniz. **Analyze_image_in_stream** gibi [ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin) yöntemlerine bakın. Ya da, yerel görüntüleri içeren senaryolar için [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py) 'daki örnek koda bakın.

### <a name="get-image-description"></a>Görüntü açıklamasını al

Aşağıdaki kod, görüntü için oluşturulan açıklamalı alt yazıların listesini alır. Daha fazla ayrıntı için bkz. [görüntüleri açıkla](../../concept-describing-images.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_describe)]

### <a name="get-image-category"></a>Görüntü kategorisini al

Aşağıdaki kod görüntünün algılanan kategorisini alır. Daha fazla ayrıntı için bkz. [görüntüleri kategorilere ayırma](../../concept-categorizing-images.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_categorize)]

### <a name="get-image-tags"></a>Görüntü etiketlerini al

Aşağıdaki kod görüntüde algılanan etiketlerin kümesini alır. Daha fazla ayrıntı için [içerik etiketlerine](../../concept-tagging-images.md) bakın.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_tags)]

### <a name="detect-objects"></a>Nesneleri Algıla

Aşağıdaki kod görüntüdeki ortak nesneleri algılar ve konsola yazdırır. Daha fazla ayrıntı için bkz. [nesne algılama](../../concept-object-detection.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_objects)]        

### <a name="detect-brands"></a>Markalar Algıla

Aşağıdaki kod görüntüde kurumsal markaların ve logoları algılar ve bunları konsola yazdırır. Daha ayrıntılı bilgi için bkz. [marka algılama](../../concept-brand-detection.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_brands)]

### <a name="detect-faces"></a>Yüz algılama

Aşağıdaki kod görüntüde dikdörtgen koordinatlarıyla algılanan yüzeyleri döndürür ve yüz niteliklerini seçer. Daha fazla ayrıntı için bkz. [yüz algılama](../../concept-detecting-faces.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Yetişkin, kcy veya Gori içeriğini algılama

Aşağıdaki kod görüntüde yetişkinlere yönelik içeriğin algılanan varlığını yazdırır. Daha fazla ayrıntı için bkz. [yetişkin, korcy, Gori içeriği](../../concept-detecting-adult-content.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Görüntü renk düzenini al

Aşağıdaki kod görüntüde, baskın renkler ve vurgu rengi gibi algılanan renk özniteliklerini yazdırır. Daha fazla ayrıntı için bkz. [renk şemaları](../../concept-detecting-color-schemes.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Etki alanına özgü içerik al

Görüntü İşleme, görüntüler üzerinde daha fazla analiz yapmak için özel model kullanabilir. Daha fazla ayrıntı için bkz. [etki alanına özgü içerik](../../concept-detecting-domain-content.md) . 

Aşağıdaki kod görüntüde algılanan ünlüler hakkında verileri ayrıştırır.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_celebs)]

Aşağıdaki kod görüntüde algılanan yer işaretleriyle ilgili verileri ayrıştırır.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Görüntü türünü al

Aşağıdaki kod, &mdash; küçük resim veya çizgi çizme gibi görüntü türü hakkında bilgi yazdırır.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_type)]

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=analyze-image) [bir görüntüyü çözümleytim](?success=analyze-image#read-printed-and-handwritten-text)

## <a name="read-printed-and-handwritten-text"></a>Yazdırılmış ve el yazısı metin oku

Görüntü İşleme görüntüdeki görünür metni okuyabilir ve bunu bir karakter akışına dönüştürebilir. Bunu iki bölümden yapabilirsiniz.

### <a name="call-the-read-api"></a>Okuma API 'sini çağırma

İlk olarak, verilen görüntü için **Read** yöntemini çağırmak üzere aşağıdaki kodu kullanın. Bu işlem KIMLIĞI döndürür ve görüntünün içeriğini okumak için zaman uyumsuz bir işlem başlatır.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_call)]

> [!TIP]
> Ayrıca, yerel görüntüden metin okuyabilirsiniz. **Read_in_stream** gibi [ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin) yöntemlerine bakın. Ya da, yerel görüntüleri içeren senaryolar için [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py) 'daki örnek koda bakın.

### <a name="get-read-results"></a>Okuma sonuçları al

Ardından, **okuma** çağrısından döndürülen işlem kimliğini alın ve bunu işlem sonuçları için hizmeti sorgulamak üzere kullanın. Aşağıdaki kod, sonuçlar döndürülünceye kadar işlemi tek saniyelik aralıklarla denetler. Daha sonra ayıklanan metin verilerini konsola yazdırır.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_response)]

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=read-printed-handwritten-text) [metni okudum](?success=read-printed-handwritten-text#run-the-application)

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı `python` hızlı başlangıç dosyanızdaki komutla çalıştırın.

```console
python quickstart-file.py
```

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=run-the-application) [uygulamayı çalıştırdım](?success=run-the-application#clean-up-resources)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler aboneliğini temizlemek ve kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=clean-up-resources) [kaynakları temizm](?success=clean-up-resources#next-steps)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Python için Görüntü İşleme kitaplığı 'nı kullanarak temel görevleri nasıl kullanacağınızı öğrendiniz. Daha sonra, kitaplık hakkında daha fazla bilgi edinmek için başvuru belgelerini inceleyin.

> [!div class="nextstepaction"]
>[Görüntü İşleme API'si Başvurusu (Python)](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)

* [Görüntü İşleme nedir?](../../overview.md)
* Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py)' da bulunabilir.
