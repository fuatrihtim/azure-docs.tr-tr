---
title: 'Öğretici Azure Active Directory: AppNeta performans Izleyicisi ile çoklu oturum açma (SSO) Tümleştirmesi | Microsoft Docs'
description: Azure Active Directory ve AppNeta performans Izleyicisi arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: b2558b4b3bcd60acba3bf47d4a973a2b6de7424f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736014"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-appneta-performance-monitor"></a>Öğretici: AppNeta performans Izleyicisi ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, AppNeta performans Izleyicisini Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. AppNeta performans Izleyicisini Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de AppNeta performans Izleyicisi 'ne erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla AppNeta performans Izleyicisine otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.


## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* AppNeta performans Izleyicisi çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* AppNeta performans Izleyicisi **SP** tarafından başlatılan SSO 'yu destekler

* AppNeta performans Izleyicisi **tam zamanında** Kullanıcı sağlamayı destekliyor

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.


## <a name="adding-appneta-performance-monitor-from-the-gallery"></a>Galeriden AppNeta performans Izleyicisi ekleme

AppNeta performans Izleyicisi 'ni Azure AD 'ye tümleştirmeyi yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize AppNeta performans Izleyicisi eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Appneta performans izleyicisi** yazın.
1. Sonuçlar panelinden **Appneta performans izleyicisi** ' ni seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-appneta-performance-monitor"></a>AppNeta performans Izleyicisi için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu AppNeta performans izleyicisinde yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve AppNeta performans Izleyicisinde ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu AppNeta performans Izleyicisi ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[AppNeta performans IZLEYICISI SSO 'Yu yapılandırma](#configure-appneta-performance-monitor-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. User 'ın Azure AD gösterimine bağlı olan AppNeta performans Izleyicisinde B. Simon 'a sahip olmak için **[Appneta performans izleyicisi test kullanıcısı oluşturun](#create-appneta-performance-monitor-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Appneta performans izleyicisi** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<subdomain>.pm.appneta.com`

    > [!NOTE]
    > Oturum açma URL 'SI değeri gerçek değil. Bu değeri gerçek Sign-On URL 'siyle güncelleştirin. Bu değeri almak için [Appneta performans Izleyicisi istemci destek ekibine](mailto:support@appneta.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. AppNeta performans Izleyicisi uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/edit-attribute.png)

1. Yukarıdaki ' a ek olarak, AppNeta performans Izleyicisi uygulaması, daha az sayıda özniteliğin aşağıda gösterilen SAML yanıtına geri geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksiniminize göre bunları gözden geçirebilirsiniz.

    | Name | Kaynak özniteliği|
    | --------| ----------------|
    | firstName| Kullanıcı.|
    | lastName| User. soyadı|
    | e-posta| User. UserPrincipalName|
    | name| User. UserPrincipalName|
    | gruplar  | Kullanıcı. atandroles |
    | telefon| Kullanıcı. telephoneNumber |
    | başlık| User. JobTitle|
    | | |

    > [!NOTE]
    > **gruplar** , Azure AD 'Deki bir **rolle** eşlenen appneta içindeki güvenlik grubunu ifade eder. Lütfen Azure AD 'de özel roller oluşturmayı açıklayan [Bu](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) belgeye başvurun.

    1. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **yeni talep Ekle** ' ye tıklayın.

    1. **Ad** metin kutusuna, bu satır için gösterilen öznitelik adını yazın.

    1. **Ad alanını** boş bırakın.

    1. **Öznitelik** olarak kaynak seçin.

    1. **Kaynak özniteliği** listesinde, bu satır için gösterilen öznitelik değerini yazın.

    1. **Tamam 'a** tıklayın

    1. **Kaydet**’e tıklayın.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **AppNeta performans Izleyicisini ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, AppNeta performans Izleyicisi 'ne erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Appneta performans izleyicisi**' ni seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Rolleri yukarıda açıklanan şekilde ayarlarsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.
## <a name="configure-appneta-performance-monitor-sso"></a>AppNeta performans Izleyicisi SSO 'SU yapılandırma

**Appneta performans izleyicisi** tarafında çoklu oturum açmayı yapılandırmak için, Indirilen **Federasyon meta veri XML** 'Sini ve uygun kopyalanmış URL 'Leri Azure Portal ' den [appneta performans izleyicisi destek ekibine](mailto:support@appneta.com)göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-appneta-performance-monitor-test-user"></a>AppNeta performans Izleyicisi test kullanıcısı oluştur

Bu bölümde, AppNeta performans Izleyicisinde Britta Simon adlı bir Kullanıcı oluşturulur. AppNeta performans Izleyicisi, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Kullanıcı AppNeta performans Izleyicisinde zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

> [!Note]
> El ile bir kullanıcı oluşturmanız gerekiyorsa, [Appneta performans izleyicisi destek ekibine](mailto:support@appneta.com)başvurun.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz AppNeta performans Izleyicisi oturum açma URL 'sine yeniden yönlendirilir. 

* AppNeta performans Izleyicisi oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki AppNeta performans Izleyicisi kutucuğuna tıkladığınızda bu işlem AppNeta performans Izleyicisi oturum açma URL 'sine yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

AppNeta performans Izleyicisini yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).
