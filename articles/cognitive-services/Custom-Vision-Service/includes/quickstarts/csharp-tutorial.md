---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 09/15/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: efbd5b455f825b0ce77da7d1e59876ca9b1facf5
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104803346"
---
.NET için Özel Görüntü İşleme istemci kitaplığı ile çalışmaya başlayın. Paketi yüklemek için bu adımları izleyin ve görüntü sınıflandırma modeli oluşturmak için örnek kodu deneyin. Bir proje oluşturacak, Etiketler ekleyecek, projeyi eğtireceksiniz ve projenin tahmin uç nokta URL 'sini programlı bir şekilde test etmek üzere kullanacaksınız. Bu örneği kendi görüntü tanıma uygulamanızı oluşturmak için bir şablon olarak kullanın.

> [!NOTE]
> Kod _yazmadan bir sınıflandırma modeli derlemek_ ve eğitemak istiyorsanız, bunun yerine [tarayıcı tabanlı kılavuza](../../getting-started-build-a-classifier.md) bakın.

.NET için Özel Görüntü İşleme istemci kitaplığı 'nı kullanın:

* Yeni bir Özel Görüntü İşleme projesi oluşturma
* Projeye Etiketler ekleyin
* Görüntüleri karşıya yükleme ve etiketleme
* Projeyi eğitme
* Geçerli yinelemeyi Yayımla
* Tahmin uç noktasını test etme

[Başvuru belgeleri](/dotnet/api/overview/azure/cognitiveservices/client/customvision) | Kitaplık kaynak kodu [(eğitim)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Training) [(tahmin)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Prediction) | Paket (NuGet) [(eğitim](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/) ) [(tahmin)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)  |  [örnekleri](/samples/browse/?products=azure&term=vision&terms=vision)


## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/)
* [Visual STUDIO IDE](https://visualstudio.microsoft.com/vs/) veya [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)'un geçerli sürümü.
* Azure aboneliğiniz olduktan sonra bir Özel Görüntü İşleme kaynak oluşturun ve bir <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title=" "  target="_blank"> </a> eğitim ve tahmin kaynağı oluşturmak ve anahtarlarınızı ve uç noktanızı almak için Azure Portal özel görüntü işleme bir kaynak oluşturun. Dağıtım için bekleyin ve **Kaynağa Git** düğmesine tıklayın.
    * Uygulamanızı Özel Görüntü İşleme bağlamak için oluşturduğunuz kaynaklarda anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
    * `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-c-application"></a>Yeni bir C# uygulaması oluşturma

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Visual Studio 'yu kullanarak yeni bir .NET Core uygulaması oluşturun. 

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler 

Yeni bir proje oluşturduktan sonra, **Çözüm Gezgini** proje çözümüne sağ tıklayıp **NuGet Paketlerini Yönet**' i seçerek istemci kitaplığını yükleyebilirsiniz. Açılan paket yöneticisinde **, Seç '** i seçin, **ön sürümü dahil** et ' i işaretleyin ve ve için arama yapın `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training` `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction` . En son sürümü seçin ve ardından öğesini **yükledikten** sonra. 

#### <a name="cli"></a>[CLI](#tab/cli)

Konsol penceresinde (cmd, PowerShell veya Bash gibi), `dotnet new` adıyla yeni bir konsol uygulaması oluşturmak için komutunu kullanın `custom-vision-quickstart` . Bu komut, tek bir kaynak dosyası olan basit bir "Merhaba Dünya" C# projesi oluşturur: *program. cs*. 

```console
dotnet new console -n custom-vision-quickstart
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

Uygulama dizini içinde, aşağıdaki komutla .NET için Özel Görüntü İşleme istemci Kitaplığı ' nı yüklemelisiniz:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training --version 2.0.0
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction --version 2.0.0
```

---

> [!TIP]
> Tüm hızlı başlangıç kodu dosyasını aynı anda görüntülemek mi istiyorsunuz? Bu hızlı başlangıçta kod örneklerini içeren [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs)'da bulabilirsiniz.

Proje dizininden *program. cs* dosyasını açın ve aşağıdaki `using` yönergeleri ekleyin:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_imports)]


Uygulamanın **ana** yönteminde, kaynağınızın anahtarı ve uç noktası için değişkenler oluşturun. Ayrıca, daha sonra kullanılacak bazı temel nesneleri de bildirebilirsiniz.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_creds)]

> [!IMPORTANT]
> Azure portala gidin. **Önkoşullar** bölümünde oluşturduğunuz özel görüntü işleme kaynakları başarıyla dağıtılırsa, **sonraki adımlar** altında **Kaynağa Git** düğmesine tıklayın. Anahtarlarınızı ve uç noktanızı kaynakların **anahtar ve uç nokta** sayfalarında bulabilirsiniz. Eğitim Kaynakları ' uç noktası ile birlikte hem eğitim ve tahmin Anahtarlarınızı almanız gerekir.
>
> Tahmin kaynak KIMLIĞI değerini, kaynağın **Özellikler** sekmesinde, **abonelik kimliği** olarak listelenmiş şekilde bulabilirsiniz.
> 
> İşiniz bittiğinde kodınızdan anahtarları kaldırmayı ve hiçbir zaman herkese açık bir şekilde göndermemeyi unutmayın. Üretim için, kimlik bilgilerinizi depolamak ve bunlara erişmek için güvenli bir yol kullanmayı düşünün. Daha fazla bilgi için bilişsel Hizmetler [güvenlik](../../../cognitive-services-security.md) makalesine bakın.

Uygulamanın **Main** yönteminde, bu hızlı başlangıçta kullanılan yöntemlere çağrılar ekleyin. Bunları daha sonra uygulayacaksınız.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_maincalls)]

## <a name="object-model"></a>Nesne modeli

|Ad|Açıklama|
|---|---|
|[CustomVisionTrainingClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Bu sınıf, modellerinizin oluşturulmasını, eğitimini ve yayımlanmasını işler. |
|[CustomVisionPredictionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Bu sınıf, görüntü sınıflandırması tahminleri için modellerinizin sorgulanmasını işler.|
|[PredictionModel](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.models.predictionmodel)| Bu sınıf tek bir görüntüde tek bir tahmin tanımlar. Nesne KIMLIĞI ve adı ve Güvenirlik puanı özelliklerini içerir.|

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, .NET için Özel Görüntü İşleme istemci kitaplığı ile aşağıdaki görevlerin nasıl yapılacağını gösterir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Yeni bir Özel Görüntü İşleme projesi oluşturma](#create-a-new-custom-vision-project)
* [Projeye Etiketler ekleyin](#add-tags-to-the-project)
* [Görüntüleri karşıya yükleme ve etiketleme](#upload-and-tag-images)
* [Projeyi eğitme](#train-the-project)
* [Geçerli yinelemeyi Yayımla](#publish-the-current-iteration)
* [Tahmin uç noktasını test etme](#test-the-prediction-endpoint)


## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

Yeni bir yöntemde, uç noktanızı ve anahtarlarınızı kullanarak eğitim ve tahmin istemcileri örneğini oluşturun.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Yeni bir Özel Görüntü İşleme projesi oluşturma

Bu sonraki kod, bir resim sınıflandırması projesi oluşturur. Oluşturulan proje, [özel görüntü işleme web sitesinde](https://customvision.ai/)görünür. Projenizi oluştururken diğer seçenekleri belirtmek için [CreateProject](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__&preserve-view=true) yöntemine bakın (sınıflandırıcı Web portalı [oluşturma](../../getting-started-build-a-classifier.md) kılavuzunda açıklanmıştır).  

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_create)]


## <a name="add-tags-to-the-project"></a>Projeye Etiketler ekleyin

Bu yöntem, modelin eğiteolacağı etiketleri tanımlar.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_addtags)]

## <a name="upload-and-tag-images"></a>Görüntüleri karşıya yükleme ve etiketleme

İlk olarak, bu proje için örnek görüntüleri indirin. [Örnek görüntüler klasörünün](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) içeriğini yerel cihazınıza kaydedin.

> [!NOTE]
> Öğreticinizi tamamlamaya yönelik daha geniş bir görüntü kümesine mi ihtiyacınız var? Microsoft garaj projesi olan Trove, eğitim amaçlarıyla görüntü kümeleri toplamanıza ve satın almanıza olanak tanır. Görüntülerinizi topladıktan sonra bunları indirebilir ve ardından Özel Görüntü İşleme projenize her zamanki şekilde aktarabilirsiniz. Daha fazla bilgi edinmek için [Trove sayfasını](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) ziyaret edin.

Ardından bu dizindeki görüntüleri karşıya yüklemek için bir yardımcı yöntem tanımlayın. Görüntülerinizin kaydedildiği konumu işaret etmek için **GetFiles** bağımsız değişkenini düzenlemeniz gerekebilir.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_loadimages)]

Daha sonra, resimleri karşıya yüklemek için bir yöntem tanımlayın, etiketleri klasör konumlarına göre uygulayın (görüntüler zaten sıralanmıştır). Resimleri tekrarlayarak veya bir toplu işte (toplu işlem başına 64 kadar) karşıya yükleyebilir ve etiketleyebilirsiniz. Bu kod parçacığı her ikisinin de örneklerini içerir. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_upload)]


## <a name="train-the-project"></a>Projeyi eğitme

Bu yöntem, projede ilk eğitim yinelemesini oluşturur. Eğitim tamamlanana kadar hizmeti sorgular.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_train)]

> [!TIP]
> Seçili etiketlerle eğitme
>
> İsteğe bağlı olarak, uygulanan etiketlerin yalnızca bir alt kümesini eğitebilirsiniz. Yalnızca belirli etiketlerden yeterince birini uygulamadıysanız bunu yapmak isteyebilirsiniz, ancak bu, başkalarının yeterli olması durumunda. [Trainproject](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.trainproject#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_TrainProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_Guid_System_String_System_Nullable_System_Int32__System_Nullable_System_Boolean__System_String_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_Models_TrainingParameters_&preserve-view=true) çağrısında *Traıningparameters* parametresini kullanın. Bir [Traıningparameters](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.models.trainingparameters) oluşturun ve **selectedtags** özelliğini kullanmak istediğiniz etiketlerin kimliklerini bir liste olarak ayarlayın. Model yalnızca listedeki etiketleri tanımak için eğitecektir.

## <a name="publish-the-current-iteration"></a>Geçerli yinelemeyi Yayımla

Bu yöntem, modelin geçerli yinelemesini sorgulama için kullanılabilir hale getirir. Tahmin istekleri göndermek için model adını bir başvuru olarak kullanabilirsiniz. İçin kendi değerini girmeniz gerekir `predictionResourceId` . Tahmin kaynak KIMLIĞINI, kaynağın **genel bakış** sekmesinde, **abonelik kimliği** olarak listelenen Azure Portal bulabilirsiniz.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Tahmin uç noktasını test etme

Komut dosyasının bu bölümü, test görüntüsünü yükler, model uç noktasını sorgular ve tahmin verilerinin konsola çıktısını verir.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_test)]


## <a name="run-the-application"></a>Uygulamayı çalıştırma

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

IDE penceresinin en üstündeki **Hata Ayıkla** düğmesine tıklayarak uygulamayı çalıştırın.

#### <a name="cli"></a>[CLI](#tab/cli)

Uygulamayı komut ile uygulama dizininizden çalıştırın `dotnet run` .

```dotnet
dotnet run
```

---

Uygulama çalışırken, bir konsol penceresi açıp aşağıdaki çıktıyı yazmalıdır:

```console
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

Ardından test görüntüsünün (**Images/Test/** yolunda bulunur) düzgün etiketlendiğini doğrulayabilirsiniz. Uygulamadan çıkmak için herhangi bir tuşa basın. Ayrıca [Özel Görüntü İşleme web sitesine](https://customvision.ai) geri dönebilir ve yeni oluşturulan projenizin geçerli durumunu görebilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Sonraki adımlar

Şimdi kodda görüntü sınıflandırma işleminin her adımını tamamladınız. Bu örnek tek bir eğitim yinelemesi yürütür, ancak genellikle modelinizi daha doğru hale getirmek için birden çok kez eğitmeniz ve test etmeniz gerekir.

> [!div class="nextstepaction"]
> [Modeli test etme ve yeniden eğitme](../../test-your-model.md)

* [Özel Görüntü İşleme nedir?](../../overview.md)
* Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs) 'da bulunabilir
* [SDK başvuru belgeleri](/dotnet/api/overview/azure/cognitiveservices/client/customvision)
