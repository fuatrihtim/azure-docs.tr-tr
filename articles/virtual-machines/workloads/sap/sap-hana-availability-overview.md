---
title: Azure VM 'lerinde SAP HANA kullanılabilirliği-genel bakış | Microsoft Docs
description: Azure yerel VM 'lerinde SAP HANA işlemler açıklanmaktadır.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 757dfc34e3be12d09b8f965a2bb0295adb712c11
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102504244"
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>Azure sanal makineleri için yüksek kullanılabilirlik SAP HANA

Azure VM 'lerinde SAP HANA gibi görev açısından kritik veritabanlarını dağıtmak için çok sayıda Azure özelliği kullanabilirsiniz. Bu makalede, Azure VM 'lerinde barındırılan SAP HANA örnekleri için nasıl kullanılabilirlik elde edilecek hakkında rehberlik sunulmaktadır. Makalesinde Azure 'da SAP HANA kullanılabilirliğini artırmak için Azure altyapısını kullanarak uygulayabileceğiniz çeşitli senaryolar açıklanmaktadır. 

## <a name="prerequisites"></a>Önkoşullar

Bu makalede aşağıdakiler de dahil olmak üzere Azure 'da hizmet olarak altyapı (IaaS) hakkında bilgi sahibi olduğunuz varsayılır. 

- Azure portal veya PowerShell aracılığıyla sanal makineleri veya sanal ağları dağıtma.
- Azure platformlar arası komut satırı arabirimi (Azure CLı) kullanarak JavaScript Nesne Gösterimi (JSON) şablonlarını kullanma seçeneği de dahildir.

Bu makalede ayrıca SAP HANA örnekleri yükleme ve yönetme ve çalıştırma SAP HANA örnekleri ile ilgili bilgi sahibi olduğunuz varsayılır. HANA sistem çoğaltmasının kurulumunu ve işlemlerini öğrenmeniz özellikle önemlidir. Bu, SAP HANA veritabanları için yedekleme ve geri yükleme gibi görevleri içerir.

Bu makaleler, Azure 'da SAP HANA kullanmaya yönelik iyi bir genel bakış sunar:

- [Azure VM 'lerinde tek örnekli SAP HANA el ile yüklenmesi](./hana-get-started.md)
- [Azure VM 'lerinde SAP HANA sistem çoğaltması ayarlama](sap-hana-high-availability.md)
- [Azure VM’leri üzerinde SAP HANA yedekleme](./sap-hana-backup-guide.md)

Ayrıca, SAP HANA hakkındaki bu makalelerle ilgili bilgi sahibi olmak iyi bir fikirdir:

- [SAP HANA için yüksek kullanılabilirlik](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [SSS: SAP HANA için yüksek kullanılabilirlik](https://archive.sap.com/documents/docs/DOC-66702)
- [SAP HANA için sistem çoğaltma gerçekleştir](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2,0 SPS 01 yenilikler: yüksek kullanılabilirlik](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [SAP HANA sistem çoğaltması için ağ önerileri](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [SAP HANA sistem çoğaltması](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [SAP HANA hizmeti otomatik yeniden başlatma](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [SAP HANA sistem çoğaltmasını yapılandırma](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)

Azure 'da VM dağıtımı hakkında daha fazla bilgi sahibi olmak için, Azure 'da kullanılabilirlik mimarinizi tanımladıktan önce, [Azure 'Da Windows sanal makinelerinin kullanılabilirliğini yönetme](../../availability.md)konusunu okumanız önerilir.

## <a name="service-level-agreements-for-azure-components"></a>Azure bileşenleri için hizmet düzeyi sözleşmeleri

Azure, ağ, depolama ve sanal makineler gibi farklı bileşenler için farklı kullanılabilirlik SLA 'Ları içerir. Tüm SLA 'Lar belgelenmiştir. Daha fazla bilgi için bkz. [hizmet düzeyi sözleşmeleri Microsoft Azure](https://azure.microsoft.com/support/legal/sla/). 

[Sanal makineler Için SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) üç farklı SLA tanımlar, üç farklı yapılandırma için:

- İşletim sistemi diski ve tüm veri diskleri için [Azure Premium SSD 'ler](../../managed-disks-overview.md) kullanan tek bir VM. Bu seçenek yüzde 99,9 ' lik bir aylık çalışma süresi sağlar.
- Bir [Azure kullanılabilirlik kümesinde](../../windows/tutorial-availability-sets.md)düzenlenmiş birden çok (en az Iki) VM. Bu seçenek yüzde 99,95 ' lik bir aylık çalışma süresi sağlar.
- Bir [kullanılabilirlik bölgesinde](../../../availability-zones/az-overview.md)düzenlenmiş birden çok (en az Iki) VM. Bu seçenek yüzde 99,99 ' lik bir aylık çalışma süresi sağladı.

Azure bileşenlerinin sağlayacağımız SLA 'Lara karşı kullanılabilirlik gereksinimini ölçün. Ardından, gerekli kullanılabilirlik düzeyini elde etmek için SAP HANA senaryolarınızı seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Tek bir Azure bölgesinde SAP HANA kullanılabilirliği](./sap-hana-availability-one-region.md)hakkında bilgi edinin.
- [Azure bölgeleri arasında SAP HANA kullanılabilirliği](./sap-hana-availability-across-regions.md)hakkında bilgi edinin. 















