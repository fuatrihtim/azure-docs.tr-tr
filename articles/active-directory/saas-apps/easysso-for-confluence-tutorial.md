---
title: 'Öğretici: Confluence için Easyswith ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Yapılandırma için Azure Active Directory ile Easysarasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 325f6ad7d9685fac17e17b28c4ffbe31b1245cca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98734554"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-confluence"></a>Öğretici: Confluence için Easyswith ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile bir sorun olması için EasySSO ile tümleştirmeyi öğreneceksiniz. EasySSO 'yi Azure AD ile ilgili bir şekilde tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Confluence 'a erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla karışabilmesi için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Confluence Single Sign-on (SSO) özellikli abonelik için EasySSO.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Confluence için EasySSO **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler
* Confluence için EasySSO **, tam zamanında** Kullanıcı sağlamayı destekliyor

## <a name="adding-easysso-for-confluence-from-the-gallery"></a>Galeri 'den Confluence için EasySSO ekleniyor

EasySSO 'nun Azure AD 'ye yönelik tümleştirmesini yapılandırmak için, galerinin yönetilen SaaS uygulamaları listenize yönelik olarak EasySSO eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Confluence Için easysso** yazın.
1. Sonuçlar panelinden **Confluence Için Easysso** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-easysso-for-confluence"></a>Yapılandırma için Azure AD SSO 'yu yapılandırın ve test edin

**B. Simon** adlı bir test kullanıcısı kullanarak yapılandırma IÇIN Azure AD SSO 'Yu EasySSO ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasında, Confluence için bir bağlantı ilişkisi kurmanız gerekir.

