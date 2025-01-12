---
title: PıM-Azure AD 'de Azure Kaynak rolü ayarlarını yapılandırma | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) içinde Azure Kaynak rolü ayarlarını yapılandırmayı öğrenin.
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
ms.date: 01/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40779e4517b610e52e41caacc46fc613b72023e5
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567144"
---
# <a name="configure-azure-resource-role-settings-in-privileged-identity-management"></a>Privileged Identity Management Azure Kaynak rolü ayarlarını yapılandırma

Azure Kaynak rolü ayarlarını yapılandırırken, Azure Active Directory (Azure AD) Privileged Identity Management (PıM) içindeki Azure Kaynak rol atamalarına uygulanan varsayılan ayarları tanımlarsınız. Onay iş akışını yapılandırmak ve istekleri onaylayabilecek veya reddedebilecek kişileri belirtmek için aşağıdaki yordamları kullanın.

## <a name="open-role-settings"></a>Rol ayarlarını aç

Azure Kaynak rolü ayarlarını açmak için aşağıdaki adımları izleyin.

1. [Ayrıcalıklı rol yöneticisi](../roles/permissions-reference.md#privileged-role-administrator) rolündeki bir kullanıcıyla [Azure Portal](https://portal.azure.com/) için oturum açın.

1. **Azure AD Privileged Identity Management** açın.

1. **Azure kaynakları**' nı seçin.

1. Yönetmek istediğiniz kaynağı bir abonelik veya yönetim grubu gibi seçin.

    ![Azure kaynakları sayfası yönetilebilecek kaynakları listeleme](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. **Rol ayarları**' nı seçin.

    ![Rol ayarları sayfası Azure Kaynak rollerini listeleme](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Ayarlarını yapılandırmak istediğiniz rolü seçin.

    ![Rol ayarı ayrıntıları sayfası çeşitli atama ve etkinleştirme ayarlarını listeleme](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. **Rol ayarları** bölmesini açmak için **Düzenle** ' yi seçin. İlk sekme, Privileged Identity Management ' de rol etkinleştirme yapılandırmasını güncelleştirmenize olanak tanır.

    ![Etkinleştirme sekmesi açık olan rol ayarlarını Düzenle sayfası](./media/pim-resource-roles-configure-role-settings/role-settings-activation-tab.png)

1. Atama ayarı sekmesini açmak için, sayfanın altındaki **atama** sekmesini veya **Sonraki: atama** düğmesini seçin. Bu ayarlar, Privileged Identity Management arabirimi içinde yapılan rol atamalarını denetler.

    ![Rol ayarları sayfasındaki rol atama sekmesi](./media/pim-resource-roles-configure-role-settings/role-settings-assignment-tab.png)

1. Bu rolün bildirim ayarı sekmesine ulaşmak için sayfanın alt kısmındaki **bildirim** sekmesini veya **Sonraki: etkinleştirme** düğmesini kullanın. Bu ayarlar, bu rolle ilgili tüm e-posta bildirimlerini denetler.

    ![Rol ayarları sayfasındaki rol bildirimleri sekmesi](./media/pim-resource-roles-configure-role-settings/role-settings-notification-tab.png)

    Rol ayarları sayfasındaki **Bildirimler** sekmesinde, Privileged Identity Management bildirimleri alan ve aldıkları bildirimler üzerinde ayrıntılı denetim olanağı sunar.

    - **E-postayı kapatma**<br>Varsayılan alıcı onay kutusunu temizleyerek ve ek alıcıları silerek belirli e-postaları devre dışı bırakabilirsiniz.  

    - **E-postaları belirtilen e-posta adresleriyle sınırla**<br>Varsayılan alıcı onay kutusunu temizleyerek varsayılan alıcılara gönderilen e-postaları devre dışı bırakabilirsiniz. Daha sonra ek alıcılar olarak ek e-posta adresleri ekleyebilirsiniz. Birden fazla e-posta adresi eklemek istiyorsanız, bunları noktalı virgül (;) ile ayırın.

    - **Varsayılan alıcılara ve ek alıcılara e-posta gönder**<br>Varsayılan alıcı onay kutusunu seçerek ve ek alıcıların e-posta adreslerini ekleyerek, hem varsayılan alıcıya hem de ek alıcıya e-posta gönderebilirsiniz.

    - **Yalnızca kritik e-postalar**<br>Her e-posta türü için, yalnızca kritik e-postaları alma onay kutusunu seçebilirsiniz. Bunun anlamı Privileged Identity Management, yalnızca e-posta acil bir eylem gerektirdiğinde, yapılandırılan alıcılara e-posta gönderilmeye devam edecektir. Örneğin, kullanıcıların rol atamasını genişletmesini isteyen e-postalar tetiklenmeyecektir, bu da yöneticilerin bir uzantı isteğini onaylaması gereken e-posta tetiklenecektir.

1. Rol ayarlarını güncelleştirmek için herhangi bir zamanda **Güncelleştir** düğmesini seçin.

## <a name="assignment-duration"></a>Atama süresi

Bir rolün ayarlarını yapılandırırken her atama türü için iki atama süresi seçeneğinden birini seçebilirsiniz (uygun ve etkin). Bu seçenekler, bir Kullanıcı Privileged Identity Management role atandığında varsayılan en uzun süre olur.

**Uygun** atama süresi seçeneklerinden birini belirleyebilirsiniz:

| | Description |
| --- | --- |
| **Kalıcı uygun atamaya izin ver** | Kaynak yöneticileri kalıcı uygun atama atayabilir. |
| **Uygun atamayı sona ermeden önce** | Kaynak yöneticileri, tüm uygun atamaların belirtilen başlangıç ve bitiş tarihine sahip olmasını gerektirebilir. |

Ve bu **etkin** atama süresi seçeneklerinden birini belirleyebilirsiniz:

| | Description |
| --- | --- |
| **Kalıcı etkin atamaya izin ver** | Kaynak yöneticileri, kalıcı etkin atama atayabilir. |
| **Etkin atamanın son kullanım tarihi** | Kaynak yöneticileri, tüm etkin atamaların belirtilen başlangıç ve bitiş tarihine sahip olmasını gerektirebilir. |

> [!NOTE]
> Belirtilen bitiş tarihine sahip tüm atamalar, kaynak yöneticileri tarafından yenilenebilir. Ayrıca, kullanıcılar, [rol atamalarını genişletmek veya yenilemek](pim-resource-roles-renew-extend.md)için self servis istekleri başlatabilir.

## <a name="require-multi-factor-authentication"></a>Multi-Factor Authentication gerektir

Privileged Identity Management, iki ayrı senaryo için isteğe bağlı Azure AD Multi-Factor Authentication zorlaması sağlar.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Etkin atama üzerinde Multi-Factor Authentication gerektir

Bazı durumlarda, bir kullanıcı veya grubu bir rol için kısa bir süre (örneğin, bir gün) atamak isteyebilirsiniz. Bu durumda, atanan kullanıcıların etkinleştirme istemesi gerekmez. Bu senaryoda, Kullanıcı rol atamasını kullandığında, rol atandığı zamandan zaten etkin olduklarından Privileged Identity Management Multi-Factor Authentication 'ı zorlayamıyor.

Atamayı karşılayan kaynak yöneticisinin bunları söyledikleri kim olduğundan emin olmak için, **etkin atama üzerinde Multi-Factor Authentication gerektir** kutusunu işaretleyerek, etkin atamada Multi-Factor Authentication 'ı zorunlu kılabilirsiniz.

### <a name="require-multi-factor-authentication-on-activation"></a>Etkinleştirme üzerinde Multi-Factor Authentication gerektir

Bir rol için uygun olan kullanıcıların etkinleştirebilmeleri için Azure AD Multi-Factor Authentication kimin kullandığını kanıtlamasını zorunlu kılabilirsiniz. Multi-Factor Authentication, kullanıcının makul bir belirsizlik olduğunu söylediklerini sağlar. Bu seçeneğin zorunlu kılınması, Kullanıcı hesabının tehlikeye girdiği durumlarda kritik kaynakları korur.

Etkinleştirmeden önce çok faktörlü kimlik doğrulaması gerektirmek için **etkinleştirme Multi-Factor Authentication gerektir** kutusunu işaretleyin.

Daha fazla bilgi için bkz. [Multi-Factor Authentication ve Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Etkinleştirme en uzun süre

Rolün süresi dolmadan önce etkin kalacağı en uzun süreyi saat cinsinden ayarlamak için **etkinleştirme en uzun süre** kaydırıcısını kullanın. Bu değer bir ile 24 saat arasında olabilir.

## <a name="require-justification"></a>Gerekçe gerektir

Kullanıcıların etkinleştirildiklerinde bir iş doğrulaması girmesini zorunlu kılabilirsiniz. Gerekçe gerektirmek için **etkin atamada gerekçe iste** kutusunu veya **etkinleştirme üzerinde gerekçe iste** kutusunu işaretleyin.

## <a name="require-approval-to-activate"></a>Etkinleştirmek için onay gerektir

Bir rolü etkinleştirmek için onay gerektirmek isterseniz, aşağıdaki adımları izleyin.

1. **Etkinleştirmek için onay gerektir** onay kutusunu işaretleyin.

1. **Bir üye veya grup seçin** sayfasını açmak Için **onaylayanları Seç** ' i seçin.

    ![Onaylayanları seçmek için bir kullanıcı veya grup bölmesi seçin](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. En az bir kullanıcı veya grup seçin ve ardından **Seç**' e tıklayın. Herhangi bir Kullanıcı ve grup bileşimini ekleyebilirsiniz. En az bir onaylayan seçmelisiniz. Varsayılan onaylayanlar yok.

    Seçimleriniz seçili onaylayanlar listesinde görünür.

1. Tüm rol ayarlarınızı belirledikten sonra, değişikliklerinizi kaydetmek için **Güncelleştir** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management Azure Kaynak rolleri atama](pim-resource-roles-assign-roles.md)
- [Privileged Identity Management Azure Kaynak rolleri için güvenlik uyarılarını yapılandırma](pim-resource-roles-configure-alerts.md)
