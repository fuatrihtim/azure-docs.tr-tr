---
title: PıM-Azure AD 'de Azure Kaynak rollerini etkinleştirme | Microsoft Docs
description: Azure Kaynak rollerinizi Azure AD Privileged Identity Management (PıM) ' de nasıl etkinleştireceğinizi öğrenin.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a6ddde80ca554aea25d24694aff76e61e47d928
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97672466"
---
# <a name="activate-my-azure-resource-roles-in-privileged-identity-management"></a>Azure Kaynak rollerimi Privileged Identity Management etkinleştir

Azure kaynakları için uygun rol üyelerinin, gelecekteki bir tarih ve saat için etkinleştirme zamanlamasını sağlamak üzere Privileged Identity Management (PıM) kullanın. Ayrıca, en fazla (yöneticiler tarafından yapılandırılan) belirli bir etkinleştirme süresi arasından seçim yapabilir.

Bu makale, Privileged Identity Management ' de Azure Kaynak rolünü etkinleştirmesi gereken üyelere yöneliktir.

## <a name="activate-a-role"></a>Rol etkinleştirme

Bir Azure Kaynak rolünü gerçekleştirmeniz gerektiğinde, Privileged Identity Management ' de **rollerim** gezinti seçeneğini kullanarak etkinleştirme isteğinde bulunabilir.

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. **Azure AD Privileged Identity Management** açın. Privileged Identity Management kutucuğunu panonuza ekleme hakkında daha fazla bilgi için bkz. [Privileged Identity Management kullanmaya başlama](pim-getting-started.md).

1. **Rollerimi** seçin.

    ![Etkinleştirebilecekleri rolleri gösteren roller sayfası](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Uygun Azure Kaynak rollerinizin listesini görmek için **Azure Kaynak rolleri** ' ni seçin.

    ![Rollerim-Azure Kaynak rolleri sayfası](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png)

1. **Azure Kaynak rolleri** listesinde, etkinleştirmek istediğiniz rolü bulun.

    ![Azure Kaynak rolleri-uygun rollerim listesi](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Etkinleştir sayfasını açmak için **Etkinleştir** ' i seçin.

    ![Kapsam, başlangıç zamanı, süre ve neden ile açılmış etkinleştirme bölmesi](./media/pim-resource-roles-activate-your-roles/azure-role-eligible-activate.png)

1. Rolünüz çok faktörlü kimlik doğrulaması gerektiriyorsa, **devam etmeden önce kimliğinizi doğrula**' yı seçin. Her oturum için yalnızca bir kez kimlik doğrulaması yapmanız gerekir.

    ![Rol etkinleştirmeden önce MFA ile kimliğimi doğrula](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. **Kimliğimi doğrula** ' yı seçin ve ek güvenlik doğrulaması sağlamak için yönergeleri izleyin.

    ![PIN kodu gibi güvenlik doğrulaması sağlayan ekran](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Azaltılmış bir kapsam belirtmek istiyorsanız **kapsam** ' ı seçerek kaynak filtresi bölmesini açın.

    Yalnızca ihtiyacınız olan kaynaklara erişim istemek için en iyi uygulamadır. Kaynak filtre bölmesinde, erişmeniz gereken kaynak gruplarını veya kaynakları belirtebilirsiniz.

    ![Kapsam belirtmek için etkinleştir-kaynak filtre bölmesi](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Gerekirse, özel bir etkinleştirme başlangıç saati belirtin. Üye, seçili saatten sonra etkinleştirilecektir.

1. **Neden** kutusunda, etkinleştirme isteğinin nedenini girin.

    ![Kapsam, başlangıç zamanı, süre ve nedenle etkinleştirme bölmesi tamamlandı](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. **Etkinleştir**' i seçin.

    Rol etkinleştirme için [onay gerektiriyorsa](pim-resource-roles-approval-workflow.md) , tarayıcınızın sağ üst köşesinde, isteğin onay beklendiğini bildiren bir bildirim görüntülenir.

    ![Etkinleştirme isteği onay bildirimi bekliyor](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>İsteklerinizin durumunu görüntüleyin

Etkinleştirme için bekleyen isteklerinizin durumunu görüntüleyebilirsiniz.

1. Azure AD Privileged Identity Management açın.

1. Azure AD rolünüzün ve Azure Kaynak rolü isteklerinizin listesini görmek için **isteklerim** ' i seçin.

    ![İsteklerim-bekleyen isteklerinizi gösteren Azure Kaynak sayfası](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. **Istek durumu** sütununu görüntülemek için sağa kaydırın.

## <a name="cancel-a-pending-request"></a>Bekleyen bir isteği iptal etme

Onay gerektiren bir rolün etkinleştirilmesini gerektirmiyorsa, bekleyen bir isteği dilediğiniz zaman iptal edebilirsiniz.

1. Azure AD Privileged Identity Management açın.

1. **Isteklerim**' i seçin.

1. İptal etmek istediğiniz rol için **iptal** bağlantısını seçin.

    Iptal ' i seçtiğinizde istek iptal edilir. Rolü yeniden etkinleştirmek için, etkinleştirme için yeni bir istek göndermeniz gerekir.

   ![Iptal eylemi vurgulanmış istek listem](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Sorun giderme

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Rol etkinleştirildikten sonra izinler verilmiyor

Privileged Identity Management bir rolü etkinleştirdiğinizde, etkinleştirme ayrıcalıklı rol gerektiren tüm portallara anında yaymayabilir. Bazı durumlarda değişiklik yayılsa bile portalda web önbelleği değişikliğin anında geçerlilik kazanmamasına yol açabilir. Etkinleştirme gecikirse, yapmanız gerekenler aşağıda verilmiştir.

1. Azure portalında oturumunuzu kapatın ve sonra yeniden oturum açın.
1. Privileged Identity Management, rolün üyesi olarak listelendiğinizi doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management Azure Kaynak rollerini genişletme veya yenileme](pim-resource-roles-renew-extend.md)
- [Privileged Identity Management 'de Azure AD rollerimi etkinleştirin](pim-how-to-activate-role.md)
