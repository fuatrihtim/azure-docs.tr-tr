---
title: CLı kullanarak işletim sistemi diskleri arasında takas et
description: CLı kullanarak bir Azure sanal makinesi tarafından kullanılan işletim sistemi diskini değiştirin.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: adcbc5b0ba0056c79576e8d4c8ded2150b206513
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87371881"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>CLı kullanarak bir Azure VM tarafından kullanılan işletim sistemi diskini değiştirme


Var olan bir VM varsa, ancak diski bir yedekleme diski veya başka bir işletim sistemi diski için değiştirmek istiyorsanız, işletim sistemi disklerini değiştirmek için Azure CLı 'yi kullanabilirsiniz. VM 'yi silip yeniden oluşturmanız gerekmez. Zaten kullanımda olmadığı sürece, başka bir kaynak grubunda yönetilen bir disk de kullanabilirsiniz.

VM 'nin stopped\serbest bırakılmış olması gerekir, ardından yönetilen diskin kaynak KIMLIĞI, farklı bir yönetilen diskin kaynak KIMLIĞI ile değiştirilebilir. 

VM boyutunun ve depolama türünün iliştirmek istediğiniz diskle uyumlu olduğundan emin olun. Örneğin, kullanmak istediğiniz disk Premium depolamada ise, VM 'nin Premium depolama alanı (DS serisi boyutu gibi) olması gerekir.

Bu makale, Azure CLı sürüm 2.0.25 veya üstünü gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme]( /cli/azure/install-azure-cli). 


Kaynak grubunuzdaki disklerin listesini almak için [az disk List](/cli/azure/disk) kullanın.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Diskleri değiştirmeden önce VM 'yi [durdurmak için az VM stop](/cli/azure/vm) komutunu kullanın.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Parametresi için yeni diskin tam kaynak KIMLIĞIYLE [az VM Update](/cli/azure/vm#az-vm-update) kullanın `--osdisk` 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
[Az VM start](/cli/azure/vm)kullanarak VM 'yi yeniden başlatın.

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Sonraki adımlar**

Bir diskin kopyasını oluşturmak için bkz. [disk anlık görüntüsü](snapshot-copy-managed-disk.md).
