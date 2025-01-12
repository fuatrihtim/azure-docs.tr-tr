---
title: 'Öğretici: toplu algılama ve Power BI kullanarak anomali görselleştirin'
titleSuffix: Azure Cognitive Services
description: Anomali algılayıcısı API 'sini kullanmayı ve zaman serisi verileriniz genelinde anormallikleri görselleştirmek için Power BI öğrenin.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: fc2667dfddf4442acaf39f6783190e8640b22a89
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561738"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>Öğretici: toplu algılama ve Power BI kullanarak anomali görselleştirin

Toplu iş olarak ayarlanan zaman serisi verilerinin içindeki bozukluklar bulmak için bu öğreticiyi kullanın. Power BI Masaüstü 'nü kullanarak bir Excel dosyası alır, anomali algılayıcı API 'sine yönelik verileri hazırlar ve bunun genelinde istatistiksel bozukluklar görselleştirebilirsiniz.

Bu öğreticide aşağıdakilerin nasıl yapılacağını öğreneceksiniz:

> [!div class="checklist"]
> * Zaman serisi veri kümesini içeri ve dışarı dönüştürmek için Power BI Desktop kullanma
> * Batch anomali algılama için anomali algılayıcı API 'siyle Power BI Desktop tümleştirme
> * Beklenen ve görülen değerler ve anomali algılama sınırları dahil olmak üzere verilerinizde bulunan anormallikleri görselleştirin.

