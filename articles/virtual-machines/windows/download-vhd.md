---
title: Azure 'dan bir Windows VHD indirin
description: Azure portal kullanarak bir Windows VHD 'YI indirin.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: a33b248c18bcbf322a1e2d911453a1c4c087e625
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550527"
---
# <a name="download-a-windows-vhd-from-azure"></a>Azure 'dan bir Windows VHD indirin

Bu makalede, Azure 'dan Azure portal kullanarak bir Windows sanal sabit disk (VHD) dosyası indirmeyi öğreneceksiniz.

## <a name="optional-generalize-the-vm"></a>İsteğe bağlı: VM 'yi Genelleştirme

VHD 'YI başka VM 'Ler oluşturmak için [görüntü](tutorial-custom-images.md) olarak kullanmak istiyorsanız, işletim sistemini genelleştirmek Için [Sysprep 'i](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) kullanmanız gerekir. 

VHD 'yi başka VM 'Ler oluşturmak için görüntü olarak kullanmak için VM 'yi genelleştirin.

1. Önceden yapmadıysanız, [Azure portal](https://portal.azure.com/)da oturum açın
2. [VM 'ye bağlanın](connect-logon.md). 
3. VM 'de yönetici olarak komut Istemi penceresini açın.
4. Dizini *%windir%\system32\sysprep* olarak değiştirin ve sysprep.exe çalıştırın.
5. Sistem Hazırlama Aracı iletişim kutusunda, **sistem kutudan çıkar deneyimi (OOBE)** seçeneğini belirleyin ve **genelleştirilemedi** ' ın seçili olduğundan emin olun.
6. Kapalı Seçenekler ' de, **kapatın**' i seçin ve ardından **Tamam**' a tıklayın. 


## <a name="stop-the-vm"></a>VM’yi durdurma

Bir VHD, çalışan bir VM 'ye eklenmişse Azure 'dan indirilemiyor. Bir VHD 'YI indirmek için VM 'yi durdurmanız gerekir. 

1. Azure portal hub menüsünde **sanal makineler**' e tıklayın.
1. Listeden VM 'yi seçin.
1. VM 'nin dikey penceresinde **Durdur**' a tıklayın.


## <a name="generate-download-url"></a>İndirme URL 'SI oluştur

VHD dosyasını indirmek için, [paylaşılan erişim imzası (SAS)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) URL 'si oluşturmanız gerekir. URL oluşturulduğunda, URL 'ye bir sona erme saati atanır.

1. VM 'nin sayfasında, sol taraftaki menüde **diskler** ' e tıklayın.
1. VM için işletim sistemi diskini seçin.
1. Disk sayfasında, sol menüden **disk dışarı aktar** ' ı seçin.
1. URL 'nin varsayılan sona erme saati *3600* saniyedir. Windows işletim sistemi diskleri için bunu **36000** artırın.
1. **URL Oluştur**' a tıklayın.

> [!NOTE]
> Son kullanma süresi, Windows Server işletim sistemi için büyük VHD dosyasını indirmek üzere yeterli zaman sağlamak üzere varsayılan değer olarak artar. Windows Server işletim sistemini içeren bir VHD dosyasının, bağlantınıza bağlı olarak birkaç saat sürer. Bir veri diski için VHD 'yi İndiriyor olmanız durumunda varsayılan süre yeterlidir. 
> 
> 

## <a name="download-vhd"></a>VHD 'YI indir

1. Oluşturulan URL altında, VHD dosyasını Indir ' e tıklayın.
1. İndirmeyi başlatmak için tarayıcınızda **Kaydet** ' e tıklamanız gerekebilir. VHD dosyasının varsayılan adı *abcd*' dir.

## <a name="next-steps"></a>Sonraki adımlar

- [BIR VHD dosyasını Azure 'a yüklemeyi](upload-generalized-managed.md)öğrenin. 
- [Bir depolama hesabındaki yönetilmeyen disklerden yönetilen diskler oluşturun](attach-disk-ps.md).
- [Azure disklerini PowerShell Ile yönetin](tutorial-manage-data-disk.md).
