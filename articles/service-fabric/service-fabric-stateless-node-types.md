---
title: Service Fabric kümesinde yalnızca durum bilgisi olan düğüm türlerini dağıtma
description: Azure Service Fabric kümesinde durum bilgisiz düğüm türleri oluşturmayı ve dağıtmayı öğrenin.
author: peterpogorski
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: pepogors
ms.openlocfilehash: eb19005019a6e4e878f6b0bd6a145048d4a2804c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563785"
---
# <a name="deploy-an-azure-service-fabric-cluster-with-stateless-only-node-types-preview"></a>Yalnızca durum bilgisi olan düğüm türleriyle bir Azure Service Fabric kümesi dağıtma (Önizleme)
Service Fabric düğüm türleri, bazı zaman bir noktada, durum bilgisi olmayan hizmetlerin düğümlere yerleştirilebileceği bir süre ile gelir. Durum bilgisiz düğüm türleri bir düğüm türü için bu varsayımını daha hızlı hale getirme, böylece düğüm türünün daha hızlı genişleme işlemleri gibi diğer özellikleri kullanmasına izin vermek, bu sayede düğüm türünün daha hızlı genişleme işlemleri, en az sayıda düğüm için destek ve tek bir sanal makine ölçek kümesinde 100 ' den fazla düğüme

* Birincil düğüm türleri durum bilgisiz olacak şekilde yapılandırılamaz
* Durum bilgisiz düğüm türleri yalnızca bronz dayanıklılık düzeyleriyle desteklenir
* Durum bilgisiz düğüm türleri yalnızca Service Fabric Runtime sürümü 7.1.409 veya üzeri sürümlerde desteklenir.


