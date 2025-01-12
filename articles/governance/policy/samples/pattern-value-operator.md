---
title: 'Model: bir ilke tanımındaki değer işleci'
description: Bu Azure Ilke modelinde, bir ilke tanımında değer işlecinin nasıl kullanılacağına ilişkin bir örnek verilmiştir.
ms.date: 10/14/2020
ms.topic: sample
ms.openlocfilehash: 8392c69ff3d63ff4ecad2a26d5d914b4766147b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92072892"
---
# <a name="azure-policy-pattern-the-value-operator"></a>Azure Ilke stili: değer operatörü

[Value](../concepts/definition-structure.md#value) işleci [parametreleri](../concepts/definition-structure.md#parameters), [desteklenen şablon işlevlerini](../concepts/definition-structure.md#policy-functions)veya sabit değerlerini belirli bir [koşul](../concepts/definition-structure.md#conditions)için sağlanan bir değere değerlendirir.

> [!WARNING]
> Bir _şablon işlevinin_ sonucu bir hata ise, ilke değerlendirmesi başarısız olur. Başarısız bir değerlendirme örtük bir **reddetme**. Daha fazla bilgi için bkz. [şablon arızalarını önleme](../concepts/definition-structure.md#avoiding-template-failures).

## <a name="sample-policy-definition"></a>Örnek ilke tanımı

Bu ilke tanımı, kaynaklardaki **TagName** (_String_) parametresinde belirtilen etiketi ekler veya değiştirir ve kaynak grubundaki **TagName** için değeri kaynağın bulunduğu kaynak grubundan devralır. Bu değerlendirme, kaynak oluşturulduğunda veya güncellendiğinde gerçekleşir. [Değişiklik](../concepts/effects.md#modify) etkisi olarak, düzeltme bir [Düzeltme görevi](../how-to/remediate-resources.md)aracılığıyla mevcut kaynaklarda çalıştırılabilir.

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json":::

### <a name="explanation"></a>Açıklama

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json" range="20-30" highlight="7,8":::

**Value** Işleci, **policyrule. If** blok **özellikleri** içinde kullanılır. Bu örnekte, her iki koşullu deyimin de etkili **olması, değişikliğin** gerçekleşmesi için doğru olması gerektiğini belirten **allof** [mantıksal işleci](../concepts/definition-structure.md#logical-operators) kullanılır.

**değer** , [resourceGroup ()](../../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup) şablon işlevinin sonucunu boş bir değerin koşul **notlarında** değerlendirir. Üst kaynak grubunda **TagName** içinde belirtilen etiket adı varsa, koşullu doğru olarak değerlendirilir.

## <a name="next-steps"></a>Sonraki adımlar

- Diğer [desenleri ve yerleşik tanımları](./index.md)gözden geçirin.
- [Azure İlkesi tanımı yapısını](../concepts/definition-structure.md) gözden geçirin.
- [İlkenin etkilerini anlama](../concepts/effects.md) konusunu gözden geçirin.