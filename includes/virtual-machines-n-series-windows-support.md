---
title: include dosyası
description: include dosyası
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 5413a6acafa0ea54f98383fc8140a34aff0cf840
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98859808"
---
## <a name="supported-operating-systems-and-drivers"></a>Desteklenen işletim sistemleri ve sürücüler

### <a name="nvidia-tesla-cuda-drivers"></a>NVıDıA Tesla (CUDA) sürücüleri

NC, NCv2, NCv3, NCasT4_v3, ND ve NDv2 serisi VM 'Ler için NVıDıA Tesla (CUDA) sürücüleri (NV serisi için isteğe bağlı) yalnızca aşağıdaki tabloda listelenen işletim sistemlerinde desteklenir. Sürücü indirme bağlantıları yayın sırasında geçerli. En son sürücüler için [NVIDIA](https://www.nvidia.com/) web sitesini ziyaret edin.

> [!TIP]
> Bir Windows Server sanal makinesinde el ile CUDA sürücüsü yüklemeye alternatif olarak Azure [veri bilimi sanal makinesi](../articles/machine-learning/data-science-virtual-machine/overview.md) görüntüsünü dağıtabilirsiniz. Windows Server 2016 için DSVM sürümleri, NVıDıA CUDA sürücülerini, CUDA derin sinir ağ kitaplığını ve diğer araçları önceden yükler.


| İşletim Sistemi | Sürücü |
| -------- |------------- |
| Windows Server 2019 | [451,82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (. exe) |
| Windows Server 2016 | [451,82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (. exe) |

### <a name="nvidia-grid-drivers"></a>NVıDıA KıLAVUZ sürücüleri

Microsoft, sanal iş istasyonları veya sanal uygulamalar olarak kullanılan NV ve NVv3 serisi VM 'Ler için NVıDıA GRID sürücü yükleyicilerini yeniden dağıtır. Yalnızca aşağıdaki tabloda listelenen işletim sistemlerinde Azure NV serisi VM 'lerine yalnızca bu KıLAVUZ sürücülerini yükler. Bu sürücüler Azure 'da GRID sanal GPU yazılımı için lisanslama içerir. NVıDıA vGPU yazılım lisans sunucusu ayarlamanız gerekmez.

Azure tarafından yeniden dağıtılan KıLAVUZ sürücüleri, NCv2, NCv3, ND ve NDv2 serisi VM 'ler gibi NV serisi olmayan VM 'lerde çalışmaz. Tek istisna, KıLAVUZ sürücülerinin NV serisi ile benzer grafik işlevlerini etkinleştirebileceği NCas_T4_V3 VM Serisi ' dir.

NVIDIA K80 GPU 'ları ile NC-Series KıLAVUZ/grafik uygulamalarını desteklemez.  

NVIDIA uzantısının en son sürücüyü her zaman yükleyeceğini lütfen unutmayın. Daha eski bir sürüme bağımlılığı olan müşteriler için buradaki önceki sürüme bağlantılar sağlıyoruz.

Windows Server 2019, Windows Server 2016 1607, 1709 ve Windows 10 için (20H2 derlemek için):
- [Kılavuz 12,0 (461,09)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe)
- [Kılavuz 11,3 (452,77)](https://download.microsoft.com/download/f/d/5/fd5ad39b-89cb-4990-ae85-a6fd30475584/452.77_grid_win10_server2016_server2019_64bit_azure_swl.exe) (. exe) 

Windows Server 2012 R2 için: 
- [Kılavuz 12,0 (461,09)](https://download.microsoft.com/download/c/5/e/c5e7df99-364d-45f5-bff7-c253d59121f1/461.09_grid_server2012R2_64bit_azure_swl.exe) (. exe)
- [Kılavuz 11,3 (452,77)](https://download.microsoft.com/download/5/4/3/54323644-3c84-4aa1-97ec-35491f94c866/452.77_grid_server2012R2_64bit_azure_swl.exe) (. exe) 


Önceki tüm NVIDIA ıZGARA sürücüsü bağlantılarının tamamı listesi için lütfen [GitHub](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json) sayfasını ziyaret edin
