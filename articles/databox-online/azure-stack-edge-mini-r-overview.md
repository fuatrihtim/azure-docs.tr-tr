---
title: Azure Stack Edge Mini e-Özeti | Microsoft Docs
description: Azure 'a Wi-Fi üzerinden aktarım için pili olan taşınabilir fiziksel bir cihaz kullanan askeri bir depolama çözümü olan Azure Stack Edge Mini R ' yi açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/03/2021
ms.author: alkohli
ms.openlocfilehash: 14a425c3aca3a1c296b96855b2c920d558e89f9e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585987"
---
# <a name="what-is-the-azure-stack-edge-mini-r"></a>Azure Stack Edge Mini R nedir?

Azure Stack Edge Mini, şiddetli ortamlarda kullanılmak üzere tasarlanan ultra taşınabilir, Rugged ve Edge bilgi işlem cihazındır. Azure Stack Edge Mini R, hizmet olarak donanım çözümü olarak sunulur. Microsoft, ağ depolama ağ geçidi olarak davranan ve hızlandırılmış AI-ınklik sağlayan yerleşik bir görüntü Işleme birimi (VPU) sunan, bulut tarafından yönetilen bir cihaz sunar.

Bu makalede, Azure Stack Edge Mini R çözümüne, önemli yeteneklere ve bu cihazı dağıtabileceğiniz senaryolara ilişkin bir genel bakış sunulmaktadır.


## <a name="key-capabilities"></a>Temel işlevler

Azure Stack Edge Mini R aşağıdaki yeteneklere sahiptir:

|Özellik |Açıklama  |
|---------|---------|
|Rugged donanımı| Rugged, Harsh ortamları için tasarlanan donanım.|
|Ultra taşınabilir| Ultra taşınabilir, pille çalıştırılan form faktörü.|
|Bulutta yönetilen|Cihaz ve hizmet, Azure portal aracılığıyla yönetilir.|
|Edge işlem iş yükleri|Verilerin analizine, işlenmesine, filtrelenmesine olanak tanır.<br>VM 'Leri ve Kapsayıcılı iş yüklerini destekler. |
|Hızlandırılmış AI ınkrii| Intel Movidius Myriad X VPU tarafından etkinleştirilir.|
|Kablolu ve kablosuz | Kablolu ve kablosuz veri aktarımlarına izin verir.|
|Veri erişimi     | Bulutta ek veri işleme için bulut API'lerini kullanarak Azure Depolama Blobları ve Azure Dosyaları'ndan doğrudan veri erişimi. Cihazdaki yerel önbellek, en son kullanılan dosyalara hızlı erişim için kullanılır.|
|Bağlantısı kesik mod|  Cihaz ve hizmet, isteğe bağlı olarak Azure Stack hub 'ı aracılığıyla yönetilebilir. Uygulamaları çevrimdışı modda dağıtın, çalıştırın, yönetin. <br> Bağlantısız mod, çevrimdışı karşıya yükleme senaryolarını destekler.|
|Desteklenen dosya aktarımı protokolleri      |Veri alımı için standart SMB, NFS ve REST protokollerini destekler. <br> Desteklenen sürümler hakkında daha fazla bilgi için [Azure Stack Edge Mini R sistem gereksinimleri](azure-stack-edge-gpu-system-requirements.md)' ne gidin.|
|Veri yenileme     | Yerel dosyaları buluttaki en son sürümle yenileme olanağı.|
|Çift şifreleme    | Kendi kendine şifrelenen sürücü kullanımı, ilk şifreleme katmanını sağlar. VPN ikinci şifreleme katmanını sağlar. Verileri yerel olarak şifrelemek ve *https* üzerinden buluta veri aktarımını güvenli hale getirmek için BitLocker desteği.|
|Bant genişliği azaltma| Yoğun saatlerde bant genişliği kullanımını sınırlandırmaya kısıtlama.|

## <a name="use-cases"></a>Uygulama alanları

Azure Stack Edge Mini R 'nin, en uçta hızlı Machine Learning (ML) için kullanılabileceği ve verileri Azure 'a göndermeden önce ön işlemesi için kullanabileceğiniz çeşitli senaryolar aşağıda verilmiştir.

- **Azure Machine Learning Ile çıkarım** -Azure Stack Edge Mini R ile, veri buluta gönderilmeden önce üzerinde işlem yapılabilir hızlı sonuçlar almak için ml modellerini çalıştırabilirsiniz. Tam veri kümesi, ML modellerinizi yeniden eğitmeye ve artırmaya devam etmek için isteğe bağlı olarak aktarılabilir. Azure Stack Edge Mini R cihazında Azure ML donanım hızlandırılmış modellerini kullanma hakkında daha fazla bilgi için bkz. [Azure Stack Edge Mini r üzerinde Azure ML donanım hızlandırılmış modellerini dağıtma](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server).

