---
title: Düzeltilebilir bir ilke dağıtma
description: Azure 'da bir düzeltme görevi kullanan ilkeleri dağıtmak için, müşteri kiracısında bir yönetilen kimlik oluşturmanız gerekir.
ms.date: 01/14/2021
ms.topic: how-to
ms.openlocfilehash: 01070133241117596bdf2b8e1e7c3fa101fc656c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98233891"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>Temsilci bir abonelik içinde düzeltilebilir bir ilke dağıtın

[Azure ışıklı kullanım](../overview.md) , hizmet sağlayıcılarının, temsilci bir abonelik içinde ilke tanımları oluşturmalarına ve düzenlemesine izin verir. Ancak, bir [Düzeltme görevi](../../governance/policy/how-to/remediate-resources.md) (yani, [Deployifnotexists](../../governance/policy/concepts/effects.md#deployifnotexists) veya [değişiklik](../../governance/policy/concepts/effects.md#modify) etkisi olan ilkeler) kullanan ilkeleri dağıtmak için, müşteri kiracısında [yönetilen bir kimlik](../../active-directory/managed-identities-azure-resources/overview.md) oluşturmanız gerekir. Bu yönetilen kimlik, şablonu ilke içinde dağıtmak için Azure Ilkesi tarafından kullanılabilir. Bu senaryoyu etkinleştirmek için, hem Azure tarafından atanan kaynak yönetimine ilişkin müşteriyi eklediğinizde hem de ilkeyi dağıtırken gereken adımlar vardır.

> [!TIP]
> Bu konudaki hizmet sağlayıcılarına ve müşterilere başvurduğumuz halde, [birden çok kiracıyı yöneten kuruluşlar](../concepts/enterprise.md) aynı işlemlerin aynısını kullanabilir.

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>Müşteri kiracısında yönetilen bir kimliğe roller atayabilecek bir kullanıcı oluşturun

Azure 'da bir müşteri eklediğinizde, müşteri kiracısında Temsilcili kaynaklara erişim izni veren yetkilendirmeler tanımlamak için bir parametre dosyası ile birlikte bir [Azure Resource Manager şablonu](onboard-customer.md#create-an-azure-resource-manager-template) kullanırsınız. Her yetkilendirme, yönetim kiracısındaki bir Azure AD kullanıcısına, gruba veya hizmet sorumlusuna karşılık gelen bir **PrincipalId** ve verilecek [Azure yerleşik rolüne](../../role-based-access-control/built-in-roles.md) karşılık gelen bir **roledefinitionıd** belirler.

**PrincipalId** 'nin müşteri kiracısında yönetilen bir kimlik oluşturmasına izin vermek Için, **roledefinitionıd** değerini **Kullanıcı erişimi Yöneticisi** olarak ayarlamanız gerekir. Bu rol genellikle desteklenmekle birlikte, bu özel senaryoda kullanılabilir, bu izne sahip kullanıcı hesaplarının yönetilen kimliklere bir veya daha fazla yerleşik rol atamasını sağlar. Bu roller, **Delegatedrokadefinitionıds** özelliğinde tanımlanmıştır ve Kullanıcı erişimi Yöneticisi veya sahibi dışında [desteklenen Azure yerleşik rollerinin](../concepts/tenants-users-roles.md#role-support-for-azure-lighthouse) dahil edilebilir.

Müşteri eklendi olduktan sonra, bu yetkilendirmede oluşturulan **PrincipalId** , bu yerleşik rolleri müşteri kiracısındaki yönetilen kimliklere atayabilecektir. Ancak, normalde Kullanıcı erişimi Yöneticisi rolüyle ilişkili başka hiçbir izinleri olmayacaktır.

Aşağıdaki örnekte, Kullanıcı erişimi yönetici rolüne sahip olacak bir **PrincipalId** gösterilmektedir. Bu Kullanıcı, müşteri kiracısında yönetilen kimliklere iki yerleşik rol atayabilecektir: katkıda bulunan ve Log Analytics katkıda bulunan.

```json
{
    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
    "principalIdDisplayName": "Policy Automation Account",
    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "delegatedRoleDefinitionIds": [
         "b24988ac-6180-42a0-ab88-20f7382dd24c",
         "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
    ]
}
```

## <a name="deploy-policies-that-can-be-remediated"></a>Düzeltilenebilir ilkeleri dağıtma

Yukarıda açıklandığı gibi gerekli izinlerle kullanıcıyı oluşturduktan sonra, bu kullanıcı, temsilci olan müşteri abonelikleri içinde düzeltme görevleri kullanan ilkeler dağıtabilir.

Örneğin, bu [örnekte](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring)gösterildiği gibi, müşteri kiracısındaki Azure Key Vault kaynaklarında tanılamayı etkinleştirmek istediğinizi varsayalım. Yönetim kiracısındaki bir kullanıcı uygun izinlerle (yukarıda açıklandığı gibi), bu senaryoyu etkinleştirmek için bir [Azure Resource Manager şablonu](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json) dağıtır.

Temsilci olarak kullanılacak ilke atamasının, Azure portal değil, API 'Ler aracılığıyla yapılması gerektiğini unutmayın. Bunu yaparken, **Apiversion** 'ın yeni **Delegatedmanagedıdentityresourceıd** özelliğini içeren **2019-04-01-Preview** olarak ayarlanması gerekir. Bu özellik, müşteri kiracısında bulunan yönetilen bir kimliği (Azure 'un eklendi ile kullanıma yönelik bir abonelik veya kaynak grubu) eklemenize olanak tanır.

Aşağıdaki örnek, bir **Delegatedmanagedıdentityresourceıd** ile bir rol atamasını gösterir.

```json
"type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2019-04-01-preview",
            "name": "[parameters('rbacGuid')]",
            "dependsOn": [
                "[variables('policyAssignment')]"
            ],
            "properties": {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', variables('rbacContributor'))]",
                "principalType": "ServicePrincipal",
                "delegatedManagedIdentityResourceId": "[concat(subscription().id, '/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment'))]",
                "principalId": "[toLower(reference(concat('/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment')), '2018-05-01', 'Full' ).identity.principalId)]"
            }
```

> [!TIP]
> [Benzer bir örnek](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) , bir etiketi (değiştirme efektini kullanarak) temsilci bir aboneliğe ekleyen veya kaldıran bir ilkenin nasıl dağıtılacağını göstermek için kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure ilkesi](../../governance/policy/index.yml)hakkında bilgi edinin.
- [Azure kaynakları için Yönetilen kimlikler](../../active-directory/managed-identities-azure-resources/overview.md)hakkında bilgi edinin.
