---
title: Azure Spring Cloud Hizmeti için güvenlik denetimleri
description: Azure yay bulut hizmetinde yerleşik olarak bulunan güvenlik denetimlerini kullanın.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 62a0bd19f6b10bbe6561f5587ed85d4d1e5880b3
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878804"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Azure Spring Cloud Hizmeti için güvenlik denetimleri

**Bu makale şu şekilde geçerlidir:** ✔️ Java ✔️ C #

Güvenlik denetimleri Azure Spring bulut hizmetinde yerleşik olarak bulunur.

Güvenlik denetimi, hizmetin güvenlik açıklarını önleme, algılama ve yanıtlama yeteneğine katkıda bulunan bir Azure hizmeti kalitesi veya özelliğidir.  Her denetim için, hizmetin Şu anda kullanımda olup olmadığını belirtmek için *Evet* veya *Hayır* ' ı kullanıyoruz.  Hizmet için geçerli olmayan bir denetim için *N/A* kullandık. 

**Veri koruma güvenlik denetimleri**

| Güvenlik denetimi | Evet/Hayır | Notlar | Belgeler |
|:-------------|:-------|:-------------------------------|:----------------------|
| Rest 'te sunucu tarafı şifreleme: Microsoft tarafından yönetilen anahtarlar | Yes | Kaynak ve yapıtları, yapılandırma sunucusu ayarlarını, uygulama ayarlarını ve kalıcı depolama alanındaki verileri karşıya yükleyen kullanıcı, Azure depolama 'da depolanır ve bu, bekleyen içeriği otomatik olarak şifreler.<br><br>Yapılandırma sunucusu önbelleği, karşıya yüklenen kaynaktan oluşturulan çalışma zamanı ikilileri ve uygulama ömrü boyunca uygulama günlükleri, bekleyen içeriği otomatik olarak şifreleyen Azure yönetilen diskine kaydedilir.<br><br>Karşıya yüklenen kullanıcının oluşturduğu kapsayıcı görüntüleri, bekleyen resim içeriğini otomatik olarak şifreleyen Azure Container Registry kaydedilir. | [Bekleyen veri için Azure Depolama şifrelemesi](../storage/common/storage-service-encryption.md)<br><br>[Azure yönetilen disklerinin sunucu tarafı şifrelemesi](../virtual-machines/disk-encryption.md)<br><br>[Azure Container Registry kapsayıcı görüntüsü depolaması](../container-registry/container-registry-storage.md) |
| Geçici olarak şifreleme | Yes | Kullanıcı uygulaması genel uç noktaları varsayılan olarak gelen trafik için HTTPS kullanır. |  |
| Şifrelenmiş API çağrıları | Yes | Azure yay bulut hizmetini yapılandırmaya yönelik yönetim çağrıları, HTTPS üzerinden Azure Resource Manager çağrıları aracılığıyla oluşur. | [Azure Resource Manager](../azure-resource-manager/index.yml) |

**Ağ erişim güvenliği denetimleri**

| Güvenlik denetimi | Evet/Hayır | Notlar | Belgeler |
|:-------------|:-------|:-------------------------------|:----------------------|
| Hizmet etiketi | Yes | Azure Spring Cloud uygulamalarına giden trafiğe izin vermek için [ağ güvenlik gruplarında](../virtual-network/network-security-groups-overview.md#security-rules) veya [Azure Güvenlik duvarında](../firewall/service-tags.md)giden ağ erişim denetimleri tanımlamak üzere **azurespringcloud** Service etiketini kullanın.<br><br>*Note:* Şu anda yalnızca yeni Azure yay bulut hizmeti örneği, 2020/07/14 sonrasında **Azurespringcloud** Service etiketi destekleniyor. | [Hizmet etiketleri](../virtual-network/service-tags-overview.md) |

## <a name="next-steps"></a>Sonraki adımlar

* [Hızlı başlangıç: ilk Azure Spring Cloud uygulamanızı dağıtın](spring-cloud-quickstart.md)