---
title: NCv3-Series-Azure sanal makineleri
description: NCv3 serisi VM 'Ler için Özellikler.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: c28ed042156865c52607ce031507c696f28b4e7c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102617310"
---
# <a name="ncv3-series"></a>NCv3 serisi

NCv3 serisi VM 'Ler NVıDıA Tesla V100 GPU 'ları tarafından desteklenir. Bu GPU 'Lar 1.5 x 'in NCv2 serisi hesaplama performansını sağlayabilir. Müşteriler rezervoır modelleme, DNA sıralaması, protestolu analiz, Monte Carlo simülasyonları ve diğerleri gibi geleneksel HPC iş yükleri için bu güncelleştirilmiş GPU 'ların avantajlarından yararlanabilir. NC24rs v3 yapılandırması, sıkı şekilde bağlanmış paralel bilgi işlem iş yükleri için iyileştirilmiş, düşük gecikme süreli ve yüksek performanslı bir ağ arabirimi sağlar. GPU 'Ların yanı sıra, NCv3 serisi VM 'Ler Intel Xeon E5-2690 v4 (geniş) CPU 'Lar tarafından da desteklenir.

[Premium Depolama](premium-storage-performance.md): desteklenir<br>
[Premium depolama önbelleği](premium-storage-performance.md): desteklenir<br>
[Dinamik geçiş](maintenance-and-updates.md): desteklenmiyor<br>
[Güncelleştirmeleri koruyan bellek](maintenance-and-updates.md): desteklenmiyor<br>
[VM oluşturma desteği](generation-2.md): 1. ve 2. nesil<br>
[Hızlandırılmış ağ](../virtual-network/create-vm-accelerated-networking-cli.md): desteklenmez<br>
[Kısa ömürlü işletim sistemi diskleri](ephemeral-os-disks.md): desteklenir <br>
NVIDIA NVLink Interconnect: desteklenmiyor<br>

> [!IMPORTANT]
> Bu VM Serisi için, aboneliğinizdeki vCPU (çekirdek) kotası başlangıçta her bölgede 0 olarak ayarlanır. [Kullanılabilir bir bölgede](https://azure.microsoft.com/regions/services/)bu seri Için [bir vCPU kota artışı isteyin](../azure-portal/supportability/resource-manager-core-quotas-request.md) . Bu SKU 'Lar deneme veya Visual Studio abonesi Azure abonelikleri için kullanılamaz. Abonelik düzeyiniz Bu SKU 'Ların seçilip dağıtılmayabilir. 
>

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | GPU | GPU belleği: GiB | Maksimum veri diskleri | Önbelleğe alınmamış maksimum disk aktarım hızı: ıOPS/MBps | En fazla NIC |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v3    | 6  | 112 | 736  | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v3   | 12 | 224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v3   | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v3 * | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = bir V100 kart.

*RDMA özellikli

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Desteklenen işletim sistemleri ve sürücüler

Azure N serisi VM 'lerin GPU yeteneklerini avantajlarından yararlanmak için NVıDıA GPU sürücüleri yüklenmelidir.

[NVıDıA GPU sürücü uzantısı](./extensions/hpccompute-gpu-windows.md) , bir N serisi VM 'ye uygun NVIDIA CUDA veya kılavuz sürücülerini yükleme. Azure portal veya Azure PowerShell veya Azure Resource Manager şablonları gibi araçları kullanarak uzantıyı yükler veya yönetir. Desteklenen işletim sistemleri ve dağıtım adımları için [NVıDıA GPU sürücü uzantısı belgelerine](./extensions/hpccompute-gpu-windows.md) bakın. VM uzantıları hakkında genel bilgi için bkz. [Azure sanal makine uzantıları ve özellikleri](./extensions/overview.md).

NVıDıA GPU sürücülerini el ile yüklemeyi tercih ederseniz desteklenen işletim sistemleri, sürücüler, yükleme ve doğrulama adımları için bkz. [Windows Için n SERISI GPU sürücü kurulumu](./windows/n-series-driver-setup.md) veya [Linux IÇIN n serisi GPU sürücü kurulumu](./linux/n-series-driver-setup.md) .

## <a name="other-sizes"></a>Diğer boyutlar

- [Genel amaçlı](sizes-general.md)
- [Bellek için iyileştirilmiş](sizes-memory.md)
- [Depolama için iyileştirilmiş](sizes-storage.md)
- [GPU için iyileştirilmiş](sizes-gpu.md)
- [Yüksek performanslı işlem](sizes-hpc.md)
- [Önceki nesiller](sizes-previous-gen.md)

## <a name="next-steps"></a>Sonraki adımlar

Azure [işlem birimlerinin (ACU)](acu.md) Azure SKU 'ları genelinde işlem performansını karşılaştırmanıza nasıl yardımcı olabileceğini öğrenin.
