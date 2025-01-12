---
title: Azure HDInsight kümeniz için doğru VM boyutunu seçme
description: HDInsight kümeniz için doğru VM boyutunu seçme hakkında bilgi edinin.
keywords: VM boyutları, küme boyutları, küme yapılandırması
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 51043f0a1009994528783a1b56ec5ccec68e99b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98931789"
---
# <a name="selecting-the-right-vm-size-for-your-azure-hdinsight-cluster"></a>Azure HDInsight kümeniz için doğru VM boyutunu seçme

Bu makalede, HDInsight kümenizdeki çeşitli düğümlerin doğru VM boyutunu seçme açıklanmaktadır. 

CPU işleme, RAM boyutu ve ağ gecikmesi gibi bir sanal makinenin özelliklerinin iş yüklerinizin işlenmesini nasıl etkileyeceğini anlamak için başlayın. Daha sonra, uygulamanızı ve farklı VM aileleri için en iyi duruma getirilmiş ile nasıl eşleştiğini düşünün. Kullanmak istediğiniz VM ailesinin, dağıtmayı planladığınız küme türüyle uyumlu olduğundan emin olun. Her küme türü için desteklenen ve önerilen tüm VM boyutlarının listesi için bkz. [Azure HDInsight desteklenen düğüm yapılandırması](hdinsight-supported-node-configuration.md). Son olarak, bazı örnek iş yüklerini test etmek ve bu ailedeki SKU 'nun sizin için uygun olduğunu denetlemek için bir benchişaretleme işlemi kullanabilirsiniz.

Kümenizin depolama türünü veya küme boyutunu seçme gibi diğer yönlerini planlama hakkında daha fazla bilgi için bkz. [HDInsight kümeleri Için kapasite planlaması](hdinsight-capacity-planning.md).

## <a name="vm-properties-and-big-data-workloads"></a>VM özellikleri ve büyük veri iş yükleri

VM boyutu ve türü, CPU işleme gücü, RAM boyutu ve ağ gecikmesi tarafından belirlenir:

- CPU: VM boyutu çekirdek sayısını belirler. Daha fazla çekirdek, her bir düğümün elde edilebileceği paralel hesaplamanın derecesi artar. Ayrıca, bazı VM türlerinde daha hızlı çekirdek vardır.

- RAM: VM boyutu VM 'deki kullanılabilir RAM miktarını da belirler. Diskten okumak yerine verileri işlenmek üzere bellekte depolayan iş yükleri için, çalışan düğümlerinizin verilere sığacak kadar yeterli belleğe sahip olduğundan emin olun.

- Ağ: çoğu küme türü Için, küme tarafından işlenen veriler yerel diskte değildir, bunun yerine Data Lake Storage veya Azure depolama gibi bir harici depolama hizmetidir. Düğüm VM ve depolama hizmeti arasındaki ağ bant genişliğini ve aktarım hızını göz önünde bulundurun. Bir VM için kullanılabilen ağ bant genişliği genellikle daha büyük boyutlarda artar. Ayrıntılar için bkz. [VM boyutlarına genel bakış](../virtual-machines/sizes.md).

## <a name="understanding-vm-optimization"></a>VM iyileştirmesini anlama

Azure 'daki sanal makine aileleri farklı kullanım durumlarına uyacak şekilde iyileştirilmiştir. Aşağıdaki tabloda, en popüler kullanım durumlarının ve bunlarla eşleşen VM ailelerinin bazılarını bulabilirsiniz.

| Tür                     | Boyutlar           |    Açıklama       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Giriş düzeyi](../virtual-machines/sizes-general.md)          | A, AV2  | Geliştirme ve test gibi giriş düzeyi iş yükleri için en uygun CPU performansı ve bellek yapılandırmalarının olması gerekir. Bunlar ekonomik bir seçenektir ve Azure kullanmaya başlamak için düşük maliyetli bir seçenek sunar. |
| [Genel amaçlı](../virtual-machines/sizes-general.md)          | D, DSv2, Dv2  | Dengeli CPU/bellek oranı. Test ve geliştirme, küçük-orta büyüklükteki veritabanları ve küçük-orta büyüklükte trafik hacmine sahip web sunucuları için idealdir. |
| [İşlem için iyileştirilmiş](../virtual-machines/sizes-compute.md)        | F           | Yüksek CPU/bellek oranı. Orta trafikli web sunucuları, ağ araçları, toplu süreçler ve uygulama sunucuları için iyi.        |
| [Bellek için iyileştirilmiş](../virtual-machines/sizes-memory.md)         | Esv3, Ev3  | Yüksek bellek/CPU oranı. İlişkisel veritabanı sunucuları, orta veya büyük boyutlu önbellekler ve bellek içi analiz için idealdir.                 |

- HDInsight tarafından desteklenen bölgelerde kullanılabilir sanal makine örneklerinin fiyatlandırması hakkında bilgi için bkz. [HDInsight fiyatlandırması](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="cost-saving-vm-types-for-light-workloads"></a>Hafif iş yükleri için VM türlerini kaydetme maliyeti

Açık işleme gereksinimleriniz varsa, HDInsight kullanmaya başlamak için [F serisi](https://azure.microsoft.com/blog/f-series-vm-size/) iyi bir seçim olabilir. Daha düşük bir saatlik liste fiyatına sahip olan F Serisi, vCPU başına Azure İşlem Birimi (ACU) açısından fiyat-performans alanında Azure portföyündeki en iyi seçenektir.

Aşağıdaki tabloda, Fsv2 serisi VM 'lerle oluşturulabilecek küme türleri ve düğüm türleri açıklanmaktadır.

| Küme Türü | Sürüm | Çalışan düğümü | Baş düğüm | Zookeeper düğümü |
|---|---|---|---|---|
| Spark | Tümü | F4 ve üzeri | hayır | hayır |
| Hadoop | Tümü | F4 ve üzeri | hayır | hayır |
| Kafka | Tümü | F4 ve üzeri | hayır | hayır |
| HBase | Tümü | F4 ve üzeri | hayır | hayır |
| LLAP | devre dışı | hayır | hayır | hayır |
| Storm | devre dışı | hayır | hayır | hayır |
| ML hizmeti | YALNıZCA HDı 3,6 | F4 ve üzeri | hayır | hayır |

Her F serisi SKU 'sunun belirtimlerini görmek için bkz. [f SERISI VM boyutları](https://azure.microsoft.com/blog/f-series-vm-size/).

## <a name="benchmarking"></a>Karşılaştırmalı

Sınama, üretim iş yükleriniz için ne kadar iyi işlem gerçekleştireceğini ölçmek üzere farklı VM 'lerde sanal iş yüklerini çalıştırma işlemidir. 

VM SKU 'Larının ve küme boyutlarının benchi hakkında daha fazla bilgi için bkz. [Azure HDInsight 'Ta küme kapasitesi planlaması ](hdinsight-capacity-planning.md#choose-the-vm-size-and-type).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure HDInsight desteklenen düğüm yapılandırması](hdinsight-supported-node-configuration.md)
- [Azure’da Linux sanal makine boyutları](../virtual-machines/sizes.md)