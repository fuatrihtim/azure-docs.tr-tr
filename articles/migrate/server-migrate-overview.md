---
title: Azure geçişi sunucu geçişi ile bir VMware geçiş seçeneği seçin
description: Azure geçişi sunucu geçişi ile VMware VM 'lerini Azure 'a geçirme seçeneklerine genel bakış sağlar
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 7446b2050fdd7bbc7704953c053da0629231191c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101715131"
---
# <a name="select-a-vmware-migration-option"></a>VMware geçiş seçeneği seçin

Azure geçişi sunucu geçiş aracını kullanarak VMware VM 'lerini Azure 'a geçirebilirsiniz. Bu araç, VMware VM geçişi için birkaç seçenek sunar:

- Aracısız çoğaltma kullanılarak geçiş. VM 'Leri bunlara hiçbir şey yüklemeye gerek kalmadan geçirin.
- Çoğaltma için bir aracı ile geçiş. Çoğaltma için VM 'ye bir aracı yükler.


## <a name="compare-migration-methods"></a>Geçiş yöntemlerini karşılaştırma

Hangi yöntemin kullanılacağına karar vermenize yardımcı olması için bu seçili karşılaştırmaları kullanın. Ayrıca, [aracısız](migrate-support-matrix-vmware-migration.md#agentless-migration) ve [aracı tabanlı](migrate-support-matrix-vmware-migration.md#agent-based-migration) geçiş için tam destek gereksinimlerini inceleyebilirsiniz.

**Ayar** | **Aracısız** | **Aracı tabanlı**
--- | --- | ---
**Azure izinleri** | Azure geçişi projesi oluşturma ve Azure geçiş gereci dağıtırken oluşturulan Azure AD uygulamalarını kaydetme izinlerine sahip olmanız gerekir. | Azure aboneliğinde katkıda bulunan izinlerine sahip olmanız gerekir. 
**Çoğaltma** | VCenter Server en fazla 500 VM aynı anda çoğaltılabilir. Portalda, çoğaltma için aynı anda en fazla 10 makine seçebilirsiniz. Daha fazla makine çoğaltmak için 10 toplu işi ekleyin.| Çoğaltma gereci ölçeklendirerek çoğaltma kapasitesi artar.
**Gereç dağıtımı** | [Azure geçişi](migrate-appliance.md) gereci şirket içinde dağıtılır. | [Azure geçişi çoğaltma](migrate-replication-appliance.md) gereci şirket içinde dağıtılır.
**Site Recovery uyumluluğu** | Uyumluluk. | Site Recovery kullanarak bir makine için çoğaltma ayarladıysanız Azure geçişi sunucu geçişi ile çoğaltamaz.
**Hedef disk** | Yönetilen diskler | Yönetilen diskler
**Disk sınırları** | İşletim sistemi diski: 2 TB<br/><br/> Veri diski: 32 TB<br/><br/> En fazla disk: 60 | İşletim sistemi diski: 2 TB<br/><br/> Veri diski: 32 TB<br/><br/> En fazla disk: 63
**Geçiş diskleri** | Desteklenmez | Desteklenir
**UEFı önyüklemesi** | Destekleniyor. | Destekleniyor.

## <a name="compare-deployment-steps"></a>Dağıtım adımlarını karşılaştırın

Sınırlamaları inceledikten sonra, her bir çözümü dağıtmaya ilişkin adımları anlamak hangi seçenek arasından seçim yapmanıza yardımcı olur.

**Görev** | **Ayrıntılar** |**Aracısız** | **Aracı tabanlı**
--- | --- | --- | ---
**Azure Geçişi aletini dağıtma** | VMware VM üzerinde çalışan bir basit gereç.<br/><br/> Gereç, makineleri saptamak ve değerlendirmek ve aracısız geçiş kullanarak makineleri geçirmek için kullanılır. | Gereklidir.<br/><br/> Gereci zaten değerlendirme için ayarladıysanız, aracısız geçiş için aynı gereci kullanabilirsiniz. | Gerekli değildir.<br/><br/> Değerlendirme için bir gereç ayarladıysanız bu uygulamayı yerinde bırakabilir veya değerlendirme ile işiniz bittiğinde kaldırabilirsiniz.
**Sunucu değerlendirmesi aracını kullanma** | Azure geçişi: Sunucu değerlendirmesi aracı ile makineleri değerlendirin. | Değerlendirme isteğe bağlıdır. | Değerlendirme isteğe bağlıdır.
**Sunucu geçiş aracını kullanma** | Azure geçişi projesine Azure geçiş sunucusu geçiş aracı 'nı ekleyin. | Gerekli | Gerekli
**VMware 'yi geçişe hazırlama** | VMware sunucularında ve VM 'lerde ayarları yapılandırın. | Gerekli | Gerekli
**VM 'Lere Mobility hizmetini yükler** | Mobility hizmeti, çoğaltmak istediğiniz her VM üzerinde çalışır | Gerekli değil | Gerekli
**Çoğaltma gereç dağıtımı** | [Çoğaltma](migrate-replication-appliance.md) gereci, aracı tabanlı geçiş için kullanılır. VM 'lerde çalışan Mobility hizmeti ve sunucu geçişi arasında bağlantı kurar. | Gerekli değil | Gerekli
**VM 'Leri çoğaltın**. VM çoğaltmasını etkinleştirin. | Çoğaltma ayarlarını yapılandırın ve çoğaltılacak VM 'Leri seçin | Gerekli | Gerekli
**Geçiş testi çalıştırma** | Her şeyin beklendiği gibi çalıştığından emin olmak için bir test geçişi çalıştırın. | Gerekli | Gerekli
**Tam geçiş çalıştırma** | VM 'Leri geçirin. | Gerekli | Gerekli



## <a name="next-steps"></a>Sonraki adımlar

[VMware VM](tutorial-migrate-vmware.md) 'lerini aracısız geçişe geçirin.



