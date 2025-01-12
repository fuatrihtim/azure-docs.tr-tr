---
title: Azure SYNAPSE Analytics 'e/verilerine veri kopyalama
description: Azure Data Factory kullanarak Azure SYNAPSE Analytics 'e/verilerine veri kopyalamayı öğrenin
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: aa364ec434db980bf226008537ca928628fcac1b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100392094"
---
# <a name="copy-data-to-and-from-azure-synapse-analytics-using-azure-data-factory"></a>Azure Data Factory kullanarak Azure SYNAPSE Analytics 'e ve veri kopyalama
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-azure-sql-data-warehouse-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [v2 'de Azure SYNAPSE Analytics Bağlayıcısı](../connector-azure-sql-data-warehouse.md).

Bu makalede, Azure SYNAPSE Analytics 'e/verilerine veri taşımak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı açıklanmaktadır. Kopyalama etkinliğiyle veri hareketine genel bir bakış sunan [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde oluşturulur.

> [!TIP]
> En iyi performansı elde etmek için PolyBase 'i kullanarak Azure SYNAPSE Analytics 'e veri yükleyin. [Azure SYNAPSE Analytics 'e veri yüklemek Için PolyBase kullanma](#use-polybase-to-load-data-into-azure-synapse-analytics) bölümünde ayrıntılar bulunur. Kullanım örneği ile ilgili bir anlatım için, [Azure Data Factory ile 15 dakika altında Azure SYNAPSE Analytics 'e 1 TB yükleme](data-factory-load-sql-data-warehouse.md)bölümüne bakın.

## <a name="supported-scenarios"></a>Desteklenen senaryolar
**Azure SYNAPSE Analytics 'ten** aşağıdaki veri depolarına veri kopyalayabilirsiniz:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Aşağıdaki veri depolarından verileri **Azure SYNAPSE Analytics 'e** kopyalayabilirsiniz:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> SQL Server veya Azure SQL veritabanındaki verileri Azure SYNAPSE Analytics 'e kopyalarken, tablo hedef depoda yoksa, Data Factory tabloyu kaynak veri deposundaki tablonun şemasını kullanarak Azure SYNAPSE Analytics 'te otomatik olarak oluşturabilir. Ayrıntılar için bkz. [Otomatik tablo oluşturma](#auto-table-creation) .

## <a name="supported-authentication-type"></a>Desteklenen kimlik doğrulama türü
Azure SYNAPSE Analytics Bağlayıcısı temel kimlik doğrulamasını destekler.

## <a name="getting-started"></a>Kullanmaya başlama
Farklı araçlar/API 'Ler kullanarak Azure SYNAPSE Analytics 'e/verilerine veri taşıyan kopyalama etkinliği ile bir işlem hattı oluşturabilirsiniz.

Azure SYNAPSE Analytics 'e/verilerine veri kopyalayan bir işlem hattı oluşturmanın en kolay yolu, veri kopyalama Sihirbazı 'nı kullanmaktır. Veri kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma hakkında hızlı bir anlatım için bkz. [öğretici: Azure SYNAPSE Analytics 'e veri yükleme Data Factory](../load-azure-sql-data-warehouse.md) .

İşlem hattı oluşturmak için aşağıdaki araçları da kullanabilirsiniz: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager şablonu**, **.NET API** ve **REST API**. Kopyalama etkinliğine sahip bir işlem hattı oluşturmak için adım adım yönergeler için bkz. [kopyalama etkinliği öğreticisi](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Araçları veya API 'Leri kullanıp kullanmayacağınızı bir kaynak veri deposundan havuz veri deposuna veri taşınan bir işlem hattı oluşturmak için aşağıdaki adımları gerçekleştirirsiniz:

1. Bir **Veri Fabrikası** oluşturun. Bir veri fabrikası bir veya daha fazla işlem hattı içerebilir. 
2. Giriş ve çıkış veri depolarını veri fabrikanıza bağlamak için **bağlı hizmetler** oluşturun. Örneğin, bir Azure Blob depolama alanından Azure SYNAPSE Analytics 'e veri kopyalıyorsanız, Azure depolama hesabınızı ve Azure SYNAPSE analizlerinizi veri fabrikanıza bağlamak için iki bağlı hizmet oluşturursunuz. Azure SYNAPSE Analytics 'e özgü bağlı hizmet özellikleri için bkz. [bağlı hizmet özellikleri](#linked-service-properties) bölümü. 
3. Kopyalama işlemi için girdi ve çıktı verilerini temsil edecek **veri kümeleri** oluşturun. Son adımda bahsedilen örnekte, blob kapsayıcısını ve girdi verilerini içeren klasörü belirtmek için bir veri kümesi oluşturursunuz. Ve, blob depolamadan kopyalanmış verileri tutan Azure SYNAPSE Analytics 'te tabloyu belirtmek için başka bir veri kümesi oluşturursunuz. Azure SYNAPSE Analytics 'e özgü veri kümesi özellikleri için bkz. [veri kümesi özellikleri](#dataset-properties) bölümü.
4. Bir veri kümesini girdi olarak ve bir veri kümesini çıkış olarak alan kopyalama etkinliği ile bir işlem **hattı** oluşturun. Daha önce bahsedilen örnekte, BlobSource değerini kaynak ve SqlDWSink olarak kopyalama etkinliği için havuz olarak kullanırsınız. Benzer şekilde, Azure SYNAPSE Analytics 'ten Azure Blob depolama alanına kopyalama yapıyorsanız kopyalama etkinliğinde SqlDWSource ve BlobSink kullanın. Azure SYNAPSE Analytics 'e özgü kopyalama etkinliği özellikleri için bkz. [kopyalama etkinliği özellikleri](#copy-activity-properties) bölümü. Bir veri deposunu kaynak veya havuz olarak kullanma hakkında ayrıntılı bilgi için, veri deponuzdaki önceki bölümde yer alan bağlantıya tıklayın.

Sihirbazı kullandığınızda, bu Data Factory varlıkların JSON tanımları (bağlı hizmetler, veri kümeleri ve işlem hattı) sizin için otomatik olarak oluşturulur. Araçlar/API 'Leri (.NET API hariç) kullandığınızda, bu Data Factory varlıkları JSON biçimini kullanarak tanımlarsınız. Azure SYNAPSE Analytics 'e/verilerine veri kopyalamak için kullanılan Data Factory varlıkları için JSON tanımları içeren örnekler için, bu makalenin [JSON örnekleri](#json-examples-for-copying-data-to-and-from-azure-synapse-analytics) bölümüne bakın.

Aşağıdaki bölümler, Azure SYNAPSE Analytics 'e özgü Data Factory varlıkları tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılı bilgi sağlar:

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri
Aşağıdaki tabloda, Azure SYNAPSE Analytics bağlı hizmetine özgü JSON öğeleri için açıklama verilmiştir.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| tür |Type özelliği: **Azuresqldw** olarak ayarlanmalıdır |Yes |
| Dizisi |ConnectionString özelliği için Azure SYNAPSE Analytics örneğine bağlanmak için gereken bilgileri belirtin. Yalnızca temel kimlik doğrulaması desteklenir. |Yes |

> [!IMPORTANT]
> Azure [hizmetlerinin sunucuya erişmesine izin](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure)vermek IÇIN [Azure SQL veritabanı güvenlik duvarını](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure) ve veritabanı sunucusunu yapılandırın. Ayrıca, Data Factory ağ geçidine sahip şirket içi veri kaynaklarından dahil olmak üzere Azure dışından Azure SYNAPSE Analytics 'e veri kopyalıyorsanız, verileri Azure SYNAPSE Analytics 'e gönderen makine için uygun IP adresi aralığını yapılandırın.

## <a name="dataset-properties"></a>Veri kümesi özellikleri
Veri kümelerini tanımlamaya yönelik özellikler & bölümlerin tam listesi için bkz. [veri kümeleri oluşturma](data-factory-create-datasets.md) makalesi. Bir veri kümesinin yapısı, kullanılabilirliği ve İlkesi gibi bölümler, tüm veri kümesi türleri (Azure SQL, Azure blob, Azure tablosu vb.) için benzerdir.

TypeProperties bölümü her bir veri kümesi türü için farklıdır ve veri deposundaki verilerin konumu hakkında bilgi sağlar. **Azuresqldwtable** türündeki veri kümesinin **typeproperties** bölümü aşağıdaki özelliklere sahiptir:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| tableName |Bağlı hizmetin başvurduğu Azure SYNAPSE Analytics veritabanında tablonun veya görünümün adı. |Yes |

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri
Etkinlikleri tanımlamaya yönelik bölüm & özelliklerinin tam listesi için, işlem [hatları oluşturma](data-factory-create-pipelines.md) makalesine bakın. Ad, açıklama, giriş ve çıkış tabloları ve ilke gibi özellikler, tüm etkinlik türleri için kullanılabilir.

> [!NOTE]
> Kopyalama etkinliği yalnızca bir girdi alır ve yalnızca bir çıktı üretir.

Ancak, etkinliğin typeProperties bölümünde kullanılabilen özellikler her etkinlik türüyle farklılık gösterir. Kopyalama etkinliği için, kaynak ve havuz türlerine göre farklılık gösterir.

### <a name="sqldwsource"></a>SqlDWSource
Kaynak **Sqldwsource** türünde olduğunda, **typeproperties** bölümünde aşağıdaki özellikler mevcuttur:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sqlReaderQuery |Verileri okumak için özel sorguyu kullanın. |SQL sorgu dizesi. Örneğin: select * from MyTable. |No |
| sqlReaderStoredProcedureName |Kaynak tablodaki verileri okuyan saklı yordamın adı. |Saklı yordamın adı. Son SQL ifadesinin saklı yordamda bir SELECT ifadesinin olması gerekir. |No |
| storedProcedureParameters |Saklı yordamın parametreleri. |Ad/değer çiftleri. Parametrelerin adları ve büyük harfleri, saklı yordam parametrelerinin adlarıyla ve büyük küçük harfleriyle aynı olmalıdır. |No |

SqlDWSource için **Sqlreaderquery** belirtilmişse, kopyalama etkinliği verileri almak için bu sorguyu Azure SYNAPSE Analytics kaynağında çalıştırır.

Alternatif olarak, **sqlReaderStoredProcedureName** ve **storedProcedureParameters** (saklı yordam parametreler alırsa) belirterek bir saklı yordam belirtebilirsiniz.

SqlReaderQuery veya sqlReaderStoredProcedureName belirtmezseniz, JSON veri kümesinin yapı bölümünde tanımlanan sütunlar, Azure SYNAPSE Analytics 'te çalıştırılacak bir sorgu oluşturmak için kullanılır. Örnek: `select column1, column2 from mytable`. Veri kümesi tanımında yapı yoksa, tablodaki tüm sütunlar seçilir.

#### <a name="sqldwsource-example"></a>SqlDWSource örneği

```JSON
"source": {
    "type": "SqlDWSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```
**Saklı yordam tanımı:**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqldwsink"></a>SqlDWSink
**Sqldwsink** aşağıdaki özellikleri destekler:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Bir kopyalama etkinliğinin yürütülmesi için belirli bir dilim verilerinin temizlenmesi için bir sorgu belirtin. Ayrıntılar için bkz. [yinelenebilirlik bölümü](#repeatability-during-copy). |Sorgu ekstresi. |No |
| allowPolyBase |BULKıNSERT mekanizması yerine PolyBase 'in (uygun olduğunda) kullanılıp kullanılmayacağını belirtir. <br/><br/> **PolyBase 'in kullanılması, verileri Azure SYNAPSE Analytics 'e yüklemek için önerilen yoldur.** Kısıtlamalar ve Ayrıntılar için bkz. [Azure SYNAPSE Analytics 'e veri yüklemek Için PolyBase kullanma](#use-polybase-to-load-data-into-azure-synapse-analytics) . |Doğru <br/>False (varsayılan) |No |
| polyBaseSettings |**Allowpolybase** özelliği **true** olarak ayarlandığında belirtilenebilir bir özellik grubu. |&nbsp; |No |
| rejectValue |Sorgu başarısız olmadan önce reddedilecek satırların sayısını veya yüzdesini belirtir. <br/><br/>[Dış tablo oluşturma (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql) konusunun **bağımsız değişkenler** bölümünde PolyBase 'in reddetme seçenekleri hakkında daha fazla bilgi edinin. |0 (varsayılan), 1, 2,... |No |
| rejectType |RejectValue seçeneğinin sabit değer değeri mi yoksa yüzde olarak mı belirtilmediğini belirtir. |Değer (varsayılan), yüzde |No |
| rejectSampleValue |PolyBase reddedilen satırların yüzdesini yeniden hesaplamadan önce alınacak satır sayısını belirler. |1, 2,... |Evet, **rejectType** **ise** |
| useTypeDefault |PolyBase metin dosyasından verileri aldığında, sınırlandırılmış metin dosyalarında eksik değerlerin nasıl işleneceğini belirtir.<br/><br/>[Dış dosya biçimi oluşturma (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql)Içindeki bağımsız değişkenler bölümünden bu özellik hakkında daha fazla bilgi edinin. |True, false (varsayılan) |No |
| writeBatchSize |Arabellek boyutu writeBatchSize ulaştığında verileri SQL tablosuna ekler |Tamsayı (satır sayısı) |Hayır (varsayılan: 10000) |
| writeBatchTimeout |Toplu ekleme işleminin, zaman aşımına uğramadan önce tamamlaması için bekleme süresi. |timespan<br/><br/> Örnek: "00:30:00" (30 dakika). |No |

#### <a name="sqldwsink-example"></a>SqlDWSink örneği

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'e veri yüklemek için PolyBase kullanma
**[PolyBase](/sql/relational-databases/polybase/polybase-guide)** 'in kullanılması, büyük miktarda veriyi yüksek aktarım hızı Ile Azure SYNAPSE Analytics 'e yüklemenin etkili bir yoludur. Varsayılan BULKıNSERT mekanizması yerine PolyBase kullanarak üretilen iş için büyük bir kazanç görebilirsiniz. Bkz. ayrıntılı karşılaştırma ile [kopyalama performansı başvuru numarası](data-factory-copy-activity-performance.md#performance-reference) . Kullanım örneği ile ilgili bir anlatım için, [Azure Data Factory ile 15 dakika altında Azure SYNAPSE Analytics 'e 1 TB yükleme](data-factory-load-sql-data-warehouse.md)bölümüne bakın.

* Kaynak verileriniz **Azure Blobu veya Azure Data Lake Store** ise ve biçim PolyBase ile uyumluysa, PolyBase kullanarak doğrudan Azure SYNAPSE Analytics 'e kopyalayabilirsiniz. Bkz. **[PolyBase 'i kullanarak doğrudan kopyalama](#direct-copy-using-polybase)** ayrıntıları.
* Kaynak veri deponuzu ve formatı İlk olarak PolyBase tarafından desteklenmiyorsa, bunun yerine **[PolyBase özelliğini kullanarak hazırlanan kopyayı](#staged-copy-using-polybase)** kullanabilirsiniz. Ayrıca, verileri otomatik olarak PolyBase uyumlu biçime dönüştürerek ve verileri Azure Blob depolamada depolayarak daha iyi bir aktarım hızı sağlar. Daha sonra verileri Azure SYNAPSE Analytics 'e yükler.

`allowPolyBase`Verileri Azure SYNAPSE Analytics 'e kopyalamak Için PolyBase 'i kullanmak Azure Data Factory için aşağıdaki örnekte gösterildiği gibi özelliği **true** olarak ayarlayın. AllowPolyBase 'i true olarak belirlediğinizde, özellik grubunu kullanarak PolyBase 'e özgü özellikleri belirtebilirsiniz `polyBaseSettings` . polyBaseSettings ile kullanabileceğiniz özellikler hakkında ayrıntılı bilgi edinmek için [Sqldwsink](#sqldwsink) bölümüne bakın.

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

### <a name="direct-copy-using-polybase"></a>PolyBase kullanarak doğrudan kopyalama
Azure SYNAPSE Analytics PolyBase, kaynak olarak ve belirli dosya biçimi gereksinimleriyle Azure Blob ve Azure Data Lake Store (hizmet sorumlusu kullanarak) doğrudan destek. Kaynak verileriniz bu bölümde açıklanan ölçütlere uyuyorsa, PolyBase kullanarak kaynak veri deposundan doğrudan Azure SYNAPSE Analytics 'e kopyalayabilirsiniz. Aksi halde, [PolyBase kullanarak hazırlanmış kopyayı](#staged-copy-using-polybase)kullanabilirsiniz.

> [!TIP]
> Data Lake Store verileri Azure SYNAPSE Analytics 'e verimli bir şekilde kopyalamak için Azure Data Factory 'tan daha fazla bilgi edinmek için [Azure SYNAPSE Analytics ile Data Lake Store kullanırken verilerin öngörülerini açığa çıkarmak daha da kolay ve kullanışlı hale gelir](/archive/blogs/azuredatalake/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse).

Gereksinimler karşılanmazsa, Azure Data Factory ayarları denetler ve veri taşıma için otomatik olarak BULKıNSERT mekanizmasına geri döner.

1. **Kaynak bağlı hizmet** , **hizmet sorumlusu kimlik doğrulamasıyla**: **azurestorage** veya AzureDataLakeStore türünde.
2. **Giriş veri kümesi** : **AzureBlob** veya **AzureDataLakeStore** ve Properties altındaki Biçim türü, `type` aşağıdaki yapılandırmalarda **orcformat**, **parquetformat** veya **TextFormat** ' dir:

   1. `rowDelimiter`**\n** olmalıdır.
   2. `nullValue`**boş dizeye** ayarlanır ("") veya `treatEmptyAsNull` **true** olarak ayarlanır.
   3. `encodingName`, **varsayılan** değer olan **UTF-8** olarak ayarlanır.
   4. `escapeChar`, `quoteChar` , `firstRowAsHeader` , ve `skipLineCount` belirtilmedi.
   5. `compression`**sıkıştırma**, **gzip** veya **söndür** olamaz.

      ```JSON
      "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8"
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
      },
      ```

3. İşlem hattındaki `skipHeaderLineCount` kopyalama etkinliği Için **Blobsource** veya **AzureDataLakeStore** altında bir ayar yoktur.
4. İşlem hattındaki `sliceIdentifierColumnName` kopyalama etkinliği Için **Sqldwsink** altında bir ayar yoktur. (PolyBase, tüm verilerin güncelleştirildiğini veya tek bir çalıştırmada hiçbir şey güncelleştirilmediğini garanti eder. **Yinelenebilirlik** ulaşmak için kullanabilirsiniz `sqlWriterCleanupScript` .
5. `columnMapping`Ilişkili kopyalama etkinliğinde kullanılmıyor.

### <a name="staged-copy-using-polybase"></a>PolyBase kullanarak hazırlanmış kopya
Kaynak verileriniz önceki bölümde tanıtılan ölçütlere uymazsa, verileri geçici hazırlama Azure Blob depolama (Premium Depolama olamaz) aracılığıyla kopyalamayı etkinleştirebilirsiniz. Bu durumda Azure Data Factory, PolyBase 'in veri biçimi gereksinimlerini karşılamak üzere verilerde otomatik olarak dönüşümler gerçekleştirir, ardından PolyBase kullanarak Azure SYNAPSE Analytics 'e veri yükleyebilir ve BLOB depolamadan geçici verilerinizi en son temizleyelim. Hazırlama Azure blobu aracılığıyla verilerin nasıl kopyalandığı hakkında ayrıntılı bilgi için bkz. [aşamalı kopya](data-factory-copy-activity-performance.md#staged-copy) , genel olarak çalışma.

> [!NOTE]
> PolyBase ve hazırlama kullanarak şirket içi veri deposundaki verileri Azure SYNAPSE Analytics 'e kopyalarken, Veri Yönetimi ağ geçidi sürümünüz 2,4 altındaysa, kaynak verilerinizi doğru biçime dönüştürmek için kullanılan ağ geçidi makinenizde JRE (Java Runtime Environment) gereklidir. Böyle bir bağımlılığı önlemek için ağ geçidinizi en son sürümüne yükseltmenizi önerin.
>

Bu özelliği kullanmak için, ara BLOB depolama alanına sahip Azure depolama hesabına başvuran bir [Azure depolama bağlı hizmeti](data-factory-azure-blob-connector.md#azure-storage-linked-service) oluşturun, ardından `enableStaging` `stagingSettings` aşağıdaki kodda gösterildiği gibi kopyalama etkinliğinin ve özelliklerini belirtin:

```json
"activities":[
{
    "name": "Sample copy activity from SQL Server to Azure Synapse Analytics via PolyBase",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDWOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlDwSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob"
        }
    }
}
]
```

## <a name="best-practices-when-using-polybase"></a>PolyBase kullanırken en iyi uygulamalar
Aşağıdaki bölümlerde, [Azure SYNAPSE Analytics Için en iyi yöntemler](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md)bölümünde bahsedilen diğer en iyi yöntemler sağlanmaktadır.

### <a name="required-database-permission"></a>Gerekli veritabanı izni
PolyBase 'i kullanmak için, Azure SYNAPSE Analytics 'e veri yüklemek üzere kullanılan kullanıcının hedef veritabanında ["Control" iznine](/sql/relational-databases/security/permissions-database-engine) sahip olması gerekir. Bunu yapmanın bir yolu, bu kullanıcıyı "db_owner" rolünün bir üyesi olarak eklemektir. [Bu bölümü](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization)izleyerek bunu nasıl yapacağınızı öğrenin.

### <a name="row-size-and-data-type-limitation"></a>Satır boyutu ve veri türü sınırlaması
PolyBase yükleri **1 MB** 'tan küçük satırları yüklemek ve VARCHR (max), nvarchar (max) veya VARBINARY (max) öğesine yüklenemez. [Buraya](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads)bakın.

Boyutu 1 MB 'tan büyük olan kaynak verileriniz varsa, kaynak tabloları her birinin en büyük satır boyutunun sınırı aşmadığı birkaç küçük yere bölmek isteyebilirsiniz. Daha sonra daha küçük tablolar PolyBase kullanılarak yüklenebilir ve Azure SYNAPSE Analytics 'te birlikte birleştirilir.

### <a name="azure-synapse-analytics-resource-class"></a>Azure SYNAPSE Analytics kaynak sınıfı
Mümkün olan en iyi verimi elde etmek için, PolyBase aracılığıyla Azure SYNAPSE Analytics 'e veri yüklemek üzere kullanılan kullanıcıya daha büyük kaynak sınıfı atamayı göz önünde bulundurun. Bunun nasıl yapılacağını, [bir Kullanıcı kaynak sınıfı örneğini değiştirme](../../synapse-analytics/sql-data-warehouse/resource-classes-for-workload-management.md)hakkında bilgi edinin.

### <a name="tablename-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te tableName
Aşağıdaki tabloda, şema ve tablo adının çeşitli birleşimleri için veri kümesi JSON içinde **TableName** özelliğinin nasıl belirtilme hakkında örnekler verilmektedir.

| DB şeması | Tablo adı | tableName JSON özelliği |
| --- | --- | --- |
| dbo |MyTable |MyTable veya dbo. MyTable veya [dbo]. MyTable |
| dbo1 |MyTable |dbo1. MyTable veya [dbo1]. MyTable |
| dbo |My. Table |[My. Table] veya [dbo]. [My. Table] |
| dbo1 |My. Table |[dbo1]. [My. Table] |

Aşağıdaki hatayı görürseniz, tableName özelliği için belirttiğiniz değerle ilgili bir sorun olabilir. TableName JSON özelliği için değerleri belirtmenin doğru yolu için tabloya bakın.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Varsayılan değerlere sahip sütunlar
Şu anda Data Factory PolyBase özelliği, hedef tablodaki aynı sayıda sütunu kabul eder. Dört sütunlu bir tablonuz olduğunu ve bunlardan birinin varsayılan bir değerle tanımlandığını varsayalım. Giriş verileri hala dört sütun içermelidir. 3 sütunlu bir giriş veri kümesi sağlamak aşağıdaki iletiye benzer bir hata verir:

```
All columns of the table must be specified in the INSERT BULK statement.
```
NULL değer, varsayılan değer olan özel bir formdur. Sütun null atanabilir ise, bu sütun için giriş verileri (blob 'ta) boş olabilir (girdi veri kümesinde eksik olamaz). PolyBase bunlar için Azure SYNAPSE Analytics 'te NULL değer ekler.

## <a name="auto-table-creation"></a>Otomatik tablo oluşturma
SQL Server veya Azure SQL veritabanından Azure SYNAPSE Analytics 'e veri kopyalamak için kopyalama Sihirbazı 'Nı kullanıyorsanız ve kaynak tabloya karşılık gelen tablo hedef depoda yoksa, Data Factory tabloyu kaynak tablo şemasını kullanarak otomatik olarak veri ambarında oluşturabilir.

Data Factory, kaynak veri deposunda aynı tablo adına sahip hedef depoda tablo oluşturur. Sütunların veri türleri aşağıdaki tür eşlemesine göre seçilir. Gerekirse, kaynak ve hedef depolarla ilgili uyumsuzlukları onarmak için tür dönüştürmeleri gerçekleştirir. Ayrıca hepsini bir kez deneme tablo dağıtımı kullanır.

| Kaynak SQL veritabanı sütun türü | Hedef Azure SYNAPSE Analytics sütun türü (boyut sınırlaması) |
| --- | --- |
| int | int |
| BigInt | BigInt |
| Small | Small |
| Iç | Iç |
| Sürümleri | Sürümleri |
| Ondalık | Ondalık |
| Sayısal | Ondalık |
| Float | Float |
| Para | Para |
| Gerçek | Gerçek |
| Küçük para | Küçük para |
| İkili | İkili |
| İkili | Varbinary (8000 'e kadar) |
| Tarih | Tarih |
| DateTime | DateTime |
| DateTime2 | DateTime2 |
| Saat | Saat |
| DateTimeOffset | DateTimeOffset |
| Girişin | Girişin |
| Metin | Varchar (8000 'e kadar) |
| N | NVarChar (4000 'e kadar) |
| Görüntü | VarBinary (8000 'e kadar) |
| Benzersiz tanımlayıcı | Benzersiz tanımlayıcı |
| Char | Char |
| NChar | NChar |
| VarChar | VarChar (8000 'e kadar) |
| NVarChar | NVarChar (4000 'e kadar) |
| Xml | Varchar (8000 'e kadar) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-synapse-analytics"></a>Azure SYNAPSE Analytics için tür eşleme
[Veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde belirtildiği gibi kopyalama etkinliği, aşağıdaki 2 adımlı yaklaşımla kaynak türlerindeki otomatik tür dönüştürmeleri, havuz türlerine uygular:

1. Yerel kaynak türlerinden .NET türüne Dönüştür
2. .NET türünden yerel havuz türüne Dönüştür

Verileri Azure SYNAPSE Analytics 'ten & taşırken, SQL türünden .NET türüne ve tam tersi de aşağıdaki eşlemeler kullanılır.

Eşleme, [ADO.NET için SQL Server veri türü eşlemesi](/dotnet/framework/data/adonet/sql-server-data-type-mappings)ile aynıdır.

| SQL Server veritabanı altyapısı türü | .NET Framework türü |
| --- | --- |
| bigint |Int64 |
| ikili |Byte [] |
| bit |Boole |
| char |Dize, Char [] |
| date |DateTime |
| Tarih saat |DateTime |
| datetime2 |DateTime |
| Türünde |DateTimeOffset |
| Ondalık |Ondalık |
| FıLESTREAM özniteliği (varbinary (max)) |Byte [] |
| Float |Çift |
| image |Byte [] |
| int |Int32 |
| etmenize |Ondalık |
| nchar |Dize, Char [] |
| n |Dize, Char [] |
| sayısal |Ondalık |
| nvarchar |Dize, Char [] |
| real |Tek |
| rowversion |Byte [] |
| girişin |DateTime |
| smallint |Int16 |
| küçük para |Ondalık |
| sql_variant |Nesne |
| metin |Dize, Char [] |
| time |TimeSpan |
| timestamp |Byte [] |
| tinyint |Bayt |
| uniqueidentifier |Guid |
| ikili |Byte [] |
| varchar |Dize, Char [] |
| xml |Xml |

Ayrıca, kaynak veri kümesindeki sütunları, kopyalama etkinliği tanımındaki havuz veri kümesinden sütunlara eşleyebilirsiniz. Ayrıntılar için bkz. [Azure Data Factory veri kümesi sütunlarını eşleme](data-factory-map-columns.md).

## <a name="json-examples-for-copying-data-to-and-from-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'e veri kopyalamaya yönelik JSON örnekleri
Aşağıdaki örnekler, [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) veya [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)kullanarak bir işlem hattı oluşturmak için kullanabileceğiniz örnek JSON tanımlarını sağlar. Azure SYNAPSE Analytics ve Azure Blob depolama 'ya veri kopyalamayı gösterir. Ancak, veriler, Azure Data Factory ' deki kopyalama etkinliği kullanılarak, [burada](data-factory-data-movement-activities.md#supported-data-stores-and-formats) belirtilen herhangi bir kaynaktan herhangi bir kaynağa **doğrudan** kopyalanabilir.

### <a name="example-copy-data-from-azure-synapse-analytics-to-azure-blob"></a>Örnek: Azure SYNAPSE Analytics 'ten Azure Blob 'a veri kopyalama
Örnek, aşağıdaki Data Factory varlıklarını tanımlar:

1. [Azuresqldw](#linked-service-properties)türünde bağlı bir hizmet.
2. [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties)türünde bağlı bir hizmet.
3. [Azuresqldwtable](#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
5. [Sqldwsource](#copy-activity-properties) ve [Blobsink](data-factory-azure-blob-connector.md#copy-activity-properties)kullanan kopyalama etkinliğine sahip bir işlem [hattı](data-factory-create-pipelines.md) .

Örnek, zaman serisi (saatlik, günlük, vb.) verileri Azure SYNAPSE Analytics veritabanındaki bir tablodan her saat bir bloba kopyalar. Bu örneklerde kullanılan JSON özellikleri, örnekleri takip eden bölümlerde açıklanmıştır.

**Azure SYNAPSE Analytics bağlı hizmeti:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure Blob depolama bağlı hizmeti:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure SYNAPSE Analytics giriş veri kümesi:**

Örnek, Azure SYNAPSE Analytics 'te bir "MyTable" tablosu oluşturduğunuzu ve zaman serisi verileri için "timestampcolumn" adlı bir sütun içerdiğini varsayar.

"External": "true" ayarı, veri kümesinin veri fabrikasında dış olduğunu ve veri fabrikasındaki bir etkinlik tarafından üretilmediğini Data Factory hizmetine bildirir.

```JSON
{
  "name": "AzureSqlDWInput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Azure Blob çıktı veri kümesi:**

Veriler her saat yeni bir bloba yazılır (sıklık: saat, Aralık: 1). Blob 'un klasör yolu, işlenmekte olan dilimin başlangıç zamanına göre dinamik olarak değerlendirilir. Klasör yolu başlangıç zamanının yıl, ay, gün ve saat kısımlarını kullanır.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**SqlDWSource ve BlobSink ile işlem hattındaki etkinliği kopyalama:**

İşlem hattı, giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış bir kopyalama etkinliği içerir ve her saat çalışacak şekilde zamanlanır. Ardışık düzen JSON tanımında **kaynak** türü **sqldwsource** olarak ayarlanır ve **Havuz** türü **blobsink** olarak ayarlanır. **Sqlreaderquery** özelliği IÇIN belirtilen SQL sorgusu, kopyalamanın Son saatteki verilerini seçer.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLDWtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSqlDWInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlDWSource",
            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
> [!NOTE]
> Örnekte, SqlDWSource için **Sqlreaderquery** belirtilir. Kopyalama etkinliği, verileri almak için Azure SYNAPSE Analytics kaynağında bu sorguyu çalıştırır.
>
> Alternatif olarak, **sqlReaderStoredProcedureName** ve **storedProcedureParameters** (saklı yordam parametreler alırsa) belirterek bir saklı yordam belirtebilirsiniz.
>
> SqlReaderQuery veya sqlReaderStoredProcedureName belirtmezseniz, JSON veri kümesinin yapı bölümünde tanımlanan sütunlar, Azure SYNAPSE Analytics 'te çalıştırmak için bir sorgu oluşturmak için kullanılır (myTable, Sütun2 'den Sütun1 'yi seçin). Veri kümesi tanımında yapı yoksa, tablodaki tüm sütunlar seçilir.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-synapse-analytics"></a>Örnek: Azure Blobundan Azure SYNAPSE Analytics 'e veri kopyalama
Örnek, aşağıdaki Data Factory varlıklarını tanımlar:

1. [Azuresqldw](#linked-service-properties)türünde bağlı bir hizmet.
2. [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties)türünde bağlı bir hizmet.
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
4. [Azuresqldwtable](#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
5. [Blobsource](data-factory-azure-blob-connector.md#copy-activity-properties) ve [Sqldwsink](#copy-activity-properties)kullanan kopyalama etkinliğine sahip bir işlem [hattı](data-factory-create-pipelines.md) .

Örnek, zaman serisi verilerini (saatlik, günlük, vb.) Azure blobundan her saatte bir Azure SYNAPSE Analytics veritabanındaki tabloya kopyalar. Bu örneklerde kullanılan JSON özellikleri, örnekleri takip eden bölümlerde açıklanmıştır.

**Azure SYNAPSE Analytics bağlı hizmeti:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure Blob depolama bağlı hizmeti:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure Blob giriş veri kümesi:**

Veriler her saat yeni bir bloba alınır (sıklık: saat, Aralık: 1). Blob için klasör yolu ve dosya adı, işlenmekte olan dilimin başlangıç zamanına göre dinamik olarak değerlendirilir. Klasör yolu başlangıç zamanının yıl, ay ve gün bölümünü ve dosya adını kullanır başlangıç zamanının saat kısmını kullanır. "External": "true" ayarı, bu tablonun veri fabrikasında dış olduğunu ve veri fabrikasında bir etkinlik tarafından üretilmediğini Data Factory hizmetine bildirir.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Azure SYNAPSE Analytics çıkış veri kümesi:**

Örnek, verileri Azure SYNAPSE Analytics 'te "MyTable" adlı bir tabloya kopyalar. Blob CSV dosyasının içermesini istediğiniz sütun sayısıyla aynı sayıda sütunla Azure SYNAPSE Analytics 'te tablo oluşturun. Yeni satırlar tabloya her saat eklenir.

```JSON
{
  "name": "AzureSqlDWOutput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**BlobSource ve SqlDWSink ile işlem hattındaki etkinliği kopyalama:**

İşlem hattı, giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış bir kopyalama etkinliği içerir ve her saat çalışacak şekilde zamanlanır. Ardışık düzen JSON tanımında **kaynak** türü **blobsource** olarak ayarlanır ve **Havuz** türü **sqldwsink** olarak ayarlanır.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQLDW",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlDWOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
İzlenecek yol için, Azure SYNAPSE Analytics belgelerindeki [Azure Data Factory](data-factory-load-sql-data-warehouse.md) ve [Azure Data Factory ile verileri yükleme](../load-azure-sql-data-warehouse.md) makalesine bakın.

## <a name="performance-and-tuning"></a>Performans ve ayarlama
Veri taşıma (kopyalama etkinliği) performansını Azure Data Factory ve en iyileştirmek için çeşitli yollarla etkileyen temel faktörlerle ilgili bilgi edinmek için bkz. [etkinlik performansını kopyalama & ayarlama Kılavuzu](data-factory-copy-activity-performance.md) .