---
title: RHEL 'de HSR ve Paceyapıcısı ile ölçeği SAP HANA | Microsoft Docs
description: RHEL üzerinde HSR ve pacemaker ile ölçeği genişletme SAP HANA
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: radeltch
ms.openlocfilehash: dd000ca36bb0ad586b973b2bcdc638d8992b1a73
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668648"
---
# <a name="high-availability-of-sap-hana-scale-out-system-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux üzerinde SAP HANA genişleme sisteminin yüksek kullanılabilirliği 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


Bu makalede, Azure Red Hat Enterprise Linux sanal makinelerinde (VM 'Ler) HANA sistem çoğaltması (HSR) ve pacemaker ile yüksek düzeyde kullanılabilir SAP HANA sisteminin nasıl dağıtılacağı açıklanır. Sunulan mimarideki paylaşılan dosya sistemleri [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) tarafından SAĞLANıR ve NFS üzerinden bağlanır.  

Örnek yapılandırmalarda, yükleme komutlarında ve bu durumda, HANA örneği **03** ' dır ve Hana sistem kimliği **HN1**' dir. Örnekler, HANA 2,0 SP4 ve Red Hat Enterprise Linux SAP 7,6 ' i temel alır. 

Başlamadan önce, aşağıdaki SAP notları ve incelemeleri inceleyin:

* SAP Note [1928533] şunları içerir:  
  * SAP yazılımının dağıtımı için desteklenen Azure VM boyutlarının listesi
  * Azure VM boyutları için önemli kapasite bilgileri
  * Desteklenen SAP yazılımı ve işletim sistemi (OS) ve veritabanı birleşimleri
  * Microsoft Azure üzerinde Windows ve Linux için gereken SAP çekirdek sürümü
* SAP Note [2015553]: Azure 'da SAP tarafından desteklenen SAP yazılım dağıtımları için önkoşulları listeler
* SAP Note [2002167] için önerilen işletim sistemi ayarları Red Hat Enterprise Linux
* SAP Note [2009879] , Red Hat Enterprise Linux Için SAP HANA yönergelerine sahiptir
* SAP Note [2178632]: Azure 'da SAP için raporlanan tüm izleme ölçümleriyle ilgili ayrıntılı bilgiler içerir
* SAP Note [2191498]: Azure 'da Linux IÇIN gereken SAP konak Aracısı sürümünü içerir
* SAP Note [2243692]: Azure 'da LINUX 'ta SAP lisanslama hakkında bilgi içerir
* SAP Note [1999351]: SAP Için Azure Gelişmiş izleme uzantısı ile ilgili ek sorun giderme bilgileri içerir
* SAP Note [1900823]: SAP HANA depolama gereksinimleriyle ilgili bilgiler içerir
* [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Linux için gereken tüm sap notlarını içerir
* [Linux 'ta SAP için Azure sanal makineleri planlama ve uygulama][planning-guide]
* [Linux 'ta SAP için Azure sanal makineleri dağıtımı][deployment-guide]
* [Linux üzerinde SAP için Azure sanal makineleri DBMS dağıtımı][dbms-guide]
* [SAP HANA ağ gereksinimleri](https://www.sap.com/documents/2016/08/1cd2c2fb-807c-0010-82c7-eda71af511fa.html)
* Genel RHEL belgeleri
  * [Yüksek kullanılabilirlik Add-On genel bakış](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Yüksek kullanılabilirlik Add-On yönetimi](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Yüksek kullanılabilirlik Add-On başvurusu](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Red Hat Enterprise Linux Ağ Kılavuzu](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
  * [NFS paylaşımlarında HANA dosya sistemleriyle bir pacemaker kümesinde SAP HANA Scale-Out sistem çoğaltmasını yapılandırma Nasıl yaparım?](https://access.redhat.com/solutions/5423971)
* Azure 'a özgü RHEL belgeleri:
  * [Microsoft Azure kullanım için Red Hat Enterprise Linux SAP HANA yüklemesi](https://access.redhat.com/public-cloud/microsoft-azure)
  * [SAP HANA Scale-Out ve sistem çoğaltması için Red Hat Enterprise Linux çözümü](https://access.redhat.com/solutions/4386601)
* [Microsoft Azure Azure NetApp Files kullanarak NetApp SAP uygulamaları][anf-sap-applications-azure]
* [Azure NetApp Files belgeleri][anf-azure-doc] 
* [SAP HANA için Azure NetApp Files üzerinde NFS v4.1 birimleri](./hana-vm-operations-netapp.md)

## <a name="overview"></a>Genel Bakış

HANA genişleme yüklemeleri için HANA yüksek kullanılabilirlik elde etmek için bir yöntem, HANA sistem çoğaltmasını yapılandırmak ve otomatik yük devretmeye izin vermek için çözümü Paceoluşturucu kümesiyle korumaktır. Etkin bir düğüm başarısız olduğunda, küme HANA kaynaklarını diğer siteye devreder.  
Sunulan yapılandırma her sitede üç HANA düğümü, ayrıca bölünmüş beyinini engellemek için de çoğunluk Oluşturucu düğümünü gösterir. Yönergeler, HANA DB düğümleri olarak daha fazla VM eklemek için uyarlanmıştır.  

Sunulan mimarideki HANA paylaşılan dosya sistemi `/hana/shared` [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md)tarafından sağlanır. Aynı HANA sistem çoğaltma sitesindeki her bir HANA düğümüne NFSv 4.1 aracılığıyla bağlanır. Dosya sistemleri `/hana/data` ve `/hana/log` yerel dosya SISTEMLERIDIR ve Hana DB düğümleri arasında paylaşılmaz. SAP HANA, paylaşılmayan modda yüklenecek. 

> [!TIP]
> Önerilen SAP HANA depolama yapılandırması için bkz. [Azure VM depolama yapılandırmalarının SAP HANA](./hana-vm-operations-storage.md).   

[![SAP HANA, HSR ve Paceyapıcısı kümesiyle genişleme](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel.png)](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel-detail.png#lightbox)

Yukarıdaki diyagramda, SAP HANA ağ önerilerini izleyerek bir Azure sanal ağı içinde üç alt ağ temsil edilir: 
* istemci iletişimi için- `client` 10.23.0.0/24  
* iç HANA düğümler arası iletişim için- `inter` 10.23.1.128/26  
* HANA sistem çoğaltması için- `hsr` 10.23.1.192/26  

`/hana/data`Ve `/hana/log` yerel disklerde dağıtıldığında, depolama ile iletişim için ayrı alt ağ ve ayrı sanal ağ kartları dağıtmak gerekli değildir.  

Azure NetApp birimleri ayrı bir alt ağda dağıtılır, [Azure NetApp Files için temsilci olarak] ( https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet: `anf` 10.23.1.0/26.   

## <a name="set-up-the-infrastructure"></a>Altyapıyı ayarlama

Aşağıdaki yönergelerde, kaynak grubu, Azure sanal ağı üç Azure ağ alt ağı ile zaten oluşturduğumuz varsayılmaktadır: `client` , `inter` ve `hsr` .

### <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Linux sanal makinelerini Azure portal aracılığıyla dağıtma
1. Azure VM 'Leri dağıtın.  
Bu belgede sunulan yapılandırma için yedi sanal makine dağıtın: 
   - Hana çoğaltma sitesi 1 için HANA DB düğümleri olarak kullanılacak üç sanal makine: **Hana-S1-DB1**, **Hana-S1-DB2** ve **Hana-S1-DB3**  
   - Hana çoğaltma sitesi 2 için HANA DB düğümleri olarak kullanılacak üç sanal makine: **Hana-S2-DB1**, **Hana-S2-DB2** ve **Hana-S2-DB3**  
   - *çoğunluk Oluşturucu* olarak kullanılacak küçük bir sanal makine: **Hana-s-mm**

   SAP DB HANA düğümleri olarak dağıtılan VM 'Ler, [SAP HANA donanım dizininde](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)yayınlanan Hana için SAP tarafından sertifikalandırılmalıdır. HANA DB düğümlerini dağıttığınızda, [hızlandırılmış ağın](../../../virtual-network/create-vm-accelerated-networking-cli.md) seçili olduğundan emin olun.  
  
   Bu VM SAP HANA kaynaklarından hiçbirini çalıştırmazsa, çoğunluk Oluşturucu düğümü için küçük bir VM dağıtabilirsiniz. Çoğunluk Oluşturucu VM, küme yapılandırmasında bir bölünmüş beyinli senaryoda tek sayıda küme düğümü elde etmek için kullanılır. Çoğunluk Oluşturucu VM 'nin `client` Bu örnekteki alt ağda yalnızca bir sanal ağ arabirimine ihtiyacı vardır.        

   Ve için yerel yönetilen diskler `/hana/data` dağıtın `/hana/log` . Ve için önerilen en düşük depolama yapılandırması, `/hana/data` `/hana/log` [Azure VM 'leri depolama yapılandırmalarında SAP HANA](./hana-vm-operations-storage.md)açıklanmıştır.

   Sanal ağ alt ağındaki her VM için birincil ağ arabirimini dağıtın `client` .  
   VM Azure portal aracılığıyla dağıtıldığında, ağ arabirimi adı otomatik olarak oluşturulur. Basitlikle ilgili Bu yönergelerde, `client` Azure sanal ağ alt ağına **Hana-S1-DB1-Client**, **Hana-S1-DB2-Client**, **Hana-S1-DB3-Client** gibi bir şekilde eklenmiş olan otomatik olarak oluşturulan, birincil ağ arabirimlerine başvuracağız.  


   > [!IMPORTANT]
   > Seçtiğiniz işletim sisteminin, kullanmakta olduğunuz belirli VM türlerinde SAP HANA için SAP sertifikalı olduğundan emin olun. Bu türlere yönelik SAP HANA sertifikalı VM türlerinin ve işletim sistemi sürümlerinin listesi için, [SAP HANA sertifikalı IaaS platformları](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) sitesine gidin. Söz konusu türe yönelik SAP HANA tarafından desteklenen işletim sistemi sürümlerinin tam listesini almak için listelenen VM türünün ayrıntılarına tıklayın.  
  

2. Sanal ağ alt ağında her bir HANA DB sanal makinesi için bir tane olmak üzere altı ağ arabirimi oluşturun `inter` (Bu örnekte, **Hana-S1-DB1-Inter**, **Hana-S1-DB2-Inter**, **Hana-S1-DB3-Inter**, **Hana-S2-DB1-Inter**, **Hana-S2-DB2-Inter** ve **Hana-S2-DB3-Inter**).  

3. Altı ağ arabirimi oluşturun, her bir HANA DB sanal makinesi için, `hsr` sanal ağ alt ağında (Bu örnekte, **Hana-S1-DB1-HSR**, Hana- **S1-DB2-HSR**, **Hana-S1-DB3-HSR**, **Hana-S2-DB1-HSR**, **Hana-S2-DB2-HSR** ve **Hana-S2-DB3-HSR**).  

4. Yeni oluşturulan sanal ağ arabirimlerini karşılık gelen sanal makinelere ekleyin:  

    a. [Azure Portal](https://portal.azure.com/#home)sanal makineye gidin.  

    b. Sol bölmede **sanal makineler**' i seçin. Sanal makine adını (örneğin, **Hana-S1-DB1**) filtreleyin ve ardından sanal makineyi seçin.  

    c. **Genel bakış** bölmesinde, sanal makineyi serbest bırakmak için **Durdur** ' u seçin.  

    d. Ağ **' ı** seçin ve ardından ağ arabirimini ekleyin. **Ağ arabirimi Ekle** aşağı açılan listesinde, `inter` ve alt ağları için önceden oluşturulmuş ağ arabirimlerini seçin `hsr` .  
    
    e. **Kaydet**’i seçin. 
 
    f. Kalan sanal makineler için b ile e arasındaki adımları yineleyin (bizim örneğimizde,  **Hana-S1-DB2**, **Hana-S1-DB3**, **Hana-S2-DB1**, **Hana-S2-DB2** ve **Hana-S2-DB3**).
 
    örneğin: Sanal makineleri şimdilik durdurulmuş durumda bırakın. Daha sonra, yeni eklenen tüm ağ arabirimleri için [hızlandırılmış ağı](../../../virtual-network/create-vm-accelerated-networking-cli.md) etkinleştireceğiz.  

5. `inter` `hsr` Aşağıdaki adımları uygulayarak ve alt ağları için ek ağ arabirimleri için hızlandırılmış ağı etkinleştirin:  

    a. Azure portal [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) açın. [](https://portal.azure.com/#home)  

    b. `inter`Ve alt ağlarına eklenen ek ağ arabirimleri için hızlandırılmış ağı etkinleştirmek üzere aşağıdaki komutları yürütün `hsr` .  

    ```azurecli
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-inter --accelerated-networking true
    
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-hsr --accelerated-networking true
    ```

7. HANA DB sanal makinelerini başlatma

### <a name="deploy-azure-load-balancer"></a>Azure Load Balancer dağıt

1. Standart yük dengeleyici kullanmanızı öneririz. Standart yük dengeleyiciyi dağıtmak için aşağıdaki yapılandırma adımlarını izleyin:
   1. İlk olarak, bir ön uç IP havuzu oluşturun:

      1. Yük dengeleyiciyi açın, **ön uç IP havuzu**' nu seçin ve **Ekle**' yi seçin.
      1. Yeni ön uç IP havuzunun adını girin (örneğin, **Hana-ön uç**).
      1. **Atamayı** **statik** olarak ayarlayın ve IP adresini (örneğin, **10.23.0.18**) girin.
      1. **Tamam**’ı seçin.
      1. Yeni ön uç IP havuzu oluşturulduktan sonra, havuzun IP adresini aklınızda edin.

   1. Sonra, arka uç havuzu oluşturun ve tüm küme VM 'lerini arka uç havuzuna ekleyin:

      1. Yük dengeleyiciyi açın, **arka uç havuzları**' nı seçin ve **Ekle**' yi seçin.
      1. Yeni arka uç havuzunun adını girin (örneğin, **Hana arka ucu**).
      1. **Sanal makine Ekle**' yi seçin.
      1. **Sanal makine**'yi seçin.
      1. SAP HANA kümesinin sanal makinelerini ve alt ağın IP adreslerini seçin `client` .
      1. **Add (Ekle)** seçeneğini belirleyin.

   1. Sonra, bir sistem durumu araştırması oluşturun:

      1. Yük dengeleyiciyi açın, **sistem durumu araştırmaları**' nı seçin ve **Ekle**' yi seçin.
      1. Yeni sistem durumu araştırmasının adını girin (örneğin, **Hana-HP**).
      1. Protokol ve bağlantı noktası 625 **03** olarak **TCP** ' yi seçin. **Aralık** değerini 5 olarak ve **sağlıksız eşik** değerini 2 olarak ayarlayın.
      1. **Tamam**’ı seçin.

   1. Sonra, Yük Dengeleme kurallarını oluşturun:
   
      1. Yük dengeleyiciyi açın, **Yük Dengeleme kuralları**' nı seçin ve **Ekle**' yi seçin.
      1. Yeni yük dengeleyici kuralının adını girin (örneğin, **Hana-lb**).
      1. Ön uç IP adresini, arka uç havuzunu ve daha önce oluşturduğunuz sistem durumu araştırmasını (örneğin, **Hana-ön uç**, **Hana-arka uç** ve **Hana-HP**) seçin.
      1. **Ha bağlantı noktalarını** seçin.
      1. **Boşta kalma zaman aşımını** 30 dakikaya yükseltin.
      1. **Kayan IP**'yi etkinleştirdiğinizden emin olun.
      1. **Tamam**’ı seçin.

   > [!IMPORTANT]
   > Kayan IP, Yük Dengeleme senaryolarında NIC ikincil IP yapılandırmasında desteklenmez. Ayrıntılar için bkz. [Azure yük dengeleyici sınırlamaları](../../../load-balancer/load-balancer-multivip-overview.md#limitations). VM için ek IP adresine ihtiyacınız varsa ikinci bir NIC dağıtın.    
   
   > [!Note]
   > Ortak IP adresleri olmayan VM 'Ler, iç (genel IP adresi olmayan) standart Azure yük dengeleyicisine yerleştirildiğinde, genel uç noktalara yönlendirmeye izin vermek için ek yapılandırma gerçekleştirilmediği takdirde giden internet bağlantısı olmaz. Giden bağlantıyı elde etme hakkında daha fazla bilgi için bkz. [Azure Standart Load Balancer kullanan sanal makineler Için genel uç nokta BAĞLANTıSı SAP yüksek kullanılabilirlik senaryolarında](./high-availability-guide-standard-load-balancer-outbound-connections.md).  


   > [!IMPORTANT]
   > Azure Load Balancer arkasına yerleştirilmiş Azure VM 'lerinde TCP zaman damgalarını etkinleştirmeyin. TCP zaman damgalarını etkinleştirmek, sistem durumu araştırmalarının başarısız olmasına neden olur. Parametre **net.ipv4.tcp_timestamps** **0** olarak ayarlayın. Ayrıntılar için bkz. [Load Balancer sistem durumu araştırmaları](../../../load-balancer/load-balancer-custom-probe-overview.md).
   > Ayrıca bkz. SAP Note [2382421](https://launchpad.support.sap.com/#/notes/2382421).  

### <a name="deploy-the-azure-netapp-files-infrastructure"></a>Azure NetApp Files altyapısını dağıtma 

Dosya sistemi için ANF birimlerini dağıtın `/hana/shared` . `/hana/shared`Her BIR Hana sistem çoğaltma sitesi için ayrı bir birime ihtiyacınız olacaktır. Daha fazla bilgi için bkz. [Azure NetApp Files altyapısını ayarlama](./sap-hana-scale-out-standby-netapp-files-rhel.md#set-up-the-azure-netapp-files-infrastructure).

Bu örnekte, aşağıdaki Azure NetApp Files birimleri kullanılmıştır: 

* Volume **HN1**-Shared-s1 (NFS://10.23.1.7/**HN1**-Shared-S1)
* Volume **HN1**-Shared-s2 (NFS://10.23.1.7/**HN1**-Shared-S2)

## <a name="operating-system-configuration-and-preparation"></a>İşletim sistemi yapılandırması ve hazırlığı

Sonraki bölümlerdeki yönergelere aşağıdaki kısaltmaların ön eki verilmiştir:
* **[A]**: tüm düğümlere uygulanabilir
* **[Ah]**: tüm Hana DB düğümlerine uygulanabilir
* **[E]**: çoğunluk Oluşturucu düğümüne uygulanabilir
* **[AH1]**: SITE 1 ' DEKI tüm Hana DB düğümlerine uygulanabilir
* **[AH2]**: SITE 2 ' DEKI tüm Hana DB düğümlerine uygulanabilir
* **[1]**: yalnızca Hana DB düğüm 1, SITE 1 için geçerlidir
* **[2]**: yalnızca Hana DB düğüm 1, SITE 2 için geçerlidir


Aşağıdaki adımları uygulayarak işletim sistemini yapılandırın ve hazırlayın:

1. **[A]** sanal makinelerdeki konak dosyalarını koruyun. Tüm alt ağların girdilerini dahil edin. Bu örnek için aşağıdaki girişler eklenmiştir `/etc/hosts` .  

    ```bash
     # Client subnet
     10.23.0.11 hana-s1-db1
     10.23.0.12 hana-s1-db1
     10.23.0.13 hana-s1-db2
     10.23.0.14 hana-s2-db1
     10.23.0.15 hana-s2-db2
     10.23.0.16 hana-s2-db3
     10.23.0.17 hana-s-mm
     # Internode subnet
     10.23.1.138 hana-s1-db1-inter
     10.23.1.139 hana-s1-db2-inter
     10.23.1.140 hana-s1-db3-inter
     10.23.1.141 hana-s2-db1-inter
     10.23.1.142 hana-s2-db2-inter
     10.23.1.143 hana-s2-db3-inter
     # HSR subnet
     10.23.1.202 hana-s1-db1-hsr
     10.23.1.203 hana-s1-db2-hsr
     10.23.1.204 hana-s1-db3-hsr
     10.23.1.205 hana-s2-db1-hsr
     10.23.1.206 hana-s2-db2-hsr
     10.23.1.207 hana-s2-db3-hsr
    ```


2. **[A]** NFS istemci paketini yükler.  

    ```yum install nfs-utils ```


3. **[Ah]** HANA yapılandırması için Red hat.  

    Aşağıdaki SAP notlarında ve ' de açıklandığı gibi RHEL 'yi yapılandırın <https://access.redhat.com/solutions/2447641> :  
   - [2292690-SAP HANA DB: RHEL 7 için önerilen işletim sistemi ayarları](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782-SAP HANA DB: RHEL 8 için önerilen işletim sistemi ayarları](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582-Linux: GCC 6. x ile derlenen SAP uygulamaları çalıştırma](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824-Linux: GCC 7. x ile derlenen SAP uygulamalarını çalıştırma](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607-Linux: GCC 9. x ile derlenen SAP uygulamalarını çalıştırma](https://launchpad.support.sap.com/#/notes/2886607)

## <a name="prepare-the-file-systems"></a>Dosya sistemlerini hazırlama
### <a name="mount-the-shared-file-systems"></a>Paylaşılan dosya sistemlerini bağlama

Bu örnekte, paylaşılan HANA dosya sistemleri Azure NetApp Files dağıtılır ve NFSv4 üzerinden bağlanır.  

1. **[Ah]** HANA veritabanı birimleri için bağlama noktaları oluşturun.  

    ```bash
    mkdir -p /hana/shared
    ```

2. **[Ah]** NFS etki alanı ayarını doğrulayın. Etki alanının varsayılan Azure NetApp Files etki alanı olarak yapılandırıldığından, diğer bir deyişle ve eşlemenin hiçbir bir **`defaultv4iddomain.com`** şekilde ayarlandığından emin olun.   
   Bu adım yalnızca Azure NetAppFiles NFSv 4.1 kullanılıyorsa gereklidir.  

    > [!IMPORTANT]
    > VM 'de NFS etki alanını `/etc/idmapd.conf` Azure NetApp Files ' deki varsayılan etki alanı yapılandırmasıyla eşleşecek şekilde ayarladığınızdan emin olun: **`defaultv4iddomain.com`** . NFS istemcisindeki (yani, VM) ve NFS sunucusunun etki alanı yapılandırması arasında uyuşmazlık varsa (örneğin, Azure NetApp yapılandırması), VM 'Lere bağlı Azure NetApp birimlerinde dosya izinleri olarak görüntülenir `nobody` .  

    ```bash
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

3. **[Ah]** Doğrulayın `nfs4_disable_idmapping` . **Y** olarak ayarlanmalıdır. Bulunduğu dizin yapısını oluşturmak için `nfs4_disable_idmapping` Mount komutunu yürütün. Erişim çekirdek/sürücü için ayrıldığından,/sys/modules altında dizini el ile oluşturamazsınız.  
   Bu adım yalnızca Azure NetAppFiles NFSv 4.1 kullanılıyorsa gereklidir.  

    ```bash
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   Parametresinin nasıl değiştirileceği hakkında daha fazla bilgi için `nfs4_disable_idmapping` bkz https://access.redhat.com/solutions/1749883 ..

4. **[AH1]** SITE1 HANA DB VM 'lerinde paylaşılan Azure NetApp Files birimlerini bağlayın.  

    ```bash
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s1 /hana/shared
    ```

5. **[AH2]** SITE2 HANA DB VM 'lerinde paylaşılan Azure NetApp Files birimlerini bağlayın.  

    ```bash
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s2 /hana/shared
    ```


10. **[Ah]** Karşılık gelen `/hana/shared/` dosya sistemlerinin, NFS protokol sürümü **NFSv4** olan tüm Hana DB VM 'lerine bağlı olduğunu doğrulayın.  

    ```bash
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

### <a name="prepare-the-data-and-log-local-file-systems"></a>Verileri hazırlama ve yerel dosya sistemlerini günlüğe kaydetme
Sunulan yapılandırmada, dosya sistemleri `/hana/data` ve `/hana/log` yönetilen diske dağıtılır ve her BIR Hana DB VM 'sine yerel olarak eklenir. Her bir HANA DB sanal makinesinde yerel veri ve günlük birimleri oluşturmak için bu adımları yürütmeniz gerekir. 

**Mantıksal birim Yöneticisi (LVM)** ile disk düzeni ayarlayın. Aşağıdaki örnek, her bir HANA sanal makinesinin, iki birim oluşturmak için kullanılan üç veri diskine bağlı olduğunu varsayar.

1. **[Ah]** Tüm kullanılabilir diskleri listeleyin:
    ```bash
    ls /dev/disk/azure/scsi1/lun*
    ```

   Örnek çıktı:

    ```bash
    /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2 
    ```

2. **[Ah]** Kullanmak istediğiniz tüm diskler için fiziksel birimler oluşturun:
    ```bash
    sudo pvcreate /dev/disk/azure/scsi1/lun0
    sudo pvcreate /dev/disk/azure/scsi1/lun1
    sudo pvcreate /dev/disk/azure/scsi1/lun2
    ```

3. **[Ah]** Veri dosyaları için bir birim grubu oluşturun. Günlük dosyaları için bir birim grubu ve SAP HANA paylaşılan dizinine yönelik bir tane kullanın:
    ```bash
    sudo vgcreate vg_hana_data_HN1 /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
    sudo vgcreate vg_hana_log_HN1 /dev/disk/azure/scsi1/lun2
    ```

4. **[Ah]** Mantıksal birimleri oluşturun. 
   Anahtar olmadan kullandığınızda doğrusal bir birim oluşturulur `lvcreate` `-i` . Daha iyi g/ç performansı için şeritli bir birim oluşturmanızı ve şeritli boyutları [SAP HANA VM depolama yapılandırmalarında](./hana-vm-operations-storage.md)belgelenen değerlere hizalamanızı öneririz. `-i`Bağımsız değişken, temeldeki fiziksel birimlerin sayısı olmalıdır ve `-I` bağımsız değişken Stripe boyutudur. Bu belgede, veri hacmi için iki fiziksel birim kullanılır, bu nedenle `-i` anahtar bağımsız değişkeni **2** olarak ayarlanır. Veri hacmi için Şerit boyutu **256 KiB**'dir. Günlük birimi için bir fiziksel birim kullanılır, bu nedenle `-i` `-I` günlük birimi komutları için hiçbir veya anahtar açık olarak kullanılmaz.  

   > [!IMPORTANT]
   > `-i`Her bir veri veya günlük birimi için birden fazla fiziksel birim kullandığınızda anahtarı kullanın ve temel alınan fiziksel birimin numarasını ayarlayın. `-I`Şeritli birim oluştururken Stripe boyutunu belirtmek için anahtarını kullanın.  
   > Bkz. şerit boyutları ve disk sayısı dahil olmak üzere önerilen depolama yapılandırmalarının [SAP HANA VM depolama yapılandırması](./hana-vm-operations-storage.md) .  

    ```bash
    sudo lvcreate -i 2 -I 256 -l 100%FREE -n hana_data vg_hana_data_HN1
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_HN1
    sudo mkfs.xfs /dev/vg_hana_data_HN1/hana_data
    sudo mkfs.xfs /dev/vg_hana_log_HN1/hana_log
    ```

5. **[Ah]** Bağlama dizinlerini oluşturun ve tüm mantıksal birimlerin UUID 'sini kopyalayın:
    ```bash
    sudo mkdir -p /hana/data/HN1
    sudo mkdir -p /hana/log/HN1
    # Write down the ID of /dev/vg_hana_data_HN1/hana_data and /dev/vg_hana_log_HN1/hana_log
    sudo blkid
    ```

6. **[Ah]** `fstab` Mantıksal birimler ve bağlama için giriş oluşturun:
    ```bash
    sudo vi /etc/fstab
    ```

   Aşağıdaki satırı `/etc/fstab` dosyasına ekleyin:

    ```bash
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_data_HN1-hana_data /hana/data/HN1 xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_log_HN1-hana_log /hana/log/HN1 xfs  defaults,nofail  0  2
    ```

   Yeni birimleri bağlama:

    ```bash
    sudo mount -a
    ```

## <a name="installation"></a>Yükleme  

Bu örnekte, Azure VM 'lerinde HSR ile genişleme yapılandırmasında SAP HANA dağıtmak için HANA 2,0 SP4 kullandık.  

### <a name="prepare-for-hana-installation"></a>HANA yüklemesine hazırlanma

1. **[Ah]** HANA yüklemesinden önce kök parolasını ayarlayın. Yükleme tamamlandıktan sonra kök parolayı devre dışı bırakabilirsiniz. Farklı Çalıştır `root` komutu `passwd` .  

2. **[1, 2]** üzerindeki izinleri değiştirme `/hana/shared` 
    ```bash
    chmod 775 /hana/shared
    ```

3. **[1]** bu sitedeki Hana **-S1-DB2** ve **Hana-S1-DB3**, bir parola sorulmadan, SSH aracılığıyla oturum açabildiğinizi doğrulayın.  
   Böyle bir durum değilse, [anahtar tabanlı kimlik doğrulaması kullanma](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs)bölümünde belirtildiği gibi Exchange SSH anahtarları.  
    ```bash
    ssh root@hana-s1-db2
    ssh root@hana-s1-db3
    ```

4. **[2]** bir parola istenmeden bu sitede **Hana-S2-DB2** ve **Hana-S2-DB3** içindeki Hana DB VM 'lerine SSH aracılığıyla oturum açabildiğinizi doğrulayın.  
   Böyle bir durum değilse, [anahtar tabanlı kimlik doğrulaması kullanma](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs)bölümünde belirtildiği gibi Exchange SSH anahtarları.  
    ```bash
    ssh root@hana-s2-db2
    ssh root@hana-s2-db3
    ```

5. **[Ah]** HANA 2,0 SP4 için gereken ek paketleri yükler. Daha fazla bilgi için bkz. RHEL 7 için SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824) . 

    ```bash
    # If using RHEL 7
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1
    # If using RHEL 8
    yum install libatomic libtool-ltdl.x86_64
    ```


6. **[A]** güvenlik duvarını geçici olarak devre DıŞı bırakarak Hana yüklemesiyle karışmaz. HANA yüklemesi yapıldıktan sonra yeniden etkinleştirebilirsiniz. 
    ```bash
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
    ```

### <a name="hana-installation-on-the-first-node-on-each-site"></a>Her sitedeki ilk düğümde HANA yüklemesi

1. **[1]** [SAP HANA 2,0 yükleme ve güncelleştirme kılavuzundaki](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html)yönergeleri izleyerek SAP HANA yükleme. Aşağıdaki yönergelerde, SITE 1 ' deki ilk düğümde SAP HANA yükleme gösterilmektedir.   

   a. **Hdblcm** programını `root` Hana yükleme yazılımı dizininden başlatın. Parametresini kullanın `internal_network` ve Iç Hana düğümler arası iletişim için kullanılan alt ağ için adres alanını geçirin.  

    ```bash
    ./hdblcm --internal_network=10.23.1.128/26
    ```

   b. İsteminde aşağıdaki değerleri girin:

     * **Eylem Seç** için: **1** girin (Install için)
     * **Yükleme Için ek bileşenler** için: **2, 3** girin
     * Yükleme yolu için: ENTER tuşuna basın (varsayılan olarak/Hana/Shared değerini alır)
     * **Yerel ana bilgisayar adı** için: varsayılan değer kabul etmek için ENTER tuşuna basın
     * İçin **sisteme konaklar eklemek istiyor musunuz?**: **n** girin
     * **SAP HANA SISTEM kimliği** Için: **HN1** girin
     * **Örnek numarası** [00]: **03** girin
     * **Yerel konak çalışan grubu** için [varsayılan]: varsayılan değer kabul etmek için ENTER tuşuna basın
     * **Sistem kullanımını Seç/dizini girin [4]**: **4** girin (özel için)
     * **Veri birimlerinin konumu** için [/Hana/Data/hn1]: varsayılanı kabul etmek için ENTER tuşuna basın
     * **Günlük birimlerinin konumu** için [/Hana/log/hn1]: varsayılanı kabul etmek için ENTER tuşuna basın
     * **Maksimum bellek ayırmayı kısıtlamak için?** [n]: **n** girin
     * **Host Hana-S1-DB1** [Hana-S1-DB1] Için sertifika ana bilgisayar adı: varsayılanı kabul etmek için ENTER tuşuna basın
     * **SAP konak Aracısı Kullanıcı (sapadm) parolası** için: parolayı girin
     * **SAP konak Aracısı Kullanıcı (sapadm) parolasını onaylayın**: parolayı girin
     * **Sistem Yöneticisi (hn1adm) parolası** için: parolayı girin
     * **Sistem Yöneticisi ana dizini** için [/usr/SAP/hn1/Home]: varsayılanı kabul etmek için ENTER tuşuna basın
     * **Sistem Yöneticisi oturum açma kabuğu** [/bin/sh]: varsayılanı kabul etmek için ENTER tuşuna basın
     * **Sistem Yöneticisi Kullanıcı kimliği** [1001]: varsayılanı kabul etmek için ENTER tuşuna basın
     * **Kullanıcı grubunun (sapsys)** [79] kimliğini girin: varsayılanı kabul etmek için ENTER tuşuna basın
     * **Sistem veritabanı kullanıcısı (sistem) parolası** için: sistemin parolasını girin
     * **Sistem veritabanı kullanıcı (sistem) parolasını onaylayın**: sistem parolasını girin
     * **Makine yeniden başlatıldıktan sonra sistem yeniden başlatma için mi?** [n]: **n** girin 
     * **Devam etmek Istiyor musunuz (e/h)**: Özeti doğrulayıp her şey iyi görünüyorsa **y** girin

2. **[2]** SITE 2 ' deki ilk düğüme SAP HANA yüklemek için önceki adımı yineleyin.   

3. **[1, 2]** Verify global.ini  

   global.ini görüntüleyin ve iç SAP HANA düğümler arası iletişimin yapılandırmasının yerinde olduğundan emin olun. **İletişim** bölümünü doğrulayın. Alt ağ için adres alanına sahip olmalıdır `inter` ve `listeninterface` olarak ayarlanmalıdır `.internal` . **İnternal_hostname_resolution** bölümünü doğrulayın. Bu, alt ağa ait olan HANA sanal makinelerinin IP adreslerine sahip olmalıdır `inter` .  

   ```bash
     sudo cat /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
     # Example from SITE1 
     [communication]
     internal_network = 10.23.1.128/26
     listeninterface = .internal
     [internal_hostname_resolution]
     10.23.1.138 = hana-s1-db1
     10.23.1.139 = hana-s1-db2
     10.23.1.140 = hana-s1-db3
   ```

4. **[1, 2]** `global.ini` SAP Note [2080991](https://launchpad.support.sap.com/#/notes/0002080991)' de açıklandığı gibi, paylaşılmayan ortamda yüklemeye hazırlanın.  

   ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    [persistence]
    basepath_shared = no
   ```

4. **[1, 2]** değişiklikleri etkinleştirmek için SAP HANA yeniden başlatın.  

   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem
   ```

6. **[1, 2]** istemci arabiriminin `client` iletişim IÇIN alt ağdan IP adreslerini kullandığını doğrulayın.  

    ```bash
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "password" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result - example from SITE 2
    "hana-s2-db1","net_publicname","10.23.0.14"
   ```

   Yapılandırmayı doğrulama hakkında daha fazla bilgi için bkz. SAP Note [2183363-SAP HANA iç ağ yapılandırması](https://launchpad.support.sap.com/#/notes/2183363).  

7. **[Ah]** HANA yükleme hatasından kaçınmak için veri ve günlük dizinlerinde izinleri değiştirin.  

   ```bash
    sudo chmod o+w -R /hana/data /hana/log
   ```

8. **[1]** ikincil Hana düğümlerini yükler. Bu adımdaki örnek yönergeler SITE 1 içindir.  
   a. Yerleşik **hdblcm** programını olarak başlatın `root` .    
    ```bash
     cd /hana/shared/HN1/hdblcm
     ./hdblcm 
    ```

   b. İsteminde aşağıdaki değerleri girin:

     * **Eylem Seç** için: **2** girin (ana bilgisayar Ekle için)
     * **Eklenecek virgülle ayrılmış ana bilgisayar adlarını girin**: Hana-S1-DB2, Hana-S1-DB3
     * **Yükleme Için ek bileşenler** için: **2, 3** girin
     * **Kök Kullanıcı adı girin [root]**: varsayılanı kabul etmek için ENTER tuşuna basın
     * **' Hana-S1-DB2 ' [1] konağının Select rolleri** için: 1 (çalışan için)
     * **Ana bilgisayar Için konak yük devretme grubunu girin ' Hana-S1-DB2 ' [varsayılan]**: varsayılanı kabul etmek için ENTER tuşuna basın
     * **' Hana-S1-DB2 ' [<<assign automatically>>] konağının depolama bölümü numarasını girin**: varsayılanı kabul etmek için ENTER tuşuna basın
     * **Konağın ' Hana-S1-DB2 ' [varsayılan] çalışan grubunu girin**: varsayılanı kabul etmek için ENTER tuşuna basın
     * **' Hana-S1-DB3 ' [1] Konağı Için seçme rolleri** için: 1 (çalışan için)
     * **Ana bilgisayar ' Hana-S1-DB3 ' [varsayılan] Için konak yük devretme grubu girin**: varsayılanı kabul etmek için ENTER tuşuna basın
     * **' Hana-S1-DB3 ' [<<assign automatically>>] konağının depolama bölümü numarasını girin**: varsayılanı kabul etmek için ENTER tuşuna basın
     * **' Hana-S1-DB3 ' [varsayılan] konağının çalışan grubunu girin**: varsayılanı kabul etmek için ENTER tuşuna basın
     * **Sistem Yöneticisi (hn1adm) parolası** için: parolayı girin
     * **SAP konak aracısı kullanıcısı (sapadm) parolasını girin**: parolayı girin
     * **SAP konak Aracısı Kullanıcı (sapadm) parolasını onaylayın**: parolayı girin
     * **Host Hana-S1-DB2** [Hana-S1-DB2] Için sertifika ana bilgisayar adı: varsayılanı kabul etmek için ENTER tuşuna basın
     * **Host Hana-S1-DB3** [Hana-S1-Db3] Için sertifika ana bilgisayar adı: varsayılanı kabul etmek için ENTER tuşuna basın
     * **Devam etmek Istiyor musunuz (e/h)**: Özeti doğrulayıp her şey iyi görünüyorsa **y** girin

9. **[2]** SITE 2 ' ye ikincil SAP HANA düğümlerini yüklemek için önceki adımı yineleyin.   

## <a name="configure-sap-hana-20-system-replication"></a>SAP HANA 2,0 sistem çoğaltmasını yapılandırma

1. **[1]** SITE 1 ' de sistem çoğaltmasını yapılandırın:

   Veritabanlarını **hn1** adm olarak yedekleme:

    ```bash
    hdbsql -d SYSTEMDB -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupSYS')"
    hdbsql -d HN1 -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupHN1')"
    ```

   Sistem PKI dosyalarını ikincil siteye kopyalayın:

    ```bash
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/data/SSFS_HN1.DAT hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/data/
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/key/SSFS_HN1.KEY  hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/key/
    ```

   Birincil siteyi oluşturun:

    ```bash
    hdbnsutil -sr_enable --name=HANA_S1
    ```

2. **[2]** SITE 2 ' de sistem çoğaltmasını yapılandırın:
    
   Sistem çoğaltmasını başlatmak için ikinci siteyi kaydedin. <hanasid adm olarak aşağıdaki komutu çalıştırın \> :

    ```bash
    sapcontrol -nr 03 -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=hana-s1-db1 --remoteInstance=03 --replicationMode=sync --name=HANA_S2
    sapcontrol -nr 03 -function StartSystem
    ```

3. **[1]** çoğaltma durumunu denetleme

   Çoğaltma durumunu denetleyin ve tüm veritabanları eşitlenene kadar bekleyin.

    ```bash
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    # | Database | Host          | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
    # |          |               |       |              |           |         |           | Host          | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
    # | -------- | ------------- | ----- | ------------ | --------- | ------- | --------- | ------------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
    # | HN1      | hana-s1-db3   | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db3   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | SYSTEMDB | hana-s1-db1   | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1   |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1   |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db2   | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db2   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    #
    # status system replication site "2": ACTIVE
    # overall system replication status: ACTIVE
    #
    # Local System Replication State
    #   
    # mode: PRIMARY
    # site id: 1
    # site name: HANA_S1
    ```

4. **[1, 2]** Hana sistem çoğaltması sanal ağ arabirimlerine yönlendirilse, Hana sistem çoğaltması için iletişim sağlamak üzere Hana yapılandırmasını değiştirin.   
   - Her iki sitede de HANA 'yı durdur
    ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem HDB
    ```

   - HANA sistem çoğaltması için konak eşlemesini eklemek üzere global.ini Düzenle: alt ağdan IP adreslerini kullanın `hsr` .  
    ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [system_replication_hostname_resolution]
    10.23.1.202 = hana-s1-db1
    10.23.1.203 = hana-s1-db2
    10.23.1.204 = hana-s1-db3
    10.23.1.205 = hana-s2-db1
    10.23.1.206 = hana-s2-db2
    10.23.1.207 = hana-s2-db3
    ```

   - Her iki sitede de HANA 'yı Başlat
   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem HDB
   ```

   Daha fazla bilgi için bkz. [sistem çoğaltma Için konak adı çözümlemesi](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/1.0.12/en-US/c0cba1cb2ba34ec89f45b48b2157ec7b.html).  

5. **[Ah]** Güvenlik duvarını yeniden etkinleştirin.  
   - Güvenlik duvarını yeniden etkinleştirin
       ```bash
       # Execute as root
       systemctl start firewalld
       systemctl enable firewalld
       ```

   - Gerekli güvenlik duvarı bağlantı noktalarını açın. HANA örnek numaranız için bağlantı noktalarını ayarlamanız gerekecektir.  

       > [!IMPORTANT]
       > HANA düğüm iletişimine ve istemci trafiğine izin vermek için güvenlik duvarı kuralları oluşturun. Gerekli bağlantı noktaları, [Tüm sap ürünlerinin TCP/IP bağlantı noktalarında](https://help.sap.com/viewer/ports)listelenir. Aşağıdaki komutlar yalnızca bir örnektir. Bu senaryoda, kullanılan sistem numarası 03.

       ```bash
        # Execute as root
        sudo firewall-cmd --zone=public --add-port=30301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30301/tcp
        sudo firewall-cmd --zone=public --add-port=30303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30303/tcp
        sudo firewall-cmd --zone=public --add-port=30306/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30306/tcp
        sudo firewall-cmd --zone=public --add-port=30307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30307/tcp
        sudo firewall-cmd --zone=public --add-port=30313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30313/tcp
        sudo firewall-cmd --zone=public --add-port=30315/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30315/tcp
        sudo firewall-cmd --zone=public --add-port=30317/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30317/tcp
        sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30340/tcp
        sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30341/tcp
        sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30342/tcp
        sudo firewall-cmd --zone=public --add-port=1128/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1128/tcp
        sudo firewall-cmd --zone=public --add-port=1129/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1129/tcp
        sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40302/tcp
        sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40301/tcp
        sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40307/tcp
        sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40303/tcp
        sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40340/tcp
        sudo firewall-cmd --zone=public --add-port=50313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50313/tcp
        sudo firewall-cmd --zone=public --add-port=50314/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50314/tcp
        sudo firewall-cmd --zone=public --add-port=30310/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30310/tcp
        sudo firewall-cmd --zone=public --add-port=30302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30302/tcp
       ```

## <a name="create-a-pacemaker-cluster"></a>Paceoluşturucu kümesi oluşturma

Bu HANA sunucusu için temel bir Paceoluşturucu kümesi oluşturmak üzere [Azure 'daki Red Hat Enterprise Linux Paceyapıcısı ayarlama](high-availability-guide-rhel-pacemaker.md) bölümündeki adımları izleyin.
Kümedeki çoğunluk Oluşturucu dahil olmak üzere tüm sanal makineleri dahil edin.  

> [!IMPORTANT]
> `quorum expected-votes`İki düğümlü bir küme olmadığından 2 olarak ayarlanmamış.  
> Küme özelliğinin etkinleştirildiğinden emin olun `concurrent-fencing`  , böylece düğüm sınırlama serisi kaldırılamaz.   

## <a name="create-file-system-resources"></a>Dosya sistemi kaynakları oluşturma

1. **[1, 2]** her iki çoğaltma sitesinde SAP HANA durdurun. <SID adm olarak yürütün \> .  

    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

2. **[Ah]** `/hana/shared`Tüm Hana DB VM 'lerinde yüklenmek üzere geçici olarak bağlanmış olan dosya sistemini kaldır. Bağlamadan önce dosya sistemini kullanan tüm işlem ve oturumları durdurmanız gerekecektir. 
 
    ```bash
    umount /hana/shared 
    ```

3. **[1]** dosya sistemi kümesi kaynaklarını `/hana/shared` devre dışı durumda için oluşturun. `--disabled`Bağlar etkinleştirilmeden önce konum kısıtlamalarını tanımlamanız gerektiğinden, bu seçenek ile kaynaklar oluşturulur.  

    ```bash
    # /hana/shared file system for site 1
    pcs resource create fs_hana_shared_s1 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1  directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # /hana/shared file system for site 2   
    pcs resource create fs_hana_shared_s2 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1 directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # clone the /hana/shared file system resources for both site1 and site2
    pcs resource clone fs_hana_shared_s1 meta clone-node-max=1 interleave=true
    pcs resource clone fs_hana_shared_s2 meta clone-node-max=1 interleave=true
    ```
 
   `OCF_CHECK_LEVEL=20` özniteliği izleyici işlemine eklenir, böylece izleme işlemleri dosya sisteminde okuma/yazma testi gerçekleştirir. Bu öznitelik olmadan, izleme işlemi yalnızca dosya sisteminin takıldığını doğrular. Bu bir sorun olabilir, çünkü bağlantı kaybediliyorsa dosya sistemi bağlı kalabilir, ancak erişilmez olmasına karşın.  

   `on-fail=fence` öznitelik, izleme işlemine de eklenir. Bu seçenekle, bir düğümde izleyici işlemi başarısız olursa, o düğüm hemen bir daha başarılı olur. Bu seçenek olmadan, varsayılan davranış, başarısız kaynağa bağlı tüm kaynakları durdurmak, sonra başarısız kaynağı yeniden başlatmak ve ardından başarısız kaynağa bağlı tüm kaynakları başlatmak olur. Bir SAPHana kaynağı başarısız kaynağa bağlı olduğunda bu davranış uzun zaman alabilir, ancak aynı zamanda tamamen başarısız olabilir. HANA ikililerini tutan NFS paylaşımının erişilemez olması halinde SAPHana kaynağı başarıyla durdurulamıyor.  

4. **[1]** düğüm özniteliklerini yapılandırın ve doğrulayın. Çoğaltma sitesi 1 ' deki tüm SAP HANA DB düğümlerine öznitelik atanır `S1` ve çoğaltma sitesi 2 ' deki tüm SAP HANA DB düğümlerine öznitelik atanır `S2` .  

    ```bash
    # HANA replication site 1
    pcs node attribute hana-s1-db1 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db2 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db3 NFS_SID_SITE=S1
    # HANA replication site 2
    pcs node attribute hana-s2-db1 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db2 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db3 NFS_SID_SITE=S2
    #To verify the attribute assignment to nodes execute
    pcs node attribute
    ```

5. **[1]** , NFS dosya sistemlerinin nereye takıldığını ve dosya sistemi kaynaklarını etkinleştirmek için kısıtlamaları yapılandırın.  
    ```bash
    # Configure the constraints
    pcs constraint location fs_hana_shared_s1-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S1
    pcs constraint location fs_hana_shared_s2-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S2
    # Enable the file system resources
    pcs resource enable fs_hana_shared_s1
    pcs resource enable fs_hana_shared_s2
    ```

   Dosya sistemi kaynaklarını etkinleştirdiğinizde, küme `/hana/shared` dosya sistemlerini bağlayacaktır.
 
6. **[Ah]** ANF birimlerinin, `/hana/shared` her iki sitedeki tüm Hana DB VM 'lerinde takılı olduğundan emin olun.

    ```bash
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

7. **[1]** öznitelik kaynaklarını yapılandırın. `true`IÇIN NFS takmalar bağlandığında, özniteliklerini olarak ayarlanacak kısıtlamaları yapılandırın `hana/shared` .  

    ```bash
    # Configure the attribure resources
    pcs resource create hana_nfs_s1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s1_active
    pcs resource create hana_nfs_s2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s2_active
    # Clone the attribure resources
    pcs resource clone hana_nfs_s1_active meta clone-node-max=1 interleave=true
    pcs resource clone hana_nfs_s2_active meta clone-node-max=1 interleave=true
    # Configure the constraints, which will set the attribute values
    pcs constraint order fs_hana_shared_s1-clone then hana_nfs_s1_active-clone
    pcs constraint order fs_hana_shared_s2-clone then hana_nfs_s2_active-clone
    ```

   > [!TIP]
   > Yapılandırmanız,/, NFS 'nin bağlı olduğu diğer dosya sistemlerini de içeriyorsa, `hana/shared` `sequential=false` dosya sistemleri arasında bir sıralama bağımlılığı olmaması için seçeneği dahil edin. Tüm NFS bağlı dosya sistemleri, karşılık gelen öznitelik kaynağıyla önce başlamalı, ancak birbirleriyle ilgili herhangi bir sırada başlaması gerekmez. Daha fazla bilgi için [, Hana dosya SISTEMLERI NFS paylaşımları olduğunda bir paceoluşturucu kümesinde Nasıl yaparım? SAP HANA Scale-Out HSR yapılandırma](https://access.redhat.com/solutions/5423971)konusuna bakın.  

8. **[1]** Hana küme kaynaklarının oluşturulmasına yönelik hazırlık bölümünde paceyapıcısı bakım moduna yerleştirirken.  
    ```bash
    pcs property set maintenance-mode=true
    ```

## <a name="create-sap-hana-cluster-resources"></a>SAP HANA kümesi kaynakları oluşturma

1. **[A]** Hana genişleme kaynak aracısını, çoğunluk Oluşturucu dahil olmak üzere tüm küme düğümlerine yükler.    

    ```bash
    yum install -y resource-agents-sap-hana-scaleout 
    ```


   > [!NOTE]
   > İşletim sistemi sürümünüz için desteklenen en düşük sürüm paketine yönelik [olarak, bir kümedeki SAP HANA yönetiminin RHEL ha kümeleri Için destek ilkelerine](https://access.redhat.com/articles/3397471) danışın `resource-agents-sap-hana-scaleout` .  

2. **[1, 2]** Hana "sistem çoğaltma kancasını" yükler. Kanca her bir sistem çoğaltma sitesinde bir HANA DB düğümüne yüklenmelidir. SAP HANA hala çalışmıyor olmalıdır.        

   1. Kancayı hazırlama `root` 
    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR-ScaleOut/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. Ayarlamayı `global.ini`
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

3. **[Ah]** Küme, <SID adm için küme düğümünde sudoers yapılandırması gerektirir \> . Bu örnekte, yeni bir dosya oluşturularak elde edilen. Komutları olarak yürütün `root` .    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # SAPHanaSR-ScaleOut needs for srHook
     Cmnd_Alias SOK = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SOK -t crm_config -s SAPHanaSR
     Cmnd_Alias SFAIL = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SFAIL -t crm_config -s SAPHanaSR
     hn1adm ALL=(ALL) NOPASSWD: SOK, SFAIL
     EOF
    ```

4. **[1, 2]** her iki çoğaltma sitesinde de SAP HANA başlatın. <SID adm olarak yürütün \> .  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

5. **[1]** kanca yüklemesini doğrulayın. \>ETKIN Hana sistem çoğaltma sitesinde <SID adm olarak yürütün.   

    ```bash
    cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*

     # Example entries
     # 2020-07-21 22:04:32.364379 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:46.905661 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.092016 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.782774 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:53.117492 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:06:35.599324 ha_dr_SAPHanaSR SOK
    ```

6. **[1]** Hana küme kaynaklarını oluşturun. Aşağıdaki komutları olarak yürütün `root` .    
   1. Kümenin zaten bakım modunda olduğundan emin olun.  
    
   2. Ardından, HANA topolojisi kaynağını oluşturun.  
      RHEL **7. x** kümesi oluşturuyorsanız aşağıdaki komutları kullanın:  
      ```bash
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopologyScaleOut \
       SID=HN1 InstanceNumber=03 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

      RHEL **8. x** kümesi oluşturuyorsanız aşağıdaki komutları kullanın:  
      ```bash
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopology \
       SID=HN1 InstanceNumber=03 meta clone-node-max=1 interleave=true \
       op methods interval=0s timeout=5 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

   3. Ardından, HANA örnek kaynağını oluşturun.  
      > [!NOTE]
      > Bu makale, Microsoft 'un artık kullandığı bir terim olan *bağımlı* dönem başvuruları içerir. Terim yazılımlardan kaldırıldığında, bu makaleden kaldıracağız.  
 
      RHEL **7. x** kümesi oluşturuyorsanız aşağıdaki komutları kullanın:    
      ```bash
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource master msl_SAPHana_HN1_HDB03 SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```

      RHEL **8. x** kümesi oluşturuyorsanız aşağıdaki komutları kullanın:  
      ```bash
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op demote interval=0s timeout=320 op methods interval=0s timeout=5 \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource promotable SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```
      > [!IMPORTANT]
      > Başarısız olan birincil örneğin otomatik olarak ikincil olarak kaydolmasını engellemek için AUTOMATED_REGISTER yalnızca **Hayır** olarak ayarlamanız önerilir. Yük devretme testleri başarıyla tamamlandıktan sonra, AUTOMATED_REGISTER ' i **Evet** olarak ayarlayın, böylelikle sistem çoğaltması otomatik olarak devam edebilir. 

   4. Sanal IP ve ilişkili kaynaklar oluşturun.  
      ```bash
      pcs resource create vip_HN1_03 ocf:heartbeat:IPaddr2 ip=10.23.0.18 op monitor interval="10s" timeout="20s"
      sudo pcs resource create nc_HN1_03 azure-lb port=62503
      sudo pcs resource group add g_ip_HN1_03 nc_HN1_03 vip_HN1_03
      ```

   5. Küme kısıtlamalarını oluşturma  
      RHEL **7. x** kümesi oluşturuyorsanız aşağıdaki komutları kullanın:  
      ```bash
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then msl_SAPHana_HN1_HDB03

      pcs constraint colocation add g_ip_HN1_03 with master msl_SAPHana_HN1_HDB03 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```
 
      RHEL **8. x** kümesi oluşturuyorsanız aşağıdaki komutları kullanın:  
      ```bash
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then SAPHana_HN1_HDB03-clone

      pcs constraint colocation add g_ip_HN1_03 with master SAPHana_HN1_HDB03-clone 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```

7. **[1]** kümeyi bakım modundan çıkar. Küme durumunun tamam olduğundan ve tüm kaynakların başlatıldığından emin olun.  
    ```bash
    sudo pcs property set maintenance-mode=false
    #If there are failed cluster resources, you may need to run the next command
    pcs resource cleanup
    ```
  
   > [!NOTE]
   > Yukarıdaki yapılandırmadaki zaman aşımları yalnızca örnektir ve belirli HANA kurulumuna uyarlanmasını gerektirebilir. Örneğin, SAP HANA veritabanını başlatmak daha uzun sürerse Başlangıç zaman aşımını artırmanız gerekebilir.
  
## <a name="test-sap-hana-failover"></a>Test SAP HANA yük devretme 

1. Bir teste başlamadan önce, kümeyi denetleyin ve sistem çoğaltma durumunu SAP HANA.  

   a. Başarısız küme eylemi olmadığını doğrulayın  
     ```bash
     #Verify that there are no failed cluster actions
     pcs status
     # Example
     #Stack: corosync
     #Current DC: hana-s-mm (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
     #Last updated: Thu Sep 24 06:00:20 2020
     #Last change: Thu Sep 24 05:59:17 2020 by root via crm_attribute on hana-s1-db1
     #
     #7 nodes configured
     #45 resources configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
     #Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
     #    Started: [ hana--s1-db1 hana-s1-db2 hana-s1-db3 ]
     #Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
     #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
     #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
     #Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
     #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
     #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
     #    Masters: [ hana-s1-db1 ]
     #    Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Resource Group: g_ip_HN1_03
     #    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
     #    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
     ```

   b. SAP HANA sistem çoğaltmasının eşitlenmiş olduğunu doğrulama

      ```bash
      # Verify HANA HSR is in sync
      sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
      #| Database | Host        | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary| Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
      #|          |             |       |              |           |         |           | Host          | Port     | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
      #| -------- | ----------- | ----- | ------------ | --------- | ------- | --------- | ------------- | -------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
      #| HN1      | hana-s1-db3 | 30303 | indexserver  |         5 |       2 | HANA_S1   | hana-s2-db3 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db2 | 30303 | indexserver  |         4 |       2 | HANA_S1   | hana-s2-db2 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |  
      #| SYSTEMDB | hana-s1-db1 | 30301 | nameserver   |         1 |       2 | HANA_S1   | hana-s2-db1 |     30301  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30307 | xsengine     |         2 |       2 | HANA_S1   | hana-s2-db1 |     30307  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30303 | indexserver  |         3 |       2 | HANA_S1   | hana-s2-db1 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |

      #status system replication site "1": ACTIVE
      #overall system replication status: ACTIVE

      #Local System Replication State
      #~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

      #mode: PRIMARY
      #site id: 1
      #site name: HANA_S1
      ```

2. Bir düğüm NFS paylaşımında () erişimi kaybettiğinde hata senaryosu için küme yapılandırmasını doğrulayın `/hana/shared` .  

   SAP HANA kaynak aracıları, `/hana/shared` Yük devretme sırasında işlemleri gerçekleştirmek için üzerinde depolanan ikili dosyalara bağımlıdır. Dosya sistemi `/hana/shared` , sunulan YAPıLANDıRMADA NFS üzerinden bağlanır. Gerçekleştirilebileceği bir test, `/hana/shared` dosya sistemini *salt okuma* olarak yeniden bağlamak içindir. Bu yaklaşım, `/hana/shared` etkin sistem çoğaltma sitesinde erişim kaybı durumunda kümenin yük devredebildiğini doğrular.  

   **Beklenen sonuç**: `/hana/shared` *salt okuma* olarak yeniden bağlama yaptığınızda dosya sisteminde okuma/yazma işlemi gerçekleştiren izleme işlemi başarısız olur, çünkü dosya sistemine yazamaz ve Hana kaynak yük devretmesini tetikleyecektir. HANA düğümünüz NFS paylaşımının erişimini kaybettiğinde aynı sonuç beklenmektedir.  
     
   Veya çalıştırarak küme kaynaklarının durumunu kontrol edebilirsiniz `crm_mon` `pcs status` . Teste başlamadan önce kaynak durumu:
      ```bash
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured

      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      #
      #Active resources:

      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s1-db1 ]
      #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
      ```

   `/hana/shared`Birincil çoğaltma sitesi VM 'lerinden birinde hata benzetimi yapmak için aşağıdaki komutu yürütün:
      ```bash
      # Execute as root 
      mount -o ro /hana/shared
      # Or if the above command returns an error
      sudo mount -o ro 10.23.1.7/HN1-shared-s1 /hana/shared
      ```
  
   ' A erişimi kesilen HANA VM, `/hana/shared` küme yapılandırmasına bağlı olarak yeniden başlatılması veya durdurulması gerekir. Küme kaynakları diğer HANA sistem çoğaltma sitesine geçirilir.  
         
   Küme VM üzerinde başlatılmamışsa, yeniden başlatıldığında, şunu yürüterek kümeyi başlatın: 

      ```bash
      # Start the cluster 
      pcs cluster start
      ```
   
   Küme başladığında, dosya sistemi `/hana/shared` otomatik olarak bağlanır.     
   AUTOMATED_REGISTER = "false" ayarlarsanız, ikincil sitede SAP HANA sistem çoğaltmasını yapılandırmanız gerekir. Bu durumda, SAP HANA ikincil olarak yeniden yapılandırmak için bu komutları çalıştırabilirsiniz.   

      ```bash
      # Execute on the secondary 
      su - hn1adm
      # Make sure HANA is not running on the secondary site. If it is started, stop HANA
      sapcontrol -nr 03 -function StopWait 600 10
      # Register the HANA secondary site
      hdbnsutil -sr_register --name=HANA_S1 --remoteHost=hana-s2-db1 --remoteInstance=03 --replicationMode=sync
      # Switch back to root and cleanup failed resources
      pcs resource cleanup SAPHana_HN1_HDB03
      ```

   Sınama sonrasında kaynakların durumu: 

      ```bash
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured
    
      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      
      #Active resources:
    
      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s2-db1 ]
      #     Slaves: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s2-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
      ```


SAP HANA küme yapılandırmasını kapsamlı olarak test etmenizi öneririz. Ayrıca, [RHEL üzerindeki Azure VM 'lerinde SAP HANA Için ha](./sap-hana-high-availability-rhel.md#test-the-cluster-setup)'da belgelenen testleri gerçekleştirerek.


## <a name="next-steps"></a>Sonraki adımlar

* [SAP için Azure sanal makineleri planlama ve uygulama][planning-guide]
* [SAP için Azure sanal makineleri dağıtımı][deployment-guide]
* [SAP için Azure sanal makineleri DBMS dağıtımı][dbms-guide]
* [SAP HANA için Azure NetApp Files üzerinde NFS v4.1 birimleri](./hana-vm-operations-netapp.md)
* Azure VM 'lerinde SAP HANA olağanüstü durum kurtarma için yüksek kullanılabilirlik ve plan oluşturma hakkında bilgi edinmek için bkz. [Azure sanal makinelerinde (VM) SAP HANA yüksek kullanılabilirliği][sap-hana-ha].