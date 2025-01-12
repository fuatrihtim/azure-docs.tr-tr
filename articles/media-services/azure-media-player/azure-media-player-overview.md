---
title: Azure Media Player genel bakış
description: Azure Media Player hakkında daha fazla bilgi edinin.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 9b99e24e47da69aa0f081e056230f4b0c1aa6bb1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93321733"
---
# <a name="azure-media-player-overview"></a>Azure Media Player'a genel bakış #

Azure Media Player, çok çeşitli tarayıcılarda ve cihazlarda [Microsoft Azure Media Services](https://azure.microsoft.com/services/media-services/) medya içeriğini yürüten bir web video oyuncusu. Azure Media Player, zenginleştirilmiş bir uyarlamalı akış deneyimi sağlamak üzere HTML5, medya kaynağı uzantıları (MSE) ve şifreli Medya Uzantıları (EME) gibi sektör standartlarını kullanır.  Bu standartlar bir cihazda veya bir tarayıcıda kullanılabilir olmadığında, Azure Media Player Flash ve Silverlight 'ı geri dönüş teknolojisi olarak kullanır. Kullanılan kayıttan yürütme teknolojisinden bağımsız olarak, geliştiricilerin API 'Lere erişmek için birleştirilmiş bir JavaScript arabirimi vardır ve Azure Media Services tarafından sunulan içeriklere ek bir çaba olmadan çok sayıda cihazda ve tarayıcıda oynatılmasına olanak tanır.

Microsoft Azure Media Services, içeriğin kayıttan yürütülmesi için DASH, Kesintisiz Akış ve HLS akış biçimleriyle birlikte sunulmasını sağlar. Azure Media Player, bu çeşitli biçimleri dikkate alır ve platform/tarayıcı özelliklerine göre en iyi bağlantıyı otomatik olarak yürütür. Microsoft Azure Media Services Ayrıca ortak şifreleme (PlayReady veya Widevine) veya AES-128 bit zarfı şifrelemesi olan varlıkların dinamik şifrelemesini de sağlar. Azure Media Player, uygun şekilde yapılandırıldığında PlayReady ve AES-128 bit şifreli içeriğin şifresini çözmeye izin verir.  Oynatıcıyı nasıl yapılandıracağınızı anlamak için, [korumalı içerik](azure-media-player-protected-content.md) bölümüne bakın.

Yeni özellikler istemek, fikir ve geri bildirim sağlamak, [Azure Media Player Için UserVoice](https://aka.ms/ampuservoice)'a gönderin. Ve belirli sorunlarla karşılaşırsanız, sorularınız varsa veya herhangi bir hata bulursanız, bize bir satır bırakın ampinfo@microsoft.com .

> [!NOTE]
> Azure Media Player yalnızca Azure Media Services medya akışlarını desteklediğine lütfen unutmayın.

## <a name="license"></a>Lisans ##

Azure Media Player lisanslanır ve Azure Media Player için Microsoft yazılımı lisans koşulları 'nda özetlenen koşullara tabidir. Tam koşullar için [Lisans dosyasına](/legal/azure-media-player/azure-media-player-license) bakın. Daha fazla bilgi için [gizlilik bildirimine](https://www.microsoft.com/en-us/privacystatement/default.aspx)bakın.

Telif hakkı 2015 Microsoft Corporation.

## <a name="next-steps"></a>Sonraki adımlar ##

- [Hızlı başlangıç Azure Media Player](azure-media-player-quickstart.md)
