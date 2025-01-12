---
title: Azure gizli bilgi işlem geliştirme araçları
description: Gizli bilgi işlem için uygulama geliştirmek üzere araçları ve kitaplıkları kullanma
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: 0ba6ee92111da66a2118ba4c490b94e5bc9449e0
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551394"
---
# <a name="application-development-on-intel-sgx"></a>Intel SGX üzerinde uygulama geliştirme 


Gizli bilgi işlem altyapısı, belirli araçlar ve yazılımlar gerektirir. Bu sayfa, Intel SGX üzerinde çalışan Azure gizli bilgi işlem sanal makineleri için uygulama geliştirmeyle ilgili kavramların ele alınmaktadır. Bu sayfayı okumadan önce, [ıNTEL SGX sanal makineler ve şifreleme bilgilerini okuyun](confidential-computing-enclaves.md). 

Şifreleme ve yalıtılmış ortamların gücünden yararlanmak için, gizli bilgi işlem desteği olan araçları kullanmanız gerekir. Şifreleme uygulaması geliştirmeyi destekleyen çeşitli araçlar vardır. Örneğin, bu açık kaynaklı çerçeveleri kullanabilirsiniz: 

- [Açık şifreleme yazılım geliştirme seti (OE SDK)](#oe-sdk)
- [Gizli konsorsiyum çatısı (CCF)](#ccf)

## <a name="overview"></a>Genel Bakış

Şifreleme ile oluşturulmuş bir uygulama iki şekilde bölümlenir:

1. "Güvenilmeyen" bir bileşen (konak)
1. "Güvenilir" bir bileşen (şifreleme)


![Uygulama geliştirme](media/application-development/oe-sdk.png)


**Ana bilgisayar** , şifreleme uygulamanızın en üstünde çalıştığı ve güvenilmeyen bir ortam olduğu yerdir. Konakta dağıtılan şifreleme koduna konak tarafından erişilemiyor. 

**Şifreleme,** uygulama kodunun ve önbelleğe alınmış verilerinin/belleğin çalıştırıldığı yerdir. Güvenli hesaplamalar, gizli dizileri ve hassas verileri sağlamak için kuşlar halinde gerçekleşmelidir, korumalı kalır. 


Uygulama tasarımı sırasında, uygulamanın hangi bölümünün kuşkuta çalıştırılması gerektiğini belirlemek ve belirlemek önemlidir. Güvenilen bileşene yerleştirilmesi için seçtiğiniz kod uygulamanızın geri kalanından yalıtılmıştır. Şifreleme başlatıldıktan ve kod belleğe yüklendikten sonra, bu kod güvenilmeyen bileşenlerden okunamaz veya değiştirilemez. 

## <a name="open-enclave-software-development-kit-oe-sdk"></a>Açık şifreleme yazılım geliştirme seti (OE SDK) <a id="oe-sdk"></a>

Bir kuşın içinde çalışan bir kod yazmak istiyorsanız, sağlayıcınız tarafından desteklenen bir kitaplık veya çerçeveyi kullanın. [Open Enclave SDK](https://github.com/openenclave/openenclave) (OE SDK), farklı gizli bilgi işlem özellikli donanımlar üzerinde soyutlama sağlayan açık KAYNAKLı bir SDK 'dir. 

OE SDK 'Sı, herhangi bir CSP üzerinde herhangi bir donanım üzerinde tek bir soyutlama katmanı olacak şekilde oluşturulmuştur. OE SDK 'Sı, Azure gizli bilgi işlem sanal makinelerinin en üstünde, en üstte uygulamalar oluşturmak ve çalıştırmak için kullanılabilir.

## <a name="confidential-consortium-framework-ccf"></a>Gizli konsorsiyum çerçevesi (CCF) <a id="ccf"></a>

[CCF](https://github.com/Microsoft/CCF) , her biri kendi kuşağını çalıştıran, dağıtılmış bir düğüm ağı. Güvenilen düğüm ağı, dağıtılmış bir defter çalıştırmanızı sağlar. Bu, iletişim kuralının kullanması için güvenli ve güvenilir bileşenler sağlar. 

![CCF düğümleri](media/application-development/ccf.png)

Bu açık kaynaklı çerçeve, blok zinciri için yüksek, hassas gizlilik ve konsorsiyum yönetimi sunar. TEEs kullanan her düğüm ile güvenli konsensus ve işlem işleme sağlayabilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar 
- [Bir gizli bilgi işlem DCsv2-Series sanal makine dağıtma](quick-create-portal.md)
- [OE SDK 'sını indirip yükleyin ve uygulama geliştirmeye başlayın](https://github.com/openenclave/openenclave)