---
title: 'Öğretici: LiquidFiles ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve LiquidFiles arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: b9ca860043d5c4a492908aa45bf0a89ab368d50f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92458481"
---
# <a name="tutorial-azure-active-directory-integration-with-liquidfiles"></a>Öğretici: LiquidFiles ile tümleştirme Azure Active Directory

Bu öğreticide, LiquidFiles 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
LiquidFiles Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* LiquidFiles 'e erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla LiquidFiles (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini LiquidFiles ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* LiquidFiles çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* LiquidFiles **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-liquidfiles-from-the-gallery"></a>Galeriden LiquidFiles ekleme

LiquidFiles tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden LiquidFiles yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

**Galeriden LiquidFiles eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **LiquidFiles** yazın, sonuç panelinden **LiquidFiles** ' yi seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

    ![Sonuç listesinde LiquidFiles](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre LiquidFiles ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve LiquidFiles 'deki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı LiquidFiles ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[LiquidFiles çoklu oturum açmayı yapılandırın](#configure-liquidfiles-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan LiquidFiles 'de Britta Simon 'ın bir karşılığı olacak şekilde **[LiquidFiles test kullanıcısı oluşturun](#create-liquidfiles-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı LiquidFiles ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **LiquidFiles** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![LiquidFiles etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier-reply.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<YOUR_SERVER_URL>/saml/init`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<YOUR_SERVER_URL>`

    c. **Yanıt URL** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<YOUR_SERVER_URL>/saml/consume`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [LiquidFiles istemci destek ekibine](https://www.liquidfiles.com/support.html) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Imzalama sertifikası** bölümünde, **SAML imzalama sertifikası** Iletişim kutusunu açmak için **Düzenle** düğmesine tıklayın.

    ![SAML Imzalama sertifikasını Düzenle](common/edit-certificate.png)

6. **SAML Imzalama sertifikası** bölümünde, **parmak izini** kopyalayın ve bilgisayarınıza kaydedin.

    ![Parmak Izi değerini Kopyala](common/copy-thumbprint.png)

7. **LiquidFiles ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-liquidfiles-single-sign-on"></a>LiquidFiles Single Sign-On yapılandırma

1. LiquidFiles şirket sitenizde yönetici olarak oturum açın.

1. Menüdeki **yönetici > yapılandırmasındaki** **Çoklu oturum açma** ' ya tıklayın.

1. **Tek Sign-On yapılandırma** sayfasında, aşağıdaki adımları gerçekleştirin

    ![Tek Sign-On yapılandırma](./media/liquidfiles-tutorial/tutorial_single_01.png)

    a. **Çoklu oturum açma yöntemi** olarak **SAML 2**' yi seçin.

    b. **IDP oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. **IDP oturum kapatma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum kapatma URL 'si** değerini yapıştırın.

    d. **IDP CERT parmak izi** metin kutusunda, Azure Portal kopyaladığınız **parmak izi** değerini yapıştırın.

    e. Ad tanımlayıcı biçimi metin kutusuna değeri yazın `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` .

    f. AuthN bağlam metin kutusuna değeri yazın `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport` .

    örneğin: **Kaydet**’e tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon** girin.
  
    b. **Kullanıcı adı** alanına yazın `brittasimon@yourcompanydomain.extension` . Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, LiquidFiles 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon özelliğini etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **LiquidFiles**' yi seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **LiquidFiles**' yi seçin.

    ![Uygulamalar listesindeki LiquidFiles bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-liquidfiles-test-user"></a>LiquidFiles test kullanıcısı oluştur

Bu bölümün amacı, LiquidFiles ' de Britta Simon adlı bir Kullanıcı oluşturmaktır. LiquidFiles uygulamanızda oturum açmadan önce bir kullanıcı olarak eklenmeleri için LiquidFiles sunucu yöneticinizle birlikte çalışın.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde LiquidFiles kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız LiquidFiles için otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)