---
title: Azure 'da bir Linux Service Fabric kümesi oluşturma
description: Azure CLI kullanarak mevcut bir Azure sanal ağına Linux Service Fabric kümesi dağıtmayı öğrenin.
ms.topic: conceptual
ms.date: 02/14/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 77cc49c1b79e5c24e78a67a69493aa0b0059d565
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791080"
---
# <a name="deploy-a-linux-service-fabric-cluster-into-an-azure-virtual-network"></a>Azure sanal ağına bir Linux Service Fabric kümesi dağıtma

Bu makalede, Azure CLı ve şablon kullanarak bir [Azure sanal ağına (VNet)](../virtual-network/virtual-networks-overview.md) bir Linux Service Fabric kümesi dağıtmayı öğreneceksiniz. Öğreticiyi tamamladığınızda, bulutta çalışan ve uygulama dağıtabileceğiniz bir kümeniz olur. PowerShell kullanarak Windows kümesi oluşturmak için bkz. [Azure’da güvenli bir Windows kümesi oluşturma](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce:

* Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun
* [Service Fabric CLI](service-fabric-cli.md)'yı yükleyin
* [Azure CLI](/cli/azure/install-azure-cli) 'yı yükler
* Kümelerin temel kavramlarını öğrenmek için [Azure kümelerine genel bakış](service-fabric-azure-clusters-overview.md) konusunu okuyun
* Üretim kümesi dağıtımını [planlayın ve hazırlayın](service-fabric-cluster-azure-deployment-preparation.md) .

Aşağıdaki yordamlar yedi düğümlü Service Fabric kümesi oluşturur. Azure’da Service Fabric kümesi çalıştırmaktan kaynaklanan maliyetleri hesaplamak için [Azure Fiyatlandırma Hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/)’nı kullanın.

## <a name="download-and-explore-the-template"></a>Şablonu indirin ve keşfedin

Aşağıdaki Resource Manager şablonu dosyalarını indirin:

Ubuntu 16,04 LTS için:

* [AzureDeploy.json][template]
* [ ÜzerindeAzureDeploy.Parameters.js][parameters]

Ubuntu 18,04 LTS için:

* [AzureDeploy.json][template2]
* [ ÜzerindeAzureDeploy.Parameters.js][parameters2]

Ubuntu 18,04 LTS için iki şablon arasındaki fark 
* **Vmımagesku** özniteliği "18,04-LTS" olarak ayarlandı
* her düğümün **Typehandlerversion** 'ı 1,1 olarak ayarlanmıştır
* Microsoft. ServiceFabric/kümeler kaynağı
   - **Apiversion** , "2019-03-01" veya üzeri olarak ayarlandı
   - **Vmımage** özelliği "Ubuntu18_04" olarak ayarlandı

Bu şablon, bir sanal ağa yedi sanal makine ve üç düğümlü türden oluşan güvenli bir küme dağıtır.  Diğer örnek şablonlar [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates)'da bulunabilir. [AzureDeploy.json][template] aşağıdakiler dahil bir grup kaynak dağıtır.

### <a name="service-fabric-cluster"></a>Service Fabric kümesi

**Microsoft.ServiceFabric/clusters** kaynağında şu özelliklere sahip bir Linux kümesi dağıtılır:

* üç düğüm türü
* birincil düğüm türünde (şablon parametrelerinde yapılandırılabilir) beş düğüm, diğer düğüm türlerinin her birindeki bir düğüm
* İşletim sistemi: (Ubuntu 16,04 LTS/Ubuntu 18,04 LTS) (şablon parametrelerinde yapılandırılabilir)
* sertifikanın güvenliğinin sağlanması (şablon parametrelerinde yapılandırılabilir)
* [DNS hizmeti](service-fabric-dnsservice.md) etkin
* Bronz [dayanıklılık düzeyi](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) (şablon parametrelerinde yapılandırılabilir)
* Gümüş [güvenilirlik düzeyi](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) (şablon parametrelerinden yapılandırılabilir)
* istemci bağlantısı uç noktası: 19000 (şablon parametrelerinde yapılandırılabilir)
* HTTP ağ geçidi uç noktası: 19080 (şablon parametrelerinde yapılandırılabilir)

### <a name="azure-load-balancer"></a>Azure yük dengeleyici

**Microsoft.Network/loadBalancers** kaynağında bir yük dengeleyici yapılandırılır ve şu bağlantı noktaları için yoklamalar ve kurallar ayarlanır:

* istemci bağlantı uç noktası: 19000
* HTTP ağ geçidi uç noktası: 19080
* uygulama bağlantı noktası: 80
* uygulama bağlantı noktası: 443

### <a name="virtual-network-and-subnet"></a>Sanal ağ ve alt ağ

Sanal ağın ve alt ağın adları şablon parametrelerinde bildirilmiştir.  Sanal ağın ve alt ağın adres alanları da şablon parametrelerinde bildirilir ve **Microsoft.Network/virtualNetworks** kaynağında yapılandırılır:

* sanal ağ adres alanı: 10.0.0.0/16
* Service Fabric alt ağ adres alanı: 10.0.2.0/24

Başka bir uygulama bağlantı noktası gerekiyorsa, gelen trafiğe izin vermek için Microsoft.Network/loadBalancers kaynağını ayarlamak gerekir.

## <a name="set-template-parameters"></a>Şablon parametrelerini ayarlama

**AzureDeploy. Parameters** dosyası, kümeyi ve ilişkili kaynakları dağıtmak için kullanılan birçok değeri bildirir. Dağıtımınız için değiştirmeniz gerekebilecek bazı parametreler:

|Parametre|Örnek değer|Notlar|
|---|---||
|adminUserName|vmadmin| Küme VM’leri için yönetici kullanıcı adı. |
|adminPassword|Password#1234| Küme VM’leri için yönetici parolası.|
|clusterName|mysfcluster123| Kümenin adı. |
|location|southcentralus| Kümenin konumu. |
|certificateThumbprint|| <p>Otomatik olarak imzalanan bir sertifika oluşturuluyor veya sertifika dosyası sağlanıyorsa değer boş olmalıdır.</p><p>Daha önce bir anahtar kasasına yüklenmiş mevcut bir sertifikayı kullanmak için sertifika SHA1 parmak izi değerini girin. Örneğin: "6190390162C988701DB5676EB81083EA608DCCF3". </p>|
|certificateUrlValue|| <p>Otomatik olarak imzalanan bir sertifika oluşturuluyor veya sertifika dosyası sağlanıyorsa değer boş olmalıdır.</p><p>Daha önce bir anahtar kasasına yüklenmiş mevcut bir sertifikayı kullanmak için sertifika URL’sini girin. Örneğin, "https: \/ /mykeyvault.Vault.Azure.net:443/Secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>Otomatik olarak imzalanan bir sertifika oluşturuluyor veya sertifika dosyası sağlanıyorsa değer boş olmalıdır.</p><p>Daha önce bir anahtar kasasına yüklenmiş mevcut bir sertifikayı kullanmak için kaynak kasa değerini girin. Örneğin: "/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT".</p>|

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Sanal ağı ve kümeyi dağıtma

Ardından, ağ topolojisini ayarlayın ve Service Fabric kümesini dağıtın. **AzureDeploy.json** Resource Manager şablonu Service Fabric için bir sanal ağ (VNET) ve bir alt ağ oluşturur. Şablon tarafından sertifika güvenliği etkin bir küme de dağıtılır.  Üretim kümeleri için küme sertifikası olarak bir sertifika yetkilisinden (CA) alınan bir sertifika kullanın. Test kümelerinin güvenliğinin sağlanması için otomatik olarak imzalanan bir sertifika kullanılabilir.

Bu makaledeki şablon, küme sertifikasını tanımlamak için sertifika parmak izini kullanan bir küme dağıtır.  İki sertifika aynı parmak izine sahip olamaz, bu da sertifika yönetimini daha zor hale getirir. Sertifika ortak adlarını kullanmak için sertifika parmak izlerini kullanarak dağıtılan bir kümeyi değiştirmek, sertifika yönetimini çok daha kolay hale getirir.  Sertifikayı sertifika yönetimi için sertifika ortak adlarını kullanacak şekilde güncelleştirme hakkında bilgi edinmek için, [değişiklik kümesini sertifika ortak ad yönetimine](service-fabric-cluster-change-cert-thumbprint-to-cn.md)okuyun.

### <a name="create-a-cluster-using-an-existing-certificate"></a>Mevcut bir sertifikayı kullanarak küme oluşturma

Aşağıdaki betik, mevcut bir sertifikayla güvenliği sağlanmış yeni bir küme dağıtmak için [az sf cluster create](/cli/azure/sf/cluster) komutunu ve şablonunu kullanır. Ayrıca, komut tarafından Azure’da yeni bir anahtar kasası oluşturulur ve sertifikanız karşıya yüklenir.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates\MyCertificate.pem"

# sign in to your Azure account and select your subscription
az login
az account set --subscription <guid>

# Create a new resource group for your deployment and give it a name and a location.
az group create --name $ResourceGroupName --location $Location

# Create the Service Fabric cluster.
az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-password $Password --certificate-file $CertPath \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file AzureDeploy.json --parameter-file AzureDeploy.Parameters.json
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>Yeni ve otomatik olarak imzalanan bir sertifika ile küme oluşturma

Aşağıdaki betik, Azure'a yeni bir küme dağıtmak için [az sf cluster create](/cli/azure/sf/cluster) komutunu ve bir şablonu kullanır. Komut ayrıca Azure 'da yeni bir Anahtar Kasası oluşturur, anahtar kasasına yeni bir kendinden imzalı sertifika ekler ve sertifika dosyasını yerel olarak indirir.

```azurecli
ResourceGroupName="sflinuxclustergroup"
ClusterName="sflinuxcluster"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates"

az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --cluster-name $ClusterName --template-file C:\temp\cluster\AzureDeploy.json \
   --parameter-file C:\temp\cluster\AzureDeploy.Parameters.json --certificate-password $Password \
   --certificate-output-folder $CertPath --certificate-subject-name $ClusterName.$Location.cloudapp.azure.com \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName
```

## <a name="connect-to-the-secure-cluster"></a>Güvenli kümeye bağlanma

Anahtarınızı kullanarak, Service Fabric CLI `sfctl cluster select` komutunu kullanan kümeye bağlanın.  Otomatik olarak imzalanan sertifika için yalnızca **--no-verify** seçeneğinin kullanılması gerektiğini göz önünde bulundurun.

```console
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Bağlı olup olmadığınızı ve kümenin sağlıklı olup olmadığını denetlemek için `sfctl cluster health` komutunu kullanın.

```console
sfctl cluster health
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Hemen bir sonraki makaleye geçmeyecekiz, ücretlendirmeden kaçınmak için [kümeyi silmek](./service-fabric-tutorial-delete-cluster.md) isteyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Bir kümeyi ölçeklendirmeyi](service-fabric-tutorial-scale-cluster.md)öğrenin.

Bu makaledeki şablon, küme sertifikasını tanımlamak için sertifika parmak izini kullanan bir küme dağıtır.  İki sertifika aynı parmak izine sahip olamaz, bu da sertifika yönetimini daha zor hale getirir. Sertifika ortak adlarını kullanmak için sertifika parmak izlerini kullanarak dağıtılan bir kümeyi değiştirmek, sertifika yönetimini çok daha kolay hale getirir.  Sertifikayı sertifika yönetimi için sertifika ortak adlarını kullanacak şekilde güncelleştirme hakkında bilgi edinmek için, [değişiklik kümesini sertifika ortak ad yönetimine](service-fabric-cluster-change-cert-thumbprint-to-cn.md)okuyun.

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.Parameters.json
[template2]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-1804-3-NodeTypes-Secure/AzureDeploy.json
[parameters2]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-1804-3-NodeTypes-Secure/AzureDeploy.Parameters.json
