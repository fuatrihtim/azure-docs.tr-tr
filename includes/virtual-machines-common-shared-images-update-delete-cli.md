---
title: include dosyası
description: include dosyası
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 4392e7f146f13e581f722b94f13038ad8abff0ba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244806"
---
## <a name="update-resources"></a>Kaynakları güncelleştirme

Nelerin güncelleştirilemeyebilir hakkında bazı sınırlamalar vardır. Aşağıdaki öğeler güncelleştirilemeyebilir: 

Paylaşılan görüntü Galerisi:
- Description

Görüntü tanımı:
- Önerilen vCPU 'Lar
- Önerilen bellek
- Description
- Yaşam tarihi sonu

Görüntü sürümü:
- Bölgesel çoğaltma sayısı
- Hedef bölgeler
- En son dışarıda bırakma
- Yaşam tarihi sonu

Çoğaltma bölgelerini eklemeyi planlıyorsanız, kaynak yönetilen görüntüyü silmeyin. Kaynak yönetilen görüntü, görüntü sürümünü ek bölgelere çoğaltmak için gereklidir. 

([Az SIG Update](/cli/azure/sig#az-sig-update)) kullanarak bir galerinin açıklamasını güncelleştirin. 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


[Az Sig Image-Definition Update](/cli/azure/sig/image-definition#az-sig-image-definition-update)kullanarak bir görüntü tanımının açıklamasını güncelleştirin.

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

[Az Sig Image-Version Update](/cli/azure/sig/image-definition#az-sig-image-definition-update)kullanılarak çoğaltılacak bir bölge eklemek için görüntü sürümünü güncelleştirin. Görüntü yeni bölgeye çoğaltılmasıyla bu değişiklik biraz zaman alır.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

Bu örnek, bu görüntü sürümünün *en son* görüntü olarak kullanılmasını hariç tutmak için [az SIG Image-Version Update](/cli/azure/sig/image-definition#az-sig-image-definition-update) 'in nasıl kullanılacağını gösterir.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=true
```

Bu örnek, *en son* görüntü için göz önünde bulundurulmakta olan bu görüntü sürümünü dahil etmek için [az SIG Image-Version Update](/cli/azure/sig/image-definition#az-sig-image-definition-update) 'in nasıl kullanılacağını gösterir.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=false
```

## <a name="delete-resources"></a>Kaynakları silme

Önce görüntü sürümünü silerek kaynakları ters sırada silmeniz gerekir. Tüm görüntü sürümlerini sildikten sonra, görüntü tanımını silebilirsiniz. Tüm görüntü tanımlarını sildikten sonra, galeriyi silebilirsiniz. 

[Az Sig Image-Version Delete](/cli/azure/sig/image-version#az-sig-image-version-delete)kullanarak bir görüntü sürümünü silin.

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

[Az Sig Image-Definition Delete](/cli/azure/sig/image-definition#az-sig-image-definition-delete)kullanarak bir görüntü tanımını silin.

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


[Az Sig Delete](/cli/azure/sig#az-sig-delete)kullanarak bir görüntü Galerisi silin.

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```