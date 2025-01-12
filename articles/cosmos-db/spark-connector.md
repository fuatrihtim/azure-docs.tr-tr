---
title: Apache Spark Azure Cosmos DB bağlama
description: Apache Spark Azure Cosmos DB bağlanmanızı sağlayan Azure Cosmos DB Spark Bağlayıcısı hakkında bilgi edinin.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: 06498a27b95a72148497efd2d1e600d802414359
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359566"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Apache Spark Azure Cosmos DB bağlayıcısını kullanarak büyük veri analizlerini hızlandırma
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

[Spark](https://spark.apache.org/) işlerini, Cosmos DB Spark bağlayıcısını kullanarak Azure Cosmos DB depolanan verilerle çalıştırabilirsiniz. Cosmos, toplu işlem ve akış işleme için ve düşük gecikmeli erişim için bir hizmet katmanı olarak kullanılabilir.

Bağlayıcıyı Azure 'da yönetilen Spark kümeleri sağlayan [Azure Databricks](https://azure.microsoft.com/services/databricks) veya [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/)ile kullanabilirsiniz. Aşağıdaki tabloda desteklenen Spark sürümleri gösterilmektedir.

| Bileşen | Sürüm |
|---------|-------|
| Apache Spark | 2.4. x, 2.3. x, 2.2. x ve 2.1. x |
| Scala | 2.11 |
| Azure Databricks çalışma zamanı sürümü | > 3,4 |

> [!WARNING]
> Bu bağlayıcı Azure Cosmos DB Çekirdek (SQL) API 'sini destekler.
> MongoDB API 'SI için Cosmos DB için [MongoDB Spark bağlayıcısını](https://docs.mongodb.com/spark-connector/master/)kullanın.
> Cosmos DB Cassandra API için [Cassandra Spark bağlayıcısını](https://github.com/datastax/spark-cassandra-connector)kullanın.

> [!IMPORTANT]
> Azure Cosmos DB Spark Bağlayıcısı Şu anda [sunucusuz](serverless.md) hesaplarda desteklenmez. Sunucusuz teklif genel kullanıma açık hale geldiği için bu işlem karşılanır.

## <a name="quickstart"></a>Hızlı Başlangıç

* Cosmos DB hesabı ayarlamak ve bazı verileri doldurmak için [Java SDK 'sını kullanmaya başlama](./create-sql-api-java.md) bölümündeki adımları izleyin.
* Azure Databricks bir çalışma alanı ve küme ayarlamaya Başlarken [Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) adımları izleyin.
* Artık yeni not defterleri oluşturabilir ve Cosmos DB bağlayıcı kitaplığını içeri aktarabilirsiniz. Çalışma alanınızı ayarlama hakkında ayrıntılar için [Cosmos DB Bağlayıcısı Ile çalışmaya](#bk_working_with_connector) atlayın.
* Aşağıdaki bölümde, bağlayıcıyı kullanarak okuma ve yazma ile ilgili kod parçacıkları vardır.

### <a name="batch-reads-from-cosmos-db"></a>Cosmos DB 'ten toplu iş okuma

Aşağıdaki kod parçacığında, PySpark içinde Cosmos DB okumak için Spark veri çerçevesinin nasıl oluşturulacağı gösterilmektedir.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "query_custom": "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format(
    "com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

Ve Scala 'da aynı kod parçacığı:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "query_custom" -> "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
))

// Connect via azure-cosmosdb-spark to create Spark DataFrame
val flights = spark.read.cosmosDB(readConfig)
flights.count()
```

### <a name="batch-writes-to-cosmos-db"></a>Cosmos DB için toplu işlem yazma

Aşağıdaki kod parçacığında, PySpark içinde Cosmos DB bir veri çerçevesinin nasıl yazılacağı gösterilmektedir.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.mode("overwrite").format("com.microsoft.azure.cosmosdb.spark").options(
    **writeConfig).save()
```

Ve Scala 'da aynı kod parçacığı:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true"
))

// Write to Cosmos DB from the flights DataFrame
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

### <a name="streaming-reads-from-cosmos-db"></a>Cosmos DB akış okuma

Aşağıdaki kod parçacığında Azure Cosmos DB değişiklik akışına bağlanma ve buradan okuma işlemlerinin nasıl yapılacağı gösterilmektedir.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "ReadChangeFeed": "true",
    "ChangeFeedQueryName": "Departure-Delays",
    "ChangeFeedStartFromTheBeginning": "false",
    "InferStreamSchema": "true",
    "ChangeFeedCheckpointLocation": "dbfs:/Departure-Delays"
}


# Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
changes = (spark
           .readStream
           .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSourceProvider")
           .options(**readConfig)
           .load())
```
Ve Scala 'da aynı kod parçacığı:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "ReadChangeFeed" -> "true",
  "ChangeFeedQueryName" -> "Departure-Delays",
  "ChangeFeedStartFromTheBeginning" -> "false",
  "InferStreamSchema" -> "true",
  "ChangeFeedCheckpointLocation" -> "dbfs:/Departure-Delays"
))

// Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
val df = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(readConfig).load()
```

### <a name="streaming-writes-to-cosmos-db"></a>Cosmos DB için akış yazma

Aşağıdaki kod parçacığında, PySpark içinde Cosmos DB bir veri çerçevesinin nasıl yazılacağı gösterilmektedir.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true",
    "WritingBatchSize": "500",
    "CheckpointLocation": "/checkpointlocation_write1"
}

# Write to Cosmos DB from the flights DataFrame
changeFeed = (changes
              .writeStream
              .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSinkProvider")
              .outputMode("append")
              .options(**writeconfig)
              .start())
