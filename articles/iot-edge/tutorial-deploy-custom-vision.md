---
title: Öğretici-Azure IoT Edge kullanarak bir cihaza Özel Görüntü İşleme Sınıflandırıcısı dağıtma
description: Bu öğreticide, Özel Görüntü İşleme ve IoT Edge kullanarak bir bilgisayar vizyonu modelinin kapsayıcı olarak nasıl çalıştırılacağını öğrenin.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 8da5c24265a43eaaf1ecfe80649b6f4fea588b3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103464080"
---
# <a name="tutorial-perform-image-classification-at-the-edge-with-custom-vision-service"></a>Öğretici: Özel Görüntü İşleme Hizmeti ile uçta görüntü sınıflandırması gerçekleştirme

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge, iş yüklerini buluttan uca taşıyarak IoT çözümünüzü daha verimli hale getirmenizi sağlayabilir. Bu özellik, özel görüntü işleme modelleri gibi çok miktarda veri işleyen hizmetler için uygundur. [Özel Görüntü İşleme Hizmeti](../cognitive-services/custom-vision-service/overview.md), özel görüntü sınıflandırıcıları derlemenizi ve bunları cihazlara kapsayıcı olarak dağıtmanızı sağlar. Bu iki hizmet birlikte tüm verileri aktarmak zorunda kalmadan görüntülerden veya video akışlarından içgörü elde etmenizi sağlar. Özel Görüntü İşleme Hizmeti, içgörü oluşturmak için bir görüntüyü eğitilmiş modelle karşılaştıran bir sınıflandırıcı sunar.

Örneğin bir IoT Edge cihazı üzerinde çalışan Özel Görüntü İşleme Hizmeti bir otoyoldaki trafik yoğunluğunun beklenenden daha yüksek veya düşük olduğunu veya bir otoparkta yeterli yer bulunup bulunmadığını belirleyebilir. Bu içgörüler eylem gerçekleştirmek üzere başka bir hizmetle paylaşılabilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Özel Görüntü İşleme Hizmeti ile bir görüntü sınıflandırıcı derleme.
> * Cihazınızdaki Özel Görüntü İşleme Hizmeti web sunucusunu sorgulayan bir IoT Edge modülü dağıtma.
> * Görüntü sınıflandırıcı sonuçlarını IoT Hub'a gönderme.

<center>

