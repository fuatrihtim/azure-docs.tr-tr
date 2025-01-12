---
title: Klasik modelden Azure Resource Manager’a geçiş sırasında sık karşılaşılan hatalar
description: Bu makalede, IaaS kaynaklarının Azure Service Management 'tan Azure Resource Manager 'ye geçirilmesi sırasında en yaygın hatalar ve azaltmaları kataloglanır.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 6d803d1a66c069f5eb42deead453a8526577f76b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102615219"
---
# <a name="errors-that-commonly-occur-during-classic-to-azure-resource-manager-migration"></a>Klasik ' e Azure Resource Manager geçiş sırasında sık karşılaşılan hatalar

> [!IMPORTANT]
> Bugün, IaaS VM 'lerinin yaklaşık %90 ' u [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)kullanıyor. 28 Şubat 2020 itibariyle klasik VM 'Ler kullanımdan kaldırılmıştır ve 1 Mart 2023 tarihinde tamamen kullanımdan kaldırılacaktır. Bu kullanımdan kaldırma ve [nasıl etkilediği](classic-vm-deprecation.md#how-does-this-affect-me)hakkında [daha fazla bilgi edinin]( https://aka.ms/classicvmretirement) .

Bu makale, IaaS kaynakları Azure klasik dağıtım modelinden Azure Resource Manager yığınına geçirilirken en sık karşılaşılan hataları ve risk azaltma yollarını içerir.


## <a name="list-of-errors"></a>Hata listesi

