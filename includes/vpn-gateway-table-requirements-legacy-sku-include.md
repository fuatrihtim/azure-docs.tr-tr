---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5b9e036816aa532d32b1b4305ef6ae646ae05bae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "67188260"
---
Aşağıdaki tabloda PolicyBased ve RouteBased VPN Gateway gereksinimleri listelenmektedir. Bu tablo hem Resource Manager, hem de klasik dağıtım modellerine uygulanır. Klasik modelde, PolicyBased VPN ağ geçitleri statik ağ geçitleriyle aynıdır ve rota tabanlı ağ geçitleri, dinamik ağ geçitleriyle aynıdır.

|  | **PolicyBased temel VPN Gateway** | **RouteBased temel VPN Gateway** | **RouteBased standart VPN Gateway** | **RouteBased yüksek performans VPN Gateway** |
| --- | --- | --- | --- | --- |
| **Siteden Siteye bağlantı   (S2S)** |PolicyBased VPN yapılandırması |RouteBased VPN yapılandırması |RouteBased VPN yapılandırması |RouteBased VPN yapılandırması |
| **Noktadan Siteye bağlantı (P2S**) |Desteklenmez |Destekleniyor (S2S ile birlikte var olabilir) |Destekleniyor (S2S ile birlikte var olabilir) |Destekleniyor (S2S ile birlikte var olabilir) |
| **Kimlik doğrulama yöntemi** |Önceden paylaşılan anahtar |S2S bağlantısı için önceden paylaşılan anahtar, P2S bağlantısı için sertifikalar |S2S bağlantısı için önceden paylaşılan anahtar, P2S bağlantısı için sertifikalar |S2S bağlantısı için önceden paylaşılan anahtar, P2S bağlantısı için sertifikalar |
| **S2S bağlantılarının en yüksek sayısı** |1 |10 |10 |30 |
| **P2S bağlantılarının en yüksek sayısı** |Desteklenmez |128 |128 |128 |
| **Etkin yönlendirme desteği (BGP)** |Desteklenmez |Desteklenmez |Desteklenir |Desteklenir |
