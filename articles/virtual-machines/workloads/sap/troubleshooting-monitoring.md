---
title: Azure 'da SAP HANA izleme (büyük örnekler) | Microsoft Docs
description: Azure 'da SAP HANA izleyin (büyük örnekler).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 246b9398824597ec337ee9e9ea3dc24267311f60
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101669595"
---
# <a name="how-to-monitor-sap-hana-large-instances-on-azure"></a>Azure 'da SAP HANA (büyük örnekler) izleme

Azure 'daki SAP HANA (büyük örnekler) diğer bir IaaS dağıtımından farklı değildir. işletim sisteminin ve uygulamanın ne yaptığını ve uygulamaların aşağıdaki kaynakları nasıl tükettiği hakkında izlemeniz gerekir:

- CPU
- Bellek
- Ağ bant genişliği
- Disk alanı

Azure sanal makineler ile yukarıda adlandırılan kaynak sınıflarının yeterli olup olmadığını veya boşaldığını belirlemeniz gerekir. Farklı sınıfların her biri hakkında daha fazla ayrıntı aşağıda verilmiştir:

**CPU kaynak tüketimi:** Belirli bir iş yükü için HANA 'ya karşı tanımlanan SAP 'nin, bellekte depolanan veriler aracılığıyla çalışmak için yeterli CPU kaynakları olduğundan emin olmak için zorunlu olduğu oran. Yine de, HANA 'nın eksik dizinler veya benzer sorunlar nedeniyle sorgu yürüten birçok CPU kullandığı durumlar olabilir. Bu, HANA büyük örnek biriminin CPU kaynak tüketimini, ayrıca belirli HANA Hizmetleri tarafından tüketilen CPU kaynaklarını izlemeniz gerektiği anlamına gelir.

**Bellek tüketimi:** , HANA içinden ve birim üzerinde HANA dışında izlemenin önem taşımaktadır. HANA 'da, gereken SAP boyutlandırma yönergeleri içinde kalmak için verilerin HANA ile ayrılmış belleği nasıl tükettiğini izleyin. Ayrıca, daha fazla yüklü HANA yazılımının çok fazla bellek tüketmediğinden emin olmak için büyük örnek düzeyinde bellek tüketimini izlemek ve bu nedenle bellek için HANA ile rekabet etmek istiyorsunuz.

**Ağ bant genişliği:** Azure VNet ağ geçidi, Azure VNet 'e taşınan veri bant genişliği ile sınırlıdır. bu nedenle, bir sanal ağ içindeki tüm Azure VM 'Leri tarafından alınan verileri, seçtiğiniz Azure Gateway SKU 'sunun sınırlarına nasıl kapaleyeceğinizi anlamak için yardımcı olur. HANA büyük örnek biriminde, gelen ve giden ağ trafiğini izlemek ve zaman içinde işlenen birimleri izlemek için bir fikir verir.

**Disk alanı:** Disk alanı tüketimi genellikle zaman içinde artar. En yaygın nedenler şunlardır: veri hacmi arttıkça, işlem günlüğü yedeklemelerinin yürütülmesi, izleme dosyalarını depolama ve depolama anlık görüntüleri gerçekleştiriliyor. Bu nedenle, disk alanı kullanımını izlemek ve HANA büyük örnek birimiyle ilişkili disk alanını yönetmek önemlidir.

HANA büyük örneklerin **tür II SKU 'ları** için, sunucu önceden yüklenmiş sistem tanılama araçlarıyla birlikte gelir. Sistem durumu denetimini gerçekleştirmek için bu tanılama araçlarından yararlanabilirsiniz. /Var/log/health_check konumunda sistem durumu denetimi günlük dosyasını oluştururken aşağıdaki komutu çalıştırın.
```
/opt/sgi/health_check/microsoft_tdi.sh
```
Bir sorunu gidermek için Microsoft Desteği takımla çalışırken, bu tanılama araçlarını kullanarak günlük dosyalarını da sağlamanız istenebilir. Aşağıdaki komutu kullanarak dosyayı zip altına alabilirsiniz.
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

**Sonraki adımlar**

- [Azure 'da SAP HANA (büyük örnekler) izleme](./hana-monitor-troubleshoot.md)bölümüne bakın.