```

Ve Scala 'da aynı kod parçacığı:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true",
  "WritingBatchSize" -> "500",
  "CheckpointLocation" -> "/checkpointlocation_write1"
))

// Write to Cosmos DB from the flights DataFrame
df
.writeStream
.format(classOf[CosmosDBSinkProvider].getName)
.options(writeConfig)
.start()
```
Daha fazla kod parçacığı ve uçtan uca örnek, bkz. [Jupyıter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="working-with-the-connector"></a><a name="bk_working_with_connector"></a> Bağlayıcıyla çalışma

Bağlayıcıyı GitHub 'da kaynaktan oluşturabilir veya aşağıdaki bağlantılardan Maven 'ten Uber jar dosyaları dışındaki 'ı indirebilirsiniz.

| Spark | Scala | En son sürüm |
|---|---|---|
| 2.4.0 | 2.11 | [Azure-cosmosdb-spark_lkg_version](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG)
| 2.3.0 | 2.11 | [Azure-cosmosdb-spark_2.3.0 _2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [Azure-cosmosdb-spark_2.2.0 _2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [Azure-cosmosdb-spark_2.1.0 _2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Databricks not defterlerini kullanma

Azure Databricks kılavuzundaki kılavuzu izleyerek Databricks çalışma alanınızı kullanarak bir kitaplık oluşturun > [Azure Cosmos DB Spark bağlayıcısını kullanın](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> **Azure Cosmos DB Spark bağlayıcısını kullan** sayfası şu anda güncel değil. Altı ayrı jar dosyaları dışındaki altı farklı kitaplıklara indirmek yerine, Maven 'ten [Azure-cosmosdb-spark_lkg_version](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) adresinden Uber jar 'i indirebilir ve bu tek bir jar/Library yükleyebilirsiniz.
> 

### <a name="using-spark-cli"></a>Spark-CLI kullanma

Spark-CLI (,,,) kullanarak bağlayıcı ile çalışmak için `spark-shell` , bu `pyspark` `spark-submit` `--packages` parametreyi bağlayıcının [Maven koordinatlarıyla](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11)birlikte kullanabilirsiniz.

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>Jupyıter not defterlerini kullanma

HDInsight içinde Jupyter Notebook kullanıyorsanız, `%%configure` bağlayıcının Maven koordinatlarını belirtmek için Spark-Magic hücresini kullanabilirsiniz.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Öğesinin dahil edilmesi, `spark.jars.excludes` bağlayıcı, Apache Spark ve Livy arasındaki olası çakışmaları kaldırmak için özeldir.

### <a name="build-the-connector"></a>Bağlayıcıyı oluşturma

Şu anda bu bağlayıcı proje `maven` , bağımlılık olmadan derlemek için bu şekilde kullanıyor, şunu çalıştırabilirsiniz:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Örneklerimizde çalışma

[Cosmos DB Spark GitHub deposunda](https://github.com/Azure/azure-cosmosdb-spark) , deneyebileceğiniz aşağıdaki örnek Not defterleri ve betikler bulunur.

* **Spark ve Cosmos DB (Seattle)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb)HTML ile zaman içinde uçuş performansı  |  [](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html): Spark SQL, graphframes 'i göstermek ve ml işlem hatlarını kullanarak uçuş gecikmelerini tahmin etmek için HDInsight Jupyter Notebook hizmetini kullanarak Spark 'ı Cosmos DB bağlayın.
* **Apache Spark ve Azure Cosmos DB değişiklik akışı ile Twitter kaynağı**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb)  |  [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Cosmos DB grafiklerini sorgulamak için Apache Spark kullanma**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb)  |  [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* Kullanarak **[Azure Cosmos DB Azure Databricks bağlama](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** `azure-cosmosdb-spark` .  Buraya bağlı, Ayrıca, [Şirket Içi uçuş performansı Not defterinin](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks)Azure Databricks bir sürümüdür.
* **[Azure Cosmos DB ve HDInsight Ile lambda mimarisi (Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)**: Cosmos DB ve Spark kullanarak büyük veri işlem hatlarını korumanın işletimsel yükünü azaltabilirsiniz.

## <a name="more-information"></a>Daha Fazla Bilgi

`azure-cosmosdb-spark` [Wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki) 'de şunlar da dahil olmak üzere daha fazla bilgi sunuyoruz:

* [Azure Cosmos DB Spark Bağlayıcısı Kullanıcı Kılavuzu](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Toplamalar örnekleri](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Yapılandırma ve Kurulum

* [Spark Bağlayıcısı yapılandırması](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Cosmos DB bağlayıcı kurulumuna Spark](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (devam ediyor)
* [Apache Spark ile Azure Cosmos DB doğrudan sorgu Power BI yapılandırma (HDI)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Sorun giderme

* [Cosmos DB toplamaları kullanma](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Bilinen Sorunlar](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Performans

* [Performans Ipuçları](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Sorgu testi çalıştırmaları](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Test çalıştırmaları yazılıyor](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Değişiklik Akışı

* [Azure Cosmos DB değişiklik akışını ve Apache Spark kullanarak akış Işleme değişiklikleri](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Akış tanıtımları değiştirme](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Yapılandırılmış akış gösterileri](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>İzleme

* [Application Insights ile Spark işlerini izleme](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Sonraki adımlar

Henüz yapmadıysanız, [Azure-cosmosdb-Spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub deposundan Spark Azure Cosmos DB bağlayıcısını indirin. Depoda aşağıdaki ek kaynakları keşfet:

* [Toplamalar örnekleri](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Örnek betikler ve Not defterleri](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Ayrıca, [SQL, DataFrames ve veri kümeleri kılavuzunu](https://spark.apache.org/docs/latest/sql-programming-guide.html)ve [Azure hdınsight 'ta Apache Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) Apache Spark gözden geçirmek isteyebilirsiniz.