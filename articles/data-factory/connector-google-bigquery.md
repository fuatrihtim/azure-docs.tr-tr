---
title: Azure Data Factory kullanarak Google BigQuery 'den veri kopyalama
description: Data Factory işlem hattındaki kopyalama etkinliğini kullanarak Google BigQuery 'den desteklenen havuz veri depolarına veri kopyalamayı öğrenin.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: e3fcaa6c1542578d983461623da743724a3114d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100389697"
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Azure Data Factory kullanarak Google BigQuery 'den veri kopyalama
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, Google BigQuery 'den veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu Google BigQuery Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

Google BigQuery 'den desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak veya havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Data Factory, bağlantıyı etkinleştirmek için yerleşik bir sürücü sağlar. Bu nedenle, bu bağlayıcıyı kullanmak için el ile bir sürücü yüklemeniz gerekmez.

>[!NOTE]
>Bu Google BigQuery Bağlayıcısı, BigQuery API 'lerinin üzerine kurulmuştur. BigQuery 'nin gelen isteklerin en yüksek oranını sınırlayan ve proje temelinde uygun kotaları uyguladığı farkında olun, [kotalar & limitleri-API istekleri](https://cloud.google.com/bigquery/quotas#api_requests)' ne bakın. Hesapta çok fazla eşzamanlı istek tetiklediğinizden emin olun.

## <a name="get-started"></a>başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümler, Google BigQuery bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Aşağıdaki özellikler Google BigQuery bağlı hizmeti için desteklenir.

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği **GoogleBigQuery** olarak ayarlanmalıdır. | Yes |
| proje | Sorgu yapılacak varsayılan BigQuery projesinin proje KIMLIĞI.  | Yes |
| additionalProjects | Erişmek için ortak BigQuery projelerinin proje kimliklerinin virgülle ayrılmış listesi.  | No |
| requestGoogleDriveScope | Google Drive 'a erişim istenip istenmediğini belirtir. Google Drive erişimine izin verilmesi, BigQuery verilerini Google Drive verileriyle birleştiren Federasyon tabloları için destek sağlar. Varsayılan değer **false** şeklindedir.  | No |
| authenticationType | Kimlik doğrulaması için kullanılan OAuth 2,0 kimlik doğrulama mekanizması. ServiceAuthentication yalnızca şirket içinde barındırılan Integration Runtime kullanılabilir. <br/>İzin verilen değerler **userauthentication** ve **serviceauthentication**' dir. Daha fazla özellik ve bu kimlik doğrulama türleri için JSON örnekleri üzerinde bu tablonun altındaki bölümlere bakın. | Yes |

### <a name="using-user-authentication"></a>Kullanıcı kimlik doğrulamasını kullanma

"AuthenticationType" özelliğini **Userauthentication** olarak ayarlayın ve önceki bölümde açıklanan genel özelliklerle birlikte aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| clientId | Yenileme belirtecini oluşturmak için kullanılan uygulamanın KIMLIĞI. | No |
| clientSecret | Yenileme belirtecini oluşturmak için kullanılan uygulamanın gizli dizisi. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | No |
| refreshToken | Google 'dan edinilen ve BigQuery 'ye erişim yetkisi vermek için kullanılan yenileme belirteci. [OAuth 2,0 erişim belirteçlerini](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens) ve [Bu topluluk blogunu](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59)edinmeyle bir tane almayı öğrenin. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | No |

**Örnek:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "clientId": "<id of the application used to generate the refresh token>",
            "clientSecret": {
                "type": "SecureString",
                "value":"<secret of the application used to generate the refresh token>"
            },
            "refreshToken": {
                "type": "SecureString",
                "value": "<refresh token>"
            }
        }
    }
}
```

### <a name="using-service-authentication"></a>Hizmet kimlik doğrulamasını kullanma

"AuthenticationType" özelliğini **Serviceauthentication** olarak ayarlayın ve önceki bölümde açıklanan genel özelliklerle birlikte aşağıdaki özellikleri belirtin. Bu kimlik doğrulama türü yalnızca kendi kendine barındırılan Integration Runtime kullanılabilir.

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| e-posta | ServiceAuthentication için kullanılan hizmet hesabı e-posta KIMLIĞI. Bu, yalnızca şirket içinde barındırılan Integration Runtime kullanılabilir.  | No |
| Keyfilepath null | Hizmet hesabı e-posta adresinin kimliğini doğrulamak için kullanılan. p12 anahtar dosyasının tam yolu. | No |
| trustedCertPath | TLS üzerinden bağlandığınızda sunucuyu doğrulamak için kullanılan Güvenilen CA sertifikalarını içeren. ped dosyasının tam yolu. Bu özellik yalnızca, şirket içinde barındırılan Integration Runtime TLS kullandığınızda ayarlanabilir. Varsayılan değer, Integration Runtime ile yüklenen CAcert. Pez dosyasıdır.  | No |
| useSystemTrustStore | Sistem güven deposundan veya belirtilen. ped dosyasından bir CA sertifikası kullanılıp kullanılmayacağını belirtir. Varsayılan değer **false** şeklindedir.  | No |

**Örnek:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project id>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "ServiceAuthentication",
            "email": "<email>",
            "keyFilePath": "<.p12 key path on the IR machine>"
        },
        "connectVia": {
            "referenceName": "<name of Self-hosted Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm, Google BigQuery veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

Google BigQuery 'den veri kopyalamak için, veri kümesinin Type özelliğini **GoogleBigQueryObject** olarak ayarlayın. Aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | DataSet 'in Type özelliği: **GoogleBigQueryObject** olarak ayarlanmalıdır | Yes |
| veri kümesi | Google BigQuery veri kümesinin adı. |Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse)  |
| tablo | Tablonun adı. |Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse)  |
| tableName | Tablonun adı. Bu özellik geriye dönük uyumluluk için desteklenir. Yeni iş yükü için `dataset` ve kullanın `table` . | Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse) |

**Örnek**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, Google BigQuery kaynak türü tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="googlebigquerysource-as-a-source-type"></a>Kaynak türü olarak GoogleBigQuerySource

Google BigQuery 'den veri kopyalamak için kopyalama etkinliğindeki kaynak türünü **GoogleBigQuerySource** olarak ayarlayın. Aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir.

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının Type özelliği **GoogleBigQuerySource** olarak ayarlanmalıdır. | Yes |
| sorgu | Verileri okumak için özel SQL sorgusunu kullanın. `"SELECT * FROM MyTable"` bunun bir örneğidir. | Hayır (veri kümesinde "tableName" belirtilmişse) |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "GoogleBigQuerySource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="next-steps"></a>Sonraki adımlar
Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).
