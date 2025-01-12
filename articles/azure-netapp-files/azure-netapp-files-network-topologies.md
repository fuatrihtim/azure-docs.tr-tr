---
title: Azure NetApp Files ağ planlamasına yönelik yönergeler | Microsoft Docs
description: Azure NetApp Files kullanarak etkin bir ağ mimarisi tasarlamanıza yardımcı olabilecek yönergeleri açıklar.
services: azure-netapp-files
documentationcenter: ''
author: ram-kakani
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: ramakk
ms.openlocfilehash: 96d8ba058a33d408ec2ee2a1adfba9011f393da9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96184493"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Azure NetApp Files ağ planlaması yönergeleri

Ağ mimarisi planlaması, herhangi bir uygulama altyapısını tasarlamanın temel bir öğesidir. Bu makale, Azure NetApp Files zengin yetilerinden yararlanmak üzere iş yükleriniz için etkin bir ağ mimarisi tasarlamanıza yardımcı olur.

Azure NetApp Files birimler, Azure sanal ağınız içinde [Temsilcili bir alt ağ](../virtual-network/virtual-network-manage-subnet.md) olarak adlandırılan özel bir amaç alt ağında yer almak üzere tasarlanmıştır. Bu nedenle, birimlere doğrudan sanal ağınızdan, aynı bölgedeki eşlenmiş VNET 'lerden veya bir sanal ağ geçidi üzerinden (ExpressRoute veya VPN Gateway) Şirket içinden erişebilirsiniz. Alt ağ Azure NetApp Files için ayrılmıştır ve diğer Azure hizmetleriyle veya Internet 'e yönelik bağlantı yoktur.

## <a name="considerations"></a>Dikkat edilmesi gerekenler  

Azure NetApp Files ağını planlarken bazı noktaları anlamanız gerekir.

### <a name="constraints"></a>Kısıtlamalar

Aşağıdaki özellikler Şu anda Azure NetApp Files için desteklenmiyor: 

