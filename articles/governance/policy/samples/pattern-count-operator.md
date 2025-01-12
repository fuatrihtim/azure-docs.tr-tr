---
title: 'Model: bir ilke tanımındaki Count işleci'
description: Bu Azure Ilke deseninin Count işlecinin bir ilke tanımında nasıl kullanılacağına ilişkin bir örnek sağlar.
ms.date: 10/14/2020
ms.topic: sample
ms.openlocfilehash: 1339dff7f8bc92a8e38ec5635690cc2069dd8df4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005427"
---
# <a name="azure-policy-pattern-the-count-operator"></a>Azure Ilke stili: Count işleci

[Count](../concepts/definition-structure.md#count) işleci bir \[ \* \] diğer adın üyelerini değerlendirir.

## <a name="sample-policy-definition"></a>Örnek ilke tanımı

Bu ilke tanımı, gelen Uzak Masaüstü Protokolü (RDP) trafiğine izin verecek şekilde yapılandırılmış ağ güvenlik gruplarını [denetler](../concepts/effects.md#audit) .

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json":::

### <a name="explanation"></a>Açıklama

**Count** işlecinin temel bileşenleri _alan_, _nerede_ ve durumdur. Her biri aşağıdaki kod parçacığında vurgulanır.

- _alan_ , üyelerini değerlendirmek için kullanılacak [diğer ad](../concepts/definition-structure.md#aliases) sayısını belirtir. Burada ağ güvenlik grubunun **\[ \* \] SecurityRules** diğer ad _dizisine_ bakıyoruz.
- , hangi _dizi_ üyelerinin ölçütlere uygun olduğunu tanımlamak için ilke _dilini kullanır._ Bu örnekte, **allof** Logical operator diğer ad _dizisi_ özelliklerinin üç farklı koşul değerlendirmesini gruplandırır: _Direction_, _Access_ ve _destinationPortRange_.
- Bu örnekteki sayı koşulu **daha büyüktür**. Diğer ad _dizisinin_ bir veya daha fazla üyesi _WHERE_ yan tümcesiyle eşleştiğinde Count değeri true olarak değerlendirilir.

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json" range="12-32" highlight="3,4,20":::

## <a name="next-steps"></a>Sonraki adımlar

- Diğer [desenleri ve yerleşik tanımları](./index.md)gözden geçirin.
- [Azure İlkesi tanımı yapısını](../concepts/definition-structure.md) gözden geçirin.
- [İlkenin etkilerini anlama](../concepts/effects.md) konusunu gözden geçirin.