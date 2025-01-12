---
title: Kapsayıcı grubunu el ile durdurma veya başlatma
description: Azure Container Instances bir kapsayıcı grubunu el ile durdurmayı veya başlatmayı öğrenin.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 1801dad463d478c754e621dad0ae9406899ae7e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198124"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Azure Container Instances’taki kapsayıcıları el ile durdurma veya başlatma

Bir kapsayıcı grubunun [yeniden başlatma ilkesi](container-instances-restart-policy.md) ayarı, kapsayıcı örneklerinin varsayılan olarak nasıl başlatılacağını veya durdurulacağını belirler. Bir kapsayıcı grubunu el ile durdurarak veya başlatarak varsayılan ayarı geçersiz kılabilirsiniz.

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="stop"></a>Durdur

Çalışan bir kapsayıcı grubunu el ile durdurun; Örneğin, [az Container stop][az-container-stop] komutunu veya Azure Portal kullanarak. Belirli kapsayıcı iş yükleri için, maliyetlerde tasarruf etmek üzere tanımlı bir dönemden sonra uzun süre çalışan bir kapsayıcı grubunu durdurmak isteyebilirsiniz. 

*Bir kapsayıcı grubu durdurulmuş duruma girdiğinde gruptaki tüm kapsayıcıları sonlandırır ve geri dönüştürür. Kapsayıcı durumunu korumaz.*

Kapsayıcılar geri dönüştürüldüğünde, [kaynak](container-instances-container-groups.md#resource-allocation) serbest bırakılır ve kapsayıcı grubu için faturalandırma duraklar.

Kapsayıcı grubu zaten sonlandırılırsa (başarılı veya başarısız durumdaysa) durdurma eylemi etkisizdir. Örneğin, başarıyla çalışan bir kez çalıştırılan kapsayıcı görevi olan bir kapsayıcı grubu başarılı durumunda sonlandırılır. Bu durumda grubu durdurma girişimleri durumu değiştirmez. 

## <a name="start"></a>Başlangıç

Bir kapsayıcı grubu durdurulduğunda-kapsayıcılar kendi üzerinde sonlandırıldığı ya da grubu el ile durdurduğu için kapsayıcıları başlatabilirsiniz. Örneğin, gruptaki kapsayıcıları el ile başlatmak için [az Container start][az-container-start] komutunu veya Azure Portal kullanın. Herhangi bir kapsayıcının kapsayıcı görüntüsü güncelleştirilirse yeni bir görüntü çekilir. 

Bir kapsayıcı grubu başlatıldığında aynı kapsayıcı yapılandırmasıyla yeni bir dağıtım başlatılır. Bu eylem, beklendiği gibi çalışarak bilinen bir kapsayıcı grubu yapılandırmasını hızlıca yeniden kullanmanıza yardımcı olabilir. Aynı iş yükünü çalıştırmak için yeni bir kapsayıcı grubu oluşturmanız gerekmez.

Bir kapsayıcı grubundaki tüm kapsayıcılar bu eylem tarafından başlatılır. Grupta belirli bir kapsayıcıyı başlatamazsınız.

Bir kapsayıcı grubunu el ile başlattıktan veya yeniden başlattıktan sonra kapsayıcı grubu, yapılandırılan yeniden başlatma ilkesine göre çalışır.
  
## <a name="restart"></a>Yeniden başlat

Bir kapsayıcı grubunu çalışırken yeniden başlatabilirsiniz; Örneğin, [az Container Restart][az-container-restart] komutunu kullanarak. Bu eylem kapsayıcı grubundaki tüm kapsayıcıları yeniden başlatır. Herhangi bir kapsayıcının kapsayıcı görüntüsü güncelleştirilirse yeni bir görüntü çekilir. 

Bir dağıtım sorunuyla ilgili sorunları gidermek istediğinizde bir kapsayıcı grubunun yeniden başlatılması yararlı olur. Örneğin, geçici bir kaynak sınırlaması, kapsayıcılarınızın başarıyla çalışmasını engelliyorsa, grubun yeniden başlatılması sorunu çözebilir.

Bir kapsayıcı grubundaki tüm kapsayıcılar bu eylem tarafından yeniden başlatılır. Grupta belirli bir kapsayıcıyı yeniden çalıştıramazsınız.

Bir kapsayıcı grubunu el ile yeniden başlattıktan sonra kapsayıcı grubu, yapılandırılan yeniden başlatma ilkesine göre çalışır.

## <a name="next-steps"></a>Sonraki adımlar

Azure Container Instances 'de [yeniden başlatma ilkesi ayarları](container-instances-restart-policy.md) hakkında daha fazla bilgi edinin.

Aynı yapılandırmaya sahip bir kapsayıcı grubunu el ile durdurup başlatmaya ek olarak, çalışan bir kapsayıcı grubunun [ayarlarını güncelleştirebilirsiniz](container-instances-update.md) .

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container#az-container-restart
[az-container-start]: /cli/azure/container#az-container-start
[az-container-stop]: /cli/azure/container#az-container-stop
