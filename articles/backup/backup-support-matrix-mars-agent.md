---
title: MARS Aracısı için destek matrisi
description: Bu makalede, Microsoft Azure Kurtarma Hizmetleri (MARS) Aracısı çalıştıran makineleri yedeklerken Azure Backup desteği özetlenmektedir.
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: 2170440b7b47861b75801b8dbd334686b4cabc8b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98985623"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Microsoft Azure Kurtarma Hizmetleri (MARS) Aracısı ile yedekleme için destek matrisi

[Azure Backup hizmetini](backup-overview.md) şirket içi makineleri ve uygulamaları yedeklemek ve Azure sanal makinelerini (VM 'ler) yedeklemek için kullanabilirsiniz. Bu makalede, makineleri yedeklemek için Microsoft Azure Kurtarma Hizmetleri (MARS) Aracısı 'nı kullandığınızda destek ayarları ve sınırlamaları özetlenmektedir.

## <a name="the-mars-agent"></a>MARS Aracısı

Azure Backup, şirket içi makinelerden ve Azure VM 'lerinden verileri Azure 'daki bir yedekleme kurtarma hizmetleri kasasına yedeklemek için MARS Aracısı 'nı kullanır. MARS Aracısı şunları yapabilir:

- Azure 'daki bir yedekleme kurtarma hizmetleri kasasından doğrudan yedekleyebilmeleri için şirket içi Windows makinelerinde çalıştırın.
- Doğrudan bir kasaya yedekleyebilmeleri için Windows VM 'lerde çalıştırın.
- Microsoft Azure Backup Server (MABS) veya bir System Center Data Protection Manager (DPM) sunucusu üzerinde çalıştırın. Bu senaryoda, makineler ve iş yükleri MABS 'e veya DPM sunucusuna yedekleme. MARS Aracısı daha sonra bu sunucuyu Azure 'daki bir kasaya yedekler.

> [!NOTE]
>Azure Backup, gün ışığından yararlanma saati (DST) için otomatik saat ayarlamayı desteklemez. Gerçek zamanlı ve zamanlanan yedekleme zamanı arasında uyuşmazlık oluşmasını engellemek için gün ışığından yararlanma işlemi yapıldığından emin olmak için ilkeyi değiştirin.

Yedekleme seçenekleriniz aracının yüklü olduğu yere bağlıdır. Daha fazla bilgi için bkz. [Mars Aracısı 'nı kullanarak Azure Backup mimarisi](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). MABS ve DPM yedekleme mimarisi hakkında daha fazla bilgi için bkz. [DPM veya mabs 'ye yedekleme](backup-architecture.md#architecture-back-up-to-dpmmabs). Ayrıca bkz. yedekleme mimarisi için [gereksinimler](backup-support-matrix-mabs-dpm.md) .

**Yükleme** | **Ayrıntılar**
--- | ---
En son MARS aracısını indirin | Aracının en son sürümünü kasadan indirebilir veya [doğrudan indirebilirsiniz](https://aka.ms/azurebackup_agent).
Doğrudan bir makineye yükler | MARS aracısını doğrudan şirket içi Windows Server 'a veya [desteklenen işletim sistemlerinden](./backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)herhangi birini çalıştıran BIR Windows sanal makinesine yükleyebilirsiniz.
Yedekleme sunucusuna yüklensin | DPM 'yi veya MABS 'yi Azure 'a yedeklemek üzere ayarlarken, MARS aracısını sunucusuna indirip yüklersiniz. Aracıyı, yedekleme sunucusu desteği matrisindeki [desteklenen işletim sistemlerine](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) yükleyebilirsiniz.

> [!NOTE]
> Varsayılan olarak, yedekleme için etkinleştirilen Azure VM 'lerinin Azure Backup uzantısı yüklemesi vardır. Bu uzantı tüm VM 'yi yedekler. Tüm VM yerine belirli klasörleri ve dosyaları yedeklemek istiyorsanız, bir Azure sanal makinesine, bir Azure VM 'ye MARS aracısını yükleyebilir ve çalıştırabilirsiniz.
> MARS aracısını bir Azure VM 'de çalıştırdığınızda, VM 'de geçici depolamada bulunan dosya veya klasörleri yedekler. Dosyalar veya klasörler geçici depolamadan kaldırılırsa veya geçici depolama kaldırılırsa yedeklemeler başarısız olur.

## <a name="cache-folder-support"></a>Önbellek klasörü desteği

Verileri yedeklemek için MARS Aracısı 'nı kullandığınızda, aracı verilerin bir anlık görüntüsünü alır ve verileri Azure 'a göndermeden önce yerel bir önbellek klasöründe depolar. Önbellek (sıfırdan) klasöründe birkaç gereksinim vardır:

**Önbellek** | **Ayrıntılar**
--- | ---
Boyut |  Önbellek klasöründeki boş alan, yedekleme verilerinizin genel boyutunun en az 5 ila yüzde 10 olması gerekir.
Konum | Önbellek klasörünün yedeklenmekte olan makinede yerel olarak depolanması ve çevrimiçi olması gerekir. Önbellek klasörü bir ağ paylaşımında, çıkarılabilir medyada veya çevrimdışı bir birimde olmalıdır.
Klasör | Önbellek klasörü, yinelenenleri kaldırılmış bir birimde veya sıkıştırılmış, bu seyrek olan veya bir yeniden ayrıştırma noktası olan bir klasörde şifrelenmemelidir.
Konum değişiklikleri | Yedekleme altyapısını ( `net stop bengine` ) durdurup önbellek klasörünü yeni bir sürücüye kopyalayarak önbellek konumunu değiştirebilirsiniz. (Yeni sürücüde yeterli alan olduğundan emin olun.) Ardından, **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** (**config/Scratchlocation** ve **config/CloudBackupProvider/scratchlocation**) altındaki iki kayıt defteri girişini yeni konuma güncelleştirin ve altyapıyı yeniden başlatın.

## <a name="networking-and-access-support"></a>Ağ ve erişim desteği

### <a name="url-and-ip-access"></a>URL ve IP erişimi

MARS aracısının bu URL 'Lere erişmesi gerekir:

- `http://www.msftncsi.com/ncsi.txt`
- *.Microsoft.com
- *.WindowsAzure.com
- *. MicrosoftOnline.com
- *. Windows.net
- `www.msftconnecttest.com`

Bu IP adresleri:

- 20.190.128.0/18
- 40.126.0.0/18

Yukarıda listelenen tüm URL ve IP adreslerine erişim, bağlantı noktası 443 ' de HTTPS protokolünü kullanır.

MARS Aracısı 'nı kullanarak Azure VM 'lerinden dosya ve klasörleri yedeklerken, Azure sanal ağının de erişime izin verecek şekilde yapılandırılması gerekir. Ağ güvenlik grupları (NSG) kullanıyorsanız, Azure Backup giden erişime izin vermek için *AzureBackup* Service etiketini kullanın. Azure Backup etiketine ek olarak, Azure AD (*AzureActiveDirectory*) ve Azure depolama (*depolama*) için benzer [NSG kuralları](../virtual-network/network-security-groups-overview.md#service-tags) oluşturarak kimlik doğrulama ve veri aktarımı için de bağlantıya izin vermeniz gerekir. Aşağıdaki adımlar Azure Backup etiketi için bir kural oluşturma işlemini anlatmaktadır:

1. **Tüm hizmetler**' de **ağ güvenlik grupları** ' na gidin ve ağ güvenlik grubunu seçin.
2. **Ayarlar** altında **giden güvenlik kuralları** ' nı seçin.
3. **Add (Ekle)** seçeneğini belirleyin. [Güvenlik kuralı ayarları](../virtual-network/manage-network-security-group.md#security-rule-settings)' nda açıklandığı gibi yeni bir kural oluşturmak için gereken tüm ayrıntıları girin. Seçenek **hedefinin** *hizmet etiketi* olarak ayarlandığından ve **hedef hizmet etiketinin** *AzureBackup* olarak ayarlandığından emin olun.
4. Yeni oluşturulan giden güvenlik kuralını kaydetmek için **Ekle** ' yi seçin.

Benzer şekilde, Azure depolama ve Azure AD için NSG giden güvenlik kuralları oluşturabilirsiniz. Hizmet etiketleri hakkında daha fazla bilgi için [Bu makaleye](../virtual-network/service-tags-overview.md)bakın.

### <a name="azure-expressroute-support"></a>Azure ExpressRoute desteği

Azure ExpressRoute üzerinden verilerinizi, genel eşleme (eski devreler için kullanılabilir) ve Microsoft eşlemesi ile birlikte yedekleyebilirsiniz. Özel eşleme üzerinde yedekleme desteklenmez.

Ortak eşleme ile: aşağıdaki etki alanlarına/adreslere erişim sağlayın:

* URL’ler
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP adresleri
  * 20.190.128.0/18
  * 40.126.0.0/18

Microsoft eşlemesi ile aşağıdaki hizmetleri/bölgeleri ve ilgili topluluk değerlerini seçin:

- Azure Backup (Kurtarma Hizmetleri kasanızın konumuna göre)
- Azure Active Directory (12076:5060)
- Azure depolama (Kurtarma Hizmetleri kasanızın konumuna göre)

Daha fazla bilgi için bkz. [ExpressRoute yönlendirme gereksinimleri](../expressroute/expressroute-routing.md#bgp).

>[!NOTE]
>Ortak eşleme, yeni devreler için kullanım dışıdır.

### <a name="private-endpoint-support"></a>Özel uç nokta desteği

Artık verilerinizi sunuculardan kurtarma hizmetleri kasanıza güvenli bir şekilde yedeklemek için özel uç noktaları kullanabilirsiniz. Azure Active Directory şu anda özel uç noktaları desteklemediğinden, Azure Active Directory için gereken IP 'Ler ve FQDN 'Lerin Ayrıca giden erişime ayrı olarak izin verilmesi gerekir.

Şirket içi kaynaklarınızı yedeklemek için MARS Aracısı 'nı kullandığınızda, şirket içi ağınızın (yedeklenecek kaynakları içeren) kasa için özel bir uç nokta içeren Azure VNet ile eşlendiğinizden emin olun. Daha sonra MARS aracısını yüklemeye ve yedeklemeyi yapılandırmanıza devam edebilirsiniz. Ancak, yedekleme için tüm iletişimin yalnızca eşlenmiş ağ aracılığıyla yapıldığından emin olmanız gerekir.

Bir MARS Aracısı kaydedildikten sonra kasadaki özel uç noktaları kaldırırsanız, kapsayıcıyı kasaya yeniden kaydetmeniz gerekir. Bunların korumasını durdurmanız gerekmez.

[Azure Backup için özel uç noktalar](private-endpoints.md)hakkında daha fazla bilgi edinin.

### <a name="throttling-support"></a>Daraltma desteği

**Özellik** | **Ayrıntılar**
--- | ---
Bant genişliği denetimi | Destekleniyor. MARS aracısında, bant genişliğini ayarlamak için **Özellikleri Değiştir** ' i kullanın.
Ağ kapasitesi azaltma | Windows Server 2008 R2, Windows Server 2008 SP2 veya Windows 7 çalıştıran yedeklenen makinelerde kullanılamaz.

## <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri

>[!NOTE]
> MARS Aracısı Windows Server Core SKU 'Larını desteklemez.

MARS aracısını aşağıda belirtilen işletim sistemlerinde çalışan Azure 'a doğrudan yedeklemek için kullanabilirsiniz:

1. Şirket içi Windows Server 'Lar
2. Windows çalıştıran Azure VM 'Leri

İşletim sistemleri 64 bit olmalıdır ve en son hizmet paketlerini ve güncelleştirmelerini çalıştırıyor olmalıdır. Aşağıdaki tabloda bu işletim sistemleri özetlenmektedir:

**İşletim sistemi** | **Dosyalar/klasörler** | **Sistem durumu** | **Yazılım/modül gereksinimleri**
--- | --- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Yes | Hayır |  Yazılım/modül gereksinimleri için karşılık gelen sunucu sürümünü denetleyin
Windows 8.1 (Enterprise, Pro)| Yes |Hayır | Yazılım/modül gereksinimleri için karşılık gelen sunucu sürümünü denetleyin
Windows 8 (Enterprise, Pro) | Yes | Hayır | Yazılım/modül gereksinimleri için karşılık gelen sunucu sürümünü denetleyin
Windows Server 2016 (Standard, Datacenter, Essentials) | Yes | Yes | -.NET 4,5 <br> -Windows PowerShell <br> -En son uyumlu Microsoft VC + + yeniden dağıtılabilir <br> -Microsoft Yönetim Konsolu (MMC) 3,0
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | Yes | Yes | -.NET 4,5 <br> -Windows PowerShell <br> -En son uyumlu Microsoft VC + + yeniden dağıtılabilir <br> -Microsoft Yönetim Konsolu (MMC) 3,0
Windows Server 2012 (Standard, Datacenter, Foundation) | Yes | Yes |-.NET 4,5 <br> -Windows PowerShell <br> -En son uyumlu Microsoft VC + + yeniden dağıtılabilir <br> -Microsoft Yönetim Konsolu (MMC) 3,0 <br> -Dağıtım Görüntüsü Bakımı ve yönetimi (DISM.exe)
Windows Storage Server 2016/2012 R2/2012 (Standart, çalışma grubu) | Yes | Hayır | -.NET 4,5 <br> -Windows PowerShell <br> -En son uyumlu Microsoft VC + + yeniden dağıtılabilir <br> -Microsoft Yönetim Konsolu (MMC) 3,0
Windows Server 2019 (Standard, Datacenter, Essentials) | Yes | Yes | -.NET 4,5 <br> -Windows PowerShell <br> -En son uyumlu Microsoft VC + + yeniden dağıtılabilir <br> -Microsoft Yönetim Konsolu (MMC) 3,0

Daha fazla bilgi için bkz. [desteklenen MABS ve DPM işletim sistemleri](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

### <a name="operating-systems-at-end-of-support"></a>Destek sonundaki işletim sistemleri

Aşağıdaki işletim sistemleri destek sonunda bulunur ve korunan olmaya devam etmek için işletim sistemini yükseltmeniz önemle önerilir.

Mevcut taahhütler işletim sisteminin yükseltilmesini engelliyorsa, Windows Server 'ı Azure VM 'lerine geçirmeyi ve korumalı kalmaya devam etmek için Azure VM yedeklemelerine yararlanmanızı göz önünde bulundurun. Windows Server 'ı geçirme hakkında daha fazla bilgi için [buraya geçiş sayfasını](https://azure.microsoft.com/migration/windows-server/) ziyaret edin.

İşletim sistemini yükseltebileceğiniz veya Azure 'a geçiremeyeceğiniz şirket içi veya barındırılan ortamlarda, korunan ve desteklenen makinelere devam etmek için makineler için genişletilmiş güvenlik güncelleştirmelerini etkinleştirin. Yalnızca belirli sürümlerin genişletilmiş güvenlik güncelleştirmelerine uygun olduğuna dikkat edin. Daha fazla bilgi edinmek için [SSS sayfasını](https://www.microsoft.com/windows-server/extended-security-updates) ziyaret edin.

| **İşletim Sistemi**                                       | **Dosyalar/klasörler** | **Sistem durumu** | **Yazılım/modül gereksinimleri**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7 (Ultimate, Enterprise, Pro, Home Premium/Basic, Starter) | Yes               | Hayır                 | Yazılım/modül gereksinimleri için karşılık gelen sunucu sürümünü denetleyin |
| Windows Server 2008 R2 (Standard, Enterprise, Datacenter, Foundation) | Yes               | Yes                | -.NET 3,5, .NET 4,5 <br>  -Windows PowerShell <br>  -Uyumlu Microsoft VC + + yeniden dağıtılabilir <br>  -Microsoft Yönetim Konsolu (MMC) 3,0 <br>  -Dağıtım Görüntüsü Bakımı ve yönetimi (DISM.exe) |
| Windows Server 2008 SP2 (Standard, Datacenter, Foundation)  | Yes               | Hayır                 | -.NET 3,5, .NET 4,5 <br>  -Windows PowerShell <br>  -Uyumlu Microsoft VC + + yeniden dağıtılabilir <br>  -Microsoft Yönetim Konsolu (MMC) 3,0 <br>  -Dağıtım Görüntüsü Bakımı ve yönetimi (DISM.exe) <br>  -Sanal sunucu 2005 tabanı + KB KB948515 |

## <a name="backup-limits"></a>Yedekleme limitleri

### <a name="size-limits"></a>Boyut sınırları

Azure Backup yedeklenebilir bir dosya veya klasör veri kaynağı boyutunu sınırlandırır. Tek bir birimden yedeklediğiniz öğeler bu tabloda özetlenen boyutları aşamaz:

**İşletim sistemi** | **Boyut sınırı**
--- | ---
Windows Server 2012 veya üzeri |54.400 GB
Windows Server 2008 R2 SP1 |1.700 GB
Windows Server 2008 SP2| 1.700 GB
Windows 8 veya üzeri| 54.400 GB
Windows 7| 1.700 GB

### <a name="minimum-retention-limits"></a>En düşük bekletme sınırları

Farklı kurtarma noktaları için ayarlanyen minimum saklama süreleri aşağıda verilmiştir:

|Kurtarma noktası |Süre  |
|---------|---------|
|Günlük kurtarma noktası    |   7 gün      |
|Haftalık kurtarma noktası     |    4 hafta     |
|Aylık kurtarma noktası    |   3 ay      |
|Yıllık kurtarma noktası  |      1 yıl   |

### <a name="other-limitations"></a>Diğer sınırlamalar

- MARS, tek bir kasada aynı ada sahip birden fazla makinenin korunmasını desteklemez.

## <a name="supported-file-types-for-backup"></a>Yedekleme için desteklenen dosya türleri

**Tür** | **Destek**
--- | ---
Memiştir<sup>*</sup>| Destekleniyor.
Sıkıştırılmış | Destekleniyor.
Seyrek | Destekleniyor.
Sıkıştırılmış ve seyrek |Destekleniyor.
Sabit bağlantılar| Desteklenmez. Atlanmış.
Yeniden ayrıştırma noktası| Desteklenmez. Atlanmış.
Şifrelenmiş ve seyrek |Desteklenmez. Atlanmış.
Sıkıştırılmış akış| Desteklenmez. Atlanmış.
Seyrek akış| Desteklenmez. Atlanmış.
OneDrive (eşitlenen dosyalar seyrek akışlardır)| Desteklenmez.
DFS Çoğaltma etkin olan klasörler | Desteklenmez.

\* MARS aracısının şifrelenmiş dosyalara erişmek için gereken sertifikalara erişimi olduğundan emin olun. Erişilemeyen dosyalar atlanacak.

## <a name="supported-drives-or-volumes-for-backup"></a>Yedekleme için desteklenen sürücüler veya birimler

**Sürücü/birim** | **Destek** | **Ayrıntılar**
--- | --- | ---
Salt okuma birimleri| Desteklenmez | Birim kopyası gölge hizmeti (VSS) yalnızca birim yazılabilir ise geçerlidir.
Çevrimdışı birimler| Desteklenmez |VSS yalnızca birim çevrimiçi olduğunda işe yarar.
Ağ paylaşma| Desteklenmez |Birimin sunucuda yerel olması gerekir.
BitLocker-kilitli birimler| Desteklenmez |Yedekleme başlamadan önce birimin kilidi açılmış olmalıdır.
Dosya sistemi kimliği| Desteklenmez |Yalnızca NTFS desteklenir.
Çıkarılabilir medya| Desteklenmez |Tüm yedekleme öğesi kaynakları *sabit* bir duruma sahip olmalıdır.
Yinelenenleri kaldırılmış sürücüler | Desteklenir | Azure Backup yinelenenleri kaldırılmış verileri normal verilere dönüştürür. Verileri en iyi duruma getirir, şifreler, depolar ve verileri kasaya gönderir.

## <a name="support-for-initial-offline-backup"></a>İlk çevrimdışı yedekleme desteği

Azure Backup, diskleri kullanarak ilk yedekleme verilerini Azure 'a aktarmaya yönelik *çevrimdışı dağıtım* desteği sağlar. Bu destek, ilk yedeklemeniz büyük olasılıkla terabayt (TBs) boyut aralığında olduğunda yararlıdır. İçin çevrimdışı yedekleme desteklenir:

- MARS aracısını çalıştıran şirket içi makinelerde dosya ve klasörlerin doğrudan yedeklemesi.
- Bir DPM sunucusundan veya MABS 'tan iş yüklerini ve dosyaları yedekleme.

Çevrimdışı yedekleme sistem durumu dosyaları için kullanılamaz.

## <a name="support-for-data-restoration"></a>Veri geri yükleme desteği

Azure Backup [anlık geri yükleme](backup-instant-restore-capability.md) özelliğini kullanarak verileri kasaya kopyalanmadan önce geri yükleyebilirsiniz. Yedeklemekte olduğunuz makinenin .NET Framework 4.5.2 veya üzeri bir sürümü çalıştırıyor olması gerekir.

Yedeklemeler, işletim sisteminin önceki bir sürümünü çalıştıran bir hedef makineye geri yüklenemez. Örneğin, Windows 7 çalıştıran bir bilgisayardan alınan bir yedekleme, Windows 8 veya sonraki bir sürümde geri yüklenebilir. Ancak Windows 8 çalıştıran bir bilgisayardan alınan bir yedekleme, Windows 7 çalıştıran bir bilgisayara geri yüklenemez.

## <a name="next-steps"></a>Sonraki adımlar

- [Mars aracısını kullanan yedekleme mimarisi](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)hakkında daha fazla bilgi edinin.
- [Mars aracısını MABS veya BIR DPM sunucusu üzerinde çalıştırdığınızda](backup-support-matrix-mabs-dpm.md)nelerin desteklendiğini öğrenin.
