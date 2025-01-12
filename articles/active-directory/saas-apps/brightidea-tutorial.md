---
title: 'Öğretici: Parlatıdea ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Parlatıa arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/23/2019
ms.author: jeedes
ms.openlocfilehash: 9967f349011b52a2218681956885c33456ba1d46
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97672772"
---
# <a name="tutorial-azure-active-directory-integration-with-brightidea"></a>Öğretici: Parlatıdea ile tümleştirme Azure Active Directory

Bu öğreticide, en parlak Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz.
Parlatıdea 'nın Azure AD ile tümleştirilmesi aşağıdaki avantajları sağlar:

* Azure AD 'de, Parlatıdea 'ya erişimi olan bir denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla en fazla (çoklu oturum açma) en fazla yük açmak için otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini en parlak bir şekilde yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Çoklu oturum açma özellikli bir aboneliğin parlatıçıkarılması

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.


* Parlatıdea **SP ve ıDP** tarafından başlatılan SSO 'yu destekler
* Parlatıdea **, tam zamanında** Kullanıcı sağlamasını destekler


## <a name="adding-brightidea-from-the-gallery"></a>Galeriden Parlakölülebilirlik ekleme

Parlatıdea 'nın Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize en parlak bir katman eklemeniz gerekir.

**Galeriden Parlatıdea eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **parlatıdea** yazın, sonuç panelinden **parlatıdea** ' yı seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

    ![Sonuç listesinde parlatıdea](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre en parlak şekilde yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve ilgili Kullanıcı arasındaki bir bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açma 'yı en Parlala yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. En **[parlak çoklu oturum açmayı yapılandırma](#configure-brightidea-single-sign-on)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan parlak bir test kullanıcısı oluşturmak için en parlaya bir **[test kullanıcısı oluşturun](#create-brightidea-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açma 'yı en parlak şekilde yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), en **parlak** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, **hizmet sağlayıcısı meta verileri dosyanız** varsa ve **IDP** başlatılmadı modunda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    a. **Meta veri dosyasını karşıya yükle**' ye tıklayın.

    ![Meta veri dosyasını karşıya yükle](common/upload-metadata.png)

    b. Meta veri dosyasını seçmek için **klasör logosu** ' na tıklayın ve **karşıya yükle**' ye tıklayın.

    ![meta veri dosyası seçin](common/browse-upload-metadata.png)

    c. Meta veri dosyası başarıyla karşıya yüklendikten sonra **tanımlayıcı** ve **yanıt URL** değerleri, en parlak bölüm metin kutusuna otomatik olarak doldurulur:

    ![Ekran görüntüsü; tanımlayıcı girebileceğiniz, yanıt U R L ve Kaydet ' i seçebileceğiniz temel SAML yapılandırmasını gösterir.](common/idp-intiated.png)

    > [!Note]
    > **Tanımlayıcı** ve **yanıt URL 'si** değerleri otomatik olarak alamazsanız, değerleri gereksinimlerinize göre el ile girin.

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    ![Ekran görüntüsü, U R L 'ye bir Işaret girebileceğiniz ek U R 'Leri ayarlamayı gösterir.](common/metadata-upload-additional-signon.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<SUBDOMAIN>.brightidea.com`

4. **SAML Ile tek Sign-On ayarla** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **Parlaklık ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-brightidea-single-sign-on"></a>En parlak tek Sign-On yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, yönetici kimlik bilgilerini kullanarak en çok bir yönetim için oturum açın.

2. En parlak sisteminizdeki SSO özelliğine ulaşmak için **Kurumsal kurulum**  ->  **kimlik doğrulaması sekmesine** gidin. İki alt sekme görürsünüz: kimlik doğrulama seçimi SAML profilleri &.

    ![Ekran görüntüsü, kimlik doğrulama sekmesi seçili olan en parlak siteyi gösterir.](./media/brightidea-tutorial/configure1.png)

3. **Kimlik doğrulama seçimini** seçin. Varsayılan olarak, yalnızca iki standart yöntemi gösterir: bir oturum açma & kaydı. Bir SSO yöntemi eklendiğinde, listede görünür.

    ![Ekran görüntüsü, kimlik doğrulama seçimi seçiliyken en parlak kimlik doğrulaması sekmesini gösterir.](./media/brightidea-tutorial/configure2.png)

4. **SAML profilleri** ' ni seçin ve aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsünde, meta verileri Indirme ve yeni ekleme seçenekleri sunan SAML profilleri seçiliyken en parlak kimlik doğrulama sekmesi gösterilmektedir.](./media/brightidea-tutorial/configure3.png)

    a. Azure portal **meta verileri indir** ve **temel SAML yapılandırmasına** yükle bölümüne tıklayın.

    b. **Kimlik sağlayıcısı ayarı** altındaki **Yeni Ekle** düğmesine tıklayın ve aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, bilgi girebileceğiniz en parlak kimlik sağlayıcısı ayarını gösterir.](./media/brightidea-tutorial/configure4.png)

   * **SAML profili adını** girin, örn.`Azure Ad SSO`

   * **Karşıya yükleme meta verileri** için Dosya Seç ' e tıklayın ve indirilen meta veri dosyasını Azure Portal yükleyin.

     > [!NOTE]
     > Meta veri dosyasını karşıya yükledikten sonra, kalan alanlar **Çoklu oturum açma hizmeti, kimlik sağlayıcısı veren, yükleme ortak anahtarı** otomatik olarak doldurulur.

   * **E-posta** metin kutusuna değerini olarak girin `mail` .

   * **Ekran adı** metin kutusuna değerini olarak girin `givenName` .

   * **Değişiklikleri Kaydet**’e tıklayın.  

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma 

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon** yazın.

    b. **Kullanıcı adı** alanına **brittasıon \@ yourcompanydomain. Extension** yazın  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, parlak bir erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **parlatıdea**' yı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Parlatıdea**' yı seçin.

    ![Uygulamalar listesindeki en parlak bağlantı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinden **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-brightidea-test-user"></a>En parlak test kullanıcısı oluşturma

Bu bölümde, Britta Simon adlı bir Kullanıcı Parlatıdea 'da oluşturulur. Parlatıdea, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir kullanıcı zaten parlak bir şekilde yoksa, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde en parlağa tıkladığınızda, SSO 'yu ayarladığınız en Parlakta otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)