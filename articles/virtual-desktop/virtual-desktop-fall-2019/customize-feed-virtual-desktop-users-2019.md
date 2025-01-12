---
title: Windows sanal masaüstü (klasik) kullanıcıları için akışı özelleştirme-Azure
description: PowerShell cmdlet 'leri olan Windows sanal masaüstü (klasik) kullanıcılarına yönelik akışı özelleştirme.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cd7496690ec88fbe4297386c32d1b8a2c3234577
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91540770"
---
# <a name="customize-feed-for-windows-virtual-desktop-classic-users"></a>Windows sanal masaüstü (klasik) kullanıcıları için akışı özelleştirme

>[!IMPORTANT]
>Bu içerik, Windows sanal masaüstü nesneleri Azure Resource Manager desteklemeyen Windows sanal masaüstü (klasik) için geçerlidir. Azure Resource Manager Windows sanal masaüstü nesnelerini yönetmeye çalışıyorsanız, [Bu makaleye](../customize-feed-for-virtual-desktop-users.md)bakın.

RemoteApp ve uzak masaüstü kaynaklarının kullanıcılarınız için tanınabilir bir şekilde görünmesi için akışı özelleştirebilirsiniz.

İlk olarak, henüz yapmadıysanız PowerShell oturumunuzda kullanmak üzere [Windows sanal masaüstü PowerShell modülünü indirip içeri aktarın](/powershell/windows-virtual-desktop/overview/) . Bundan sonra hesabınızda oturum açmak için aşağıdaki cmdlet 'i çalıştırın:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>RemoteApp için görünen adı özelleştirme

Kolay adı ayarlayarak, yayımlanan bir RemoteApp için görünen adı değiştirebilirsiniz. Varsayılan olarak, kolay ad RemoteApp programının adı ile aynıdır.

Bir uygulama grubu için yayımlanmış RemoteApps listesini almak için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```

> [!div class="mx-imgBorder"]
> ![Görünen adı özelleştirmek için Name ve FriendlyName ile birlikte Get-RDSRemoteApp PowerShell cmdlet 'inin ekran görüntüsü.](../media/get-rdsremoteapp.png)

Bir RemoteApp 'e kolay bir ad atamak için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```

> [!div class="mx-imgBorder"]
> ![Görünen adı özelleştirmek için, Name ve New FriendlyName ile birlikte Set-RDSRemoteApp PowerShell cmdlet 'inin ekran görüntüsü.](../media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Uzak Masaüstü için görünen adı özelleştirme

Bir kolay ad ayarlayarak yayımlanmış bir uzak masaüstü için görünen adı değiştirebilirsiniz. PowerShell aracılığıyla el ile bir konak havuzu ve Masaüstü uygulama grubu oluşturduysanız, varsayılan kolay ad "oturum Masaüstü" dir. GitHub Azure Resource Manager şablonu veya Azure Marketi teklifi aracılığıyla bir konak havuzu ve Masaüstü uygulama grubu oluşturduysanız, varsayılan kolay ad, ana bilgisayar havuzu adıyla aynıdır.

Uzak Masaüstü kaynağını almak için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```

> [!div class="mx-imgBorder"]
> ![PowerShell cmdlet 'inin adı ve FriendlyName vurgulanmış Get-RDSRemoteApp ekran görüntüsü.](../media/get-rdsremotedesktop.png)

Uzak Masaüstü kaynağına kolay bir ad atamak için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```

> [!div class="mx-imgBorder"]
> ![PowerShell cmdlet 'inin adı ve yeni FriendlyName vurgulanmış Set-RDSRemoteApp ekran görüntüsü.](../media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Sonraki adımlar

Artık kullanıcıların akışını özelleştirdiğinize göre, test etmek için bir Windows sanal masaüstü istemcisinde oturum açabilirsiniz. Bunu yapmak için, Windows sanal masaüstü nasıl yapılır-TOS ' a bağlanma konusuna geçin:

 * [Windows 10 veya Windows 7'den bağlanma](connect-windows-7-10-2019.md)
 * [Web tarayıcısından bağlanma](connect-web-2019.md)
