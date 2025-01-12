---
title: Azure Güvenlik Merkezi 'nde mevzuat uyumluluk panosunu kullanma
description: Güvenlik Merkezi 'ndeki mevzuat uyumluluk panosundan mevzuat standartları ekleme ve kaldırma hakkında bilgi edinin
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 768f686889663d9b1af4b88d84b361ac9460a5a0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381741"
---
# <a name="customize-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>Yasal uyumluluk panonuzdaki standartlar kümesini özelleştirme

Azure Güvenlik Merkezi, kaynaklarınızın yapılandırmalarını endüstri standartları, yönetmelikler ve kıyaslamalar ile sürekli olarak karşılaştırır. **Mevzuat uyumluluk panosu** , uyumluluk için özel uyumluluk gereksinimlerini nasıl karşıladığınızı temel alarak öngörülerinizi açıklar.


## <a name="how-are-regulatory-compliance-standards-represented-in-security-center"></a>Yasal uyumluluk standartları Güvenlik Merkezi 'nde nasıl temsil edilir?

Endüstri standartları, mevzuat standartları ve kıyaslamalar, güvenlik merkezi 'nin mevzuat uyumluluk panosunda temsil edilir. Her standart Azure Ilkesinde tanımlanan bir girişimdir.

Panonuzda değerlendirmelere eşlenen uyumluluk verilerini görmek için, **güvenlik ilkesi** sayfasından yönetim grubunuza veya aboneliğinize bir uyumluluk standardı ekleyin. Azure Ilkesi ve girişimleri hakkında daha fazla bilgi edinmek için bkz. [güvenlik Ilkeleriyle çalışma](tutorial-security-policy.md).

Seçtiğiniz kapsamınızda bir standart veya kıyaslama atadıktan sonra, tüm ilişkili uyumluluk verileriyle değerlendirmelere eşlenen standart, mevzuat uyumluluk panonuzda görünür. Atanan standartların herhangi biri için Özet raporları da indirebilirsiniz.

Microsoft, mevzuat standartlarının kendisini izler ve zaman içindeki bazı paketlerdeki kapsamını otomatik olarak geliştirir. Microsoft, girişim için yeni içerik yayımlarsa, panoda, standart denetimlere eşlenen yeni ilkeler olarak otomatik olarak görünür.


## <a name="what-regulatory-compliance-standards-are-available-in-security-center"></a>Güvenlik Merkezi 'nde hangi yasal uyumluluk standartları mevcuttur?

Varsayılan olarak, her abonelikte **Azure Güvenlik kıyaslaması** atanır. Bu, yaygın uyumluluk çerçevelerine göre güvenlik ve uyum en iyi uygulamaları için Microsoft tarafından yazılan, Azure 'a özgü kılavuzlardır. [Azure Güvenlik Karşılaştırması hakkında daha fazla bilgi edinin](../security/benchmarks/introduction.md).

Ayrıca, gibi standartlar da ekleyebilirsiniz:

- NIST SP 800-53 R4
- SWIFT CSP CSCF-V2020
- UK resmi ve UK NHS
- Kanada Federal PBMM
- Azure CIS 1.1.0

Standartlar, kullanılabilir hale geldiğinde panoya eklenir.


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>Panonuza bir düzenleme standardı ekleme

Aşağıdaki adımlarda, desteklenen mevzuata standartlarından biriyle uyumluluğunuzu izlemek için bir paket ekleme açıklanmaktadır.

> [!NOTE]
> Panonuza standartlar eklemek için abonelikte Azure Defender etkinleştirilmiş olmalıdır. Ayrıca, yalnızca sahibi veya ilke katılımcısı olan kullanıcılar uyumluluk standartları eklemek için gerekli izinlere sahiptir. 

1. Güvenlik Merkezi 'nin kenar çubuğundan, mevzuat uyumluluk panosunu açmak için **mevzuata uyumluluğu** ' nu seçin. Burada şu anda seçili olan aboneliklerde Şu anda atanmış olan uyumluluk standartlarını görebilirsiniz.   

