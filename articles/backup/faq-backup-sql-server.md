---
title: SSS-Azure VM 'lerinde SQL Server veritabanlarını yedekleme
description: Azure Backup ile Azure VM 'lerinde SQL Server veritabanlarının yedeklenmesi hakkında sık sorulan soruların yanıtlarını bulun.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: ca785e217da4355a44ffbb26b813d55d942c5c14
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98787629"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Azure VM yedeklemesi üzerinde çalışan SQL Server veritabanları hakkında SSS

Bu makalede, Azure sanal makinelerinde (VM 'Ler) çalışan SQL Server veritabanlarının yedeklenmesi ve [Azure Backup](backup-overview.md) hizmetinin kullanılması hakkında sık sorulan sorular yanıtlanmaktadır.

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>IaaS sanal makinesi için Azure Backup ve aynı makinede SQL Server kullanabilir miyim?

Evet, VM yedeklemesi ve SQL Backup 'ın aynı VM 'de olmasını sağlayabilirsiniz. Bu durumda, günlükleri kesmeyin şekilde sanal makinede salt kopya tam yedeklemeyi tetikliyoruz.

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>Çözüm yeniden denensin veya yedeklemeleri otomatik olarak al mi?

Bazı durumlarda Azure Backup hizmeti düzeltme yedeklemelerini tetikler. Otomatik Heal aşağıda bahsedilen altı koşuldan herhangi biri için gerçekleşebilir:

- Günlük veya değişiklik yedeklemesi LSN doğrulama hatası nedeniyle başarısız olursa, sonraki günlük veya değişiklik yedeklemesi bunun yerine tam yedeklemeye dönüştürülür.
- Bir günlük veya değişiklik yedeklemesinden önce tam yedekleme gerçekleştiyse, bu günlük veya değişiklik yedeklemesi bunun yerine tam yedeklemeye dönüştürülür.
- En son tam yedeklemenin zaman aşımı 15 günden eskiyse, sonraki günlük veya değişiklik yedeklemesi bunun yerine tam yedeklemeye dönüştürülür.
- Uzantı yükseltmesi nedeniyle iptal edilen tüm yedekleme işleri, yükseltme tamamlandıktan ve uzantı başlatıldıktan sonra yeniden tetiklenir.
- Geri yükleme sırasında veritabanının üzerine yazmayı seçerseniz, bir sonraki günlük/değişiklik yedeklemesi başarısız olur ve bunun yerine tam yedekleme tetiklenir.
- Veritabanı kurtarma modelindeki değişiklik nedeniyle günlük zincirlerinin sıfırlanması için tam yedeklemenin gerekli olduğu durumlarda, bir sonraki zamanlamaya göre bir tam otomatik olarak tetiklenir.

Otomatik olarak, bir özellik olarak tüm kullanıcılar için varsayılan olarak etkindir. Ancak, devre dışı bırakmak isterseniz aşağıdaki adımları gerçekleştirin:

- SQL Server örneğinde, *C:\Program Files\Azure Iş yükü Backup\bin* klasöründe, dosya **üzerindeExtensionSettingsOverrides.js** oluşturun veya düzenleyin.
- **ExtensionSettingsOverrides.js**, *{"EnableAutoHealer": false}* olarak ayarlayın.
- Değişikliklerinizi kaydedin ve dosyayı kapatın.
- SQL Server örneğinde, **Görevi Yönet** ' i açın ve sonra **AzureWLBackupCoordinatorSvc** hizmetini yeniden başlatın.

## <a name="can-i-control-how-many-concurrent-backups-run-on-the-sql-server"></a>SQL Server üzerinde eş zamanlı olarak kaç yedekleme işlemi çalıştırılacağını denetleyebilir miyim?

Evet. SQL Server örneği üzerindeki etkiyi en aza indirmek için yedekleme ilkesinin çalışma hızını azaltabilirsiniz. Ayarı değiştirmek için:

1. SQL Server örneğinde, *C:\Program Files\Azure Iş yükü Backup\bin* klasöründe, dosya *üzerindeExtensionSettingsOverrides.js* oluşturun.
2. *ExtensionSettingsOverrides.js* dosyadaki **Defaultbackuptasksthreshold** ayarını daha düşük bir değere (örneğin, 5) değiştirin. <br>
  `{"DefaultBackupTasksThreshold": 5}`
<br>
DefaultBackupTasksThreshold varsayılan değeri **20**' dir.

3. Değişikliklerinizi kaydedin ve dosyayı kapatın.
4. SQL Server örneğinde, **Görev Yöneticisi**'ni açın. **AzureWLBackupCoordinatorSvc** hizmetini yeniden başlatın.<br/> <br/>
 Bu yöntem, yedek uygulamanın büyük miktarda kaynak tükettiği durumlarda, SQL Server [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) , gelen uygulama ISTEKLERININ kullanabileceği CPU, fiziksel GÇ ve bellek miktarına yönelik sınırları belirtmenin daha genel bir yoludur.

> [!NOTE]
> UX 'de, belirli bir zamanda devam edebilir ve çok sayıda yedekleme zamanlayabilirsiniz. Ancak, yukarıdaki örneğe göre, 5 ' in bir kayan penceresinde işlenir.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>İkincil bir çoğaltmadan tam yedekleme çalıştırabilir miyim?

SQL kısıtlamalarına göre, Ikincil çoğaltmada yalnızca tam yedeklemeyi Kopyala ' yı çalıştırabilirsiniz. Ancak, tam yedeklemeye izin verilmez.

## <a name="can-i-protect-availability-groups-on-premises"></a>Şirket içi kullanılabilirlik gruplarını koruyabilir miyim?

Hayır. Azure 'da çalışan SQL Server veritabanlarını Azure Backup korur. Bir kullanılabilirlik grubu (AG) Azure ile şirket içi makineler arasında yayılaysa, ağ yalnızca birincil çoğaltma Azure 'da çalışıyorsa korunabilir. Ayrıca, Azure Backup yalnızca kurtarma hizmetleri kasasıyla aynı Azure bölgesinde çalışan düğümleri korur.

## <a name="can-i-protect-availability-groups-across-regions"></a>Bölge genelindeki kullanılabilirlik gruplarını koruyabilir miyim?

Azure Backup kurtarma hizmetleri Kasası, kasala aynı bölgedeki tüm düğümleri algılayabilir ve koruyabilir. SQL Server Always on kullanılabilirlik grubunuz birden çok Azure bölgesine yayılmışsa, birincil düğümü olan bölgeden Yedeklemeyi ayarlayın. Azure Backup, yedekleme tercihlerinize göre kullanılabilirlik grubundaki tüm veritabanlarını algılayabilir ve koruyabilir. Yedekleme tercihiniz karşılanmazsa yedeklemeler başarısız olur ve hata uyarısını alırsınız.

## <a name="do-successful-backup-jobs-create-alerts"></a>Başarılı yedekleme işleri sonucunda uyarı oluşturulur mu?

Hayır. Başarılı yedekleme işleri uyarı oluşturmaz. Uyarılar yalnızca başarısız olan yedekleme işleri için gönderilir. Portal uyarıları için ayrıntılı davranış [burada](backup-azure-monitoring-built-in-monitor.md)belgelenmiştir. Ancak, başarılı işler için bile uyarıları almak istiyorsanız, [Izlemeyi Azure izleyici 'yi kullanarak](backup-azure-monitoring-use-azuremonitor.md)kullanabilirsiniz.

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Yedekleme Işleri menüsünde zamanlanmış yedekleme işlerini görebilir miyim?

**Yedekleme işi** menüsünde, çok sık olabileceğinizden bu yana zamanlanan günlük yedeklemeleri hariç tüm zamanlanmış ve isteğe bağlı işlemler gösterilir. Zamanlanan günlük işleri için [Azure izleyici 'yi kullanarak izlemeyi](backup-azure-monitoring-use-azuremonitor.md)kullanın.

## <a name="are-future-databases-automatically-added-for-backup"></a>Gelecekteki veritabanları yedekleme için otomatik olarak eklenir mi?

Evet, bu özelliği [otomatik koruma](backup-sql-server-database-azure-vms.md#enable-auto-protection)ile elde edebilirsiniz.  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Otomatik korumalı bir örnekten veritabanı silersem yedeklemelere ne olur?

Bir veritabanı, bir yeniden korunan örnekten bırakılırsa, veritabanı yedeklemeleri hala denenir. Bu, silinen veritabanının **yedekleme öğeleri** altında sağlıksız olarak gösterilmeye başladığı ve hala korunduğu anlamına gelir.

Bu veritabanını korumayı durdurmak için doğru yol, bu veritabanındaki **silme verileriyle** **yedeklemeyi durdurmaktır** .  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Otomatik korumalı bir veritabanının yedekleme işlemini durdurdum, davranışı ne olur?

**Verileri koruma ile yedeklemeyi durdurursanız**, gelecekteki yedeklemeler gerçekleşmez ve var olan kurtarma noktaları değişmeden kalır. Veritabanı hala korumalı olarak kabul edilir ve **yedekleme öğeleri** altında gösterilir.

**Silme verileriyle yedeklemeyi durdurursanız**, gelecekteki yedeklemeler gerçekleşmeyecektir ve var olan kurtarma noktaları da silinir. Veritabanı korunmuyor olarak değerlendirilir ve yapılandırma Yedeklemedeki örnek altında gösterilir. Bununla birlikte, el ile seçilemeyen veya bu veritabanı tarafından korunabilen diğer, korunan veritabanlarının aksine, bu veritabanı gri görünür ve seçilemez. Bu veritabanını yeniden korumanın tek yolu, örnekte otomatik korumayı devre dışı bırakmanız. Artık bu veritabanını seçebilir ve korumayı yapılandırabilir veya örnekte otomatik korumayı yeniden etkinleştirebilirsiniz.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Korunduktan sonra veritabanının adını değiştirdiğimde, davranış ne olur?

Yeniden adlandırılmış bir veritabanı yeni bir veritabanı olarak değerlendirilir. Bu nedenle, hizmet bu durumu veritabanı bulunmazsa ve yedeklemelerle başarısız olarak değerlendirir.

Şimdi yeniden adlandırılan veritabanını seçebilirsiniz ve üzerinde koruma yapılandırabilirsiniz. Örnekte otomatik koruma etkinse, yeniden adlandırılmış veritabanı otomatik olarak algılanır ve korunur.

## <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Neden yeniden korunan bir örnek için eklenen bir veritabanını göremiyorum?

[Bir oto korumalı örneğe eklediğiniz](backup-sql-server-database-azure-vms.md#enable-auto-protection) bir veritabanı, korunan öğeler altında hemen görünmeyebilir. Bunun nedeni, bulmanın genellikle her 8 saatte bir çalışır. Ancak, aşağıdaki görüntüde gösterildiği gibi, veritabanlarını **yeniden keşfet**' i seçerek bir bulmayı el ile çalıştırırsanız yeni veritabanlarını hemen bulabilir ve koruyabilirsiniz:

  ![Yeni eklenen bir veritabanını el ile bulma](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="can-i-protect-databases-on-virtual-machines-that-have-azure-disk-encryption-ade-enabled"></a>Azure disk şifrelemesi (ADE) etkin olan sanal makinelerde veritabanlarını koruyabilir miyim?
Evet, Azure disk şifrelemesi (ADE) etkin olan sanal makinelerde veritabanlarını koruyabilirsiniz.

## <a name="can-i-protect-databases-that-have-tde-transparent-data-encryption-turned-on-and-will-the-database-stay-encrypted-through-the-entire-backup-process"></a>TDE (Saydam Veri Şifrelemesi) açık olan veritabanlarını koruyabilir ve veritabanı tüm yedekleme işlemi boyunca şifreli olarak kalır mi?

Evet, Azure Backup SQL Server veritabanlarının veya TDE 'ın etkinleştirildiği sunucunun yedeklenmesini destekler. Yedekleme, Azure tarafından yönetilen anahtarlarla veya müşteri tarafından yönetilen anahtarlarla (BYOK) birlikte [TDE](/sql/relational-databases/security/encryption/transparent-data-encryption) 'yı destekler.  Yedekleme, yedekleme işleminin bir parçası olarak herhangi bir SQL şifrelemesi gerçekleştirmez, bu sayede veritabanı yedeklendiğinde şifreli olarak kalır.

## <a name="does-azure-backup-perform-a-checksum-operation-on-the-data-stream"></a>Veri akışında bir sağlama toplamı işlemi mi Azure Backup?

Veri akışında bir sağlama toplamı işlemi gerçekleştiririz. Ancak bu, [SQL sağlama toplamıyla](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server)karıştırılmamalıdır.
Azure iş yükü yedeklemesi, veri akışındaki sağlama toplamını hesaplar ve yedekleme işlemi sırasında açıkça depolar. Bu sağlama toplamı akışı daha sonra bir başvuru olarak alınır ve verilerin tutarlı olduğundan emin olmak için geri yükleme işlemi sırasında veri akışının sağlaması sırasında çapraz doğrulanır.

## <a name="next-steps"></a>Sonraki adımlar

Azure VM 'de çalışan [SQL Server bir veritabanını nasıl yedekleyeceğinizi](backup-azure-sql-database.md) öğrenin.