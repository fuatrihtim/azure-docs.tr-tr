---
title: Azure CLı kullanarak hızlandırılmış ağ ile Azure VM oluşturma
description: Hızlandırılmış ağ etkin bir Linux sanal makinesi oluşturmayı öğrenin.
services: virtual-network
documentationcenter: na
author: gsilva5
manager: gedegrac
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: gsilva
ms.custom: ''
ms.openlocfilehash: 643a52c9be04fb325b8e1d088faeb68e473aa673
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919961"
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking-using-azure-cli"></a>Azure CLI kullanarak Hızlandırılmış Ağ ile Linux sanal makinesi oluşturma

Bu öğreticide, hızlandırılmış ağ ile bir Linux sanal makinesi (VM) oluşturmayı öğreneceksiniz. Hızlandırılmış ağ ile Windows VM oluşturmak için bkz. [hızlandırılmış ağ Ile WINDOWS VM oluşturma](create-vm-accelerated-networking-powershell.md). Hızlandırılmış ağ, bir VM 'ye tek köklü g/ç Sanallaştırması (SR-ıOV) sağlar ve ağ performansını büyük ölçüde geliştirir. Bu yüksek performanslı yol, desteklenen VM türlerinde en zorlu ağ iş yükleri ile kullanım için, gecikme süresi, değişim ve CPU kullanımını azaltan ana bilgisayarı veri yolundan atlar. Aşağıdaki resimde, hızlandırılmış ağ ile ve olmadan iki VM arasındaki iletişim gösterilmektedir:

![Karşılaştırma](./media/create-vm-accelerated-networking/accelerated-networking.png)

Hızlandırılmış ağ olmadan, VM 'deki ve olmayan tüm ağ trafiği ana bilgisayar ve sanal anahtar arasında gezinmelidir. Sanal anahtar ağ güvenlik grupları, erişim denetim listeleri, yalıtım ve ağ trafiğine diğer ağ sanallaştırılmış hizmetler gibi tüm ilke zorlamasına olanak sağlar. Sanal anahtarlar hakkında daha fazla bilgi edinmek için [Hyper-V ağ sanallaştırma ve sanal anahtar](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134230(v=ws.11)) makalesini okuyun.

Hızlandırılmış ağ ile ağ trafiği, sanal makinenin ağ arabirimine (NIC) ulaşır ve ardından VM 'ye iletilir. Sanal anahtarın geçerli olduğu tüm ağ ilkeleri artık boşaltılmış ve donanımda uygulandı. İlkeyi donanıma uygulamak, NIC 'nin ağ trafiğini konak ve sanal anahtarı atlayarak, ana bilgisayara uyguladığı tüm ilkeyi koruyarak doğrudan VM 'ye iletmesini sağlar.

Hızlandırılmış ağ avantajları yalnızca üzerinde etkin olduğu VM için geçerlidir. En iyi sonuçlar için, bu özelliğin aynı Azure sanal ağına (VNet) bağlı en az iki VM üzerinde etkinleştirilmesi idealdir. VNET 'lerde iletişim kurarken veya şirket içinde bağlantı kurarken, bu özelliğin genel gecikme süresine en az etkisi vardır.

## <a name="benefits"></a>Avantajlar
* **Saniye başına düşük gecikme süresi/daha yüksek paketler (PPS):** Sanal anahtarın veri yolundan kaldırılması, ilke işleme için konakta harcadıkları zaman paketlerini kaldırır ve sanal makıne içinde işlenebilecek paketlerin sayısını artırır.
* **Azaltılan değişim:** Sanal anahtar işleme, uygulanması gereken ilke miktarına ve işleme yapan CPU 'nun iş yüküne bağlıdır. İlke zorlamayı donanıma devreetmek, paketleri doğrudan VM 'ye teslim ederek bu değişkenliği, Konağı VM iletişimine ve tüm yazılım kesintileri ve bağlam anahtarlarına kaldırarak bu değişkenliği kaldırır.
* **AZALTıLMıŞ CPU kullanımı:** Konaktaki sanal anahtarı atlamak, ağ trafiğini işlemeye yönelik daha az CPU kullanımına neden oluyor.

## <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri
Aşağıdaki dağıtımlar Azure galerisindeki kutudan çıkar: 
* **Linux-Azure çekirdekle Ubuntu 14,04**
* **Ubuntu 16,04 veya üzeri** 
* **SLES12 SP3 veya üzeri** 
* **RHEL 7,4 veya üzeri**
* **CentOS 7,4 veya üzeri**
* **CoreOS Linux**
* **Geribağlantı noktaları çekirdeği, "Buster" veya üzeri ile "uzat"**
* **Red Hat uyumlu çekirdek ile Oracle Linux 7,4 ve üzeri (RHCK)**
* **UEK sürüm 5 ile Oracle Linux 7,5 ve üzeri**
* **FreeBSD 10,4, 11,1 & 12,0 veya üzeri**

