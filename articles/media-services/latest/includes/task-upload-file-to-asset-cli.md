---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: affd55d65ed8454ebfcdf14f697849badf8e5d3c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88658471"
---
<!-- ### Upload files with the CLI -->

Aşağıdaki komut tek bir dosyayı karşıya yükler.  

Aşağıdaki değerleri değiştirin:

1. `/path/to/file`Dosyanın yerel yolunuza geçin.  
1. `MyContainer`VARLıK kimliğini (adı değil) kullanarak daha önce oluşturduğunuz bir varlığa geçin.
1. `MyBlob`Karşıya yüklenen dosya için kullanmak istediğiniz adı değiştirin.
1. `MyStorageAccountName`Kullanmakta olduğunuz depolama hesabının adına geçin.
1. `MyStorageAccountKey`Depolama hesabınızın erişim anahtarına geçin.

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob --acount-name MyStorageAccountName --account-key MyStorageAccountKey
    ```
