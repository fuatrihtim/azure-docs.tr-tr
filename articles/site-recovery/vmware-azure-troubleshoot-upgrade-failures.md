---
title: Microsoft Azure Site Recovery sağlayıcısının yükseltilmesiyle ilgili sorunları giderme
description: Microsoft Azure Site Recovery sağlayıcısı yükseltilirken oluşan yaygın sorunları çözün.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/10/2019
ms.author: raynew
ms.openlocfilehash: 5a6e4b415a9fe8ea80a84e415879df9d2f359478
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "84434378"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>Microsoft Azure Site Recovery Sağlayıcısını yükseltme hatalarını giderme

Bu makale, Microsoft Azure Site Recovery sağlayıcı yükseltmesi sırasında hatalara neden olabilecek sorunları çözmenize yardımcı olur.

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>Yükseltme, en son Site Recovery sağlayıcının zaten yüklü olduğunu bildiriyor

Microsoft Azure Site Recovery sağlayıcısını (DRA) yükseltirken, Birleşik kurulum yükseltmesi başarısız olur ve hata iletisini yayınlar:

Yazılımın daha yüksek bir sürümü zaten yüklü olduğundan yükseltme desteklenmiyor.

Yükseltmek için aşağıdaki adımları kullanın:

1. Birleşik kurulum Site Recovery Microsoft Azure indirin:
   1. [Azure Site Recovery makalesindeki hizmet güncelleştirmelerinin](service-updates-how-to.md#links-to-currently-supported-update-rollups) "Şu anda desteklenen güncelleştirme paketlerinin bağlantıları" bölümünde, yükseltmekte olduğunuz sağlayıcıyı seçin.
   2. Toplama sayfasında, **güncelleştirme bilgileri** bölümünü bulun ve Site Recovery birleşik kurulum Microsoft Azure Için güncelleştirme paketini indirin.

2. Bir komut istemi açın ve Birleşik kurulum dosyasını indirdiğiniz klasöre gidin. Aşağıdaki komutu kullanarak kurulum dosyalarını indirme dosyasından ayıklayın MicrosoftAzureSiteRecoveryUnifiedSetup.exe/q/x: &lt; ayıklanan dosyalar için klasör yolu &gt; .
    
    Örnek komut:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe/q/x: C:\temp\ayıklanmış

3. Komut isteminde, dosyaları ayıkladığınız klasöre gidin ve aşağıdaki yükleme komutlarını çalıştırın:
   
    CX_THIRDPARTY_SETUP.EXE/VERYSILENT/SUPPRESSMSGKUTULAR/NORESTART UCX_SERVER_SETUP.EXE/VERYSILENT/SUPPRESSMSGKUTULAR/NORESTART/UPGRADE

1. Birleşik kurulumu indirdiğiniz klasöre dönün ve yükseltme işleminin tamamlanabilmesi için MicrosoftAzureSiteRecoveryUnifiedSetup.exe çalıştırın. 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>Yeniden adlandırılmakta olan üçüncü taraf klasör nedeniyle yükseltme hatası

Yükseltmenin başarılı olması için 3. taraf klasörü yeniden adlandırılmamalıdır.

Bu sorunu çözmek için

1. Kayıt Defteri Düzenleyicisi 'Ni (regedit.exe) başlatın ve HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10 dalını açın.
1. `Build_Version`Anahtar değerini inceleyin. En son sürüme ayarlanırsa sürüm numarasını küçültün. Örneğin, en son sürüm 9,22 ise. \* ve `Build_Version` bu değere ayarlanan anahtar, 9,21 olarak azaltılmasını sağlar. \*
1. Birleşik kurulum Site Recovery en son Microsoft Azure indirin:
   1. [Azure Site Recovery makalesindeki hizmet güncelleştirmelerinin](service-updates-how-to.md#links-to-currently-supported-update-rollups) "Şu anda desteklenen güncelleştirme paketlerinin bağlantıları" bölümünde, yükseltmekte olduğunuz sağlayıcıyı seçin.
   2. Toplama sayfasında, **güncelleştirme bilgileri** bölümünü bulun ve Site Recovery birleşik kurulum Microsoft Azure Için güncelleştirme paketini indirin.
1. Bir komut istemi açın ve birleştirilmiş kurulum dosyasını indirdiğiniz klasöre gidin ve aşağıdaki komutu kullanarak kurulum dosyalarını indirmeden ayıklayın MicrosoftAzureSiteRecoveryUnifiedSetup.exe/q/x: &lt; ayıklanan dosyalar için klasör yolu &gt; .

    Örnek komut:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe/q/x: C:\temp\ayıklanmış

1. Komut isteminde, dosyaları ayıkladığınız klasöre gidin ve aşağıdaki yükleme komutlarını çalıştırın:
   
    CX_THIRDPARTY_SETUP.EXE/VERYSILENT/SUPPRESSMSGKUTULAR/NORESTART

1. Yüklemenin ilerlemesini izlemek için Görev Yöneticisi 'ni kullanın. CX_THIRDPARTY_SETUP.EXE işlemi artık görev yöneticisi 'nde görünür olmadığında, sonraki adıma geçin.
1. C:\üçüncü taraf olduğunu ve klasörün RRD kitaplıklarını içerdiğini doğrulayın.
1. Birleşik kurulumu indirdiğiniz klasöre dönün ve yükseltme işleminin tamamlanabilmesi için MicrosoftAzureSiteRecoveryUnifiedSetup.exe çalıştırın.

## <a name="upgrade-failure-due-to-master-target-installation-failure"></a>Ana hedef yükleme hatası nedeniyle yükseltme hatası

Microsoft Azure Site Recovery sağlayıcısı (DRA) yükseltilirken, ana hedef yüklemesi ' yükleme konumu yok ' hatası ile başarısız olur veya 1 GB boş alana sahip değildir ve/veya sabit bir sürücüde mevcut değildir.

Bunun nedeni, kayıt defteri anahtarındaki bir parametre için null değer olabilir. Sorunu çözmek için-

1. Kayıt Defteri Düzenleyicisi 'Ni (regedit.exe) başlatın ve HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\4 dalını açın.
1. ' InstallDirectory ' anahtar değerini inceleyin. Null ise, geçerli install dizin değerini ekleyin.
1. Benzer şekilde, kayıt defteri düzenleyicisinde HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5 dalını açın.
1. ' InstallDirectory ' anahtar değerini inceleyin ve geçerli install Directory değerini ekleyin.
1. Birleşik kurulum yükleyicisini yeniden çalıştırın.
