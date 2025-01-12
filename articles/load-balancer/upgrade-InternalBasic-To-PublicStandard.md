---
title: Temel Iç istemciden standart Genel Azure Load Balancer yükseltme
description: Bu makalede, Azure temel Iç Load Balancer standart genel Load Balancer sürümüne nasıl yükselteceğiniz gösterilmektedir
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: 3394754f2829018f7862b3775f8ab2cb2d07d005
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98051369"
---
# <a name="upgrade-azure-internal-load-balancer---outbound-connection-required"></a>Azure Iç Load Balancer yükseltme-giden bağlantı gerekli
[Azure Standart Load Balancer](load-balancer-overview.md) , bölge artıklığı aracılığıyla zengin bir işlev kümesi ve yüksek kullanılabilirlik sağlar. Load Balancer SKU 'SU hakkında daha fazla bilgi için bkz. [karşılaştırma tablosu](./skus.md#skus). Standart dahili Load Balancer giden bağlantı sağlamadığından, bunun yerine standart bir genel Load Balancer oluşturmak için bir çözüm sağlıyoruz.

Yükseltmenin dört aşaması vardır:

1. Yapılandırmayı standart genel Load Balancer geçirme
2. Standart genel Load Balancer arka uç havuzlara VM ekleme
3. Internet 'ten/veya Internet 'e ayarlanması gereken alt ağ/VM 'Ler için NSG kuralları ayarlama

