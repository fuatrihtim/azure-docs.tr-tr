---
title: Azure Site Recovery ile Hyper-V olağanüstü durum kurtarma sorunlarını giderme
description: Azure Site Recovery kullanarak Hyper-V ile Azure çoğaltma için olağanüstü durum kurtarma sorunlarını nasıl giderebileceğinizi açıklar
services: site-recovery
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: sharrai
ms.openlocfilehash: c804e13029dcec42a43885cbf0d9b227b3d0338f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96750811"
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>Hyper-V’den Azure’a çoğaltma ve yük devretmeye ilişkin sorunları giderme

Bu makalede, [Azure Site Recovery](site-recovery-overview.md)kullanarak şirket içi Hyper-V VM 'lerini Azure 'a çoğaltma sırasında karşılaşabileceğiniz yaygın sorunlar açıklanmaktadır.

## <a name="enable-protection-issues"></a>Koruma sorunlarını etkinleştir

Hyper-V VM 'Leri için korumayı etkinleştirdiğinizde sorunlarla karşılaşırsanız, aşağıdaki önerileri kontrol edin:

1. Hyper-V konaklarınızın ve sanal makinelerinizin tüm [gereksinimleri ve önkoşulları](hyper-v-azure-support-matrix.md)karşıladığından emin olun.
2. Hyper-V sunucuları System Center Virtual Machine Manager (VMM) bulutlarında bulunuyorsa [VMM sunucusunu](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional)hazırladığınızı doğrulayın.
3. Hyper-v sanal makine yönetimi hizmetinin Hyper-V konaklarında çalışıp çalışmadığını denetleyin.
4. Hyper-V-VMMS\Admin oturum açma bölümünde görünen sorunları kontrol edin. Bu günlük, **uygulama ve hizmet günlükleri**  >  **Microsoft**  >  **Windows**'da bulunur.
5. Konuk VM 'de, WMI 'nın etkinleştirildiğini ve erişilebilir olduğunu doğrulayın.
   - Temel WMI testi [hakkında bilgi edinin](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf) .
   - [Sorun giderme](/windows/win32/wmisdk/wmi-troubleshooting) 'Ya.
   - WMI betikleri ve hizmetleriyle ilgili sorunları [giderin](/previous-versions/tn-archive/ff406382(v=msdn.10)#H22) .
6. Konuk VM 'de Integration Services 'ın en son sürümünün çalıştığından emin olun.
    - En son sürüme sahip [olup olmadığınızı denetleyin](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) .
    - [Sakla](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) Tümleştirme Hizmetleri güncel.

### <a name="cannot-enable-protection-as-the-virtual-machine-is-not-highly-available-error-code-70094"></a>Sanal makine yüksek oranda kullanılabilir olmadığından koruma etkinleştirilemiyor (hata kodu 70094)

Bir makine için çoğaltmayı etkinleştirirken ve makinenin yüksek oranda kullanılabilir olmadığı için çoğaltmanın etkinleştirilemeyeceğini belirten bir hata ile karşılaşırsanız, bu sorunu düzelttikten sonra aşağıdaki adımları deneyin:

- VMM sunucusunda VMM hizmetini yeniden başlatın.
- Sanal makineyi kümeden kaldırın ve yeniden ekleyin.

### <a name="the-vss-writer-ntds-failed-with-status-11-and-writer-specific-failure-code-0x800423f4"></a>VSS Yazıcı NTDS, durum 11 ve yazıcı özel hata kodu ile başarısız oldu 0x800423F4

Çoğaltmayı etkinleştirmeye çalışırken, çoğaltmayı etkinleştirme başarısız olan AST NTDS başarısız olduğunu bildiren bir hata olabilir. Bu sorunun olası nedenlerinden biri, sanal makinenin Windows Server 2012 ' deki işletim sisteminin Windows Server 2012 R2 değil. Bu sorunu onarmak için aşağıdaki adımları deneyin:

- 4072650 ile Windows Server R2 'ye yükseltme.
- Hyper-V konağının de Windows 2016 veya üzeri olduğundan emin olun.

## <a name="replication-issues"></a>Çoğaltma sorunları

İlk ve sürekli çoğaltma sorunlarını aşağıda gösterildiği gibi giderin:

1. Site Recovery hizmetlerinin [en son sürümünü](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) çalıştırdığınızdan emin olun.
2. Çoğaltmanın duraklatılıp duraklatılmadığını doğrulayın:
   - Hyper-V Yöneticisi konsolundaki VM sistem durumunu kontrol edin.
   - Kritik ise, **çoğaltma**  >  **görünümü çoğaltma durumu**> VM 'ye sağ tıklayın.
   - Çoğaltma duraklatılmışsa **çoğaltmayı yeniden başlatma**' ya tıklayın.
3. Gerekli hizmetlerin çalıştığından emin olun. Aksi takdirde, yeniden başlatın.
    - VMM olmadan Hyper-V ' d i çoğaltdıysanız, bu hizmetlerin Hyper-V konağında çalıştığından emin olun:
        - Sanal Makine Yönetim hizmeti
        - Microsoft Azure Kurtarma Hizmetleri Aracısı hizmeti
        - Microsoft Azure Site Recovery hizmeti
        - WMI Sağlayıcısı Ana Bilgisayar hizmeti
    - Ortamda VMM ile çoğaltma yapıyorsanız, bu hizmetlerin çalıştığından emin olun:
        - Hyper-V konağında, sanal makine yönetimi hizmeti, Microsoft Azure Kurtarma Hizmetleri Aracısı ve WMI sağlayıcısı ana bilgisayar hizmetinin çalıştığından emin olun.
        - VMM sunucusunda, System Center Virtual Machine Manager hizmetinin çalıştığından emin olun.
4. Hyper-V sunucusuyla Azure arasındaki bağlantıyı denetleyin. Bağlantıyı denetlemek için, Hyper V konağında Görev Yöneticisi 'ni açın. **Performans** sekmesinde **Kaynak İzleyicisi aç**' a tıklayın. **Ağ sekmesinde >** **ağ etkinliği ile işlem** cbengine.exe, büyük birimleri (MB) etkin bir şekilde gönderip göndermediğini denetleyin.
5. Hyper-V konaklarının Azure Depolama Blobu URL 'sine bağlanıp bağlanamadığını denetleyin. Ana bilgisayarların bağlanıp bağlanamadığını denetlemek için **cbengine.exe** seçin ve işaretleyin. Ana bilgisayardan Azure Storage blob 'una bağlantıyı doğrulamak için **TCP bağlantılarını** görüntüleyin.
6. Aşağıda açıklandığı gibi performans sorunlarını kontrol edin.
    
### <a name="performance-issues"></a>Performans sorunları

Ağ bant genişliği sınırlamaları, çoğaltmayı etkileyebilir. Sorunları aşağıdaki gibi giderin:

1. Ortamınızda bant genişliği veya kısıtlama kısıtlamaları olup olmadığını [denetleyin](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) .
2. [Dağıtım planlayıcısı profil oluşturucuyu](hyper-v-deployment-planner-run.md)çalıştırın.
3. Profil oluşturucuyu çalıştırdıktan sonra [bant genişliği](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) ve [depolama](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation) önerilerini izleyin.
4. [Veri dalgalanma sınırlamalarını](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)denetleyin. Bir sanal makinede yüksek veri dalgalanması görürseniz şunları yapın:
   - SANAL makinenizin yeniden eşitleme için işaretlenip işaretlenmediğini denetleyin.
   - Karmaşıklığın kaynağını araştırmak için [Bu adımları](https://techcommunity.microsoft.com/t5/virtualization/bg-p/Virtualization) izleyin.
   - HRL günlük dosyaları kullanılabilir disk alanının %50 ' ü aştığında dalgalanma gerçekleşebilir. Sorun bu ise, sorunun gerçekleştiği tüm VM 'Ler için daha fazla depolama alanı sağlayın.
   - Çoğaltmanın duraklatılmadığını denetleyin. Varsa, bu, daha fazla boyut katkıda bulunmak için HRL dosyasına değişiklikleri yazmaya devam eder.
 

## <a name="critical-replication-state-issues"></a>Kritik çoğaltma durumu sorunları

1. Çoğaltma durumunu denetlemek için şirket içi Hyper-V Yöneticisi konsoluna bağlanın, VM 'yi seçin ve sistem durumunu doğrulayın.

    ![Çoğaltma durumu](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. Ayrıntıları görmek için **çoğaltma durumunu görüntüle** ' ye tıklayın:

    - Çoğaltma duraklatıldığında, sanal makineye sağ tıklayın > **çoğaltma**  >  **çoğaltmayı sürdürür**.
    - Site Recovery ' de yapılandırılmış bir Hyper-V ana bilgisayarındaki VM aynı kümedeki farklı bir Hyper-V konağına veya tek başına bir makineye geçerse, VM için çoğaltma etkilenmez. Yeni Hyper-V konağının tüm önkoşulları karşıladığını ve Site Recovery ' de yapılandırıldığını kontrol edin.

## <a name="app-consistent-snapshot-issues"></a>Uygulamayla tutarlı anlık görüntü sorunları

Uygulamayla tutarlı bir anlık görüntü, VM içindeki uygulama verilerinin zaman içinde bir noktadaki anlık görüntüsüdür. Birim Gölge Kopyası Hizmeti (VSS), anlık görüntü çekilirken VM üzerindeki uygulamaların tutarlı bir durumda olmasını sağlar.  Bu bölümde karşılaşabileceğiniz bazı yaygın sorunlar ayrıntılı olarak ele verilebilir.

### <a name="vss-failing-inside-the-vm"></a>VSS, VM içinde başarısız oldu

1. Integration Services 'ın en son sürümünün yüklü ve çalışır olduğunu denetleyin.  Hyper-V konağındaki yükseltilmiş bir PowerShell isteminden aşağıdaki komutu çalıştırarak bir güncelleştirmenin kullanılabilir olup olmadığını denetleyin: **Get-VM | SELECT name, State, ıntegrationservicesstate**.
2. VSS hizmetlerinin çalıştığını ve sağlıklı olduğunu kontrol edin:
   - Hizmetleri denetlemek için konuk VM 'de oturum açın. Sonra bir yönetici komut istemi açın ve tüm VSS yazıcılarının sağlıklı olup olmadığını denetlemek için aşağıdaki komutları çalıştırın.
       - **Vssadmin list yazarları**
       - **Vssadmin list gölgeleri**
       - **Vssadmin list sağlayıcıları**
   - Çıktıyı denetleyin. Yazarlar başarısız durumdaysa, şunları yapın:
       - VSS işlem hataları için VM 'deki uygulama olay günlüğünü denetleyin.
   - Başarısız yazıcı ile ilişkili bu hizmetleri yeniden başlatmayı deneyin:
     - Birim gölge kopyası
       - VSS sağlayıcısı Azure Site Recovery
   - Bunu yaptıktan sonra, uygulamayla tutarlı anlık görüntülerin başarıyla oluşturulup Oluşturulanı görmek için birkaç saat bekleyin.
   - Son çare olarak VM 'yi yeniden başlatmayı deneyin. Bu durum yanıt vermeyen Hizmetleri çözümleyebilir.
3. Sanal makinede dinamik disklere sahip olmadığınızı kontrol edin. Bu, uygulamayla tutarlı anlık görüntüler için desteklenmez. Disk Yönetimi (Diskmgmt. msc) iade edebilirsiniz.

    ![Dinamik disk](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. VM 'ye bağlı bir Iscsı diskine sahip olup olmadığınızı denetleyin. Bu özellik desteklenmez.
5. Yedekleme hizmetinin etkin olup olmadığını denetleyin. **Hyper-V ayarları**  >  **Tümleştirme Hizmetleri**'nde etkin olduğunu doğrulayın.
6. VSS anlık görüntülerini alan uygulamalarla çakışma olmadığından emin olun. Aynı anda birden çok uygulama VSS anlık görüntüsü almaya çalışıyorsa, çakışmalar meydana gelebilir. Örneğin, bir yedekleme uygulaması, bir anlık görüntü almak için Site Recovery çoğaltma ilkenize zamanlandığında VSS anlık görüntülerini alıyorsa.   
7. VM 'nin yüksek bir karmaşıklık oranı yaşıyor olup olmadığını denetleyin:
    - Hyper-V konağındaki performans sayaçlarını kullanarak Konuk VM 'Ler için günlük veri değişikliği oranını ölçebilirsiniz. Veri değişikliği oranını ölçmek için aşağıdaki sayacı etkinleştirin. VM dalgalanmasını almak için, VM disklerinde bu değerin bir örneğini 5-15 dakika boyunca toplayın.
        - Kategori: "Hyper-V sanal depolama cihazı"
        - Sayaç: "yazılan bayt/sn"</br>
        - Bu veri değişim hızı, VM 'nin veya uygulamalarının ne kadar meşgul olduğuna bağlı olarak yüksek düzeyde artar veya kalır.
        - Ortalama kaynak disk verileri karmaşıklığı Site Recovery için standart depolama için 2 MB/sn 'dir. [Daha fazla bilgi edinin](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - Ayrıca, [depolama ölçeklenebilirliği hedeflerini doğrulayabilirsiniz](../storage/common/scalability-targets-standard-account.md).
8. Linux tabanlı bir sunucu kullanıyorsanız, üzerinde uygulama tutarlılığını etkinleştirdiğinizden emin olun. [Daha fazla bilgi edinin](./site-recovery-faq.md#replication)
9. [Dağıtım planlayıcısı](hyper-v-deployment-planner-run.md)çalıştırın.
10. [Ağ](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) ve [depolama](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input)önerilerini gözden geçirin.


### <a name="vss-failing-inside-the-hyper-v-host"></a>Hyper-V konağı içinde VSS başarısız oldu

1. VSS hataları ve önerileri için olay günlüklerini denetleyin:
    - Hyper-v ana bilgisayar sunucusunda, **Olay Görüntüleyicisi**  >  **uygulamalar ve hizmetler günlükleri**  >  **Microsoft**  >  **Windows**  >  **Hyper-v**  >  **Yöneticisi**' nde Hyper-v Yöneticisi olay günlüğünü açın.
    - Uygulamayla tutarlı anlık görüntü başarısızlıklarını belirten herhangi bir olay olup olmadığını doğrulayın.
    - Tipik bir hata: "Hyper-V, ' XYZ ' sanal makinesi için VSS anlık görüntü kümesi oluşturamadı: yazıcı geçici olmayan bir hatayla karşılaştı. Hizmet yanıt vermezse VSS hizmetini yeniden başlatmak sorunları çözebilir. "

2. VM için VSS anlık görüntüleri oluşturmak için, Hyper-V tümleştirme hizmetlerinin VM 'de yüklü olduğundan ve yedekleme (VSS) tümleştirme hizmetinin etkin olduğundan emin olun.
    - Tümleştirme Hizmetleri VSS hizmeti/Daemon 'ları 'nin konukta çalıştığından ve bir **Tamam** durumunda olduğundan emin olun.
    - Bunu, **Get-VMIntegrationService-VMName \<VMName> -Name VSS** komutunu kullanarak Hyper-V konağındaki yükseltilmiş bir PowerShell oturumundan kontrol edebilirsiniz. Ayrıca Konuk VM 'de oturum açarak bu bilgileri alabilirsiniz. [Daha fazla bilgi edinin](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).
    - VM 'deki yedekleme/VSS tümleştirme hizmetlerinin çalıştığından ve sağlıklı durumda olduğundan emin olun. Aksi takdirde, bu hizmetleri ve Hyper-v ana bilgisayar sunucusunda Hyper-V Birim gölge kopyası istek sahibi hizmetini yeniden başlatın.

### <a name="common-errors"></a>Sık karşılaşılan hatalar

**Hata kodu** | **İleti** | **Ayrıntılar**
--- | --- | ---
**0x800700EA** | "Hyper-V sanal makine için VSS anlık görüntü kümesini oluşturamadı: daha fazla veri var. (0x800700EA). Yedekleme işlemi devam ediyorsa VSS anlık görüntü kümesi oluşturma işlemi başarısız olabilir.<br/><br/> Sanal makine için çoğaltma işlemi başarısız oldu: daha fazla veri var. " | VM 'nizin etkin dinamik disk olup olmadığını denetleyin. Bu özellik desteklenmez.
**0x80070032** | "Hyper-V Birim gölge kopyası Istek sahibi, Hyper-V tarafından beklenen sürümle eşleşmediğinden <./VMname> sanal makinesine bağlanamadı | En son Windows güncelleştirmelerinin yüklenip yüklenmediğini denetleyin.<br/><br/> Tümleştirme Hizmetleri 'nin en son sürümüne [yükseltin](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) .



## <a name="collect-replication-logs"></a>Çoğaltma günlüklerini topla

Tüm Hyper-V çoğaltma olayları, **uygulama ve hizmet günlükleri**  >  **Microsoft**  >  **Windows**'ta bulunan Hyper-V-VMMS\Admin günlüğüne kaydedilir. Ayrıca, Hyper-V sanal makine yönetimi hizmeti için bir analitik günlüğü aşağıdaki şekilde etkinleştirebilirsiniz:

1. Analitik ve hata ayıklama günlüklerini Olay Görüntüleyicisi görüntülenebilir yapın. Günlükleri kullanılabilir hale getirmek için, Olay Görüntüleyicisi **Görünüm**  >  **analitik ve hata ayıklama günlüklerini göster**' e tıklayın. Analitik günlük, **Hyper-V-VMMS** altında görünür.
2. **Eylemler** bölmesinde **günlüğü etkinleştir**' e tıklayın. 

    ![Günlüğü etkinleştir](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. Etkinleştirildikten sonra, **veri toplayıcı kümeleri** altında **olay Izleme oturumu** olarak **performans izleyicisinde** görüntülenir. 
4. Toplanan bilgileri görüntülemek için, günlüğü devre dışı bırakarak izleme oturumunu durdurun. Ardından, günlüğü kaydedin ve Olay Görüntüleyicisi ' de yeniden açın veya diğer araçları kullanarak gerekli şekilde dönüştürün.


### <a name="event-log-locations"></a>Olay günlüğü konumları

**Olay günlüğü** | **Ayrıntılar** |
--- | ---
**Uygulamalar ve hizmet günlükleri/Microsoft/VirtualMachineManager/sunucu/yönetici** (VMM sunucusu) | VMM sorunlarını gidermek için günlüklere bakın.
**Uygulamalar ve hizmet günlükleri/MicrosoftAzureRecoveryServices/çoğaltma** (Hyper-V konağı) | Microsoft Azure Kurtarma Hizmetleri aracı sorunlarını gidermek için günlüklere bakın. 
**Uygulamalar ve hizmet günlükleri/Microsoft/Azure Site Recovery/sağlayıcı/işletimsel** (Hyper-V konağı)| Microsoft Azure Site Recovery hizmet sorunlarını gidermek için günlüklere bakın.
**Uygulamalar ve hizmet günlükleri/Microsoft/Windows/Hyper-v-vmms/admin** (Hyper-v Konağı) | Hyper-V VM yönetimi sorunlarını gidermek için günlüklere bakın.

### <a name="log-collection-for-advanced-troubleshooting"></a>Gelişmiş sorun giderme için günlük koleksiyonu

Bu araçlar Gelişmiş sorun giderme konusunda yardımcı olabilir:

-   VMM için, [Destek Tanılama Platformu (SDP) aracını](https://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx)kullanarak Site Recovery günlük koleksiyonu gerçekleştirin.
-   VMM olmadan Hyper-V için [Bu aracı indirin](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab)ve günlükleri toplamak için Hyper-v konağında çalıştırın.