---
title: SKU kullanılamıyor hatası
description: Azure Resource Manager ile kaynak dağıtımında SKU kullanılamıyor hatası ile ilgili sorunların nasıl giderileceği açıklanmaktadır.
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: 5b0bbd653907c109eca526af86979013b3137cfa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737159"
---
# <a name="resolve-errors-for-sku-not-available"></a>Kullanılamayan SKU’larla ilgili hataları giderme

Bu makalede, **Skunotavailable** hatasının nasıl çözümleneceği açıklanır. Bu bölgede/bölgede veya iş gereksinimlerinizi karşılayan alternatif bir bölgede/bölgede uygun bir SKU bulamıyorsanız Azure desteği 'ne bir [SKU isteği](/troubleshoot/azure/general/region-access-request-process) gönderebilirsiniz.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Belirti

Bir kaynak (genellikle bir sanal makine) dağıttığınızda aşağıdaki hata kodunu ve hata iletisini alırsınız:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Nedeni

Seçtiğiniz kaynak SKU 'SU (VM boyutu gibi) seçtiğiniz konum için kullanılabilir olmadığında bu hatayı alırsınız.

Azure spot VM veya spot ölçek kümesi örneği dağıtıyorsanız, bu konumda Azure noktası için herhangi bir kapasite yoktur. Daha fazla bilgi için bkz. [spot hata iletileri](../../virtual-machines/error-codes-spot.md).

## <a name="solution-1---powershell"></a>Çözüm 1-PowerShell

Bir bölgede/bölgede hangi SKU 'Ların kullanılabildiğini öğrenmek için [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) komutunu kullanın. Sonuçları konuma göre filtreleyin. Bu komut için en son PowerShell sürümüne sahip olmanız gerekir.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Sonuçlar, konum ve bu SKU için herhangi bir kısıtlama için SKU 'ların bir listesini içerir. SKU 'nun olarak listelendiğine dikkat edin `NotAvailableForSubscription` .

```output
ResourceType          Name           Locations   Zone      Restriction                      Capability           Value
------------          ----           ---------   ----      -----------                      ----------           -----
virtualMachines       Standard_A0    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2    centralus             NotAvailableForSubscription      MaxResourceVolumeMB  138240
virtualMachines       Standard_D1_v2 centralus   {2, 1, 3}                                  MaxResourceVolumeMB
```

Bazı ek örnekler:

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

"FC" sonuna ekleme daha fazla ayrıntı döndürüyor.

## <a name="solution-2---azure-cli"></a>Çözüm 2-Azure CLı

Bir bölgede hangi SKU 'Ların kullanılabildiğini öğrenmek için `az vm list-skus` komutunu kullanın. `--location`Kullandığınız konuma çıktıyı filtrelemek için parametresini kullanın. `--size`Kısmi bir boyut adına göre arama yapmak için parametresini kullanın.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

Komut şunun gibi sonuçlar döndürür:

```output
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```

## <a name="solution-3---azure-portal"></a>Çözüm 3-Azure portal

Bir bölgede hangi SKU 'Ların kullanılabildiğini öğrenmek için [portalını](https://portal.azure.com)kullanın. Portalda oturum açın ve arabirim aracılığıyla bir kaynak ekleyin. Değerleri ayarladığınız sürece bu kaynak için kullanılabilir SKU 'Ları görürsünüz. Dağıtımı doldurmanız gerekmez.

Örneğin, bir sanal makine oluşturma işlemini başlatın. Kullanılabilir diğer boyutu görmek için **boyutu Değiştir**' i seçin.

![VM oluşturma](./media/error-sku-not-available/create-vm.png)

Kullanılabilir boyutlarda filtre uygulayabilir ve bu boyutları kaydırabilirsiniz.

![Kullanılabilir SKU'lar](./media/error-sku-not-available/available-sizes.png)

## <a name="solution-4---rest"></a>Çözüm 4-REST

Bir bölgede hangi SKU 'Ların kullanılabildiğini öğrenmek için [kaynak SKU 'lar-listeleme](/rest/api/compute/resourceskus/list) işlemini kullanın.

Kullanılabilir SKU 'Ları ve bölgeleri aşağıdaki biçimde döndürür:

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```