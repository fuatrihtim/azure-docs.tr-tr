---
title: 'Öğretici: Templamy SAML2 ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ile Templamy SAML2 arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: jeedes
ms.openlocfilehash: d83bf1b6e986f867a3781b5da7d2784e65815520
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92515832"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-templafy-saml2"></a>Öğretici: Templamy SAML2 ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Templamy SAML2 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Templamy SAML2 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Templamy SAML2 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Templamy SAML2 için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Templamy SAML2 çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Templamy SAML2, **SP** tarafından başlatılan SSO 'yu destekler
* Templamy SAML2 **, tam zamanında** Kullanıcı sağlamayı destekler

## <a name="adding-templafy-saml2-from-the-gallery"></a>Galeriden Templamy SAML2 ekleme

Templamy SAML2 tümleştirmesini Azure AD 'ye göre yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize Templamy SAML2 eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **templamy SAML2** yazın.
1. Sonuçlar panelinden **Templamy SAML2** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-templafy-saml2"></a>Templamy SAML2 için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu TEMPLAMY SAML2 ile yapılandırın ve test edin. SSO 'nun çalışması için, Templamy SAML2 içinde bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Templamy SAML2 ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Templamy SAML2 SSO 'Yu yapılandırın](#configure-templafy-saml2-sso)** .
    * Kullanıcı Azure AD gösterimine bağlı olan Templamy SAML2 içinde B. Simon 'ın bir karşılığı olacak şekilde **[TEMPLAMY SAML2 test kullanıcısı oluşturun](#create-templafy-saml2-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **templamy SAML2** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<CLIENTSUBDOMAIN>.templafy.com`

    > [!NOTE]
    > Değer gerçek değil. Değeri gerçek Sign-On URL 'siyle güncelleştirin. Değeri almak için [Templamy SAML2 istemci destek ekibine](mailto:support@templafy.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. Templamy SAML2 uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. Yukarıdaki ' a ek olarak, Templamy SAML2 uygulaması, daha fazla özniteliğin aşağıda gösterilen SAML yanıtına geri geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.

    | Name | Kaynak özniteliği| Ad Alanı  |
    | ---------------| --------------- | --------- |
    | givenname | Kullanıcı. | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | surname | User. soyadı | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | EmailAddress | Kullanıcı. Mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`
    | StreetAddress | User. StreetAddress | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | city | Kullanıcı. City | `http://schemas.templafy.com/2016/06/identity/claims`|
    | PostalCode | Kullanıcı. PostaKodu | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | Eyalet veya il | Kullanıcı. durum | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | ülke | Kullanıcı. ülke | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | JobTitle | User. JobTitle | `http://schemas.templafy.com/2016/06/identity/claims`|
    | bölüm | User. Departmanı | `http://schemas.templafy.com/2016/06/identity/claims`|
    | PhoneNumber | Kullanıcı. telephoneNumber | `http://schemas.templafy.com/2016/06/identity/claims` |
    | facsimilenumber | User. facsimileTelephoneNumber | `http://schemas.templafy.com/2016/06/identity/claims`|
    | 'le | User. UserPrincipalName | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | nameidentifier | Kullanıcı. Mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | | |

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak ve bilgisayarınıza kaydetmek için Kopyala düğmesine tıklayın.

    ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)

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

Bu bölümde, Templamy SAML2 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Templamy SAML2** öğesini seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-templafy-saml2-sso"></a>Templamy SAML2 SSO yapılandırma

**TEMPLAMY SAML2** Side çoklu oturum açmayı yapılandırmak Için, **uygulama Federasyon meta veri URL 'SINI** [templamy SAML2 support ekibine](mailto:support@templafy.com)göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-templafy-saml2-test-user"></a>Templamy SAML2 test kullanıcısı oluşturma

Bu bölümde, Templamy SAML2 içinde B. Simon adlı bir Kullanıcı oluşturulur. Templamy SAML2, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Kullanıcı Templamy SAML2 içinde zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Templamy SAML2 kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Templamy SAML2 ' de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile Templamy SAML2 'i deneyin](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security oturum denetimi nedir?](/cloud-app-security/proxy-intro-aad)