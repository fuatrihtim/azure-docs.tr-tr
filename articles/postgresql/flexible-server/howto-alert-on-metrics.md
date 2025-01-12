---
title: Uyarıları Yapılandırma-Azure portal-PostgreSQL için Azure veritabanı-esnek sunucu
description: Bu makalede, Azure portal 'den PostgreSQL için Azure veritabanı-esnek sunucu için ölçüm uyarılarının nasıl yapılandırılacağı ve erişebileceği açıklanır.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 365cb5604c1f9ddc2c0d7f3cc63daff96b043515
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608060"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---flexible-server"></a>PostgreSQL için Azure veritabanı ile ilgili ölçümler hakkında uyarı ayarlamak için Azure portal kullanın-esnek sunucu

> [!IMPORTANT]
> PostgreSQL için Azure veritabanı-esnek sunucu önizlemededir

Bu makalede, Azure portal kullanarak PostgreSQL için Azure veritabanı uyarılarını ayarlama konusu gösterilmektedir. Azure hizmetleriniz için izleme ölçümlerini temel alan bir uyarı alabilirsiniz.

Uyarı, belirtilen bir ölçümün değeri atadığınız bir eşiği aştığında tetiklenir. Uyarı hem koşul ilk karşılandığında hem de daha sonra bu koşul karşılanamadığında daha sonra tetiklenir.

Bir uyarıyı, tetiklendiğinde aşağıdaki eylemleri yapmak üzere yapılandırabilirsiniz:

* Hizmet yöneticisine ve ortak yöneticilere e-posta bildirimleri gönderin.
* Belirttiğiniz ek e-postalara e-posta gönderin.
* Web kancası çağırın.

Kullanarak uyarı kuralları hakkında bilgi alabilir ve bunları alabilirsiniz:

* [Azure portalı](../../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [Azure İzleyici REST API'si](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Azure portaldan bir ölçüm hakkında uyarı kuralı oluşturma

1. [Azure Portal](https://portal.azure.com/), Izlemek Istediğiniz PostgreSQL Için Azure veritabanı sunucusu ' nu seçin.

2. Kenar çubuğunun **izleme** bölümü altında gösterildiği gibi **Uyarılar** ' ı seçin:

   :::image type="content" source="./media/howto-alert-on-metrics/2-alert-rules.png" alt-text="Uyarı kurallarını seçin":::

3. **Ölçüm uyarısı Ekle** (+ simgesi) seçeneğini belirleyin.

4. **Kural oluştur** sayfası aşağıda gösterildiği gibi açılır. Gereken bilgileri doldurun:

   :::image type="content" source="./media/howto-alert-on-metrics/4-add-rule-form.png" alt-text="Ölçüm uyarısı formu Ekle":::

5. **Koşul** bölümünde **Koşul Ekle**' yi seçin.

6. Uyarı almak için sinyaller listesinden bir ölçüm seçin. Bu örnekte, "depolama alanı yüzdesi" ni seçin.

   :::image type="content" source="./media/howto-alert-on-metrics/6-configure-signal-logic.png" alt-text="Ölçüm seçin":::

7. **Koşul** (örn.) gibi uyarı mantığını yapılandırın. "Büyüktür"), **eşik** (örn. yüzde 85), **zaman toplama**, ölçüm kuralının uyarı tetikleyiciden önce karşılanması **gereken süre (** örn. "Son 30 dakika içinde") ve **Sıklık**.

   Tamamlandığında **bitti** ' yi seçin.

   :::image type="content" source="./media/howto-alert-on-metrics/7-set-threshold-time.png" alt-text="Eşik ayarlama":::

8. Uyarı üzerinde bildirim almak üzere yeni bir grup oluşturmak için **eylem grupları** bölümünde **Yeni oluştur** ' u seçin.

9. "Eylem grubu Ekle" formunu bir ad, kısa ad, abonelik ve kaynak grubuyla doldurun.

10. **E-posta/SMS/Push/Voice** eylem türünü yapılandırın.

    1. Bildirimleri almak için abonelik sahipleri, katkıda bulunanlar ve okuyucular seçmek üzere "e-posta Azure Resource Manager rolü" nü seçin.

    2. İsteğe bağlı olarak, uyarı tetiklendiğinde çağırılabilmesi istiyorsanız **Web kancası** alanında GEÇERLI bir URI sağlayın.

    3. Tamamlandığında **Tamam ' ı** seçin.

    :::image type="content" source="./media/howto-alert-on-metrics/10-action-group-type.png" alt-text="Eylem grubu":::

11. Bir uyarı kuralı adı, açıklaması ve önem derecesi belirtin.

    :::image type="content" source="./media/howto-alert-on-metrics/11-name-description-severity.png" alt-text="Ad açıklaması ve önem derecesi belirtin"::: 

12. Uyarı oluşturmak için **Uyarı kuralı oluştur** ' u seçin.

    Birkaç dakika içinde, uyarı etkin ve daha önce açıklandığı gibi tetikler.

## <a name="manage-your-alerts"></a>Uyarılarınızı yönetme

Bir uyarı oluşturduktan sonra, bunu seçebilir ve aşağıdaki eylemleri gerçekleştirebilirsiniz:

* Ölçüm eşiğini ve Bu uyarıyla ilgili olan önceki günün gerçek değerlerini gösteren bir grafik görüntüleyin.
* Uyarı kuralını **düzenleyin** veya **silin** .
* Bildirimleri almayı geçici olarak durdurmak veya yeniden başlatmak istiyorsanız, uyarıyı **devre dışı bırakın** veya **etkinleştirin** .

## <a name="next-steps"></a>Sonraki adımlar

* [Uyarılarda Web kancalarını yapılandırma](../../azure-monitor/alerts/alerts-webhooks.md)hakkında daha fazla bilgi edinin.
* Hizmetinizin kullanılabilir olduğundan ve yanıt verebilmesini sağlamak için [ölçüm koleksiyonuna genel bakış](../../azure-monitor/data-platform.md) alın.