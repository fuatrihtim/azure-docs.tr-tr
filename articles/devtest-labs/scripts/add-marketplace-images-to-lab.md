---
title: PowerShell-Azure DevTest Labs bir laboratuvarda Market görüntüsü ekleme
description: Bu PowerShell betiği, Azure DevTest Labs bir laboratuvara Market görüntüsü ekler.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 543f20af270769dd16e4a1ecf6ee93e9259cdfd1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96022305"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs 'de laboratuvara Market görüntüsü eklemek için PowerShell 'i kullanma

Bu örnek PowerShell betiği, Azure DevTest Labs bir laboratuvara Market görüntüsü ekler. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Önkoşullar
* **Laboratuvar**. Betik, mevcut bir laboratuvarınızın olmasını gerektirir. 

## <a name="sample-script"></a>Örnek betik

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik şu komutları kullanır: 

| Komut | Notlar |
|---|---|
| Find-AzResource | Belirtilen parametrelere göre kaynak arar. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Kaynakları alır. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Bir kaynağı değiştirir. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Kaynak oluşturma. |

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/).

Ek Azure Lab Services PowerShell betiği örnekleri [Azure Lab Services PowerShell örneklerinde](../samples-powershell.md)bulunabilir.
