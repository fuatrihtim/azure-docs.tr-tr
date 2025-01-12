---
title: 'Öğretici: Netüse Kullanıcı kimlik doğrulamasıyla çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Netüse Kullanıcı kimlik doğrulaması arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/01/2019
ms.author: jeedes
ms.openlocfilehash: 1ae9e39f5a23725b17451d7144c8304194d2a1d0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92514376"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-user-authentication"></a>Öğretici: Netüse Kullanıcı kimlik doğrulamasıyla çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Netüse Kullanıcı kimlik doğrulamasını Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Netüse Kullanıcı kimlik doğrulamasını Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Netüse Kullanıcı kimlik doğrulamasına erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Netüse Kullanıcı kimlik doğrulaması için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Netüse Kullanıcı kimlik doğrulaması çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Netüse Kullanıcı kimlik doğrulaması **SP ve ıDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-netskope-user-authentication-from-the-gallery"></a>Galeriden Netüse Kullanıcı kimlik doğrulaması ekleme

Netüse Kullanıcı kimlik doğrulamasının tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize Netüse Kullanıcı kimlik doğrulaması eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, ara kutusuna **Netüse Kullanıcı kimlik doğrulaması** yazın.
1. Sonuçlar panelinden **Netüse Kullanıcı kimlik doğrulaması** ' nı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netskope-user-authentication"></a>Netüse Kullanıcı kimlik doğrulaması için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Netüse Kullanıcı kimlik doğrulamasıyla yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Netüse Kullanıcı kimlik doğrulamasında ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Netüse Kullanıcı kimlik doğrulamasıyla yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Netüse Kullanıcı kimlik doğrulama SSO 'Su yapılandırma](#configure-netskope-user-authentication-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    * Kullanıcının Azure AD gösterimine bağlı olan Netüse Kullanıcı kimlik doğrulamasında B. Simon 'a sahip olmak için **[Netüse Kullanıcı kimlik doğrulaması test kullanıcısı oluşturun](#create-netskope-user-authentication-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Netüse Kullanıcı kimlik doğrulaması** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<tenantname>.goskope.com/<customer entered string>`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<tenantname>.goskope.com/nsauth/saml2/http-post/<customer entered string>`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri öğreticide ilerleyen kısımlarında bulabilirsiniz.

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<tenantname>.goskope.com`

    > [!NOTE]
    > Oturum açma URL 'SI değerleri gerçek değil. Oturum açma URL 'SI değerini, gerçek oturum açma URL 'SI ile güncelleştirin. Oturum açma URL 'SI değerini almak için [Netüse Kullanıcı kimlik doğrulama istemci destek ekibine](mailto:support@netskope.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Netüse Kullanıcı kimlik doğrulamasını ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Netüse Kullanıcı kimlik doğrulamasına erişim vererek, B. Simon 'u Azure çoklu oturum açma özelliğini kullanacak şekilde etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Netüse Kullanıcı kimlik doğrulaması**' nı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-netskope-user-authentication-sso"></a>Netüse Kullanıcı kimlik doğrulama SSO 'SU yapılandırma

1. Tarayıcınızda yeni bir sekme açın ve Netüse Kullanıcı kimlik doğrulaması şirket sitenizde yönetici olarak oturum açın.

1. **Etkin platform** sekmesi ' ne tıklayın.

    ![Ekran görüntüsü ayarlarından seçilen etkin platformu gösterir.](./media/netskope-user-authentication-tutorial/user1.png)

1. **Ara sunucuyu ileri** kaydırarak **SAML**' yi seçin.

    ![Ekran görüntüsü etkin platformdan seçilen SAML 'yi gösterir.](./media/netskope-user-authentication-tutorial/config-saml.png)

1. **SAML ayarları** sayfasında, aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz SAML ayarlarını gösterir.](./media/netskope-user-authentication-tutorial/configure-copyurls.png)

    a. **SAML VARLıK kimliği** değerini kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümünde **tanımlayıcı** metin kutusuna yapıştırın.

    b. **SAML ACS URL 'si** değerini kopyalayın ve Azure Portal **temel SAML YAPıLANDıRMASı** bölümündeki **yanıt URL** metin kutusuna yapıştırın.

1. **Hesap Ekle**' ye tıklayın.

    ![Ekran görüntüsü SAML bölmesinde seçili HESABı Ekle ' ye gösterilir.](./media/netskope-user-authentication-tutorial/config-addaccount.png)

1. **SAML hesabı ekle** sayfasında, aşağıdaki adımları uygulayın:

    ![Ekran görüntüsünde, açıklanan değerleri girebileceğiniz SAML hesabı ekleme gösterilmektedir.](./media/netskope-user-authentication-tutorial/config-settings1.png)

    a. **Ad** metin kutusuna Azure AD gibi bir ad girin.

    b. **IDP URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. **IDP VARLıK kimliği** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.

    d. İndirilen meta veri dosyanızı Not defteri 'nde açın, içeriğini panonuza kopyalayın ve **IDP sertifika** metin kutusuna yapıştırın.

    e. **Kaydet**' e tıklayın.

### <a name="create-netskope-user-authentication-test-user"></a>Netüse Kullanıcı kimlik doğrulaması test kullanıcısı oluşturma

1. Tarayıcınızda yeni bir sekme açın ve Netüse Kullanıcı kimlik doğrulaması şirket sitenizde yönetici olarak oturum açın.

1. Sol gezinti bölmesindeki **Ayarlar** sekmesine tıklayın.

    ![Ekran görüntüsü seçili ayarı gösterir.](./media/netskope-user-authentication-tutorial/config-settings.png)

1. **Etkin platform** sekmesi ' ne tıklayın.

    ![Ekran görüntüsü ayarlarından seçilen etkin platformu gösterir.](./media/netskope-user-authentication-tutorial/user1.png)

1. **Kullanıcılar** sekmesi ' ne tıklayın.

    ![Ekran görüntüsü, etkin platformlardan seçilen kullanıcıları gösterir.](./media/netskope-user-authentication-tutorial/add-user.png)

1. **Kullanıcı Ekle**' ye tıklayın.

    ![Ekran görüntüsü, Kullanıcı Ekle ' yi seçebileceğiniz kullanıcılar iletişim kutusunu gösterir.](./media/netskope-user-authentication-tutorial/user-add.png)

1. Eklemek istediğiniz kullanıcının e-posta adresini girin ve **Ekle**' ye tıklayın.

    ![Ekran görüntüsünde, kullanıcı listesi girebileceğiniz Kullanıcı ekleme gösterilir.](./media/netskope-user-authentication-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Netüse Kullanıcı kimlik doğrulaması kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Netüse Kullanıcı kimlik doğrulamasında otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile Netüse Kullanıcı kimlik doğrulamasını deneyin](https://aad.portal.azure.com/)