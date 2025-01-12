---
title: Yetkilendirme yönetiminde erişim paketini gizleme veya silme-Azure AD
description: Azure Active Directory yetkilendirme yönetiminde bir erişim paketini gizlemeyi veya silmeyi öğrenin.
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
ms.openlocfilehash: 22aa97c6e627c2072636ca2e079877ff0f608b68
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87798809"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD Yetkilendirme Yönetimi 'nde erişim paketini gizleme veya silme

Erişim paketleri varsayılan olarak bulunabilir. Bu, bir ilke bir kullanıcının erişim paketini istemesine izin veriyorsa, erişim paketini otomatik olarak erişim portalı 'nda listelenmiş olarak görebilecekleri anlamına gelir. Ancak, **gizli** ayarı, erişim paketinin Kullanıcı erişim portalı 'nda listelenmemesi için değiştirebilirsiniz.

Bu makalede bir erişim paketinin nasıl gizlenmesi veya silineceği açıklanır.

## <a name="change-the-hidden-setting"></a>Gizli ayarı Değiştir

Bir erişim paketinin **gizli** ayarını değiştirmek için bu adımları izleyin.

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi, Katalog sahibi veya erişim paketi Yöneticisi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik** Yönetimi ' ne tıklayın.

1. Sol menüde, **erişim paketleri** ' ne tıklayın ve ardından erişim paketini açın.

1. Genel Bakış sayfasında, **Düzenle**' ye tıklayın.

1. **Gizli** ayarını ayarlayın.

    **Hayır** olarak ayarlanırsa, erişim paketi kullanıcının erişim portalında listelenir.

    **Evet** olarak ayarlanırsa, erişim paketi kullanıcının erişim portalı 'nda listelenmez. Bir kullanıcının erişim paketine doğrudan **erişim portalı bağlantısı** varsa, erişim paketini görüntüleyebilecekleri tek yöntem. Daha fazla bilgi için bkz. [erişim paketi istemek için bağlantıyı paylaşma](entitlement-management-access-package-settings.md).

## <a name="delete-an-access-package"></a>Erişim paketini silme

Erişim paketi yalnızca etkin kullanıcı atamaları yoksa silinebilir. Bir erişim paketini silmek için bu adımları izleyin.

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi, Katalog sahibi veya erişim paketi Yöneticisi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik** Yönetimi ' ne tıklayın.

1. Sol menüde, **erişim paketleri** ' ne tıklayın ve ardından erişim paketini açın.

1. Sol taraftaki menüde **atamalar** ' a tıklayın ve tüm kullanıcılar için erişimi kaldırın.

1. Sol menüde **genel bakış** ' a ve ardından **Sil**' e tıklayın.

1. Görüntülenen silme iletisinde **Evet**' e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

- [Erişim paketi için atamaları görüntüleme, ekleme ve kaldırma](entitlement-management-access-package-assignments.md)
- [Raporları ve günlükleri görüntüleme](entitlement-management-reports.md)