Örnek şablonlar mevcuttur: [Service Fabric durum bilgisiz düğüm türleri şablonu](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="enabling-stateless-node-types-in-service-fabric-cluster"></a>Service Fabric kümesinde durum bilgisiz düğüm türlerini etkinleştirme
Bir küme kaynağında durum bilgisiz olarak bir veya daha fazla düğüm türü ayarlamak için, **ısdurumsuz** özelliğini "true" olarak ayarlayın. Durum bilgisiz düğüm türleriyle bir Service Fabric kümesi dağıtımında, küme kaynağında en az bir tane birincil düğüm türü olduğunu unutmayın.

* Service Fabric küme kaynağı apiVersion, "2020-12-01-Preview" veya üzeri olmalıdır.

```json
{
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "isStateless": false,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Bronze",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": false,
        "isStateless": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    }    
    ],
}
```

## <a name="configuring-virtual-machine-scale-set-for-stateless-node-types"></a>Durum bilgisiz düğüm türleri için sanal makine ölçek kümesini yapılandırma
Durum bilgisiz düğüm türlerini etkinleştirmek için, temel alınan sanal makine ölçek kümesi kaynağını aşağıdaki şekilde yapılandırmalısınız:

* 100 ' den fazla VM 'ye ölçeklendirmeniz gerekiyorsa, **false** olarak ayarlanması gereken **Singleplacementgroup** özelliği.
* Ölçek kümesinin **Upgradepolicy** **modu** sıralı olarak **ayarlanmalıdır.**
* Yükseltme modu, uygulama durumu uzantısının veya sistem durumu araştırmalarını gerektirir. Aşağıda önerilen durum bilgisiz düğüm türleri için varsayılan yapılandırmayla sistem durumu araştırmasını yapılandırın. Uygulamalar düğüm türüne dağıtıldıktan sonra, sistem durumu araştırması/sistem durumu uzantısı bağlantı noktaları, uygulama durumunu izlemek için değiştirilebilir.

>[!NOTE]
> Durum bilgisiz düğüm türü birden çok bölgeye yayılan bir sanal makine ölçek kümesi tarafından desteklendiğinde, platform hatası etki alanı sayısının 5 olarak güncelleştirildiğinden, bu gereklidir. Daha fazla ayrıntı için lütfen bu [şablona](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-2-NodeTypes-Windows-Stateless-CrossAZ-Secure) bakın.
> 
> **platformFaultDomainCount: 5**
```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
          "mode": "Rolling",
          "automaticOSUpgradePolicy": {
            "enableAutomaticOSUpgrade": true
          }
        }
    }
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Bronze",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
        },
        "typeHandlerVersion": "1.0"
    }
    },
    {
        "type": "extensions",
        "name": "HealthExtension",
        "properties": {
            "publisher": "Microsoft.ManagedServices",
            "type": "ApplicationHealthWindows",
            "autoUpgradeMinorVersion": true,
            "typeHandlerVersion": "1.0",
            "settings": {
            "protocol": "tcp",
            "port": "19000"
            }
            }
        },
    ]
}
```

## <a name="networking-requirements"></a>Ağ gereksinimleri
### <a name="public-ip-and-load-balancer-resource"></a>Genel IP ve Load Balancer kaynağı
Bir sanal makine ölçek kümesi kaynağında 100 ' den fazla VM 'ye ölçeklendirmeyi etkinleştirmek için, bu sanal makine ölçek kümesi tarafından başvurulan yük dengeleyici ve IP kaynağının her ikisi de *Standart* SKU kullanmalıdır. SKU özelliği olmadan bir yük dengeleyici veya IP kaynağı oluşturmak, 100 ' den fazla VM 'ye ölçeklendirmeyi desteklemeyen temel bir SKU oluşturur. Standart SKU yük dengeleyici, dışarıdaki tüm trafiği varsayılan olarak engeller; Dış trafiğe izin vermek için bir NSG 'nin alt ağa dağıtılması gerekir.

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> Ortak IP ve yük dengeleyici kaynaklarında bir yerinde SKU değişikliği yapmak mümkün değildir. Temel SKU 'SU olan mevcut kaynaklardan geçiş yapıyorsanız, bu makalenin geçiş bölümüne bakın.

### <a name="virtual-machine-scale-set-nat-rules"></a>Sanal makine ölçek kümesi NAT kuralları
Yük dengeleyici gelen NAT kuralları, sanal makine ölçek kümesindeki NAT havuzlarıyla eşleşmelidir. Her sanal makine ölçek kümesinin benzersiz bir gelen NAT havuzu olmalıdır.

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>Standart SKU Load Balancer giden kuralları
Standart Load Balancer ve standart genel IP, temel SKU 'Ları kullanmaya kıyasla giden bağlantılara yeni yetenekler ve farklı davranışlar getirir. Standart SKU 'Lar ile çalışırken giden bağlantı isterseniz, standart genel IP adresleriyle veya standart ortak Load Balancer açıkça tanımlamanız gerekir. Daha fazla bilgi için bkz. [giden bağlantılar](../load-balancer/load-balancer-outbound-connections.md) ve [Azure Standart Load Balancer](../load-balancer/load-balancer-overview.md).

>[!NOTE]
> Standart Şablon, tüm giden trafiğe varsayılan olarak izin veren bir NSG 'ye başvurur. Gelen trafik Service Fabric yönetim işlemleri için gerekli olan bağlantı noktalarıyla sınırlıdır. NSG kuralları gereksinimlerinizi karşılayacak şekilde değiştirilebilir.

>[!NOTE]
> Standart SKU SLB kullanarak her bir Service Fabric kümesi, bağlantı noktası 443 ' de giden trafiğe izin veren her düğüm türünün bir kurala sahip olduğundan emin olunması gerekir. Bu, küme kurulumunu tamamlaması için gereklidir ve bu tür bir kural olmadan herhangi bir dağıtım başarısız olur.



### <a name="migrate-to-using-stateless-node-types-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Temel SKU Load Balancer ve temel SKU IP 'si kullanarak bir kümeden durum bilgisiz düğüm türlerini kullanmak için geçirme
Tüm geçiş senaryolarında, yalnızca durum bilgisi olmayan yeni bir düğüm türünün eklenmesi gerekir. Mevcut düğüm türü yalnızca durum bilgisi olmayan bir şekilde geçirilemez.

Temel SKU ile Load Balancer ve IP kullanan bir kümeyi geçirmek için, önce standart SKU 'yu kullanarak tamamen yeni bir Load Balancer ve IP kaynağı oluşturmanız gerekir. Bu kaynakları yerinde güncelleştirmek mümkün değildir.

Yeni LB ve IP 'yi kullanmak istediğiniz yeni durum bilgisiz düğüm türlerinde başvurulmalıdır. Yukarıdaki örnekte, durum bilgisiz düğüm türleri için kullanılacak yeni bir sanal makine ölçek kümesi kaynakları eklenir. Bu sanal makine ölçek kümeleri, yeni oluşturulan LB ve IP 'ye başvurur ve Service Fabric küme kaynağında durum bilgisiz düğüm türü olarak işaretlenir.

Başlamak için, mevcut Kaynak Yöneticisi şablonunuza yeni kaynakları eklemeniz gerekir. Bu kaynaklar şunları içerir:
* Standart SKU kullanan genel bir IP kaynağı.
* Standart SKU kullanan bir Load Balancer kaynağı.
* Sanal makine ölçek kümelerinizi dağıttığınız alt ağ tarafından başvurulan bir NSG.

Kaynakların dağıtımı tamamlandıktan sonra, özgün kümeden kaldırmak istediğiniz düğüm türündeki düğümleri devre dışı bırakmayı deneyebilirsiniz.

>[!NOTE]
> Bronz dayanıklılık ile, yok etme dayanıklılığına sahip durum bilgisiz olmayan nodetypes ile otomatik ölçeklendirmeyi kullanırken, düğüm durumu otomatik olarak temizlenmez. Otomatik ölçeklendirme sırasında aşağı düğümlerin NodeState 'i temizlemek için [Service Fabric otomatik ölçeklendirme Yardımcısı](https://github.com/Azure/service-fabric-autoscale-helper) kullanılması önerilir.

## <a name="next-steps"></a>Sonraki adımlar 
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Düğüm türleri ve sanal makine ölçek kümeleri](service-fabric-cluster-nodetypes.md)

