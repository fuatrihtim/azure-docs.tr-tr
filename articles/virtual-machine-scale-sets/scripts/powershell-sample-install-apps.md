---
title: Azure PowerShell örnekleri-uygulama yüklemesi
description: Bu betik Windows Server 2016 çalıştıran bir sanal makine ölçek kümesi oluşturur ve Özel Betik Uzantısı kullanarak temel bir web uygulaması yükler.
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: powershell
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: ea4dbf06bd1c129131d57748986e22feca80d808
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079617"
---
# <a name="install-applications-into-a-virtual-machine-scale-set-with-powershell"></a>PowerShell ile sanal makine ölçek kümesine uygulama yükleme
Bu betik Windows Server 2016 çalıştıran bir sanal makine ölçek kümesi oluşturur ve Özel Betik Uzantısı kullanarak temel bir web uygulaması yükler. Betiği çalıştırdıktan sonra web uygulamasına web tarayıcısı üzerinden erişebilirsiniz.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Örnek betik

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/install-apps/install-apps.ps1 "Install apps into a scale set")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme
Kaynak grubunu, ölçek kümesini ve ilgili tüm kaynakları kaldırmak için aşağıdaki komutu çalıştırın.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Betik açıklaması
Bu betik, dağıtımı oluşturmak için aşağıdaki komutları kullanır. Tablodaki her öğe, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [New-AzVmss](/powershell/module/az.compute/new-azvmss) | Sanal makine ölçek kümesi ve sanal ağ, yük dengeleyici ve NAT kurallarının dahil olduğu tüm destekleyici kaynakları oluşturur. |
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Sanal makine ölçek kümesi hakkında bilgi alır. |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) | Özel Betik için sanal makine uzantısı ekleyerek temel bir web uygulaması yükler. |
| [Güncelleştirme-AzVmss](/powershell/module/az.compute/update-azvmss) | Sanal makine uzantısını uygulamak için sanal makine ölçek kümesi modelini güncelleştirir. |
| [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) | Yük dengeleyici tarafından kullanılan atanan genel IP adresi hakkında bilgi alır. |
|  [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Kaynak grubunu ve grubun içerdiği tüm kaynakları kaldırır. |

## <a name="next-steps"></a>Sonraki adımlar
Azure PowerShell modülü hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/azure/).
