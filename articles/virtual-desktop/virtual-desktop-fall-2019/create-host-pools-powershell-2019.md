---
title: Windows sanal masaüstü (klasik) konak havuzu oluşturma PowerShell-Azure
description: PowerShell cmdlet 'leriyle Windows sanal masaüstü 'nde (klasik) bir konak havuzu oluşturma.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c035a7fbafe9b3a42fbd16e3f8377014010ddd49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88003547"
---
# <a name="create-a-host-pool-in-windows-virtual-desktop-classic-with-powershell"></a>PowerShell ile Windows sanal masaüstü 'nde (klasik) bir konak havuzu oluşturma

>[!IMPORTANT]
>Bu içerik, Windows sanal masaüstü nesneleri Azure Resource Manager desteklemeyen Windows sanal masaüstü (klasik) için geçerlidir. Azure Resource Manager Windows sanal masaüstü nesnelerini yönetmeye çalışıyorsanız, [Bu makaleye](../create-host-pools-powershell.md)bakın.

Konak havuzları, Windows sanal masaüstü kiracı ortamlarında bir veya daha fazla özdeş sanal makine koleksiyonudur. Her konak havuzu, kullanıcıların fiziksel bir masaüstünde yaptıkları gibi etkileşime girebilecekleri bir uygulama grubu içerebilir.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>PowerShell istemcinizi kullanarak bir konak havuzu oluşturun

İlk olarak, henüz yapmadıysanız PowerShell oturumunuzda kullanmak üzere [Windows sanal masaüstü PowerShell modülünü indirip içeri aktarın](/powershell/windows-virtual-desktop/overview/) .

