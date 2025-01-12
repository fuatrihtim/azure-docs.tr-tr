---
title: Install-Unpack, rafı, kablo Azure Stack Edge Pro fiziksel cihaz | Microsoft Docs
description: Azure Stack Edge Pro 'Yu yüklemeyle ilgili ikinci öğretici, fiziksel cihazın paketini açma, raf ve kablo oluşturma ile ilgilidir.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/17/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Azure Stack Edge Pro in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: 9aa02521d91d41380b1bdac3efe50ab3d196a856
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90894201"
---
# <a name="tutorial-install-azure-stack-edge-pro"></a>Öğretici: Azure Stack Edge Pro 'Yu yükler

Bu öğreticide, Azure Stack Edge Pro fiziksel cihazının nasıl yükleneceği açıklanmaktadır. Yükleme yordamı, cihazı açmak, rafa monte etmek ve kablolama işlemini içerir. 

Yüklemenin tamamlanması iki saat sürebilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Cihazı kutusundan çıkarma
> * Cihaza raf bağlama
> * Cihazın kablolarını bağlama

## <a name="prerequisites"></a>Önkoşullar

Fiziksel bir cihazı yükleme önkoşulları şunlardır:

### <a name="for-the-azure-stack-edge-resource"></a>Azure Stack Edge kaynağı için

Başlamadan önce aşağıdakilerden emin olun:

