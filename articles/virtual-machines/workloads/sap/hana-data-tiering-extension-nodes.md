---
title: Azure 'da SAP HANA yönelik veri katmanlama ve genişletme düğümleri (büyük örnekler) | Microsoft Docs
description: Azure 'da SAP HANA yönelik veri katmanlaması ve uzantı düğümleri (büyük örnekler).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: eb780d10996cb22f5e6fe5bc8889e897e8c3854d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666774"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>SAP HANA veri katmanlama ve uzantı düğümlerini kullanma

SAP, farklı SAP NetWeaver sürümleri ve SAP BW/4HANA SAP BW için bir veri katmanlama modeli destekler. Veri katmanlama modeli hakkında daha fazla bilgi için bkz. SAP Document [SAP BW/4HANA ve SAP BW SAP HANA uzantı düğümleri Ile Hana](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
HANA büyük örnekle, SSS ve SAP blog belgelerinde açıklandığı gibi SAP HANA uzantısı düğümlerinin seçenek-1 yapılandırmasını kullanabilirsiniz. 2. seçenek, aşağıdaki HANA büyük örnek SKU 'Larında ayarlanabilir: S72m, S192, S192m, S384 ve S384m. 

Belgelere baktığınızda, avantajı hemen görünür olmayabilir. Ancak SAP boyutlandırma yönergelerine baktığınızda, seçenek-1 ve seçenek-2 SAP HANA uzantı düğümlerini kullanarak bir avantaj görebilirsiniz. Örnekler aşağıdadır:

- SAP HANA boyutlandırma yönergeleri genellikle bellek olarak iki veri birimi miktarına gerek duyar. SAP HANA örneğinizi sık erişimli verilerle çalıştırdığınızda, verilerin yalnızca yüzde 50 veya daha az olması gerekir. Belleğin geri kalanı, işini SAP HANA için ideal olarak tutulur.
- Bu, SAP BW bir veritabanı çalıştıran bir HANA büyük örnek S192 biriminde, yalnızca 1 TB veri hacminin bulunduğu anlamına gelir.
- Ayrıca, S192 HANA büyük örnek SKU 'SU olan-1 ' in ek SAP HANA uzantısı düğümü kullanıyorsanız, veri hacmi için ek 2 TB kapasiteli bir kapasite sağlar. 2. seçenek yapılandırmasında, yarı veri hacmi için ek 4 TB alırsınız. Sık erişimli düğümle karşılaştırıldığında, "normal" uzantı düğümünün tam bellek kapasitesi,-1 seçeneği için veri depolamak için kullanılabilir. Çift bellek, seçenek-2 SAP HANA uzantısı düğüm yapılandırmasındaki veri hacmi için kullanılabilir.
- Verileriniz için 3 TB kapasiteli bir kapasiteye ve 1. seçenek için de sık erişimli 1:2 oranına sahip olursunuz. 5 TB veri ve seçenek-2 uzantı düğümü yapılandırmasıyla 1:4 oranına sahip olursunuz.

Veri hacmi bellek ile karşılaştırıldığında, ne kadar yüksek olursa, istediğiniz ısınma verileri disk depolama alanında depolanır.

**Sonraki adımlar**
- [Azure 'da SAP HANA (büyük örnekler) mimarisine](hana-architecture.md) başvurun