---
title: Azure Service Fabric isteğe bağlı yedekleme
description: Uygulama verilerinizi bir gereksinim temelinde yedeklemek için Service Fabric yedekleme ve geri yükleme özelliğini kullanın.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: d7986c8cd8d0714215c7b4dc57170be346e627ed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98928041"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Azure Service Fabric isteğe bağlı yedekleme

Güvenilir durum bilgisi olan hizmetleri ve Reliable Actors olağanüstü durum veya veri kaybı senaryolarına yönelik verileri yedekleyebilirsiniz.

Azure Service Fabric, verilerin düzenli olarak [yedeklenmesi](service-fabric-backuprestoreservice-quickstart-azurecluster.md) ve verilerin yedeklenmesi için gerekli özelliklere sahiptir. İsteğe bağlı yedekleme,  / temel hizmette veya ortamındaki planlı değişiklikler nedeniyle veri kaybı _verisinin bozulmasına_ karşı koruma sağladığından yararlıdır.

İsteğe bağlı yedekleme özellikleri, bir hizmet veya hizmet ortamı işlemini el ile tetiklemeniz için hizmetlerin durumunu yakalamaya yardımcı olur. Örneğin, hizmeti yükseltirken veya eski sürüme düşürme sırasında hizmet ikili dosyalarında değişiklik yaparsanız. Böyle bir durumda, isteğe bağlı yedekleme, uygulama kodu hataları ile verileri bozulmaya karşı korumanıza yardımcı olabilir.
## <a name="prerequisites"></a>Önkoşullar

- Yapılandırma çağrıları yapmak için Microsoft. ServiceFabric. PowerShell. http modülünü (Önizleme) yükler.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

> [!NOTE]
> PowerShellGet sürümünüz 1.6.0 'den küçükse, *-allowbir ön* sürüm bayrağı için destek eklemek üzere ' yi güncelleştirmeniz gerekir:
>
> `Install-Module -Name PowerShellGet -Force`

- `Connect-SFCluster`Microsoft. ServiceFabric. PowerShell. http modülünü kullanarak herhangi bir yapılandırma isteği yapmadan önce, kümenin komutunu kullanarak bağlı olduğundan emin olun.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggering-on-demand-backup"></a>İsteğe bağlı yedekleme tetikleniyor

İsteğe bağlı yedekleme, yedekleme dosyalarını karşıya yüklemeye yönelik depolama ayrıntılarını gerektirir. İsteğe bağlı yedekleme konumunu, düzenli yedekleme ilkesinde veya isteğe bağlı yedekleme isteğinde belirtirsiniz.

### <a name="on-demand-backup-to-storage-specified-by-a-periodic-backup-policy"></a>Düzenli yedekleme ilkesiyle belirtilen depolamaya isteğe bağlı yedekleme

Düzenli olarak yedekleme ilkesini, güvenilir bir durum bilgisi olan hizmetin bir bölümünü veya depolamaya yönelik ek isteğe bağlı yedekleme için güvenilir Aktöri kullanacak şekilde yapılandırabilirsiniz.

Aşağıdaki durum, [güvenilir durum bilgisi olan hizmet ve Reliable Actors için düzenli yedeklemeyi etkinleştirmeye yönelik](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors)senaryonun devamlıdır. Bu durumda, bir yedekleme ilkesinin bölüm kullanmasını ve bir yedeklemenin Azure Storage 'da ayarlanmış bir sıklıkta gerçekleşmesini sağlayabilirsiniz.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Microsoft. ServiceFabric. PowerShell. http modülünü kullanan PowerShell

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' 

