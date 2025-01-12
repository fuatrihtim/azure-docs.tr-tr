---
title: Azure 'da Linux VM 'lerinde güvenlik ilkelerini zorunlu tutun
description: Azure Resource Manager Linux sanal makinesine ilke uygulama
author: mimckitt
ms.service: virtual-machines
ms.collection: linux
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: c1fb80001f0669672e9f6e8a567685b799314df1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549830"
---
# <a name="apply-policies-to-linux-vms-with-azure-resource-manager"></a>Azure Resource Manager ile Linux VM 'lerine ilke uygulama
Bir kuruluş, ilkeleri kullanarak kuruluş genelinde çeşitli kuralları ve kuralları zorunlu kılabilir. İstenen davranışın uygulanması, kuruluşun başarısına katkıda bulunmaya karşın riski azaltmaya yardımcı olabilir. Bu makalede, kuruluşunuzun sanal makineleri için istenen davranışı tanımlamak üzere Azure Resource Manager ilkelerini nasıl kullanabileceğinizi betimliyoruz.

İlkelere giriş için bkz. [Azure ilkesi nedir?](../../governance/policy/overview.md).

## <a name="permitted-virtual-machines"></a>İzin verilen sanal makineler
Kuruluşunuzun sanal makinelerinin bir uygulamayla uyumlu olduğundan emin olmak için izin verilen işletim sistemlerini kısıtlayabilirsiniz. Aşağıdaki ilke örneğinde, yalnızca Ubuntu 14.04.2-LTS sanal makinelerinin oluşturulmasını sağlayabilirsiniz.

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "in": [
          "Microsoft.Compute/disks",
          "Microsoft.Compute/virtualMachines",
          "Microsoft.Compute/VirtualMachineScaleSets"
        ]
      },
      {
        "not": {
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "Canonical"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "UbuntuServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "14.04.2-LTS"
              ]
            },
            {
              "field": "Microsoft.Compute/imageVersion",
              "in": [
                "latest"
              ]
            }
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Yukarıdaki ilkeyi herhangi bir Ubuntu LTS görüntüsüne izin verecek şekilde değiştirmek için bir joker karakter kullanın: 

```json
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

İlke alanları hakkında bilgi için bkz. [ilke diğer adları](../../governance/policy/concepts/definition-structure.md#aliases).

## <a name="managed-disks"></a>Yönetilen diskler

Yönetilen disklerin kullanımını gerektirmek için aşağıdaki ilkeyi kullanın:

```json
{
  "if": {
    "anyOf": [
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/osDisk.uri",
            "exists": true
          }
        ]
      },
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/VirtualMachineScaleSets"
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osDisk.vhdContainers",
                "exists": true
              },
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl",
                "exists": true
              }
            ]
          }
        ]
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="images-for-virtual-machines"></a>Sanal makineler için görüntüler

Güvenlik nedenleriyle, ortamınızda yalnızca onaylanan özel görüntülerin dağıtılmasını zorunlu kılabilirsiniz. Onaylanan görüntüleri içeren kaynak grubunu ya da onaylanan belirli görüntüleri belirtebilirsiniz.

Aşağıdaki örnek, onaylanan bir kaynak grubundan görüntüleri gerektirir:

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in": [
                    "Microsoft.Compute/virtualMachines",
                    "Microsoft.Compute/VirtualMachineScaleSets"
                ]
            },
            {
                "not": {
                    "field": "Microsoft.Compute/imageId",
                    "contains": "resourceGroups/CustomImage"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
} 
```

Aşağıdaki örnek, onaylanan görüntü kimliklerini belirtir:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Sanal makine uzantıları

Belirli uzantı türlerinin kullanımını sağlamak isteyebilirsiniz. Örneğin, bir uzantı belirli özel sanal makine görüntüleriyle uyumlu olmayabilir. Aşağıdaki örnek, belirli bir uzantının nasıl engelleyeceğinizi gösterir. Hangi uzantının engelleneceğini belirleyen yayımcıyı ve türünü kullanır.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "equals": "{extension-type}"

      }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```


## <a name="next-steps"></a>Sonraki adımlar
* Bir ilke kuralı tanımladıktan sonra (önceki örneklerde gösterildiği gibi), ilke tanımını oluşturmanız ve bir kapsama atamanız gerekir. Kapsam bir abonelik, kaynak grubu veya kaynak olabilir. İlke atamak için bkz. [kaynak ilkeleri atamak ve yönetmek için Azure Portal kullanma](../../governance/policy/assign-policy-portal.md), [Ilke atamak için PowerShell kullanma](../../governance/policy/assign-policy-powershell.md)veya [Ilke atamak için Azure CLI kullanma](../../governance/policy/assign-policy-azurecli.md).
* Kaynak ilkelerine giriş için bkz. [Azure ilkesi nedir?](../../governance/policy/overview.md).
* Kuruluşların abonelikleri etkili bir şekilde yönetmek için Resource Manager'ı nasıl kullanabileceği hakkında yönergeler için bkz. [Azure kurumsal iskelesi: öngörücü abonelik idaresi](/azure/architecture/cloud-adoption-guide/subscription-governance).
