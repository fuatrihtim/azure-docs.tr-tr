---
title: Azure Izleyici günlük sorgularının işlevleri | Microsoft Docs
description: Bu makalede, Azure Izleyici 'de başka bir günlük sorgusundan bir sorgu çağırmak için işlevlerinin nasıl kullanılacağı açıklanır.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/31/2020
ms.openlocfilehash: 07a959d4e8ba41652ba4e31ad59cf852659a5926
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199759"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Azure Izleyici günlük sorgularındaki işlevleri kullanma

Başka bir sorguyla bir günlük sorgusu kullanmak için, bunu bir işlev olarak kaydedebilirsiniz. Bu, karmaşık sorguları bölümlere ayırarak basitleştirmeye ve birden çok sorguyla ortak kod kullanmanıza olanak sağlar.

## <a name="create-a-function"></a>İşlev oluşturma

**Kaydet** ' e tıklayarak ve ardından aşağıdaki tablodaki bilgileri sağlayarak Azure Portal Log Analytics bir işlev oluşturun.

| Ayar | Açıklama |
|:---|:---|
| Ad           | Sorgu **Gezgini**'nde sorgu için görünen ad. |
| Farklı kaydet        | İşlev |
| İşlev diğer adı | Diğer sorgularda işlevi kullanmak için kısa ad. Boşluk içeremez ve benzersiz olmalıdır. |
| Kategori       | **Sorgu Gezgini**'nde Kaydedilmiş sorguları ve işlevleri düzenlemek için bir kategori. |

Ayrıca, [REST API](/rest/api/loganalytics/savedsearches/createorupdate) veya [PowerShell](/powershell/module/az.operationalinsights/new-azoperationalinsightssavedsearch)kullanarak işlevler de oluşturabilirsiniz.


## <a name="use-a-function"></a>Bir işlev kullanın
Diğer bir sorguya diğer adını ekleyerek bir işlev kullanın. Diğer tablolar gibi kullanılabilir.

## <a name="function-parameters"></a>İşlev parametreleri 
Bir işleve parametreler ekleyerek belirli değişkenler için değer sağlayabilmenizi sağlayabilirsiniz. Şu anda parametrelere sahip bir işlev oluşturmanın tek yolu Kaynak Yöneticisi şablonunu kullanmaktır. Bir örnek için bkz. [Azure izleyici 'de günlük sorguları için Kaynak Yöneticisi şablonu örnekleri](./resource-manager-log-queries.md#parameterized-function) .

## <a name="example"></a>Örnek
Aşağıdaki örnek sorgu, son gün içinde bildirilen tüm eksik güvenlik güncelleştirmelerini döndürür. Bu sorguyu, _security_updates_last_day_ diğer adıyla bir işlev olarak kaydedin. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Başka bir sorgu oluşturun ve SQL ile ilgili gerekli güvenlik güncelleştirmelerini aramak için _security_updates_last_day_ işlevine başvurun.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>Sonraki adımlar
Bkz. Azure Izleyici günlük sorgularını yazmak için diğer dersler:

- [Dize işlemleri](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations)
- [Tarih ve saat işlemleri](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#date-and-time-operations)
- [Toplama işlevleri](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#aggregations)
- [Gelişmiş toplamalar](/azure/data-explorer/write-queries#advanced-aggregations)
- [JSON ve veri yapıları](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#json-and-data-structures)
- [Birleştirmeler](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins)
- [Grafikler](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#charts)
