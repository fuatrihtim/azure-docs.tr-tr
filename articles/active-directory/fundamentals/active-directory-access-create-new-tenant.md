---
title: Hızlı başlangıç-erişim & yeni kiracı oluşturma-Azure AD
description: Azure Active Directory bulma ve kuruluşunuz için yeni bir kiracı oluşturma hakkında yönergeler.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: eeea88d8c21ba754fbeadbb24891126b639616c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96437249"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Hızlı başlangıç: Azure Active Directory yeni bir kiracı oluşturun
Kuruluşunuz için yeni bir kiracı oluşturulması da dahil olmak üzere, Azure Active Directory (Azure AD) portalı kullanarak tüm yönetim görevlerinizi gerçekleştirebilirsiniz. 

Bu hızlı başlangıçta, Azure portala ve Azure Active Directory’ye nasıl erişeceğinizi ve kuruluşunuz için temel bir kiracının nasıl oluşturulacağını öğreneceksiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="create-a-new-tenant-for-your-organization"></a>Kuruluşunuz için yeni bir kiracı oluşturma
Azure portalda oturum açtıktan sonra kuruluşunuz için yeni bir kiracı oluşturabilirsiniz. Yeni kiracınız kuruluşunuzu temsil eder ve şirket içindeki ve dışındaki kullanıcılarınız için belirli bir Microsoft bulut hizmetleri örneğini yönetmenize yardımcı olur.

### <a name="to-create-a-new-tenant"></a>Yeni kiracı oluşturmak için

1. Kuruluşunuzun [Azure Portal](https://portal.azure.com/)oturum açın.

1. Azure portal menüsünde **Azure Active Directory**' i seçin.  

    <kbd>![Azure Active Directory-genel bakış sayfası-kiracı oluşturma](media/active-directory-access-create-new-tenant/azure-ad-portal.png)</kbd>  

1. **Kiracı oluştur**' u seçin.

1. Temel bilgiler sekmesinde, oluşturmak istediğiniz kiracı türünü ( **Azure Active Directory** veya **Azure Active Directory (B2C)** seçin.

1. **İleri ' yi seçin:** yapılandırma sekmesine gitmek için yapılandırma.

    <kbd>![Azure Active Directory-kiracı sayfası oluşturma-yapılandırma sekmesi ](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)</kbd>

1.  Yapılandırma sekmesinde, aşağıdaki bilgileri girin:
    
    - _Contoso organizasyonu_ ' nu **kuruluş adı** kutusuna yazın.

    - **İlk etki alanı adı** kutusuna _contosoorg_ yazın.

    - **Ülke veya bölge** kutusunda _Amerika Birleşik Devletleri_ seçeneğini değiştirmeden bırakın.

1. **İleri ' yi seçin: gözden geçir + oluştur**. Girdiğiniz bilgileri gözden geçirin ve bilgiler doğru ise **Oluştur**' u seçin.

    <kbd>![Azure Active Directory-kiracı sayfasını gözden geçirin ve oluşturun](media/active-directory-access-create-new-tenant/azure-ad-review.png)</kbd>

contoso.onmicrosoft.com etki alanıyla yeni kiracınız oluşturulur.

## <a name="clean-up-resources"></a>Kaynakları temizleme
Bu uygulamayı kullanmaya devam etmeyecekecekseniz, aşağıdaki adımları kullanarak kiracıyı silebilirsiniz:

- Azure portal **Dizin + abonelik** filtresi aracılığıyla silmek istediğiniz dizinde oturum açtığınızdan ve gerekirse hedef dizine geçiş yapıldığından emin olun.
- **Azure Active Directory** seçeneğini belirleyin ve sonra **Contoso - Genel Bakış** sayfasında **Dizini sil**’i seçin.

    Kiracı ve ilişkili bilgileri silinir.

    <kbd>![Vurgulanan dizini Sil düğmesi ile genel bakış sayfası](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)</kbd>

## <a name="next-steps"></a>Sonraki adımlar
- Etki alanı adlarını değiştirme veya ekleme; bkz. [Azure Active Directory’ye özel etki alanı adı ekleme](add-custom-domain.md)

- Kullanıcı ekleme; bkz. [Yeni kullanıcı ekleme veya silme](add-users-azure-active-directory.md)

- Gruplar ve üyeler ekleme; bkz. [Temel bir grup oluşturma ve üyeler ekleme](active-directory-groups-create-azure-portal.md)

- Kuruluşunuzun uygulamasının ve kaynak erişiminin yönetilmesine yardımcı olmak üzere Privileged Identity Management ve [koşullu erişim](../../role-based-access-control/conditional-access-azure-management.md) [kullanarak rol tabanlı erişim](../../role-based-access-control/best-practices.md) hakkında bilgi edinin.

- [Temel lisans bilgileri, terminoloji ve ilişkili özellikleri](active-directory-whatis.md) dahil olmak üzere Azure AD hakkında bilgi edinin.
