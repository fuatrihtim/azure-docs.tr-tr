---
title: Azure Market uygulamanızın tek tıklamayla, çoklu oturum açma (SSO) yapılandırması | Microsoft Docs
description: Azure Marketi 'nden uygulamanız için SSO 'nun tek tıklamayla yapılandırmasına yönelik adımlar.
services: active-directory
documentationCenter: na
author: kenwith
manager: daveba
ms.reviewer: kenwith
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50f705e587010d956c29a71127e97dd85cb517ea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258261"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>Çoklu oturum açma için tek tıklamayla uygulama yapılandırması

 Bu öğreticide, Azure Marketi 'nden SAML destekleme, Azure Active Directory (Azure AD) uygulamaları için tek tıklamayla, çoklu oturum açma (SSO) yapılandırması gerçekleştirmeyi öğrenirsiniz.

## <a name="introduction-to-one-click-sso"></a>Tek tıklamayla SSO 'ya giriş

Tek tıklamayla SSO özelliği, SAML protokolünü destekleyen Azure Market uygulamaları için çoklu oturum açmayı yapılandırmak üzere tasarlanmıştır. Azure AD SSO yapılandırma sayfasında bu seçenek, Azure AD meta verilerini uygulama tarafında otomatik olarak yapılandırmanızı sağlar. Bu şekilde, en az el ile gerçekleştirilen çabayla kolayca SSO 'yu ayarlayabilirsiniz.

## <a name="advantages-of-one-click-sso"></a>Tek tıklama SSO 'SU avantajları

- Uygulama tarafında el ile kurulum gerektiren Azure Marketi uygulamalarının hızlı SSO yapılandırması.
- Daha verimli ve doğru SSO yapılandırması.
- Kurulum için ortak iletişim veya destek gerekmez. Uygulama, SAML yapılandırması için Kullanıcı arabirimi sağlar.

## <a name="prerequisites"></a>Önkoşullar

- SSO ile yapılandırılacak uygulamanın etkin bir aboneliği. Yönetici kimlik bilgilerine de ihtiyacınız vardır.
- Uygulamalarım Microsoft 'un tarayıcıda yüklü olan **güvenli oturum açma uzantısı** . Daha fazla bilgi için bkz. [uygulamalarım portalındaki uygulamalara erişme ve bunları kullanma](../user-help/my-apps-portal-end-user-access.md).

## <a name="one-click-sso-configuration-steps"></a>Tek tıklamayla SSO yapılandırma adımları

1. Uygulamayı Azure Marketi 'nden ekleyin.

2. **Çoklu oturum açma** seçeneğini belirleyin.

3. **Çoklu oturum açmayı etkinleştir '** i seçin.

4. **Temel SAML yapılandırması** bölümünde zorunlu yapılandırma değerlerini doldurun.

    > [!NOTE]
    > Uygulamanın yapılandırmanız gereken özel talepleri varsa, tek tıklamayla SSO gerçekleştirmeden önce bunları işleyin.

5. Azure Market uygulamanız için bir tıklama SSO özelliği varsa, aşağıdaki ekranı görürsünüz. **Uzantıyı yükleyebilirsiniz**' i seçerek **uygulamalarım güvenli oturum açma tarayıcı uzantısını** yüklemelisiniz.

   ![Uygulamalarımı güvenli oturum açma tarayıcı uzantısı 'nı yükler](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. Uzantıyı tarayıcıya ekledikten sonra **Kurulum \<Application Name\>**' u seçin. Uygulama Yöneticisi portalına yeniden yönlendirildikten sonra, yönetici olarak oturum açın.

   ![Kurulum uygulaması adı](./media/one-click-sso-tutorial/setup-sso.png)

7. Tarayıcı uzantısı, uygulamada SSO 'yu otomatik olarak yapılandırır. **Evet**' i seçerek onaylayın.

   ![Otomatik doldurulmuş verileri kaydetme](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > Uygulamanız için SSO Yapılandırması ek adımlar gerektiriyorsa, adımları gerçekleştirmek için istemleri takip edin.

8. Yapılandırma tamamlandıktan sonra, değişiklikleri kaydetmek için **Tamam** ' ı seçin.

   ![Otomatik olarak doldurulan verileri kaydetme](./media/one-click-sso-tutorial/save-data.png)

9. SSO ayarlarının başarıyla yapılandırıldığını bilmenizi sağlamak için bir onay penceresi görüntülenir.

   ![SSO yapılandırıldı](./media/one-click-sso-tutorial/sso-configured.png)

10. Yapılandırma başarılı olduktan sonra, uygulamadan oturumunuzu kapatıp Azure portal geri döndürülürsünüz.

11. Çoklu oturum açmayı test etmek için **Test** ' i seçebilirsiniz.

## <a name="additional-resources"></a>Ek kaynaklar

* [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](../saas-apps/tutorial-list.md)
* [Uygulamalarım güvenli oturum açma tarayıcı uzantısı nedir?](../user-help/my-apps-portal-end-user-access.md)