* Temsilci alt ağına uygulanan ağ güvenlik grupları (NSG 'ler)
* Temsilci alt ağına uygulanan Kullanıcı tanımlı yollar (UDRs)
* Azure NetApp Files arabirimindeki Azure ilkeleri (örneğin, Özel adlandırma ilkeleri)
* Azure NetApp Files trafiği için yük dengeleyiciler
* Azure Sanal WAN 
* Bölgesel olarak yedekli sanal ağ geçitleri (az olan ağ geçidi SKU 'Ları) 
* Etkin/etkin sanal ağ GWs 
* Çift yığın (IPv4 ve IPv6) VNet

Aşağıdaki ağ kısıtlamaları Azure NetApp Files için geçerlidir:

* Azure NetApp Files (eşlenen sanal ağlar dahil) bir VNet 'te kullanılmakta olan IP sayısı 1000 ' i aşamaz. Müşteri ölçek taleplerini karşılamak için bu sınırı artırmak üzere çalışıyoruz. 
* Her bir Azure sanal ağında (VNet), Azure NetApp Files için yalnızca bir alt ağ atanabilir.


### <a name="supported-network-topologies"></a>Desteklenen ağ topolojileri

Aşağıdaki tabloda Azure NetApp Files tarafından desteklenen ağ topolojileri açıklanmaktadır.  Ayrıca, desteklenmeyen topolojilerle ilgili geçici çözümleri açıklar. 

|    Topolojiler    |    Desteklenir    |     Geçici çözüm    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    Yerel VNet 'teki birime bağlantı    |    Yes    |         |
|    Eşlenen VNet 'teki birime bağlantı (aynı bölge)    |    Yes    |         |
|    Eşlenmiş VNet 'teki birime bağlantı (çapraz bölge veya genel eşleme)    |    No    |    Yok    |
|    ExpressRoute ağ geçidi üzerinden bir birime bağlantı    |    Yes    |         |
|    Şirket içinden bir bağlantı noktasında ExpressRoute ağ geçidi ile VNet eşlemesi ve ağ geçidi geçişi ile VNet eşlemesi    |    Yes    |        |
|    Şirket içinden VPN Gateway üzerinden bağlı olan VNet 'teki bir birime bağlantı    |    Yes    |         |
|    Şirket içinden VPN Gateway ve VNet eşlemesi ile ağ geçidi geçişi üzerinden bağlı olan VNet 'teki bir birime bağlantı    |    Yes    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Azure NetApp Files birimleri için sanal ağ

Bu bölümde, sanal ağ planlamasında size yardımcı olacak kavramlar açıklanmaktadır.

### <a name="azure-virtual-networks"></a>Azure sanal ağları

Azure NetApp Files bir birimi sağlamadan önce, bir Azure sanal ağı (VNet) oluşturmanız veya aboneliğinizde zaten mevcut olan bir tane kullanmanız gerekir. VNet birimin ağ sınırını tanımlar.  Sanal ağlar oluşturma hakkında daha fazla bilgi için bkz. [Azure sanal ağ belgeleri](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Alt ağlar

Alt ağlar sanal ağı, içindeki Azure kaynakları tarafından kullanılabilen ayrı adres alanlarına bölüler.  Azure NetApp Files birimler, [Temsilcili alt ağ](../virtual-network/virtual-network-manage-subnet.md)adlı özel amaçlı bir alt ağda yer alır. 

Alt ağ temsili, alt ağda hizmete özel kaynaklar oluşturmak için Azure NetApp Files hizmetine açık izinler verir.  Hizmeti dağıtmaya yönelik benzersiz bir tanımlayıcı kullanır. Bu durumda, Azure NetApp Files bağlantısının etkinleştirilmesi için bir ağ arabirimi oluşturulur.

Yeni bir sanal ağ kullanıyorsanız, [Azure NetApp Files için bir alt ağ devretmek](azure-netapp-files-delegate-subnet.md)içindeki talimatları izleyerek bir alt ağ oluşturabilir ve alt ağı Azure NetApp Files. Ayrıca, diğer hizmetlere atanmış olmayan var olan bir boş alt ağın yetkisini de atayabilirsiniz.

VNet başka bir sanal ağ ile eşlenirse, VNet adres alanını genişletebilirsiniz. Bu nedenle, yeni atanan alt ağın VNet adres alanı içinde oluşturulması gerekir. Adres alanını genişletmeniz gerekiyorsa, adres alanını genişletmeden önce VNet eşlemesini silmeniz gerekir.

### <a name="udrs-and-nsgs"></a>UDRs ve NSG 'ler

Kullanıcı tanımlı yollar (UDRs) ve ağ güvenlik grupları (NSG 'ler) Azure NetApp Files için temsilci alt ağlarda desteklenmez. Ancak, Azure NetApp Files için Temsilcili alt ağ ile aynı VNet içinde bile UDRs ve NSG 'leri diğer alt ağlara uygulayabilirsiniz.

* UDRs daha sonra diğer alt ağlardan gelen trafik akışlarını Azure NetApp Files Temsilcili alt ağa tanımlar. Bu, sistem yollarını kullanarak bunun Azure NetApp Files trafik akışına doğru diğer alt ağlara hizalandığından emin olmaya yardımcı olur.  
* NSG 'ler, Azure NetApp Files Temsilcili alt ağdan gelen ve giden trafiğe izin verir ya da reddeder. 

## <a name="azure-native-environments"></a>Azure yerel ortamları

Aşağıdaki diyagramda Azure yerel bir ortam gösterilmektedir:

![Azure-yerel ağ ortamı](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>Yerel VNet

Temel senaryo, aynı VNet 'teki bir sanal makineden (VM) Azure NetApp Files bir birim oluşturmak veya bu birime bağlanmak. Yukarıdaki diyagramda VNet 2 için, 1. birim, temsilci bir alt ağda oluşturulur ve varsayılan alt ağda VM 1 ' de bağlanabilir.

### <a name="vnet-peering"></a>VNet eşlemesi

Aynı bölgede diğer her kaynağa erişmesi gereken ek VNET varsa, sanal ağlar Azure altyapısı aracılığıyla güvenli bağlantı sağlamak için [VNET eşlemesi](../virtual-network/virtual-network-peering-overview.md) kullanılarak bağlanabilir. 

Yukarıdaki diyagramda VNet 2 ve VNet 3 ' ü göz önünde bulundurun. VM 1 ' in VM 2 veya Volume 2 ' ye bağlanması gerekiyorsa veya VM 2 ' nin VM 1 veya birim 1 ' e bağlanması gerekiyorsa, VNET 2 ile VNet 3 arasında VNet eşlemesini etkinleştirmeniz gerekir. 

Ayrıca, VNET 1 ' in VNet 2 ' ye eşlenme ve VNET 2 ' nin aynı bölgedeki VNet 3 ile eşlenme senaryosu da göz önünde bulundurun. VNET 1 ' deki kaynaklar VNet 2 ' deki kaynaklara bağlanabilir ancak VNET 1 ve VNet 3 eşlenmediği müddetçe VNet 3 ' teki kaynaklara bağlanamaz. 

Yukarıdaki diyagramda, VM 3 ' ün birim 1 ' e bağlanabilmesine karşın VM 4 ' te birim 2 ' ye bağlanamaz.  Bunun nedeni, bağlı olan VNET 'lerin eşlenmediğinden ve _geçiş yönlendirmenin VNET eşlemesi üzerinden desteklenmemelidir_.

## <a name="hybrid-environments"></a>Karma ortamlar

Aşağıdaki diyagramda karma bir ortam gösterilmektedir: 

![Karma ağ ortamı](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

Karma senaryoda, şirket içi veri merkezlerinden uygulamaların Azure 'daki kaynaklara erişmesi gerekir.  Bu, veri merkezinizi Azure 'a genişletmek veya Azure yerel hizmetleri 'ni veya olağanüstü durum kurtarma için kullanmak istediğiniz durumdur. Siteden siteye VPN veya ExpressRoute aracılığıyla şirket içi birden çok kaynağı Azure 'daki kaynaklara bağlama hakkında bilgi için bkz. [VPN Gateway planlama seçenekleri](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) .

Karma hub tabanlı topolojide Azure 'daki hub VNet, şirket içi ağınıza yönelik merkezi bir bağlantı noktası görevi görür. Bağlı bileşen hub 'iyle eşlenirler ve iş yüklerini yalıtmak için kullanılabilirler.

Yapılandırmaya bağlı olarak, şirket içi kaynakları hub ve bağlı bileşenler içindeki kaynaklara bağlayabilirsiniz.

Yukarıda gösterilen topolojide şirket içi ağ, Azure 'daki bir hub VNet 'e bağlanır ve hub VNet ile aynı bölgede 2 ' ye ait VNET 'ler vardır.  Bu senaryoda, Azure NetApp Files birimleri için desteklenen bağlantı seçenekleri şunlardır:

* Şirket içi kaynaklar VM 1 ve VM 2, hub 'da bir siteden siteye VPN veya ExpressRoute devresi üzerinden birim 1 ' e bağlanabilir. 
* Şirket içi kaynaklar VM 1 ve VM 2, bir siteden siteye VPN ve bölgesel VNET eşlemesi üzerinden birim 2 veya birim 3 ' e bağlanabilir.
* Hub VNet 'teki VM 3, bağlı olan VNet 1 ' de 2. birim ve bağlı ağ VNet 2 ' de birim 3 ' e bağlanabilir.
* Sanal ağ VNet 1 ' den VM 4 ve bağlı ağ VNet 2 ' den VM 5, hub VNet 'teki birim 1 ' e bağlanabilir.
* Bağlı ağ VNet 1 ' deki VM 4, bağlı olan VNet 2 ' de birim 3 ' e bağlanamaz. Ayrıca, bağlı bileşen VNet2 içindeki VM 5, bağlı olan VNet 1 ' de birim 2 ' ye bağlanamaz. Bu durum, bağlı olan VNET 'lerin eşlenmediği ve _geçiş yönlendirmenin VNET eşlemesi üzerinden desteklenmediği_ için oluşur.
* Yukarıdaki mimaride, bağlı olan VNET 'te bir ağ geçidi varsa, hub 'daki ağ geçidi üzerinden bağlantı kurulurken ANF hacminin bağlantısı kaybedilir. Tasarıma göre, tercih, bağlı olan VNet 'teki ağ geçidine verilmelidir ve bu nedenle yalnızca söz konusu ağ geçidi üzerinden bağlanan makinelerin ANF birimine bağlanmasını sağlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Azure NetApp Files için bir alt ağı temsilci olarak belirleme](azure-netapp-files-delegate-subnet.md)