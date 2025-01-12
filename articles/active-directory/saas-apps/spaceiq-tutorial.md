---
title: 'Öğretici: SpaceIQ ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve SpaceIQ arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 74c3c4d1a9192256c36d41db7a4f9406b6c966f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92514733"
---
# <a name="tutorial-azure-active-directory-integration-with-spaceiq"></a>Öğretici: SpaceIQ ile tümleştirme Azure Active Directory

Bu öğreticide, SpaceIQ 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
SpaceIQ 'ı Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de SpaceIQ erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla SpaceIQ (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini SpaceIQ ile yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* SpaceIQ çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* SpaceIQ, **IDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-spaceiq-from-the-gallery"></a>Galeriden SpaceIQ ekleme

SpaceIQ 'ın Azure AD 'ye tümleştirmesini yapılandırmak için, Galeriden SpaceIQ ' ı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden SpaceIQ eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **spaceiq** yazın, sonuç panelinden **spaceiq** ' ı seçin, sonra da uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

    ![Sonuç listesinde SpaceIQ](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre SpaceIQ ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve SpaceIQ içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin oluşturulması gerekir.

Azure AD çoklu oturum açmayı, SpaceIQ ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Bir uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[SpaceIQ çoklu oturum açmayı yapılandırın](#configure-spaceiq-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. User 'ın Azure AD gösterimine bağlı olan SpaceIQ 'da Britta Simon 'un bir karşılığı olacak şekilde **[spaceiq test kullanıcısı oluşturun](#create-spaceiq-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı SpaceIQ ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **spaceiq** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **SAML Ile tek Sign-On ayarlama** sayfasında, aşağıdaki adımları gerçekleştirin:

    ![SpaceIQ etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/idp-intiated.png)

    a. **Tanımlayıcı** metin kutusuna URL 'yi yazın:`https://api.spaceiq.com`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://api.spaceiq.com/saml/<instanceid>/callback`

    > [!NOTE]
    > Bu değerleri, Öğreticinin ilerleyen kısımlarında açıklanan gerçek yanıt URL 'SI ve tanımlayıcısı ile güncelleştirin.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **SpaceIQ ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-spaceiq-single-sign-on"></a>Spaceq Single Sign-On yapılandırma

1. Yeni bir tarayıcı penceresi açın ve ardından SpaceIQ ortamınızda yönetici olarak oturum açın.

1. Oturum açtıktan sonra sağ üstteki bulmaca işaretine tıklayın ve ardından **tümleştirmelere** tıklayın

    ![Hesap ayarları](./media/spaceiq-tutorial/setting1.png) 

1. **Tüm sağlama & SSO** altında, Azure kutucuğuna TıKLAYARAK IDP olarak **bir Azure örneği** ekleyin.

    ![SAML simgesi](./media/spaceiq-tutorial/setting2.png)

1. **SSO** iletişim kutusunda aşağıdaki adımları gerçekleştirin:

    ![SAML kimlik doğrulama ayarları](./media/spaceiq-tutorial/setting3.png)

    a. **SAML veren URL 'si** kutusunda, Azure AD uygulama yapılandırma penceresinden KOPYALANMıŞ **Azure AD tanımlayıcı** değerini yapıştırın.

    b. **SAML geri çağırma uç noktası URL 'si (salt okunurdur)** değerini kopyalayın ve değeri Azure Portal **temel SAML yapılandırması** bölümündeki **yanıt URL 'si** kutusuna yapıştırın.

    c. **SAML hedef KITLESI URI (salt okunurdur)** değerini kopyalayın ve değeri Azure Portal **temel SAML yapılandırması** bölümündeki **tanımlayıcı** kutusuna yapıştırın.

    d. İndirilen sertifika dosyasını Not defteri 'nde açın, içeriği kopyalayın ve **X. 509.440 sertifikası** kutusuna yapıştırın.

    e. **Kaydet**’e tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon** girin.
  
    b. **Kullanıcı adı** alan türü`brittasimon@yourcompanydomain.extension`  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, SpaceIQ 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **spaceiq**' u seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Spaceiq**' ı seçin.

    ![Uygulamalar listesindeki SpaceIQ bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-spaceiq-test-user"></a>SpaceIQ test kullanıcısı oluşturma

Bu bölümde, SpaceIQ 'da Britta Simon adlı bir Kullanıcı oluşturacaksınız. SpaceIQ platformunda kullanıcıları eklemek için [çalışmaspaceiq destek ekibi](mailto:eng@spaceiq.com) . Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde SpaceIQ kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız SpaceIQ öğesinde otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)