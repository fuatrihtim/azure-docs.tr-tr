---
title: Yetkilendirme yönetiminde yeni bir erişim paketi oluşturma-Azure AD
description: Azure Active Directory yetkilendirme yönetimi 'nde paylaştırmak istediğiniz yeni bir kaynak paketi oluşturmayı öğrenin.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3df08272b352ee789c9879b1118105c435cffbd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103011094"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>Azure AD Yetkilendirme Yönetimi 'nde yeni bir erişim paketi oluşturma

Erişim paketi, erişim paketinin ömrü boyunca erişimi otomatik olarak yöneten kaynak ve ilkelerin bir kerelik kurulumunu yapmanızı sağlar. Bu makalede, yeni bir erişim paketinin nasıl oluşturulacağı açıklanır.

## <a name="overview"></a>Genel Bakış

Tüm erişim paketleri, Katalog adlı bir kapsayıcıya yerleştirilmelidir. Katalog, erişim paketinize ekleyebileceğiniz kaynakları tanımlar. Bir katalog belirtmezseniz, erişim paketiniz genel kataloğa konur. Şu anda, var olan bir erişim paketini farklı bir kataloğa taşıyamazsınız.

Bir erişim paketi yöneticisi değilseniz, sahip olduğunuz kaynakları bir kataloğa ekleyemezsiniz. Katalogda bulunan kaynakları kullanmaya kısıtlıdır. Bir kataloğa kaynak eklemeniz gerekiyorsa, Katalog sahibinden sorun olabilir.

Tüm erişim paketlerinde en az bir ilke olmalıdır. İlkeler, erişim paketini ve ayrıca onay ve yaşam döngüsü ayarlarını kimlerin isteyebilen belirler. Yeni bir erişim paketi oluşturduğunuzda, dizininizde kullanıcılar için, dizininizde bulunmayan kullanıcılar için bir başlangıç ilkesi oluşturabilir, yalnızca yönetici doğrudan atamaları için veya ilkeyi daha sonra oluşturmayı tercih edebilirsiniz.

![Erişim paketi oluşturma](./media/entitlement-management-access-package-create/access-package-create.png)

Yeni bir erişim paketi oluşturmak için üst düzey adımlar aşağıda verilmiştir.

1. Identity Idare ' de, yeni bir erişim paketi oluşturmak için işlemi başlatın.

1. Erişim paketini oluşturmak istediğiniz kataloğu seçin.

1. Katalogdan erişim paketinize kaynak ekleyin.

1. Her kaynak için kaynak rolleri atayın.

1. Erişim isteyeerişebilecek kullanıcıları belirtin.

1. Herhangi bir onay ayarını belirtin.

1. Yaşam döngüsü ayarlarını belirtin.

## <a name="start-new-access-package"></a>Yeni erişim paketini Başlat

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi, Katalog sahibi veya erişim paketi Yöneticisi

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. **Azure Active Directory** ' a ve ardından **kimlik** Yönetimi ' ne tıklayın.

1. Sol menüde, **erişim paketleri**' ne tıklayın.

