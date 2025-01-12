---
title: Amazon Redshift 'tan veri kopyalama
description: Azure Data Factory kullanarak Amazon Redshift 'tan desteklenen havuz veri depolarına veri kopyalama hakkında bilgi edinin.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 9441885766dad97dfc237ab81a59710245bf13ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100364268"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Azure Data Factory kullanarak Amazon Redshift 'tan veri kopyalama
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-amazon-redshift-connector.md)
> * [Güncel sürüm](connector-amazon-redshift.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, bir Amazon Redshift 'tan veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu Amazon Redshift Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

Amazon Redshift 'tan, desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Özellikle, bu Amazon Redshift Connector, sorgu veya yerleşik Redshift KALDıRMA desteği kullanılarak Redshift 'den veri almayı destekler.

> [!TIP]
> Redshift adresinden büyük miktarlarda veri kopyalarken en iyi performansı elde etmek için, Amazon S3 aracılığıyla yerleşik Redshift UNLOAD ' ı kullanmayı göz önünde bulundurun. Ayrıntılar için bkz. [Amazon Redshift 'tan verileri kopyalamak IÇIN kaldırma kullanma](#use-unload-to-copy-data-from-amazon-redshift) .

## <a name="prerequisites"></a>Önkoşullar

* Şirket içinde [barındırılan Integration Runtime](create-self-hosted-integration-runtime.md)kullanarak verileri şirket içi veri deposuna kopyalıyorsanız, Amazon Redshift kümesine erişim Integration Runtime izni verin (makinenin IP adresini kullanın). Yönergeler için bkz. [kümeye erişim yetkisi verme](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) .
* Verileri bir Azure veri deposuna kopyalıyorsunuz, Azure veri merkezleri tarafından kullanılan Işlem IP adresi ve SQL aralıkları için bkz. [Azure veri MERKEZI IP aralıkları](https://www.microsoft.com/download/details.aspx?id=41653) .

## <a name="getting-started"></a>Kullanmaya başlama

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, Amazon Redshift Connector 'a özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Amazon Redshift Linked Service için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği: **AmazonRedshift** olarak ayarlanmalıdır | Yes |
| sunucu |Amazon Redshift sunucusunun IP adresi veya ana bilgisayar adı. |Yes |
| port |Amazon Redshift sunucusunun istemci bağlantılarını dinlemek için kullandığı TCP bağlantı noktası sayısı. |Hayır, varsayılan değer 5439 ' dir |
| database |Amazon Redshift veritabanının adı. |Yes |
| username |Veritabanına erişimi olan kullanıcının adı. |Yes |
| password |Kullanıcı hesabı için parola. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. |Yes |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . Azure Integration Runtime veya şirket içinde barındırılan Integration Runtime (veri depolduğunuz özel ağda yer alıyorsa) kullanabilirsiniz. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. |No |

**Örnek:**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "<server name>",
            "database": "<database name>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm, Amazon Redshift DataSet tarafından desteklenen özelliklerin bir listesini sağlar.

Amazon Redshift 'tan veri kopyalamak için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | DataSet 'in Type özelliği: **AmazonRedshiftTable** olarak ayarlanmalıdır | Yes |
| schema | Şemanın adı. |Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse)  |
| tablo | Tablonun adı. |Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse)  |
| tableName | Şemanın bulunduğu tablonun adı. Bu özellik geriye dönük uyumluluk için desteklenir. `schema` `table` Yeni iş yükü için ve kullanın. | Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse) |

**Örnek**

