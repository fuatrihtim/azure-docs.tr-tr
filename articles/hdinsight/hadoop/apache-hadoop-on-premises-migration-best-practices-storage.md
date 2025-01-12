---
title: "Depolama: şirket içi Apache Hadoop Azure HDInsight 'a geçirme"
description: Şirket içi Hadoop kümelerini Azure HDInsight 'a geçirmek için en iyi depolama uygulamalarını öğrenin.
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/10/2019
ms.openlocfilehash: 89a2a659e195afcae336c930a101ed0c0b470cb4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101715420"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight"></a>Şirket içi Apache Hadoop kümelerini Azure HDInsight 'a geçirme

Bu makale, Azure HDInsight sistemlerinde veri depolamaya yönelik öneriler sağlar. Şirket içi Apache Hadoop sistemlerini Azure HDInsight 'a geçirmeye yardımcı olmak için en iyi uygulamaları sağlayan bir serinin bir parçasıdır.

## <a name="choose-right-storage-system-for-hdinsight-clusters"></a>HDInsight kümeleri için sağ depolama sistemi seçin

Şirket içi Apache Hadoop dosya sistemi (bir) dizin yapısı, Azure Blob depolamada veya Azure Data Lake Storage yeniden oluşturulabilir. Daha sonra, hesaplama için kullanılan HDInsight kümelerini Kullanıcı verilerini kaybetmeden güvenle silebilirsiniz. Her iki hizmet de bir HDInsight kümesi için hem varsayılan dosya sistemi hem de ek dosya sistemi olarak kullanılabilir. HDInsight kümesi ve depolama hesabı aynı bölgede barındırılmalıdır.

### <a name="azure-storage"></a>Azure Depolama

HDInsight kümeleri, Azure depolama 'daki blob kapsayıcısını varsayılan dosya sistemi veya ek bir dosya sistemi olarak kullanabilir. Standart katman depolama hesabı, HDInsight kümeleri ile kullanım için desteklenir. Premier katmanı desteklenmez. Varsayılan Blob kapsayıcısı iş geçmişi ve iş günlükleri gibi kümeye özel bilgileri depolar. Birden çok küme için varsayılan dosya sistemi olarak bir blob kapsayıcısının paylaşılması desteklenmez.

Oluşturma işleminde tanımlanan depolama hesapları ve ilgili anahtarları `%HADOOP_HOME%/conf/core-site.xml` küme düğümlerinde depolanır. Ayrıca, bu kişiler, ambarı Kullanıcı arabirimindeki, "özel çekirdek site" bölümü altında de erişilebilir. Depolama hesabı anahtarı varsayılan olarak şifrelenir ve bir özel şifre çözme betiği, Hadoop Daemon 'ları 'e geçirilmeden önce anahtarların şifresini çözmek için kullanılır. Hive, MapReduce, Hadoop akışı ve Pig dahil işler, depolama hesaplarının ve meta verilerin bir açıklamasını taşır.

Azure depolama, coğrafi olarak çoğaltılabilir. Coğrafi çoğaltma coğrafi kurtarma ve veri artıklığı sağlasa da, coğrafi olarak çoğaltılan konuma yönelik bir yük devretme performansı önemli ölçüde etkiler ve ek ücret ödemeniz gerekebilir. Bu öneri, coğrafi çoğaltmanın daha seyrek ve yalnızca verilerin değeri ek maliyete değer alıyorsa tercih edilir.

Azure depolama 'da depolanan verilere erişmek için aşağıdaki biçimlerden biri kullanılabilir:

|Veri erişim biçimi |Description |
|---|---|
|`wasb:///`|Şifrelenmemiş iletişim kullanarak varsayılan depolamaya erişin.|
|`wasbs:///`|Şifrelenmiş iletişim kullanarak varsayılan depolamaya erişin.|
|`wasb://<container-name>@<account-name>.blob.core.windows.net/`|Varsayılan olmayan bir depolama hesabıyla iletişim kurulurken kullanılır. |

[Standart depolama hesapları Için ölçeklenebilirlik hedefleri](../../storage/common/scalability-targets-standard-account.md) , Azure depolama hesaplarındaki geçerli sınırları listeler. Uygulamanın ihtiyaçları tek bir depolama hesabının ölçeklenebilirlik hedeflerini aşarsa, uygulama birden fazla depolama hesabı kullanmak ve ardından bu depolama hesaplarında veri nesnelerini bölümlemek üzere oluşturulabilir.

