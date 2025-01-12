---
title: Grup adlandırma ilkesi hızlı başlangıç-Azure Active Directory | Microsoft Docs
description: Azure Active Directory'de yeni kullanıcıların eklenmesini var olan kullanıcıların silinmesini açıklar
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6013d14d7639bf4f7fe318c9a2da96e4d8acbcb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96546293"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Hızlı başlangıç: Azure Active Directory'de gruplar için adlandırma ilkesi

Bu hızlı başlangıçta, kuruluşunuzun gruplarını sıralamanıza ve aramanıza yardımcı olması için Azure Active Directory (Azure AD) kuruluşunuzda kullanıcı tarafından oluşturulan Microsoft 365 grupları için adlandırma ilkesi ayarlayacaksınız. Adlandırma ilkesini aşağıdaki gibi amaçlar için kullanabilirsiniz:

* Grubun işlevini, üyelerini, coğrafi bölgesini veya grubu oluşturan kişiyi paylaşma.
* Adres defterindeki grupların kategorilere ayrılmasına yardımcı olma.
* Belirli sözcüklerin grup adlarında ve diğer adlarında kullanılmasını engelleme.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="configure-the-group-naming-policy-in-the-azure-portal"></a>Azure portal grup adlandırma ilkesini yapılandırma

1. [Azure AD Yönetim merkezinde](https://aad.portal.azure.com) Kullanıcı yönetici hesabıyla oturum açın.
1. **Gruplar**' ı seçin ve adlandırma ilkesi sayfasını açmak için **adlandırma ilkesi** ' ni seçin.

    ![Yönetim merkezinde adlandırma ilkesi sayfasını açın](./media/groups-quickstart-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Ön ek adlandırma ilkesini görüntüleme veya düzenleme

1. **Adlandırma ilkesi** sayfasında **grup adlandırma ilkesi**' ni seçin.
1. Adlandırma ilkesinin bir parçası olarak zorlamak istediğiniz öznitelikleri veya dizeleri seçerek geçerli ön eki veya sonek adlandırma ilkelerini tek tek görüntüleyebilir veya düzenleyebilirsiniz.
1. Listeden bir ön eki veya soneki kaldırmak için ön eki veya soneki seçin, sonra **Sil**' i seçin. Aynı anda birden çok öğe silinebilir.
1. İlke üzerinde yaptığınız değişikliklerin yürürlüğe geçmek için **Kaydet** ' i seçin.

### <a name="view-or-edit-the-custom-blocked-words"></a>Özel engellenen sözcükleri görüntüleyin veya düzenleyin

1. **Adlandırma ilkesi** sayfasında, **Engellenen sözcükler**' i seçin.

    ![adlandırma ilkesi için engellenen sözcükler listesini düzenle ve karşıya yükle](./media/groups-quickstart-naming-policy/blockedwords.png)

1. **İndir**' i seçerek özel engellenen sözcüklerin geçerli listesini görüntüleyin veya düzenleyin.
1. Dosya simgesini seçerek özel engellenen sözcüklerin yeni listesini karşıya yükleyin.
1. İlke üzerinde yaptığınız değişikliklerin yürürlüğe geçmek için **Kaydet** ' i seçin.

İşte bu kadar. Adlandırma ilkenizi ayarladınız ve özel engellenen sözcüklerinizi eklediniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

### <a name="remove-the-naming-policy-using-azure-portal"></a>Azure portal kullanarak adlandırma ilkesini kaldırma

1. **Adlandırma ilkesi** sayfasında **ilkeyi Sil**' i seçin.
1. Silme işlemini doğruladıktan sonra, tüm önek-sonek adlandırma ilkesi ve özel engellenen sözcükler dahil olmak üzere, adlandırma ilkesi kaldırılır.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure portal aracılığıyla Azure AD kuruluşunuz için adlandırma ilkesini nasıl ayarlayadığınızı öğrendiniz.

Adlandırma ilkesi, teknik kısıtlamalar, özel engellenen sözcüklerin listesini ekleme ve Microsoft 365 uygulamalarda son kullanıcı deneyimleri gibi daha fazla bilgi için sonraki makaleye ilerleyin.
> [!div class="nextstepaction"]
> [Adlandırma ilkesi PowerShell](groups-naming-policy.md)