Bu makalede yapılandırma geçişi ele alınmaktadır. Arka uç havuzlara sanal makine eklemek, belirli ortamınıza bağlı olarak değişebilir. Ancak, bazı üst düzey genel öneriler [sağlanır](#add-vms-to-backend-pools-of-standard-load-balancer).

## <a name="upgrade-overview"></a>Yükseltmeye genel bakış

Aşağıdakileri gerçekleştiren bir Azure PowerShell betiği vardır:

* Kaynak grubunda ve belirttiğiniz konumda standart bir SKU ortak Load Balancer oluşturur.
* Temel SKU Iç Load Balancer yapılandırmalarının yeni oluşturma standart ortak Load Balancer sorunsuz şekilde kopyasını oluşturur.
* Çıkış bağlantısı sağlayan bir giden kuralı oluşturur.

### <a name="caveatslimitations"></a>Caveats\Limitations

* Betik, giden bağlantının gerekli olduğu dahili Load Balancer yükseltmesini destekler. Tüm VM 'Ler için giden bağlantı gerekli değilse, en iyi yöntem için [Bu sayfaya](upgrade-basicInternal-standard.md) bakın.
* Standart Load Balancer yeni bir ortak adrese sahiptir. Farklı SKU 'Lara sahip olduklarından, mevcut temel dahili Load Balancer ilişkili IP adreslerini standart ortak Load Balancer sorunsuzca taşımak olanaksızdır.
* Standart yük dengeleyici farklı bir bölgede oluşturulduysa, eski bölgede var olan VM 'Leri yeni oluşturulan Standart Load Balancer ilişkilendiremeyeceksiniz. Bu kısıtlamayı geçici olarak çözmek için yeni bölgede yeni bir VM oluşturun.
* Load Balancer herhangi bir ön uç IP yapılandırması veya arka uç havuzu yoksa, betiği çalıştırırken bir hatayla karşılaşamayacaksınız.  Bunların boş olmadığından emin olun.

## <a name="download-the-script"></a>Betiği indir

[PowerShell Galerisi](https://www.powershellgallery.com/packages/AzureLBUpgrade/2.0)geçiş betiğini indirin.
## <a name="use-the-script"></a>Betiği kullan

Yerel PowerShell ortamınız kuruluma ve tercihlerinize bağlı olarak sizin için iki seçenek vardır:

* Azure az modules yüklü değilse veya Azure az modüllerini kaldırmayı bilmiyorsanız, en iyi seçenek `Install-Script` betiği çalıştırmak için seçeneğini kullanmaktır.
* Azure az modules tutmanız gerekiyorsa, en iyi sonuç, betiği indirmek ve doğrudan çalıştırmak olacaktır.

Azure az modules yüklü olup olmadığınızı öğrenmek için çalıştırın `Get-InstalledModule -Name az` . Yüklü az modül görmüyorsanız, `Install-Script` yöntemini kullanabilirsiniz.

### <a name="install-using-the-install-script-method"></a>Install-Script yöntemi kullanarak Install

Bu seçeneği kullanmak için, bilgisayarınızda Azure az modules yüklü olmamalıdır. Yüklüyse, aşağıdaki komut bir hata görüntüler. Azure az modüller ' i kaldırabilir veya betiği el ile indirmek ve çalıştırmak için diğer seçeneği kullanabilirsiniz.
  
Betiği aşağıdaki komutla çalıştırın:

`Install-Script -Name AzurePublicLBUpgrade`

Bu komut ayrıca gerekli az modülleri de yüklüyor.  

### <a name="install-using-the-script-directly"></a>Betiği kullanarak doğrudan yüklemeyi

Bazı Azure az modülleriniz varsa ve bunları kaldıramıyorsanız (veya kaldırmak istemiyorsanız), betik indirme bağlantısındaki **El Ile indir** sekmesini kullanarak betiği el ile indirebilirsiniz. Betik, ham nupkg dosyası olarak indirilir. Betiği bu nupkg dosyasından yüklemek için bkz. [El Ile paket indirme](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Betiği çalıştırmak için:

1. `Connect-AzAccount`Azure 'a bağlanmak için kullanın.

1. `Import-Module Az`Az modülleri içeri aktarmak için kullanın.

1. Gerekli parametreleri inceleyin:

   * **Oldrgname: [dize]: gereklidir** – bu, yükseltmek Istediğiniz mevcut temel Load Balancer kaynak grubudur. Bu dize değerini bulmak için Azure portal gidin, temel Load Balancer kaynağınızı seçin ve yük dengeleyiciye **Genel Bakış ' a** tıklayın. Kaynak grubu bu sayfada bulunur.
   * **Oldlbname: [dize]: gerekli** – bu, yükseltmek Istediğiniz mevcut temel dengeleyicinizin adıdır. 
   * **Newrgname: [dize]: gereklidir** – bu, standart Load Balancer oluşturulacağı kaynak grubudur. Yeni bir kaynak grubu veya var olan bir grup olabilir. Var olan bir kaynak grubunu seçerseniz, Load Balancer adının kaynak grubu içinde benzersiz olması gerektiğini unutmayın. 
   * **newLocation: [dize]: gerekli** – standart Load Balancer oluşturulacağı konumdur. Diğer mevcut kaynaklarla daha iyi ilişki sağlamak için seçili temel Load Balancer aynı konumunun Standart Load Balancer devralınmasını öneririz.
   * **Newlbname: [dize]: gerekli** – bu, oluşturulacak standart Load Balancer adıdır.
1. Uygun parametreleri kullanarak betiği çalıştırın. Tamamlanması beş ila yedi dakika sürebilir.

    **Örnek**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newrgName "test_userInput3_rg" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

### <a name="add-vms-to-backend-pools-of-standard-load-balancer"></a>Standart Load Balancer arka uç havuzlarına VM ekleme

İlk olarak, betiğin, temel ortak Load Balancer üzerinden geçirilmiş doğru yapılandırmayla yeni bir standart ortak Load Balancer başarıyla oluşturduğunu kontrol edin. Bunu Azure portal doğrulayabilirsiniz.

El ile test olarak Standart Load Balancer aracılığıyla az miktarda trafik gönderdiğinizden emin olun.
  
Aşağıda, yeni oluşturulan Standart genel Load Balancer için arka uç havuzlarına sanal makineler eklemenin ve her biri için önerdiğimiz bazı senaryolar verilmiştir:

* **Mevcut VM 'leri eski temel genel Load Balancer arka uç havuzlarından yeni oluşturulan standart ortak Load Balancer arka uç havuzlarından taşıma**.
    1. Bu hızlı başlangıçta görevleri yapmak için [Azure Portal](https://portal.azure.com)oturum açın.
 
    1. Sol taraftaki menüden **tüm kaynaklar** ' ı seçin ve ardından kaynak listesinden **Yeni oluşturulan standart Load Balancer** seçin.
   
    1. **Ayarlar**’ın altında **Arka Uç Havuzları**’nı seçin.
   
    1. Temel Load Balancer arka uç havuzuyla eşleşen arka uç havuzunu seçin, aşağıdaki değeri seçin: 
      - **Sanal makine**: açılır ve temel Load Balancer eşleşen arka uç havuzundan VM 'leri seçin.
    1. **Kaydet**’i seçin.
    >[!NOTE]
    >Ortak IP 'leri olan VM 'Ler için, ilk olarak aynı IP adresinin garantili olmadığı standart IP adresleri oluşturmanız gerekecektir. VM 'Lerin temel IP 'lerden ilişkisini kaldırın ve yeni oluşturulan standart IP adresleriyle ilişkilendirin. Ardından, Standart Load Balancer arka uç havuzuna VM 'Ler eklemek için yönergeleri takip edebilirsiniz. 

* **Yeni oluşturulan Standart genel Load Balancer arka uç havuzlarına eklemek için yeni VM 'Ler oluşturma**.
    * VM oluşturma ve Standart Load Balancer ile ilişkilendirme hakkında daha fazla yönerge [burada](./quickstart-load-balancer-standard-public-portal.md#create-virtual-machines)bulunabilir.

### <a name="create-an-outbound-rule-for-outbound-connection"></a>Giden bağlantı için giden bir kural oluşturma

Bir giden kuralı oluşturmak için [yönergeleri](./quickstart-load-balancer-standard-public-powershell.md#create-outbound-rule-configuration) izleyin, böylece
* Giden NAT 'yi sıfırdan tanımlayın.
* Varolan giden NAT 'nin davranışını ölçeklendirin ve ayarlayın.

### <a name="create-nsg-rules-for-vms-which-to-refrain-communication-from-or-to-the-internet"></a>Internet 'ten veya internetten gelen iletişimi belirten sanal makineler için NSG kuralları oluşturma
Internet trafiğinin sanal makinelerinize ulaşmasını istiyorsanız, VM 'lerin ağ arabiriminde bir [NSG kuralı](../virtual-network/manage-network-security-group.md) oluşturabilirsiniz.

## <a name="common-questions"></a>Sık sorulan sorular

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Yapılandırmayı v1 'den v2 'ye geçirmek için Azure PowerShell betiğiyle ilgili herhangi bir sınırlama var mı?

Evet. Bkz. [Uyarılar/sınırlamalar](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Azure PowerShell betiği, temel Load Balancer trafik üzerinde yeni oluşturulan Standart Load Balancer da geçiş yapar mi?

Hayır. Azure PowerShell betiği yalnızca yapılandırmayı geçirir. Gerçek trafik geçişi, sizin ve denetiminizin sorumluluğundadır.

## <a name="next-steps"></a>Sonraki adımlar

[Standart Load Balancer hakkında bilgi edinin](load-balancer-overview.md)
