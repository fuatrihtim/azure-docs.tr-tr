---
title: include dosyası
description: include dosyası
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 08/13/2020
ms.author: danlep
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 173c9156f253e43111299b53287e97ab7b2c0aa5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92746939"
---
## <a name="create-azure-container-registry"></a>Azure kapsayıcı kayıt defteri oluşturma

Kapsayıcı kayıt defterinizi oluşturmadan önce bunun dağıtılacağı bir *kaynak grubu* gerekir. Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal koleksiyondur.

[az group create][az-group-create] komutuyla bir kaynak grubu oluşturun. Aşağıdaki örnekte, *eastus* bölgesinde *myResourceGroup* adlı bir kaynak grubu oluşturulur:

```azurecli
az group create --name myResourceGroup --location eastus
```

Kaynak grubunu oluşturduktan sonra [az acr create][az-acr-create] komutuyla bir Azure kapsayıcı kayıt defteri oluşturun. Kapsayıcı kayıt defteri adı Azure’da benzersiz olmalı ve 5-50 arası alfasayısal karakter içermelidir. `<acrName>` değerini kayıt defteriniz için benzersiz bir adla değiştirin:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Aşağıda, *mycontainerregistry082* adlı yeni bir Azure Container Registry için kısmi çıktı verilmiştir:

```output
{
  "creationDate": "2020-07-16T21:54:47.297875+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry082",
  "location": "eastus",
  "loginServer": "mycontainerregistry082.azurecr.io",
  "name": "mycontainerregistry082",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Bu öğreticinin geri kalan aşamalarında, bu adımda seçtiğiniz kapsayıcı kayıt defteri adı için yer tutucu olarak `<acrName>` kullanılmaktadır.

## <a name="log-in-to-container-registry"></a>Kapsayıcı kayıt defterinde oturum açma

Görüntü göndermeden önce, Azure Container Registry örneğinizde oturum açmanız gerekir. İşlemi tamamlamak için [az acr login][az-acr-login] komutunu kullanın. Oluşturduğunuzda, kapsayıcı kayıt defteri için seçtiğiniz benzersiz adı sağlamanız gerekir.

```azurecli
az acr login --name <acrName>
```

Örnek:

```azurecli
az acr login --name mycontainerregistry082
```

Komut tamamlandığında `Login Succeeded` döndürülür:

```output
Login Succeeded
```

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create
