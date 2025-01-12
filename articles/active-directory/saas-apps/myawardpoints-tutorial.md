---
title: 'Öğretici: ödül noktalarıyla üst alt/üst ekiple tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Ikramiye noktalarım üst alt/üst ekibi arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: f2328bd51712089f706c8491007f9f51eba52337
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92508137"
---
# <a name="tutorial-azure-active-directory-integration-with-my-award-points-top-subtop-team"></a>Öğretici: ödül noktalarıyla üst alt/üst ekiple tümleştirme Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile ödül noktalarımı En Iyi şekilde tümleştirme hakkında bilgi edineceksiniz.
Azure AD ile ödül noktalarımı en üst alt/üst ekiplerimi tümleştirerek aşağıdaki avantajlar sunulmaktadır:

* Azure AD 'de, Ikramiye noktalarıma En Iyi alt/üst ekibe erişimi olan bir denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla, ödül noktalarım için en yüksek alt/üst ekiple (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini, Ikramiye noktalarım üst alt/üst ekibiyle yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Ikramiye noktalarım üst alt/üst takım çoklu oturum açma özellikli abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Ikramiye noktalarım üst alt/üst ekip **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-my-award-points-top-subtop-team-from-the-gallery"></a>Galeriden ödül noktaları üst alt/üst ekibini ekleme

Ikramiye noktalarımın en üst alt/üst ekibinin tümleştirmesini Azure AD 'ye göre yapılandırmak için, kendi Ikramiye noktalarımı galerisinden yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

**Kendi Ikramiye noktalarımı en üst alt/üst takımdan Galeriye eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna, **Ikramiye noktalarımı en üst alt/üst takımdan** yazın, sonuç panelinden **Ikramiye noktalarımı en üst alt/üst ekip** ' i seçin, sonra da uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

     ![Sonuç listesinde Ikramiye noktalarım üst alt/üst ekip](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, **Britta Simon** adlı bir test kullanıcısına dayalı olarak, Ikramiye noktalarım Ile Azure AD çoklu oturum açma 'yı yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve Ikramiye noktamdaki ilgili Kullanıcı arasındaki bir bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı, Ikramiye noktalarım en üst alt/üst ekiple yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Tek Sign-On ayarlarını uygulama tarafında yapılandırmak için, **Ikramiye noktalarımı üst alt/üst takım çoklu oturum açmayı yapılandırın** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. **İkramiye noktalarımı en üst alt/üst ekip test kullanıcısına** , kullanıcının Azure AD gösterimine bağlı olan Ikramiye noktamda en üst alt/üst ekipte Britta Simon 'ın bir karşılığı olacak şekilde oluşturun.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açma 'yı ödül noktalarım üst alt/üst ekibinden yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Ikramiye noktalarım üst alt/üst ekip** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Ikramiye noktalarım üst alt/üst ekip etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-signonurl.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://microsoftrr.performnet.com/biwv1auth/Shibboleth.sso/Login?providerId=<Azure AD Identifier>`

    > [!NOTE]
    > Değer gerçek değil. `<Azure AD Identifier>`Bu öğreticide sonraki adımlarda değeri alacaksınız.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **Ikramiye noktalarımı ayarla üst alt/üst ekip** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın. 

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

    >[!NOTE]
    >Kopyalanmış Azure AD tanımlayıcı değerini, `<Azure AD Identifier>` Azure Portal **temel SAML yapılandırması** bölümünde bulunan ' ın yerine oturum açma URL 'si ile ekleyin.

### <a name="configure-my-award-points-top-subtop-team-single-sign-on"></a>Ikramiye noktalarımı en üst alt/üst takım tek Sign-On yapılandırın

**Ikramiye noktamda en üst alt/üst ekip** tarafında çoklu oturum açmayı yapılandırmak için, Indirilen **Federasyon meta veri XML** 'Sini ve uygun kopyalanmış URL 'Leri Azure Portal ' den [ödül noktaları üst alt/üst ekip destek ekibine](mailto:myawardpoints@biworldwide.com)göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

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

Bu bölümde, ödül noktalarıma en üst alt/üst ekibe erişim vererek Britta Simon 'u Azure çoklu oturum açma özelliğini kullanacak şekilde etkinleştirirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **ödül noktaları üst alt/üst ekip**' i seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Ikramiye noktalarımı üst alt/üst ekip**' i seçin.

    ![Uygulama listesindeki Ikramiye noktaları üst alt/üst ekip bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-my-award-points-top-subtop-team-test-user"></a>Ikramiye noktalarımı üst alt/üst ekip test kullanıcısı oluştur

Bu bölümde, ödül noktalarım üst alt/üst ekibimde Britta Simon adlı bir Kullanıcı oluşturacaksınız. İkramiye noktalarım en üst alt/üst ekip platformundaki kullanıcıları eklemek için [ödüllü noktalarıma ve en üst düzey takım destek ekibinden](mailto:myawardpoints@biworldwide.com) çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde ödül noktalarım üst alt/üst ekip kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Ikramiye noktam üst tarafında otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)