---
title: Spark-Azure HDInsight ile uygulama Insight günlüklerini çözümleme
description: Application Insight günlüklerini blob depolamaya aktarmayı öğrenin ve ardından HDInsight 'ta Spark ile günlükleri çözümleyin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 9ae06565039e53c0096ff0541f7b28a5b041d9e8
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864559"
---
# <a name="analyze-application-insights-telemetry-logs-with-apache-spark-on-hdinsight"></a>HDInsight üzerinde Apache Spark Application Insights telemetri günlüklerini çözümleme

HDInsight üzerinde [Apache Spark](https://spark.apache.org/) kullanarak Application Insight telemetri verilerini analiz edin.

[Visual Studio Application Insights](../../azure-monitor/app/app-insights-overview.md) , Web uygulamalarınızı izleyen bir analiz hizmetidir. Application Insights tarafından oluşturulan telemetri verileri, Azure depolama 'ya aktarılabilir. Veriler Azure Storage 'da olduktan sonra, HDInsight analiz etmek için kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar

* Application Insights kullanacak şekilde yapılandırılmış bir uygulama.

* Linux tabanlı HDInsight kümesi oluşturma hakkında benzerlik. Daha fazla bilgi için bkz. [HDInsight üzerinde Apache Spark oluşturma](apache-spark-jupyter-spark-sql.md).

* Bir Web tarayıcısı.

Bu belgeyi geliştirmek ve test etmek için aşağıdaki kaynaklar kullanılmıştır:

* Application Insights telemetri verileri, [ Application Insights kullanmak üzere yapılandırılmış birNode.js Web uygulaması](../../azure-monitor/app/nodejs.md)kullanılarak oluşturulmuştur.

* HDInsight kümesi sürüm 3,5 ' de Linux tabanlı bir Spark, verileri çözümlemek için kullanıldı.

## <a name="architecture-and-planning"></a>Mimari ve planlama

Aşağıdaki diyagramda bu örneğin hizmet mimarisi gösterilmektedir:

:::image type="content" source="./media/apache-spark-analyze-application-insight-logs/application-insights.png" alt-text="Application Insights blob depolamaya ve Spark 'a kadar veri akışı" border="false":::

### <a name="azure-storage"></a>Azure Depolama

Application Insights, telemetri bilgilerini sürekli olarak bloblara aktarmak üzere yapılandırılabilir. HDInsight daha sonra bloblarda depolanan verileri okuyabilir. Ancak izlemeniz gereken bazı gereksinimler vardır:

* **Konum**: depolama hesabı ve HDInsight farklı konumlarda ise gecikme süresini artırabilir. Ayrıca, bölgeler arasında taşınan verilere giden çıkış ücretleri uygulandıkça maliyeti de artırır.

    > [!WARNING]  
    > HDInsight 'tan farklı bir konumda depolama hesabı kullanılması desteklenmez.

* **BLOB türü**: HDInsight yalnızca blok bloblarını destekler. Varsayılan olarak blok bloblarını kullanmak Application Insights, bu nedenle HDInsight ile varsayılan olarak çalışmalıdır.

Mevcut bir kümeye depolama ekleme hakkında daha fazla bilgi için bkz. [ek depolama hesapları ekleme](../hdinsight-hadoop-add-storage.md) belgesi.

### <a name="data-schema"></a>Veri şeması

Application Insights, bloblara dışarı aktarılmış telemetri veri biçimi için [veri modeli bilgilerini dışarı aktarma](../../azure-monitor/app/export-data-model.md) sağlar. Bu belgedeki adımlarda, verilerle çalışmak için Spark SQL kullanılır. Spark SQL, Application Insights tarafından günlüğe kaydedilen JSON veri yapısı için otomatik olarak bir şema üretebilir.

## <a name="export-telemetry-data"></a>Telemetri verilerini dışarı aktar

Telemetri bilgilerini bir Azure Storage blobuna aktarmak üzere Application Insights yapılandırmak için [sürekli dışarı aktarmayı yapılandırma](../../azure-monitor/app/export-telemetry.md) bölümündeki adımları izleyin.

## <a name="configure-hdinsight-to-access-the-data"></a>HDInsight 'ı verilere erişecek şekilde yapılandırma

HDInsight kümesi oluşturuyorsanız, küme oluşturma sırasında depolama hesabını ekleyin.

Azure Depolama hesabını mevcut bir kümeye eklemek için, [ek depolama hesapları ekleme](../hdinsight-hadoop-add-storage.md) belgesi içindeki bilgileri kullanın.

## <a name="analyze-the-data-pyspark"></a>Verileri analiz etme: PySpark

1. Bir Web tarayıcısından `https://CLUSTERNAME.azurehdinsight.net/jupyter` clustername öğesinin Kümenizin adı olduğu yere gidin.

2. Jupi sayfasının sağ üst köşesinde, **Yeni** ve ardından **pyspark**' ı seçin. Python tabanlı Jupyter Notebook içeren yeni bir tarayıcı sekmesi açılır.

3. Sayfada ilk alanda ( **hücre** olarak adlandırılır) aşağıdaki metni girin:

   ```python
   sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   ```

    Bu kod Spark 'ı, giriş verileri için dizin yapısına yinelemeli olarak erişecek şekilde yapılandırır. Application Insights telemetri, öğesine benzer bir dizin yapısına kaydedilir `/{telemetry type}/YYYY-MM-DD/{##}/` .

4. Kodu çalıştırmak için **SHIFT + enter** tuşlarını kullanın. Hücrenin sol tarafında, \* Bu hücredeki kodun yürütüldüğü belirten köşeli ayraçlar arasında bir ' ' görünür. İşlem tamamlandıktan sonra ' \* ' bir sayıya değişir ve aşağıdaki metne benzer bir çıkış hücrenin altında görüntülenir:

    ```output
    Creating SparkContext as 'sc'

    ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
    3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔

    Creating HiveContext as 'sqlContext'
    SparkContext and HiveContext created. Executing user code ...
    ```

5. Birinci bir hücre aşağıda oluşturulur. Yeni hücreye aşağıdaki metni girin. İle `CONTAINER` ve `STORAGEACCOUNT` Application Insights verileri içeren blob kapsayıcısı adı ile değiştirin.

   ```python
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Bu hücreyi yürütmek için **SHIFT + enter** tuşlarını kullanın. Aşağıdaki metne benzer bir sonuç görürsünüz:

    ```output
    Found 1 items
    drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
    ```

    Döndürülen IBS yolu, Application Insights Telemetri verilerinin konumudur. `hdfs dfs -ls`Döndürülen IBS yolunu kullanmak için hücredeki çizgiyi değiştirin ve sonra hücreyi yeniden çalıştırmak Için **SHIFT + enter** tuşlarını kullanın. Bu kez, sonuçlar telemetri verilerini içeren dizinleri görüntülemelidir.

   > [!NOTE]  
   > Bu bölümdeki adımların geri kalanı için `wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` Dizin kullanılmıştır. Dizin yapınız farklı olabilir.

6. Sonraki hücrede şu kodu girin: `WASB_PATH` önceki adımdaki yol Ile değiştirin.

   ```python
   jsonFiles = sc.textFile('WASB_PATH')
   jsonData = sqlContext.read.json(jsonFiles)
   ```

    Bu kod, sürekli dışarı aktarma işlemi tarafından dışarı aktarılmış JSON dosyalarından bir dataframe oluşturur. Bu hücreyi çalıştırmak için **SHIFT + enter** tuşlarını kullanın.
7. Bir sonraki hücrede, Spark 'ın JSON dosyaları için oluşturduğu şemayı görüntülemek için aşağıdakileri girin ve aşağıdakini çalıştırın:

   ```python
   jsonData.printSchema()
   ```

    Her telemetri türünün şeması farklıdır. Aşağıdaki örnek, Web istekleri için oluşturulan şemadır ( `Requests` alt dizinde depolanan veriler):

    ```output
    root
    |-- context: struct (nullable = true)
    |    |-- application: struct (nullable = true)
    |    |    |-- version: string (nullable = true)
    |    |-- custom: struct (nullable = true)
    |    |    |-- dimensions: array (nullable = true)
    |    |    |    |-- element: string (containsNull = true)
    |    |    |-- metrics: array (nullable = true)
    |    |    |    |-- element: string (containsNull = true)
    |    |-- data: struct (nullable = true)
    |    |    |-- eventTime: string (nullable = true)
    |    |    |-- isSynthetic: boolean (nullable = true)
    |    |    |-- samplingRate: double (nullable = true)
    |    |    |-- syntheticSource: string (nullable = true)
    |    |-- device: struct (nullable = true)
    |    |    |-- browser: string (nullable = true)
    |    |    |-- browserVersion: string (nullable = true)
    |    |    |-- deviceModel: string (nullable = true)
    |    |    |-- deviceName: string (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- osVersion: string (nullable = true)
    |    |    |-- type: string (nullable = true)
    |    |-- location: struct (nullable = true)
    |    |    |-- city: string (nullable = true)
    |    |    |-- clientip: string (nullable = true)
    |    |    |-- continent: string (nullable = true)
    |    |    |-- country: string (nullable = true)
    |    |    |-- province: string (nullable = true)
    |    |-- operation: struct (nullable = true)
    |    |    |-- name: string (nullable = true)
    |    |-- session: struct (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- isFirst: boolean (nullable = true)
    |    |-- user: struct (nullable = true)
    |    |    |-- anonId: string (nullable = true)
    |    |    |-- isAuthenticated: boolean (nullable = true)
    |-- internal: struct (nullable = true)
    |    |-- data: struct (nullable = true)
    |    |    |-- documentVersion: string (nullable = true)
    |    |    |-- id: string (nullable = true)
    |-- request: array (nullable = true)
    |    |-- element: struct (containsNull = true)
    |    |    |-- count: long (nullable = true)
    |    |    |-- durationMetric: struct (nullable = true)
    |    |    |    |-- count: double (nullable = true)
    |    |    |    |-- max: double (nullable = true)
    |    |    |    |-- min: double (nullable = true)
    |    |    |    |-- sampledValue: double (nullable = true)
    |    |    |    |-- stdDev: double (nullable = true)
    |    |    |    |-- value: double (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- name: string (nullable = true)
    |    |    |-- responseCode: long (nullable = true)
    |    |    |-- success: boolean (nullable = true)
    |    |    |-- url: string (nullable = true)
    |    |    |-- urlData: struct (nullable = true)
    |    |    |    |-- base: string (nullable = true)
    |    |    |    |-- hashTag: string (nullable = true)
    |    |    |    |-- host: string (nullable = true)
    |    |    |    |-- protocol: string (nullable = true)
    ```

8. Veri çerçevesini geçici bir tablo olarak kaydetmek ve verilere karşı bir sorgu çalıştırmak için aşağıdakileri kullanın:

   ```python
   jsonData.registerTempTable("requests")
   df = sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   df.show()
   ```

    Bu sorgu, Context. Location. City null olmayan ilk 20 kayıt için şehir bilgilerini döndürür.

   > [!NOTE]  
   > Bağlam yapısı, Application Insights tarafından günlüğe kaydedilen tüm telemetride mevcuttur. City öğesi günlüklerinizi doldurmayabilir. Günlüklerinizin verilerini içerebilen Sorgulayabileceğiniz diğer öğeleri belirlemek için şemayı kullanın.

    Bu sorgu, aşağıdaki metne benzer bilgiler döndürür:

    ```output
    +---------+
    |     city|
    +---------+
    | Bellevue|
    |  Redmond|
    |  Seattle|
    |Charlotte|
    ...
    +---------+
    ```

## <a name="analyze-the-data-scala"></a>Verileri analiz etme: Scala

1. Bir Web tarayıcısından `https://CLUSTERNAME.azurehdinsight.net/jupyter` clustername öğesinin Kümenizin adı olduğu yere gidin.

2. Jupi sayfasının sağ üst köşesinde, **Yeni**' yi ve ardından **Scala**' yı seçin. Scala tabanlı Jupyter Notebook içeren yeni bir tarayıcı sekmesi görüntülenir.

3. Sayfada ilk alanda ( **hücre** olarak adlandırılır) aşağıdaki metni girin:

   ```scala
   sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   ```

    Bu kod Spark 'ı, giriş verileri için dizin yapısına yinelemeli olarak erişecek şekilde yapılandırır. Application Insights telemetri, öğesine benzer bir dizin yapısına kaydedilir `/{telemetry type}/YYYY-MM-DD/{##}/` .

4. Kodu çalıştırmak için **SHIFT + enter** tuşlarını kullanın. Hücrenin sol tarafında, \* Bu hücredeki kodun yürütüldüğü belirten köşeli ayraçlar arasında bir ' ' görünür. İşlem tamamlandıktan sonra ' \* ' bir sayıya değişir ve aşağıdaki metne benzer bir çıkış hücrenin altında görüntülenir:

    ```output
    Creating SparkContext as 'sc'

    ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
    3    application_1468969497124_0001    spark    idle    Link    Link    ✔

    Creating HiveContext as 'sqlContext'
    SparkContext and HiveContext created. Executing user code ...
    ```

5. Birinci bir hücre aşağıda oluşturulur. Yeni hücreye aşağıdaki metni girin. `CONTAINER`Ve `STORAGEACCOUNT` , Application Insights günlükleri içeren blob kapsayıcısı adı ve Azure depolama hesabı adı ile değiştirin.

   ```scala
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Bu hücreyi yürütmek için **SHIFT + enter** tuşlarını kullanın. Aşağıdaki metne benzer bir sonuç görürsünüz:

    ```output
    Found 1 items
    drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
    ```

    Döndürülen IBS yolu, Application Insights Telemetri verilerinin konumudur. `hdfs dfs -ls`Döndürülen IBS yolunu kullanmak için hücredeki çizgiyi değiştirin ve sonra hücreyi yeniden çalıştırmak Için **SHIFT + enter** tuşlarını kullanın. Bu kez, sonuçlar telemetri verilerini içeren dizinleri görüntülemelidir.

   > [!NOTE]  
   > Bu bölümdeki adımların geri kalanı için `wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` Dizin kullanılmıştır. Telemetri verileriniz bir Web uygulaması için değilse, bu dizin mevcut olmayabilir.

6. Sonraki hücrede şu kodu girin: `WASB\_PATH` önceki adımdaki yol Ile değiştirin.

   ```scala
   var jsonFiles = sc.textFile('WASB_PATH')
   val sqlContext = new org.apache.spark.sql.SQLContext(sc)
   var jsonData = sqlContext.read.json(jsonFiles)
   ```

    Bu kod, sürekli dışarı aktarma işlemi tarafından dışarı aktarılmış JSON dosyalarından bir dataframe oluşturur. Bu hücreyi çalıştırmak için **SHIFT + enter** tuşlarını kullanın.

7. Bir sonraki hücrede, Spark 'ın JSON dosyaları için oluşturduğu şemayı görüntülemek için aşağıdakileri girin ve aşağıdakini çalıştırın:

   ```scala
   jsonData.printSchema
   ```

    Her telemetri türünün şeması farklıdır. Aşağıdaki örnek, Web istekleri için oluşturulan şemadır ( `Requests` alt dizinde depolanan veriler):

    ```output
    root
    |-- context: struct (nullable = true)
    |    |-- application: struct (nullable = true)
    |    |    |-- version: string (nullable = true)
    |    |-- custom: struct (nullable = true)
    |    |    |-- dimensions: array (nullable = true)
    |    |    |    |-- element: string (containsNull = true)
    |    |    |-- metrics: array (nullable = true)
    |    |    |    |-- element: string (containsNull = true)
    |    |-- data: struct (nullable = true)
    |    |    |-- eventTime: string (nullable = true)
    |    |    |-- isSynthetic: boolean (nullable = true)
    |    |    |-- samplingRate: double (nullable = true)
    |    |    |-- syntheticSource: string (nullable = true)
    |    |-- device: struct (nullable = true)
    |    |    |-- browser: string (nullable = true)
    |    |    |-- browserVersion: string (nullable = true)
    |    |    |-- deviceModel: string (nullable = true)
    |    |    |-- deviceName: string (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- osVersion: string (nullable = true)
    |    |    |-- type: string (nullable = true)
    |    |-- location: struct (nullable = true)
    |    |    |-- city: string (nullable = true)
    |    |    |-- clientip: string (nullable = true)
    |    |    |-- continent: string (nullable = true)
    |    |    |-- country: string (nullable = true)
    |    |    |-- province: string (nullable = true)
    |    |-- operation: struct (nullable = true)
    |    |    |-- name: string (nullable = true)
    |    |-- session: struct (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- isFirst: boolean (nullable = true)
    |    |-- user: struct (nullable = true)
    |    |    |-- anonId: string (nullable = true)
    |    |    |-- isAuthenticated: boolean (nullable = true)
    |-- internal: struct (nullable = true)
    |    |-- data: struct (nullable = true)
    |    |    |-- documentVersion: string (nullable = true)
    |    |    |-- id: string (nullable = true)
    |-- request: array (nullable = true)
    |    |-- element: struct (containsNull = true)
    |    |    |-- count: long (nullable = true)
    |    |    |-- durationMetric: struct (nullable = true)
    |    |    |    |-- count: double (nullable = true)
    |    |    |    |-- max: double (nullable = true)
    |    |    |    |-- min: double (nullable = true)
    |    |    |    |-- sampledValue: double (nullable = true)
    |    |    |    |-- stdDev: double (nullable = true)
    |    |    |    |-- value: double (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- name: string (nullable = true)
    |    |    |-- responseCode: long (nullable = true)
    |    |    |-- success: boolean (nullable = true)
    |    |    |-- url: string (nullable = true)
    |    |    |-- urlData: struct (nullable = true)
    |    |    |    |-- base: string (nullable = true)
    |    |    |    |-- hashTag: string (nullable = true)
    |    |    |    |-- host: string (nullable = true)
    |    |    |    |-- protocol: string (nullable = true)
    ```

8. Veri çerçevesini geçici bir tablo olarak kaydetmek ve verilere karşı bir sorgu çalıştırmak için aşağıdakileri kullanın:

   ```scala
   jsonData.registerTempTable("requests")
   var city = sqlContext.sql("select context.location.city from requests where context.location.city isn't null limit 10").show()
   ```

    Bu sorgu, Context. Location. City null olmayan ilk 20 kayıt için şehir bilgilerini döndürür.

   > [!NOTE]  
   > Bağlam yapısı, Application Insights tarafından günlüğe kaydedilen tüm telemetride mevcuttur. City öğesi günlüklerinizi doldurmayabilir. Günlüklerinizin verilerini içerebilen Sorgulayabileceğiniz diğer öğeleri belirlemek için şemayı kullanın.

    Bu sorgu, aşağıdaki metne benzer bilgiler döndürür:

    ```output
    +---------+
    |     city|
    +---------+
    | Bellevue|
    |  Redmond|
    |  Seattle|
    |Charlotte|
    ...
    +---------+
    ```

## <a name="next-steps"></a>Sonraki adımlar

Azure 'daki veri ve hizmetlerle çalışmak üzere Apache Spark kullanmaya yönelik daha fazla örnek için aşağıdaki belgelere bakın:

* [BI ile Apache Spark: bı araçlarıyla HDInsight 'ta Spark kullanarak etkileşimli veri çözümlemesi gerçekleştirme](apache-spark-use-bi-tools.md)
* [Machine Learning ile Apache Spark: HVAC verilerini kullanarak oluşturma sıcaklığını çözümlemek için HDInsight 'ta Spark kullanma](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning Apache Spark: yemek İnceleme sonuçlarını tahmin etmek için HDInsight 'ta Spark kullanma](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight 'ta Apache Spark kullanarak Web sitesi günlüğü Analizi](apache-spark-custom-library-website-log-analysis.md)

Spark uygulamaları oluşturma ve çalıştırma hakkında daha fazla bilgi için aşağıdaki belgelere bakın:

* [Scala kullanarak tek başına uygulama oluşturma](apache-spark-create-standalone-application.md)
* [Livy kullanarak Apache Spark kümesinde işleri uzaktan çalıştırma](apache-spark-livy-rest-interface.md)
