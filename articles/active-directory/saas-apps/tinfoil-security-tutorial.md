---
title: 'Öğretici: TINFOIL SECURITY ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ile TINFOIL SECURITY arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.openlocfilehash: 5c2ac2c7bb1b60c87075a1bc62241edab2fc310e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92516309"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tinfoil-security"></a>Öğretici: TINFOIL SECURITY ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, TINFOIL SECURITY 'yi Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. TINFOIL SECURITY 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de TINFOIL SECURITY erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla GÜVENLIK altına almak için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* TINFOIL SECURITY çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* TINFOIL SECURITY, **IDP** tarafından başlatılan SSO 'yu destekler

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="adding-tinfoil-security-from-the-gallery"></a>Galeriden TINFOIL SECURITY ekleme

TINFOIL SECURITY 'nin Azure AD ile tümleştirilmesini yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize TINFOIL SECURITY eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **TINFOIL SECURITY** yazın.
1. Sonuçlar panelinden **TINFOIL SECURITY** ' yi seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tinfoil-security"></a>Azure AD çoklu oturum açmayı, TINFOIL SECURITY için yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'YU TINFOIL SECURITY ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı ile TINFOIL SECURITY arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu TINFOIL SECURITY ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[TINFOIL SECURITY SSO 'Yu yapılandırın](#configure-tinfoil-security-sso)** .
    * Kullanıcının Azure AD gösterimine bağlı olan TINFOIL SECURITY 'de B. Simon 'ın bir karşılığı olacak şekilde **[TINFOIL Security test kullanıcısı oluşturun](#create-tinfoil-security-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **TINFOIL SECURITY** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, uygulama önceden yapılandırılmıştır ve gerekli URL 'ler Azure ile önceden doldurulmuştur. Kullanıcının **Kaydet** düğmesine tıklayarak yapılandırmayı kaydetmesi gerekir.

1. Visitly uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. Visitly uygulaması, yukarıdakine ek olarak, aşağıda gösterilen SAML yanıtına daha fazla öznitelik geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.

    | Name | Kaynak özniteliği |
    | ------------------- | -------------|
    | accountid | UıXXXXXXXXXXXXX |

    > [!NOTE]
    > Öğreticide daha sonra açıklanacak AccountID değerini alacaksınız.

1. **SAML Imzalama sertifikası** bölümünde, **SAML imzalama sertifikası** Iletişim kutusunu açmak için **Düzenle** düğmesine tıklayın.

    ![SAML Imzalama sertifikasını Düzenle](common/edit-certificate.png)

1. **SAML Imzalama sertifikası** bölümünde, **parmak izi değerini** kopyalayın ve bilgisayarınıza kaydedin.

    ![Parmak Izi değerini Kopyala](common/copy-thumbprint.png)

1. **TINFOIL SECURITY 'Yi ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak TINFOIL SECURITY 'ye erişim izni vererek B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **TINFOIL SECURITY**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-tinfoil-security-sso"></a>TINFOIL SECURITY SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, TINFOIL SECURITY şirket sitenizde yönetici olarak oturum açın.

1. Üstteki araç çubuğunda **Hesabım**' ı tıklatın.

    ![Pano](./media/tinfoil-security-tutorial/ic798971.png "Pano")

1. **Güvenlik**' e tıklayın.

    ![Güvenlik](./media/tinfoil-security-tutorial/ic798972.png "Güvenlik")

1. **Çoklu oturum açma** yapılandırması sayfasında, aşağıdaki adımları uygulayın:

    ![Çoklu oturum açma](./media/tinfoil-security-tutorial/ic798973.png "Çoklu Oturum Açma")

    a. **SAML etkinleştir**' i seçin.

    b. **El Ile yapılandırma**' ya tıklayın.

    c. **SAML Post URL** metin kutusuna kopyaladığınız **oturum açma URL 'si** değerini yapıştırın Azure Portal

    d. **SAML sertifikası parmak izi** metin kutusunda, **SAML imzalama sertifikası** bölümünden kopyaladığınız **parmak izine** ait değeri yapıştırın.
  
    e. **Hesap kimliği** değerini kopyalayın ve Azure Portal Içindeki **Kullanıcı öznitelikleri & talepler** bölümünde bulunan **kaynak öznitelik** metin kutusuna değeri yapıştırın.

    f. **Kaydet**’e tıklayın.

### <a name="create-tinfoil-security-test-user"></a>TINFOIL SECURITY test kullanıcısı oluşturma

Azure AD kullanıcılarının TINFOIL SECURITY 'de oturum açmasını sağlamak için, bu kullanıcıların TINFOIL SECURITY 'de sağlanması gerekir. TINFOIL SECURITY, sağlama durumunda el ile gerçekleştirilen bir görevdir.

**Sağlanan bir kullanıcıyı almak için aşağıdaki adımları gerçekleştirin:**

1. Kullanıcı bir Kurumsal hesabın parçasıysa, Kullanıcı hesabının oluşturulmasını sağlamak için [TINFOIL Security destek ekibine başvurmanız](https://www.tinfoilsecurity.com/contact) gerekir.

1. Kullanıcı normal bir TINFOIL SECURITY SaaS kullanıcısı ise Kullanıcı, kullanıcının sitelerine bir ortak çalışan ekleyebilir. Bu, yeni bir TINFOIL SECURITY Kullanıcı hesabı oluşturmak için belirtilen e-postaya davetiye gönderme işlemini tetikler.

> [!NOTE]
> Azure AD Kullanıcı hesapları sağlamak için TINFOIL SECURITY tarafından sunulan herhangi bir diğer TINFOIL SECURITY Kullanıcı hesabı oluşturma aracını veya API 'Leri kullanabilirsiniz.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde TINFOIL SECURITY kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız TINFOIL SECURITY 'de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile TINFOIL SECURITY 'yi deneyin](https://aad.portal.azure.com/)