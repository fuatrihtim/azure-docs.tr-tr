---
title: Azure AD Domain Services Grup ilkesi oluşturma ve yönetme | Microsoft Docs
description: Azure Active Directory Domain Services yönetilen bir etki alanında yerleşik Grup İlkesi nesnelerini (GPO 'Lar) düzenlemeyi ve kendi özel ilkelerinizi oluşturmayı öğrenin.
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: f1f2499c49c4adf16b632bc75c246a28330ad27b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619394"
---
# <a name="administer-group-policy-in-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services yönetilen bir etki alanında grup ilkesi yönetme

Azure Active Directory Domain Services (Azure AD DS) içindeki kullanıcı ve bilgisayar nesnelerine yönelik ayarlar genellikle grup ilkesi nesneleri (GPO 'Lar) kullanılarak yönetilir. Azure AD DS, *Aaddc kullanıcıları* ve *Aaddc bilgisayarları* kapsayıcıları için yerleşik GPO 'lar içerir. Bu yerleşik GPO 'Ları, ortamınız için gereken grup ilkesi yapılandırmak üzere özelleştirebilirsiniz. *Azure AD DC Administrators* grubunun üyeleri, Azure AD DS etki alanında Grup ilkesi yönetim ayrıcalıklarına sahiptir ve ayrıca özel GPO 'lar ve kuruluş birimleri (OU 'lar) oluşturabilir. Grup ilkesi ne olduğu ve nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Grup ilkesi genel bakış][group-policy-overview].

Karma bir ortamda, şirket içi AD DS ortamda yapılandırılan Grup ilkeleri Azure AD DS ile eşitlenmez. Azure AD DS 'de kullanıcıların veya bilgisayarların yapılandırma ayarlarını tanımlamak için varsayılan GPO 'Lardan birini düzenleyin veya özel bir GPO oluşturun.

Bu makalede grup ilkesi yönetim araçlarının nasıl yükleneceği ve ardından yerleşik GPO 'Ların nasıl düzenleneceği ve özel GPO 'Ların nasıl oluşturulduğu gösterilir.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makaleyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, [Azure Active Directory Domain Services yönetilen bir etki alanı oluşturmak ve yapılandırmak][create-azure-ad-ds-instance]için öğreticiyi doldurun.
* Azure AD DS yönetilen etki alanına katılmış bir Windows Server Yönetim sanal makinesi.
    * Gerekirse, [bir Windows Server VM oluşturma ve bunu yönetilen bir etki alanına katma][create-join-windows-vm]öğreticisini doldurun.
* Azure AD kiracınızda *Azure AD DC Administrators* grubunun üyesi olan bir kullanıcı hesabı.

> [!NOTE]
> Yeni şablonları yönetim iş istasyonuna kopyalayarak grup ilkesi Yönetim Şablonları kullanabilirsiniz. *. Admx* dosyalarını içine kopyalayın `%SYSTEMROOT%\PolicyDefinitions` ve yerel ayara özgü *. adml* dosyalarını öğesine kopyalayın `%SYSTEMROOT%\PolicyDefinitions\[Language-CountryRegion]` `Language-CountryRegion` *. adml* dosyalarının diliyle ve bölgesiyle eşleşir.
>
> Örneğin, *. adml* dosyalarının ingilizce, Birleşik Devletler sürümünü `\en-us` klasörüne kopyalayın.
>
> Alternatif olarak, grup ilkesi yönetim şablonunuzu yönetilen etki alanının parçası olan etki alanı denetleyicilerinde merkezi olarak saklayabilirsiniz. Daha fazla bilgi için bkz. [Windows 'da grup ilkesi Yönetim Şablonları Için merkezi mağaza oluşturma ve yönetme](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra).

## <a name="install-group-policy-management-tools"></a>grup ilkesi yönetim araçları 'nı yükler

Grup ilkesi nesnesi (GPO) oluşturmak ve yapılandırmak için grup ilkesi yönetim araçlarını yüklemeniz gerekir. Bu araçlar, Windows Server 'da bir özellik olarak yüklenebilir. Windows istemcisine yönetimsel araçların nasıl yükleneceği hakkında daha fazla bilgi için bkz. Install [uzak sunucu yönetim araçları (RSAT)][install-rsat].

1. Yönetim sanal makinenizde oturum açın. Azure portal kullanarak bağlanma adımları için bkz. [Windows Server VM 'ye bağlanma][connect-windows-server-vm].
1. **Sunucu Yöneticisi** , VM 'de oturum açtığınızda varsayılan olarak açılmalıdır. Aksi takdirde, **Başlat** menüsünde **Sunucu Yöneticisi**' yi seçin.
1. **Sunucu Yöneticisi** penceresinin *Pano* bölmesinde **rol ve Özellik Ekle**' yi seçin.
1. *Rol ve Özellik Ekleme Sihirbazı*' nın **başlamadan önce** sayfasında **İleri**' yi seçin.
1. *Yükleme türü* Için, **rol tabanlı veya özellik tabanlı yükleme** seçeneğini işaretli bırakın ve **İleri ' yi** seçin.
1. **Sunucu seçimi** sayfasında, sunucu havuzundan *MYVM.AADDSCONTOSO.com* gibi geçerli VM 'Yi seçin ve ardından **İleri**' yi seçin.
1. **Sunucu rolleri** sayfasında, **İleri**' ye tıklayın.
1. **Özellikler** sayfasında **Grup İlkesi Yönetimi** özelliğini seçin.

    ![Özellikler sayfasından ' grup ilkesi Management ' öğesini yükler](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)

1. **Onay** sayfasında, **yükler**' i seçin. Grup ilkesi yönetim araçlarını yüklemek bir dakika veya iki dakika sürebilir.
1. Özellik yüklemesi tamamlandığında, **rol ve özellik ekleme** Sihirbazı 'ndan çıkmak için **Kapat** ' ı seçin.

## <a name="open-the-group-policy-management-console-and-edit-an-object"></a>Grup İlkesi Yönetim Konsolu açma ve nesneyi düzenleme

Yönetilen bir etki alanındaki kullanıcılar ve bilgisayarlar için varsayılan Grup İlkesi nesneleri (GPO 'Lar) vardır. Önceki bölümden yüklenmiş grup ilkesi yönetimi özelliği ile mevcut bir GPO 'YU görüntüleyip düzenleyelim. Sonraki bölümde, özel bir GPO oluşturursunuz.

> [!NOTE]
> Yönetilen bir etki alanında Grup İlkesi 'ni yönetmek için *AAD DC Administrators* grubunun üyesi olan bir kullanıcı hesabında oturum açmış olmanız gerekir.

1. Başlangıç ekranından **Yönetim Araçları**' nı seçin. Önceki bölümde yüklü **Grup İlkesi Yönetimi** de dahil olmak üzere kullanılabilir yönetim araçlarının bir listesi gösterilir.
1. Grup İlkesi Yönetim Konsolu (GPMC) açmak için **Grup İlkesi Yönetim**' i seçin.

    ![Grup İlkesi Yönetim Konsolu, Grup İlkesi nesnelerini düzenlemeye hazırmı açılır](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

Yönetilen bir etki alanında, *Aaddc bilgisayarları* kapsayıcısı için bir, diğeri de *Aaddc kullanıcıları* kapsayıcısı için bir adet yerleşik Grup İlkesi nesnesi (GPO) vardır. Bu GPO 'Ları, yönetilen etki alanınız dahilinde gereken Grup İlkesi 'ni yapılandırmak üzere özelleştirebilirsiniz.

1. **Grup İlkesi Yönetim** konsolunda, **Orman: aaddscontoso.com** düğümünü genişletin. Sonra, **etki alanı** düğümlerini genişletin.

    *Aaddc bilgisayarları* ve *Aaddc kullanıcıları* için iki yerleşik kapsayıcı bulunur. Bu kapsayıcıların her birine, uygulanan varsayılan bir GPO vardır.

    ![Varsayılan ' AADDC Computers ' ve ' AADDC Users ' kapsayıcılarına uygulanan yerleşik GPO 'Lar](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)

1. Bu yerleşik GPO 'Lar, yönetilen etki alanındaki belirli grup ilkelerini yapılandırmak için özelleştirilebilir. GPO 'Lardan birini ( *Aaddc bilgisayarları GPO 'su* gibi) sağ seçin ve **Düzenle...** seçeneğini belirleyin.

    ![Yerleşik GPO 'Lardan birini ' düzenleme ' seçeneğini belirleyin](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)

1. *Hesap ilkeleri* gıbı, GPO 'yu özelleştirmenizi sağlamak için Grup İlkesi Yönetimi Düzenleyicisi aracı açılır:

    ![Grup İlkesi Yönetimi Düzenleyicisi ekran görüntüsü.](./media/active-directory-domain-services-admin-guide/gp-editor.png)

    İşiniz bittiğinde, ilkeyi kaydetmek için **dosya > kaydet** ' i seçin. Bilgisayarlar varsayılan olarak her 90 dakikada bir grup ilkesi yeniler ve yaptığınız değişiklikleri uygular.

## <a name="create-a-custom-group-policy-object"></a>Özel bir grup ilkesi nesnesi oluşturma

Benzer ilke ayarlarını gruplamak için, tüm gerekli ayarları tek, varsayılan GPO 'da uygulamak yerine genellikle ek GPO 'Lar oluşturursunuz. Azure AD DS ile kendi özel Grup İlkesi nesnelerinizi oluşturup içeri aktarabilir ve bunları özel bir OU 'ya bağlayabilirsiniz. Önce özel bir OU oluşturmanız gerekiyorsa, bkz. yönetilen bir [etki alanında özel bır OU oluşturma](create-ou.md).

1. **Grup İlkesi Yönetim** konsolunda, *mycustomou* gibi özel kuruluş birimi (OU) seçeneğini belirleyin. OU 'yu sağ seçin ve **Bu etki alanında GPO oluştur ve buraya bağla ' yı seçin...**:

    ![grup ilkesi yönetim konsolunda özel bir GPO oluşturma](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)

1. Yeni GPO için *özel GPO 'IM* gibi bir ad belirtin ve ardından **Tamam**' ı seçin. İsteğe bağlı olarak bu özel GPO 'YU mevcut bir GPO 'da ve ilke seçenekleri kümesinde temel alabilirsiniz.

    ![Yeni özel GPO için bir ad belirtin](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)

1. Özel GPO oluşturulup özel OU 'nuzun bağlanır. İlke ayarlarını şimdi yapılandırmak için özel GPO 'YU sağ seçin ve **Düzenle...**' yi seçin:

    ![Özel GPO 'nuzun ' düzenleme ' seçeneğini belirleyin](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)

1. **Grup İlkesi Yönetimi Düzenleyicisi** , GPO 'yu özelleştirmenize olanak sağlamak için açılır:

    ![Ayarları gerektiği gibi yapılandırmak için GPO 'YU özelleştirme](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)

    İşiniz bittiğinde, ilkeyi kaydetmek için **dosya > kaydet** ' i seçin. Bilgisayarlar varsayılan olarak her 90 dakikada bir grup ilkesi yeniler ve yaptığınız değişiklikleri uygular.

## <a name="next-steps"></a>Sonraki adımlar

Grup İlkesi Yönetim Konsolu kullanarak yapılandırabileceğiniz kullanılabilir grup ilkesi ayarları hakkında daha fazla bilgi için bkz. [Grup İlkesi tercih öğeleriyle çalışma][group-policy-console].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[group-policy-overview]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11)
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
[group-policy-console]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789194(v=ws.11)
