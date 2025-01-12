---
title: Kaynak gruplarını yönetme-Azure portal
description: Kaynak gruplarınızı Azure Resource Manager kullanarak yönetmek için Azure portal kullanın. Kaynak grupları oluşturma, listeleme ve silme işlemlerinin nasıl yapılacağını gösterir.
author: mumian
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: aabaad54dce52d57aaa4689f591351e290b0f9a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101093691"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Azure portal kullanarak Azure Resource Manager kaynak gruplarını yönetme

Azure Kaynak gruplarınızı yönetmek için [Azure Resource Manager](overview.md) [Azure Portal](https://portal.azure.com) nasıl kullanacağınızı öğrenin. Azure kaynaklarını yönetmek için, bkz. [Azure Portal kullanarak Azure kaynaklarını yönetme](manage-resources-portal.md).

Kaynak gruplarını yönetme hakkında diğer makaleler:

- [Azure CLı kullanarak Azure kaynak gruplarını yönetme](manage-resources-cli.md)
- [Azure PowerShell kullanarak Azure kaynak gruplarını yönetme](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>Kaynak grubu nedir?

Kaynak grubu, bir Azure çözümüne ilişkin kaynakları tutan bir kapsayıcıdır. Kaynak grubu bir çözümün tüm kaynaklarını veya yalnızca grup olarak yönetmek istediğiniz kaynakları içerebilir. Kuruluş için önemli olan faktörleri temel alarak kaynakları kaynak gruplarına nasıl ayıracağınıza siz karar verirsiniz. Genellikle, bunları bir grup olarak kolayca dağıtabilmeniz, güncelleştirebilmeniz ve silebilmeniz için aynı yaşam döngüsünü paylaşan kaynakları ekleyin.

Kaynak grubu, kaynaklarla ilgili meta verileri depolar. Bu nedenle, kaynak grubu için bir konum belirttiğinizde meta verilerin nereye depolanacağını belirtirsiniz. Uyumluluk nedeniyle verilerinizin belirli bir bölgeye depolandığından emin olmanız gerekebilir.


## <a name="create-resource-groups"></a>Kaynak grupları oluşturma

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. **Kaynak gruplarını** seçin

    ![Kaynak Grubu Ekle](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. **Add (Ekle)** seçeneğini belirleyin.
4. Aşağıdaki değerleri girin:

   - **Abonelik**: Azure aboneliğinizi seçin. 
   - **Kaynak grubu**: yeni bir kaynak grubu adı girin. 
   - **Bölge**: **Orta ABD** gibi bir Azure konumu seçin.

     ![kaynak grubu oluştur](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. **Gözden geçir + oluştur** ' u seçin
6. **Oluştur**’u seçin. Bir kaynak grubu oluşturmak birkaç saniye sürer.
7. Kaynak grubu listesini yenilemek için üstteki menüden **Yenile** ' yi seçin ve ardından yeni oluşturulan kaynak grubunu seçerek dosyayı açın. Ya da üst kısımdaki **bildirim**(zil simgesi) öğesini seçin ve ardından yeni oluşturulan kaynak grubunu açmak için **kaynak grubuna git** ' i seçin.

    ![kaynak grubuna git](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Kaynak gruplarını listeleme

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Kaynak gruplarını listelemek için **kaynak grupları** ' nı seçin.

    ![kaynak gruplarına gözatamıyorum](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Kaynak grupları için görüntülenecek bilgileri özelleştirmek için **Sütunları Düzenle**' yi seçin. Aşağıdaki ekran görüntüsünde, ekranda ekleyebileceğiniz ekleme sütunları gösterilmektedir:

## <a name="open-resource-groups"></a>Kaynak gruplarını aç

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. **Kaynak grupları**’nı seçin.
3. Açmak istediğiniz kaynak grubunu seçin.

## <a name="delete-resource-groups"></a>Kaynak gruplarını silme

1. Silmek istediğiniz kaynak grubunu açın.  Bkz. [açık kaynak grupları](#open-resource-groups).
2. **Kaynak grubunu sil**'i seçin.

    ![Azure kaynak grubunu sil](./media/manage-resource-groups-portal/delete-group.png)

Kaynakların silinmesini Azure Resource Manager nasıl sipariş Azure Resource Manager hakkında daha fazla bilgi için bkz. [kaynak grubu silme](delete-resource-group.md).

## <a name="deploy-resources-to-a-resource-group"></a>Kaynakları bir kaynak grubuna dağıtma

Kaynak Yöneticisi şablonu oluşturduktan sonra, Azure kaynaklarınızı dağıtmak için Azure portal kullanabilirsiniz. Şablon oluşturmak için bkz. [hızlı başlangıç: Azure Portal kullanarak Azure Resource Manager şablonları oluşturma ve dağıtma](../templates/quickstart-create-templates-use-the-portal.md). Portalı kullanarak bir şablon dağıtmak için bkz. [Kaynak Yöneticisi şablonlarıyla kaynak dağıtma ve Azure Portal](../templates/deploy-portal.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Başka bir kaynak grubuna veya aboneliğe taşıma

Gruptaki kaynakları başka bir kaynak grubuna taşıyabilirsiniz. Daha fazla bilgi için bkz. [Kaynakları yeni kaynak grubuna veya aboneliğe taşıma](move-resource-group-and-subscription.md).

## <a name="lock-resource-groups"></a>Kaynak gruplarını kilitle

Kilitleme, kuruluşunuzdaki diğer kullanıcıların Azure aboneliği, kaynak grubu veya kaynak gibi önemli kaynakları yanlışlıkla silmesini veya değiştirmelerini engeller. 

1. Kilitlemek istediğiniz kaynak grubunu açın.  Bkz. [açık kaynak grupları](#open-resource-groups).
2. Sol bölmede, **kilitler**' ı seçin.
3. Kaynak grubuna bir kilit eklemek için **Ekle**' yi seçin.
4. **Kilit adı**, **Kilit türü** ve **Not** girin. Kilit türleri **salt okunurdur** ve **Delete**' i içerir.

    ![Azure kaynak grubunu kilitle](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

Daha fazla bilgi için bkz. [beklenmeyen değişiklikleri engellemek için kaynakları kilitleme](lock-resources.md).

## <a name="tag-resource-groups"></a>Kaynak gruplarını etiketleme

Varlıklarınızı mantıksal olarak düzenlemek için kaynak gruplarına ve kaynaklarına Etiketler uygulayabilirsiniz. Daha fazla bilgi için bkz. [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](tag-resources.md#portal).

## <a name="export-resource-groups-to-templates"></a>Kaynak gruplarını şablonlara aktarma

Şablonları dışarı aktarma hakkında daha fazla bilgi için bkz. [şablona tek ve çoklu kaynak dışarı aktarma-Portal](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Kaynak gruplarına erişimi yönetme

Azure [rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md) , Azure 'daki kaynaklara erişimi yönetme yöntemidir. Daha fazla bilgi için bkz. [Azure Portal kullanarak Azure rolleri atama](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Sonraki adımlar

- Azure Resource Manager öğrenmek için bkz. [Azure Resource Manager genel bakış](overview.md).
- Kaynak Yöneticisi Şablon sözdizimini öğrenmek için bkz. [Azure Resource Manager şablonlarının yapısını ve sözdizimini anlayın](../templates/template-syntax.md).
- Şablon geliştirmeyi öğrenmek için [adım adım öğreticiler](../index.yml)bölümüne bakın.
- Azure Resource Manager şablonu şemalarını görüntülemek için bkz. [şablon başvurusu](/azure/templates/).