```

#### <a name="rest-call-using-powershell"></a>PowerShell kullanarak Rest çağrısı

Bölüm KIMLIĞI için isteğe bağlı yedekleme için [tetiklemeyi ayarlamak üzere Backuppartition](/rest/api/servicefabric/sfclient-api-backuppartition) API 'sini kullanın `974bd92a-b395-4631-8a7f-53bd4ae9cf22` .

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

[İsteğe bağlı yedekleme ilerlemesi](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress)için izlemeyi etkinleştirmek üzere [getbackupprogress](/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API 'sini kullanın.

### <a name="on-demand-backup-to-specified-storage"></a>Belirtilen depolamaya isteğe bağlı yedekleme

Güvenilir bir durum bilgisi olan hizmetin veya güvenilir aktörün bir bölümü için isteğe bağlı yedekleme isteğinde bulunabilir. Depolama bilgilerini isteğe bağlı yedekleme isteğinin bir parçası olarak sağlayın.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Microsoft. ServiceFabric. PowerShell. http modülünü kullanan PowerShell

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -AzureBlobStore -ConnectionString  'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>PowerShell kullanarak Rest çağrısı

Bölüm KIMLIĞI için isteğe bağlı yedekleme için [tetiklemeyi ayarlamak üzere Backuppartition](/rest/api/servicefabric/sfclient-api-backuppartition) API 'sini kullanın `974bd92a-b395-4631-8a7f-53bd4ae9cf22` . Aşağıdaki Azure depolama bilgilerini ekleyin:

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$OnDemandBackupRequest = @{
    BackupStorage = $StorageInfo
}

$body = (ConvertTo-Json $OnDemandBackupRequest)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

[İstek üzerine yedekleme işleminin](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress)izlenmesini ayarlamak Için [getbackupprogress](/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API 'sini kullanabilirsiniz.

### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer kullanma
Service Fabric Explorer ayarlarında gelişmiş modun etkinleştirildiğinden emin olun.
1. İstediğiniz bölümleri seçin ve Eylemler ' e tıklayın. 
2. Bölüm yedeklemesini Tetikle ' i seçin ve Azure için bilgi doldur:

    ![Bölüm yedeklemesini Tetikle][0]

    veya FileShare:

    ![Bölüm yedeklemesi dosya yedeklemesini tetikleme][1]

## <a name="tracking-on-demand-backup-progress"></a>İsteğe bağlı yedekleme ilerlemesini izleme

Güvenilir bir durum bilgisi olan hizmetin veya güvenilir aktörün bir bölümü aynı anda yalnızca bir isteğe bağlı yedekleme isteği kabul eder. Başka bir istek yalnızca geçerli isteğe bağlı yedekleme isteği tamamlandıktan sonra kabul edilebilir.

Farklı bölümler, isteğe bağlı yedekleme isteklerini aynı anda tetikleyebilir.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Microsoft. ServiceFabric. PowerShell. http modülünü kullanan PowerShell

```powershell

Get-SFPartitionBackupProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'

```
#### <a name="rest-call-using-powershell"></a>PowerShell kullanarak Rest çağrısı

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

İsteğe bağlı yedekleme istekleri aşağıdaki durumlarda olabilir:

- **Kabul edildi**: yedekleme bölümde başlatıldı ve devam ediyor.
  ```
  BackupState             : Accepted
  TimeStampUtc            : 0001-01-01T00:00:00Z
  BackupId                : 00000000-0000-0000-0000-000000000000
  BackupLocation          :
  EpochOfLastBackupRecord :
  LsnOfLastBackupRecord   : 0
  FailureError            :
  ```
- **Başarı**, **hata** veya **zaman aşımı**: istenen isteğe bağlı yedekleme, aşağıdaki durumların hiçbirinde tamamlanabilir:
  - **Başarı**: _başarılı_ yedekleme durumu, Bölüm durumunun başarıyla yedeklendiğini gösterir. Yanıt, bölüm için _backupdönem_ ve _BACKUPLSN_ sağlar ve UTC 'deki zaman ile birlikte.
    ```
    BackupState             : Success
    TimeStampUtc            : 2018-11-21T20:00:01Z
    BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
    BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
    EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
    LsnOfLastBackupRecord   : 36
    FailureError            :
    ```
  - **Hata**: bir yedekleme durumu, Bölüm durumunun yedeklenmesi sırasında _bir hata oluştuğunu_ gösterir. Hatanın nedeni yanıt olarak belirtilir.
    ```
    BackupState             : Failure
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
    ```
  - **Zaman aşımı**: bir _zaman aşımı_ yedekleme durumu, bölüm durumu yedeklemesinin belirli bir süre içinde oluşturulamadığından emin olduğunu gösterir. Varsayılan zaman aşımı değeri 10 dakikadır. Bu senaryoda daha büyük [Backuptimeout](/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) ile yeni bir isteğe bağlı yedekleme isteği başlatın.
    ```
    BackupState             : Timeout
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
    ```

## <a name="next-steps"></a>Sonraki adımlar

- [Düzenli yedekleme yapılandırmasını anlama](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [BackupRestore REST API başvurusu](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/trigger-partition-backup.png
[1]: ./media/service-fabric-backuprestoreservice/trigger-backup-fileshare.png
