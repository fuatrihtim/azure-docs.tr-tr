---
title: 'Öğretici: LinkedIn ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve LinkedIn 'ten çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.openlocfilehash: d4410a39cc9b04565d7b753b7821e11c8ece2593
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92458552"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-elevate"></a>Öğretici: LinkedIn yükseltme ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, LinkedIn yükseltmesine Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. LinkedIn yükseltmeyi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de LinkedIn yükseltmesine erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla LinkedIn 'e otomatik olarak kaydolmalarına imkan tanıyın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* LinkedIn çoklu oturum açma (SSO) özellikli aboneliği yükselt.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.



* LinkedIn yükseltme **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler
* LinkedIn 'in **tam zamanında** Kullanıcı sağlamasını desteklemesi
* LinkedIn yükseltme [ **Otomatik** Kullanıcı sağlamasını destekler](linkedinelevate-provisioning-tutorial.md)

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Galeriden LinkedIn 'i ekleme

LinkedIn 'in Azure AD 'ye tümleştirilmesini yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize LinkedIn 'ten yükseltme eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **LinkedIn Yükselt** yazın.
1. Sonuçlar panelinden **LinkedIn Yükselt** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-elevate"></a>LinkedIn 'in Azure AD çoklu oturum açma bilgilerini yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak LinkedIn Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, LinkedIn 'in bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu LinkedIn ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[LinkedIn yükseltme SSO 'su](#configure-linkedin-elevate-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. LinkedIn 'in Azure AD gösterimine bağlı olan LinkedIn yükselmekte olan bir B. Simon 'ya sahip olmak için **[LinkedIn yükseltme testi kullanıcısı oluşturun](#create-linkedin-elevate-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **LinkedIn** Uygulama tümleştirmesini Yükselt sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusuna **varlık kimliği** değerini girin, bu öğreticide daha sonra AÇıKLANAN LinkedIn portalından varlık kimliği değerini kopyalayacaksınız.

    b. **Yanıt URL** 'si metin kutusuna, **onaylama TÜKETICI erişimi (ACS) URL 'si** değerini girin, bu öğreticide daha sonra açıklanan LinkedIn portalından, onaylama tüketici erişimi (ACS) URL 'si değerini kopyalayacaksınız.

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>`

1. LinkedIn 'i uygulama, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde, **NameIdentifier** 'ın **User. UserPrincipalName** ile eşlendiği varsayılan özniteliklerin listesi gösterilmektedir. LinkedIn yükseltme uygulaması, NameIdentifier 'ın **User. Mail** ile eşlenmesini bekler, bu nedenle, Düzenle simgesine tıklayarak ve öznitelik eşlemesini değiştirerek öznitelik eşlemesini düzenlemeniz gerekir.

    ![image](common/edit-attribute.png)

1. LinkedIn 'in yanı sıra, LinkedIn 'in uygulamanın aşağıda gösterilen SAML yanıtına daha fazla sayıda özniteliğin geri geçirilmesi beklenir. Bu öznitelikler de önceden doldurulur, ancak gereksiniminize göre bunları gözden geçirebilirsiniz.

    | Name | Kaynak özniteliği|
    | -------| -------------|
    | bölüm | User. Departmanı |

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. LinkedIn 'i **Ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, LinkedIn yükseltmesine erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **LinkedIn Yükselt**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-linkedin-elevate-sso"></a>LinkedIn yükseltme SSO 'SU yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, LinkedIn yükseltme kiracınızda yönetici olarak oturum açın.

1. **Hesap Merkezi**'nde **Ayarlar**' ın altında **Genel ayarlar** ' a tıklayın. Ayrıca, açılır listeden **Yükselt-Yükselt AAD test** ' i seçin.

    ![Ekran görüntüsü, D testi Yükselt ' i seçebileceğiniz genel ayarları gösterir.](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. **Formdan tek tek alanları yüklemek ve kopyalamak için tıklayın veya buraya tıklayın** ve aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz tek Sign-On gösterir.](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

    a. **VARLıK kimliğini** kopyalayın ve Azure Portal **temel SAML yapılandırmasındaki** **tanımlayıcı** metin kutusuna yapıştırın.

    b. **Onaylama tüketici erişimi (ACS) URL 'sini** kopyalayın ve Azure Portal **temel SAML yapılandırmasındaki** **yanıt URL 'si** metin kutusuna yapıştırın.

1. **LinkedIn yönetici ayarları** bölümüne gidin. XML dosyasını karşıya yükle seçeneğine tıklayarak Azure portal indirdiğiniz XML dosyasını karşıya yükleyin.

    ![Ekran görüntüsü, bir X M L dosyasını karşıya yükleyebileceğiniz LinkedIn Service Provider S S O ayarlarını yapılandırmayı gösterir.](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. SSO 'yu **etkinleştirmek için tıklayın** . SSO durumu **bağlı** **değil** olarak değiştirilecek

    ![Ekran görüntüsü, otomatik olarak lisans atayabileceğiniz tek Sign-On gösterir.](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-elevate-test-user"></a>LinkedIn yükseltme testi Kullanıcı Oluştur

LinkedIn uygulaması, yalnızca Kullanıcı sağlamasını ve kimlik doğrulama kullanıcılarının uygulamada otomatik olarak oluşturulmasını destekler. LinkedIn 'in yönetim ayarları sayfasında, anahtarı ters çevir ' i tersine çevir, anahtarı **otomatik olarak** etkin bir şekilde sağlamak üzere atar ve bu da kullanıcıya bir lisans atar. LinkedIn, otomatik Kullanıcı sağlamayı da destekler, Ayrıca, otomatik Kullanıcı sağlamayı yapılandırma hakkında daha [fazla ayrıntı bulabilirsiniz](linkedinelevate-provisioning-tutorial.md) .

   ![Azure AD test kullanıcısı oluşturma](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde LinkedIn yükseltme kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız LinkedIn yükseltmesine otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile LinkedIn 'i deneyin](https://aad.portal.azure.com/)