## <a name="prerequisites"></a>Önkoşullar
* [Azure aboneliği](https://azure.microsoft.com/free/cognitive-services)
* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/)ücretsiz olarak kullanılabilir.
* Zaman serisi veri noktaları içeren bir Excel dosyası (. xlsx). Bu hızlı başlangıçta örnek veriler [GitHub](https://go.microsoft.com/fwlink/?linkid=2090962) 'da bulunabilir
* Azure aboneliğiniz olduktan sonra <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" "  target="_blank"> </a> anahtar ve uç noktanıza ulaşmak için Azure Portal bir anomali algılayıcı kaynağı oluşturun.
    * Uygulamanızı anomali algılayıcı API 'sine bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Bunu daha sonra hızlı başlangıçta yapacaksınız.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>Zaman serisi verilerini yükleme ve biçimlendirme

Başlamak için Power BI Desktop açın ve önkoşullardan indirdiğiniz zaman serisi verilerini yükleyin. Bu Excel dosyası, bir dizi Eşgüdümlü Evrensel Saat (UTC) zaman damgası ve değer çiftleri içerir.  

> [!NOTE]
> Power BI. csv dosyaları, SQL veritabanları, Azure Blob depolama ve daha fazlası gibi çok çeşitli kaynaklardan verileri kullanabilir.  

Ana Power BI Desktop penceresinde, **giriş** şeridi ' ne tıklayın. Şeridin **dış veri** grubunda, **veri al** açılır menüsünü açın ve **Excel**' e tıklayın.

![Power BI içindeki "veri al" düğmesinin görüntüsü](../media/tutorials/power-bi-get-data-button.png)

İletişim kutusu görüntülendikten sonra, example. xlsx dosyasını indirdiğiniz klasöre gidin ve dosyayı seçin. **Gezgin** iletişim kutusu görüntülendikten sonra, **Sheet1**' e ve ardından **Düzenle**' ye tıklayın.

![Power BI ' de veri kaynağı "Gezgini" ekranının görüntüsü](../media/tutorials/navigator-dialog-box.png)

Power BI, ilk sütundaki zaman damgasını bir `Date/Time` veri türüne dönüştürür. Bu zaman damgaları, anomali algılayıcısı API 'sine gönderilmek üzere metne dönüştürülemelidir. Power Query Düzenleyicisi otomatik olarak açılmazsa Giriş sekmesinde **sorguları Düzenle** ' ye tıklayın.

Power Query düzenleyicisinde **Dönüştür** şeridine tıklayın. **Herhangi bir sütun** grubunda, **veri türü:** açılan menü menüsünü açın ve **metin**' i seçin.

![Veri türü açılan listesi görüntüsü](../media/tutorials/data-type-drop-down.png)

Sütun türünü değiştirme hakkında bir bildirim aldığınızda, **geçerli Değiştir**' e tıklayın. Daha sonra, **kapat &** **giriş** şeridinde Uygula veya **Uygula** ' ya tıklayın.

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>Verileri göndermek ve yanıtı biçimlendirmek için bir işlev oluşturma

Veri dosyasını anomali algılayıcı API 'sine biçimlendirmek ve göndermek için yukarıda oluşturulan tabloda bir sorgu çağırabilirsiniz. Power Query düzenleyicisinde, **giriş** şeridinde, **Yeni kaynak** açılan menüsünü açın ve **boş sorgu**' ya tıklayın.

Yeni sorgunuzun seçili olduğundan emin olun ve ardından **Gelişmiş Düzenleyici**' ye tıklayın.

!["Gelişmiş Düzenleyici" ekranının görüntüsü](../media/tutorials/advanced-editor-screen.png)

Gelişmiş Düzenleyici içinde, tablodaki sütunları ayıklamak ve API 'ye göndermek için aşağıdaki Power Query a kod parçacığını kullanın. Daha sonra sorgu, JSON yanıtından bir tablo oluşturur ve döndürür. `apiKey`Değişkeni geçerli anomali ALGıLAYıCı API anahtarınızla ve uç noktanızla değiştirin `endpoint` . Sorguyu Gelişmiş Düzenleyici girdikten sonra **bitti**' ye tıklayın.

```M
(table as table) => let

    apikey      = "[Placeholder: Your Anomaly Detector resource access key]",
    endpoint    = "[Placeholder: Your Anomaly Detector resource endpoint]/anomalydetector/v1.0/timeseries/entire/detect",
    inputTable = Table.TransformColumnTypes(table,{{"Timestamp", type text},{"Value", type number}}),
    jsontext    = Text.FromBinary(Json.FromValue(inputTable)),
    jsonbody    = "{ ""Granularity"": ""daily"", ""Sensitivity"": 95, ""Series"": "& jsontext &" }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Content-Type" = "application/json", #"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody, ManualStatusHandling={400}]),
    jsonresp    = Json.Document(bytesresp),

    respTable = Table.FromColumns({

                     Table.Column(inputTable, "Timestamp")
                     ,Table.Column(inputTable, "Value")
                     , Record.Field(jsonresp, "IsAnomaly") as list
                     , Record.Field(jsonresp, "ExpectedValues") as list
                     , Record.Field(jsonresp, "UpperMargins")as list
                     , Record.Field(jsonresp, "LowerMargins") as list
                     , Record.Field(jsonresp, "IsPositiveAnomaly") as list
                     , Record.Field(jsonresp, "IsNegativeAnomaly") as list

                  }, {"Timestamp", "Value", "IsAnomaly", "ExpectedValues", "UpperMargin", "LowerMargin", "IsPositiveAnomaly", "IsNegativeAnomaly"}
               ),

    respTable1 = Table.AddColumn(respTable , "UpperMargins", (row) => row[ExpectedValues] + row[UpperMargin]),
    respTable2 = Table.AddColumn(respTable1 , "LowerMargins", (row) => row[ExpectedValues] -  row[LowerMargin]),
    respTable3 = Table.RemoveColumns(respTable2, "UpperMargin"),
    respTable4 = Table.RemoveColumns(respTable3, "LowerMargin"),

    results = Table.TransformColumnTypes(

                respTable4,
                {{"Timestamp", type datetime}, {"Value", type number}, {"IsAnomaly", type logical}, {"IsPositiveAnomaly", type logical}, {"IsNegativeAnomaly", type logical},
                 {"ExpectedValues", type number}, {"UpperMargins", type number}, {"LowerMargins", type number}}
              )

 in results
```

Aşağıdaki parametre girin ' i seçerek veri sayfanızda sorguyu `Sheet1` çağırın ve **çağır**' a tıklayın.

![Invoke işlevinin görüntüsü](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>Veri kaynağı gizliliği ve kimlik doğrulaması

> [!NOTE]
> Veri gizliliği ve erişimi için kuruluşunuzun ilkelerine dikkat edin. Daha fazla bilgi için bkz. [Power BI Desktop gizlilik düzeyleri](/power-bi/desktop-privacy-levels) .

Bir dış veri kaynağı kullandığından sorguyu çalıştırmaya çalıştığınızda bir uyarı iletisi alabilirsiniz.

![Power BI tarafından oluşturulan bir uyarıyı gösteren resim](../media/tutorials/blocked-function.png)

Bu hatayı onarmak için **Dosya** ve **Seçenekler ve ayarlar**' a tıklayın. **Seçenekler**' e tıklayın. **Geçerli dosyanın** altında **Gizlilik**' i seçin ve **gizlilik düzeylerini yoksayın ve potansiyel olarak performansı geliştirebilirsiniz**.

Ayrıca, API 'ye nasıl bağlanmak istediğinizi belirtmenizi isteyen bir ileti alabilirsiniz.

![Erişim kimlik bilgilerini belirtme isteğini gösteren bir görüntü](../media/tutorials/edit-credentials-message.png)

Bu hatayı onarmak için iletideki **kimlik bilgilerini düzenle** ' ye tıklayın. İletişim kutusu görüntülendikten sonra, API 'ye anonim olarak bağlanmak için **anonim** ' i seçin. Ardından **Bağlan**’a tıklayın.

Ardından, değişiklikleri uygulamak için **giriş** şeridindeki **Kapat & Uygula** ' ya tıklayın.

## <a name="visualize-the-anomaly-detector-api-response"></a>Anomali algılayıcı API 'SI yanıtını görselleştirin

Ana Power BI ekranında, verileri görselleştirmek için yukarıda oluşturulan sorguları kullanmaya başlayın. Önce **Görselleştirmelerde** **çizgi grafik** ' i seçin. Sonra çağrılan işlevden zaman damgasını çizgi grafiğinin **eksenine** ekleyin. Sağ tıklayın ve **zaman damgası**' nı seçin.

![Zaman damgası değerine sağ tıklayın](../media/tutorials/timestamp-right-click.png)

**Çağrılan işlevden** aşağıdaki alanları grafiğin **değerler** alanına ekleyin. Grafiğinizi oluşturmaya yardımcı olması için aşağıdaki ekran görüntüsünü kullanın.

* Değer
* Üst Üstekenar boşlukları
* Harf kenar boşlukları
* ExpectedValues

![Grafik ayarlarının görüntüsü](../media/tutorials/chart-settings.png)

Alanları ekledikten sonra grafiğe tıklayın ve tüm veri noktalarını gösterecek şekilde yeniden boyutlandırın. Grafiğiniz aşağıdaki ekran görüntüsüne benzer şekilde görünür:

![Grafik görselleştirmesinin görüntüsü](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>Anomali veri noktalarını görüntüle

Power BI penceresinin sağ tarafında, **alanlar** bölmesinin altında, **çağrılan Işlev sorgusunun** altındaki **değere** sağ tıklayın ve **yeni hızlı ölçü**' e tıklayın.

![Yeni hızlı ölçü ekranının görüntüsü](../media/tutorials/new-quick-measure.png)

Görüntülenen ekranda, hesaplama olarak **filtrelenmiş değer** ' i seçin. **Taban değerini** olarak ayarlayın `Sum of Value` . Ardından `IsAnomaly` , **çağrılan Işlev** alanlarından **filtrelemek** için sürükleyin. `True` **Filtre** açılan menüsünden seçim yapın.

![Yeni hızlı ölçü ekranının ikinci bir görüntüsü](../media/tutorials/new-quick-measure-2.png)

**Tamam**' a tıkladıktan sonra, `Value for True` alanlarınızın listesinin en altında bulunan bir alana sahip olursunuz. Sağ tıklayın ve **anomali** olarak yeniden adlandırın. Bunu grafiğin **değerlerine** ekleyin. Daha sonra **Biçim** aracını seçin ve X ekseni türünü **kategorik** olarak ayarlayın.

![X ekseninin biçimi resmi](../media/tutorials/format-x-axis.png)

**Biçim** aracına ve **veri renklerine** tıklayarak, grafiğinize renkler uygulayın. Grafiğiniz aşağıdakine benzer görünmelidir:

![Son grafiğin bir görüntüsü](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
>[Azure Databricks ile akış anomali algılama](../overview.md)