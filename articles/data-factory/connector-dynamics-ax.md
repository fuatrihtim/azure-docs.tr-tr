---
title: Dynamics AX 'ten veri kopyalama
description: Azure Data Factory bir işlem hattındaki kopyalama etkinliğini kullanarak Dynamics AX 'ten desteklenen havuz veri depolarına veri kopyalamayı öğrenin.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/12/2020
ms.openlocfilehash: 38ff77ad56f16fbd33b77021b18be77f6a153b3f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100380993"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory"></a>Azure Data Factory kullanarak Dynamics AX 'ten veri kopyalama

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, Dynamics AX kaynağından veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Makale, kopyalama etkinliğine genel bir bakış sunan [Azure Data Factory kopyalama etkinliği](copy-activity-overview.md)üzerinde oluşturulur.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu Dynamics AX Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

Dynamics AX 'ten desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Kopyalama etkinliğinin kaynak ve havuz olarak desteklediği veri depolarının bir listesi için bkz. [desteklenen veri depoları ve biçimleri](copy-activity-overview.md#supported-data-stores-and-formats).

Özellikle, bu Dynamics AX Connector, **hizmet sorumlusu kimlik doğrulamasıyla** **OData protokolünü** kullanarak Dynamics AX 'ten veri kopyalamayı destekler.

>[!TIP]
>Ayrıca, bu bağlayıcıyı **Dynamics 365 finans ve işlemlerinden** veri kopyalamak için de kullanabilirsiniz. Dynamics 365 ' in [OData desteği](/dynamics365/unified-operations/dev-itpro/data-entities/odata) ve [kimlik doğrulama yöntemi](/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication)' ne bakın.

## <a name="get-started"></a>başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, Dynamics AX bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanabileceğiniz özellikler hakkında ayrıntılı bilgi sağlanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

Hizmet sorumlusu kimlik doğrulamasını kullanmak için şu adımları izleyin:

1. Uygulamanızı [bir Azure AD kiracısıyla](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)kaydederek Azure Active Directory (Azure AD) uygulamasına bir uygulama varlığı kaydedin. Bağlı hizmeti tanımlamak için kullandığınız aşağıdaki değerleri unutmayın:

    - Uygulama Kimliği
    - Uygulama anahtarı
    - Kiracı Kimliği

2. Dynamics AX 'e gidin ve Dynamics AX 'e erişmek için bu hizmete asıl uygun izni verin.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Dynamics AX bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | **Type** özelliği **DynamicsAx** olarak ayarlanmalıdır. |Yes |
| url | Dynamics AX (veya Dynamics 365 finans ve Operations) örneği OData uç noktası. |Yes |
| Serviceprincipalıd | Uygulamanın istemci KIMLIĞINI belirtin. | Yes |
| Servicesprincipalkey | Uygulamanın anahtarını belirtin. Data Factory güvenli bir şekilde depolamak için bu alanı **SecureString** olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | Yes |
| Kiracı | Uygulamanızın altında bulunduğu kiracı bilgilerini (etki alanı adı veya kiracı KIMLIĞI) belirtin. Fareyi, Azure portal sağ üst köşesine getirerek alın. | Yes |
| Aadresourceıd | Yetkilendirme için istediğiniz AAD kaynağını belirtin. Örneğin, Dynamics URL 'niz ise `https://sampledynamics.sandbox.operations.dynamics.com/data/` , karşılık gelen AAD kaynağı genellikle olur `https://sampledynamics.sandbox.operations.dynamics.com` . | Yes |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . Azure Integration Runtime veya şirket içinde barındırılan Integration Runtime (veri depolubir özel ağda bulunuyorsa) seçeneğini belirleyebilirsiniz. Belirtilmemişse, varsayılan Azure Integration Runtime kullanılır. |No |

**Örnek**

```json
{
    "name": "DynamicsAXLinkedService",
    "properties": {
        "type": "DynamicsAX",
        "typeProperties": {
            "url": "<Dynamics AX instance OData endpoint>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource, e.g. https://sampledynamics.sandbox.operations.dynamics.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}

```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Bu bölüm, Dynamics AX veri kümesinin desteklediği özelliklerin bir listesini sağlar.

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri ve bağlı hizmetler](concepts-datasets-linked-services.md). 

Dynamics AX 'ten veri kopyalamak için veri kümesinin **Type** özelliğini **Dynamicsaxresource** olarak ayarlayın. Aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Veri kümesinin **Type** özelliği **Dynamicsaxresource** olarak ayarlanmalıdır. | Yes |
| path | Dynamics AX OData varlığının yolu. | Yes |

**Örnek**

```json
{
    "name": "DynamicsAXResourceDataset",
    "properties": {
        "type": "DynamicsAXResource",
        "typeProperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Dynamics AX linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliği özellikleri

Bu bölüm, Dynamics AX kaynağının desteklediği özelliklerin bir listesini sağlar.

Etkinlikleri tanımlamak için kullanılabilen bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md). 

### <a name="dynamics-ax-as-source"></a>Kaynak olarak Dynamics AX

Dynamics AX 'ten veri kopyalamak için kopyalama etkinliğindeki **kaynak** türünü **Dynamicsaxsource** olarak ayarlayın. Aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının **Type** özelliği **Dynamicsaxsource** olarak ayarlanmalıdır. | Yes |
| sorgu | Verileri filtrelemek için OData sorgu seçenekleri. Örnek: `"?$select=Name,Description&$top=5"`.<br/><br/>**Note**: bağlayıcı, VERILERI birleştirilmiş URL 'den kopyalar: `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]` . Daha fazla bilgi için bkz. [OData URL bileşenleri](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | No |
| httpRequestTimeout | HTTP isteğinin yanıt almak için zaman aşımı ( **TimeSpan** değeri). Bu değer, yanıt verilerinin okunması için zaman aşımı değil, yanıt almaya yönelik zaman aşımı değeridir. Belirtilmemişse, varsayılan değer **00:30:00** ' dir (30 dakika). | No |

**Örnek**

```json
"activities":[
    {
        "name": "CopyFromDynamicsAX",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics AX input dataset name>",
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
                "type": "DynamicsAXSource",
                "query": "$top=10"
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

Kopyalama etkinliğinin Azure Data Factory kaynak ve havuz olarak desteklediği veri depolarının listesi için bkz. [desteklenen veri depoları ve biçimleri](copy-activity-overview.md#supported-data-stores-and-formats).