![Diyagram-öğretici mimarisi, aşama ve dağıtım Sınıflandırıcısı](./media/tutorial-deploy-custom-vision/custom-vision-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

>[!TIP]
>Bu öğretici, [bir Raspberry PI 3 örnek projesinde özel görüntü işleme ve Azure IoT Edge](https://github.com/Azure-Samples/custom-vision-service-iot-edge-raspberry-pi) basitleştirilmiş bir sürümüdür. Bu öğretici bir bulut VM 'de çalışacak şekilde tasarlandı ve görüntü sınıflandırıcısını eğitme ve test etmek için statik görüntüler kullanır. Bu, IoT Edge Özel Görüntü İşleme değerlendirmek için yalnızca bir kullanıcı için faydalıdır. Örnek proje fiziksel donanım kullanır ve görüntü sınıflandırıcısını eğitmek ve test etmek için, daha ayrıntılı, gerçek yaşam senaryosu denemek isteyen bir kişi için kullanışlıdır.

Bu öğreticiye başlamadan önce, bir önceki öğreticiden yararlanarak Linux kapsayıcı geliştirmesi için ortamınızı ayarlamanız gerekir: [Linux kapsayıcıları kullanarak IoT Edge modülleri geliştirme](tutorial-develop-for-linux.md). Bu öğreticiyi tamamlayarak aşağıdaki önkoşulların yerine gelmelidir:

* Azure'da ücretsiz veya standart katman [IoT Hub'ı](../iot-hub/iot-hub-create-through-portal.md).
* Azure IoT Edge çalıştıran bir cihaz. Hızlı başlangıçlarını bir [Linux cihazı](quickstart-linux.md) veya [Windows cihazı](quickstart.md)ayarlamak için kullanabilirsiniz.
* [Azure Container Registry](../container-registry/index.yml)gibi bir kapsayıcı kayıt defteri.
* [Azure IoT araçlarıyla](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)yapılandırılmış [Visual Studio Code](https://code.visualstudio.com/) .
* Linux kapsayıcılarını çalıştırmak için yapılandırılmış [Docker CE](https://docs.docker.com/install/) .

Özel Görüntü İşleme hizmetiyle IoT Edge bir modül geliştirmek için aşağıdaki ek önkoşulları geliştirme makinenize yüklersiniz:

* [Python](https://www.python.org/downloads/)
* [Git](https://git-scm.com/downloads)
* [Visual Studio Code için Python uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

## <a name="build-an-image-classifier-with-custom-vision"></a>Özel Görüntü İşleme Hizmeti ile bir görüntü sınıflandırıcı derleme

Görüntü sınıflandırıcı derlemek için bir Özel Görüntü İşleme Hizmeti projesi oluşturmanız ve eğitim amaçlı görüntü sağlamanız gerekir. Bu bölümde gerçekleştireceğiniz adımlar hakkında daha fazla bilgi için bkz. [Özel Görüntü İşleme Hizmeti ile sınıflandırıcı derleme](../cognitive-services/custom-vision-service/getting-started-build-a-classifier.md).

Görüntü sınıflandırıcıyı derleyip eğittikten sonra Docker kapsayıcısı olarak dışarı aktarabilir ve bir IoT Edge cihazına dağıtabilirsiniz.

### <a name="create-a-new-project"></a>Yeni proje oluşturma

1. Web tarayıcınızda [Özel Görüntü İşleme Hizmeti web sayfasına](https://customvision.ai/) gidin.

2. **Sign in** (Oturum aç) öğesini seçin ve Azure kaynaklarına erişmek için kullandığınız hesapla oturum açın.

3. **Yeni proje**'yi seçin.

4. Aşağıdaki değerlerle bir proje oluşturun:

   | Alan | Değer |
   | ----- | ----- |
   | Ad | Projeniz için bir ad belirleyin, örneğin: **EdgeTreeClassifier**. |
   | Description | İsteğe bağlı proje açıklaması. |
   | Kaynak | Özel Görüntü İşleme Hizmeti kaynağı içeren Azure Kaynak gruplarınızdan birini seçin veya henüz bir tane eklemediyseniz yeni bir tane **oluşturun** . |
   | Proje Türleri | **Sınıflandırma** |
   | Sınıflandırma Türleri | **Birden çok Sınıf (görüntü başına tek etiket)** |
   | Etki Alanları | **General (compact)** (Genel (kompakt)) |
   | Verme özellikleri | **Temel platformlar (TensorFlow, CoreML, ONNX,...)** |

5. **Create project** (Proje oluştur) öğesini seçin.

### <a name="upload-images-and-train-your-classifier"></a>Görüntüleri karşıya yükleme ve sınıflandırıcınızı eğitme

Görüntü sınıflandırıcı oluşturmak için eğitim görüntülerine ve test görüntülerine ihtiyacınız vardır.

1. [Cognitive-CustomVision-Windows](https://github.com/Microsoft/Cognitive-CustomVision-Windows) deposundaki örnek görüntüleri yerel geliştirme makinenize kopyalayın veya indirin.

   ```cmd/sh
   git clone https://github.com/Microsoft/Cognitive-CustomVision-Windows.git
   ```

2. Özel Görüntü İşleme Hizmeti projenize dönün ve **Add images** (Görüntü ekle) öğesini seçin.

3. Kopyaladığınız yerel git deposuna gidin ve ilk görüntü klasörünü açın: **Cognitive-CustomVision-Windows / Samples / Images / Hemlock**. Klasördeki 10 görüntüyü ve ardından **Open** (Aç) öğesini seçin.

4. Bu görüntü grubuna **hemlock** etiketini ekleyin ve **Enter** tuşuna basarak etiketi uygulayın.

5. **Upload 10 files** (10 dosyayı karşıya yükle) öğesini seçin.

   ![Hemlock etiketli dosyaları karşıya yükleme Özel Görüntü İşleme](./media/tutorial-deploy-custom-vision/upload-hemlock.png)

6. Görüntüler başarıyla karşıya yüklendikten sonra **Done** (Bitti) öğesini seçin.

7. Yeniden **Add images** (Görüntü ekle) öğesini seçin.

8. İkinci görüntü klasörüne göz atın: **Cognitive-CustomVision-Windows / Samples / Images / Japanese Cherry**. Klasördeki 10 görüntüyü ve ardından **Open** (Aç) öğesini seçin.

9. Bu görüntü grubuna **japanese cherry** etiketini ekleyin ve **Enter** tuşuna basarak etiketi uygulayın.

10. **Upload 10 files** (10 dosyayı karşıya yükle) öğesini seçin. Görüntüler başarıyla karşıya yüklendikten sonra **Done** (Bitti) öğesini seçin.

11. İki görüntü kümesini de etiketleyip yükledikten sonra sınıflandırıcıyı eğitmek için **Train** (Eğit) öğesini seçin.

### <a name="export-your-classifier"></a>Sınıflandırıcınızı dışarı aktarma

1. Sınıflandırıcınızı eğittikten sonra sınıflandırıcının Performance (Performans) sayfasında **Export** (Dışarı aktar) öğesini seçin.

   ![Eğitilen görüntü sınıflandırıcınızı dışa aktarma](./media/tutorial-deploy-custom-vision/export.png)

2. Platform için **DockerFile** girişini seçin. 

3. Sürüm olarak **Linux** seçin.  

4. **Export** (Dışarı aktar) öğesini seçin. 

   ![Linux kapsayıcılarla DockerFile olarak dışarı aktarma](./media/tutorial-deploy-custom-vision/export-2.png)

5. Dışarı aktarma işlemi tamamlandıktan sonra **Download** (İndir) öğesini seçin ve .zip paketini bilgisayarınıza kaydedin. Paketteki tüm dosyaları ayıklayın. Bu dosyaları görüntü sınıflandırma sunucusunu içeren bir IoT Edge modülü oluşturmak için kullanacaksınız. 

Özel Görüntü İşleme Hizmeti projenizi oluşturma ve eğitme adımlarını tamamladınız. Dışarı aktarılan dosyaları bir sonraki bölümde kullanacaksınız ancak Özel Görüntü İşleme Hizmeti web sayfasıyla işiniz bitti. 

## <a name="create-an-iot-edge-solution"></a>IoT Edge çözümü oluşturma

Artık geliştirme makinenizde görüntü sınıflandırıcınızın kapsayıcı sürümü için gerekli dosyalara sahipsiniz. Bu bölümde görüntü sınıflandırıcı kapsayıcısını IoT Edge modülü olarak çalıştıracak şekilde yapılandıracaksınız. Ayrıca görüntü sınıflandırıcı ile birlikte dağıtılacak ikinci bir modül de oluşturacaksınız. İkinci modül istekleri sınıflandırıcıya, sonuçları da ileti olarak IoT Hub'a gönderir. 

### <a name="create-a-new-solution"></a>Yeni çözüm oluşturma

Çözüm, tek bir IoT Edge dağıtımı için birden fazla modül geliştirmek ve düzenlemek için kullanabileceğiniz mantıksal bir yoldur. Bir çözümde bir veya daha fazla modülün kodunun yanı sıra bunların bir IoT Edge cihazında nasıl yapılandırılacağını bildiren dağıtım bildirimi bulunur. 

1. Visual Studio Code,   >  vs Code komut paletini açmak için **komut paletini** görüntüle ' yi seçin. 

1. Komut paletinde **Azure IoT Edge: New IoT Edge solution** komutunu girin ve çalıştırın. Komut paletinde çözümünüzü oluşturmak için aşağıdaki bilgileri girin: 

   | Alan | Değer |
   | ----- | ----- |
   | Klasör seçin | Geliştirme makinenizde VS Code'un çözüm dosyalarını oluşturmak için kullanacağı konumu seçin. |
   | Çözüm adı sağlayın | Çözümünüz için **CustomVisionSolution** gibi açıklayıcı bir ad girin veya varsayılan değeri kabul edin. |
   | Modül şablonunu seçin | **Python Modülü**'nü seçin. |
   | Modül adı sağlayın | Modülünüze **classifier** adını verin.<br><br>Modül adının küçük harfli olması önemlidir. IoT Edge, modüllere başvururken büyük/küçük harfe duyarlıdır ve bu çözümde tüm istekleri küçük harf ile biçimlendiren bir kitaplık kullanılmaktadır. |
   | Modül için Docker görüntü deposunu sağlama | Görüntü deposu, kapsayıcı kayıt defterinizin adını ve kapsayıcı görüntünüzün adını içerir. Kapsayıcı görüntünüz bir önceki adımdaki değerle önceden doldurulur. **Localhost: 5000** ' i Azure Container kayıt defterinizin **oturum açma sunucusu** değeriyle değiştirin. Oturum açma sunucusunu Azure portal kapsayıcı kayıt defterinizin genel bakış sayfasından alabilirsiniz.<br><br>Son dize **\<registry name\> . azurecr.io/Classifier** gibi görünür. |
 
   ![Docker görüntü deposunu sağlama](./media/tutorial-deploy-custom-vision/repository.png)

Ardından Visual Studio Code penceresi IoT Edge çözümü çalışma alanınızı yükler.

### <a name="add-your-registry-credentials"></a>Kayıt defteri kimlik bilgilerinizi ekleme

Ortam dosyası, kapsayıcı kayıt defterinizin kimlik bilgilerini depolar ve bu bilgileri IoT Edge çalışma zamanı ile paylaşır. Çalışma zamanı, özel görüntülerinizi IoT Edge cihazına çekmek için bu kimlik bilgilerine ihtiyaç duyar.

IoT Edge uzantısı, Azure 'dan kapsayıcı kayıt defteri kimlik bilgilerinizi çekmeye ve ortam dosyasına doldurmaya çalışır. Kimlik bilgilerinizin zaten eklenmiş olup olmadığını denetleyin. Yoksa, şimdi ekleyin:

1. VS Code gezgininde .env dosyasını açın.
2. Alanları Azure kapsayıcı kayıt defterinizden kopyaladığınız **kullanıcı adı** ve **parola** değerleriyle güncelleştirin.
3. Bu dosyayı kaydedin.

### <a name="select-your-target-architecture"></a>Hedef mimarinizi seçin

Şu anda Visual Studio Code Linux AMD64 ve Linux ARM32v7 cihazları için modüller geliştirebilir. Kapsayıcı oluşturulup her mimari türü için farklı çalıştığından, her çözümle hedeflediğiniz mimariyi seçmeniz gerekir. Bu öğretici için kullanacağımız Linux AMD64 varsayılandır. 

1. Komut paleti ' ni açın ve Azure IoT Edge için arama yapın **: Edge çözümü Için varsayılan hedef platformunu ayarla** veya pencerenin altındaki yan çubukta kısayol simgesini seçin. 

2. Komut paletinde, seçenekler listesinden hedef mimariyi seçin. Bu öğreticide, IoT Edge cihaz olarak bir Ubuntu sanal makinesi kullanıyoruz, bu nedenle varsayılan **AMD64**'yi tutacağız. 

### <a name="add-your-image-classifier"></a>Görüntü sınıflandırıcınızı ekleme

Visual Studio Code uygulamasındaki Python modülü şablonu, IoT Edge örneğinizi test etmek için çalıştırabileceğiniz örnek kod içerir. Bu senaryoda bu kodu kullanmayacaksınız. Bunun yerine bu bölümdeki adımları kullanarak örnek kodu daha önceden dışarı aktardığınız görüntü sınıflandırıcı kapsayıcısıyla değiştirin. 

1. Dosya gezgininizde indirip ayıkladığınız Özel Görüntü İşleme Hizmeti paketine göz atın. Ayıklanan paketin içeriğin tamamını kopyalayın. **app** ve **azureml** adlı iki klasörün yanı sıra **Dockerfile** ve **README** adlı iki dosyanın bulunması gerekir. 

2. Dosya gezgininde Visual Studio Code'un IoT Edge çözümünüzü oluşturmasını istediğiniz dizine gidin. 

3. Sınıflandırıcı modülü klasörünü açın. Önceki bölümde önerilen adları kullandıysanız klasör yapısı şu şekilde olacaktır: **CustomVisionSolution / modules / classifier**. 

4. Dosyaları **classifier** klasörüne yapıştırın. 

5. Visual Studio Code penceresine dönün. Çözüm çalışma alanınızda görüntü sınıflandırıcı dosyaları modül klasöründe görünmelidir. 

   ![Görüntü sınıflandırıcı dosyalarını gösteren çözüm çalışma alanı](./media/tutorial-deploy-custom-vision/workspace.png)

6. Sınıflandırıcı klasöründeki **module.json** dosyasını açın. 

7. **Platformlar** parametresini, eklediğiniz yeni Dockerfile 'ı işaret etmek üzere güncelleştirin ve bu öğretici için kullandığımız tek MIMARIDE AMD64 'in yanı sıra tüm seçenekleri kaldırın. 

   ```json
   "platforms": {
       "amd64": "./Dockerfile"
   }
   ```

8. Yaptığınız değişiklikleri kaydedin. 

### <a name="create-a-simulated-camera-module"></a>Sanal kamera modülü oluşturma

Gerçek bir Özel Görüntü İşleme Hizmeti dağıtımında canlı görüntü veya video akışı sağlayan bir kameranız olacaktır. Bu senaryo için görüntü sınıflandırıcıya test görüntüsü gönderen bir modül oluşturarak kamera simülasyonu yapabilirsiniz. 

#### <a name="add-and-configure-a-new-module"></a>Yeni modül ekleme ve yapılandırma

Bu bölümde aynı CustomVisionSolution öğesine yeni bir modül ekleyecek ve sanal kamerayı oluşturmak için gerekli kodu sağlayacaksınız. 

1. Aynı Visual Studio Code penceresinde komut paletini kullanarak **Azure IoT Edge: Add IoT Edge Module** komutunu çalıştırın. Komut paletinde yeni modülünüz için aşağıdaki bilgileri girin: 

   | İstem | Değer | 
   | ------ | ----- |
   | Dağıtım şablonu dosyasını seçin | CustomVisionSolution klasöründeki dosya **deployment.template.js** seçin. |
   | Modül şablonunu seçin | **Python Modülü**'nü seçin |
   | Modül adı sağlayın | Modülünüze **cameraCapture** adını verin |
   | Modül için Docker görüntü deposunu sağlama | **Localhost: 5000** ' i Azure Container Registry 'Nizin **oturum açma sunucusu** değeriyle değiştirin.<br><br>Son dize **\<registryname\> . azurecr.io/cameracapture** gibi görünür. |

   VS Code penceresi yeni modülünüzü çözüm çalışma alanında yükler ve deployment.template.json dosyasını güncelleştirir. Bu noktada iki modül klasörü görmeniz gerekir: classifier ve cameraCapture. 

2. **modules** / **cameraCapture** klasöründeki **main.py** dosyasını açın. 

3. Dosyanın tamamını aşağıdaki kod ile değiştirin. Bu kod örneği, sınıflandırıcı modülünde çalışan görüntü işleme hizmetine POST istekleri gönderir. Bu modül kapsayıcısı, isteklerde kullanabileceğiniz örnek bir görüntüyle sunulmaktadır. Modül ardından yanıtı bir IoT Hub iletisi olarak paketler ve bir çıkış kuyruğuna gönderir.  

    ```python
    # Copyright (c) Microsoft. All rights reserved.
    # Licensed under the MIT license. See LICENSE file in the project root for
    # full license information.

    import time
    import sys
    import os
    import requests
    import json
    from azure.iot.device import IoTHubModuleClient, Message

    # global counters
    SENT_IMAGES = 0

    # global client
    CLIENT = None

    # Send a message to IoT Hub
    # Route output1 to $upstream in deployment.template.json
    def send_to_hub(strMessage):
        message = Message(bytearray(strMessage, 'utf8'))
        CLIENT.send_message_to_output(message, "output1")
        global SENT_IMAGES
        SENT_IMAGES += 1
        print( "Total images sent: {}".format(SENT_IMAGES) )

    # Send an image to the image classifying server
    # Return the JSON response from the server with the prediction result
    def sendFrameForProcessing(imagePath, imageProcessingEndpoint):
        headers = {'Content-Type': 'application/octet-stream'}

        with open(imagePath, mode="rb") as test_image:
            try:
                response = requests.post(imageProcessingEndpoint, headers = headers, data = test_image)
                print("Response from classification service: (" + str(response.status_code) + ") " + json.dumps(response.json()) + "\n")
            except Exception as e:
                print(e)
                print("No response from classification service")
                return None

        return json.dumps(response.json())

    def main(imagePath, imageProcessingEndpoint):
        try:
            print ( "Simulated camera module for Azure IoT Edge. Press Ctrl-C to exit." )

            try:
                global CLIENT
                CLIENT = IoTHubModuleClient.create_from_edge_environment()
            except Exception as iothub_error:
                print ( "Unexpected error {} from IoTHub".format(iothub_error) )
                return

            print ( "The sample is now sending images for processing and will indefinitely.")

            while True:
                classification = sendFrameForProcessing(imagePath, imageProcessingEndpoint)
                if classification:
                    send_to_hub(classification)
                time.sleep(10)

        except KeyboardInterrupt:
            print ( "IoT Edge module sample stopped" )

    if __name__ == '__main__':
        try:
            # Retrieve the image location and image classifying server endpoint from container environment
            IMAGE_PATH = os.getenv('IMAGE_PATH', "")
            IMAGE_PROCESSING_ENDPOINT = os.getenv('IMAGE_PROCESSING_ENDPOINT', "")
        except ValueError as error:
            print ( error )
            sys.exit(1)

        if ((IMAGE_PATH and IMAGE_PROCESSING_ENDPOINT) != ""):
            main(IMAGE_PATH, IMAGE_PROCESSING_ENDPOINT)
        else: 
            print ( "Error: Image path or image-processing endpoint missing" )
    ```

4. **main.py** dosyasını kaydedin. 

5. **requrements.txt** dosyasını açın. 

6. Kapsayıcıya dahil etmek için yeni bir kitaplık satırı ekleyin.

   ```Text
   requests
   ```

7. **requirements.txt** dosyasını kaydedin.


#### <a name="add-a-test-image-to-the-container"></a>Kapsayıcıya test görüntüsü ekleme

Bu senaryoda görüntü beslemesi sağlamak için gerçek bir kamera kullanmak yerine tek bir test amaçlı görüntü kullanacağız. Bu öğreticinin önceki bölümlerinde eğitim görüntüleri için indirdiğiniz GitHub deposunda bir test görüntüsü bulunur. 

1. Bilişsel **-customvision-Windows**  /  **örnek**  /  **görüntüleri**  /  **testinde** bulunan test görüntüsüne gidin. 

2. **test_image.jpg** dosyasını kopyalayın 

3. IoT Edge çözüm dizininize gidin ve test görüntüsünü **modüller**  /  **cameraCapture** klasörüne yapıştırın. Görüntü önceki bölümde düzenlediğiniz main.py dosyasıyla aynı klasörde olmalıdır. 

4. Visual Studio Code'da cameraCapture modülünün **Dockerfile.amd64** dosyasını açın.

5. Çalışma dizinini belirleyen `WORKDIR /app` satırından sonra şu kod satırını ekleyin:

   ```Dockerfile
   ADD ./test_image.jpg .
   ```

6. Dockerfile dosyasını kaydedin.

### <a name="prepare-a-deployment-manifest"></a>Dağıtım bildirimi hazırlama

Bu öğreticide bu noktaya kadar ağaç görüntülerini sınıflandırmak için bir Özel Görüntü İşleme Hizmeti modeli eğittiniz ve bu modeli bir IoT Edge modülü olarak paketlediniz. Ardından görüntü sınıflandırma sunucusunu sorgulayıp sonuçlarını IoT Hub'a bildirebilecek ikinci bir modül oluşturdunuz. Artık bir IoT Edge cihazına bu iki modülü birlikte başlatma ve çalıştırma sürecini anlatacak dağıtım bildirimini oluşturmaya hazırsınız. 

Visual Studio Code için IoT Edge uzantısı dağıtım bildirimi oluşturmanıza yardımcı olmak için her IoT Edge çözümünde bir şablon oluşturur. 

1. Çözüm klasöründeki **deployment.template.json** dosyasını açın. 

2. Üç modül içermesi gereken **modüller** bölümünü bulun: oluşturduğunuz iki, sınıflandırıcı ve cameraCapture ve varsayılan olarak SimulatedTemperatureSensor içeren bir üçüncü. 

3. **SimulatedTemperatureSensor** modülünü tüm parametreleriyle birlikte silin. Bu modül, test senaryoları için örnek veri sağlamak için eklenmiştir ancak bu dağıtımda ihtiyacınız yoktur. 

4. Görüntü sınıflandırma modülüne **classifier** dışında bir ad verdiyseniz adı bu adımda kontrol edin ve tamamının küçük harf olduğundan emin olun. cameraCapture modülü, classifier modülünü tüm istekleri küçük harf olarak biçimlendiren istek kitaplığını kullanarak çağırır ve IoT Edge büyük/küçük harfe duyarlıdır. 

5. cameraCapture modülünün **createOptions** parametresini aşağıdaki JSON koduyla güncelleştirin. Bu bilgiler main.py işleminde alınan modül kapsayıcısında ortam değişkenlerini oluşturur. Bu bilgileri dağıtım bildirimine ekleyerek modül görüntüsünü yeniden derlemek zorunda kalmadan görüntüyü veya uç noktayı değiştirebilirsiniz. 

    ```json
    "createOptions": "{\"Env\":[\"IMAGE_PATH=test_image.jpg\",\"IMAGE_PROCESSING_ENDPOINT=http://classifier/image\"]}"
    ```

    Özel Görüntü İşleme Hizmeti modülünüzün adını *classifier* dışında bir değer olarak belirlediyseniz görüntü işleme uç nokta değerini uygun şekilde güncelleştirin. 

6. Dosyanın en altındaki $edgeHub modülünün **routes** parametresini güncelleştirin. Tahmin sonuçlarının cameraCapture kaynağından IoT Hub'a yönlendirilmesini istersiniz.

    ```json
        "routes": {
          "cameraCaptureToIoTHub": "FROM /messages/modules/cameraCapture/outputs/* INTO $upstream"
        },
    ```

    İkinci modülün adını *cameraCapture* dışında bir değer olarak belirlediyseniz yönlendirme değerini uygun şekilde güncelleştirin. 

7. **deployment.template.json** dosyasını kaydedin.

## <a name="build-and-push-your-iot-edge-solution"></a>IoT Edge çözümünüzü derleyin ve gönderin

İki modülü de oluşturduğunuza ve dağıtım bildirimi şablonunu yapılandırdığınıza göre kapsayıcı görüntülerini oluşturmaya ve kapsayıcı kayıt defterinize göndermeye hazırsınız.

Görüntüleri kayıt defterinize gönderdikten sonra çözümü bir IoT Edge cihazına dağıtabilirsiniz. IoT Hub üzerinden bir cihazda modül ayarlayabilirsiniz ancak IoT Hub ve cihazlara Visual Studio Code aracılığıyla da erişebilirsiniz. Bu bölümde IoT Hub'ınıza erişim ayarlarını yapacak ve ardından çözümünüzü IoT Edge cihazınıza dağıtmak için VS Code uygulamasını kullanacaksınız.

İlk olarak çözümünüzü oluşturun ve kapsayıcı kayıt defterinize gönderin.

1. **Görünüm** terminali ' i seçerek vs Code tümleşik Terminal ' i açın  >  .

2. Terminalde aşağıdaki komutu girerek Docker 'da oturum açın. Azure Container Registry 'nizden Kullanıcı adı, parola ve oturum açma sunucusu ile oturum açın. Azure portal kayıt defterinizin **erişim tuşları** bölümünden bu değerleri alabilirsiniz.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Kullanımını öneren bir güvenlik uyarısı alabilirsiniz `--password-stdin` . Bu en iyi uygulama, üretim senaryolarında önerilse de, Bu öğreticinin kapsamı dışındadır. Daha fazla bilgi için bkz. [Docker oturum açma](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) başvurusu.

3. VS Code Gezgini 'nde, dosya **üzerindedeployment.template.js** sağ tıklayın ve **Build ve push IoT Edge çözümünü** seçin.

   Build ve push komutu üç işlem başlatır. İlk olarak, dağıtım şablonunda ve diğer çözüm dosyalarında bilgi oluşturulan tam dağıtım bildirimini tutan **config** adlı çözümde yeni bir klasör oluşturur. İkincisi, `docker build` hedef mimariniz için uygun dockerfile 'ı temel alan kapsayıcı görüntüsünü oluşturmak için çalışır. Ardından, `docker push` görüntü deposunu kapsayıcı Kayıt defterinize göndermek için çalışır.

   Bu işlem ilk kez birkaç dakika sürebilir, ancak komutları bir sonraki çalıştırışınızda daha hızlıdır.

## <a name="deploy-modules-to-device"></a>Modülleri cihaza dağıt

Modül projesini IoT Edge cihazınıza dağıtmak için Visual Studio Code Gezginini ve Azure IoT araçları uzantısını kullanın. Senaryonuz için hazırlanan bir dağıtım bildiriminiz zaten var, config klasöründeki dosya **deployment.amd64.js** . Tek yapmanız gereken dağıtımı almak üzere bir cihaz seçmek.

IoT Edge cihazınızın çalışır ve çalışıyor olduğundan emin olun.

1. Visual Studio Code Gezgini ' nde, **Azure IoT Hub** bölümünde **aygıtlar** ' ı genişleterek IoT cihazları listesini görüntüleyin.

2. IoT Edge cihazınızın adına sağ tıklayıp **Create Deployment for Single Device** (Tek bir cihaz için dağıtım oluştur) öğesini seçin.

3. **Config** klasöründeki dosya **deployment.amd64.js** seçin ve ardından **kenar dağıtım bildirimini Seç**' e tıklayın. deployment.template.json dosyasını kullanmayın.

4. Cihazınızın altında, dağıtılan ve çalışan modüllerin listesini görmek için **modüller** ' i genişletin. Yenile düğmesine tıklayın. **$EdgeAgent** ve **$edgeHub** birlikte çalışan yeni **sınıflandırıcının** ve **cameraCapture** modüllerinin görmeniz gerekir.  

Tüm modüllerin cihazınızda çalışıp çalışmadığını da kontrol edebilirsiniz. IoT Edge cihazında modüllerin durumunu görmek için aşağıdaki komutu çalıştırın.

   ```bash
   iotedge list
   ```

Modüllerin başlaması birkaç dakika sürebilir. IoT Edge çalışma zamanının yeni dağıtım bildirimini alması, kapsayıcı çalışma zamanından modül görüntülerini çekmek ve sonra her yeni modülü başlatması gerekir.

## <a name="view-classification-results"></a>Sınıflandırma sonuçlarını görüntüleme

Modüllerinizin sonuçlarını görüntülemek için kullanabileceğiniz iki yöntem vardır. Bunu iletiler gönderilip alındıkça cihazın üzerinden veya iletiler IoT Hub'a ulaştıkça Visual Studio Code'dan gerçekleştirebilirsiniz. 

Cihazdan cameraCapture modülü günlüklerini görüntüleyerek iletilerin gönderildiğini ve IoT Hub tarafından alındığına dair onay verildiğini görebilirsiniz. 

   ```bash
   iotedge logs cameraCapture
   ```

Visual Studio Code, IoT Edge cihazınızın adına sağ tıklayın ve **Izlemeyi Başlat yerleşik olay uç noktasını** seçin. 

> [!NOTE]
> Başlangıçta, cameraCapture modülünden Çıkışta bağlantı hataları görebilirsiniz. Bunun nedeni, dağıtılan ve başlatılan modüller arasındaki gecikmeden kaynaklanır.
>
> CameraCapture modülü başarılı olana kadar otomatik olarak bağlantı bağlantısını otomatik olarak yeniden durdurur. Başarılı bağlantıdan sonra, aşağıda açıklanan beklenen görüntü sınıflandırma iletilerini görürsünüz.

cameraCapture modülünden iletiler olarak gönderilen Özel Görüntü İşleme Hizmeti modülü sonuçları, görüntünün köknar veya kiraz ağacı olma ihtimalini içerir. Görüntü köknar ağacına ait olduğundan olasılık değeri 1.0 olmalıdır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sonraki önerilen makaleye geçmeyi planlıyorsanız, oluşturduğunuz kaynaklarla yapılandırmaları tutabilir ve yeniden kullanabilirsiniz. Aynı IoT Edge cihazını test cihazı olarak kullanmaya devam edebilirsiniz. 

Aksi takdirde, ücretlerden kaçınmak için bu makalede kullandığınız yerel konfigürasyonları ve Azure kaynaklarını silebilirsiniz. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide bir Özel Görüntü İşleme Hizmeti modeli eğittiniz ve bunu modül olarak bir IoT Edge cihazına dağıttınız. Ardından görüntü sınıflandırma hizmetini sorgulayıp sonuçlarını IoT Hub'a bildirebilecek bir modül oluşturdunuz.

Azure IoT Edge'in verileri iş içgörüsüne çevirmenize yardımcı olabilecek diğer yollar hakkında bilgi edinmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [SQL Server veritabanları ile uç cihazlarda veri depolama](tutorial-store-data-sql-server.md)