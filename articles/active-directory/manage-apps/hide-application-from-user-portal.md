---
title: Azure AD 'de bir kurumsal uygulamayı kullanıcının deneyiminden gizleme
description: Azure Active Directory erişim panellerinde veya Microsoft 365 başlatanlardan bir kurumsal uygulamayı kullanıcının deneyiminden gizleme.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 03/25/2020
ms.author: kenwith
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8469b48b92f3f9a645a0c05441e6c1943b02e16f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258889"
---
# <a name="hide-enterprise-applications-from-end-users-in-azure-active-directory"></a>Azure Active Directory içindeki son kullanıcılardan kurumsal uygulamaları gizleyin

Son kullanıcıların Uygulamaps panelinden veya Microsoft 365 başlatıcısının uygulamalarının nasıl gizlenmek için yönergeler. Bir uygulama gizli olduğunda, kullanıcıların uygulama izinleri hala vardır. 

## <a name="prerequisites"></a>Önkoşullar

Uygulama Yöneticisi ayrıcalıkları, bir uygulamayı Uygps panelinden ve Microsoft 365 başlatıcısı 'ndan gizlemek için gereklidir.

Tüm Microsoft 365 uygulamalarını gizlemek için genel yönetici ayrıcalıkları gerekir.


## <a name="hide-an-application-from-the-end-user"></a>Bir uygulamayı son kullanıcıdan gizleme
Bir uygulamayı Uygulamaps panelinden ve uygulama başlatıcısı Microsoft 365 gizlemek için aşağıdaki adımları kullanın.

1.  [Azure Portal](https://portal.azure.com) , dizininiz için genel yönetici olarak oturum açın.
2.  **Azure Active Directory** seçin.
3.  **Kurumsal uygulamalar**' ı seçin. **Kurumsal uygulamalar-tüm uygulamalar** dikey penceresi açılır.
4.  **Uygulama türü**' nün altında, henüz seçili değilse **Kurumsal uygulamalar**' ı seçin.
5.  Gizlemek istediğiniz uygulamayı arayın ve uygulamayı tıklatın.  Uygulamanın genel görünümü açılır.
6.  **Özellikler**'e tıklayın. 
7.  **Kullanıcılar Için görünebilir mi?** sorusu için **Hayır**'a tıklayın.
8.  **Kaydet**’e tıklayın.

> [!NOTE]
> Bu yönergeler yalnızca kurumsal uygulamalar için geçerlidir.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Azure AD PowerShell kullanarak bir uygulamayı gizleme

Uygulamaps panelinden bir uygulamayı gizlemek için, HideApp etiketini uygulamanın hizmet sorumlusuna el ile ekleyebilirsiniz. Uygulamanın **Users olarak görünür mü?** özelliğini **Hayır** olarak ayarlamak Için aşağıdaki [azuread PowerShell](/powershell/module/azuread/#service_principals) komutlarını çalıştırın. 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-microsoft-365-applications-from-the-myapps-panel"></a>Uygulamaps panelinden Microsoft 365 uygulamalarını gizle

Uygulamaps panelinden tüm Microsoft 365 uygulamalarını gizlemek için aşağıdaki adımları kullanın. Uygulamalar hala Office 365 portalında görünür.

1.  [Azure Portal](https://portal.azure.com) , dizininiz için genel yönetici olarak oturum açın.
2.  **Azure Active Directory** seçin.
3.  **Kullanıcılar**’ı seçin.
4.  **Kullanıcı ayarları**'nı seçin.
5.  **Kurumsal uygulamalar** altında **son kullanıcıların uygulamalarını nasıl başlatıp görüntüleyebileceğini Yönet** ' e tıklayın.
6.  **Kullanıcılar yalnızca office 365 portalında office 365 uygulamalarını görebilir**, **Evet**' e tıklayın.
7.  **Kaydet**’e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar
* [Tüm gruplarımı gör](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Kurumsal uygulamaya Kullanıcı veya Grup atama](assign-user-or-group-access-portal.md)
* [Bir kurumsal uygulamadan Kullanıcı veya grup atamasını kaldırma](./assign-user-or-group-access-portal.md)
* [Kurumsal uygulamanın adını veya logosunu değiştirme](./add-application-portal-configure.md)
