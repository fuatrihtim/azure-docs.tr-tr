---
title: DNS Bölgeleri ve kayıtları koruma-Azure DNS
description: Bu öğrenme yolunda, DNS bölgelerini ve kayıt kümelerini Microsoft Azure DNS ' de korumaya başlayın.
services: dns
author: asudbring
ms.service: dns
ms.topic: how-to
ms.date: 2/20/2020
ms.author: allensu
ms.openlocfilehash: 85aaf40237b6b6687c54d4b036f280805c98e7b2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618976"
---
# <a name="how-to-protect-dns-zones-and-records"></a>DNS bölgelerini ve kayıtlarını koruma

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

DNS bölgeleri ve kayıtları kritik kaynaklardır. Bir DNS bölgesini veya tek bir DNS kaydını silmek, hizmet kesintisi oluşmasına neden olabilir. DNS bölgelerinin ve kayıtlarının yetkisiz veya yanlışlıkla yapılan değişikliklere karşı korunması önemlidir.

Bu makalede Azure DNS, özel DNS bölgelerinizi ve kayıtlarınızı bu değişikliklere karşı korumanıza nasıl olanak sağladığını açıklamaktadır.  [Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/overview.md) ve [kaynak kilitleri](../azure-resource-manager/management/lock-resources.md)Azure Resource Manager tarafından sunulan iki güçlü menkul Özellik uygulayacağız.

## <a name="azure-role-based-access-control"></a>Azure rol tabanlı erişim denetimi

Azure rol tabanlı erişim denetimi (Azure RBAC), Azure kullanıcıları, grupları ve kaynakları için ayrıntılı erişim yönetimine izin vermez. Azure RBAC ile, kullanıcıların ihtiyaç duyduğu erişim düzeyine izin verebilirsiniz. Azure RBAC 'nin erişimi yönetmenize nasıl yardımcı olduğu hakkında daha fazla bilgi için bkz. [Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/overview.md).

### <a name="the-dns-zone-contributor-role"></a>DNS bölgesi katılımcısı rolü

DNS bölgesi katılımcısı rolü, özel DNS kaynaklarını yönetmeye yönelik yerleşik bir roldür. Bir kullanıcıya veya gruba uygulanan bu rol, DNS kaynaklarını yönetmesine olanak sağlar.

Kaynak grubu *Myresourcegroup* , contoso Corporation için beş bölge içerir. DNS Yöneticisi DNS bölgesine katkıda bulunan izinleri bu kaynak grubuna vermek, bu DNS bölgeleri üzerinde tam denetim sağlar. Gereksiz izinler verilmesini önler. DNS Yöneticisi sanal makineleri oluşturamaz veya durdurabilir.

Azure RBAC izinlerini atamak için en kolay yol [Azure Portal](../role-based-access-control/role-assignments-portal.md)kullanmaktır.  

Kaynak grubu için **erişim denetimi 'ni (IAM)** açın, ardından **Ekle**' yi seçin ve **DNS bölgesi katılımcısı** rolünü seçin. İzin vermek için gerekli kullanıcıları veya grupları seçin.

![Kaynak grubu düzeyi Azure portal aracılığıyla Azure RBAC](./media/dns-protect-zones-recordsets/rbac1.png)

