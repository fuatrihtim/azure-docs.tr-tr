---
title: Azure HDInsight 'ta Hive sorgularını iyileştirme
description: Bu makalede, Azure HDInsight 'ta Apache Hive sorgularınızın nasıl iyileştirileceği açıklanır.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 10/28/2020
ms.openlocfilehash: 551d985ea78e83397e507676c5fd7ecfce12ff7b
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864253"
---
# <a name="optimize-apache-hive-queries-in-azure-hdinsight"></a>Azure HDInsight’ta Apache Hive sorgularını iyileştirme

Bu makalede, Apache Hive Sorgularınızın performansını artırmak için kullanabileceğiniz en yaygın performans iyileştirmelerinden bazıları açıklanmaktadır.

## <a name="cluster-type-selection"></a>Küme türü seçimi

Azure HDInsight 'ta, birkaç farklı küme türünde Apache Hive sorguları çalıştırabilirsiniz. 

İş yükü gereksinimleriniz için performansı iyileştirmenize yardımcı olması için uygun küme türünü seçin:

* Etkileşimli sorgular için iyileştirmek üzere **etkileşimli sorgu** kümesi türünü seçin `ad hoc` . 
* Toplu işlem olarak kullanılan Hive sorgularını iyileştirmek için Apache **Hadoop** kümesi türünü seçin. 
* **Spark** ve **HBase** küme türleri de Hive sorguları çalıştırabilir ve bu iş yüklerini çalıştırıyorsanız uygun olabilir. 

