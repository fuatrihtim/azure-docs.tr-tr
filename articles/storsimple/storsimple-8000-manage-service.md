---
title: StorSimple Device Manager hizmetini Azure 'da dağıtma | Microsoft Docs
description: Oluşturma, silme, hizmetin geçirilmesi ve hizmet kayıt anahtarının yönetimi için gereken adımlar hakkında bilgi edinin.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: 66244bd8e24ff62be41df72f7a39c0ce0ed13135
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93360708"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>StorSimple 8000 serisi cihazlar için StorSimple Device Manager hizmetini dağıtma

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Genel Bakış

StorSimple Device Manager hizmeti Microsoft Azure ' de çalışır ve birden çok StorSimple cihaza bağlanır. Hizmeti oluşturduktan sonra, tek bir merkezi konumdan StorSimple Device Manager hizmetine bağlı tüm cihazları yönetmek için bunu kullanabilir, böylece yönetim yükünü en aza indirebilirsiniz.

Bu öğreticide, hizmetin oluşturulması, silinmesi, geçirilmesi ve hizmet kayıt anahtarının yönetimi için gereken adımlar açıklanmaktadır. Bu makalede yer alan bilgiler yalnızca StorSimple 8000 serisi cihazlara uygulanabilir. StorSimple Sanal dizileri hakkında daha fazla bilgi için, [StorSimple Sanal diziniz için bir storsimple Device Manager hizmeti dağıtma](storsimple-virtual-array-manage-service.md)konusuna gidin.

> [!NOTE]
> -  Azure portal güncelleştirme 5,0 veya üstünü çalıştıran cihazları destekler. Cihazınız güncel değilse, güncelleştirme 5 ' i hemen kurun. Daha fazla bilgi için [güncelleştirme 5](storsimple-8000-install-update-5.md)' e gidin. 
> - StorSimple Cloud Appliance (8010/8020) kullanıyorsanız, bir bulut gerecini güncelleştiremezsiniz. Güncelleştirme 5,0 ile yeni bir bulut gereci oluşturmak için en son yazılım sürümünü kullanın ve ardından yeni bulut gerecine yük devretmek. 
> - Güncelleştirme 4,0 veya önceki bir sürümü çalıştıran tüm cihazlar, daha az yönetim işlevselliğiyle karşılaşacaktır. 

## <a name="create-a-service"></a>Hizmet oluşturma
StorSimple Device Manager hizmeti oluşturmak için şunları yapmanız gerekir:

* Kurumsal Anlaşma bir abonelik
* Etkin bir Microsoft Azure depolama hesabı
* Erişim yönetimi için kullanılan faturalandırma bilgileri

Yalnızca bir Kurumsal Anlaşma aboneliğe izin verilir. Hizmeti oluştururken varsayılan bir depolama hesabı oluşturmayı da tercih edebilirsiniz.

Tek bir hizmet birden çok cihazı yönetebilir. Ancak, bir cihaz birden fazla hizmete yayılamaz. Büyük bir kuruluşun farklı abonelikler, kuruluşlar veya hatta dağıtım konumları ile çalışması için birden çok hizmet örneği olabilir. 

> [!NOTE]
> StorSimple 8000 serisi cihazlarını ve StorSimple Sanal dizilerini yönetmek için StorSimple Device Manager hizmetinin ayrı örneklerine ihtiyacınız vardır.

Bir hizmet oluşturmak için aşağıdaki adımları gerçekleştirin.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


Her StorSimple Device Manager hizmeti için aşağıdaki öznitelikler mevcuttur:

* **Name** : storsimple Device Manager hizmetinize atanan ad. **Hizmet adı, hizmet oluşturulduktan sonra değiştirilemez. Bu Ayrıca, Azure portal yeniden adlandırılamayan cihazlar, birimler, birim kapsayıcıları ve yedekleme ilkeleri gibi diğer varlıklar için de geçerlidir.**
* **Durum** : **etkin**, **oluşturma** veya **çevrimiçi** olabilen hizmetin durumu.
* **Location** : StorSimple cihazının dağıtılacağı coğrafi konum.
* **Abonelik** – hizmetinizden ilişkilendirilen faturalandırma aboneliği.