## <a name="limitations-and-constraints"></a>Sınırlamalar ve kısıtlamalar

### <a name="supported-vm-instances"></a>Desteklenen VM örnekleri
Hızlandırılmış ağ, 2 veya daha fazla vCPU ile en genel amaçlı ve işlem için iyileştirilmiş örnek boyutlarında desteklenir. Hiper iş parçacığı destekleyen örneklerde, hızlandırılmış ağ, 4 veya daha fazla vCPU içeren VM örneklerinde desteklenir. 

Hızlandırılmış ağ desteği ayrı [sanal makine boyutları](../virtual-machines/sizes.md) belgelerinde bulunabilir. 

### <a name="custom-images"></a>Özel Görüntüler
Özel bir görüntü kullanıyorsanız ve görüntünüz hızlandırılmış ağı destekliyorsa, lütfen Azure 'da Mellanox ConnectX-3 ve ConnectX-4 LX NIC 'lerle çalışmak için gerekli sürücülere sahip olduğunuzdan emin olun.

### <a name="regions"></a>Bölgeler
Tüm genel Azure bölgelerinde ve Azure Kamu bulutlarında kullanılabilir.

<!-- ### Network interface creation 
Accelerated networking can only be enabled for a new NIC. It cannot be enabled for an existing NIC.
removed per issue https://github.com/MicrosoftDocs/azure-docs/issues/9772 -->
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Çalışan bir VM 'de hızlandırılmış ağı etkinleştirme
Hızlandırılmış ağ etkin olmayan desteklenen bir VM boyutu, yalnızca durdurulduğunda ve serbest bırakıldığında özelliği etkin hale getirebilirsiniz.  
### <a name="deployment-through-azure-resource-manager"></a>Azure Resource Manager üzerinden dağıtım
Sanal makineler (klasik) hızlandırılmış ağlarla dağıtılamaz.

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>Azure hızlandırılmış ağ ile Linux VM oluşturma
## <a name="portal-creation"></a>Portal oluşturma
Bu makalede, Azure CLı kullanarak hızlandırılmış ağ içeren bir sanal makine oluşturma adımları sunulmaktadır, ancak [Azure Portal kullanarak hızlandırılmış ağ içeren bir sanal makine oluşturabilirsiniz](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Portalda bir sanal makine oluştururken, **sanal makine oluştur** dikey penceresinde **ağ** sekmesini seçin.  Bu sekmede, **hızlandırılmış ağ** için bir seçenek vardır.  [Desteklenen bir işletim sistemi](#supported-operating-systems) ve [VM boyutu](#supported-vm-instances)seçtiyseniz, bu seçenek otomatik olarak "açık" olarak doldurulur.  Aksi takdirde, hızlandırılmış ağ için "kapalı" seçeneğini doldurur ve kullanıcıya neden etkinleştirilmemiş bir neden olur.   

* *Note:* Portal aracılığıyla yalnızca desteklenen işletim sistemleri etkinleştirilebilir.  Özel bir görüntü kullanıyorsanız ve görüntünüz hızlandırılmış ağı destekliyorsa, lütfen CLı veya PowerShell kullanarak VM 'nizi oluşturun. 

Sanal makine oluşturulduktan sonra, [hızlandırılmış ağ oluşturma özelliğinin etkinleştirildiğini onaylama](#confirm-that-accelerated-networking-is-enabled)' daki yönergeleri Izleyerek hızlandırılmış ağın etkinleştirildiğini doğrulayabilirsiniz.

## <a name="cli-creation"></a>CLı oluşturma
### <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

En son [Azure CLI](/cli/azure/install-azure-cli) 'yı yükleyip [az Login](/cli/azure/reference-index)kullanarak bir Azure hesabında oturum açın. Aşağıdaki örneklerde, örnek parametre adlarını kendi değerlerinizle değiştirin. *Myresourcegroup*, *MYNIC* ve *myvm* dahil olmak üzere örnek parametre adları.

[az group create](/cli/azure/group) ile bir kaynak grubu oluşturun. Aşağıdaki örnek, *merkezileştirme* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturur:

```azurecli
az group create --name myResourceGroup --location centralus
```

[Linux hızlandırılmış ağlarda](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview)listelenen desteklenen bir Linux bölgesi seçin.

[az network vnet create](/cli/azure/network/vnet) komutu ile bir sanal ağ oluşturun. Aşağıdaki örnek, bir alt ağ ile *Myvnet* adlı bir sanal ağ oluşturur:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

### <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma
[Az Network NSG Create](/cli/azure/network/nsg)komutuyla bir ağ güvenlik grubu oluşturun. Aşağıdaki örnek *myNetworkSecurityGroup* adında bir ağ güvenlik grubu oluşturur:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Ağ güvenlik grubu, biri Internet 'ten gelen tüm erişimi devre dışı bırakan birkaç varsayılan kural içerir. [Az Network NSG Rule Create](/cli/azure/network/nsg/rule)komutuyla sanal makineye SSH erişimine izin vermek için bir bağlantı noktası açın:

```azurecli
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup \
  --name Allow-SSH-Internet \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 22
```

### <a name="create-a-network-interface-with-accelerated-networking"></a>Hızlandırılmış ağ ile bir ağ arabirimi oluşturma

[az network public-ip create](/cli/azure/network/public-ip) komutu ile bir genel IP adresi oluşturun. Sanal makineye Internet 'ten erişmeyi planlamıyorsanız genel IP adresi gerekli değildir, ancak bu makaledeki adımları tamamlayabilmeniz için gereklidir.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

[Az Network Nic Create](/cli/azure/network/nic) ile hızlandırılmış ağ etkinken bir ağ arabirimi oluşturun. Aşağıdaki örnek, *Myvnet* sanal ağının *mysubnet* alt ağında *MYNIC* adlı bir ağ arabirimi oluşturur ve *mynetworksecuritygroup* ağ güvenlik grubunu ağ arabirimiyle ilişkilendirir:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --accelerated-networking true \
    --public-ip-address myPublicIp \
    --network-security-group myNetworkSecurityGroup
```

### <a name="create-a-vm-and-attach-the-nic"></a>VM oluşturma ve NIC 'yi iliştirme
VM oluştururken, ile oluşturduğunuz NIC 'i belirtin `--nics` . [Linux hızlandırılmış ağlarda](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview)listelenen bir boyut ve dağıtım seçin. 

[az vm create](/cli/azure/vm) ile bir VM oluşturun. Aşağıdaki örnek, UbuntuLTS görüntüsü ile *Myvm* ADLı bir VM ve hızlandırılmış ağı destekleyen bir boyut oluşturur (*Standard_DS4_v2*):

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS4_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic
```

Tüm VM boyutlarının ve özelliklerinin bir listesi için bkz. [LINUX VM boyutları](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

VM oluşturulduktan sonra aşağıdaki örnek çıktıya benzer bir çıktı döndürülür. **publicIpAddress** değerini not alın. Bu adres, sonraki adımlarda sanal makineye erişmek için kullanılır.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "centralus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "192.168.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

### <a name="confirm-that-accelerated-networking-is-enabled"></a>Hızlandırılmış ağ özelliğinin etkin olduğunu onaylayın

Sanal makine ile bir SSH oturumu oluşturmak için aşağıdaki komutu kullanın. `<your-public-ip-address>`Oluşturduğunuz sanal makineye atanan genel IP adresiyle değiştirin ve VM oluştururken farklı bir değer kullandıysanız *azureuser* değiştirin `--admin-username` .

```bash
ssh azureuser@<your-public-ip-address>
```

Bash kabuğundan, `uname -r` çekirdek sürümünün aşağıdaki sürümlerden biri veya daha fazlası olduğunu girin ve onaylayın:

* **Ubuntu 16,04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Mellanox VF cihazının VM 'ye komutuyla açık olduğunu doğrulayın `lspci` . Döndürülen çıkış aşağıdaki çıktıya benzer:

```output
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Komutuyla VF (sanal işlev) üzerindeki etkinliği denetleyin `ethtool -S eth0 | grep vf_` . Aşağıdaki örnek çıktıya benzer bir çıktı alırsanız, hızlandırılmış ağ etkinleştirilir ve çalışır.

```output
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
Hızlandırılmış ağ, VM 'niz için artık etkinleştirilmiştir.

## <a name="handle-dynamic-binding-and-revocation-of-virtual-function"></a>Sanal işlevin dinamik bağlamasını ve iptalini işle 
Uygulamalar VM 'de kullanıma sunulan yapay NIC 'in üzerinde çalışmalıdır. Uygulama doğrudan VF NIC üzerinden çalışıyorsa, bazı paketler yapay arabirim üzerinde gösterildiğinden, VM 'ye giden **Tüm** paketleri almaz.
Yapay NIC üzerinden bir uygulama çalıştırırsanız, uygulamanın kendisine gidecek **Tüm** paketleri almasını güvence altına alır. Ayrıca, ana bilgisayara bakım yapıldığında VF iptal edilse bile uygulamanın çalışmaya devam etmelerini sağlar. Yapay NIC 'e bağlayan uygulamalar, **hızlandırılmış ağ** özelliğinden yararlanan tüm uygulamaların **zorunlu** bir gereksinimidir.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Mevcut VM 'lerde hızlandırılmış ağı etkinleştir
Hızlandırılmış ağ olmadan bir VM oluşturduysanız, bu özelliği var olan bir VM 'de etkinleştirmek mümkündür.  Yukarıda da özetlenen aşağıdaki önkoşulları izleyerek VM 'nin hızlandırılmış ağı desteklemesi gerekir:

* Sanal makine hızlandırılmış ağ için desteklenen bir boyut olmalıdır
* VM, desteklenen bir Azure Galeri görüntüsü (ve Linux için çekirdek sürümü) olmalıdır
* Herhangi bir NIC üzerinde hızlandırılmış ağ etkinleştirilmeden önce bir kullanılabilirlik kümesindeki veya VMSS 'deki tüm sanal makinelerin durdurulması/serbest bırakılmasının gerekir

### <a name="individual-vms--vms-in-an-availability-set"></a>Bir kullanılabilirlik kümesindeki VM 'Ler & bireysel VM 'Ler
İlk olarak VM 'yi durdurun/serbest bırakın veya bir kullanılabilirlik kümesi ise, kümesindeki tüm VM 'Ler:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

Önemli bir deyişle, VM 'niz tek tek oluşturulduysa, bir kullanılabilirlik kümesi olmadan, yalnızca hızlandırılmış ağı etkinleştirmek için tek bir VM 'yi durdurmanız/serbest getirmeniz gerektiğini lütfen unutmayın.  VM 'niz bir kullanılabilirlik kümesi ile oluşturulduysa, NIC 'lerden hiçbirinde hızlandırılmış ağı etkinleştirmeden önce kullanılabilirlik kümesinde bulunan tüm VM 'Lerin durdurulması/serbest bırakılmasının gerekir. 

Durdurulduktan sonra, sanal makinenizin NIC 'inde hızlandırılmış ağı etkinleştirin:

```azurecli
az network nic update \
    --name myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

SANAL makinelerinizi yeniden başlatın veya bir kullanılabilirlik kümesinde, küme içindeki tüm VM 'Leri ve hızlandırılmış ağ 'ın etkin olduğunu onaylayın: 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>VMSS
VMSS biraz farklıdır, ancak aynı iş akışına uyar.  İlk olarak, VM 'Leri durdurun:

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

VM 'Ler durdurulduğunda, ağ arabirimi altındaki hızlandırılmış ağ özelliğini güncelleştirin:

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

Lütfen bir VMSS 'nin üç farklı ayar, otomatik, sıralı ve el ile güncelleştirme uygulayan VM yükseltmeleri olduğunu unutmayın.  Bu yönergelerde, VMSS 'nin değişiklikleri yeniden başlattıktan hemen sonra alması için ilke otomatik olarak ayarlanır.  Değişikliklerin hemen çekilmesi için otomatik olarak ayarlamak için: 

```azurecli
az vmss update \
    --name myvmss \
    --resource-group myrg \
    --set upgradePolicy.mode="automatic"
```

Son olarak, VMSS 'yi yeniden başlatın:

```azurecli
az vmss start \
    --name myvmss \
    --resource-group myrg
```

Yeniden başlattıktan sonra, yükseltmelerin bitmesini bekleyin, ancak tamamlandığında, sanal makine içinde VF görüntülenir.  (Lütfen desteklenen bir işletim sistemi ve VM boyutu kullandığınızdan emin olun.)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Hızlandırılmış ağ ile mevcut VM 'Leri yeniden boyutlandırma

Hızlandırılmış ağ etkin olan sanal makineler yalnızca hızlandırılmış ağı destekleyen VM 'lere yeniden boyutlandırılabilir.  

Hızlandırılmış ağ özellikli bir VM, yeniden boyutlandırma işlemi kullanılarak hızlandırılmış ağı desteklemeyen bir VM örneğine yeniden boyutlandırılamaz.  Bunun yerine, bu VM 'lerden birini yeniden boyutlandırmak için: 

* VM 'yi durdurma/serbest bırakma veya bir kullanılabilirlik kümesinde/VMSS 'de, set/VMSS içindeki tüm VM 'Leri durdur/serbest bırak.
* Hızlandırılmış ağ, VM 'nin NIC 'inde veya bir kullanılabilirlik kümesinde/VMSS 'de, küme/VMSS 'de bulunan tüm VM 'lerde devre dışı bırakılmalıdır.
* Hızlandırılmış ağ devre dışı bırakıldığında, VM/kullanılabilirlik kümesi/VMSS, hızlandırılmış ağı desteklemeyen ve yeniden başlatılan yeni bir boyuta taşınabilir.