[Azure depolama Analizi](../../storage/common/storage-analytics.md) tüm depolama hizmetleri için ölçümler sağlar ve Azure Portal, grafikler aracılığıyla görselleştirildiği ölçümleri topla olarak yapılandırılabilir. Depolama kaynağı ölçümleri için eşiklere ulaşıldığında bildirimde bulunan uyarılar oluşturulabilir.

Azure depolama, bir uygulama veya başka bir depolama hesabı kullanıcısı tarafından yanlışlıkla değiştirildiğinde veya silindiğinde verileri kurtarmaya yardımcı olması için [BLOB nesnelerine geçici silme](../../storage/blobs/soft-delete-blob-overview.md) olanağı sağlar.

[BLOB anlık görüntüleri](/rest/api/storageservices/creating-a-snapshot-of-a-blob)oluşturabilirsiniz. Anlık görüntü, zaman içinde bir noktada alınmış bir Blobun salt okunurdur ve bir blob 'u yedeklemek için bir yol sağlar. Anlık görüntü oluşturulduktan sonra okunabilir, kopyalanabilir veya silinebilir, ancak değiştirilmez.

> [!Note]
> "Nesnelerin" sertifikası olmayan şirket içi Hadoop dağıtımların daha eski sürümleri için, Java güven deposuna aktarılmaları gerekir.

Aşağıdaki yöntemler, sertifikaları Java güven deposuna aktarmak için kullanılabilir:

Azure Blob TLS/SSL sertifikası 'nı bir dosyaya indirme

```bash
echo -n | openssl s_client -connect <storage-account>.blob.core.windows.net:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > Azure_Storage.cer
```

Yukarıdaki dosyayı tüm düğümlerdeki Java güven deposuna aktar

```bash
keytool -import -trustcacerts -keystore /path/to/jre/lib/security/cacerts -storepass changeit -noprompt -alias blobtrust -file Azure_Storage.cer
```

Eklenen sertifikanın güven deposunda olduğunu doğrulayın

```bash
keytool -list -v -keystore /path/to/jre/lib/security/cacerts
```

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