Windows sanal masaüstü ortamında oturum açmak için aşağıdaki cmdlet 'i çalıştırın

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Ardından, Windows sanal masaüstü kiracınızda yeni bir konak havuzu oluşturmak için bu cmdlet 'i çalıştırın:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Bir oturum ana bilgisayarının konak havuzuna katılması ve yerel bilgisayarınızdaki yeni bir dosyaya kaydetmesi için bir kayıt belirteci oluşturmak üzere bir sonraki cmdlet 'i çalıştırın. Kayıt belirtecinin,-ExpirationHours parametresini kullanarak ne kadar süreyle geçerli olduğunu belirtebilirsiniz.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token | Out-File -FilePath <PathToRegFile>
```

Bundan sonra, konak havuzu için varsayılan masaüstü uygulama grubuna Azure Active Directory kullanıcıları eklemek için bu cmdlet 'i çalıştırın.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

**Add-RdsAppGroupUser** cmdlet 'i güvenlik grupları eklemeyi desteklemez ve tek seferde uygulama grubuna yalnızca bir kullanıcı ekler. Uygulama grubuna birden çok kullanıcı eklemek istiyorsanız, cmdlet 'i uygun Kullanıcı asıl adlarıyla yeniden çalıştırın.

Kayıt belirtecini, daha sonra [sanal makineleri Windows sanal masaüstü ana bilgisayar havuzuna kaydet](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)bölümünde kullanacağınız bir değişkene dışarı aktarmak için aşağıdaki cmdlet 'i çalıştırın.

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Konak havuzu için sanal makineler oluşturma

Artık Windows sanal masaüstü ana bilgisayar havuzunuza katılebilecek bir Azure sanal makinesi oluşturabilirsiniz.

Bir sanal makineyi birden çok şekilde oluşturabilirsiniz:

- [Azure Galeri görüntüsünden sanal makine oluşturma](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Yönetilen görüntüden sanal makine oluşturma](../../virtual-machines/windows/create-vm-generalized-managed.md)
- [Yönetilmeyen görüntüden sanal makine oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

>[!NOTE]
>Konak işletim sistemi olarak Windows 7 ' yi kullanarak bir sanal makine dağıtıyorsanız, oluşturma ve dağıtım işlemi biraz farklı olacaktır. Daha ayrıntılı bilgi için bkz. Windows [sanal masaüstü 'Nde Windows 7 sanal makinesi dağıtma](deploy-windows-7-virtual-machine.md).

Oturum Ana bilgisayar sanal makinelerinizi oluşturduktan sonra, Windows veya Windows Server sanal makinelerinizi başka bir lisans için ödeme yapmadan çalıştırmak için bir [Oturum Ana BILGISAYAR VM 'sine bir Windows lisansı uygulayın](../apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) .

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Windows sanal masaüstü Aracısı yüklemeleri için sanal makineleri hazırlama

Windows sanal masaüstü aracılarını yüklemeden ve sanal makineleri Windows sanal masaüstü konak havuzunuza kaydedebilmeniz için önce sanal makinelerinizi hazırlamak üzere aşağıdaki işlemleri yapmanız gerekir:

- Makineyi etki alanına katmalısınız. Bu, gelen Windows sanal masaüstü kullanıcılarının Azure Active Directory hesabından Active Directory hesaplarına eşlenmesini ve sanal makineye başarıyla erişim izni verilmesini sağlar.
- Sanal makine bir Windows Server işletim sistemi çalıştırıyorsa Uzak Masaüstü Oturumu Ana Bilgisayarı (RDSH) rolünü yüklemelisiniz. RDSH rolü Windows sanal masaüstü aracılarının düzgün yüklenmesine izin verir.

Başarıyla etki alanına katılmayı sağlamak için, her bir sanal makine için aşağıdaki işlemleri yapın:

1. Sanal makineyi oluştururken girdiğiniz kimlik bilgileriyle [sanal makineye bağlanın](../../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) .
2. Sanal makinede, **Denetim Masası** ' nı başlatın ve **sistem**' i seçin.
3. **Bilgisayar adı**' nı seçin, **Ayarları Değiştir**' i seçin ve ardından Değiştir ' i seçin **.**
4. **Etki alanı** ' nı seçin ve ardından sanal ağda Active Directory etki alanını girin.
5. Etki alanına katma makinelere ayrıcalıkları olan bir etki alanı hesabıyla kimlik doğrulaması yapın.

    >[!NOTE]
    > VM 'lerinizi bir Azure Active Directory Domain Services (Azure AD DS) ortamına katılıyorsanız, etki alanına katılma kullanıcısının [AAD DC Yöneticiler grubunun](../../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)da bir üyesi olduğundan emin olun.

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Sanal makineleri Windows sanal masaüstü ana bilgisayar havuzuna Kaydet

Sanal makinelerin bir Windows sanal masaüstü konak havuzuna kaydedilmesi, Windows sanal masaüstü aracılarını yüklemek kadar basittir.

Windows sanal masaüstü aracılarını kaydettirmek için, her bir sanal makinede aşağıdakileri yapın:

1. Sanal makineyi oluştururken girdiğiniz kimlik bilgileriyle [sanal makineye bağlanın](../../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) .
2. Windows sanal masaüstü aracısını indirip yükleyin.
   - [Windows sanal masaüstü aracısını](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv)indirin.
   - İndirilen yükleyiciyi sağ tıklatın, **Özellikler**' i seçin, **Engellemeyi kaldır**' ı seçin ve **Tamam**' ı seçin. Bu, sisteminizin yükleyiciye güvenmesini sağlar.
   - Yükleyiciyi çalıştırın. Yükleyici kayıt belirtecini istediğinde, **Export-RdsRegistrationInfo** cmdlet 'inden aldığınız değeri girin.
3. Windows sanal masaüstü Aracısı önyükleme yükleyicisine indirin ve yükleyin.
   - [Windows sanal masaüstü Aracısı önyükleme yükleyicisine](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)indirin.
   - İndirilen yükleyiciyi sağ tıklatın, **Özellikler**' i seçin, **Engellemeyi kaldır**' ı seçin ve **Tamam**' ı seçin. Bu, sisteminizin yükleyiciye güvenmesini sağlar.
   - Yükleyiciyi çalıştırın.

>[!IMPORTANT]
>Azure 'da Windows sanal masaüstü ortamınızı güvenli hale getirmeye yardımcı olmak için, VM 'leriniz üzerinde gelen bağlantı noktası 3389 ' i açmanız önerilir. Windows sanal masaüstü, kullanıcıların konak havuzunun VM 'lerine erişmesi için açık bir gelen bağlantı noktası 3389 gerektirmez. Sorun giderme amacıyla bağlantı noktası 3389 ' i açmanız gerekiyorsa, [tam ZAMANıNDA VM erişimi](../../security-center/security-center-just-in-time.md)kullanmanızı öneririz.

## <a name="next-steps"></a>Sonraki adımlar

Artık bir konak havuzu oluşturduğunuza göre, bunu RemoteApps ile doldurabilirsiniz. Windows sanal masaüstündeki uygulamaları yönetme hakkında daha fazla bilgi edinmek için bkz. uygulama gruplarını yönetme öğreticisi.

> [!div class="nextstepaction"]
> [Uygulama gruplarını yönetme öğreticisi](../manage-app-groups.md)