## <a name="delete-a-service"></a>Hizmet silme

Bir hizmeti silmeden önce, bağlı hiçbir cihazın bu uygulamayı kullandığından emin olun. Hizmet kullanımda ise, bağlı cihazları devre dışı bırakın. Devre dışı bırakma işlemi, cihaz ve hizmet arasındaki bağlantıyı bırakır, ancak buluttaki cihaz verilerini korur.

> [!IMPORTANT]
> Bir hizmet silindikten sonra işlem tersine çevrilemez. Hizmeti kullanan herhangi bir cihazın, başka bir hizmetle kullanılabilmesi için önce fabrika varsayılanlarına sıfırlanması gerekir. Bu senaryoda, cihazdaki yerel verilerin yanı sıra yapılandırma de kaybedilir.

Bir hizmeti silmek için aşağıdaki adımları gerçekleştirin.

### <a name="to-delete-a-service"></a>Bir hizmeti silmek için

1. Silmek istediğiniz hizmeti arayın. **Kaynaklar** simgesine tıklayın ve ardından arama yapmak için uygun koşulları girin. Arama sonuçlarında, silmek istediğiniz hizmete tıklayın.

    ![Silinecek arama hizmeti](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Bu sizi StorSimple Device Manager hizmeti dikey penceresine götürür. **Sil**'e tıklayın.

    ![Hizmeti Sil](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. Onay bildiriminde **Evet** ' e tıklayın. Hizmetin silinmesi birkaç dakika sürebilir.

    ![Silme işlemini onaylama](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>Hizmet kayıt anahtarı alma

Bir hizmeti başarıyla oluşturduktan sonra StorSimple cihazınızı hizmete kaydetmeniz gerekir. İlk StorSimple cihazınızı kaydetmek için hizmet kayıt anahtarına ihtiyacınız olacaktır. Mevcut bir StorSimple hizmetine ek cihazları kaydetmek için, kayıt anahtarı ve hizmet veri şifreleme anahtarı (kayıt sırasında ilk cihazda oluşturulur) gereklidir. Hizmet veri şifreleme anahtarı hakkında daha fazla bilgi için bkz. [StorSimple Security](storsimple-8000-security.md). StorSimple Device Manager dikey penceresindeki **anahtarlara** erişerek kayıt anahtarını alabilirsiniz.

Hizmet kayıt anahtarını almak için aşağıdaki adımları gerçekleştirin.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

Hizmet kayıt anahtarını güvenli bir konumda saklayın. Bu hizmete ek cihaz kaydetmek için bu anahtara ve hizmet veri şifreleme anahtarına ihtiyacınız olacak. Hizmet kayıt anahtarını aldıktan sonra cihazınızı StorSimple için Windows PowerShell arabirimi aracılığıyla yapılandırmanız gerekir.

Bu kayıt anahtarını kullanma hakkında ayrıntılı bilgi için bkz. [3. Adım: cihazı yapılandırma ve StorSimple için Windows PowerShell aracılığıyla kaydetme](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>Hizmet kayıt anahtarını yeniden üret
Anahtar dönüşü yapmanız gerekiyorsa veya hizmet yöneticileri listesi değiştiyse, bir hizmet kayıt anahtarını yeniden oluşturmanız gerekir. Anahtarı yeniden oluşturduğunuzda yeni anahtar yalnızca sonraki cihazları kaydetmek için kullanılır. Zaten kayıtlı olan cihazlar bu işlemden etkilenmez.

Hizmet kayıt anahtarını yeniden oluşturmak için aşağıdaki adımları gerçekleştirin.

### <a name="to-regenerate-the-service-registration-key"></a>Hizmet kayıt anahtarını yeniden oluşturmak için
1. **StorSimple Device Manager** dikey penceresinde **&gt; Yönetim** **anahtarlar**' a gidin.
    
    ![Anahtarlar dikey penceresine git](./media/storsimple-8000-manage-service/regenregkey2.png)

2. **Anahtarlar** dikey penceresinde yeniden **Oluştur**' a tıklayın.

    ![Yeniden oluştur 'a tıklayın](./media/storsimple-8000-manage-service/regenregkey3.png)
3. **Hizmet kayıt anahtarını yeniden oluştur** dikey penceresinde, anahtarlar yeniden üretildiğinde gereken eylemi gözden geçirin. Bu hizmete kayıtlı tüm sonraki cihazlar yeni kayıt anahtarını kullanır. Onaylamak için yeniden **Oluştur** ' a tıklayın. Yeniden oluşturma işlemi tamamlandıktan sonra bilgilendirilirsiniz.

    ![Yeniden oluşturmayı Onayla](./media/storsimple-8000-manage-service/regenregkey4.png)

4. Yeni bir hizmet kayıt anahtarı görüntülenir.

5. Bu anahtarı kopyalayın ve yeni cihazları bu hizmete kaydetmek için kaydedin.



## <a name="change-the-service-data-encryption-key"></a>Hizmet veri şifreleme anahtarını değiştirme
Hizmet veri şifreleme anahtarları, StorSimple Yöneticisi hizmetinizden StorSimple cihazına gönderilen depolama hesabı kimlik bilgileri gibi gizli müşteri verilerini şifrelemek için kullanılır. BT kuruluşunuzda depolama cihazlarında bir anahtar döndürme ilkesi varsa, bu anahtarları düzenli aralıklarla değiştirmeniz gerekir. Anahtar değişikliği işlemi, StorSimple Yöneticisi hizmeti tarafından yönetilen tek bir cihaz veya birden çok cihaz olmasına bağlı olarak biraz farklı olabilir. Daha fazla bilgi için, [StorSimple güvenlik ve veri koruma](storsimple-8000-security.md)bölümüne gidin.

Hizmet veri şifreleme anahtarını değiştirmek 3 adımlı bir işlemdir:

1. Azure Resource Manager için Windows PowerShell betiklerini kullanarak hizmet veri şifreleme anahtarını değiştirmeye yönelik bir cihaz yetkilendirin.
2. StorSimple için Windows PowerShell kullanarak, hizmet veri şifreleme anahtarı değişikliğini başlatın.
3. Birden fazla StorSimple cihazınız varsa, diğer cihazlarda hizmet veri şifreleme anahtarını güncelleştirin.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>1. Adım: bir cihaza hizmet veri şifreleme anahtarını değiştirme yetkisi vermek için Windows PowerShell betiği kullanma
Genellikle, Cihaz Yöneticisi hizmet yöneticisinin hizmet veri şifreleme anahtarlarını değiştirmek için bir cihazı yetkilendirmesini ister. Hizmet Yöneticisi, daha sonra cihazı anahtarı değiştirecek şekilde yetkilendirecek.

Bu adım Azure Resource Manager tabanlı betik kullanılarak gerçekleştirilir. Hizmet Yöneticisi yetkilendirilebilecek bir cihaz seçebilir. Daha sonra cihaz, hizmet veri şifreleme anahtarı değiştirme işlemini başlatmaya yetkilidir. 

Betiği kullanma hakkında daha fazla bilgi için [Authorize-ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1) gidin.

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Hizmet veri şifreleme anahtarlarını değiştirmek için hangi cihazlara yetki atanabilir?
Bir cihazın hizmet veri şifreleme anahtarı değişikliklerini başlatma yetkisine sahip olabilmesi için aşağıdaki ölçütleri karşılaması gerekir:

* Hizmet veri şifreleme anahtarı değişiklik yetkilendirmesi için uygun olması için cihazın çevrimiçi olması gerekir.
* Anahtar değişikliği başlatılmamış 30 dakika sonra aynı cihazı yetkilendirebilirsiniz.
* Anahtar değişikliğinin önceden yetkilendirilmiş cihaz tarafından başlatılmadığından farklı bir cihaza yetki verebilirsiniz. Yeni cihaz yetkilendirildikten sonra, eski cihaz değişikliği başlatamıyor.
* Hizmet veri şifreleme anahtarı geçişi devam ederken bir cihazı yetkilendiremezsiniz.
* Bir cihazı, hizmete kayıtlı cihazların bazıları, diğer kullanıcılar olmadığında şifrelemeyi geri alındığında yetkilendirebilirsiniz. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>2. Adım: hizmet veri şifreleme anahtarı değişikliğini başlatmak için StorSimple için Windows PowerShell kullanma
Bu adım yetkili StorSimple cihazında StorSimple için Windows PowerShell arabiriminde gerçekleştirilir.

> [!NOTE]
> Anahtar geçişi tamamlanana kadar StorSimple Manager hizmetinizin Azure portal hiçbir işlem gerçekleştirilemez.


Windows PowerShell arabirimine bağlanmak için cihaz seri konsolunu kullanıyorsanız aşağıdaki adımları gerçekleştirin.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>Hizmet veri şifreleme anahtarı değişikliğini başlatmak için
1. Tam erişimle oturum açmak için 1 seçeneğini belirleyin.
2. Komut istemine şunları yazın:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Cmdlet başarıyla tamamlandıktan sonra yeni bir hizmet veri şifreleme anahtarı alacaksınız. Bu işlemin 3. adımında kullanılmak üzere bu anahtarı kopyalayın ve kaydedin. Bu anahtar, StorSimple Yöneticisi hizmetine kayıtlı tüm kalan cihazları güncelleştirmek için kullanılacaktır.
   
   > [!NOTE]
   > Bu işlem, StorSimple cihazını yetkilendirmek için dört saat içinde başlatılmalıdır.
   > 
   > 
   
   Bu yeni anahtar daha sonra hizmete kaydedilen tüm cihazlara gönderilmek üzere hizmete gönderilir. Daha sonra hizmet panosunda bir uyarı görüntülenir. Hizmet, kayıtlı cihazlardaki tüm işlemleri devre dışı bırakacak ve cihaz yöneticisinin diğer cihazlarda hizmet veri şifreleme anahtarını güncelleştirmesi gerekir. Ancak, g/ç (buluta veri gönderen konaklar) kesintiye uğramaz.
   
   Hizmetinize kayıtlı tek bir cihazınız varsa, geçiş işlemi tamamlanır ve sonraki adımı atlayabilirsiniz. Hizmetinize kayıtlı birden çok cihazınız varsa adım 3 ' e geçin.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>3. Adım: diğer StorSimple cihazlarında hizmet veri şifreleme anahtarını güncelleştirme
StorSimple Yöneticisi hizmetinize kayıtlı birden çok cihazınız varsa, bu adımların StorSimple cihazınızın Windows PowerShell arabiriminde gerçekleştirilmesi gerekir. 2. adımda elde ettiğiniz anahtarın, StorSimple Yöneticisi hizmetine kayıtlı olan tüm kalan StorSimple cihazını güncelleştirmek için kullanılması gerekir.

Cihazınızda hizmet veri şifrelemesini güncelleştirmek için aşağıdaki adımları gerçekleştirin.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>Fiziksel cihazlarda hizmet veri şifreleme anahtarını güncelleştirmek için
1. Konsola bağlanmak için StorSimple için Windows PowerShell kullanın. Tam erişimle oturum açmak için 1 seçeneğini belirleyin.
2. Komut isteminde şunu yazın:  `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. 2. adımda elde ettiğiniz hizmet veri şifreleme anahtarını sağlayın [: hizmet verileri şifreleme anahtarı değişikliğini başlatmak için StorSimple için Windows PowerShell kullanın](#to-initiate-the-service-data-encryption-key-change).

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>Tüm 8010/8020 bulut gereçlerinde hizmet veri şifreleme anahtarını güncelleştirmek için
1. [Update-CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) PowerShell betiğini indirip kurun. 
2. PowerShell 'i açın ve komut isteminde şunu yazın:  `Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Bu betik, hizmet veri şifreleme anahtarının, cihaz yöneticisi altındaki tüm 8010/8020 bulut gereçlerinde ayarlanmış olmasını sağlayacaktır.

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>Güncelleştirme 5,0 ' den önceki sürümleri çalıştıran cihazlarda desteklenen işlemler
Azure portal, yalnızca güncelleştirme 5,0 ve üstünü çalıştıran StorSimple cihazları desteklenir. Daha eski sürümleri çalıştıran cihazların desteği sınırlıdır. Azure portal geçirdikten sonra, güncelleştirme 5,0 ' den önceki sürümleri çalıştıran cihazlarda hangi işlemlerin desteklendiğini anlamak için aşağıdaki tabloyu kullanın.

| İşlem                                                                                                                       | Desteklenir      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Cihaz kaydetme                                                                                                               | Yes            |
| Genel, ağ ve güvenlik gibi cihaz ayarlarını yapılandırma                                                                | Yes            |
| Güncelleştirmeleri tarama, indirme ve yükleme                                                                                             | Yes            |
| Cihazı devre dışı bırak                                                                                                               | Yes            |
| Cihazı silme                                                                                                                   | Yes            |
| Birim kapsayıcısı oluşturma, değiştirme ve silme                                                                                   | No             |
| Birim oluşturma, değiştirme ve silme                                                                                             | No             |
| Yedekleme ilkesi oluşturma, değiştirme ve silme                                                                                      | No             |
| El ile yedekleme yapın                                                                                                            | No             |
| Zamanlanmış bir yedekleme yapın                                                                                                         | Uygulanamaz |
| Yedek kümesi 'ten geri yükleme                                                                                                        | No             |
| Güncelleştirme 3,0 ve üstünü çalıştıran bir cihaza Kopyala <br> Kaynak cihaz güncelleştirme 3,0 ' den önceki sürümü çalıştırıyor.                                | Yes            |
| Güncelleştirme 3,0 ' den önceki sürümleri çalıştıran bir cihaza Kopyala                                                                          | No             |
| Kaynak cihaz olarak yük devretme <br> (güncelleştirme 3,0 ' den önceki sürümü çalıştıran bir cihazdan güncelleştirme 3,0 ve üzeri sürümlerini çalıştıran bir cihaza)                                                               | Yes            |
| Hedef cihaz olarak yük devretme <br> (güncelleştirme 3,0 ' dan önce yazılım sürümü çalıştıran bir cihaza)                                                                                   | No             |
| Bir uyarıyı Temizleme                                                                                                                  | Yes            |
| Klasik portalda oluşturulan yedekleme ilkelerini, yedekleme kataloğunu, birimleri, birim kapsayıcılarını, izleme grafiklerini, işleri ve uyarıları görüntüleme | Yes            |
| Cihaz denetleyicilerini aç ve Kapat                                                                                              | Evet            |


## <a name="next-steps"></a>Sonraki adımlar
* [StorSimple dağıtım süreci](storsimple-8000-deployment-walkthrough-u2.md)hakkında daha fazla bilgi edinin.
* [StorSimple depolama hesabınızı yönetme](storsimple-8000-manage-storage-accounts.md)hakkında daha fazla bilgi edinin.
* StorSimple [cihazınızı yönetmek Için storsimple Device Manager hizmetini kullanma](storsimple-8000-manager-service-administration.md)hakkında daha fazla bilgi edinin.
