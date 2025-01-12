---
title: Azure CLı kullanarak Genelleştirilmiş görüntüden VM oluşturma
description: Azure CLı kullanarak Genelleştirilmiş görüntü sürümünden bir VM oluşturun.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2bcaf85f61a4d8cf4d23c9c5be7f46d765d77dbb
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551051"
---
# <a name="create-a-vm-from-a-generalized-image-version-using-the-cli"></a>CLı kullanarak Genelleştirilmiş görüntü sürümünden VM oluşturma

Paylaşılan görüntü galerisinde depolanan [Genelleştirilmiş görüntü sürümünden](./shared-image-galleries.md#generalized-and-specialized-images) bir VM oluşturun. Özel bir görüntü kullanarak bir VM oluşturmak istiyorsanız, bkz. [özel bir GÖRÜNTÜDEN VM oluşturma](vm-specialized-image-version-powershell.md). 


## <a name="get-the-image-id"></a>Görüntü KIMLIĞINI al

Tanımların adını ve KIMLIĞINI görmek için [az Sig Image-Definition List](/cli/azure/sig/image-definition#az-sig-image-definition-list) öğesini kullanarak bir galerideki görüntü tanımlarını listeleyin.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list --resource-group $resourceGroup --gallery-name $gallery --query "[].[name, id]" --output tsv
```

## <a name="create-the-vm"></a>Sanal makineyi oluşturma

[az vm create](/cli/azure/vm#az-vm-create) kullanarak VM oluşturun. Görüntünün en son sürümünü kullanmak için, `--image` görüntü TANıMıNıN kimliğine ayarlayın. 

Kaynak adlarını bu örnekte gereken şekilde değiştirin. 

```azurecli-interactive 
imgDef="/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition"
vmResourceGroup=myResourceGroup
location=eastus
vmName=myVM
adminUsername=azureuser


az group create --name $vmResourceGroup --location $location

az vm create\
   --resource-group $vmResourceGroup \
   --name $vmName \
   --image $imgDef \
   --admin-username $adminUsername \
   --generate-ssh-keys
```

Ayrıca, parametresi için görüntü sürümü KIMLIĞINI kullanarak belirli bir sürümü kullanabilirsiniz `--image` . Örneğin, görüntü sürümü *1.0.0* türünü kullanmak için: `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` .

## <a name="next-steps"></a>Sonraki adımlar

[Azure görüntü Oluşturucu (Önizleme)](./image-builder-overview.md) , görüntü sürümü oluşturmayı otomatikleştirmenize yardımcı olabilir, hatta [mevcut bir görüntü sürümünden yeni bir görüntü sürümünü](./linux/image-builder-gallery-update-image-version.md)güncelleştirmek ve oluşturmak için de kullanabilirsiniz.