---
title: 'Öğretici: TIMU ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory Microsoft Docs'
description: Azure Active Directory ve TIMU arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/10/2020
ms.author: jeedes
ms.openlocfilehash: 903150a199bac1189274cadc9755b38873cde73e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96182113"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-timu"></a>Öğretici: TIMU ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, TIMU 'yı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. TIMU 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de TIMU 'ya erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla TIMU 'da otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* TIMU çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* TIMU **SP** tarafından başlatılan SSO 'yu destekliyor

* TIMU **yalnızca zaman** Kullanıcı sağlamasını destekler

## <a name="adding-timu-from-the-gallery"></a>Galeriden TIMU ekleniyor

TIMU 'nın tümleştirmesini Azure AD 'ye göre yapılandırmak için Galeri 'den yönetilen SaaS uygulamaları listenize TIMU eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **timu** yazın.
1. Sonuçlar panelinden **timu** ' yi seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-timu"></a>TIMU için Azure AD SSO 'YU yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak, timu Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve TIMU 'daki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu TIMU ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[TIMU SSO 'Yu yapılandırma](#configure-timu-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Timu **[testi kullanıcısı oluşturun](#create-timu-test-user)** . Bu, kullanıcının Azure AD gösterimine bağlı olan timu 'de B. Simon 'a karşılık gelen bir.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **timu** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki URL 'lerden birini yazın:

    | Oturum açma URL'si |
    |-------------|
    | `https://auth.timu.com` |
    | `https://auth.timu.life` |
    |

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusuna aşağıdaki desenlerden birini yazın:
    
    | Tanımlayıcı |
    |-------------|
    | `https://<SUBDOMAIN>.timu.com/api/login/saml/callback` |
    | `https://<SUBDOMAIN>.timu.life/api/login/saml/callback`|
    |

    c. **Yanıt URL 'si** metin kutusuna aşağıdaki desenlerden birini yazın:
    
    | Yanıt URL'si |
    |-------------|
    | `https://<SUBDOMAIN>.timu.com/api/login/saml/callback` |
    | `https://<SUBDOMAIN>.timu.life/api/login/saml/callback`|
    |

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [timu istemci desteği ekibine](mailto:support@timu.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

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

Bu bölümde, TIMU 'ya erişim vererek, B. Simon 'u Azure çoklu oturum açma özelliğini kullanacak şekilde etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **timu**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-timu-sso"></a>TIMU SSO 'YU yapılandırma

**Timu** tarafında çoklu oturum açmayı yapılandırmak Için, [timu destek ekibine](mailto:support@timu.com) **uygulama Federasyon meta veri URL 'sini** göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-timu-test-user"></a>TIMU testi kullanıcısı oluştur

Bu bölümde, TIMU 'da Britta Simon adlı bir Kullanıcı oluşturulmuştur. TIMU, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı TIMU 'da zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz TIMU oturum açma URL 'sine yeniden yönlendirilir. 

* TIMU oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft Access panel ' i kullanabilirsiniz. Erişim panelinde TIMU kutucuğuna tıkladığınızda bu, TIMU oturum açma URL 'sine yeniden yönlendirilir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

TIMU 'yı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).