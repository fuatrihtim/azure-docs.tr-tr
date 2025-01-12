---
title: Azure 'da Windows VM 'Leri hakkında SSS
description: Kaynak Yöneticisi modeliyle oluşturulan Windows sanal makineler ile ilgili bazı yaygın soruların yanıtlarını sağlar.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: 2dc94ea904da128be01decf36e2d36dc9510c2a5
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043895"
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Windows Sanal Makineleri hakkında sık sorulan sorular
Bu makalede, Azure 'da Kaynak Yöneticisi dağıtım modeli kullanılarak oluşturulan Windows sanal makineleri hakkında bazı yaygın soruların ele alınmaktadır. Bu konunun Linux sürümü için bkz. [Linux sanal makineleri hakkında sık sorulan sorular](../linux/faq.md).

## <a name="what-can-i-run-on-an-azure-vm"></a>Azure sanal makinesinde ne çalıştırabilirim?
Tüm aboneler bir Azure sanal makinesinde sunucu yazılımı çalıştırabilir. Azure 'da Microsoft Server yazılımını çalıştırmaya yönelik destek ilkesi hakkında daha fazla bilgi için bkz. [Azure sanal makineleri Için Microsoft sunucu yazılımı desteği](https://support.microsoft.com/kb/2721672).

Windows 7, Windows 8.1 ve Windows 10 ' un bazı sürümleri, geliştirme ve test görevlerinde MSDN Azure avantajı aboneleri ve MSDN geliştirme ve Test Kullandıkça Öde aboneleri tarafından kullanılabilir. Yönerge ve kısıtlamalar dahil olmak üzere ayrıntılı bilgi edinmek için bkz. [MSDN aboneleri için Windows İstemci görüntüleri](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Bir sanal makineyle birlikte ne kadar depolama alanı kullanabilirim?
Her veri diski 32.767 GiB 'a kadar olabilir. Kullanabileceğiniz veri diski sayısı, sanal makinenin boyutuna bağlıdır. Ayrıntılar için bkz. [Virtual Machines boyutları](../sizes.md).

