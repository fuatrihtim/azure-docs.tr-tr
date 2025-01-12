---
title: Azure 'da bir Linux VM 'sine Kullanıcı eklemek için Cloud-init kullanma
description: Azure CLı ile oluşturma sırasında bir Linux VM 'sine Kullanıcı eklemek için Cloud-init kullanma
author: rickstercdn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 2c459965f2eb29a469ac90fdeb42107d1dbcf86a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559418"
---
# <a name="use-cloud-init-to-add-a-user-to-a-linux-vm-in-azure"></a>Azure 'da bir Linux VM 'sine Kullanıcı eklemek için Cloud-init kullanma
Bu makalede, Azure 'da sağlama zamanında bir sanal makineye (VM) veya sanal makine ölçek kümelerine (VMSS) Kullanıcı eklemek için [Cloud-init](https://cloudinit.readthedocs.io) ' i nasıl kullanacağınız gösterilmektedir. Bu Cloud-init betiği, kaynaklar Azure tarafından sağlandıktan sonra ilk önyüklemede çalışır. Cloud-init 'in Azure 'da ve desteklenen Linux korumalar 'daki yerel olarak nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Cloud-init Overview](using-cloud-init.md).

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Cloud-init ile bir VM 'ye Kullanıcı ekleme
Yeni bir Linux VM 'deki ilk görevlerden biri, *kök* kullanımını önlemek için kendinize ek bir kullanıcı eklemektir. SSH anahtarları, güvenlik ve kullanılabilirlik için en iyi uygulamadır. Bu Cloud-init betiğine sahip *~/. SSH/authorized_keys* dosyasına anahtarlar eklenir.

Bir Linux VM 'ye Kullanıcı eklemek için, geçerli kabuğunuzun *cloud_init_add_user.txt* adlı bir dosya oluşturun ve aşağıdaki yapılandırmayı yapıştırın. Bu örnekte, dosyayı yerel makinenizde değil Cloud Shell oluşturun. İstediğiniz düzenleyiciyi kullanabilirsiniz. Dosyayı oluşturmak ve kullanılabilir düzenleyicilerin listesini görmek için `sensible-editor cloud_init_add_user.txt` adını girin. **Nano** düzenleyiciyi kullanmak için #1 seçin. Tüm Cloud-init dosyalarının, özellikle de ilk satırda doğru şekilde kopyalandığından emin olun.  Örnek için kendi ortak anahtarınızı ( *~/. ssh/id_rsa. pub* içeriği gibi) sağlamanız gerekir `ssh-authorized-keys:` . Bu, örneği basitleştirmek için burada kısaltıldı.

```yaml
#cloud-config
users:
  - default
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```
> [!NOTE] 
> #Cloud-config dosyası, `- default` dahil edilen parametreyi içerir. Bu işlem, kullanıcıyı sağlama sırasında oluşturulan mevcut yönetici kullanıcısına ekler. Parametresi olmadan bir kullanıcı oluşturursanız, `- default` Azure platformu tarafından oluşturulan otomatik olarak oluşturulan Yönetici Kullanıcı üzerine yazılır. 

Bu görüntüyü dağıtılmadan önce [az Group Create](/cli/azure/group) komutuyla bir kaynak grubu oluşturmanız gerekir. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Aşağıdaki örnek *eastus* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Şimdi [az VM Create](/cli/azure/vm) Ile bir VM oluşturun ve Cloud-init dosyasını `--custom-data cloud_init_add_user.txt` aşağıdaki gibi belirtin:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_add_user.txt \
  --generate-ssh-keys 
```

Önceki komutun çıktısında gösterilen sanal makinenizin genel IP adresine SSH. Kendi **Publicıpaddress** değerini aşağıdaki şekilde girin:

```bash
ssh <publicIpAddress>
```

Kullanıcının VM 'ye ve belirtilen gruplara eklendiğini doğrulamak için, */etc/group* dosyasının içeriğini aşağıdaki gibi görüntüleyin:

```bash
cat /etc/group
```

Aşağıdaki örnek çıktı, *cloud_init_add_user.txt* DOSYASıNDAKI kullanıcının VM 'ye ve uygun gruba eklendiğini gösterir:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Sonraki adımlar
Yapılandırma değişikliklerine yönelik ek Cloud-init örnekleri için aşağıdakilere bakın:
 
- [VM 'ye ek bir Linux kullanıcısı ekleme](cloudinit-add-user.md)
- [İlk önyüklemede var olan paketleri güncelleştirmek için bir paket yöneticisi çalıştırın](cloudinit-update-vm.md)
- [VM yerel ana bilgisayar adını değiştir](cloudinit-update-vm-hostname.md) 
- [Uygulama paketi yüklemesi, yapılandırma dosyalarını güncelleştirme ve anahtarları ekleme](tutorial-automate-vm-deployment.md)
