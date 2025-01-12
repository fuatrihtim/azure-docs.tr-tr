---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için BlueJeans yapılandırma | Microsoft Docs'
description: Kullanıcı hesaplarının şemasını otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırma hakkında bilgi edinin.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 58cd69ebe97d9d0965d7e648b0ded012ac71cd0d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101646138"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı hazırlama için BlueJeans yapılandırma

Bu öğretici, otomatik Kullanıcı sağlamayı yapılandırmak için hem BlueJeans hem de Azure Active Directory (Azure AD) içinde gerçekleştirmeniz gereken adımları açıklamaktadır. Yapılandırıldığında, Azure AD, Azure AD sağlama hizmeti 'ni kullanarak kullanıcıları otomatik olarak hazırlar ve devre dışı [bırakmayı sağlar.](https://www.bluejeans.com/pricing) Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Desteklenen özellikler
> [!div class="checklist"]
> * Şema içinde Kullanıcı oluşturma
> * Kullanıcıları artık erişim gerektirdiklerinde BlueJeans 'ta kaldır
> * Azure AD ve şema arasında kullanıcı özniteliklerinin eşitlenmiş olmasını sağlama
> * Şeması ['Nda çoklu oturum açma](./bluejeans-tutorial.md) (önerilir)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* [Azure AD kiracısı](../develop/quickstart-create-new-tenant.md).
* Azure AD 'de sağlamayı yapılandırma [izni](../roles/permissions-reference.md) olan bir kullanıcı hesabı (örneğin, uygulama Yöneticisi, bulut uygulaması Yöneticisi, uygulama sahibi veya genel yönetici). 
* [Şirket](https://www.bluejeans.com/pricing) planınızla veya daha iyi etkinleştirilmiş bir şema kiracısı.
* Yönetici izinlerine sahip bir kullanıcı hesabı.
* BlueJeans kuruluşunda SCıM sağlama etkin.

> [!NOTE]
> Azure AD sağlama tümleştirmesi, standart plan veya daha iyi bir şekilde BlueJeans ekipleri tarafından kullanılabilen [BLUEJEANS API](https://BlueJeans.github.io/developer)'sini kullanır.

## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama
1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
2. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
3. [Azure AD Ile BlueJeans arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini saptayın.

## <a name="step-2-configure-bluejeans-to-support-provisioning-with-azure-ad"></a>Adım 2. Şemayı Azure AD ile sağlamayı destekleyecek şekilde yapılandırma

1. BlueJeans yönetici konsolunda oturum açın. Grup ayarları > güvenlik ' e gidin.
2. **Çoklu oturum açma** ve **Şimdi Yapılandır**' ı seçin.

    ![şimdi](./media/bluejeans-provisioning-tutorial/configure.png)

3. **& tümleştirme** penceresinde, **IDP aracılığıyla Kullanıcı hesapları oluştur ve Yönet** ' i seçin ve **belirteç oluştur**' a tıklayın.

    ![oluştur](./media/bluejeans-provisioning-tutorial/token.png)
    
4. Belirteci kopyalayın ve kaydedin. 
5. BlueJeans kiracı URL 'SI `https://api.bluejeans.com/v2/scim` . **Kiracı URL 'si** ve önceki adımdan gelen **gizli belirteç** , Azure Portal şema uygulamanızın sağlama sekmesine girilir.

## <a name="step-3-add-bluejeans-from-the-azure-ad-application-gallery"></a>3. Adım Azure AD uygulama galerisinden şema ekleme

Azure AD uygulama galerisindeki şema ekleyerek BlueJeans 'a sağlamayı yönetmeye başlayın. Daha önceden SSO için BlueJeans 'u ayarladıysanız aynı uygulamayı kullanabilirsiniz. Ancak başlangıçta tümleştirmeyi test ederken ayrı bir uygulama oluşturmanız önerilir. Galeriden uygulama ekleme hakkında daha fazla bilgi için [buraya](../manage-apps/add-application-portal.md) bakın.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama 

Azure AD sağlama hizmeti, uygulamaya atamaya ve Kullanıcı özniteliklerine göre sağlanacak olan kapsamınızı sağlar. Atamaya göre uygulamanıza sağlanacak kapsamı tercih ederseniz, kullanıcıları uygulamaya atamak için aşağıdaki [adımları](../manage-apps/assign-user-or-group-access-portal.md) kullanabilirsiniz. Yalnızca kullanıcının özniteliklerine göre sağlanacak olan kapsamı tercih ederseniz, [burada](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)açıklandığı gibi bir kapsam filtresi kullanabilirsiniz. 

* Kullanıcıları BlueJeans 'a atarken **varsayılan erişim** dışında bir rol seçmelisiniz. Varsayılan Erişim rolüne sahip kullanıcılar hazırlama kapsamından hariç tutulur ve hazırlama günlüklerinde yeterli yetkiye sahip olmadıkları belirtilir. Uygulama için kullanılabilen tek rol varsayılan erişim rolüyse [uygulama bildirimini güncelleştirerek](../develop/howto-add-app-roles-in-azure-ad-apps.md) daha fazla rol ekleyebilirsiniz. 

* Başlangıçta kapsamı sınırlı tutun. Herkese sunulmadan önce küçük bir Kullanıcı kümesiyle test edin. Sağlama kapsamı atanan kullanıcılara ayarlandığında, uygulamaya bir veya iki Kullanıcı atayarak bunu kontrol edebilirsiniz. Kapsam tüm kullanıcılara ayarlandığında, [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)belirtebilirsiniz. 

## <a name="step-5-configure-automatic-user-provisioning-to-bluejeans"></a>5. Adım. Şeması otomatik Kullanıcı sağlamayı yapılandırma

Bu bölümde, Azure AD sağlama hizmeti 'ni, Azure AD 'de Kullanıcı atamalarına göre TestApp 'deki kullanıcıları oluşturmak, güncelleştirmek ve devre dışı bırakmak üzere yapılandırma adımlarında kılavuzluk eder.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Azure AD 'de BlueJeans için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **BlueJeans**' ı seçin.

    ![Uygulamalar listesindeki BlueJeans bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Sekme otomatik sağlama](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümü altında, BlueJeans kiracı URL 'Nizi ve gizli belirtecinizi 2. adımda girin. Azure AD 'in BlueJeans 'a bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, BlueJeans hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Belirteç](common/provisioning-testconnection-tenanturltoken.png)


6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken kişinin e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’i seçin.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları Bluelerile eşitler**' ı seçin.

9. **Öznitelik eşleme** bölümünde Azure AD 'Den BlueJeans 'a eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme işlemleri için şema içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. [Eşleşen hedef özniteliğini](../app-provisioning/customize-application-attributes.md)değiştirmeyi seçerseniz, BlueJeans API 'sinin kullanıcıları bu özniteliğe göre filtrelemeyi desteklediğinden emin olmanız gerekir. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

|Öznitelik|Tür|Filtreleme için destekleniyor|
|---|---|---|
|userName|Dize|&check;|
|active|Boole|
|başlık|Dize|
|emails[type eq "work"].value|Dize|
|name.givenName|Dize|
|name.familyName|Dize|
|phoneNumbers[type eq "work"].value|Dize|
|urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Manager|Dize|

10. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

11. Şema için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

12. **Ayarlar** bölümünde **kapsam** içindeki istenen değerleri seçerek şema için sağlamak istediğiniz kullanıcıları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

13. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsamda** tanımlanan tüm kullanıcıların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. 

## <a name="step-6-monitor-your-deployment"></a>6. Adım. Dağıtımınızı izleme
Hazırlama ayarlarını yapılandırdıktan sonra dağıtımınızı izlemek için aşağıdaki kaynakları kullanın:

1. Hazırlama işlemi başarılı ve başarısız olan kullanıcıları belirlemek için [hazırlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md) kullanın
2. Hazırlama döngüsünün durumunu ve tamamlanması için kalan miktarı görmek için [ilerleme çubuğuna](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) bakın
3. Hazırlama yapılandırmasının durumu iyi görünmüyorsa uygulama karantinaya geçer. Karantina durumu hakkında daha fazla bilgi edinmek için [buraya](../app-provisioning/application-provisioning-quarantine-status.md) bakın.  

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* Şema, 30 karakterden uzun kullanıcı adlarıyla izin vermez.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)