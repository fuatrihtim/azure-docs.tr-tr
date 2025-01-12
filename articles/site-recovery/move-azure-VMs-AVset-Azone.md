---
title: Azure Site Recovery kullanarak VM 'Leri kullanılabilirlik alanları içeren bir Azure bölgesine taşıyın
description: VM 'Leri Site Recovery farklı bir bölgedeki kullanılabilirlik bölgesine nasıl taşıyacağınızı öğrenin
services: site-recovery
author: sideeksh
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: sideeksh
ms.custom: MVC
ms.openlocfilehash: 8224ae4a48bb4915492240c414b90edb86a4c258
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93393141"
---
# <a name="move-azure-vms-into-availability-zones"></a>Azure VM 'lerini Kullanılabilirlik Alanları içine taşıyın

Bu makalelerde, Azure VM 'lerinin farklı bir bölgedeki kullanılabilirlik bölgesine nasıl taşınacağı açıklanır. Aynı bölgedeki farklı bir bölgeye taşımak istiyorsanız, [Bu makaleyi gözden geçirin](./azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).


Azure 'daki Kullanılabilirlik Alanları, veri merkezi hatalarından uygulamalarınızın ve verilerinizin korunmasına yardımcı olur. Her Kullanılabilirlik Alanı bağımsız enerji, soğutma ve ağ kaynaklarıyla donatılmış bir veya daha fazla veri merkezinden oluşur. Dayanıklılık sağlamak için, tüm etkin bölgelerde en az üç ayrı bölge vardır. Bir bölgedeki Kullanılabilirlik Alanları fiziksel ayrımı, uygulamaların ve verilerin veri merkezi hatalarından korunmasını sağlar. Azure, Kullanılabilirlik Alanları ile sanal makinelerin (VM) çalışma süresi için% 99,99 ' lik bir hizmet düzeyi sözleşmesi (SLA) sunmaktadır. Kullanılabilirlik Alanları, [kullanılabilirlik alanları destekleyen bölgelerde](../availability-zones/az-region.md)belirtildiği gibi, seçme bölgelerinde desteklenir.

Sanal makinelerinizin belirli bir bölgeye *tek bir örnek* olarak dağıtıldığı ve bu VM 'Leri bir kullanılabilirlik bölgesine taşıyarak kullanılabilirliği artırmak istediğiniz bir senaryoda, Azure Site Recovery kullanarak bunu yapabilirsiniz. Bu eylem, daha sonra kategorilere ayrılabilir:

- Tek örnekli VM 'Leri hedef bölgede Kullanılabilirlik Alanları taşıyın
- Bir kullanılabilirlik kümesindeki VM 'Leri hedef bölgedeki Kullanılabilirlik Alanları taşıyın

> [!IMPORTANT]
> Azure VM 'lerini farklı bir bölgedeki bir kullanılabilirlik bölgesine taşımak için artık [Azure Kaynak taşıyıcısı](../resource-mover/move-region-availability-zone.md)kullanmanızı öneririz. Kaynak taşıyıcısı genel önizlemede bulunur ve şunları sağlar:
> - Kaynakları bölgeler arasında taşımak için tek bir hub.
> - Daha az taşıma süresi ve karmaşıklığı. İhtiyacınız olan her şey tek bir konumda.
> - Farklı türlerde Azure kaynaklarını taşımak için basit ve tutarlı bir deneyim.
> - Taşımak istediğiniz kaynaklar arasında bağımlılıkları belirlemenin kolay bir yolu. Bu, ilgili kaynakları bir arada taşımanızı sağlar, böylece taşıma işleminden sonra her şeyin hedef bölgede beklendiği gibi çalışması gerekir.
> - Taşıma işleminden sonra silmek istiyorsanız kaynak bölgedeki kaynakların otomatik olarak temizlenmesi.
> - Edici. Bir taşımayı deneyebilir ve tam bir taşıma yapmak istemiyorsanız bu uygulamayı atabilirsiniz.



## <a name="check-prerequisites"></a>Önkoşulları denetleme