İzinler, [Azure PowerShell kullanılarak da verilebilir](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

Eşdeğer komut [Azure CLI aracılığıyla da kullanılabilir](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="zone-level-azure-rbac"></a>Bölge düzeyi Azure RBAC

Azure RBAC kuralları bir aboneliğe, bir kaynak grubuna veya tek bir kaynağa uygulanabilir. Bu kaynak tek bir DNS bölgesi veya tek bir kayıt kümesi olabilir.

Örneğin, *Myresourcegroup* kaynak grubu *contoso.com* bölgesini ve bir alt bölge *Customers.contoso.com* içerir. CNAME kayıtları her müşteri hesabı için oluşturulur. CNAME kayıtlarını yönetmek için kullanılan yönetici hesabına, *Customers.contoso.com* bölgesinde kayıt oluşturmak için izinler atanır. Hesap yalnızca *Customers.contoso.com* yönetebilir.

Bölge düzeyinde Azure RBAC izinleri Azure portal aracılığıyla verilebilir.  Bölge için **erişim denetimi (IAM)** açın, **Ekle**' yi seçin, sonra **DNS bölgesi katılımcısı** rolünü seçin ve izin vermek için gerekli kullanıcıları veya grupları seçin.

![Azure portal aracılığıyla DNS bölge düzeyi Azure RBAC](./media/dns-protect-zones-recordsets/rbac2.png)

İzinler, [Azure PowerShell kullanılarak da verilebilir](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to a specific zone

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"
$zon = "<zone name>"
$typ = "Microsoft.Network/DNSZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $typ
```

Eşdeğer komut [Azure CLI aracılığıyla da kullanılabilir](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/DnsZones/<zone name>/"
```

### <a name="record-set-level-azure-rbac"></a>Kayıt kümesi düzeyi Azure RBAC

İzinler, kayıt kümesi düzeyinde uygulanır.  Kullanıcıya ihtiyaç duydukları girişlere denetim verilir ve başka bir değişiklik yapamaz.

Kayıt kümesi düzeyi Azure RBAC izinleri, kayıt kümesi sayfasındaki **Access Control (IAM)** düğmesini kullanarak Azure Portal aracılığıyla yapılandırılabilir:

![Azure portal aracılığıyla kayıt kümesi düzeyi Azure RBAC](./media/dns-protect-zones-recordsets/rbac3.png)

Kayıt kümesi düzeyi Azure RBAC izinleri, [Azure PowerShell kullanılarak da verilebilir](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

Eşdeğer komut [Azure CLI aracılığıyla da kullanılabilir](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Özel roller

Yerleşik DNS bölgesi katılımcısı rolü bir DNS kaynağı üzerinde tam denetim olanağı sunar. Daha ayrıntılı denetim sağlamak için kendi özel Azure rollerinizi oluşturmak mümkündür.

CNAMEs 'leri yönetmek için kullanılan hesaba yalnızca CNAME kayıtlarını yönetme izni verilir. Hesap, diğer türlerin kayıtlarını değiştiremiyor. Hesap, bölge silme gibi bölge düzeyindeki işlemleri yapamıyor.

Aşağıdaki örnekte yalnızca CNAME kayıtlarını yönetmek için özel bir rol tanımı gösterilmektedir:

```json
{
    "Name": "DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/dnsZones/CNAME/*",
        "Microsoft.Network/dnsZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

Actions özelliği, aşağıdaki DNS 'e özgü izinleri tanımlar:

* `Microsoft.Network/dnsZones/CNAME/*` CNAME kayıtları üzerinde tam denetim verir
* `Microsoft.Network/dnsZones/read` DNS bölgelerini okuma izni verir, ancak bunları değiştirmemelidir, CNAME 'in oluşturulduğu bölgeyi görmenizi sağlar.

Kalan eylemler, [DNS bölgesi katılımcısı yerleşik rolünden](../role-based-access-control/built-in-roles.md#dns-zone-contributor)kopyalanır.

> [!NOTE]
> Kayıt kümelerinin güncelleştirilmesini engellemek için Azure özel rolü kullanma, hala güncelleştirilebilmesine izin verirken etkili bir denetim değildir. Kayıt kümelerinin silinmesini engeller, ancak değiştirilmesini engellemez.  İzin verilen değişiklikler, kayıt kümesinden kayıtları ekleme ve kaldırma, boş bir kayıt kümesi bırakmak için tüm kayıtları kaldırma dahil. Bu, kayıt kümesini bir DNS çözünürlüğü bakış açısından silme ile aynı etkiye sahiptir.

Özel rol tanımları Şu anda Azure portal aracılığıyla tanımlanamaz. Bu rol tanımına dayalı özel bir rol, Azure PowerShell kullanılarak oluşturulabilir:

```azurepowershell
# Create new role definition based on input file
New-AzRoleDefinition -InputFile <file path>
```

Azure CLı aracılığıyla da oluşturulabilir:

```azurecli
# Create new role definition based on input file
az role create -inputfile <file path>
```

Daha sonra bu makalede açıklandığı gibi, rol yerleşik rollerle aynı şekilde atanabilir.

Özel roller oluşturma, yönetme ve atama hakkında daha fazla bilgi için bkz. [Azure özel roller](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Kaynak kilitleri

Azure Resource Manager, kaynakları kilitleme özelliği olan başka tür güvenlik denetimini destekler. Kaynak kilitleri kaynağa uygulanır ve tüm kullanıcılar ve roller arasında etkilidir. Daha fazla bilgi için bkz. [Azure Resource Manager ile kaynakları kilitleme](../azure-resource-manager/management/lock-resources.md).

İki tür kaynak kilidi vardır: **Cannotdelete** ve **ReadOnly**. Bu kilit türleri bir Özel DNS bölgesine veya tek bir kayıt kümesine uygulanabilir. Aşağıdaki bölümlerde bazı yaygın senaryolar ve kaynak kilitleri kullanılarak bunların nasıl destekleyeceği açıklanır.

### <a name="protecting-against-all-changes"></a>Tüm değişikliklere karşı koruma

Yapılan değişikliklerin oluşmasını engellemek için, bölgeye salt okunur bir kilit uygulayın. Bu kilit, yeni kayıt kümelerinin oluşturulmasını ve varolan kayıt kümelerinin değiştirilmesini veya silinmesini engeller.

Bölge düzeyi kaynak kilitleri Azure portal aracılığıyla oluşturulabilir.  DNS bölgesi sayfasında, **kilitler**' ı seçin ve **+ Ekle**' yi seçin:

![Azure portal aracılığıyla bölge düzeyi kaynak kilitleri](./media/dns-protect-zones-recordsets/locks1.png)

Bölge düzeyi kaynak kilitleri [Azure PowerShell](/powershell/module/az.resources/new-azresourcelock)aracılığıyla da oluşturulabilir:

```azurepowershell
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/DNSZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Eşdeğer komut [Azure CLI aracılığıyla da kullanılabilir](/cli/azure/lock#az-lock-create):

```azurecli
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "DnsZones" \
--resource-group "<resource group name>"
```

### <a name="protecting-individual-records"></a>Bireysel kayıtları koruma

Varolan bir DNS kaydının değişikliğe karşı yapılmasını engellemek için kayıt kümesine salt okunur bir kilit uygulayın.

> [!NOTE]
> Kayıt kümesine CanNotDelete kilidi uygulamak, etkili bir denetim değildir. Kayıt kümesinin silinmesini engeller, ancak değiştirilmesini engellemez.  İzin verilen değişiklikler, kayıt kümesinden kayıtları ekleme ve kaldırma, boş bir kayıt kümesi bırakmak için tüm kayıtları kaldırma dahil. Bu, kayıt kümesini bir DNS çözünürlüğü bakış açısından silme ile aynı etkiye sahiptir.

Kayıt kümesi düzeyi kaynak kilitleri, şu anda yalnızca Azure PowerShell kullanılarak yapılandırılabilir.  Azure portal veya Azure CLı 'de desteklenmez.

```azurepowershell
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>/<record set name>"
$rty = "Microsoft.Network/DNSZones/<record type>"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

### <a name="protecting-against-zone-deletion"></a>Bölge silmeye karşı koruma

Azure DNS bir bölge silindiğinde, bölgedeki tüm kayıt kümeleri silinir.  Bu işlem geri alınamaz. Yanlışlıkla kritik bir bölgeyi silmenin önemli bir iş etkisi olması olası olur.  Yanlışlıkla bölge silmeye karşı korunması önemlidir.

Bir bölgeye CanNotDelete kilidi uygulamak bölgenin silinmesini engeller. Kilitler alt kaynaklar tarafından devralınır. Kilit, bölgedeki tüm kayıt kümelerinin silinmesini engeller. Yukarıdaki notta açıklandığı gibi, kayıtların mevcut kayıt kümelerinden hala kaldırılabileceği için bu, verimsiz değildir.

Alternatif olarak, bölgenin SOA kayıt kümesi gibi bir kayıt kümesine bir CanNotDelete kilidi uygulayın. Bölge, kayıt kümelerini silmeksizin silinmez. Bu kilit bölge silmeye karşı koruma sağlarken bölge içindeki kayıt kümelerinin de serbestçe değiştirilmesini sağlar. Bölgeyi silmeye yönelik bir girişim yapılırsa Azure Resource Manager Bu kaldırma işlemini algılar. Kaldırma işlemi SOA kayıt kümesini de siler, Azure Resource Manager SOA kilitli olduğu için çağrıyı engeller.  Hiçbir kayıt kümesi silinmez.

Aşağıdaki PowerShell komutu, belirtilen bölgenin SOA kaydında bir CanNotDelete kilidi oluşturur:

```azurepowershell
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rsc = "<zone name>/@"
$rty = "Microsoft.Network/DNSZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Yanlışlıkla bölge silmeyi engellemek için başka bir seçenek de özel bir rol kullanmaktır. Bu rol, bölgelerinizi yönetmek için kullanılan hesapların bölge silme izinlerine sahip olmamasını sağlar. 

Bir bölgeyi silmeniz gerektiğinde, iki adımlı bir silmeyi zorunlu kılabilirsiniz:

 - İlk olarak, bölge silme izinleri verin
 - İkinci olarak, bölgeyi silmek için izin verin.

Özel rol, bu hesaplar tarafından erişilen tüm bölgeler için geçerlidir. Abonelik sahibi gibi bölge silme izinlerine sahip hesaplar yine de yanlışlıkla bir bölgeyi silebilir.

Aynı anda, DNS bölgesi korumasına yönelik derinlemesine savunma yaklaşımı olarak hem yaklaşımların kaynak kilitleri hem de özel roller kullanmak mümkündür.

## <a name="next-steps"></a>Sonraki adımlar

* Azure RBAC ile çalışma hakkında daha fazla bilgi için bkz. [Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/overview.md).
* Kaynak kilitleri ile çalışma hakkında daha fazla bilgi için bkz. [Azure Resource Manager ile kaynakları kilitleme](../azure-resource-manager/management/lock-resources.md).