1. Sayfanın üst kısmından **Uyumluluk Ilkelerini Yönet**' i seçin. Ilke Yönetimi sayfası görüntülenir.

1. Mevzuat uyumluluk duruşunu yönetmek istediğiniz aboneliği veya yönetim grubunu seçin. 

    > [!TIP]
    > Tüm iç içe geçmiş kaynaklar için uyumluluk verilerinin toplanması ve izlenmesi için standart için geçerli olan en yüksek kapsamı seçmenizi öneririz. 

1. Kuruluşunuza uygun standartları eklemek için **daha fazla standartlar Ekle**' ye tıklayın. 

1. **Yasal uyumluluk standartları Ekle** sayfasında, aşağıdakiler dahil olmak üzere kullanılabilir standartlardan herhangi birini arayabilirsiniz:

    - **NIST SP 800-53 R4**
    - **NIST SP 800 171 R2**
    - **SWIFT CSP CSCF v2020**
    - **UKO ve UK NHS**
    - **Kanada Federal PBMM**
    - **HIPAA HITRUST**
    - **Azure CIS 1.1.0**
    
    ![Azure Güvenlik Merkezi 'nin mevzuat uyumluluk panosuna mevzuat standartları ekleme](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. **Ekle** ' yi seçin ve belirli bir girişim için kapsam, parametre ve düzeltme gibi tüm gerekli ayrıntıları girin.

1. Güvenlik Merkezi 'nin kenar çubuğundan, mevzuat uyumluluk panosuna geri dönmek için **yasal uyumluluk** ' i seçin.

    Yeni standart, sektör & mevzuat standartları listenizde görünür. 

    > [!NOTE]
    > Yeni eklenen bir standart, uyumluluk panosu 'nda görünmesi birkaç saat sürebilir.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Mevzuat uyumluluk panosu" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

## <a name="remove-a-standard-from-your-dashboard"></a>Panonuzdan bir standart kaldırma

Sağlanan yönetmelik standartlarından herhangi biri kuruluşunuzla ilgili değilse, bunları kullanıcı arabiriminden kaldırmak basit bir işlemdir. Bu, mevzuat uyumluluk panosunu daha da özelleştirmenize ve yalnızca sizin için geçerli olan standartlara odaklanmanıza olanak tanır.

Standart bir kaldırma:

1. Güvenlik Merkezi menüsünde **güvenlik ilkesi**' ni seçin.

1. Standart kaldırmak istediğiniz aboneliği seçin.

    > [!NOTE]
    > Bir standart aboneliği bir yönetim grubundan kaldırabilirsiniz. 

    Güvenlik ilkesi sayfası açılır. Seçili abonelik için, varsayılan ilkeyi, sektör ve mevzuat standartlarını ve oluşturduğunuz özel girişimleri gösterir.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Azure Güvenlik Merkezi 'nde mevzuat uyumluluk panonuzdan bir yasal düzenleme standardı kaldırma":::

1. Kaldırmak istediğiniz standart için **devre dışı bırak**' ı seçin. Bir onay penceresi görüntülenir.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="Seçtiğiniz mevzuat standardını gerçekten kaldırmak istediğinizi onaylayın":::

1. **Evet**’i seçin. Standart kaldırılacak. 


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, mevzuat ve sektör standartları ile uyumluluğunuzu izlemek için **uyumluluk standartları ekleme** hakkında daha fazla öğrendiniz.

İlgili malzemeler için aşağıdaki sayfalara bakın:

- [Azure Güvenlik Karşılaştırması](../security/benchmarks/introduction.md)
- [Güvenlik Merkezi mevzuat uyumluluk panosu](security-center-compliance-dashboard.md) -yasal uyumluluk verilerinizi güvenlik merkezi ve dış araçlarla izleme ve dışa aktarma hakkında bilgi edinin
- [Güvenlik ilkeleriyle çalışma](tutorial-security-policy.md)