| Hata dizesi | Risk azaltma |
| --- | --- |
| İç sunucu hatası |Bazı durumlarda bu tekrar denendiğinde geçen, geçici bir hatadır. Karşılaşmaya devam ederseniz platform günlüklerinin araştırılması gerekeceğinden [Azure desteğine başvurun](../azure-portal/supportability/how-to-create-azure-support-request.md). <br><br> **NOT:** Olayı destek ekibi tarafından izlenirken lütfen kendiniz sorun giderme işlemi yapmaya çalışmayın. Bu durum ortamınızda istenmeyen sonuçlara neden olabilir. |
| HostedService {barındırılan hizmet adı} içindeki {dağıtım adı} Dağıtımı bir PaaS dağıtımı (Web/Çalışan) olduğundan, dağıtımın geçirilmesi desteklenmiyor. |Bu, dağıtım web/çalışan rolü içerdiğinde gerçekleşir. Geçiş yalnızca sanal makineler için desteklendiğinden, lütfen web/çalışan rolünü dağıtımdan kaldırın ve geçiş işlemini yeniden deneyin. |
| Şablon {şablon-adı} dağıtımı başarısız oldu. CorrelationId={guid} |Geçiş hizmeti arka ucunda Azure Resource Manager yığınında kaynakları oluşturmak için Azure Resource Manager şablonları kullanıyoruz. Şablonları tekrar denenebilir yapıda olduğundan, bu hatayı gidermek için geçiş işlemi güvenli bir şekilde yeniden denenebilir. Hata devam ederse, lütfen [Azure desteğine başvurun](../azure-portal/supportability/how-to-create-azure-support-request.md) ve CorrelationId değerini verin. <br><br> **NOT:** Olayı destek ekibi tarafından izlenirken lütfen kendiniz sorun giderme işlemi yapmaya çalışmayın. Bu durum ortamınızda istenmeyen sonuçlara neden olabilir. |
| Sanal ağ {sanal-ağ-adı} yok. |Bu hata, sanal ağı yeni Azure portalında oluşturduysanız oluşabilir. Gerçek sanal ağ adı şu yapıdadır: "Grup * \<VNET name>" |
| HostedService {barındırılan-hizmet-adı} içindeki VM {vm-adı}, Azure Resource Manager'da desteklenmeyen bir Uzantı {uzantı-adı} içeriyor. Geçirme işlemine devam etmeden önce sanal makineden bunu kaldırmanız için önerilir. |BgInfo 1 gibi XML uzantıları. \* Azure Resource Manager desteklenmez. Bu nedenle bu uzantılar geçirilemez. Bu uzantılar sanal makinede yüklü bırakılırsa bunlar geçiş tamamlanmadan önce otomatik olarak kaldırılır. |
| HostedService {barındırılan-service-adı} içindeki VM {vm-adı}, VMSnapshot/VMSnapshotLinux Uzantısı içeriyor. Bu uzantının şu an için geçişi desteklenmemektedir. Uzantıyı sanal makineden kaldırın ve geçiş tamamlandıktan sonra Azure Resource Manager kullanarak tekrar ekleyin |Bu, sanal makinenin Azure Backup için yapılandırıldığı senaryodur. Bu şu anda desteklenmeyen bir senaryo olduğundan, lütfen aşağıdaki geçici çözümü okuyun: https://aka.ms/vmbackupmigration |
| HostedService {barındırılan-hizmet-adı} içindeki VM {vm-adı}, Durumu VM’den bildirilmeyen bir Uzantı {uzantı-adı} içeriyor. Bu nedenle bu VM geçirilemez. Uzantı durumunun bildirildiğinden emin olun veya uzantıyı VM'den kaldırın ve geçişi yeniden deneyin. <br><br> HostedService {barındırılan-hizmet-adı} içindeki VM {vm-adı}, İşleyici Durumu: {işleyici-durumu} bildiren bir Uzantı {uzantı-adı} içeriyor. Bu nedenle VM geçirilemiyor. Uzantı işleyici durumunun {handler-status} olduğundan emin olun veya uzantıyı VM’den kaldırıp geçişi tekrar deneyin. <br><br> HostedService {barındırılan-hizmet-adı} içindeki VM için VM Aracısı {vm-adı} genel aracı durumunun hazır olmadığı bildiriyor. Bu nedenle, geçirilebilir bir uzantısı varsa VM geçirilemeyebilir. VM Aracısının genel aracı durumunu hazır olarak bildirdiğinden emin olun. Adresine bakın https://aka.ms/classiciaasmigrationfaqs . |Azure konuk aracısı ve VM Uzantılarının, durumlarını bildirmek için VM depolama hesabına giden İnternet erişimine sahip olması gerekir. Durum hatasının yaygın nedenleri <li> Giden İnternet erişimini engelleyen bir Ağ Güvenlik Grubu <li> VNET 'in şirket içi DNS sunucuları varsa ve DNS bağlantısı kaybolursa <br><br> Desteklenmeyen durum görmeye devam ederseniz, bu denetimi atlamak ve geçişe devam etmek için uzantıları kaldırabilirsiniz. |
| HostedService {barındırılan hizmet adı} içindeki {dağıtım adı} Dağıtımı birden fazla Kullanılabilirlik Kümesine sahip olduğundan, dağıtımın geçirilmesi desteklenmiyor. |Şu anda yalnızca bir veya daha az kullanılabilirlik kümesine sahip barındırılan hizmetler geçirilebilir. Bu sorunu geçici olarak çözmek için, ek kullanılabilirlik kümelerini ve bu kullanılabilirlik kümelerindeki sanal makineleri farklı bir barındırılan hizmete taşıyın. |
| HostedService, içerdiği Kullanılabilirlik Kümesine ait olmayan VM'lere sahip olduğundan, HostedService {barındırılan-hizmet-adı} içindeki Dağıtım {dağıtım-adı} için geçiş desteklenmiyor. |Bu senaryo için geçici çözüm, tüm sanal makineleri tek bir kullanılabilirlik kümesine taşımak veya barındırılan hizmetteki kullanılabilirlik kümesinden tüm sanal makineleri kaldırmaktır. |
| Depolama hesabı/HostedService/Sanal Ağ {sanal ağ-adı} geçiş sürecinde ve bu nedenle değiştirilemiyor |Bu hata, kaynakta "Hazırlama" geçiş işlemi tamamlandığında ve kaynakta değişiklik yapacak bir işlem tetiklendiğinde olur. "Hazırlama" işleminden sonra yönetim düzeyindeki kilit nedeniyle, kaynak değişiklikleri engellenir. Yönetim düzeyi kilidini açmak için, "İşleme" geçiş işlemini çalıştırarak geçişi tamamlayabilir veya "Hazırlama" işlemi geri almak için "İptal" geçiş işlemini çalıştırabilirsiniz. |
| Durumu: RoleStateUnknown olan bir VM’e sahip olduğundan HostedService {barındırılan-hizmet-adı} için geçişe izin verilmiyor. Geçişe yalnızca VM şu durumlardan birinde olduğunda izin verilir: Çalışıyor, Durduruldu, Durduruldu Serbest. |VM, genellikle bir yeniden başlatma, Uzantı yükleme gibi HostedService 'te bir güncelleştirme işlemi sırasında gerçekleşen bir durum geçişi üzerinden gelebilir. Geçişe çalışmadan önce, güncelleştirme işleminin HostedService üzerinde tamamlanmasını öneririz. |
| HostedService {barındırılan-hizmet-adı} içindeki Dağıtım {dağıtım-adı}, fiziksel blob boyutu {veri-diskinin-bulunduğu-vhd-blobunun-boyutu} VM veri diski mantıksal boyutuyla {vm-api’sinde-belirtilen-veri-diskinin-boyutu} bayt eşleşmeyen bir Veri Diski {veri-diski-adı} olan bir VM {vm-adı} VM içeriyor. Geçiş, Azure Resource Manager VM için bir veri diski boyutu belirtilmeden devam edecek. | Bu hata, VHD blobunun boyutunu VM API modelinde güncelleştirmeden belirlediyseniz oluşur. Ayrıntılı sorun giderme adımları [aşağıdadır](#vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes).|
| Bulut Hizmetinde {Bulut-Hizmeti-Adı} VM’si {VM-adı} için medya bağlantısı [veri-diski-Uri’si} olan veri diski {veri-diski-adı} doğrulanırken bir depolama özel durumu oluştu. Lütfen VHD medya bağlantısının bu sanal makine için erişilebilir olduğundan emin olun | Bu hata, VM diskleri silinmiş veya erişilemez durumda olduğunda oluşabilir. Lütfen VM’nin disklerinin var olduğundan emin olun.|
| HostedService {barındırılan-hizmet-adı} içindeki VM {vm-adı}, blob adı {vhd-blob-adı} olan ve Azure Resource Manager'da desteklenmeyen bir MediaLink'li {vhd-uri’si} Disk içeriyor. | Blobun adı, İşlem Kaynak Sağlayıcısı tarafından şu anda desteklenmeyen "/" karakterini içeriyorsa bu hata oluşur. |
| Bölgesel kapsamda olmadığından HostedService {bulut-hizmeti-adı} içindeki Dağıtım {dağıtım-adı} için geçişe izin verilmiyor. \/Bu dağıtımı bölgesel kapsama taşımak için lütfen https:/aka.MS/regionalscope adresine bakın. | 2014'te Azure, ağ kaynaklarının küme düzeyi kapsamından bölgesel kapsama taşınacağını duyurdu. [https://aka.ms/regionalscope](https://aka.ms/regionalscope)Daha fazla ayrıntı için bkz.. Geçirilmekte olan dağıtımda otomatik olarak bölgesel kapsama taşınan bir güncelleştirme işlemi yapılmamış olduğunda bu hata oluşur. En iyi çözüm, bir VM 'ye bir uç nokta veya VM 'ye bir veri diski eklemektir ve sonra geçişi yeniden dener. <br> Bkz. [Azure 'da klasik bir sanal makinede uç noktaları ayarlama](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#create-an-endpoint) veya [klasik dağıtım modeliyle oluşturulan bir sanal makineye veri diski iliştirme](./linux/attach-disk-portal.md)|
| Ağ geçidi olmayan PaaS dağıtımları içerdiğinden, {VNET-Name} sanal ağı için geçiş desteklenmiyor. | Bu hata, sanal ağa bağlı Application Gateway veya API Management hizmetleri gibi ağ geçidi olmayan PaaS dağıtımlarınız olduğunda oluşur.|


## <a name="detailed-mitigations"></a>Ayrıntılı sorun giderme

### <a name="vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes"></a>Fiziksel blob boyutu baytları, VM Veri Diskinin mantıksal boyut baytlarıyla eşleşmeyen bir Veri Diskine sahip VM.

Bu, Veri diskinin mantıksal boyutu ile gerçek VHD blob boyutu eşitlenmediğinde görülür. Durum, Aşağıdaki komutlar kullanılarak kolayca doğrulanabilir:

#### <a name="verifying-the-issue"></a>Sorunu doğrulama

```powershell
# Store the VM details in the VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

# Display the data disk properties
# NOTE the data disk LogicalDiskSizeInGB below which is 11GB. Also note the MediaLink Uri of the VHD blob as we'll use this in the next step
$vm.VM.DataVirtualHardDisks


HostCaching         : None
DiskLabel           : 
DiskName            : coreosvm-coreosvm-0-201611230636240687
Lun                 : 0
LogicalDiskSizeInGB : 11
MediaLink           : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
SourceMediaLink     : 
IOType              : Standard
ExtensionData       : 

# Now get the properties of the blob backing the data disk above
# NOTE the size of the blob is about 15 GB which is different from LogicalDiskSizeInGB above
$blob = Get-AzStorageblob -Blob "coreosvm-dd1.vhd" -Container vhds 

$blob

ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudPageBlob
BlobType          : PageBlob
Length            : 16106127872
ContentType       : application/octet-stream
LastModified      : 11/23/2016 7:16:22 AM +00:00
SnapshotTime      : 
ContinuationToken : 
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext
Name              : coreosvm-dd1.vhd
```

#### <a name="mitigating-the-issue"></a>Sorunu giderme

```powershell
# Convert the blob size in bytes to GB into a variable which we'll use later
$newSize = [int]($blob.Length / 1GB)

# See the calculated size in GB
$newSize

15

# Store the disk name of the data disk as we'll use this to identify the disk to be updated
$diskName = $vm.VM.DataVirtualHardDisks[0].DiskName

# Identify the LUN of the data disk to remove
$lunToRemove = $vm.VM.DataVirtualHardDisks[0].Lun

# Now remove the data disk from the VM so that the disk isn't leased by the VM and it's size can be updated
Remove-AzureDataDisk -LUN $lunToRemove -VM $vm | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       213xx1-b44b-1v6n-23gg-591f2a13cd16   Succeeded  

# Verify we have the right disk that's going to be updated
Get-AzureDisk -DiskName $diskName

AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : 
Location             : East US
DiskSizeInGB         : 11
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 0c56a2b7-a325-123b-7043-74c27d5a61fd
OperationStatus      : Succeeded

# Now update the disk to the new size
Update-AzureDisk -DiskName $diskName -ResizedSizeInGB $newSize -Label $diskName

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureDisk     cv134b65-1b6n-8908-abuo-ce9e395ac3e7 Succeeded 

# Now verify that the "DiskSizeInGB" property of the disk matches the size of the blob 
Get-AzureDisk -DiskName $diskName


AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : coreosvm-coreosvm-0-201611230636240687
Location             : East US
DiskSizeInGB         : 15
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 1v53bde5-cv56-5621-9078-16b9c8a0bad2
OperationStatus      : Succeeded

# Now we'll add the disk back to the VM as a data disk. First we need to get an updated VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

Add-AzureDataDisk -Import -DiskName $diskName -LUN 0 -VM $vm -HostCaching ReadWrite | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       b0ad3d4c-4v68-45vb-xxc1-134fd010d0f8 Succeeded      
```

### <a name="moving-a-vm-to-a-different-subscription-after-completing-migration"></a>Geçişi tamamlandıktan sonra bir VM’yi farklı bir aboneliğe taşıma

Geçiş işlemini tamamladıktan sonra VM’yi başka bir aboneliğe taşımak isteyebilirsiniz. Ancak, VM’de bir Key Vault kaynağına başvuran bir gizli diziniz/sertifikanız varsa, taşıma şu an için desteklenmez. Aşağıdaki yönergeler soruna geçici bir çözüm yapmanıza olanak sağlayacak. 

#### <a name="powershell"></a>PowerShell

```powershell
$vm = Get-AzVM -ResourceGroupName "MyRG" -Name "MyVM"
Remove-AzVMSecret -VM $vm
Update-AzVM -ResourceGroupName "MyRG" -VM $vm
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm update -g "myrg" -n "myvm" --set osProfile.Secrets=[]
```


## <a name="next-steps"></a>Sonraki adımlar

* [IaaS kaynaklarının klasik ile Azure Resource Manager geçişine genel bakış](migration-classic-resource-manager-overview.md)
* [Klasik modelden Azure Resource Manager’a platform destekli geçişe ayrıntılı teknik bakış](migration-classic-resource-manager-deep-dive.md)
* [IaaS kaynaklarının Klasik’ten Azure Resource Manager’a geçişini planlama](migration-classic-resource-manager-plan.md)
* [IaaS kaynaklarını klasik 'ten Azure Resource Manager geçirmek için PowerShell 'i kullanma](migration-classic-resource-manager-ps.md)
* [IaaS kaynaklarını klasik 'ten Azure Resource Manager geçirmek için CLı kullanma](migration-classic-resource-manager-cli.md)
* [IaaS kaynaklarının klasik 'dan Azure Resource Manager geçişine yardımcı olacak topluluk araçları](migration-classic-resource-manager-community-tools.md)
* [IaaS kaynaklarını klasik konumundan Azure Resource Manager geçirme hakkında en sık sorulan soruları gözden geçirin](migration-classic-resource-manager-faq.md)