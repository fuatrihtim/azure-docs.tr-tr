---
ms.openlocfilehash: df6137e4e00ebd089ef7fb5efa163a513fb30728
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98061070"
---
Bu hızlı başlangıç için Azure kaynaklarını ayarlarken, bir park lotunun kısa bir videosu, Azure 'daki IoT Edge cihaz olarak kullanılan Linux VM 'sine kopyalanır. Bu video dosyası, bu öğretici için canlı bir akışın benzetimini yapmak üzere kullanılacaktır.

[VLC medya oynatıcı](https://www.videolan.org/vlc/)gibi bir uygulama açın, CTRL + N ' ı seçin ve [Bu bağlantıyı](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) Park lotu videosunu seçerek kayıttan yürütmeyi başlatın. 5 saniyelik işaret hakkında bir beyaz araba, Park partisi üzerinden geçer.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LUbN]

Arabasının hareketini algılamak ve 5 saniyelik işaret etrafında bir video klibi kaydetmek için IoT Edge üzerinde canlı video analizlerini kullanmak için aşağıdaki adımları izleyin.
