---
title: Media Services için medya oyuncuları genel bakış
description: Azure Media Services ile hangi medya yürütücüleri kullanabilirim? Azure Media Player, Shaka ve Video.js şu ana kadar.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 3/08/2021
ms.openlocfilehash: 47ef56a770ca9965cef3e50630be4b69342a038d
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559868"
---
# <a name="media-players-for-media-services"></a>Media Services için medya yürütücüleri

Media Services ile birkaç medya oynatıcı kullanabilirsiniz.

## <a name="azure-media-player"></a>Azure Media Player

Azure Media Player, çok çeşitli tarayıcılar ve cihazlar için bir video oynatıcı olur. Azure Media Player, zenginleştirilmiş bir uyarlamalı akış deneyimi sağlamak üzere HTML5, medya kaynağı uzantıları (MSE) ve şifreli Medya Uzantıları (EME) gibi sektör standartlarını kullanır. Bu standartlar bir cihazda veya bir tarayıcıda kullanılabilir olmadığında, Azure Media Player Flash ve Silverlight 'ı geri dönüş teknolojisi olarak kullanır. Kullanılan kayıttan yürütme teknolojisinden her ne kadar, geliştiricilerin API 'Lere erişmek için birleştirilmiş bir JavaScript arabirimi vardır. Azure Media Services tarafından sunulan içerikler, ek bir çaba olmadan çok çeşitli cihazlarda ve tarayıcılarda çalınabilir.

[Azure Media Player belgelerine](../azure-media-player/azure-media-player-overview.md)bakın.

## <a name="shaka"></a>Shaka

Shaka oynatıcı, uyarlamalı medya için açık kaynaklı bir JavaScript kitaplığıdır. Eklentiler veya Flash kullanmadan bir tarayıcıda Uyarlamalı medya biçimlerini (DASH ve HLS gibi) çalar. Bunun yerine, Shaka Player açık Web standartları medya kaynağı uzantılarını ve şifreli medya uzantılarını kullanır.

[Azure Media Services Shaka oynatıcıyı nasıl kullanacağınızı](how-to-shaka-player.md)öğrenin.

## <a name="videojs"></a>Video.js

Video.js, bir tarayıcıda Uyarlamalı medya biçimlerini (DASH ve HLS gibi) oynatan bir video oynatıcı olur. Video.js, Open Web standartları MediaSource uzantılarını ve şifreli medya uzantılarını kullanır. Masaüstleri ve mobil cihazlarda video oynatmayı destekler. Resmi belgelerini adresinde bulabilirsiniz https://docs.videojs.com/ .

[Video.js oynatıcıyı Azure Media Services Ile nasıl kullanacağınızı](how-to-video-js-player.md)öğrenin.


## <a name="next-steps"></a>Sonraki adımlar ##

- [Hızlı başlangıç Azure Media Player](../azure-media-player/azure-media-player-quickstart.md)