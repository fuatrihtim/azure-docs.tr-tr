---
title: 1 TB veri kümesi üzerinde Azure HDInsight Hadoop kümesi kullanma-takım veri bilimi Işlemi
description: Büyük (1 TB) genel kullanıma açık veri kümesi kullanarak bir model oluşturup dağıtmak için bir HDInsight Hadoop kümesi kullanan uçtan uca bir senaryo için Team Data Science Işlemini kullanma
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e66bd0a4e56f63185d8361355d6cf8e0e29bc30b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93305931"
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>Ekip veri bilimi Işlemi, 1 TB 'lik bir veri kümesinde Azure HDInsight Hadoop kümesi kullanılarak yapılır.

Bu izlenecek yol, genel kullanıma açık olan [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) veri kümelerinden birindeki örnek verileri depolamak, araştırmak, özellik mühendisliğini ve daha aşağı almak Için, takım veri bilimi sürecini bir [Azure HDInsight Hadoop kümesiyle](https://azure.microsoft.com/services/hdinsight/) birlikte nasıl kullanacağınızı gösterir. Bu verilerde ikili bir sınıflandırma modeli oluşturmak için Azure Machine Learning kullanır. Ayrıca, bu modellerden birinin bir Web hizmeti olarak nasıl yayımlanacağını gösterir.

Bu kılavuzda sunulan görevleri gerçekleştirmek için bir IPython Not defteri kullanmak da mümkündür. Bu yaklaşımı denemek isteyen kullanıcılar, [HIVE ODBC bağlantısı Ile Criteo talimatına](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) danışmalıdır.

## <a name="criteo-dataset-description"></a><a name="dataset"></a>Criteo DataSet açıklaması
Criteo verileri, 4.300.000.000 ' den fazla kayıttan oluşan 370 GB gzip sıkıştırılmış TSV dosyası (~ 1,3 TB sıkıştırılmamış) olan bir tıklama tahmini veri kümesidir. Bu, [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/)tarafından kullanılabilir hale getirilen 24 günden fazla tıklama verisi üzerinden alınır. Veri bilimcilerinin kolay olması için, bizim için deneydiğimiz veriler sıkıştırıldı.

Bu veri kümesindeki her kayıt 40 sütun içerir:

* ilk sütun, kullanıcının bir **Ekle** (değer 1) tıkladığını veya bir tıklamadığını (değer 0) gösteren bir etiket sütunudur.
* sonraki 13 sütun sayısal ve
* son 26 kategorik sütunlardır

Sütunlar anonimdir ve bir dizi numaralandırılmış ad kullanır: "Sütun1" (etiket sütunu için) ' Col40 ' (son kategorik sütun için).

Bu veri kümesindeki iki gözlemden (satır) ilk 20 sütunun bir alıntısı aşağıda verilmiştir:

> `Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20`
>
> `0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb`
>
> `0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb`

Bu veri kümesindeki hem sayısal hem de kategorik sütunlarda eksik değerler var. Eksik değerleri işlemek için basit bir yöntem açıklanır. Verilerin ek ayrıntıları, Hive tablolarına depolarken araştırılabilir.

**Tanım:** *tıklama oranı (Mrk):* Bu ölçüm, verilerdeki tıklama yüzdesinin yüzdesidir. Bu Criteo DataSet 'de, MRK% 3,3 veya 0,033 ' dir.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Tahmin görevlerinin örnekleri
Bu izlenecek yolda iki örnek tahmin sorunu giderilmiştir:

1. **İkili sınıflandırma**: bir kullanıcının bir ekleme tıkladığını tahmin eder:

   * Sınıf 0: tıklama yok
   * Sınıf 1: tıklama
2. **Gerileme**: Kullanıcı özelliklerinden bir ad tıklama olasılığını tahmin eder.

## <a name="set-up-an-hdinsight-hadoop-cluster-for-data-science"></a><a name="setup"></a>Veri bilimi için bir HDInsight Hadoop kümesi ayarlama
> [!NOTE]
> Bu adım genellikle bir **Yönetim** görevidir.

Üç adımda HDInsight kümeleriyle tahmine dayalı analiz çözümleri oluşturmak için Azure veri bilimi ortamınızı ayarlayın:

1. [Depolama hesabı oluştur](../../storage/common/storage-account-create.md): Bu depolama hesabı, verileri Azure Blob depolama alanında depolamak için kullanılır. HDInsight kümelerinde kullanılan veriler burada depolanır.
2. [Veri bilimi için Azure HDInsight Hadoop kümelerini özelleştirme](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md): Bu adım, tüm düğümlerde yüklü 64 bit Anaconda Python 2,7 ile bir Azure HDInsight Hadoop kümesi oluşturur. HDInsight kümesini özelleştirirken tamamlanacak iki önemli adım (Bu konu başlığı altında açıklanmıştır) vardır.

   * Adım 1 ' de oluşturulan depolama hesabını HDInsight kümenizin oluşturulduğu sırada bağlayın. Bu depolama hesabı, küme içinde işlenebilecek verilere erişmek için kullanılır.
   * Oluşturulduktan sonra kümenin baş düğümüne uzaktan erişimi etkinleştirin. Burada belirttiğiniz uzaktan erişim kimlik bilgilerini hatırlayın (küme oluşturulurken belirtilen kimlik bilgilerinden farklı): aşağıdaki yordamları uygulayın.
3. [Azure Machine Learning Studio (klasik) çalışma alanı oluştur](../classic/create-workspace.md): Bu Azure Machine Learning çalışma alanı, HDInsight kümesindeki bir ilk veri araştırması ve azaltma örneklemesi sonrasında makine öğrenimi modelleri oluşturmak için kullanılır.

## <a name="get-and-consume-data-from-a-public-source"></a><a name="getdata"></a>Ortak bir kaynaktan veri edinme ve bunları kullanma
Bağlantı kümesine tıklayarak, kullanım koşullarını kabul ederek ve bir ad sağlayarak [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) DataSet 'e erişilebilir. Burada bir anlık görüntü gösterilir:

![Criteo koşullarını kabul edin](./media/hive-criteo-walkthrough/hLxfI2E.png)

Veri kümesi ve kullanılabilirliği hakkında daha fazla bilgi için **indirmek Için devam** ' a tıklayın.

Veriler bir [Azure Blob depolama](../../storage/blobs/storage-quickstart-blobs-dotnet.md) konumunda bulunuyor: wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/ . "SLA b", Azure Blob depolama konumunu ifade eder.

1. Bu Azure Blob depolama alanındaki veriler, daraltılmış verilerin üç alt grubundan oluşur.

   1. *Ham/Count/* alt klasörü, \_ 00 ila gün 20 ' nin ilk 21 günü veri içerir \_
   2. *Ham/eğit* /bir günden oluşan alt klasör, gün \_ 21
   3. *Ham/test/* alt klasörü iki günden oluşur, gün \_ 22 ve gün \_ 23
2. Ham gzip verileri, *RAW/* as day_NN. gz ana klasöründe de mevcuttur, burada nn 00 ile 23 arasında gider.

Herhangi bir yerel indirme gerektirmeyen bu verileri erişmek, araştırmak ve modellemek için alternatif bir yaklaşım, Hive tabloları oluştururken bu izlenecek yolda açıklanacaktır.

## <a name="log-in-to-the-cluster-headnode"></a><a name="login"></a>Küme baş düğümüne 'da oturum açın
Kümenin baş düğümüne ' da oturum açmak için, kümeyi bulmak için [Azure Portal](https://ms.portal.azure.com) kullanın. Sol taraftaki HDInsight fil simgesine tıklayın ve ardından kümenizin adına çift tıklayın. **Yapılandırma** sekmesine gidin, sayfanın altındaki Bağlan simgesine çift tıklayın ve istendiğinde, bu bilgileri kümenin baş düğümüne ' a götüryerek uzaktan erişim kimlik bilgilerinizi girin.

Küme oluşturma düğümündeki tipik bir ilk oturum açma işlemi şöyle görünür:

![Kümede oturum açma](./media/hive-criteo-walkthrough/Yys9Vvm.png)

Sol tarafta, veri araştırması için çalışmamız olan "Hadoop komut satırı" bulunur. İki yararlı URL-"Hadoop Yarn durumu" ve "Hadoop adı düğümü" gibi dikkat edin. Yarn durum URL 'SI iş ilerlemesini gösterir ve ad düğümü URL 'SI küme yapılandırması üzerinde ayrıntılar verir.

Şimdi ayarladığınız ve ilk kez başlamaya hazırsınız: Hive kullanarak veri araştırması ve Azure Machine Learning için hazır veri alma.

## <a name="create-hive-database-and-tables"></a><a name="hive-db-tables"></a> Hive veritabanı ve tabloları oluşturma
Criteo veri kümeniz için Hive tabloları oluşturmak üzere baş düğümün masaüstündeki ***Hadoop komut satırını*** açın ve komutu girerek Hive dizinini girin

```console
cd %hive_home%\bin
```

> [!NOTE]
> Bu izlenecek yolda bulunan tüm Hive komutlarını Hive bin/Dizin isteminden çalıştırın. Bu, tüm yol sorunlarını otomatik olarak gerçekleştirir. "Hive Dizin istemi", "Hive bin/Dizin istemi" ve "Hadoop komut satırı" terimlerini birbirinin yerine kullanabilirsiniz.
>
> [!NOTE]
> Herhangi bir Hive sorgusunu yürütmek için, birisi her zaman aşağıdaki komutları kullanabilir:
>
> `cd %hive_home%\bin`
> `hive`

Hive REPL bir "Hive >" işareti ile görüntülendikten sonra, sorguyu yürütmek için kesip yapıştırmanız yeterlidir.

Aşağıdaki kod, "Criteo" veritabanını oluşturur ve ardından dört tablo oluşturur:

* gün 00 ile 20 gün arasında oluşturulan *sayıları oluşturmak için bir tablo* \_ \_ ,
* 21 *. günde oluşturulan eğitme veri kümesi olarak kullanılacak bir tablo* \_ ve
* için iki tablo, sırasıyla 22 ve gün 23 gün içinde oluşturulmuş *test veri kümeleri olarak kullanılacak* \_ \_ .

Her gün bir tatil olduğundan, test veri kümesini iki farklı tabloya ayırın. Amaç, modelin tatil ve tatil arasındaki farkları tıklama oranlarından algılamamasını belirlemektir.

Betik [örneği&#95;hive&#95;,&#95;Criteo&#95;veritabanı&#95;ve&#95;tabloları oluştur. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) , burada kolaylık sağlaması için görüntülenir:

```hiveql
CREATE DATABASE IF NOT EXISTS criteo;
DROP TABLE IF EXISTS criteo.criteo_count;
CREATE TABLE criteo.criteo_count (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

DROP TABLE IF EXISTS criteo.criteo_train;
CREATE TABLE criteo.criteo_train (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

DROP TABLE IF EXISTS criteo.criteo_test_day_22;
CREATE TABLE criteo.criteo_test_day_22 (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

DROP TABLE IF EXISTS criteo.criteo_test_day_23;
CREATE TABLE criteo.criteo_test_day_23 (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';
```

Tüm bu tablolar, Azure Blob depolama (ILB) konumlarına işaret edebilmeniz için dış bir yerdedir.

**Hive sorgusu yürütmek için iki yol vardır:**

* **HIVE REPL komut satırını kullanarak**: Birincisi bir "Hive" komutu vermektir ve Hive REPL komut satırına bir sorgu kopyalayıp yapıştırmaktır:

  ```console
  cd %hive_home%\bin
  hive
  ```

     Şimdi REPL komut satırında, sorguyu kesip yapıştırarak yürütür.
* **Sorguları bir dosyaya kaydetme ve komutu yürütme**: İkincisi, sorguları bir '. HQL ' dosyasına ([örnek&#95;Hive&#95;&#95;criteo&#95;veritabanı&#95;ve&#95;tabloları. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) kaydederek sorguyu yürütmek için aşağıdaki komutu yayınlayamaz:

  ```console
  hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql
  ```

### <a name="confirm-database-and-table-creation"></a>Veritabanı ve tablo oluşturmayı Onayla
Sonra, Hive bin/Dizin isteminden aşağıdaki komutla veritabanı oluşturmayı onaylayın:

```console
hive -e "show databases;"
```

Bu şunları sağlar:

```output
criteo
default
Time taken: 1.25 seconds, Fetched: 2 row(s)
```

Bu, "Criteo" yeni veritabanının oluşturulmasını onaylar.

Hangi tabloların oluşturulduğunu görmek için Hive bin/Dizin isteminde komutu burada yapmanız yeterlidir:

```output
hive -e "show tables in criteo;"
```

Ardından aşağıdaki çıktıyı görmeniz gerekir:

```ouput
criteo_count
criteo_test_day_22
criteo_test_day_23
criteo_train
Time taken: 1.437 seconds, Fetched: 4 row(s)
```

## <a name="data-exploration-in-hive"></a><a name="exploration"></a> Hive 'de veri araştırması
Artık Hive 'de bazı temel veri araştırması yapmaya hazırsınız. Tren ve test veri tablolarındaki örneklerin sayısını sayarak başlarsınız.

### <a name="number-of-train-examples"></a>Tren örnekleri sayısı
[Örnek&#95;hive&#95;count&#95;&#95;tabloyu eğitme örnekleri&#95;örnekler. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) burada gösterilmektedir:

```hiveql
SELECT COUNT(*) FROM criteo.criteo_train;
```

Bu şunları verir:

```output
192215183
Time taken: 264.154 seconds, Fetched: 1 row(s)
```

Alternatif olarak, bir diğeri de Hive bin/Dizin isteminden aşağıdaki komutu verebilir:

```console
hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql
```

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>İki test veri kümelerinde test örneklerinin sayısı
Şimdi iki test veri kümesinin örnek sayısını Sayın. [Örnek&#95;Hive içeriği&#95;count&#95;Criteo&#95;test&#95;günü&#95;22&#95;tablo&#95;örnek. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) burada verilmiştir:

```hiveql
SELECT COUNT(*) FROM criteo.criteo_test_day_22;
```

Bu şunları verir:

```output
189747893
Time taken: 267.968 seconds, Fetched: 1 row(s)
```

Her zamanki gibi, komutu vererek Hive bin/Dizin isteminden betiği de çağırabilirsiniz:

```console
hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql
```

Son olarak, test veri kümesindeki test örneklerinin sayısını 23. gün temelinde inceleyebilirsiniz \_ .

Bunu yapmak için komut, [gösterilenle benzerdir (örnek&#95;hive&#95;count&#95;Criteo&#95;test&#95;günü&#95;23&#95;örnek. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

```hiveql
SELECT COUNT(*) FROM criteo.criteo_test_day_23;
```

Bu şunları sağlar:

```output
178274637
Time taken: 253.089 seconds, Fetched: 1 row(s)
```

### <a name="label-distribution-in-the-train-dataset"></a>Tren veri kümesinde etiket dağılımı
Tren veri kümesindeki etiket dağıtımı ilgi alanıdır. Bunu görmek için [örnek&#95;Hive içeriğini göster&#95;Criteo&#95;etiket&#95;dağıtım&#95;eğit. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

```hiveql
SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;
```

Bu, etiket dağılımını verir:

```output
1       6292903
0       185922280
Time taken: 459.435 seconds, Fetched: 2 row(s)
```

Pozitif etiketlerin yüzdesi% 3,3 ' dir (özgün veri kümesiyle tutarlıdır).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Tren veri kümesindeki bazı sayısal değişkenlerin histogram dağıtımları
\_Sayısal değişkenlerin dağılımının ne şekilde göründüğünü öğrenmek Için Hive 'nin yerel "histogram sayısal" işlevini kullanabilirsiniz. [Örnek&#95;hive&#95;Criteo&#95;histogram&#95;sayısal. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql)içeriğini burada bulabilirsiniz:

```hiveql
SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
    (SELECT
    histogram_numeric(col2, 20) as col2_hist
    FROM
    criteo.criteo_train
    ) a
    LATERAL VIEW explode(col2_hist) exploded_table as hist;
```

Bu, aşağıdakileri verir:

```output
26      155878415
2606    92753
6755    22086
11202   6922
14432   4163
17815   2488
21072   1901
24113   1283
27429   1225
30818   906
34512   723
38026   387
41007   290
43417   312
45797   571
49819   428
53505   328
56853   527
61004   160
65510   3446
Time taken: 317.851 seconds, Fetched: 20 row(s)
```

Hive içindeki yan yana görünümü-aç kombinasyonu, her zamanki liste yerine SQL benzeri bir çıkış üretmeye çalışır. Bu tabloda, ilk sütun bin merkezine ve ikincisi de bin frekansına karşılık gelir.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Tren veri kümesindeki bazı sayısal değişkenlerin yaklaşık yüzdebirlik değeri
Sayısal değişkenlerle ilgili Ayrıca, yaklaşık yüzdebirlik değeri hesaplama. Hive 'in yerel "yüzdebirlik \_ yaklaşık" değeri bizim için bunu yapar. [Örnek&#95;hive&#95;Criteo&#95;yaklaşık&#95;yüzdebirlik değeri. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) ' nin içerikleri şunlardır:

```hiveql
SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;
```

Bu şunları verir:

```output
1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
Time taken: 564.953 seconds, Fetched: 1 row(s)
```

Yüzdebirlik değeri dağıtımı, genellikle herhangi bir sayısal değişkenin histogram dağılımı ile ilgilidir.

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Eğitme veri kümesindeki bazı kategorik sütunların benzersiz değerlerinin sayısını bulma
Veri araştırmasına devam etmek, bazı kategorik sütunlar için, aldıkları benzersiz değerlerin sayısını bulun. Bunu yapmak için, [örnek&#95;Hive içeriğini&#95;Criteo&#95;benzersiz&#95;değerleri&#95;categoricals. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql)) görüntüleyin:

```hiveql
SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;
```

Bu şunları verir:

```output
19011825
Time taken: 448.116 seconds, Fetched: 1 row(s)
```

Col15, 19D benzersiz değerlere sahip! Bu tür yüksek boyutlu kategorik değişkenleri kodlamak için "tek yönlü kodlama" gibi Naïve tekniklerin kullanılması uygulanamaz. Özellikle, bu sorunu etkili bir şekilde [öğrenirken, öğrenme](/archive/blogs/machinelearning/big-learning-made-easy-with-counts) adlı güçlü ve güçlü bir tekniktir.

Son olarak bazı diğer kategorik sütunlar için benzersiz değer sayısına göz atın. [Örnek&#95;hive&#95;Criteo&#95;, birden çok&#95;categoricals. HQL&#95;benzersiz&#95;değerleri](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) olan içerikleri şunlardır:

```hiveql
SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
FROM criteo.criteo_train;
```

Bu şunları verir:

```output
30935   15200   7349    20067   3
Time taken: 1933.883 seconds, Fetched: 1 row(s)
```

Yine, Col20 dışında, diğer tüm sütunların çok sayıda benzersiz değeri olduğunu unutmayın.

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>Eğitme veri kümesindeki kategorik değişkenlerin çiftlerinin ortak oluşma sayısı

Kategorik değişkenlerin çiftlerinin sayı dağıtımları da ilgi alanıdır. Bu, [örnek&#95;hive&#95;Criteo&#95;eşleştirilmiş&#95;kategorik&#95;sayılarını. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql)içindeki kodu kullanarak belirlenebilir:

```hiveql
SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;
```

Sayıları kendi oluşumlarına göre ters sıralayın ve bu durumda en üstteki 15 ' i bulun. Bu bizi bize verir:

```output
ad98e872        cea68cd3        8964458
ad98e872        3dbb483e        8444762
ad98e872        43ced263        3082503
ad98e872        420acc05        2694489
ad98e872        ac4c5591        2559535
ad98e872        fb1e95da        2227216
ad98e872        8af1edc8        1794955
ad98e872        e56937ee        1643550
ad98e872        d1fade1c        1348719
ad98e872        977b4431        1115528
e5f3fd8d        a15d1051        959252
ad98e872        dd86c04a        872975
349b3fec        a52ef97d        821062
e5f3fd8d        a0aaffa6        792250
265366bf        6f5c7c41        782142
Time taken: 560.22 seconds, Fetched: 15 row(s)
```

## <a name="down-sample-the-datasets-for-azure-machine-learning"></a><a name="downsample"></a> Azure Machine Learning için veri kümelerini aşağı örnekle
Veri kümelerini keşfetme ve herhangi bir değişken (birleşimler dahil) için bu tür bir araştırma yapmak için, Azure Machine Learning içindeki modellerin derlenebilir olması için veri kümelerini aşağı doğru örnekle. Sorunun odağının: bir örnek öznitelikler kümesi (col2-Col40 ' den özellik değerleri) verildiğinde, Sütun1 'nin 0 (Hayır) veya 1 ' e (tıklasın) göre tahmin edilir.

Örnek olarak, tren ve test veri kümelerini özgün boyutun %1 ' i ile örneklemek için Hive 'in yerel S_SAYI_ÜRET () işlevini kullanın. Sonraki betik, [örnek&#95;hive&#95;Criteo&#95;aşağı örnek&#95;eğitme&#95;veri kümesini eğitme. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) bunu eğitme veri kümesi için yapar:

```hiveql
CREATE TABLE criteo.criteo_train_downsample_1perc (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE;

---Now downsample and store in this table

INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;
```

Bu şunları verir:

```output
Time taken: 12.22 seconds
Time taken: 298.98 seconds
```

Betik [örneği&#95;hive&#95;Criteo&#95;aşağı örnek&#95;test&#95;gün&#95;22&#95;veri kümesi. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) bu dosyayı test verileri, 22. gün için yapar \_ :

```hiveql
--- Now for test data (day_22)

CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE;

INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;
```

Bu şunları verir:

```output
Time taken: 1.22 seconds
Time taken: 317.66 seconds
```

Son olarak, komut dosyası [örneği&#95;hive&#95;Criteo&#95;aşağı örnek&#95;test&#95;gün&#95;23&#95;veri kümesi. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) bunu test verileri, 23. gün için yapar \_ :

```hiveql
--- Finally test data day_23
CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE;

INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;
```

Bu şunları verir:

```output
Time taken: 1.86 seconds
Time taken: 300.02 seconds
```

Bu şekilde, Azure Machine Learning ' de modeller oluşturmak için, alt örneklenmiş eğimlerimizi ve test veri kümelerini kullanmaya hazırsınızdır.

Azure Machine Learning 'e geçmeden önce son önemli bir bileşen vardır. Bu, sayı tablosu ile ilgilidir. Sonraki alt bölümde, Count tablosu bazı ayrıntılarla ele alınmıştır.

## <a name="a-brief-discussion-on-the-count-table"></a><a name="count"></a> Count tablosu hakkında kısa bir tartışma
Gördüğünüz gibi, çeşitli kategorik değişkenlerin yüksek boyutlılık vardır. İzlenecek yolda, bu değişkenleri verimli ve sağlam bir şekilde kodlamak için [sayımlar Ile öğrenme](/archive/blogs/machinelearning/big-learning-made-easy-with-counts) adlı güçlü bir teknik sunulmaktadır. Bu teknik hakkında daha fazla bilgi, girilen bağlantıdır.

>[!NOTE]
>Bu kılavuzda, odak, yüksek boyutlu kategorik özelliklerin küçük temsillerini oluşturmak için Count tabloları kullanmaktır. Kategorik özellikleri kodlamak için tek yol bu değildir; diğer teknikler hakkında daha fazla bilgi edinmek için, kullanıcılar bir çok [yönlü kodlama](https://en.wikipedia.org/wiki/One-hot) ve [özellik karma](https://en.wikipedia.org/wiki/Feature_hashing)'i kullanıma alabilir.
>

Sayı verilerinde sayı tabloları oluşturmak için, ham/sayı klasöründeki verileri kullanın. Modelleme bölümünde, kullanıcılar kategorik özellikler için bu sayım tablolarının nasıl oluşturulacağını veya alternatif olarak, araştırmalar için önceden oluşturulmuş bir sayım tablosu kullanımını göstermiştir. Aşağıdaki şekilde, "önceden oluşturulmuş sayım tabloları" dendiğinde, sağlanmış olan Count tablolarını kullanırız. Bu tablolara erişme hakkında ayrıntılı yönergeler sonraki bölümde verilmiştir.

## <a name="build-a-model-with-azure-machine-learning"></a><a name="aml"></a> Azure Machine Learning bir model oluşturun
Model oluşturma sürecimiz Azure Machine Learning şu adımları izler:

1. [Hive tablolarından verileri Azure Machine Learning içine alın](#step1)
2. [Deneme oluşturma: verileri temizleme ve sayı tablolarının bulunduğu bir özellik yapma](#step2)
3. [Model oluşturma, eğitme ve Puanlama](#step3)
4. [Modeli değerlendirme](#step4)
5. [Modeli Web hizmeti olarak yayımlama](#step5)

Artık Azure Machine Learning Studio 'da model oluşturmaya hazırsınız. Aşağı örneklenmiş verilerimiz, kümeye Hive tabloları olarak kaydedilir. Bu verileri okumak için Azure Machine Learning **veri alma** modülünü kullanın. Bu kümenin depolama hesabına erişmek için gereken kimlik bilgileri aşağıda verilmiştir.

### <a name="step-1-get-data-from-hive-tables-into-azure-machine-learning-using-the-import-data-module-and-select-it-for-a-machine-learning-experiment"></a><a name="step1"></a> 1. Adım: verileri Içeri aktarma modülünü kullanarak Hive Azure Machine Learning tablolarından veri alma ve makine öğrenimi denemesi için seçme
**Yeni** bir  ->  **deneme**  ->  **boş deneme** seçerek başlayın. Ardından, sol üstteki **arama** kutusundan "verileri içeri aktar" ifadesini aratın. Veri erişimi modülünü kullanmak için **veri Içeri aktarma** modülünü deneme tuvaline (ekranın orta bölümü) sürükleyin ve bırakın.

Yığın tablosundan veri alırken **Içeri aktarma verileri** şöyle görünür:

![Verileri içeri aktarma verileri alır](./media/hive-criteo-walkthrough/i3zRaoj.png)

**Veri Içeri aktarma** modülü için, grafikte sunulan parametrelerin değerleri, sağlamanız gereken değer sıralamasına yalnızca örnektir. **Veri Içeri aktarma** modülünün parametre kümesini doldurmaya ilişkin bazı genel yönergeler aşağıda verilmiştir.

1. **Veri kaynağı** Için "Hive sorgusu" seçin
2. **Hive veritabanı sorgu** kutusunda, veritabanınızın adını <basıt bir seçim * \_ \_ . \_ tablo \_ adı>-yeterlidir.
3. **Hcatalog sunucusu URI 'si**: kümeniz "abc" ise, bu yalnızca: https: \/ /ABC.azurehdinsight.net
4. **Hadoop Kullanıcı hesabı adı**: kümeyi komisyonlama sırasında seçilen Kullanıcı adı. (Uzaktan Erişim Kullanıcı adı DEĞIL!)
5. **Hadoop Kullanıcı hesabı parolası**: kümeyi komisyonlama sırasında seçilen kullanıcı adının parolası. (Uzaktan erişim parolası DEĞIL!)
6. **Çıkış verilerinin konumu**: "Azure" seçeneğini belirleyin
7. **Azure depolama hesabı adı**: kümeyle ilişkili depolama hesabı
8. **Azure depolama hesabı anahtarı**: kümeyle ilişkili depolama hesabının anahtarı.
9. **Azure kapsayıcı adı**: küme adı "abc" ise, bu, genellikle "abc" olur.

**Içeri aktarma verileri** veri almayı tamamladığında (modülde yeşil onay işareti görürsünüz), bu verileri bir veri kümesi olarak (seçtiğiniz bir adla) kaydedin. Bu şöyle görünür:

![Verileri içeri aktarma verileri kaydetme](./media/hive-criteo-walkthrough/oxM73Np.png)

**Veri Içeri aktarma** modülünün çıkış bağlantı noktasına sağ tıklayın. Bu, bir **veri kümesi olarak kaydet** seçeneği ve bir **Görselleştir** seçeneği ortaya koyar. Tıklama **seçeneği,** tıklandıysanız, bazı Özet istatistikleri için yararlı olan doğru bir panelle birlikte, verilerin 100 satırlarını görüntüler. Verileri kaydetmek için veri **kümesi olarak kaydet** ' i seçin ve yönergeleri izleyin.

Makine öğrenimi denemesinde kullanılacak kaydedilmiş veri kümesini seçmek için aşağıdaki şekilde gösterilen **arama** kutusunu kullanarak veri kümelerini bulun. Daha sonra, veri kümesine erişmek için yalnızca verdiğiniz adı yazın ve veri kümesini ana panele sürükleyin. Ana panele bırakma, Machine Learning modelleme 'de kullanılmak üzere onu seçer.

![Veri kümesini ana panele sürükleyin](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> Bunu hem eğitim hem de test veri kümeleri için yapın. Ayrıca, bu amaçla verdiğiniz veritabanı adını ve tablo adlarını kullanmayı unutmayın. Şekilde kullanılan değerler yalnızca çizim amaçlıdır. * *
>
>

### <a name="step-2-create-an-experiment-in-azure-machine-learning-to-predict-clicks--no-clicks"></a><a name="step2"></a> 2. Adım: tıklamaların tahmin edilmesi/tıklamaları tahmin etmek için Azure Machine Learning bir deneme oluşturma
Azure Machine Learning Studio (klasik) denemeniz şöyle görünür:

![Machine Learning deneme](./media/hive-criteo-walkthrough/xRpVfrY.png)

Şimdi bu denemenin önemli bileşenlerini inceleyin. Kaydedilen eğdiğimiz tren ve test veri kümelerimizi önce deneme tuvalimize sürükleyin.

#### <a name="clean-missing-data"></a>Eksik Verileri Temizleme
**Eksik veri modülünü Temizleme** , adının ne kadar önereceği: eksik verileri Kullanıcı tarafından belirtilebilecekleri yollarla temizler. Bunu görmek için bu modüle bakın:

![Eksik verileri temizle](./media/hive-criteo-walkthrough/0ycXod6.png)

Burada, tüm eksik değerleri 0 ile değiştirmeyi seçin. Ayrıca, modüldeki açılan seçeneklere bakarak görünebilen başka seçenekler de vardır.

#### <a name="feature-engineering-on-the-data"></a>Verilerde Özellik Mühendisliği
Büyük veri kümelerinin bazı kategorik özellikleri için milyonlarca benzersiz değer olabilir. Bu tür yüksek boyutlu kategorik özellikleri temsil etmek için tek yönlü kodlama gibi Naïve yöntemlerinin kullanılması tamamen uygulanamaz. Bu izlenecek yol, bu yüksek boyutlu kategorik değişkenlerin küçük gösterimlerini oluşturmak için yerleşik Azure Machine Learning modüller kullanılarak Count özelliklerinin nasıl kullanılacağını göstermektedir. Son sonuç, daha küçük bir model boyutudur, daha hızlı eğitim süreleridir ve diğer teknikleri kullanarak karşılaştırılabilir performans ölçümleridir.

##### <a name="building-counting-transforms"></a>Sayım dönüşümleri oluşturma
Sayım özellikleri oluşturmak için Azure Machine Learning ' de kullanılabilen **derleme sayım dönüştürme** modülünü kullanın. Modül şuna benzer:

![Derleme sayım dönüştürme modülü özellikleri ](./media/hive-criteo-walkthrough/e0eqKtZ.png)
 ![ derleme sayım dönüştürme modülü](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT]
> **Sütunları say** kutusunda, üzerinde sayım yapmak istediğiniz sütunları girin. Genellikle, bunlar (belirtildiği gibi) yüksek boyutlu kategorik sütunlardır. Criteo DataSet 'in 26 kategorik sütun olduğunu unutmayın: Col15 to Col40. Burada, bunların tümünde sayımını yapın ve dizinlerini verin (gösterildiği gibi, 15 ile 40 arasında ayırarak).
>

Modülünü MapReduce modunda kullanmak için (büyük veri kümeleri için uygun), bir HDInsight Hadoop kümesine erişmeniz gerekir (özellik araştırması için kullanılan bir tane, bu amaçla ve kimlik bilgileriyle birlikte yeniden kullanılabilir). Önceki rakamlar, doldurulmuş değerlerin nasıl göründüğünü gösterir (çizim için belirtilen değerleri kendi kullanım örneğinizden ilgili olanlarla değiştirin).

![Modül parametreleri](./media/hive-criteo-walkthrough/05IqySf.png)

Yukarıdaki şekilde, giriş blobu konumunun nasıl girilmesi gösterilmektedir. Bu konum, üzerinde sayım tabloları oluşturmak için ayrılmış verilere sahiptir.

Bu modül çalışmayı bitirdiğinde, modülü daha sonra modüle sağ tıklayıp **dönüştürme olarak kaydet** seçeneğini belirleyerek kaydedin:

!["Dönüşüm olarak Kaydet" seçeneği](./media/hive-criteo-walkthrough/IcVgvHR.png)

Yukarıda gösterilen deneme mimarimizde, "ytransform2" veri kümesi tam olarak kaydedilmiş bir sayı dönüşümüne karşılık gelir. Bu denemenin geri kalanı için okuyucunun, bazı verilerde sayım oluşturmak için bir **Yapı sayması dönüştürme** modülünü kullanmakta olduğu varsayılır ve daha sonra tren ve test veri kümelerinde Count özellikleri oluşturmak için bu sayıları kullanabilir.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Tren ve test veri kümelerinin bir parçası olarak hangi sayım özelliklerinin ekleneceğini seçme
Sayı dönüşümüyle, Kullanıcı, **Sayım tablosu parametrelerini değiştirme** modülünü kullanarak eğitelere ve test veri kümelerine dahil edilecek özellikleri seçebilir. Bu modül, tamamlanma açısından burada gösterilmiştir. Ancak kolaylık olması durumunda bu uygulamayı gerçekten denediğimiz bir şekilde kullanmıyor.

![Sayım tablosu parametrelerini değiştir](./media/hive-criteo-walkthrough/PfCHkVg.png)

Bu durumda, görünebilirler gibi, günlük-Odd 'ler kullanılır ve geri dönüş sütunu yok sayılır. Çöp kutusu eşiği, düzeltme için kaç tane sözde önceki örnek ekleme ve herhangi bir Laplacıya gürültü kullanıp kullanmayacağınızı gibi parametreleri de ayarlayabilirsiniz. Bunların hepsi gelişmiş özelliklerdir ve varsayılan değerlerin bu özellik oluşturma türünde yeni olan kullanıcılar için iyi bir başlangıç noktası olması belirtilecektir.

##### <a name="data-transformation-before-generating-the-count-features"></a>Sayı özelliklerini oluşturmadan önce veri dönüştürme
Artık odak, sayı özellikleri oluşturmadan önce tren ve test verilerimizi dönüştürme hakkında önemli bir noktasıdır. Sayı dönüşümü verilerimize uygulanmadan önce iki **yürütme R betik** modülü kullanılır.

![R betik modüllerini yürütme](./media/hive-criteo-walkthrough/aF59wbc.png)

İlk R betiği aşağıda verilmiştir:

![İlk R betiği](./media/hive-criteo-walkthrough/3hkIoMx.png)

Bu R betiği, sütunlarımızı "Sütun1" adlarıyla "Col40" olarak yeniden adlandırır. Bunun nedeni, sayı dönüştürmesinin bu biçimin adlarını beklediği bir biçimdir.

İkinci R betiği negatif sınıfı aşağı örnekleyerek pozitif ve negatif sınıflar (sırasıyla sınıflar 1 ve 0) arasındaki dağılımı dengeler. Burada R betiği bunun nasıl yapılacağını göstermektedir:

![İkinci R betiği](./media/hive-criteo-walkthrough/91wvcwN.png)

Bu basit R betiğinde, \_ \_ pozitif ve negatif sınıflar arasındaki bakiye miktarını ayarlamak için "POS neg oranı" kullanılır. Bu, sınıf kullanımının çarpılacağı sınıflandırma sorunları için genellikle performans avantajlarının (Bu durumda,% 3,3 pozitif sınıfa ve% 96,7 ' e sahip olduğunuz), bu durum için önemlidir.

##### <a name="applying-the-count-transformation-on-our-data"></a>Verilerimizde Count dönüşümünü uygulama
Son olarak, eğdiğimiz ve test veri kümelerinde sayı dönüşümleri uygulamak için **dönüştürme modülünü Uygula** ' yı kullanabilirsiniz. Bu modül, kaydedilen sayıyı bir giriş olarak, eğitme veya test veri kümelerini diğer girdi olarak alır ve sayı özelliklerine sahip verileri döndürür. Burada gösterilmektedir:

![Dönüştürme modülünü Uygula](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Count özelliklerinin nasıl göründüğünü bir alıntı
Bu, sayı özelliklerinin ne gibi göründüğünü görmek için kullanılır. Bunun bir alıntısıdır:

![Sayı özellikleri](./media/hive-criteo-walkthrough/FO1nNfw.png)

Bu alıntı, üzerinde sayılan sütunlar için, her türlü ilgili geri alma ' ya ek olarak sayımlar ve günlük Odd 'ler olduğunu gösterir.

Artık bu dönüştürülmüş veri kümelerini kullanarak bir Azure Machine Learning modeli oluşturmaya hazırsınız. Sonraki bölümde bunun nasıl yapılacağını gösterir.

### <a name="step-3-build-train-and-score-the-model"></a><a name="step3"></a> 3. Adım: modeli oluşturma, eğitme ve Puanlama

#### <a name="choice-of-learner"></a>Öğrenimi seçimi
İlk olarak, bir öğrenici seçmeniz gerekir. Learner 'imiz olarak iki sınıf bir önceden maliyetli karar ağacı kullanın. Bu öğrenner için varsayılan seçenekler şunlardır:

![Two-Class artırılmış karar ağacı parametreleri](./media/hive-criteo-walkthrough/bH3ST2z.png)

Deneme için varsayılan değerleri seçin. Varsayılanlar anlamlı ve performans üzerinde hızlı taban çizgileri almanın iyi bir yoludur. Taban çizgisini bir kez daha seçerseniz, performans ile performansı artırabilirsiniz.

#### <a name="train-the-model"></a>Modeli eğitme
Eğitim için yalnızca bir **eğitme modeli** modülü çağırın. Bu iki giriş Two-Class, artırılmış karar ağacı öğrenici ve tren veri kümidir. Bu, burada gösterilmektedir:

![Model modülünü eğitme](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>Modeli puanlama
Eğitilen bir modelle, test veri kümesi üzerinde puan almaya ve performansını değerlendirmeye başlamaya hazırız. Bunu, aşağıdaki şekilde gösterildiği gibi, **model değerlendir** **modülünü kullanarak** yapın:

![Score Model (Model Puanlama) modülü](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step-4-evaluate-the-model"></a><a name="step4"></a> 4. Adım: modeli değerlendirme
Son olarak, model performansını çözümlemeniz gerekir. Genellikle, iki sınıf (ikili) sınıflandırma sorunları için iyi bir ölçü, AUC 'dir. Bu eğriyi görselleştirmek için, **puan modeli** modülünü bir **değerlendirme modeli** modülüne bağlayın. **Modeli değerlendir** modülünde **Görselleştir** ' i tıklatmak aşağıdakine benzer bir grafik verir:

![Modül BDT modelini değerlendir](./media/hive-criteo-walkthrough/0Tl0cdg.png)

İkili (veya iki sınıf) sınıflandırma sorunları, bir tahmin doğruluk ölçüsü, eğri (AUC) altındaki alandır. Aşağıdaki bölümde, test veri kümizdeki bu modeli kullanan sonuçlarımız gösterilmektedir. **Modeli değerlendir** modülünün çıkış bağlantı noktasına sağ tıklayın ve ardından **görselleştirin**.

![Model değerlendirme modülünü görselleştirin](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step-5-publish-the-model-as-a-web-service"></a><a name="step5"></a> 5. Adım: modeli Web hizmeti olarak yayımlama
Bir Azure Machine Learning modelini en az Fuss ile Web Hizmetleri olarak yayımlayabilme özelliği, yaygın olarak kullanılabilir hale getirilmesi için değerli bir özelliktir. Bu işlem yapıldıktan sonra herkes, tahmin gerektiren giriş verileriyle Web hizmetine çağrı yapabilir ve Web hizmeti bu tahminleri döndürmek için modeli kullanır.

İlk olarak eğitilen modelimizi eğitilen model nesnesi olarak kaydederek eğitim **modeli modülüne sağ** tıklayıp **eğitilen model olarak kaydet** seçeneğini kullanın.

Sonra, Web hizmetimiz için giriş ve çıkış bağlantı noktaları oluşturun:

* giriş bağlantı noktası, verileri tahmin etmeniz gereken verilerle aynı biçimde alır
* çıkış bağlantı noktası, puanlanmış etiketleri ve ilişkili olasılıkların sonucunu döndürür.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Giriş bağlantı noktası için birkaç veri satırı seçin
Giriş bağlantı noktası verileri olarak kullanılacak olan 10 satırı seçmek için bir **SQL dönüştürme modülü uygulama** kullanımı uygundur. Burada gösterilen SQL sorgusunu kullanarak giriş bağlantı noktası için yalnızca şu veri satırlarını seçin:

![Giriş bağlantı noktası verileri](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Web hizmeti
Şimdi Web hizmetimizi yayımlamak için kullanılabilecek küçük bir deneme çalıştırmaya hazırsınız.

#### <a name="generate-input-data-for-webservice"></a>Web hizmeti için giriş verileri oluşturma
Ayrıntılı bir adım olarak, sayı tablosu büyük olduğundan, birkaç satırlık test verisi alın ve sayı özellikleriyle bundan çıkış verileri oluşturun. Bu çıktı, burada gösterildiği gibi, WebService için girdi veri biçimi olarak görev yapabilir:

![BDT giriş verileri oluşturma](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> Giriş verisi biçimi için, **sayı korlaizer** modülünün çıkışını kullanın. Bu deneme çalışmayı tamamladıktan sonra, sayımı bir veri kümesi olarak **Count Feaizer** modülünden kaydedin. Bu veri kümesi, WebService 'daki giriş verileri için kullanılır.
>
>

#### <a name="scoring-experiment-for-publishing-webservice"></a>Web hizmeti yayımlamak için Puanlama denemesi
İlk olarak, temel yapı, eğitilen model nesnemizi kabul eden bir **puan modeli** modülüdür ve önceki adımlarda oluşturulan bazı giriş verileri satırını, **sayı korlaizer** modülünü kullanarak kabul eder. Puanlanmış etiketleri ve Kullanım Puanını belirlemek için "veri kümesindeki sütunları seç" seçeneğini kullanın.

![Veri kümesindeki Sütunları seçme](./media/hive-criteo-walkthrough/kRHrIbe.png)

Veri kümesindeki **Select sütunlarının** bir veri kümesinden ' filtreleme ' verileri için nasıl kullanılabileceğini fark edebilirsiniz. İçerik burada gösterilir:

![Veri kümesi modülündeki sütunları seçme modülü ile filtreleme](./media/hive-criteo-walkthrough/oVUJC9K.png)

Mavi giriş ve çıkış bağlantı noktalarını almak için sağ alt kısımdaki **Web hizmetini hazırla** ' yı tıklamanız yeterlidir. Bu denemeyi çalıştırmak, Web hizmetini yayımlamamızı de sağlar: aşağıda gösterildiği gibi, sağ alt kısımdaki **Web hizmeti Yayımla** simgesine tıklayın:

![Web hizmetini Yayımla](./media/hive-criteo-walkthrough/WO0nens.png)

Web hizmeti yayımlandıktan sonra, bu nedenle şu şekilde bir sayfaya yeniden yönlendirilirsiniz:

![Web hizmeti panosu](./media/hive-criteo-walkthrough/YKzxAA5.png)

Sol taraftaki WebServices için iki bağlantı bildirimi görürsünüz:

* **İstek/yanıt** hizmeti (veya RR 'ler), tek tahminlere yöneliktir ve bu atölyde kullanılmış olan şeydir.
* Toplu **yürütme** HIZMETI (bes) toplu tahminlerde kullanılır ve tahmine dayalı hale getirmek için kullanılan giriş verilerinin Azure Blob depolama alanında bulunmasını gerektirir.

Bağlantı **isteğine/yanıta** tıkladığınızda, C#, Python ve R 'de önceden tamamlanmış kodu sağlayan bir sayfaya gidersiniz. Bu kod, Web hizmeti çağrıları yapmak için rahat bir şekilde kullanılabilir. Bu sayfadaki API anahtarının kimlik doğrulaması için kullanılması gerekir.

Bu Python kodunu, IPython not defterindeki yeni bir hücreye kopyalamak uygun bir yoldur.

Doğru API anahtarına sahip Python kodu segmenti aşağıda verilmiştir.

![Python kodu](./media/hive-criteo-walkthrough/f8N4L4g.png)

Varsayılan API anahtarı, Web hizmeti 'nin API anahtarımız ile değiştirilmiştir. Bir IPython not defterinde bu hücrede **Çalıştır** ' a tıklamak aşağıdaki yanıtı verir:

![IPython yanıtı](./media/hive-criteo-walkthrough/KSxmia2.png)

Python betiği JSON çerçevesinde istenen iki test örneği için, "puanlanmış Etiketler, puanlanmış olasılıklara" biçimindeki yanıtları geri alırsınız. Bu durumda, önceden oluşturulmuş kodun sağladığı varsayılan değerler seçilir (tüm sayısal sütunlar için 0 ve tüm kategorik sütunlar için "Value" dizesi).

Sonuç olarak, izlenecek yol Azure Machine Learning kullanarak büyük ölçekli veri kümesinin nasıl işleneceğini gösterir. Terabaytlık bir veri ile başlayıp bir tahmin modeli oluşturup bulutta Web hizmeti olarak dağıttınız.