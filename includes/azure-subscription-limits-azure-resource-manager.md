---
title: include dosyası
description: include dosyası
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/15/2021
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 97d80e999ac61a2c2f8f561dc19213419014beb8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103471005"
---
| Kaynak | Sınır |
| --- | --- |
| [Azure Active Directory kiracısıyla ilişkili](../articles/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) abonelikler | Sınırsız |
| Abonelik başına [Coadministrators](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) |Sınırsız |
| Abonelik başına [kaynak grupları](../articles/azure-resource-manager/management/overview.md) |980 |
| Azure Resource Manager API isteği boyutu |4.194.304 bayt |
| Abonelik başına Etiketler<sup>1</sup> |50 |
| Abonelik başına benzersiz etiket hesaplamaları<sup>1</sup> | 80,000 |
| Konum başına [abonelik düzeyi dağıtımlar](../articles/azure-resource-manager/templates/deploy-to-subscription.md) | 800<sup>2</sup> |

<sup>1</sup> Doğrudan bir aboneliğe en fazla 50 etiket uygulayabilirsiniz. Ancak, abonelik, abonelik içindeki kaynak gruplarına ve kaynaklara uygulanan sınırsız sayıda etiket içerebilir. Kaynak veya kaynak grubu başına etiket sayısı 50 ile sınırlıdır. Kaynak Yöneticisi, yalnızca etiket sayısı 80.000 veya daha az olduğunda, abonelikteki [benzersiz etiket adı ve değerlerinin listesini](/rest/api/resources/tags) döndürür. Sayı 80.000 ' i aştığında yine de etikete göre bir kaynak bulabilirsiniz.

<sup>2</sup> 800 dağıtım sınırına ulaşırsanız, artık geçmişten gerekmeyen dağıtımları silin. Abonelik düzeyi dağıtımlarını silmek için [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) veya [az Deployment Sub Delete](/cli/azure/deployment/sub#az-deployment-sub-delete)komutunu kullanın.