Azure yönetilen diskler, verilerin kalıcı olarak depolanması için Azure sanal makineler ile kullanılmak üzere önerilen disk depolama tekliflerdir. Her bir sanal makine ile birden fazla yönetilen disk kullanabilirsiniz. Yönetilen diskler iki tür dayanıklı depolama seçeneği sunar: Premium ve standart yönetilen diskler. Fiyatlandırma bilgileri için bkz. [yönetilen diskler fiyatlandırması](https://azure.microsoft.com/pricing/details/managed-disks).

Azure depolama hesapları, işletim sistemi diski ve tüm veri diskleri için depolama alanı da sağlayabilir. Her disk bir sayfa blobu olarak depolanan bir .vhd dosyasıdır. Fiyatlandırma ayrıntıları için bkz. [Depolama Fiyatlandırma Ayrıntıları](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Sanal makinime nasıl erişebilirim?
Bir Windows sanal makinesi için Uzak Masaüstü Bağlantısı (RDP) kullanarak uzak bağlantı kurun. Yönergeler için bkz. [Windows çalıştıran bir Azure sanal makinesine bağlanma ve oturum açma](connect-logon.md). Sunucu bir Uzak Masaüstü Hizmetleri oturumu ana bilgisayarı olarak yapılandırılmadığı sürece en fazla iki eşzamanlı bağlantı desteklenir.  

Uzak Masaüstü ile ilgili sorun yaşıyorsanız, bkz. [Windows tabanlı bir Azure sanal makinesine yönelik uzak masaüstü bağlantılarında sorun giderme](/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Hyper-V ' y i kullandıysanız, VMConnect 'e benzer bir araç arıyor olabilirsiniz. Azure, bir sanal makineye konsol erişimi desteklenmediğinden benzer bir araç sunmaz.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Verileri depolamak için geçici diski (varsayılan olarak D: sürücü) kullanabilir miyim?
Verileri depolamak için geçici diski kullanmayın. Yalnızca geçici depolama alanı olduğundan, kurtarılamamış verileri kaybetme riskini alırsınız. Sanal makine farklı bir konağa taşınırsa veri kaybı oluşabilir. Sanal makinenin yeniden boyutlandırılması, konağın güncelleştirilmesi veya konaktaki bir donanım hatası, sanal makinenin taşınmasını gerektirecek olası nedenler arasındadır.

D: sürücü harfini kullanması gereken bir uygulamanız varsa, geçici diskin d:farklı bir şey kullanması için sürücü harflerini yeniden atayabilirsiniz. Yönergeler için bkz. [Windows geçici diskinin sürücü harfini değiştirme](change-drive-letter.md).


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Geçici diskin sürücü harfini nasıl değiştirebilirim?
Sürücü harfini, sayfa dosyasını taşıyarak ve sürücü harflerini yeniden atayarak değiştirebilirsiniz, ancak adımları belirli bir sırada gerçekleştirdiğinizden emin olmanız gerekir. Yönergeler için bkz. [Windows geçici diskinin sürücü harfini değiştirme](change-drive-letter.md).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Mevcut bir VM 'yi bir kullanılabilirlik kümesine ekleyebilir miyim?
Hayır. SANAL makinenizin bir kullanılabilirlik kümesinin parçası olmasını istiyorsanız, sanal makineyi küme içinde oluşturmanız gerekir. Şu anda, bir kullanılabilirlik kümesine oluşturulduktan sonra VM eklemenin bir yolu yoktur.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Azure 'a bir sanal makineyi karşıya yükleyebilir miyim?
Evet. Yönergeler için bkz. [Şirket Içi VM 'Leri Azure 'A geçirme](on-prem-to-azure.md).

## <a name="can-i-resize-the-os-disk"></a>İşletim sistemi diskini yeniden boyutlandırabilir miyim?
Evet. Yönergeler için bkz. [Azure Kaynak grubundaki bir sanal makinenin işletim sistemi sürücüsünü genişletme](expand-os-disk.md).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Mevcut bir Azure VM 'yi kopyalayabilir veya kopyalayabilir miyim?
Evet. Yönetilen görüntüleri kullanarak, bir sanal makinenin görüntüsünü oluşturabilir ve sonra birden çok yeni VM oluşturmak için görüntüsünü kullanabilirsiniz. Yönergeler için bkz. [VM 'nin özel bir görüntüsünü oluşturma](tutorial-custom-images.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Neden Kanada Orta ve Kanada Doğu bölgelerini Azure Resource Manager göremiyorum?

Kanada Orta ve Kanada Doğu iki yeni bölgesi, mevcut Azure abonelikleri için sanal makine oluşturma için otomatik olarak kaydedilmemiş. Bu kayıt, bir sanal makine Azure Resource Manager kullanılarak başka bir bölgeye Azure portal aracılığıyla dağıtıldığında otomatik olarak yapılır. Bir sanal makine başka bir Azure bölgesine dağıtıldıktan sonra, yeni bölgeler sonraki sanal makineler için kullanılabilir olmalıdır.

## <a name="does-azure-support-linux-vms"></a>Azure, Linux VM 'lerini destekliyor mu?
Evet. Denemek için hızlı bir şekilde bir Linux VM oluşturmak için, bkz. [portalı kullanarak Azure 'Da LINUX VM oluşturma](../linux/quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Oluşturulduktan sonra VM 'ye bir NIC ekleyebilir miyim?
Evet, bu artık mümkündür. Önce VM 'nin serbest bırakılmasının durdurulması gerekir. Ardından, bir NIC ekleyebilir veya kaldırabilirsiniz (VM 'deki son NIC olmadığı müddetçe). 

## <a name="are-there-any-computer-name-requirements"></a>Herhangi bir bilgisayar adı gereksinimi var mı?
Evet. Bilgisayar adı en fazla 15 karakter uzunluğunda olabilir. Kaynaklarınızı adlandırma hakkında daha fazla bilgi için bkz. [adlandırma kuralları kuralları ve kısıtlamaları](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging) .

## <a name="are-there-any-resource-group-name-requirements"></a>Herhangi bir kaynak grubu adı gereksinimi var mı?
Evet. Kaynak grubu adı en fazla 90 karakter uzunluğunda olabilir. Kaynak grupları hakkında daha fazla bilgi için bkz. [adlandırma kuralları kuralları ve kısıtlamaları](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming) .

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>VM oluştururken Kullanıcı adı gereksinimleri nelerdir?

Kullanıcı adları en fazla 20 karakter uzunluğunda olabilir ve nokta (".") ile bitemez. 

Şu kullanıcı adlarıyla izin verilmiyor:

- `1`
- `123`
- `a`
- `actuser`
- `adm`
- `admin`
- `admin1`
- `admin2`
- `administrator`
- `aspnet`
- `backup`
- `console`
- `david`
- `guest`
- `john`
- `owner`
- `root`
- `server`
- `sql`
- `support_388945a0`
- `support`
- `sys`
- `test`
- `test1`
- `test2`
- `test3`
- `user`
- `user1`
- `user2`

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>VM oluştururken parola gereksinimleri nelerdir?

Kullanmakta olduğunuz araca bağlı olarak, farklı parola uzunluğu gereksinimleri vardır:
 - Portal-12-72 karakter arası
 - PowerShell-8-123 karakter arası
 - CLı-12-123 arasında

* Küçük harfli karakter içermeli
* Büyük harfli karakter içermeli
* Rakam içermeli
* Özel karakter içermeli (Regex eşleşmesi [\W_])

Aşağıdaki parolalara izin verilmiyor:

<table>
    <tr>
        <td>abc@123</td>
        <td>o kadar!</td>
        <td>P@ $ $w 0rd</td>
        <td>P@ssw0rd</td>
        <td>P@ssword123</td>
    </tr>
    <tr>
        <td>PA $ $word</td>
        <td>pass@word1</td>
        <td>Parolayı!</td>
        <td>Parola1</td>
        <td>Password22</td>
    </tr>
</table>