---
title: Visual Studio için Apache Hive & Data Lake araçları-Azure HDInsight
description: Azure HDInsight üzerinde Apache Hadoop ile Apache Hive sorguları çalıştırmak üzere Visual Studio için Data Lake araçları 'nı nasıl kullanacağınızı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 4512c9d9fdb66713ba24fbf30278e5d5dbb2ae23
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863760"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Visual Studio için Data Lake araçlarını kullanarak Apache Hive sorgularını çalıştırma

Apache Hive sorgulamak için Visual Studio Data Lake araçları 'nı nasıl kullanacağınızı öğrenin. Data Lake araçları, Azure HDInsight üzerinde Apache Hadoop için Hive sorgularını kolayca oluşturmanıza, göndermenize ve izlemenize olanak tanır.

## <a name="prerequisites"></a>Önkoşullar

* HDInsight üzerinde bir Apache Hadoop kümesi. Bu öğeyi oluşturma hakkında daha fazla bilgi için bkz. [Azure HDInsight 'ta Kaynak Yöneticisi şablonu kullanarak Apache Hadoop kümesi oluşturma](./apache-hadoop-linux-tutorial-get-started.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/). Bu makaledeki adımlarda Visual Studio 2019 kullanılır.

* Visual Studio için HDInsight araçları veya Visual Studio için Azure Data Lake araçları. Araçları yükleme ve yapılandırma hakkında bilgi için bkz. [Visual Studio için Data Lake araçları 'Nı yükleme](apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio).

## <a name="run-apache-hive-queries-using-the-visual-studio"></a>Visual Studio 'Yu kullanarak Apache Hive sorguları çalıştırma

Hive sorguları oluşturmak ve çalıştırmak için iki seçeneğiniz vardır:

* Geçici sorgular oluşturun.
* Hive uygulaması oluşturun.

### <a name="create-an-ad-hoc-hive-query"></a>Geçici Hive sorgusu oluşturma

Geçici sorgular **toplu** veya **etkileşimli** modda çalıştırılabilir.

1. **Visual Studio 'yu** başlatın ve **kod olmadan devam et**' i seçin.

2. **Sunucu Gezgini**, **Azure**' a sağ tıklayın, **Microsoft Azure aboneliğine Bağlan...** öğesini seçin ve oturum açma işlemini doldurun.

3. **HDInsight**' ı genişletin, sorguyu çalıştırmak istediğiniz kümeye sağ tıklayın ve ardından **Hive sorgusu yaz**' ı seçin.

4. Aşağıdaki Hive sorgusunu girin:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. **Yürüt**’ü seçin. Yürütme modu varsayılan olarak **etkileşimli** olur.

    :::image type="content" source="./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png" alt-text="Etkileşimli Hive sorgusu yürütme, Visual Studio" border="true":::

6. **Toplu iş** modunda aynı sorguyu çalıştırmak için açılan listeyi **etkileşimli** moddan **Toplu işe** değiştirin. Yürütme düğmesi **Execute** iken **Gönder** olarak değişir.

    :::image type="content" source="./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png" alt-text="Toplu işlem Hive sorgusu, Visual Studio 'Yu gönder" border="true":::

    Hive düzenleyicisi IntelliSense’i destekler. Visual Studio için Data Lake Araçları, Hive betiğinizi düzenlerken uzak meta verilerin yüklenmesini destekler. Örneğin, yazarsanız `SELECT * FROM` , IntelliSense önerilen tüm tablo adlarını listeler. Bir tablo adı belirtildiğinde, IntelliSense sütun adlarını listeler. Araçlar çoğu Hive DML deyimlerini, alt sorguları ve yerleşik UDF'leri destekler. IntelliSense yalnızca HDInsight araç çubuğunda seçilen kümelerin meta verilerini önerir.

7. Sorgu araç çubuğunda (sorgu sekmesinin altındaki ve sorgu metninin üzerindeki alan), **Gönder**' i seçin ya da **Gönder** ' ın yanındaki aşağı açılan oku seçin ve açılan listeden **Gelişmiş** ' i seçin. İkinci seçeneği belirlerseniz,

8. Gelişmiş gönder seçeneğini belirlediyseniz, **betik gönder** Iletişim kutusunda **iş adı**, **bağımsız değişkenler**, **ek konfigürasyonlar** ve **durum dizini** ' ni yapılandırın. Sonra **Gönder**' i seçin.

    :::image type="content" source="./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png" alt-text="Betik Gönder iletişim kutusu, HDInsight Hadoop Hive sorgusu" border="true":::

### <a name="create-a-hive-application"></a>Hive uygulaması oluşturma

Hive uygulaması oluşturarak Hive sorgusu çalıştırmak için aşağıdaki adımları izleyin:

1. **Visual Studio 'yu** açın.

2. **Başlangıç** penceresinde **Yeni proje oluştur**' u seçin.

3. **Yeni proje oluştur** penceresinde, **şablon ara** kutusuna *Hive* yazın. Ardından **Hive uygulaması** ' nı seçin ve **İleri**' yi seçin.

4. **Yeni projeyi yapılandırın** penceresinde bir **Proje adı** girin, yeni proje için bir **konum** seçin veya oluşturun ve ardından **Oluştur**' u seçin.

5. Bu projeyle oluşturulan **Script. HQL** dosyasını açın ve aşağıdaki HiveQL deyimlerine yapıştırın:

    ```hql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Bu deyimler aşağıdaki işlemleri yapılır:

    * `DROP TABLE`: Varsa tabloyu siler.

    * `CREATE EXTERNAL TABLE`: Hive içinde yeni bir ' External ' tablosu oluşturur. Dış tablolar yalnızca tablo tanımını Hive içinde depolar. (Veriler özgün konumda bırakılır.)

        > [!NOTE]  
        > Dış tablolar, temel verilerin bir MapReduce işi veya bir Azure hizmeti gibi bir dış kaynak tarafından güncelleştirilmesini beklediğinde kullanılmalıdır.
        >
        > Dış tablonun atılması, yalnızca tablo tanımı olan **verileri silmez.**

    * `ROW FORMAT`: Kovanın verilerin nasıl biçimlendirildiğini söyler. Bu durumda, her günlükteki alanlar boşlukla ayrılır.

    * `STORED AS TEXTFILE LOCATION`: Hive 'a verilerin *örnek/veri* dizininde depolandığını ve metin olarak depolandığını söyler.

    * `SELECT`: Sütunun değeri içerdiği tüm satırların sayısını seçer `t4` `[ERROR]` . Bu ifade bir değeri döndürür `3` , çünkü üç satır bu değeri içerir.

    * `INPUT__FILE__NAME LIKE '%.log'`: Kovanın yalnızca. log ile biten dosyalardaki verileri döndürmesini söyler. Bu yan tümce, aramayı verileri içeren *Sample. log* dosyası ile sınırlandırır.

6. Sorgu dosyası araç çubuğundan (diğer bir deyişle, geçici sorgu araç çubuğuna benzer bir görünüm), bu sorgu için kullanmak istediğiniz HDInsight kümesini seçin. Ardından **etkileşimli** olarak **Batch** (gerekliyse) seçeneğini değiştirin ve deyimleri Hive Işi olarak çalıştırmak için **Gönder** ' i seçin.

   **Hive Iş Özeti** görünür ve çalışan iş hakkında bilgileri görüntüler. İş **durumu** **tamamlanana** kadar değişene kadar Iş bilgilerini yenilemek için **Yenile** bağlantısını kullanın.

   :::image type="content" source="./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png" alt-text="Tamamlanan Hive iş Özeti, Hive uygulaması, Visual Studio" border="true":::

7. Bu işin çıkışını görüntülemek için **Iş çıkışı** ' nı seçin. `[ERROR] 3`Bu sorgu tarafından döndürülen değer olan öğesini görüntüler.

### <a name="additional-example"></a>Ek örnek

Aşağıdaki örnek, `log4jLogs` önceki yordamda oluşturulan tabloyu temel alır, [bir Hive uygulaması oluşturur](#create-a-hive-application).

1. **Sunucu Gezgini**, kümenize sağ tıklayın ve **Hive sorgusu yaz**' ı seçin.

2. Aşağıdaki Hive sorgusunu girin:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Bu deyimler aşağıdaki işlemleri yapılır:

    * `CREATE TABLE IF NOT EXISTS`: Zaten yoksa tablo oluşturur. `EXTERNAL`Anahtar sözcüğü kullanılmadığından, bu ifade bir iç tablo oluşturur. İç tablolar Hive veri ambarında depolanır ve Hive tarafından yönetilir.

        > [!NOTE]  
        > `EXTERNAL`Tabloların aksine, iç tablo bırakılırken temel alınan veriler de silinir.

    * `STORED AS ORC`: Verileri *en iyileştirilmiş satır sütunlu* (ORC) biçimde depolar. ORC, Hive verilerinin depolanması için yüksek düzeyde iyileştirilmiş ve etkili bir biçimdir.

    * `INSERT OVERWRITE ... SELECT`: İçeren tablodan satırları seçer `log4jLogs` `[ERROR]` , ardından verileri `errorLogs` tabloya ekler.

3. Gerekirse, **etkileşimli** olarak **Batch** 'e değiştirip **Gönder**' i seçin.

4. İşin tabloyu oluşturduğunu doğrulamak için **Sunucu Gezgini** gidin ve **Azure**  >  **HDInsight**' ı genişletin. HDInsight kümenizi genişletin ve ardından **Hive veritabanlarının**  >  **Varsayılanı**' nı genişletin. **Errorlogs** tablosu ve **log4jLogs** tablosu listelenir.

## <a name="next-steps"></a>Sonraki adımlar

Gördüğünüz gibi, Visual Studio için HDInsight araçları, HDInsight 'ta Hive sorguları ile çalışmanın kolay bir yolunu sunar.

* HDInsight 'ta Hive hakkında genel bilgi için bkz. [Azure HDInsight 'ta Apache Hive ve HiveQL nedir?](hdinsight-use-hive.md)

* HDInsight 'ta Hadoop ile çalışmanın diğer yolları hakkında bilgi için bkz. [HDInsight üzerinde MapReduce kullanma Apache Hadoop](hdinsight-use-mapreduce.md)

* Visual Studio için HDInsight araçları hakkında daha fazla bilgi için bkz.[Visual Studio için Data Lake araçları 'Nı kullanarak Azure HDInsight 'a bağlanma ve Apache Hive sorguları çalıştırma](apache-hadoop-visual-studio-tools-get-started.md)