```json
{
    "name": "AmazonRedshiftDataset",
    "properties":
    {
        "type": "AmazonRedshiftTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Amazon Redshift linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

`RelationalTable`Türü belirtilmiş veri kümesi kullanıyorsanız, hala olduğu gibi desteklenir, ancak yeni bir adım ileri kullanmanız önerilir.

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, Amazon Redshift source tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="amazon-redshift-as-source"></a>Kaynak olarak Amazon Redshift

Amazon Redshift 'tan veri kopyalamak için kopyalama etkinliğindeki kaynak türünü **AmazonRedshiftSource** olarak ayarlayın. Aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının Type özelliği: **AmazonRedshiftSource** olarak ayarlanmalıdır | Yes |
| sorgu |Verileri okumak için özel sorguyu kullanın. Örneğin: select * from MyTable. |Hayır (veri kümesinde "tableName" belirtilmişse) |
| Redkaydırıcı Tunloadsettings | Amazon Redshift UNLOAD kullanılırken özellik grubu. | No |
| s3LinkedServiceName | "AmazonS3" türünde bağlı bir hizmet adı belirtilerek geçici depo olarak kullanılacak bir Amazon S3 'e başvurur. | KALDıRMA kullanılıyorsa Evet |
| bucketName | Geçici verileri depolamak için S3 demetini belirtin. Sağlanmazsa, Data Factory hizmet otomatik olarak oluşturur.  | KALDıRMA kullanılıyorsa Evet |

**Örnek: yüklemeyi kaldır kullanarak kopyalama etkinliğinde Amazon Redshift Source**

```json
"source": {
    "type": "AmazonRedshiftSource",
    "query": "<SQL query>",
    "redshiftUnloadSettings": {
        "s3LinkedServiceName": {
            "referenceName": "<Amazon S3 linked service>",
            "type": "LinkedServiceReference"
        },
        "bucketName": "bucketForUnload"
    }
}
```

Bir sonraki bölümden, Amazon Redshift 'tan verileri etkin bir şekilde kopyalamak için KALDıRMA kullanma hakkında daha fazla bilgi edinin.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Amazon Redshift 'tan veri kopyalamak için KALDıRMA kullanma

[Kaldırma](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) , Amazon Redshift tarafından sağlanmış bir mekanizmadır. Bu, bir sorgunun sonuçlarını Amazon Simple Storage Service (Amazon S3) üzerindeki bir veya daha fazla dosyaya kaldırabilen bir mekanizmadır. Bu, Redshift ' den büyük veri kümesini kopyalamak için Amazon tarafından önerilme yöntemidir.

**Örnek: UNLOAD, hazırlanan Copy ve PolyBase kullanarak Amazon Redshift 'tan Azure SYNAPSE Analytics 'e veri kopyalama**

Bu örnek kullanım örneği için, kopyalama etkinliği, "Redkaydırıcı Tunloadsettings" içinde yapılandırıldığı şekilde Amazon Redshift 'tan Amazon S3 'e verileri kaldırır ve ardından Amazon S3 ' dan Azure blob ' a verileri "stagingSettings" bölümünde belirtildiği gibi kopyalar, son olarak Azure SYNAPSE Analytics 'e veri yüklemek için PolyBase kullanın. Tüm geçici biçim, kopyalama etkinliği tarafından düzgün şekilde işlenir.

![Redshift to Azure SYNAPSE Analytics Copy Workflow](media/copy-data-from-amazon-redshift/redshift-to-sql-dw-copy-workflow.png)

```json
"activities":[
    {
        "name": "CopyFromAmazonRedshiftToSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "AmazonRedshiftDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AmazonRedshiftSource",
                "query": "select * from MyTable",
                "redshiftUnloadSettings": {
                    "s3LinkedServiceName": {
                        "referenceName": "AmazonS3LinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "bucketName": "bucketForUnload"
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "AzureStorageLinkedService",
                "path": "adfstagingcopydata"
            },
            "dataIntegrationUnits": 32
        }
    }
]
```

## <a name="data-type-mapping-for-amazon-redshift"></a>Amazon Redshift için veri türü eşlemesi

Amazon Redshift 'tan veri kopyalarken, Amazon Redshift veri türlerinden aşağıdaki eşlemeler, geçici veri türleri Azure Data Factory için kullanılır. Kopyalama etkinliğinin kaynak şemayı ve veri türünü havuza nasıl eşlediğini öğrenmek için bkz. [şema ve veri türü eşlemeleri](copy-activity-schema-and-type-mapping.md) .

| Amazon Redshift veri türü | Veri Fabrikası geçici veri türü |
|:--- |:--- |
| BIGıNT |Int64 |
| BOOLEAN |Dize |
| CHAR |Dize |
| DATE |DateTime |
| KATEGORI |Ondalık |
| ÇIFT DUYARLıK |Çift |
| TAMSAYI |Int32 |
| GERÇEK SAYI |Tek |
| Small |Int16 |
| TEXT |Dize |
| TIMESTAMP |DateTime |
| VARCHAR |Dize |

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).
