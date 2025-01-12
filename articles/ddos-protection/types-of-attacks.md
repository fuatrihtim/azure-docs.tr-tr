---
title: Azure DDoS koruması standart azaltmalarına yönelik saldırı türleri
description: Azure DDoS koruması standardına göre hangi tür saldırıları koruduğunu öğrenin.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 8b213755aeed1590df3d1f5e91f44796c13c7711
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94991783"
---
# <a name="types-of-ddos-attacks-overview"></a>DDoS saldırılarına genel bakış türleri

DDoS koruma standardı aşağıdaki tür saldırıları hafifletmenize yardımcı olabilir:

- **Volumetric saldırıları**: Bu saldırılar, ağ katmanını önemli miktarda meşru trafik ile taşmıştır. Bunlar UDP floods, ampmi, floods ve diğer sahte paket floods içerirler. DDoS koruma standardı, bu potansiyel çok gigabaytlık saldırıları, Azure 'un küresel ağ ölçeklendirilmesi ve bunları otomatik olarak yaparak, bu olası çok kiracılı saldırıları azaltır.
- **Protokol saldırıları**: Bu saldırılar, katman 3 ve katman 4 protokol yığınındaki zayıf bir durumu kötüye vererek bir hedefi işlemez. SYN taşma saldırıları, yansıma saldırıları ve diğer protokol saldırıları içerirler. DDoS koruması standardı, bu saldırıları azaltır, istemciyle etkileşime girerek ve kötü amaçlı trafiği engelleyerek kötü amaçlı ve meşru trafik arasında ayrım gerçekleştirebilir. 
- **Kaynak (uygulama) katmanı saldırıları**: Bu saldırılar, ana bilgisayarlar arasında veri aktarımını kesintiye uğratan Web uygulaması paketlerini hedeflemelidir. HTTP protokol ihlalleri, SQL ekleme, siteler arası komut dosyası ve diğer katman 7 saldırıları içerirler. Azure [Application Gateway Web uygulaması güvenlik duvarı](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)gibi bir Web uygulaması güvenlik duvarı ve bu saldırılara karşı savunma sağlamak Için DDoS koruma standardı kullanın. Ayrıca, [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)'nde bulunan üçüncü taraf Web uygulaması güvenlik duvarı teklifleri de mevcuttur.

## <a name="azure-ddos-protection-standard"></a>Azure DDoS Koruması Standart

DDoS koruma standardı sanal makineler, yük dengeleyiciler ve uygulama ağ geçitleri ile ilişkili genel IP adresleri dahil bir sanal ağdaki kaynakları korur. Application Gateway Web uygulaması güvenlik duvarı ile veya genel IP 'si olan bir sanal ağda dağıtılan bir üçüncü taraf Web uygulaması güvenlik duvarı ile birlikte kullanıldığında, DDoS koruma standardı, katman 7 hafifletme özelliği için tam katman 3 sağlayabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [DDoS koruma planı oluşturma](manage-ddos-protection.md)hakkında bilgi edinin.