* [Azure Stack Edge Pro 'yu dağıtmaya hazırlanma](azure-stack-edge-deploy-prep.md)bölümündeki tüm adımları tamamladınız.
    * Cihazınızı dağıtmak için bir Azure Stack Edge kaynağı oluşturdunuz.
    * Cihazınızı Azure Stack Edge kaynağıyla etkinleştirmek için etkinleştirme anahtarını oluşturdunuz.

 
### <a name="for-the-azure-stack-edge-pro-physical-device"></a>Azure Stack Edge Pro fiziksel cihazı için

Cihazı dağıtmadan önce:

- Cihazın düz, kararlı ve düzeyi bir iş yüzeyine güvenli bir şekilde oturduğu emin olun.
- Kurulumu gerçekleştirmeyi planladığınız yerde şunların bulunduğundan emin olun:
    - Bağımsız bir kaynaktan standart AC gücü

        -VEYA-
    - Kesintisiz güç kaynağı (UPS) ile bir raf güç dağıtım birimi (PDU)
    - Cihazı bağlamak istediğiniz rafa ait kullanılabilir bir 1U yuvası

### <a name="for-the-network-in-the-datacenter"></a>Veri merkezindeki ağ için

Başlamadan önce:

- Azure Stack Edge Pro 'Yu dağıtmaya yönelik ağ gereksinimlerini gözden geçirin ve veri merkezi ağını gereksinimlere göre yapılandırın. Daha fazla bilgi için bkz. [Edge Pro ağ gereksinimleri Azure Stack](azure-stack-edge-system-requirements.md#networking-port-requirements).

- Cihazın en iyi şekilde çalışması için en düşük Internet bant genişliğinin 20 Mbps olduğundan emin olun.


## <a name="unpack-the-device"></a>Cihazı kutusundan çıkarma

Cihaz tek bir kutuda gönderilir. Cihazınızı kutusundan çıkarmak için aşağıdaki adımları gerçekleştirin. 

1. Kutuyu düz ve sabit bir yüzeye yerleştirin.
2. Kutuda ve ambalajda ezik, kesik, su hasarı veya gözle görülür herhangi bir hasar olup olmadığını kontrol edin. Kutu veya paketleme ciddi hasar görmüşse bunu açmayın. Cihazın iyi durumda olup olmadığının değerlendirilmesi için Microsoft Desteği ile iletişim kurun.
3. Kutuyu açın. Kutuyu açtıktan sonra aşağıdakilerin bulunduğundan emin olun:
    - Tek bir kutu Azure Stack Edge Pro cihazı
    - İki güç kablosu
    - Bir demiryolu seti derlemesi
    - Güvenlik, ortam ve mevzuat bilgileri kitapçık

Burada listelenen öğelerin tümünü almadıysanız Azure Stack Edge Pro desteği 'ne başvurun. Sonraki adım, cihazınızı takmaya yönelik bir adımdır.


## <a name="rack-the-device"></a>Cihazı rafa yerleştirme

Cihaz, standart 19 inç bir rafa yüklenmiş olmalıdır. Cihazınızı standart 19 inç bir rafa bağlamak için aşağıdaki yordamı kullanın.

> [!IMPORTANT]
> Azure Stack Edge Pro cihazlarının uygun işlemler için rafa bağlı olması gerekir.


### <a name="prerequisites"></a>Önkoşullar

- Başlamadan önce güvenlik, ortam ve mevzuat bilgi kitapçığlarınızın güvenlik yönergelerini okuyun. Bu broşür cihazla birlikte gönderilmiştir.
- Dolap kutusu alt kısmına en yakın olan ayrılan alana rayları yüklemeye başlayın.
- Toulan demiryolu yapılandırması için:
    -  Sekiz adet screws sağlamanız gerekir: #10-32, #12-24, #M5 veya #M6. Screws 'nin baş çapı 10 mm 'den (0,4 ") az olmalıdır.
    -  Düz eğimli bir screwsürücü gerekir.

### <a name="identify-the-rail-kit-contents"></a>Demiryolu Kit içeriğini tanımla

Demiryolu Kit derlemesini yüklemek için bileşenleri bulun:
1. İki A7 Dell Readyrayları II kayan demiryolu derlemeleri
2. İki kanca ve döngü Straps

    ![Demiryolu Kit içeriğini tanımla](./media/azure-stack-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>Araç-Less raylarını (köşeli delik veya yuvarlak delik raflarını) yükleyip kaldırın

> [!TIP]
> Bu seçenek, bir araç daha küçüktür çünkü bu, rayları yüklemek ve raf dışı kareye ya da raflarda yuvarlak delikleri kaldırmak için araç gerektirir.

1. Sola ve sağa bakan Kııl bitiş parçalarını, içe ve dikey Raf ayırtların **ön tarafındaki** deliklere oturtine yönlendir olarak konumlandırın.
2. Her bir son parçayı istenen U boşlukların alt ve üst deliklere hizalayın.
3. Dikey Raf flanş üzerinde tam olarak oturana kadar, ve mandal bir yere tıkladığı sürece, rayın arka ucuna katılın. Bu adımları, ön uç parçasını dikey Raf flanş üzerinde konumlandırmak ve oturmek için tekrarlayın.
4. Rayları kaldırmak için, son nokta orta noktasına mandal yayını düğmesini çekin ve her bir demiryolu 'yi serbest bırakın.

    ![Araç-daha az rayın ve Kaldır](./media/azure-stack-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>Toulan raylarını (Iş parçacıklı delik raflarını) yükleyip kaldırın

> [!TIP]
> Bu seçenek, bir araç (_düz eğimli bir screwsürücü) için bir araç (düz eğimli bir screwsürücü_) gerektirdiğinden, bu seçeneği bir araç (düz eğimli bir vida), rafların raf

1. Düz eğimli bir screwsürücü kullanarak ön ve arka bağlama parantezlerinin PIN 'lerini kaldırın.
2. Montaj ayraçlarından kaldırmak için, demiryolu halkalarını çekin ve döndürün.
3. Sol ve sağ montaj rayları, iki adet sayıda screws kullanarak ön dikey rafa monte edilebilir olarak ekleyin.
4. Sol dikey dolapta, sol ve sağ köşeli ayraçlara doğru kaydırın ve iki adet screws çifti kullanarak bunları ekleyin.

    ![Toulan rayları yükleyip kaldırın](./media/azure-stack-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>Sistemi bir rafa yükler

1. İç slaydı, parçalara çıkana kadar raf dışına çekin.
2. Sistemin her tarafında arka raytan Doff bulun ve bunları slayt derlemelerindeki arka uç yuvalarına düşürün. Tüm RAIL stanon 'lar J yuvalarında kümelendirilene kadar sistemi aşağı döndürün.
3. Kilit bir yere tıklatalana kadar sistemi içe gönderin.
4. Her iki rayda da slayt yayın kilidi düğmelerine basın ve sistemi rafa kaydırın.

    ![Sistemi bir rafa yükler](./media/azure-stack-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>Sistemi rafa kaldır

1. İç rayların kenarlarından kilit çimlerini bulun.
2. Her mandalını, serbest bırakma konumuna döndürerek açın.
3. Sistemin yüzlerini korkutın ve RAIL stanon, J yuvalarının önünde oluncaya kadar ileriye doğru çekin. Sistemi rafa kaldırın ve bir düzey yüzeyine yerleştirin.

    ![Sistemi rafa kaldır](./media/azure-stack-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>Slad mandalına katılın ve yayınlayın

> [!NOTE]
> SLA 'lar ile donatılmış sistemler için, bu yordamın 3. adımında açıklandığı gibi, screws kullanarak sistemi güvenli hale getirin.

1. Önde oturun, sistemin her iki tarafında da Slad mandalı bulun.
2. Sistem rafa itilmiş ve bu, laçler üzerine çekilerek serbest bırakılanlar otomatik olarak çalışır.
3. Raf veya diğer kararsız ortamlar için sistemin sevk irsaliyesine karşı korumak için her mandal altında sabit takel vidalı konumunu bulun ve her bir vida #2 Phillips screwdriver ile güçlendir.

    ![SLA ve yayın ile katılın](./media/azure-stack-edge-deploy-install/engaging-releasing-slam-latch.png)


## <a name="cable-the-device"></a>Cihazın kablolarını bağlama

Kabloları yönlendirin ve sonra cihazınızı bağlayın. Aşağıdaki yordamlarda Azure Stack Edge Pro cihazınızın güç ve ağ için nasıl kablo yapılacağı açıklanmaktadır.

Cihazınızı kablolamayı başlatmaya başlamadan önce şunlar gerekir:

- Azure Stack Edge Pro fiziksel cihazınız, paketten çıkarılan ve takılabilir.
- İki güç kablosu.
- Yönetim arabirimine bağlamak için en az bir 1-GbE RJ-45 ağ kablosu. Cihazda biri yönetim ve diğeri veri olmak üzere iki 1-GbE ağ arabirimi vardır.
- Yapılandırılacak her veri ağı arabirimi için bir 25-GbE SFP+ bakır kablo. Bağlantı noktası 2, bağlantı noktası 3, bağlantı noktası 4, bağlantı noktası 5 veya bağlantı noktası 6 arasındaki en az bir veri ağı arabiriminin Internet 'e bağlanması gerekir (Azure bağlantısı ile).  
- İki güç dağıtımı birimine erişim (önerilir).

> [!NOTE]
> - Yalnızca bir veri ağı arabirimi bağlıyorsanız, Azure 'a veri göndermek için bağlantı noktası 3, bağlantı noktası 4, bağlantı noktası 5 veya bağlantı noktası 6 gibi bir 25/10-GbE ağ arabirimi kullanmanızı öneririz. 
> - En iyi performansı elde etmek ve büyük miktarda veriyi işlemek için tüm veri bağlantı noktalarını bağlamak isteyebilirsiniz.
> - Azure Stack Edge Pro cihazının veri kaynağı sunucularından veri alabilmesi için veri merkezi ağına bağlı olması gerekir.

Azure Stack Edge Pro cihazınızda:

- Ön panelde disk sürücüleri ve bir güç düğmesi bulunur.

    - Cihazınızın önünde 10 disk yuvası vardır.
    - Yuva 0 ' da, işletim sistemi diski olarak kullanılan 240 GB SATA sürücü bulunur. Yuva 1 boş ve 2 ile 9 yuvaları, veri diskleri olarak kullanılan NVMe SSD 'ler.
- Arka düzlem, yedekli güç kaynağı birimleri (PSUs) içerir.
- Arka düzlemi altı ağ arabirimine sahiptir:

    - 2 1 Gbps arabirimler.
    - 10 Gbps arabirim olarak da kullanılabilen 4 25 Gbps arabirim.
    - Bir temel kart yönetim denetleyicisi (BMC).

- Arka düzlemi 6 bağlantı noktasına karşılık gelen iki ağ kartına sahiptir:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

Bu ağ kartlarında desteklenen kabloların, anahtarların ve alıcı sayısının tam listesi için, [Cavium Fastlınq 41000 serisi birlikte çalışabilirlik matrisine](https://www.marvell.com/documents/xalflardzafh32cfvi0z/)gidin.
 
Cihazınızı güç ve ağ için kablolu yapmak üzere aşağıdaki adımları uygulayın.

1. Cihazınızın arka düzlemine ait çeşitli bağlantı noktalarını belirler.

    ![Kablolu cihazın arka düzlemi](./media/azure-stack-edge-deploy-install/backplane-cabled.png)

2. Cihazın önünde disk yuvalarını ve güç düğmesini bulun.

    ![Bir cihazın ön düzlemi](./media/azure-stack-edge-deploy-install/device-front-plane-labeled-1.png)

3. Güç kablolarını kasadaki PSU'lara bağlayın. Yüksek kullanılabilirlik için iki PSU'yu da takın ve ayrı güç kaynaklarına bağlayın.
4. Güç kablolarını raf güç dağıtım birimlerine (PDU) takın. İki PSU'nun ayrı güç kaynaklarını kullandığından emin olun.
5. Cihazı açmak için güç düğmesine basın.
6. 1-GbE ağ arabirimi bağlantı noktası 1 ' i fiziksel cihazı yapılandırmak için kullanılan bilgisayara bağlayın. PORT 1, yönetim için ayrılmış arabirimdir.
7. PORT 2, PORT 3, PORT 4, PORT 5 veya PORT 6 bağlantı noktalarından birini veya birkaçını veri merkezi ağına/İnternete bağlayın.

    - PORT 2’yi bağlıyorsanız, RJ-45 ağ kablosunu kullanın.
    - 10/25-GbE ağ arabirimleri için, SFP + bakır kablolarını kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, aşağıdaki gibi Edge Pro konuları Azure Stack hakkında bilgi edindiniz:

> [!div class="checklist"]
> * Cihazı kutusundan çıkarma
> * Cihazı rafa yerleştirme
> * Cihazın kablolarını bağlama

Cihazınıza bağlanma, kurulumunu yapma ve etkinleştirme adımları için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro 'Yu bağlama ve ayarlama](./azure-stack-edge-deploy-connect-setup-activate.md)