1. **Yeni erişim paketi**' ne tıklayın.
   
    ![Azure portal Yetkilendirme Yönetimi](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>Temel Bilgiler

**Temel bilgiler** sekmesinde, erişim paketine bir ad verirsiniz ve erişim paketinin hangi katalogda oluşturulacağını belirtebilirsiniz.

1. Erişim paketi için bir görünen ad ve açıklama girin. Kullanıcılar, erişim paketi için bir istek gönderdiğinde bu bilgileri görür.

1. **Katalog** açılan listesinde, erişim paketini oluşturmak istediğiniz kataloğu seçin. Örneğin, istenmiş olan tüm pazarlama kaynaklarını yöneten bir katalog sahibi olabilirsiniz. Bu durumda, pazarlama kataloğunu seçebilirsiniz.

    Yalnızca ' de erişim paketleri oluşturma izniniz olan kataloglar görüntülenir. Mevcut bir katalogda bir erişim paketi oluşturmak için, bir genel yönetici veya Kullanıcı Yöneticisi olmanız ya da söz konusu katalogda bir katalog sahibi ya da paket yöneticisi olmanız gerekir.

    ![Erişim paketi-temel bilgiler](./media/entitlement-management-access-package-create/basics.png)

    Bir genel yönetici, bir Kullanıcı Yöneticisi veya katalog Oluşturucu ise ve listelenmeyen yeni bir katalogda erişim paketinizi oluşturmak istiyorsanız **Yeni Katalog oluştur**' a tıklayın. Katalog adı ve açıklamasını girip **Oluştur**' a tıklayın.

    Oluşturmakta olduğunuz erişim paketi ve kendisine dahil edilen kaynaklar yeni kataloğa eklenecektir. Daha sonra da ek Katalog sahipleri ekleyebilirsiniz.

1. **İleri**’ye tıklayın.

## <a name="resource-roles"></a>Kaynak rolleri

**Kaynak rolleri** sekmesinde, erişim paketine dahil edilecek kaynakları seçersiniz. Erişim paketini isteyen ve alan kullanıcılar, erişim paketindeki tüm kaynak rollerini alacaktır.

1. Eklemek istediğiniz kaynak türüne (**gruplar ve takımlar**, **uygulamalar** veya **SharePoint siteleri**) tıklayın.

1. Görüntülenen seçim bölmesinde listeden bir veya daha fazla kaynak seçin.

    ![Paket-kaynak rollerine erişme](./media/entitlement-management-access-package-create/resource-roles.png)

    Erişim paketini genel katalogda veya yeni bir katalogda oluşturuyorsanız, sahip olduğunuz dizinden herhangi bir kaynak seçebilirsiniz. En az bir genel yönetici, bir Kullanıcı Yöneticisi veya katalog Oluşturucu olmalıdır.

    Erişim paketini mevcut bir katalogda oluşturuyorsanız, zaten katalogda olan herhangi bir kaynağı sahip olmadan seçebilirsiniz.

    Genel yönetici, bir Kullanıcı Yöneticisi veya katalog sahibiyseniz, sahip olduğunuz kaynakları seçmek için henüz katalogda olmayan ek bir seçeneğe sahip olursunuz. Seçili katalogda mevcut olmayan kaynakları seçerseniz, bu kaynaklar diğer katalog yöneticilerinin ile erişim paketleri oluşturması için kataloğa da eklenecektir. Kataloğa eklenebilecek tüm kaynakları görmek için, seçme bölmesinin üst tarafındaki **Tüm** onay kutularını işaretleyin. Yalnızca seçili katalogda olan kaynakları seçmek istiyorsanız, **onay kutusunun işaretini** işaretsiz (varsayılan durum) olarak bırakın.

1. Kaynakları seçtikten sonra, **rol** listesinde, kullanıcıların kaynak için atanmasını istediğiniz rolü seçin.

    ![Paket-kaynak rolü seçimine erişin](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. **İleri**’ye tıklayın.

>[!NOTE]
>Bir kataloğa ve bir erişim paketine dinamik gruplar ekleyebilirsiniz. Ancak, bir erişim paketindeki dinamik bir grup kaynağını yönetirken yalnızca sahip rolünü seçebileceksiniz.

## <a name="requests"></a>İstekler

**İstekler** sekmesinde, erişim paketini ve ayrıca onay ayarlarını kimin isteyebilen belirlemek için ilk ilkeyi oluşturursunuz. Daha sonra, ek kullanıcı gruplarının kendi onay ayarlarına sahip erişim paketini istemesine izin vermek için daha fazla istek ilkesi oluşturabilirsiniz.

![Paket Istekleri sekmesine erişin](./media/entitlement-management-access-package-create/requests.png)

Bu erişim paketini isteyebilmek istediğinize bağlı olarak, aşağıdaki bölümlerden birindeki adımları gerçekleştirin.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>Gözden geçirme ve oluşturma

**Gözden geçir + oluştur** sekmelerinde, ayarlarınızı gözden geçirebilir ve herhangi bir doğrulama hatası olup olmadığını denetleyebilirsiniz.

1. Erişim paketinin ayarlarını gözden geçirin

    ![Erişim paketi-ilke ayarını etkinleştir](./media/entitlement-management-access-package-create/review-create.png)

1. Erişim paketini oluşturmak için **Oluştur** ' a tıklayın.

    Yeni erişim paketi, erişim paketleri listesinde görünür.

## <a name="creating-an-access-package-programmatically"></a>Programlı olarak bir erişim paketi oluşturma

Ayrıca, Microsoft Graph kullanarak bir erişim paketi de oluşturabilirsiniz.  Temsilci izni olan bir uygulamayla uygun bir roldeki kullanıcı `EntitlementManagement.ReadWrite.All` , API 'yi çağırabilir

1. [Katalogda accesspackageresoresources listesini listeleyin](/graph/api/accesspackagecatalog-list?tabs=http&view=graph-rest-beta) ve henüz katalogda olmayan kaynaklar Için [bir accessPackageResourceRequest oluşturun](/graph/api/accesspackageresourcerequest-post?tabs=http&view=graph-rest-beta) .
1. Bir accessPackageCatalog içindeki her accessPackageResource 'ın [accessPackageResourceRoles](/graph/api/accesspackage-list-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta) ' i listeleyin. Daha sonra bu roller listesi, daha sonra bir accessPackageResourceRoleScope oluştururken bir rol seçmek için kullanılacaktır.
1. [Bir accessPackage oluşturun](/graph/tutorial-access-package-api?view=graph-rest-beta).
1. [Accesspackageatamailkesi oluşturun](/graph/api/accesspackageassignmentpolicy-post?tabs=http&view=graph-rest-beta).
1. Erişim paketinde gereken her kaynak rolü için [bir accessPackageResourceRoleScope oluşturun](/graph/api/accesspackage-post-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta) .

## <a name="next-steps"></a>Sonraki adımlar

- [Erişim paketi istemek için bağlantıyı paylaşma](entitlement-management-access-package-settings.md)
- [Erişim paketi için kaynak rollerini değiştirme](entitlement-management-access-package-resources.md)
- [Erişim paketine doğrudan kullanıcı atama](entitlement-management-access-package-assignments.md)
- [Erişim paketi için erişim gözden geçirmesi oluşturma](entitlement-management-access-reviews-create.md)
