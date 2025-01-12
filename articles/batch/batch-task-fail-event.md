---
title: Azure Batch görev başarısız olayı
description: Batch görevi hata olayı için başvuru. Bu olay, görev tamamlanma olayına ek olarak yayınlanacaktır ve bir görevin başarısız olduğunu algılamak için kullanılabilir.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: e13692b45ff5a049d0b724525ad6565d2b894a3d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91850821"
---
# <a name="task-fail-event"></a>Görev başarısızlık olayı

 Bu olay, bir görev hata ile tamamlandığında yayınlanır. Şu anda tüm sıfır olmayan çıkış kodları hatalara göre değerlendirilir. Bu olay, görev tamamlanma olayına *ek* olarak yayınlanacaktır ve bir görevin başarısız olduğunu algılamak için kullanılabilir.


 Aşağıdaki örnekte, bir görev başarısız olayının gövdesi gösterilmektedir.

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 0,
    "requiredSlots": 1,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "startTime": "2016-09-08T16:32:23.799Z",
        "endTime": "2016-09-08T16:34:00.666Z",
        "exitCode": 1,
        "retryCount": 2,
        "requeueCount": 0
    }
}
```

|Öğe adı|Tür|Notlar|
|------------------|----------|-----------|
|`jobId`|Dize|Görevi içeren işin KIMLIĞI.|
|`id`|Dize|Görevin KIMLIĞI.|
|`taskType`|Dize|Görevin türü. Bu, bir iş Yöneticisi görevi olduğunu ya da bir iş Yöneticisi görevi olmadığını belirten ' user ' olduğunu belirten ' JobManager ' olabilir. Bu olay iş hazırlama görevleri, iş bırakma görevleri veya başlangıç görevleri için yayınlanmaz.|
|`systemTaskVersion`|Int32|Bu, bir görevde iç yeniden deneme sayacıdır. Toplu olarak Batch hizmeti, geçici sorunlar için bir görevi hesaba yeniden deneyebilir. Bu sorunlar, iç zamanlama hataları içerebilir veya işlem düğümlerinden hatalı bir durumda kurtarmaya çalışır.|
|`requiredSlots`|Int32|Görevi çalıştırmak için gereken yuvalar.|
|[`nodeInfo`](#nodeInfo)|Karmaşık Tür|Görevin çalıştırıldığı işlem düğümüyle ilgili bilgiler içerir.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Karmaşık Tür|Görevin birden çok işlem düğümü gerektiren çok örnekli bir görev olduğunu belirtir.  [`multiInstanceSettings`](/rest/api/batchservice/get-information-about-a-task)Ayrıntılar için bkz..|
|[`constraints`](#constraints)|Karmaşık Tür|Bu görev için uygulanan yürütme kısıtlamaları.|
|[`executionInfo`](#executionInfo)|Karmaşık Tür|Görevin yürütülmesi hakkındaki bilgileri içerir.|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a> NodeInfo

|Öğe adı|Tür|Notlar|
|------------------|----------|-----------|
|`poolId`|Dize|Görevin çalıştırıldığı havuzun KIMLIĞI.|
|`nodeId`|Dize|Görevin çalıştırıldığı düğümün KIMLIĞI.|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a> Multiınstancesettings

|Öğe adı|Tür|Notlar|
|------------------|----------|-----------|
|`numberOfInstances`|Int32|Görevin gerektirdiği işlem düğümü sayısı.|

###  <a name="constraints"></a><a name="constraints"></a> kısıtlamaları

|Öğe adı|Tür|Notlar|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|Görevin en fazla yeniden denenme sayısı. Batch hizmeti, çıkış kodu sıfır değilse bir görevi yeniden dener.<br /><br /> Bu değerin özellikle yeniden deneme sayısını denetlediğine unutmayın. Batch hizmeti görevi bir kez dener ve daha sonra bu sınıra kadar yeniden deneyebilir. Örneğin, en fazla yeniden deneme sayısı 3 ise Batch, 4 kata kadar bir görevi dener (bir başlangıç denemesi ve 3 yeniden deneme).<br /><br /> En fazla yeniden deneme sayısı 0 ise, Batch hizmeti görevleri yeniden denemeyecek.<br /><br /> En fazla yeniden deneme sayısı-1 ise, Batch hizmeti görevleri sınırlı olmadan yeniden dener.<br /><br /> Varsayılan değer 0 ' dır (yeniden deneme yok).|


###  <a name="executioninfo"></a><a name="executionInfo"></a> ExecutionInfo

|Öğe adı|Tür|Notlar|
|------------------|----------|-----------|
|`startTime`|DateTime|Görevin çalışmaya başladığı zaman. ' Running ' **çalışma** durumuna karşılık gelir, bu nedenle görev kaynak dosyalarını veya uygulama paketlerini belirtiyorsa, başlangıç zamanı, görevin bu dosyaları indirmeye veya dağıtmaya başladığı saati yansıtır.  Görev yeniden başlatılmış veya yeniden denenip, görevin çalışmaya başladığı en son zaman budur.|
|`endTime`|DateTime|Görevin tamamlandığı zaman.|
|`exitCode`|Int32|Görevin çıkış kodu.|
|`retryCount`|Int32|Görevin Batch hizmeti tarafından yeniden denenme sayısı. Bu görev, belirtilen MaxTaskRetryCount 'a kadar sıfır olmayan çıkış kodu ile çıktıktan sonra yeniden denenir.|
|`requeueCount`|Int32|Bir Kullanıcı isteğinin sonucu olarak Batch hizmeti tarafından görevin yeniden kuyruğa kaç kez olduğu.<br /><br /> Kullanıcı bir havuzdan düğümleri kaldırdığında (havuzu yeniden boyutlandırarak veya küçülterek) veya iş devre dışı bırakıldığında, Kullanıcı, çalışan görevlerin yeniden kuyruğa olması için, düğümlerde çalıştığını belirtebilir. Bu sayı, bu nedenlerden dolayı görevin kaç kez yeniden kuyruğa olduğunu izler.|