Çeşitli HDInsight kümesi türlerinde Hive sorguları çalıştırma hakkında daha fazla bilgi için bkz. [Azure HDInsight 'ta Apache Hive ve HiveQL nedir?](hadoop/hdinsight-use-hive.md).

## <a name="scale-out-worker-nodes"></a>Çalışan düğümlerinin ölçeğini genişletme

Bir HDInsight kümesindeki çalışan düğümü sayısının artırılması, çalışmanın daha fazla mapbir ve azaltıcının kullanarak paralel olarak çalıştırılmasını sağlar. HDInsight 'ta ölçeği arttırmanın iki yolu vardır:

* Bir küme oluşturduğunuzda, çalışan düğümlerinin sayısını Azure portal, Azure PowerShell veya komut satırı arabirimini kullanarak belirtebilirsiniz.  Daha fazla bilgi için bkz. [HDInsight kümesi oluşturma](hdinsight-hadoop-provision-linux-clusters.md). Aşağıdaki ekran görüntüsünde Azure portal çalışan düğümü yapılandırması gösterilmektedir:
  
    :::image type="content" source="./media/hdinsight-hadoop-optimize-hive-query/azure-portal-cluster-configuration.png" alt-text="Azure portal küme boyutu düğümleri":::

* Oluşturulduktan sonra, bir kümeyi yeniden oluşturmadan daha fazla ölçek genişletmek için çalışan düğümü sayısını da düzenleyebilirsiniz:

    :::image type="content" source="./media/hdinsight-hadoop-optimize-hive-query/azure-portal-settings-nodes.png " alt-text="Azure portal ölçek kümesi boyutu":::

HDInsight ölçeklendirme hakkında daha fazla bilgi için bkz. [HDInsight kümelerini ölçeklendirme](hdinsight-scaling-best-practices.md)

## <a name="use-apache-tez-instead-of-map-reduce"></a>Eşleme azaltma yerine Apache Tez kullan

[Apache tez](https://tez.apache.org/) , MapReduce altyapısına alternatif bir yürütme altyapısıdır. Linux tabanlı HDInsight kümeleri varsayılan olarak tez 'yi etkinleştirdi.

:::image type="content" source="./media/hdinsight-hadoop-optimize-hive-query/hdinsight-tez-engine.png" alt-text="HDInsight Apache Tez genel bakış Diyagramı":::

Tez şu nedenle daha hızlıdır:

* **MapReduce altyapısında tek bir iş olarak yönlendirilmiş çevrimsiz grafiği (DAG) yürütün**. DAG, her bir mapıset 'in arkasından bir dizi azaltıcının gerektirir. Bu gereksinim, her Hive sorgusu için birden çok MapReduce işinin devre dışı bırakılmasına neden olur. Tez bu tür kısıtlamasına sahip değildir ve bir iş, iş başlangıç yükünü en aza indirerek karmaşık DAG 'yi işleyebilir.
* **Gereksiz yazmaları önler**. MapReduce altyapısında aynı Hive sorgusunu işlemek için birden çok iş kullanılır. Her MapReduce işinin çıktısı, ara veriler için bir. Tez, her Hive sorgusu için iş sayısını en aza indirir, bu, gereksiz yazmaları önleyebilir.
* **Başlangıç gecikmelerini en aza indirir**. Tez, başlaması gereken mapkas sayısını azaltarak ve ayrıca iyileştirme 'yi iyileştirmek için başlangıç gecikmesini en aza indirmenize daha iyidir.
* **Kapsayıcıları yeniden kullanır**. Mümkün olan tez, kapsayıcılardan başlayan gecikme süresinin azaltıldığı durumlarda kapsayıcıları yeniden kullanır.
* **Sürekli iyileştirme teknikleri**. Derleme aşamasında geleneksel iyileştirme gerçekleştirildi. Ancak, çalışma zamanı sırasında daha iyi iyileştirilmesine izin veren girişler hakkında daha fazla bilgi sağlanır. Tez, planın çalışma zamanı aşamasına daha fazla iyileştirmesine imkan tanıyan sürekli iyileştirme teknikleri kullanır.

Bu kavramlar hakkında daha fazla bilgi için bkz. [Apache TEZ](https://tez.apache.org/).

Aşağıdaki set komutuyla sorguyu önek olarak ekleyerek herhangi bir Hive sorgusu tez 'yi etkin hale getirebilirsiniz:

```hive
set hive.execution.engine=tez;
```

## <a name="hive-partitioning"></a>Hive bölümlendirme

G/ç işlemleri, Hive sorguları çalıştırmak için önemli performans sorununa neden oluyor. Okunması gereken veri miktarı azaltılılabildiğinden performans artırılabilir. Varsayılan olarak, Hive sorguları Hive tablolarının tamamını tarar. Ancak, yalnızca küçük miktarda veri taraması gereken sorgular (örneğin, filtrelemeye sahip sorgular) için bu davranış gereksiz ek yük oluşturur. Hive bölümlendirme, Hive sorgularının yalnızca Hive tablolarındaki gerekli veri miktarına erişmesine izin verir.

Hive bölümlendirme, ham verileri yeni dizinlere yeniden düzenleyerek uygulanır. Her bölümün kendi dosya dizini vardır. Bölümleme Kullanıcı tarafından tanımlanır. Aşağıdaki diyagramda, bir Hive tablosunun *yıl* sütununa göre bölümlenmesi gösterilmektedir. Her yıl için yeni bir dizin oluşturulur.

:::image type="content" source="./media/hdinsight-hadoop-optimize-hive-query/hdinsight-partitioning.png" alt-text="HDInsight Apache Hive bölümlendirme":::

Bazı bölümlendirme konuları:

* **Bölüm bölümlemenin altında değil** yalnızca birkaç değer içeren sütunlarda bölümlendirme az sayıda bölüme neden olabilir. Örneğin, cinsiyet üzerinde bölümlendirme yalnızca oluşturulacak iki bölüm oluşturuyor (erkek ve female), bu nedenle gecikme süresini en fazla yarıya küçültün.
* **Bölüm üzerinde kullanmayın** -diğer bir deyişle, benzersiz bir değere sahip bir sütunda (örneğin, UserID) bölüm oluşturmak birden çok bölüme neden olur. Bölüm üzerinde, çok sayıda dizin işlenmesi gerektiği için küme süs Yot üzerinde çok daha fazla stres olur.
* **Veri eğriliğini önleyin** -bölümleme anahtarınızı, tüm bölümlerin hatta boyutu olacak şekilde seçin. Örneğin, *durum* sütununda bölümlendirme, verilerin dağıtımını eğebilir. California 'nın durumunda Vermont 'in neredeyse 30 kata bir popülasyon olduğundan, bölüm boyutu büyük olasılıkla çarpıtılmış ve performans gecenin farklılık gösterebilir.

Bölüm tablosu oluşturmak için *bölümlenmiş by* yan tümcesini kullanın:

```sql
CREATE TABLE lineitem_part
      (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
      L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
      L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
      L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
      L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT STRING)
PARTITIONED BY(L_SHIPDATE STRING)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
STORED AS TEXTFILE;
```

Bölümlenmiş tablo oluşturulduktan sonra statik bölümlendirme veya dinamik bölümlendirme oluşturabilirsiniz.

* **Statik bölümlendirme** , verileri zaten uygun dizinlerde oluşturmuş olduğunuz anlamına gelir. Statik bölümlerle, dizin konumuna göre Hive bölümlerini el ile eklersiniz. Aşağıdaki kod parçacığı bir örnektir.
  
   ```sql
   INSERT OVERWRITE TABLE lineitem_part
   PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   SELECT * FROM lineitem
   WHERE lineitem.L_SHIPDATE = '5/23/1996 12:00:00 AM'

   ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   LOCATION 'wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
   ```

* **Dinamik bölümlendirme** , Hive 'nin sizin için otomatik olarak bölüm oluşturmasını istediğiniz anlamına gelir. Hazırlama tablosundan bölümleme tablosunu zaten oluşturduğunuzdan, tek yapmanız gereken, bölümlenmiş tabloya veri eklemedir:
  
   ```hive
   SET hive.exec.dynamic.partition = true;
   SET hive.exec.dynamic.partition.mode = nonstrict;
   INSERT INTO TABLE lineitem_part
   PARTITION (L_SHIPDATE)
   SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY,
       L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
       L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
       L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as L_RETURNFLAG,
       L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as L_SHIPDATE_PS,
       L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as L_RECEIPTDATE,
       L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as L_SHIPMODE,
       L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;
   ```

Daha fazla bilgi için bkz. [bölümlenmiş tablolar](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

## <a name="use-the-orcfile-format"></a>ORCFile biçimini kullanın

Hive farklı dosya biçimlerini destekler. Örnek:

* **Metin**: varsayılan dosya biçimi ve çoğu senaryolarla birlikte kullanılır.
* **Avro**: birlikte çalışabilirlik senaryolarında iyi sonuç verir.
* **Orc/Parquet**: en iyi performans için idealdir.

ORC (En Iyi duruma getirilmiş satır sütunlu) biçimi, Hive verilerini depolamanın son derece verimli bir yoludur. Diğer biçimlere kıyasla ORC aşağıdaki avantajlara sahiptir:

* DateTime ve karmaşık ve yarı yapılandırılmış türler dahil karmaşık türler için destek.
* %70 ' e kadar sıkıştırma.
* satırları atlamaya izin veren her 10.000 satırı dizine ekler.
* çalışma zamanı yürütmesinde önemli bir bırakma.

ORC biçimini etkinleştirmek için, ilk *olarak orc olarak depolanan* yan tümcesini içeren bir tablo oluşturursunuz:

```sql
CREATE TABLE lineitem_orc_part
      (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
      L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
      L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
      L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
      L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
PARTITIONED BY(L_SHIPDATE STRING)
STORED AS ORC;
```

Ardından, hazırlama tablosundan ORC tablosuna veri eklersiniz. Örnek:

```sql
INSERT INTO TABLE lineitem_orc
SELECT L_ORDERKEY as L_ORDERKEY,
         L_PARTKEY as L_PARTKEY ,
         L_SUPPKEY as L_SUPPKEY,
         L_LINENUMBER as L_LINENUMBER,
         L_QUANTITY as L_QUANTITY,
         L_EXTENDEDPRICE as L_EXTENDEDPRICE,
         L_DISCOUNT as L_DISCOUNT,
         L_TAX as L_TAX,
         L_RETURNFLAG as L_RETURNFLAG,
         L_LINESTATUS as L_LINESTATUS,
         L_SHIPDATE as L_SHIPDATE,
         L_COMMITDATE as L_COMMITDATE,
         L_RECEIPTDATE as L_RECEIPTDATE,
         L_SHIPINSTRUCT as L_SHIPINSTRUCT,
         L_SHIPMODE as L_SHIPMODE,
         L_COMMENT as L_COMMENT
FROM lineitem;
```

Daha fazla bilgi için [Apache Hive dil el ile](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC)daha fazla bilgi edinebilirsiniz.

## <a name="vectorization"></a>Vektörleştirme

Vektörleştirme, Hive 'nin bir kerede bir satırı işlemek yerine bir toplu iş 1024 satırı işlemesini sağlar. Daha az iç kodun çalıştırılması gerektiğinden basit işlemlerin daha hızlı yapıldığı anlamına gelir.

Vektörleştirme önekini etkinleştirmek için Hive sorgunuz aşağıdaki ayarla:

```hive
set hive.vectorized.execution.enabled = true;
```

Daha fazla bilgi için bkz. [Vektörleştirilmiş sorgu yürütme](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).

## <a name="other-optimization-methods"></a>Diğer en iyi duruma getirme yöntemleri

Göz önünde bulundurmanız gereken daha fazla iyileştirme yöntemi vardır, örneğin:

* **Hive demetlenmesidir:** sorgu performansını iyileştirmek için büyük veri kümelerinin kümelamasına veya segmentine izin veren bir tekniktir.
* **Birleştirme iyileştirmesi:** kovanın verimliliğini artırmak ve Kullanıcı ipuçlarına ihtiyacı azaltmak için Hive sorgu yürütme planlamasının iyileştirmesi. Daha fazla bilgi için bkz. [JOIN iyileştirmesi](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
* **Azaltıcının artırın**.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, birkaç genel Hive sorgu iyileştirme yöntemi öğrendiniz. Daha fazla bilgi için aşağıdaki makalelere bakın:

* [Apache Hive’ı iyileştirme](./optimize-hive-ambari.md)
* [HDInsight 'ta etkileşimli sorgu kullanarak Uçuş gecikmesi verilerini çözümleme](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [HDInsight 'ta Apache Hive kullanarak Twitter verilerini çözümleme](hdinsight-analyze-twitter-data-linux.md)
