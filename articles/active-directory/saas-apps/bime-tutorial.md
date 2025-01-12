---
title: 'Öğretici: Bime ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Bime arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.openlocfilehash: 3bbd18bc7851d4ccffca4f721f6e2aef45ff3c3d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97673737"
---
# <a name="tutorial-azure-active-directory-integration-with-bime"></a>Öğretici: Bime ile tümleştirme Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile bir Bime tümleştirmeyi öğreneceksiniz.
Bime 'yi Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de, Bıme 'ye erişimi olan denetimi yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak (çoklu oturum açma) oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini bir Bime ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Bime çoklu oturum açma etkin abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Bıme, **SP** tarafından başlatılan SSO 'yu destekliyor

## <a name="adding-bime-from-the-gallery"></a>Galeriden Kaynakça ekleme

Bime 'nin Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize bir kaynakça eklemeniz gerekir.

**Galeriden bir Kaynakça eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Bime** yazın, sonuç panelinden **Bime** ' yi seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuçlar listesinde Bime](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına bağlı olarak, Bime ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ile ilişkili kullanıcı arasındaki bir bağlantı ilişkisinin oluşturulması gerekir.

Azure AD çoklu oturum açma 'yı çift ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Çoklu **[oturum açmayı yapılandırma](#configure-bime-single-sign-on)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. **[Bime test kullanıcısı oluşturun](#create-bime-test-user)** -Kullanıcı IÇIN Azure AD gösterimine bağlı olan, Bime 'de Britta Simon 'un bir karşılığı olacak.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açma 'yı Bime ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Bime** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Bime etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<tenant-name>.Bimeapp.com`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<tenant-name>.Bimeapp.com`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [Bime istemci destek ekibine](https://bime.zendesk.com/hc/categories/202604307-Support-tech-notes-and-tips-) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Imzalama sertifikası** bölümünde, **SAML imzalama sertifikası** Iletişim kutusunu açmak için **Düzenle** düğmesine tıklayın.

    ![SAML Imzalama sertifikasını Düzenle](common/edit-certificate.png)

6. **SAML Imzalama sertifikası** bölümünde, **parmak izini** kopyalayın ve bilgisayarınıza kaydedin.

    ![Parmak Izi değerini Kopyala](common/copy-thumbprint.png)

7. **Bime 'Yi ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-bime-single-sign-on"></a>Bime tek Sign-On yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, yönetici olarak Bime şirket sitenizde oturum açın.

2. Araç çubuğunda **yönetici**' ye ve ardından **Hesap**' a tıklayın.

    ![Ekran görüntüsünde seçili yönetici öğesi ve hesap seçili görüntülenir.](./media/bime-tutorial/ic775558.png "Yönetici")

3. Hesap yapılandırması sayfasında, aşağıdaki adımları uygulayın:

    ![Çoklu oturum açmayı yapılandırma](./media/bime-tutorial/ic775559.png "Tek Sign-On yapılandırma")

    a. **SAML kimlik doğrulamasını etkinleştir**' i seçin.

    b. **Uzaktan oturum açma URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. **Sertifika parmak izi** metin kutusunda, Azure Portal kopyaladığınız **parmak izi** değerini yapıştırın.

    d. **Kaydet**’e tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon** girin.

    b. **Kullanıcı adı** alanına **\@ bricompansıon yourcompanydomain. Extension** yazın  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, BIG 'ye erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Bime**' yi seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Bime**' yi seçin.

    ![Uygulamalar listesindeki Bime bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-bime-test-user"></a>Bime test kullanıcısı oluştur

Azure AD kullanıcılarının, Bıme 'de oturum açmasını sağlamak için, bu kullanıcıların da Iki kez sağlanması gerekir. Bime durumunda, sağlama el ile gerçekleştirilen bir görevdir.

**Kullanıcı sağlamayı yapılandırmak için aşağıdaki adımları uygulayın:**

1. **Bime** kiracınızda oturum açın.

2. Araç çubuğunda **yönetici**' ye ve ardından **Kullanıcılar**' a tıklayın.

    ![Ekran görüntüsünde seçili olan yönetici öğesi ve kullanıcıların seçtiği görüntülenir.](./media/bime-tutorial/ic775561.png "Yönetici")

3. **Kullanıcılar listesinde**, **Yeni Kullanıcı Ekle** ' ye ("+") tıklayın.

    ![Kullanıcılar](./media/bime-tutorial/ic775562.png "Kullanıcılar")

4. **Kullanıcı ayrıntıları** iletişim sayfasında, aşağıdaki adımları uygulayın:

    ![Kullanıcı ayrıntıları](./media/bime-tutorial/ic775563.png "Kullanıcı ayrıntıları")

    a. **Ad** metin kutusuna, ilk Kullanıcı adını **Britta** gibi girin.

    b. **Soyadı** metin kutusunda, **Simon** gibi kullanıcı adının soyadını girin.

    c. **E-posta** metin kutusuna, **brittasıon \@ contoso.com** gibi kullanıcının e-postasını girin.

    d. **Kaydet**’e tıklayın.

> [!NOTE]
> Azure AD Kullanıcı hesapları sağlamak için, bir diğer Kullanıcı hesabı oluşturma araçlarını veya Bime tarafından sunulan API 'Leri kullanabilirsiniz.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Bime kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Iki adımda de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)