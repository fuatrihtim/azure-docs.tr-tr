---
title: Eklenmemiş yönetilen ve yönetilmeyen Azure disklerini bulma ve silme
description: Azure PowerShell kullanarak eklenmemiş Azure tarafından yönetilen ve yönetilmeyen (VHD/sayfa Blobları) disklerini bulma ve silme.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 66a54ea74fcc6d8d354f5adbffe214c34b4c20d5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554387"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Eklenmemiş yönetilen ve yönetilmeyen Azure disklerini bulma ve silme

Azure 'da bir sanal makineyi (VM) sildiğinizde, varsayılan olarak, VM 'ye bağlı olan tüm diskler silinmez. Bu özellik, VM 'lerin istenmeden silinmesinden dolayı veri kaybını önlemeye yardımcı olur. Bir VM silindikten sonra, eklenmemiş diskler için ödeme yapmaya devam edersiniz. Bu makalede, eklenmemiş disklerin nasıl bulunacağını ve silineceğini ve gereksiz maliyetlerin nasıl azaltalacağı gösterilmektedir.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Yönetilen diskler: eklenmemiş diskleri bulma ve silme

Aşağıdaki betik, **ManagedBy** özelliğinin değerini inceleyerek eklenmemiş [yönetilen diskleri](../managed-disks-overview.md) arar. Yönetilen bir disk bir sanal makineye eklendiğinde, **ManagedBy** özelliği sanal makınenın kaynak kimliğini içerir. Yönetilen bir disk eklendiği zaman, **ManagedBy** özelliği null olur. Betik, bir Azure aboneliğindeki tüm yönetilen diskleri inceler. Betik, **ManagedBy** özelliği null olarak ayarlanmış bir yönetilen disk bulduktan sonra, komut dosyası diskin eklenmemiş olduğunu belirler.

>[!IMPORTANT]
>İlk olarak, **Deleteunattacheddisks** değişkenini 0 olarak ayarlayarak betiği çalıştırın. Bu eylem, tüm ekli yönetilen diskleri bulmanıza ve görüntülemenize olanak sağlar.
>
>Eklenmemiş tüm diskleri gözden geçirdikten sonra, betiği yeniden çalıştırın ve **Deleteunattacheddisks** değişkenini 1 olarak ayarlayın. Bu eylem, eklenmemiş tüm yönetilen diskleri silmenize olanak sağlar.

```azurepowershell-interactive
# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0
$managedDisks = Get-AzDisk
foreach ($md in $managedDisks) {
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){
        if($deleteUnattachedDisks -eq 1){
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"
            $md | Remove-AzDisk -Force
            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "
        }else{
            $md.Id
        }
    }
 }
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Yönetilmeyen diskler: eklenmemiş diskleri bulma ve silme

Yönetilmeyen diskler, [Azure depolama hesaplarında](../../storage/common/storage-account-overview.md) [sayfa BLOBLARı](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) olarak depolanan VHD dosyalarıdır. Aşağıdaki betik, **Leasestatus** özelliğinin değerini inceleyerek eklenmemiş yönetilmeyen diskleri (sayfa Blobları) arar. Bir sanal makineye yönetilmeyen bir disk eklendiğinde, **Leasestatus** özelliği **kilitli** olarak ayarlanır. Yönetilmeyen bir disk bağlı değilken, **Leasestatus** özelliği **kilitlenmemiş** olarak ayarlanır. Betik, bir Azure aboneliğindeki tüm Azure depolama hesaplarında yönetilmeyen tüm diskleri inceler. Betik, **Leasestatus** özelliği **kilitlenmemiş** olarak ayarlanmış bir yönetilmeyen disk bulduktan sonra, komut dosyası diskin eklenmemiş olduğunu belirler.

>[!IMPORTANT]
>İlk olarak, **Deleteunattachedvhd** değişkenini olarak ayarlayarak betiği çalıştırın `$false` . Bu eylem, tüm eklenmemiş yönetilmeyen VHD 'leri bulup görüntülemenizi sağlar.
>
>Eklenmemiş tüm diskleri gözden geçirdikten sonra, betiği yeniden çalıştırın ve **Deleteunattachedvhd** değişkenini olarak ayarlayın `$true` . Bu eylem, tüm eklenmemiş yönetilmeyen VHD 'leri silmenize olanak sağlar.

```azurepowershell-interactive
# Set deleteUnattachedVHDs=$true if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=$false if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=$false
$storageAccounts = Get-AzStorageAccount
foreach($storageAccount in $storageAccounts){
    $storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value
    $context = New-AzStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey
    $containers = Get-AzStorageContainer -Context $context
    foreach($container in $containers){
        $blobs = Get-AzStorageBlob -Container $container.Name -Context $context
        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
                    if($deleteUnattachedVHDs){
                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                        $_ | Remove-AzStorageBlob -Force
                        Write-Host "Deleted unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                    }
                    else{
                        $_.ICloudBlob.Uri.AbsoluteUri
                    }
            }
        }
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [bir depolama hesabını silme](../../storage/common/storage-account-create.md#delete-a-storage-account) ve [PowerShell kullanarak yalnız bırakılmış diskleri tanımla](/archive/blogs/ukplatforms/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell)