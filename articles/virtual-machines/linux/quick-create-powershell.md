---
title: Hızlı başlangıç-Azure PowerShell bir Linux sanal makinesi oluşturma
description: Bu hızlı başlangıçta Azure PowerShell’i kullanarak Linux sanal makinesi oluşturmayı öğrenirsiniz
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 07/31/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 2cef611fe79ca04303840076b09b4cf6344b7e7d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102616239"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-azure-with-powershell"></a>Hızlı Başlangıç: PowerShell ile Azure'da Linux sanal makinesi oluşturma

Azure PowerShell modülü, PowerShell komut satırından veya betik içinden Azure kaynakları oluşturmak ve yönetmek için kullanılır. Bu hızlı başlangıçta Azure PowerShell modülünü kullanarak Azure’da bir Linux sanal makinesinin (VM) nasıl dağıtılacağı gösterilir. Bu hızlı başlangıçta Ubuntu 18,04 LTS Market görüntüsü kurallı olarak kullanılmaktadır. Ayrıca VM'nizin çalıştığını görmek için SSH ile VM bağlantısı kurup NGINX web sunucusunu da yükleyeceksiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell’i başlatma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. 

Cloud Shell'i açmak için kod bloğunun sağ üst köşesinden **Deneyin**'i seçmeniz yeterlidir. **Kopyala**’yı seçerek kod bloğunu kopyalayın, Cloud Shell’e yapıştırın ve Enter tuşuna basarak çalıştırın.

## <a name="create-ssh-key-pair"></a>SSH anahtar çifti oluşturma

SSH anahtar çifti oluşturmak için [SSH-keygen](https://www.ssh.com/ssh/keygen/) kullanın. Bir SSH anahtar çiftiniz varsa bu adımı atlayabilirsiniz.


```azurepowershell-interactive
ssh-keygen -t rsa -b 4096
```

Anahtar çifti için bir dosya adı sağlamanız istenir veya varsayılan konumunu kullanmak için **ENTER** tuşuna basabilirsiniz `/home/<username>/.ssh/id_rsa` . İsterseniz anahtarlar için bir parola da oluşturabilirsiniz.

SSH anahtar çiftleri oluşturma hakkında daha ayrıntılı bilgi için bkz. [Windows Ile SSH anahtarlarını kullanma](ssh-from-windows.md).

Cloud Shell kullanarak SSH anahtar çiftini oluşturursanız, [Cloud Shell tarafından otomatik olarak oluşturulan bir depolama hesabında](../../cloud-shell/persisting-shell-storage.md)depolanır. Anahtarlarınızı aldıktan veya VM 'ye erişiminizi kaybedecelene kadar depolama hesabını ya da bu dosyada paylaşılan dosyaları silmeyin. 

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)Ile bir Azure Kaynak grubu oluşturun. Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır:

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-virtual-network-resources"></a>Sanal ağ kaynakları oluşturma

Bir sanal ağ, alt ağ ve genel IP adresi oluşturun. Bu kaynaklar, VM'ye ağ bağlantısı sağlamak ve VM'yi İnternet'e bağlamak için kullanılır:

```azurepowershell-interactive
# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -Name "myVNET" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```

Azure Ağ Güvenlik Grubu ve trafik kuralı oluşturun. Ağ güvenlik Grubu, gelen ve giden kurallarını kullanarak VM'nin güvenliğini sağlar. Aşağıdaki örnekte, TCP bağlantı noktası 22 için SSH bağlantılarına izin veren bir gelen kuralı oluşturulur. Gelen web trafiğine izin vermek amacıyla, TCP bağlantı noktası 80 için de bir gelen kuralı oluşturulur.

```azurepowershell-interactive
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleSSH"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 22 `
  -Access "Allow"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleWWW"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access "Allow"

# Create a network security group
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -Name "myNetworkSecurityGroup" `
  -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

[New-Aznetworkınterface](/powershell/module/az.network/new-aznetworkinterface)ile bir sanal ağ arabirim kartı (NIC) oluşturun. Sanal NIC, VM'yi bir alt ağa, Ağ Güvenlik Grubu'na ve genel IP adresine bağlar.

```azurepowershell-interactive
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzNetworkInterface `
  -Name "myNic" `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

PowerShell’de bir VM oluşturmak için kullanılacak görüntü, boyut ve kimlik doğrulaması seçenekleri gibi ayarların olduğu bir yapılandırma oluşturursunuz. Ardından bu yapılandırma VM’yi derlemek için kullanılır.

SSH kimlik bilgilerini, işletim sistemi bilgilerini ve VM boyutunu tanımlayın. Bu örnekte SSH anahtarı `~/.ssh/id_rsa.pub`‘da depolanmaktadır. 

```azurepowershell-interactive
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig `
  -VMName "myVM" `
  -VMSize "Standard_D1" | `
Set-AzVMOperatingSystem `
  -Linux `
  -ComputerName "myVM" `
  -Credential $cred `
  -DisablePasswordAuthentication | `
Set-AzVMSourceImage `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "18.04-LTS" `
  -Version "latest" | `
Add-AzVMNetworkInterface `
  -Id $nic.Id

# Configure the SSH key
$sshPublicKey = cat ~/.ssh/id_rsa.pub
Add-AzVMSshPublicKey `
  -VM $vmconfig `
  -KeyData $sshPublicKey `
  -Path "/home/azureuser/.ssh/authorized_keys"
```

Şimdi [New-AzVM](/powershell/module/az.compute/new-azvm)ile oluşturmak için önceki yapılandırma tanımlarını birleştirin:

```azurepowershell-interactive
New-AzVM `
  -ResourceGroupName "myResourceGroup" `
  -Location eastus -VM $vmConfig
```

VM'nizin dağıtılması birkaç dakika sürer. Dağıtım tamamlandıktan sonra bir sonraki bölüme geçin.

## <a name="connect-to-the-vm"></a>VM’ye bağlanma

Genel IP adresini kullanarak VM ile bir SSH bağlantısı oluşturun. VM 'nin genel IP adresini görmek için [Get-Azpublicıpaddress](/powershell/module/az.network/get-azpublicipaddress) cmdlet 'ini kullanın:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

SSH anahtar çiftini oluşturmak için kullandığınız kabuğu kullanarak, SSH oturumu oluşturmak için aşağıdaki komutu kabuğa yapıştırın. *10.111.12.123* değerini sanal makinenizin IP adresiyle değiştirin.

```bash
ssh azureuser@10.111.12.123
```

Sorulduğunda, kullanıcı adı *azureuser* olarak girilmelidir. SSH anahtarlarınızla birlikte bir parola kullanılıyorsa, istendiğinde bu parolayı girmelisiniz.


## <a name="install-nginx"></a>NGINX yükleme

Sanal makinenizin çalıştığını görmek için NGINX web sunucusunu yükleyin. SSH oturumunuzdan paket kaynaklarınızı güncelleştirip en son NGINX paketini yükleyin.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

İşlemi tamamladığınızda `exit` yazarak SSH oturumunu kapatın.


## <a name="view-the-web-server-in-action"></a>Web sunucusunun çalıştığını görme

İstediğiniz web tarayıcısını kullanarak varsayılan NGINX karşılama sayfasını görüntüleyin. VM'nin genel IP adresini web adresi olarak girin. Genel IP adresini VM genel bakış sayfasında veya önceden kullandığınız SSH bağlantı dizesinde bulabilirsiniz.

![NGıNX varsayılan karşılama sayfası](./media/quick-create-cli/nginix-welcome-page.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet 'ini kullanarak kaynak grubunu, VM 'yi ve tüm ilgili kaynakları kaldırabilirsiniz:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta basit bir sanal makine dağıttınız, bir Ağ Güvenlik Grubu ve kuralı oluşturdunuz ve temel bir web sunucusu yüklediniz. Azure sanal makineleri hakkında daha fazla bilgi için Linux VM’lerine yönelik öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure Linux sanal makine öğreticileri](./tutorial-manage-vm.md)