Yapılandırma için Azure AD SSO 'yu yapılandırmak ve test etmek için, aşağıdaki adımları uygulayın:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için, **[Confluence SSO Için EasySSO 'Yu yapılandırın](#configure-easysso-for-confluence-sso)** .
    1. Bu **[nedenle, Confluence test kullanıcısına](#create-easysso-for-confluence-test-user)** , kullanıcının Azure AD gösterimine bağlı olan confso 'da B. Simon 'un bir karşılığı olması Için Easysso oluşturun.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Confluence Için Easysso** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<server-base-url>/plugins/servlet/easysso/saml`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<server-base-url>/plugins/servlet/easysso/saml`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri şüpheli olarak almak için [Easysso destek ekibine](mailto:support@techtime.co.nz) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. Confluence uygulaması için EasySSO, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. Daha fazlasına ek olarak, Confluence uygulaması için EasySSO aşağıda gösterilen SAML yanıtında birkaç özniteliğin daha fazla özniteliğe geri geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.
    
    | Name | Kaynak özniteliği|
    | ---------------| --------- |
    | urn: OID: 0.9.2342.19200300.100.1.1 | User. UserPrincipalName |
    | urn: OID: 0.9.2342.19200300.100.1.3 | Kullanıcı. Mail |
    | urn: OID: 2.16.840.1.113730.3.1.241 | User. DisplayName |
    | urn: OID: 2.5.4.4 | User. soyadı |
    | urn: OID: 2.5.4.42 | Kullanıcı. |
    
    Azure AD kullanıcılarınızın **sAMAccountName** ile yapılandırılmış olması durumunda **urn: OID: 0.9.2342.19200300.100.1.1** ' i **sAMAccountName** özniteliğine eşlemeniz gerekir.
    
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** veya **Federasyon meta verileri XML** seçenekleri için bağlantıları **İndir** ' e tıklayın ve bilgisayarınıza ya da tümünü kaydedin. Daha sonra Confluence EasySSO öğesini yapılandırmak için buna ihtiyacınız olacak.

    ![Sertifika indirme bağlantısı](./media/easysso-for-confluence-tutorial/certificate.png)
    
    Sertifika ile el ile yapılandırma için EasySSO yapmayı planlıyorsanız, aşağıdaki bölümden **oturum açma URL 'sini** ve **Azure ad tanımlayıcısını** kopyalamanız ve bilgisayarınıza kaydetmeniz gerekir.

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

Bu bölümde, Confluence için Easysa erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Confluence Için Easysso** öğesini seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-easysso-for-confluence-sso"></a>Confluence SSO için EasySSO yapılandırma

1. Yapılandırma için Easysde yapılandırmayı otomatik hale getirmek amacıyla, **uzantıyı yüklemek** Için **uygulamalarım güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya uzantı ekledikten sonra, **Confluence için** , yapılandırmacıya tıklayın ve bu nedenle, Confluence uygulaması Için sizi easyssize yönlendirir. Buradan, bu nedenle, bir yönetici kimlik bilgilerini, Confluence için Easysde oturum açmak üzere belirtin. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-9 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

1. El ile yapılandırmalarınız için Easysu 'yi kurmak istiyorsanız, Atlasder Confluence Örneğinizde yönetici ayrıcalıklarıyla oturum açın ve **Uygulamaları Yönet** bölümüne gidin. 

    ![Uygulamaları Yönetme](./media/easysso-for-confluence-tutorial/confluence-admin-1.png)

2. Sol tarafta **Easysso** öğesini bulun ve tıklatın. Ardından **Yapılandır** düğmesine tıklayın.

    ![Kolay SSO](./media/easysso-for-confluence-tutorial/confluence-admin-2.png)

3. **SAML** seçeneğini belirleyin. Bu, sizi SAML yapılandırması bölümüne götürür.

    ![SAML](./media/easysso-for-confluence-tutorial/confluence-admin-3.png)

4. Üstteki **Sertifikalar** sekmesini seçin ve aşağıdaki ekran görüntülenir: 

    ![Meta veri URL 'SI](./media/easysso-for-confluence-tutorial/confluence-admin-4.png)

5. Şimdi **Azure AD SSO** yapılandırmasının önceki adımlarında kaydettiğiniz **sertifika (base64)** veya **meta veri dosyasını** bulun. İlerlemeniz için aşağıdaki seçenekleri kullanabilirsiniz:

    a. Bilgisayarınızda yerel dosyaya indirdiğiniz uygulama Federasyon **meta veri dosyasını** kullanın. **Yükleme** radyo düğmesini seçin ve işletim sisteminize özel karşıya yükleme dosyası iletişim kutusunu izleyin

    **OR**

    b. Dosyanın içeriğini (herhangi bir düz metin düzenleyicisinde) görmek ve panoya kopyalamak için uygulama Federasyon **meta verileri dosyasını** açın. **Giriş** seçeneğini belirleyin ve Pano içeriğini metin alanına yapıştırın.
 
    **OR**

    c. Tam el ile yapılandırma. Dosyanın içeriğini (herhangi bir düz metin düzenleyicisinde) görmek ve panoya kopyalamak için uygulama Federasyon **sertifikası 'nı (base64)** açın. **IDP belirteç Imzalama sertifikaları** metin alanına yapıştırın. Ardından **genel** sekmesine gidin ve bağlama URL **'SI** ve **varlık KIMLIĞI** alanlarını, **oturum açma URL** 'si ve daha önce kaydettiğiniz **Azure AD tanımlayıcısı** için ilgili değerlerle birlikte girin.
 
6. Sayfanın alt kısmındaki **Kaydet** düğmesine tıklayın. Meta verilerin içeriğini veya sertifika dosyalarını yapılandırma alanlarına ayrıştırmış olursunuz. Confluence yapılandırması için EasySSO işlemi tamamlanmıştır.

7. En iyi test deneyimi için, **&** Me sekmesine gidin ve üzerinde **SAML oturum açma düğmesi** seçeneğini işaretleyin. Bu, özellikle Azure AD SAML tümleştirme uçtan uca test etmek için, Gelişmiş oturum açma ekranında ayrı bir düğmeye olanak tanır. Bu düğmeyi açık bırakabilir ve üretim modu için yerleşimini, rengini ve çevirisini de yapılandırabilirsiniz.

    ![& göz atın](./media/easysso-for-confluence-tutorial/confluence-admin-5.png)

    > [!NOTE]
    > Herhangi bir sorununuz olması gerekir, lütfen [Easysso destek ekibine](mailto:support@techtime.co.nz)başvurun.

### <a name="create-easysso-for-confluence-test-user"></a>Confluence test kullanıcısı için EasySSO oluştur

Bu bölümde, Konluence 'de Britta Simon adlı bir Kullanıcı oluşturulur. Confluence için EasySSO, varsayılan olarak **devre dışı** olan tam zamanında Kullanıcı sağlamayı destekler. Kullanıcı sağlamayı etkinleştirmek için EasySSO eklentisi yapılandırmasının Genel bölümünde oturum **açma başarılı olduğunda Kullanıcı oluşturma** seçeneğini açıkça denetlemeniz gerekir. Bir Kullanıcı Confluence 'de zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

Ancak, Kullanıcı ilk oturum açmada otomatik Kullanıcı sağlamayı etkinleştirmek istemiyorsanız, kullanıcıların, LDAP veya Atlasence Crowd gibi ' nin, arka uç Kullanıcı dizinlerinde mevcut olması gerekir.

![Kullanıcı sağlama](./media/easysso-for-confluence-tutorial/confluence-admin-6.png)

## <a name="test-sso"></a>Test SSO 'SU 

### <a name="idp-initiated-workflow"></a>IDP tarafından başlatılan iş akışı

Bu bölümde, uygulamalarımı kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Uygulamalarım için EasySSO kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Confluence örneğinde otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

### <a name="sp-initiated-workflow"></a>SP tarafından başlatılan iş akışı

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı Confluence **SAML oturum açma** düğmesini kullanarak test edersiniz.

![Kullanıcı SAML oturum açma](./media/easysso-for-confluence-tutorial/confluence-admin-7.png)

Bu senaryo, Confluence EasySSO yapılandırma sayfasında bulunan **&** Me sekmesinde **SAML oturum açma düğmesini** etkinleştirmiş olduğunu varsayar (yukarıya bakın). Mevcut oturumlarınız ile herhangi bir girişimden kaçınmak için, tarayıcı ınbilito modunda Confluence Login URL 'nizi açın. **SAML oturum açma** düğmesine TıKLADıĞıNıZDA Azure AD Kullanıcı kimlik doğrulama akışına yönlendirilirsiniz. Başarılı bir şekilde tamamlandıktan sonra, SAML aracılığıyla kimliği doğrulanmış kullanıcı olarak, Confluence örneğinize geri yönlendirilirsiniz.

Azure AD 'den geri yönlendirildikten sonra aşağıdaki ekranla karşılaşacağınız bir olasılık vardır

![EasySSO hata ekranı](./media/easysso-for-confluence-tutorial/confluence-admin-8.png)

Bu durumda, **Atlassian-Confluence. log** dosyasına erişim sağlamak için [Bu sayfadaki yönergeleri]( https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS) izlemeniz gerekir. Hatanın ayrıntıları EasySSO hata sayfasında bulunan başvuru Kımlığı tarafından kullanılabilir olacaktır.

Günlük iletilerini geri almaya yönelik herhangi bir sorununuz olması gerekir, lütfen [Easysso destek ekibine](mailto:support@techtime.co.nz)başvurun.

## <a name="next-steps"></a>Sonraki adımlar

Bu nedenle, Confluence için EasySSO 'yı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).