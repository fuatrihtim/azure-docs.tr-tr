---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5687fb99c27b8b2141e0a2a817327cfbb124951a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102109404"
---
## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a> El ile tetiklenen WebJob oluşturma

1. [Azure Portal](https://portal.azure.com)alındı.
1. **App Service** gidin <abbr title="Uygulama kaynağınız bir Web uygulaması, API uygulaması veya mobil uygulama olabilir.">Uygulama kaynağı</abbr>.
1. **Web işleri**' ni seçin.

    ![Web Işleri seçin](../media/web-sites-create-web-jobs/select-webjobs.png)

2. **WebJobs** sayfasında **Ekle**' yi seçin.

   ![WebJob sayfası](../media/web-sites-create-web-jobs/wjblade.png)

3. Tabloda belirtilen **WebJob Ekle** ayarlarını kullanın.

    ![El ile tetiklenen bir WebJob oluşturmak için ayarlanması gereken ayarları gösteren ekran görüntüsü.](../media/web-sites-create-web-jobs/addwjtriggered.png)
    
    | Ayar      | Örnek değer   | 
    | ------------ | ----------------- | 
   | <abbr title="App Service uygulaması içinde benzersiz olan bir ad. Bir harf veya sayı ile başlamalı ve ve dışında özel karakterler içermemelidir `-` `_` .">Name</abbr> | myTriggeredWebJob | 
    | <abbr title="Yürütülebilir dosyayı veya betik dosyanızı içeren bir *. zip* dosyası ve program veya betiği çalıştırmak için gereken destekleyici dosyalar.">Karşıya dosya yükleme</abbr> | ConsoleApp.zip |
    | <abbr title="Türler arasında sürekli, tetiklenen.">Tür</abbr> | Diğini | 
    | <abbr title="Zamanlanmış veya el ile türleri içerir">Tetikleyiciler</a> | El ile | |

4. **Tamam**'a tıklayın. 

   Yeni WebJob, **WebJobs** sayfasında görünür.

   ![Web Işleri listesi](../media/web-sites-create-web-jobs/listallwebjobs.png)

7. **El Ile WebJob çalıştırmak için**, listede adına sağ tıklayın ve **Çalıştır**' a tıklayın.
   
    ![WebJob Çalıştır](../media/web-sites-create-web-jobs/runondemand.png)

