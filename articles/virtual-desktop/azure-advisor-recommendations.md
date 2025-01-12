---
title: Azure Advisor Windows sanal masaüstü Izlenecek yol-Azure
description: Windows sanal masaüstü için Azure Advisor önerilerini çözümleme.
author: Heidilohr
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 760e0212d2d863e6b869c23c2e523a0e056a28ed
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90033039"
---
# <a name="how-to-resolve-azure-advisor-recommendations"></a>Azure Advisor önerilerini çözme

Bu makalede, Windows sanal masaüstü için Azure Danışmanı 'nda görüntülenen önerileri nasıl giderebileceğinizi açıklanmaktadır.

## <a name="no-validation-environment-enabled"></a>"Doğrulama ortamı etkin değil"

>[!div class="mx-imgBorder"]
>![Azure Advisor Işletimsel üstün sayfasının ekran görüntüsü. "Doğrulama ortamı etkin değil" önerisi kırmızı renkle vurgulanır.](media/no-validation-environment.png)

Bu öneri, Işlemsel üstün kullanım altında görünür. Öneri aşağıdakine benzer bir uyarı iletisi de göstermelidir:

"Bu abonelikte etkin bir doğrulama ortamınız yok. Konak havuzlarınızı yaptığınızda, Özellikler sekmesinde "doğrulama ortamı" için **Hayır** ' ı seçmiş olursunuz. Windows sanal masaüstü hizmeti dağıtımları aracılığıyla iş sürekliliği sağlamak için, olası sorunları test etmek üzere doğrulama ortamı olan en az bir konak havuzuna sahip olduğunuzdan emin olun. "

Bu uyarı iletisini, ana bilgisayar havuzlarınızdaki bir doğrulama ortamını etkinleştirerek bir yere gidebilirsiniz.

Doğrulama ortamını etkinleştirmek için:

1. Azure portal giriş sayfanıza gidin ve değiştirmek istediğiniz konak havuzunu seçin.

2. Ardından, bir üretim ortamından doğrulama ortamına değiştirmek istediğiniz konak havuzunu seçin.

3. Konak havuzunuzdaki sol sütundaki **Özellikler** ' i seçin. Sonra, "doğrulama ortamı" görene kadar aşağı kaydırın. **Evet**' i seçin ve ardından **Uygula**' yı seçin.

>[!div class="mx-imgBorder"]
>![Özellikler menüsünün ekran görüntüsü. "Doğrulama ortamı" kırmızı renkle vurgulanır ve "Evet" balonu seçilidir.](media/validation-yes.png)

Bu değişiklikler, uyarının hemen sürmesine neden olmaz, ancak sonunda kaybolacaktır. Azure Advisor günde iki kez güncelleştirilir. Bundan sonra, öneriyi el ile erteleyebilir veya kapatabilirsiniz. Önerinin kendi kendine gitmesini öneririz. Bu şekilde, Azure Danışmanı, ayarlar değiştikçe herhangi bir sorun olup olmadığını bilmenize izin verebilir.

## <a name="not-enough-production-non-validation-environments-enabled"></a>"Yeterli üretim (doğrulama olmayan) ortamları etkin değil"

Bu öneri, Işlemsel üstün kullanım altında görünür.

Bu öneri için aşağıdaki nedenlerden biri için uyarı iletisi görünür:

- Doğrulama ortamınızda çok fazla konak havuzu var.
- Herhangi bir üretim ana bilgisayar havuzunuz yok.

Kullanıcıların, bir doğrulama ortamında ana bilgisayar havuzlarının yarısından daha az olması önerilir.

Bu uyarıyı çözmek için:

1. Azure portal giriş sayfanıza gidin.

2. Doğrulamadan üretime geçmek istediğiniz konak havuzlarını seçin.

3. Konak havuzunuzdaki ekranın sağ tarafındaki sütunun **Özellikler** sekmesini seçin. Sonra, "doğrulama ortamı" görene kadar aşağı kaydırın. **Hayır**' ı seçin ve ardından **Uygula**' yı seçin.

>[!div class="mx-imgBorder"]
>![Özellikler menüsünün ekran görüntüsü. "Doğrulama ortamı" kırmızı renkle vurgulanır ve "Hayır" balonu seçilidir.](media/validation-no.png)

Bu değişiklikler, uyarının hemen sürmesine neden olmaz, ancak sonunda kaybolacaktır. Azure Advisor günde iki kez güncelleştirilir. Bundan sonra, öneriyi el ile erteleyebilir veya kapatabilirsiniz. Önerinin kendi kendine gitmesini öneririz. Bu şekilde, Azure Danışmanı, ayarlar değiştikçe herhangi bir sorun olup olmadığını bilmenize izin verebilir.

## <a name="not-enough-links-are-unblocked-to-successfully-implement-your-vm"></a>"VM 'nizi başarıyla uygulamak için yeterli bağlantı engellenmiyor"

Bu öneri, Işlemsel üstün kullanım altında görünür.

Sanal makinenizin (VM) düzgün çalıştığından emin olmak için belirli URL 'Lerin engellemesini kaldırmanız gerekir. Listeyi [GÜVENLI URL listesinde](safe-url-list.md)görebilirsiniz. URL 'Lerin engeli engellenmemişse, VM 'niz düzgün çalışmaz.

Bu öneriyi çözümlemek için [GÜVENLI URL listesindeki](safe-url-list.md)tüm URL 'lerin engelini kaldırmayı unutmayın. URL 'Leri kaldırmak için hizmet etiketi veya FQDN etiketleri kullanabilirsiniz.

## <a name="propose-new-recommendations"></a>Yeni öneriler önerin

Öneriler için fikirler göndererek Azure Advisor 'ı geliştirmemize yardımcı olabilirsiniz. Önerinizi başka bir kullanıcının zor bir noktadan çıkmasına yardımcı olabilir. Bir öneri göndermek için [UserVoice forumumuza](https://windowsvirtualdesktop.uservoice.com/forums/930847-azure-advisor-recommendations) gidin ve gönderim formunu doldurun. Formu doldururken, bize mümkün olduğunca fazla ayrıntı verdiğinizden emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Yaygın sorunları çözme hakkında daha ayrıntılı kılavuzlar arıyorsanız, [sorun giderme genel bakış, geri bildirim ve Windows sanal masaüstü desteği için desteğe](troubleshoot-set-up-overview.md)göz atın.