- Hedef bölgenin [kullanılabilirlik alanları için desteğe](../availability-zones/az-region.md)sahip olup olmadığını denetleyin. [Kaynak bölgesi/hedef bölge bileşiminin desteklenip desteklenmediğini](./azure-to-azure-support-matrix.md#region-support)denetleyin. Hedef bölgede bilinçli bir karar alın.
- [Senaryo mimarisini ve bileşenlerini ](azure-to-azure-architecture.md) anladığınızdan emin olun.
- [Destek sınırlamaları ve gereksinimleri](azure-to-azure-support-matrix.md) konusunu inceleyin.
- Hesap izinlerini denetleyin. Ücretsiz Azure hesabınızı oluşturduysanız aboneliğinizin yöneticisi olursunuz. Abonelik yöneticisi değilseniz, ihtiyaç duyduğunuz izinleri atamak için yönetici ile çalışın. Bir VM için çoğaltmayı etkinleştirmek ve sonunda Azure Site Recovery kullanarak verileri hedefe kopyalamak için, şunları yapmanız gerekir:

    1. Azure kaynaklarında VM oluşturma izni. *Sanal makine katılımcısı* yerleşik rolü bu izinlere sahiptir ve şunları içerir:
        - Seçilen kaynak grubunda sanal makine oluşturma izni
        - Seçilen sanal ağda sanal makine oluşturma izni
        - Seçilen depolama hesabına yazma izni

    2. Azure Site Recovery görevlerini yönetme izni. *Site Recovery katkıda* bulunan rolü, bir kurtarma hizmetleri kasasındaki Site Recovery eylemleri yönetmek için gereken tüm izinlere sahiptir.

## <a name="prepare-the-source-vms"></a>Kaynak VM 'Leri hazırlama

1. Site Recovery kullanarak bunları bir kullanılabilirlik bölgesine taşımak istiyorsanız sanal makinelerinizin yönetilen diskleri kullanması gerekir. Yönetilmeyen diskleri kullanan mevcut Windows VM 'lerini yönetilen diskleri kullanmak üzere dönüştürebilirsiniz. [Windows sanal makinesini yönetilmeyen disklerden yönetilen disklere dönüştürme](../virtual-machines/windows/convert-unmanaged-to-managed-disks.md)bölümündeki adımları izleyin. Kullanılabilirlik kümesinin *yönetilen* olarak yapılandırıldığından emin olun.
2. Taşımak istediğiniz Azure VM 'lerinde en son kök sertifikaların mevcut olup olmadığını denetleyin. En son kök sertifikalar mevcut değilse, hedef bölgeye veri kopyalama güvenlik kısıtlamaları nedeniyle etkinleştirilemez.

3. Windows VM’ler için, güvenilir kök sertifikaların tamamı makinede mevcut olacak şekilde sanal makineye en son Windows güncelleştirmelerinin tümünü yükleyin. Bağlantısı kesilmiş bir ortamda, kuruluşunuz için standart Windows Update ve sertifika güncelleştirme süreçlerini izleyin.

4. Linux sanal makineleri için, VM 'deki en son güvenilen kök sertifikaları ve sertifika iptal listesini almak için Linux dağıtıcısının sunduğu yönergeleri izleyin.
5. Taşımak istediğiniz VM 'Ler için ağ bağlantısını denetlemek üzere bir kimlik doğrulama proxy 'si kullandığınızdan emin olun.

6. [VM 'ler için giden bağlantı gereksinimlerini](azure-to-azure-tutorial-enable-replication.md#set-up-vm-connectivity)doğrulayın.

7. Yük dengeleyiciler, NSG 'ler ve genel IP dahil olmak üzere kaynak ağ düzeni ve şu anda doğrulama için kullandığınız kaynakları belirler.

## <a name="prepare-the-target-region"></a>Hedef bölgeyi hazırlama

1. Azure aboneliğinizin olağanüstü durum kurtarma için kullanılan hedef bölgede VM 'Ler oluşturmanıza izin olup olmadığını denetleyin. Gerekirse, gerekli kotayı etkinleştirmek için desteğe başvurun.

2. Aboneliğinizin, kaynak VM’lerinize uygun boyutlardaki VM’leri desteklemek için yeterli kaynakları içerdiğinden emin olun. Verileri hedefe kopyalamak için Site Recovery kullanırsanız, hedef VM için aynı boyutu veya mümkün olan en yakın boyutu seçer.

3. Kaynak ağ düzeninde tanımlanan her bileşen için bir hedef kaynak oluşturun. Bu eylem, hedef bölgeye kestikten sonra sanal makinelerinizin kaynakta bulunan tüm işlevsellik ve özelliklere sahip olmasını sağlar.

    > [!NOTE]
    > Azure Site Recovery, kaynak VM için çoğaltmayı etkinleştirdiğinizde otomatik olarak bir sanal ağ ve depolama hesabı bulur ve oluşturur. Ayrıca, bu kaynakları önceden oluşturup, çoğaltmayı etkinleştirme adımının bir parçası olarak VM 'ye atayabilirsiniz. Ancak, daha sonra bahsedilen diğer kaynaklar için, bunları hedef bölgede el ile oluşturmanız gerekir.

     Aşağıdaki belgeler, kaynak VM yapılandırmasına bağlı olarak, sizin için uygun olan en yaygın kullanılan ağ kaynaklarını oluşturmayı söyler.

    - [Ağ güvenlik grupları](../virtual-network/manage-network-security-group.md)
    - [Yük dengeleyiciler](../load-balancer/index.yml)
    - [Genel IP](../virtual-network/virtual-network-public-ip-address.md)
    
   Diğer ağ bileşenleri için ağ [belgelerine](../index.yml?pivot=products&panel=network)bakın.

    > [!IMPORTANT]
    > Hedefte bölge yedekli yük dengeleyici kullandığınızdan emin olun. [Standart Load Balancer ve kullanılabilirlik alanları](../load-balancer/load-balancer-standard-availability-zones.md)daha fazla bilgi edinebilirsiniz.

4. Hedef bölgeye geçmeden önce yapılandırmayı test etmek istiyorsanız hedef bölgede el ile [bir üretim dışı ağ oluşturun](../virtual-network/quick-create-portal.md) . Üretim ortamında en az girişim oluşmasına neden olduğundan bu yaklaşım önerilir.

## <a name="enable-replication"></a>Çoğaltmayı etkinleştirme
Aşağıdaki adımlar, verilerin hedef bölgeye çoğaltılmasını sağlamak için Azure Site Recovery kullandığınızda size rehberlik eder. Bu işlem, sonunda Kullanılabilirlik Alanları.

> [!NOTE]
> Bu adımlar tek bir sanal makine içindir. Aynı şekilde birden çok VM 'ye genişletebilirsiniz. Kurtarma Hizmetleri kasasına gidin, **+ Çoğalt**' ı seçin ve Ilgili VM 'leri birlikte seçin.

1. Azure portal **sanal makineler**' i seçin ve kullanılabilirlik alanları taşımak istediğiniz VM 'yi seçin.
2. **İşlemler** menüsünden **Olağanüstü durum kurtarma** seçeneğini belirleyin.
3. **Olağanüstü durumdan kurtarma yapılandırma** > **Hedef bölge** bölümünde, çoğaltma yapacağınız hedef bölgeyi seçin. Bu bölgenin Kullanılabilirlik Alanları [desteklediğinden](../availability-zones/az-region.md) emin olun.
4. **İleri ' yi seçin: Gelişmiş ayarlar**.
5. Hedef abonelik, hedef VM kaynak grubu ve sanal ağ için uygun değerleri seçin.
6. **Kullanılabilirlik** bölümünde, sanal makineyi taşımak Istediğiniz kullanılabilirlik bölgesini seçin. 
   > [!NOTE]
   > Kullanılabilirlik kümesi veya kullanılabilirlik bölgesi seçeneğini görmüyorsanız [önkoşulların](#prepare-the-source-vms) karşılandığından ve kaynak VM 'lerin [hazırlanması](#prepare-the-source-vms) tamamlandığından emin olun.
  

7. **Çoğaltmayı etkinleştir**' i seçin. Bu eylem VM için çoğaltmayı etkinleştirmek üzere bir iş başlatır.

## <a name="check-settings"></a>Ayarları denetle

Çoğaltma işlemi bittikten sonra, çoğaltma durumunu denetleyebilir, çoğaltma ayarlarını değiştirebilir ve dağıtımı test edebilirsiniz.

1. VM menüsünden **Olağanüstü durum kurtarma** seçeneğini belirleyin.
2. Çoğaltma durumunu, oluşturulan kurtarma noktalarını ve kaynak ve haritadaki hedef bölgeleri kontrol edebilirsiniz.


## <a name="test-the-configuration"></a>Yapılandırmayı test edin

1. Sanal makine menüsünde  **olağanüstü durum kurtarma**' yı seçin.
2. **Yük devretme testi** simgesini seçin.
3. Yük **devretme testi** bölümünde, yük devretme için kullanılacak bir kurtarma noktası seçin:

   - **En son işlenen**: VM’nin yükünü, Site Recovery hizmeti tarafından işlenen en son kurtarma noktasına devreder. Zaman damgası gösterilir. Bu seçenekle veri işlemeye zaman harcanmadığından düşük kurtarma süresi hedefi (RTO) elde edilir.
   - **Uygulamayla tutarlı olan son**: Bu seçenek, tüm VM’lerin yükünü uygulamayla tutarlı olan en son kurtarma noktasına devreder. Zaman damgası gösterilir.
   - **Özel**: Herhangi bir kurtarma noktası seçin.

3. Yapılandırmayı sınamak için Azure VM 'lerini taşımak istediğiniz test hedefi Azure sanal ağını seçin. 

    > [!IMPORTANT]
    > VM 'lerinizi taşımak istediğiniz hedef bölgede üretim ağı değil, test hatası için ayrı bir Azure VM ağı kullanmanızı öneririz.

4. Taşımayı test etmeye başlamak için **Tamam**' ı seçin. İlerlemeyi izlemek için VM 'yi seçerek özelliklerini açın. Ya da, **Yük devretme testi** işini kasa adı > **Ayarlar**  >  **işler**  >  **Site Recovery işler**' de seçebilirsiniz.
5. Yük devretme bittikten sonra, çoğaltma Azure VM, Azure portalı > **Sanal Makineler** bölümünde görünür. VM’nin çalıştığından, uygun şekilde boyutlandırıldığından ve uygun ağa bağlı olduğundan emin olun.
6. Taşımayı test etme işleminin bir parçası olarak oluşturulan VM 'yi silmek istiyorsanız, çoğaltılan öğedeki **Yük devretme testini temizle** ' yi seçin. **Notlar**' da, testle ilişkili tüm gözlemlerinizi kaydedin ve kaydedin.

## <a name="move-to-the-target-region-and-confirm"></a>Hedef bölgeye gidin ve onaylayın

1.  Sanal makine menüsünde  **olağanüstü durum kurtarma**' yı seçin.
2. **Yük devretme** simgesini seçin.
3. **Yük devretme** bölümünde **En geç** seçeneğini belirleyin. 
4. **Yük devretmeyi başlatmadan önce makineyi kapatın** seçeneğini belirleyin. Site Recovery, yük devretmeyi tetiklemeden önce kaynak sanal makineyi kapatmaya çalışır. Kapatma işlemi başarısız olsa bile yük devretme devam eder. **İşler** sayfasında yük devretme ilerlemesini izleyebilirsiniz. 
5. İş bittikten sonra, sanal makinenin hedef Azure bölgesinde beklenen şekilde göründüğünden emin olun.
6. **Çoğaltılan öğeler** bölümünde VM’ye sağ tıklayıp **Yürüt**’e tıklayın. Bu, hedef bölgeye taşıma işlemini tamamlar. Tamamlama işi bitene kadar bekleyin.

## <a name="discard-the-resource-in-the-source-region"></a>Kaynak bölgedeki kaynağı at

VM 'ye gidin. **Çoğaltmayı devre dışı bırak** seçeneğini belirleyin. Bu eylem, VM için verileri kopyalama işlemini sonlandırır.  

> [!IMPORTANT]
> Taşıma işleminden sonra Site Recovery çoğaltma için ücretlendirmemek için önceki adımı yapın. Kaynak çoğaltma ayarları otomatik olarak temizlenir. Çoğaltmanın bir parçası olarak yüklenen Site Recovery uzantısının kaldırılmadığını ve el ile kaldırılması gerektiğini unutmayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide bir Azure VM 'nin kullanılabilirliğini, bir kullanılabilirlik kümesi veya kullanılabilirlik bölgesine taşıyarak artırmış olursunuz. Artık taşınan VM için olağanüstü durum kurtarma ayarlayabilirsiniz.

> [!div class="nextstepaction"]
> [Geçişten sonra olağanüstü durum kurtarmayı ayarlama](azure-to-azure-quickstart.md)