- Daha önceden uygulanabilir bir veri kümesi oluşturmak üzere **verileri** Azure 'a göndermeden önce kapsayıcılar veya sanal makineler gibi işlem seçenekleri aracılığıyla veri dönüştürme. Önceden işleme şu amaçlarla kullanılabilir:

    - Verileri toplama.
    - Verileri değiştirin, örneğin kişisel verileri kaldırmak için.
    - Depolama ve bant genişliğini iyileştirmek veya daha fazla analiz için verileri alt kümele.
    - IoT Olaylarını analiz etme ve bunlara yanıt verme.

- **Ağ üzerinden Azure 'a veri aktarma** -daha fazla bilgi işlem ve analiz sağlamak veya arşivleme amacıyla verileri kolayca ve hızlı bir şekilde Azure 'a aktarmak Için Azure Stack Edge Mini R 'yi kullanın.

## <a name="components"></a>Bileşenler

Azure Stack Edge Mini R çözümü, bir Azure Stack Edge kaynağı, Azure Stack Edge Mini R Rugged, ultra taşınabilir fiziksel cihaz ve yerel bir Web kullanıcı arabiriminden oluşur.

* **Azure Stack Edge Mini R fiziksel cihazı** -Microsoft tarafından sağlanan bir ultra taşınabilir, Rugged, işlem ve depolama cihazı. Cihazda, yerleşik bir pil bulunur ve en fazla 7 lb.

    ![Azure Stack Edge Mini R cihazı](media/azure-stack-edge-mini-r-overview/perspective-view-1.png)

* **Azure Stack Edge kaynağı** : bir rugged, Azure Stack Edge Mini R cihazını farklı coğrafi konumlardan erişebileceğiniz bir web arabiriminden yönetmenize olanak tanıyan bir Azure Portal kaynaktır. Kaynak oluşturmak ve yönetmek, cihazları ve uyarıları görüntülemek ve yönetmek ve paylaşımları yönetmek için Azure Stack Edge kaynağını kullanın.  

* **Azure Stack Edge Mini r Yerel Web Kullanıcı arabirimi** -öncelikle cihazın ilk yapılandırması için tasarlanan Azure Stack Edge Mini r cihazınızda tarayıcı tabanlı bir yerel kullanıcı arabirimi. Yerel Web Kullanıcı arabirimini kullanarak da tanılamayı çalıştırın, Azure Stack Edge Pro cihazını kapatıp yeniden başlatın, kopyalama günlüklerini görüntüleyin ve bir hizmet isteği dosyası için Microsoft Desteği başvurun.


## <a name="region-availability"></a>Bölge kullanılabilirliği

Azure Stack Edge Mini R fiziksel cihazı, Azure kaynağı ve veri aktarımı yaptığınız hedef depolama hesabı, tümünün aynı bölgede olması gerekmez.

- **Kaynak kullanılabilirliği** -Azure Stack Edge kaynağının kullanılabildiği tüm bölgelerin listesi için [bölgeye göre kullanılabilir Azure ürünlerine](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)gidin. 

- **Cihaz kullanılabilirliği** -Azure Stack Edge Mini r cihazının kullanılabildiği tüm ülkelerin listesi için, [Azure Stack Edge](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgeMiniR)mini r fiyatlandırması Için Azure Stack Edge Mini r sekmesinde kullanılabilirlik bölümüne gidin.

- **Hedef Depolama hesapları**: Verilerin depolandığı depolama hesapları, tüm Azure bölgelerinde sağlanır. Depolama hesaplarının Azure Stack Edge Mini R verileri, cihazın en iyi performans için bulunduğu konuma yakın olması gerekir. Cihazdan uzağa konumlandırılan depolama hesabı uzun gecikme sürelerine ve daha yavaş bir performansa yol açar.

Azure Stack Edge hizmeti bölgesel olmayan bir hizmettir. Daha fazla bilgi için bkz. [Azure 'Da bölgeler ve kullanılabilirlik alanları](https://docs.microsoft.com/azure/availability-zones/az-overview). Azure Stack Edge hizmetinin belirli bir Azure bölgesine bağımlılığı yoktur, bu da bölge genelinde kesintiler ve bölge genelinde kesintiler için dayanıklı hale gelir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Stack Edge Mini R sistem gereksinimlerini](azure-stack-edge-gpu-system-requirements.md)gözden geçirin.
