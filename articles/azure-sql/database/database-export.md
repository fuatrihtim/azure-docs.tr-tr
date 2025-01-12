---
title: Bir Azure SQL veritabanını BACPAC dosyasına dışarı aktarma (Azure portal)
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure portal kullanarak bir veritabanını BACPAC dosyasına dışarı aktarın.
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
author: stevestein
ms.custom: sqldbrb=2
ms.author: sstein
ms.reviewer: ''
ms.date: 01/11/2021
ms.topic: how-to
ms.openlocfilehash: 14854f839d6dfe3c8a08a4a1453fd78e389fe8d3
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568741"
---
# <a name="export-to-a-bacpac-file---azure-sql-database-and-azure-sql-managed-instance"></a>BACPAC dosyasına aktarma-Azure SQL veritabanı ve Azure SQL yönetilen örneği

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Arşivleme için bir veritabanını dışarı veya başka bir platforma taşımak istediğinizde, veritabanı şemasını ve verileri [bacpac](/sql/relational-databases/data-tier-applications/data-tier-applications#Anchor_4) dosyasına dışarı aktarabilirsiniz. BACPAC dosyası, bir BACPAC uzantısı olan ve veritabanından meta verileri ve verileri içeren bir ZIP dosyasıdır. BACPAC dosyası, Azure Blob depolama alanında veya yerel depolama alanında Şirket içi bir konumda depolanabilir ve daha sonra Azure SQL veritabanı, Azure SQL yönetilen örneği veya bir SQL Server örneğine geri alınabilir.

## <a name="considerations"></a>Dikkat edilmesi gerekenler

- Bir dışa aktarmanın işlem açısından tutarlı olması için, dışarı aktarma sırasında herhangi bir yazma etkinliğinin gerçekleşmemesini veya veritabanınızın [işlemsel olarak tutarlı bir kopyasından](database-copy.md) dışarı aktardığınız emin olmanız gerekir.
- Blob depolamaya veriyorsanız, BACPAC dosyasının en büyük boyutu 200 GB 'tır. Daha büyük bir BACPAC dosyasını arşivlemek için yerel depolama alanına dışarı aktarın.
- Bu makalede ele alınan yöntemleri kullanarak BACPAC dosyasını Azure Premium Depolama 'ya aktarmak desteklenmez.
- Bir güvenlik duvarının arkasındaki depolama Şu anda desteklenmiyor.
- StorageURI için depolama dosya adı veya giriş değeri 128 karakterden az olmalıdır ve '. ' ile bitemez ve boşluk karakteri veya ' <, >, *,%, &,:, \, /,? ' gibi özel karakterler içeremez. 
- Dışarı aktarma işlemi 20 saati aşarsa, iptal edilebilir. Dışarı aktarma sırasında performansı artırmak için şunları yapabilirsiniz:

  - İşlem boyutunuzu geçici olarak artırın.
  - Dışarı aktarma sırasında tüm okuma ve yazma etkinliklerini durdurun.
  - Tüm büyük tablolardaki null olmayan değerler içeren bir [kümelenmiş dizin](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described) kullanın. Kümelenmiş dizinler olmadan, 6-12 saatten uzun sürerse dışarı aktarma başarısız olabilir. Bunun nedeni, dışa aktarma hizmetinin tüm tabloyu dışarı aktarmaya çalışmak için bir tablo taramasını tamamlaması gerekir. Tablolarınızın dışarı aktarma için iyileştirildiğini belirlemenin iyi bir yolu, **DBCC SHOW_STATISTICS** çalıştırmak ve *RANGE_HI_KEY* null olmadığından ve değeri iyi bir dağıtıma sahip olduğundan emin olmanızı sağlar. Ayrıntılar için bkz. [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql).

> [!NOTE]
> BACPACs yedekleme ve geri yükleme işlemleri için kullanılmak üzere tasarlanmamıştır. Azure, her kullanıcı veritabanı için otomatik olarak yedeklemeler oluşturur. Ayrıntılar için bkz. [iş sürekliliği genel bakış](business-continuity-high-availability-disaster-recover-hadr-overview.md) ve [SQL veritabanı yedeklemeleri](automated-backups-overview.md).

## <a name="the-azure-portal"></a>Azure portal

[Azure SQL yönetilen örneğinden](../managed-instance/sql-managed-instance-paas-overview.md) bir veritabanının bacpac 'sini Azure Portal kullanarak dışarı aktarma işlemi şu anda desteklenmiyor. Bunun yerine SQL Server Management Studio veya SQLPackage kullanın.

> [!NOTE]
> Azure portal veya PowerShell aracılığıyla gönderilen içeri/dışarı aktarma isteklerini işleyen makineler, BACPAC dosyasını ve Data-Tier uygulama çerçevesi (DacFX) tarafından oluşturulan geçici dosyaları da depolaması gerekir. Gerekli disk alanı, aynı boyuta sahip veritabanları arasında önemli ölçüde farklılık gösterir ve veritabanı boyutunun 3 katına kadar disk alanı gerektirebilir. İçeri/dışarı aktarma isteği çalıştıran makinelerde yalnızca 450GB yerel disk alanı vardır. Sonuç olarak, bazı istekler hata vererek başarısız olabilir `There is not enough space on the disk` . Bu durumda, geçici çözüm yeterli yerel disk alanına sahip bir makinede sqlpackage.exe çalıştırmak olur. Bu sorundan kaçınmak için, [SQL paketini](#sqlpackage-utility) 150gb 'den büyük veritabanlarını içeri/dışarı aktarmak için kullanmayı öneririz.

1. [Azure Portal](https://portal.azure.com)kullanarak bir veritabanını dışarı aktarmak için veritabanınızın sayfasını açın ve araç çubuğunda **dışarı aktar** ' a tıklayın.

   ![Dışarı Aktar düğmesini vurgulayan ekran görüntüsü.](./media/database-export/database-export1.png)

2. BACPAC dosya adını belirtin, var olan bir Azure Depolama hesabını ve dışarı aktarma kapsayıcısını seçin ve ardından kaynak veritabanına erişim için uygun kimlik bilgilerini sağlayın. Azure Yöneticisi, Azure SQL veritabanı veya Azure SQL yönetilen örneği üzerinde yönetici izinlerine sahip olmadığı için Azure yöneticisi olsanız bile bir SQL **Server yönetici oturumu** açmanız gerekir.

    ![Veritabanı dışarı aktarma](./media/database-export/database-export2.png)

3. **Tamam**'a tıklayın.

4. Dışarı aktarma işleminin ilerlemesini izlemek için, dışarı aktarılmakta olan veritabanını içeren sunucu sayfasını açın. **Ayarlar** ' ın altında, **Içeri/dışarı aktarma geçmişi**' ne tıklayın.

   ![dışarı aktarma geçmişi](./media/database-export/export-history.png)

## <a name="sqlpackage-utility"></a>SQLPackage yardımcı programı

[SqlPackage](/sql/tools/sqlpackage) komut satırı yardımcı programını kullanarak SQL veritabanında bir veritabanını dışarı aktarmak için bkz. [dışarı aktarma parametreleri ve özellikleri](/sql/tools/sqlpackage#export-parameters-and-properties). SQLPackage yardımcı programı, [Visual Studio için](/sql/ssdt/download-sql-server-data-tools-ssdt) [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ve SQL Server veri araçları en son sürümleriyle birlikte gelir veya [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) 'ın en son sürümünü doğrudan Microsoft Yükleme Merkezi ' nden indirebilirsiniz.

Çoğu üretim ortamında, ölçek ve performans için SQLPackage yardımcı programının kullanılmasını öneririz. BACPAC dosyalarını kullanarak geçiş hakkında bir SQL Server Müşteri Danışmanlık Ekibi blogu için bkz. [BACPAC Dosyalarını kullanarak SQL Server’dan Azure SQL Veritabanına Geçiş](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files).

Bu örnek, Active Directory evrensel kimlik doğrulamasıyla SqlPackage.exe kullanarak bir veritabanını dışarı aktarmayı gösterir:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

SQL Server Management Studio en yeni sürümleri, Azure SQL veritabanındaki bir veritabanını veya bir SQL yönetilen örnek veritabanını BACPAC dosyasına aktarmak için bir sihirbaz sağlar. [Veri katmanı uygulamasını dışarı aktarma](/sql/relational-databases/data-tier-applications/export-a-data-tier-application)bölümüne bakın.

## <a name="powershell"></a>PowerShell

> [!NOTE]
> [Azure SQL yönetilen örneği](../managed-instance/sql-managed-instance-paas-overview.md) şu anda Azure PowerShell kullanarak bir veritabanının bacpac dosyasına verilmesini desteklemez. Yönetilen bir örneği BACPAC dosyasına aktarmak için SQL Server Management Studio veya SQLPackage kullanın.

Azure SQL veritabanı hizmetine bir veritabanı dışarı aktarma isteği göndermek için [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) cmdlet 'ini kullanın. Veritabanınızın boyutuna bağlı olarak, dışarı aktarma işleminin tamamlanması biraz zaman alabilir.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Dışarı aktarma isteğinin durumunu denetlemek için [Get-Azsqldatabaseımportexportstatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) cmdlet 'ini kullanın. İstek genellikle **durum: InProgress** döndürür. **Durumu gördüğünüzde: başarılı** verme işlemi tamamlandı.

```powershell
$exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
}
[Console]::WriteLine("")
$exportStatus
```
## <a name="cancel-the-export-request"></a>Dışarı aktarma isteğini iptal et

[Database Operations-Cancel API](/rest/api/sql/databaseoperations/cancel) veya PowerShell [stop-AzSqlDatabaseActivity komutunu](/powershell/module/az.sql/Stop-AzSqlDatabaseActivity)kullanın, burada PowerShell komutuna bir örnektir.

```cmd
Stop-AzSqlDatabaseActivity -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -OperationId $Operation.OperationId
```

## <a name="next-steps"></a>Sonraki adımlar

- Tek bir veritabanı ve havuza alınmış veritabanlarının uzun süreli yedek saklama hakkında bilgi edinmek için, bir veritabanını arşiv amaçlarıyla dışarı aktarmaya alternatif olarak bkz. [uzun süreli yedek saklama](long-term-retention-overview.md). [Yalnızca kopya veritabanı yedeklemelerini](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) , uzun süreli yedek bekletmeye alternatif olarak ZAMANLAMAK Için SQL Agent işlerini kullanabilirsiniz.
- BACPAC dosyalarını kullanarak geçiş hakkında bir SQL Server Müşteri Danışmanlık Ekibi blogu için bkz. [BACPAC Dosyalarını kullanarak SQL Server’dan Azure SQL Veritabanına Geçiş](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files).
- Bir SQL Server veritabanına BACPAC aktarma hakkında bilgi edinmek için bkz. [BACPAC 'yi bir SQL Server veritabanına aktarma](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database).
- Bir SQL Server veritabanından BACPAC dışarı aktarma hakkında bilgi edinmek için bkz. [veri katmanı uygulamasını dışarı aktarma](/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
- Veri taşıma hizmetini kullanarak bir veritabanını geçirme hakkında bilgi edinmek için bkz. [DMS kullanarak SQL Server 'Ten Azure SQL veritabanı 'Na geçiş](../../dms/tutorial-sql-server-to-azure-sql.md).
- Azure SQL veritabanı 'na geçiş yapmak için SQL Server 'den bir Prelude olarak veriyorsanız, bkz. [SQL Server veritabanını Azure SQL veritabanı 'Na geçirme](migrate-to-database-from-sql-server.md).
- Depolama anahtarlarını ve paylaşılan erişim imzalarını güvenli bir şekilde yönetme ve paylaşma hakkında bilgi edinmek için bkz. [Azure Storage Güvenlik Kılavuzu](../../storage/blobs/security-recommendations.md).