- [Azure HDInsight kümeleri ile Azure depolama kullanma](../hdinsight-hadoop-use-blob-storage.md)
- [Standart depolama hesapları için ölçeklenebilirlik hedefleri](../../storage/common/scalability-targets-standard-account.md)
- [BLOB depolama için ölçeklenebilirlik ve performans hedefleri](../../storage/blobs/scalability-targets.md)
- [Microsoft Azure Depolama Performansı ve Ölçeklenebilirlik Onay Listesi](../../storage/blobs/storage-performance-checklist.md)
- [Microsoft Azure Depolama izleme, tanılama ve sorun giderme](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
- [Azure portalında depolama hesabını izleme](../../storage/common/manage-storage-analytics-logs.md)

### <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Azure Data Lake Storage 1., IBir ve POSIX stili erişim denetimi modeli uygular. Ayrıntılı erişim denetimi için Azure AD ile ilk sınıf tümleştirmesini sağlar. Depolayabileceği verilerin boyutu veya büyük ölçüde paralel analiz çalıştırma yeteneği yoktur.

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

- [Azure portal kullanarak Data Lake Storage 1. HDInsight kümeleri oluşturma](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
- [Azure HDInsight kümeleri ile Data Lake Storage 1. kullanma](../hdinsight-hadoop-use-data-lake-storage-gen1.md)

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Azure Data Lake Storage 2. en son depolama sunumudur. Azure Blob depolama ile doğrudan tümleştirilmiş bir Hadoop uyumlu dosya sistemi uç noktasıyla, ilk nesil Azure Data Lake Storage 1. temel özellikleri birleştirir. Bu geliştirme, nesne depolamanın ölçek ve maliyet avantajlarını, genellikle yalnızca şirket içi dosya sistemleriyle ilişkili güvenilirlik ve performans ile birleştirir.

Azure Data Lake Storage Gen 2, [Azure Blob depolama](../../storage/blobs/storage-blobs-introduction.md) alanının üzerine kurulmuştur ve hem dosya sistemi hem de nesne depolama paradigmalarına kullanarak verilerle arabirim oluşturmanızı sağlar. Dosya sistemi semantiği, dosya düzeyi güvenliği ve ölçek gibi [Azure Data Lake Storage 1.](../../data-lake-store/index.yml)Özellikler düşük maliyetli, katmanlı depolama, yüksek kullanılabilirlik/olağanüstü durum kurtarma özellikleri ve [Azure Blob depolama](../../storage/blobs/storage-blobs-introduction.md)'dan büyük bir SDK/araç oluşturma ekosistemi ile birleştirilir. Data Lake Storage 2., nesne depolamanın tüm nitelikleri, analiz iş yükleri için iyileştirilmiş bir dosya sistemi arabiriminin avantajları eklenirken kalır.

Data Lake Storage 2. temel bir özelliği, bir dizi veri erişimi için nesneleri/dosyaları bir Dizin hiyerarşisinde düzenleyen BLOB depolama hizmetine [hiyerarşik bir ad alanının](../../storage/blobs/data-lake-storage-namespace.md) eklenmesinin bir özelliğidir. Hiyerarşik yapı, dizinin ad önekini paylaşan tüm nesneleri listelemek ve işlemek yerine dizin üzerinde tek Atomik meta veri işlemleri gibi bir dizini yeniden adlandırma veya silme gibi işlemleri sağlar.

Geçmişte, bulut tabanlı analizler performans, yönetim ve güvenlik alanlarında tehlikeye atabilir. Azure Data Lake Storage (ADLS) Gen2 'in temel özellikleri şunlardır:

- **Hadoop uyumlu erişim**: Azure Data Lake Storage 2., verileri [Hadoop Dağıtılmış dosya sistemi (bir)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html)ile yaptığınız gibi yönetmenizi ve erişmenize olanak tanır. Yeni [ABFS sürücüsü](../../storage/blobs/data-lake-storage-abfs-driver.md) , [Azure HDInsight](../index.yml)'a dahil edilen tüm Apache Hadoop ortamlarında kullanılabilir. Bu sürücü, Data Lake Storage 2. depolanan verilere erişmenizi sağlar.

- **POSIX Izinlerinin bir üst kümesi**: Data Lake Gen2 için güvenlik modeli, Data Lake Storage 2. özgü bazı ayrıntı düzeyi Ile birlikte ACL ve POSIX izinlerini tam olarak destekler. Ayarlar, yönetim araçları aracılığıyla veya Hive ve Spark gibi çerçeveler aracılığıyla yapılandırılabilir.

- Uygun **maliyetli**: Data Lake Storage 2. Özellikler düşük maliyetli depolama kapasitesi ve işlemler. Veriler tam yaşam döngüsü boyunca geçiş yaparken, [Azure Blob depolama yaşam döngüsü](../../storage/blobs/storage-lifecycle-management-concepts.md)gibi yerleşik özellikler aracılığıyla maliyetleri en aza indirmek için faturalandırma ücretleri değişir.

- **BLOB depolama araçları, çerçeveler ve uygulamalarla çalışır**: Data Lake Storage 2., bugün BLOB depolama için mevcut olan çok sayıda araç, çerçeve ve uygulama ile çalışmaya devam eder.

- **İyileştirilmiş sürücü**: Azure Blob FileSystem sürücüsü (ABFS), büyük veri analizi için [özel olarak iyileştirilmiştir](../../storage/blobs/data-lake-storage-abfs-driver.md) . Karşılık gelen REST API 'Leri, dfs.core.windows.net DFS uç noktası üzerinden ortaya çıkmış.

Aşağıdaki biçimlerden biri, ADLS 2. depolanan verilere erişmek için kullanılabilir:
- `abfs:///`: Küme için varsayılan Data Lake Storage erişin.
- `abfs://file_system@account_name.dfs.core.windows.net`: Varsayılan olmayan bir Data Lake Storage iletişim kurulurken kullanılır.

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

- [Azure Data Lake Storage 2. Nesil'e giriş](../../storage/blobs/data-lake-storage-introduction.md)
- [Azure blob dosya sistemi sürücüsü (ABFS.md)](../../storage/blobs/data-lake-storage-abfs-driver.md)
- [Azure HDInsight kümeleriyle Azure Data Lake Storage 2. Nesil hizmetini kullanma](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="secure-azure-storage-keys-within-on-premises-hadoop-cluster-configuration"></a>Şirket içi Hadoop kümesi yapılandırması içindeki güvenli Azure depolama anahtarları

Hadoop yapılandırma dosyalarına eklenen Azure depolama anahtarları, şirket için ve Azure Blob depolama arasında bağlantı kurar. Bu anahtarlar, Hadoop kimlik bilgisi sağlayıcısı çerçevesiyle şifrelenerek korunabilir. Şifrelendikten sonra, güvenli bir şekilde saklanabilir ve erişilebilir.

**Kimlik bilgilerini sağlamak için:**

```bash
hadoop credential create fs.azure.account.key.account.blob.core.windows.net -value <storage key> -provider jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks/file
```

**Yukarıdaki sağlayıcı yolunu core-site.xml veya özel çekirdek site altındaki ambarı yapılandırmasına eklemek için:**

```xml
<property>
    <name>hadoop.security.credential.provider.path</name>
        <value>
        jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks
        </value>
    <description>Path to interrogate for protected credentials.</description>
</property>
```

> [!Note]
> Sağlayıcı yolu özelliği, anahtar core-site.xml aşağıdaki gibi küme düzeyinde depolamak yerine, distcp komut satırına da eklenebilir:

```bash
hadoop distcp -D hadoop.security.credential.provider.path=jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks /user/user1/ wasb:<//yourcontainer@youraccount.blob.core.windows.net/>user1
```

## <a name="restrict-azure-storage-data-access-using-sas"></a>SAS kullanarak Azure depolama veri erişimini kısıtlama

HDInsight, varsayılan olarak, kümeyle ilişkili Azure depolama hesaplarında bulunan verilere tam erişime sahiptir. Blob kapsayıcısında paylaşılan erişim Imzaları (SAS), kullanıcılara salt okuma erişimi sağlamak gibi verilere erişimi kısıtlamak için kullanılabilir.

### <a name="using-the-sas-token-created-with-python"></a>Python ile oluşturulan SAS belirtecini kullanma

1. [SASToken.py](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature/blob/master/Python/SASToken.py) dosyasını açın ve aşağıdaki değerleri değiştirin:

    |Belirteç özelliği|Description|
    |---|---|
    |policy_name|Oluşturulacak saklı ilke için kullanılacak ad.|
    |storage_account_name|Depolama hesabınızın adı.|
    |storage_account_key|Depolama hesabı için anahtar.|
    |storage_container_name|Erişimi kısıtlamak istediğiniz depolama hesabındaki kapsayıcı.|
    |example_file_path|Kapsayıcıya yüklenen bir dosyanın yolu.|

2. SASToken.py dosyası izinlerle birlikte gelir `ContainerPermissions.READ + ContainerPermissions.LIST` ve kullanım örneğine göre ayarlanabilir.

3. Betiği şu şekilde yürütün: `python SASToken.py`

4. Komut dosyası tamamlandığında aşağıdaki metne benzer SAS belirtecini görüntüler: `sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14`

5. Paylaşılan erişim Imzasıyla bir kapsayıcıya erişimi sınırlandırmak için, ambarı, gelişmiş özel çekirdek-site ekleme özelliği altındaki kümenin çekirdek-site yapılandırmasına özel bir giriş ekleyin.

6. **Anahtar** ve **değer** alanları için aşağıdaki değerleri kullanın:

    **Anahtar**: `fs.azure.sas.YOURCONTAINER.YOURACCOUNT.blob.core.windows.net` **değer**: Yukarıdaki 4. adımda Python uygulaması tarafından döndürülen SAS anahtarı.

7. Bu anahtarı ve değeri kaydetmek için **Ekle** düğmesine tıklayın ve sonra yapılandırma değişikliklerini kaydetmek için **Kaydet** düğmesine tıklayın. İstendiğinde, değişikliğin açıklamasını ekleyin (örneğin, "SAS depolama erişimi ekleme") ve ardından **Kaydet**' e tıklayın.

8. Ambarı Web Kullanıcı arabiriminde, sol taraftaki listeden işlemler ' i seçin ve ardından sağdaki hizmet eylemleri açılan listesinden **etkilenen tümünü yeniden Başlat** ' ı seçin. İstendiğinde, **Tümünü Yeniden Başlat**' ı seçin.

9. MapReduce2 ve YARN için bu işlemi tekrarlayın.

Azure 'da SAS belirteçleri kullanımı hakkında dikkat etmeniz gereken üç önemli nokta vardır:

1. SAS belirteçleri "okuma + LISTE" izinleriyle oluşturulduğunda, bu SAS belirtecine sahip blob kapsayıcısına erişen kullanıcılar verileri "yazamayacak ve silemiyor". Blob kapsayıcısına bu SAS belirtecine erişen ve yazma veya silme işlemini deneyen kullanıcılar, gibi bir ileti alır `"This request is not authorized to perform this operation"` .

2. SAS belirteçleri `READ + LIST + WRITE` izinlerle oluşturulduğunda (yalnızca kısıtlamak için `DELETE` ), `hadoop fs -put` bir dosyaya ilk yazma gibi komutlar `\_COPYING\_` ve sonra dosyayı yeniden adlandırmayı deneyin. Bu işlem, için bir ile eşlenir `copy+delete` . `DELETE`İzin sağlanmadığından "put" başarısız olur. `\_COPYING\_`İşlem, bazı eşzamanlılık denetimi sağlamak için tasarlanan bir Hadoop özelliğidir. Şu anda "yazma" işlemlerini etkilemeden yalnızca "SIL" işlemini kısıtlamak için bir yol yoktur.

3. Ne yazık ki, Hadoop kimlik bilgisi sağlayıcısı ve şifre çözme anahtar sağlayıcısı (ShellDecryptionKeyProvider) Şu anda SAS belirteçleriyle çalışmıyor ve bu nedenle şu anda görünürlüğe karşı korunamaz.

Daha fazla bilgi için bkz. [HDInsight 'taki verilere erişimi kısıtlamak Için Azure depolama paylaşılan erişim Imzalarını kullanma](../hdinsight-storage-sharedaccesssignature-permissions.md).

## <a name="use-data-encryption-and-replication"></a>Veri şifrelemeyi ve çoğaltmayı kullanma

Azure depolama 'ya yazılan tüm veriler [depolama hizmeti şifrelemesi (SSE)](../../storage/common/storage-service-encryption.md)kullanılarak otomatik olarak şifrelenir. Azure depolama hesabındaki veriler, her zaman yüksek kullanılabilirlik için çoğaltılır. Bir depolama hesabı oluşturduğunuzda, aşağıdaki çoğaltma seçeneklerinden birini belirleyebilirsiniz:

- [Yerel olarak yedekli depolama (LRS)](../../storage/common/storage-redundancy.md#locally-redundant-storage)
- [Alanlar arası yedekli depolama (ZRS)](../../storage/common/storage-redundancy.md#zone-redundant-storage)
- [Coğrafi olarak yedekli depolama (GRS)](../../storage/common/storage-redundancy.md#geo-redundant-storage)
- [Okuma erişimli coğrafi olarak yedekli depolama (RA-GRS)](../../storage/common/storage-redundancy.md)

Azure depolama, yerel olarak yedekli depolama (LRS) sağlar, ancak önemli verileri olağanüstü durum kurtarma planının ihtiyaçlarına göre bir sıklık ile başka bir bölgedeki başka bir Azure depolama hesabına de kopyalamanız gerekir. [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md), [distcp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html), [Azure PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md)veya [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)dahil olmak üzere verileri kopyalamak için farklı yöntemler vardır. Yanlışlıkla silinmeye engel olmak için Azure Storage hesabı için erişim ilkelerini zorlamak de önerilir.

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

- [Azure Depolama çoğaltması](../../storage/common/storage-redundancy.md)
- [Azure Data Lake Storage 1. (ADLS) için olağanüstü durum Kılavuzu](../../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

## <a name="attach-additional-azure-storage-accounts-to-cluster"></a>Kümeye ek Azure depolama hesapları iliştirme

HDInsight oluşturma işlemi sırasında, varsayılan dosya sistemi olarak bir Azure depolama hesabı, Azure Data Lake Storage 1. veya Azure Data Lake Storage 2. seçilir. Bu varsayılan depolama hesabına ek olarak, küme oluşturma işlemi sırasında veya bir küme oluşturulduktan sonra aynı Azure aboneliğinden veya farklı Azure aboneliklerinden ek depolama hesapları eklenebilir.

Ek depolama hesabı, aşağıdaki yollarla bir birine eklenebilir:
- Ambarı bir yapılandırma gelişmiş özel çekirdek-site depolama hesabı adını ve anahtarını ekleme hizmetleri yeniden başlatma
- Depolama hesabı adını ve anahtarını geçirerek [betik eylemi](../hdinsight-hadoop-add-storage.md) kullanma

> [!Note]
> Geçerli kullanım durumlarında, Azure depolama 'daki sınırlamalar [Azure desteği](https://azure.microsoft.com/support/faq/)'ne yapılan bir istek aracılığıyla artırılabilir.

Daha fazla bilgi için bkz. [HDInsight’a başka depolama hesapları ekleme](../hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu serideki bir sonraki makaleyi okuyun: [geçiş Azure HDInsight Hadoop için şirket Içi veri geçişi en iyi yöntemleri](apache-hadoop-on-premises-migration-best-practices-data-migration.md).