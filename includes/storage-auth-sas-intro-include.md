---
title: include dosyası
description: include dosyası
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/20/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: aec1faa4de1149f08fb6fbc1cc5bf3aa2ab6becd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "75371794"
---
Paylaşılan erişim imzası (SAS) Depolama hesabınızdaki kapsayıcılara ve bloblara sınırlı erişim sağlamanıza olanak sağlar. SAS oluşturduğunuzda, bir istemcinin hangi Azure depolama kaynakları erişimine izin verileceğini, bu kaynaklarda sahip oldukları izinleri ve SAS 'ın ne kadar süreyle geçerli olduğunu de içeren kısıtlamalarını belirtirsiniz.

Her SAS bir anahtarla imzalanır. SAS 'yi iki şekilde imzalayabilirsiniz:

- Azure Active Directory (Azure AD) kimlik bilgileri kullanılarak oluşturulmuş bir anahtarla oluşturulur. Azure AD kimlik bilgileriyle imzalanmış bir SAS, *Kullanıcı temsili* SAS 'dir.
- Depolama hesabı anahtarıyla. Depolama hesabı anahtarıyla bir *HIZMET SAS* ve *Hesap SAS* 'si imzalanır.

Bir Kullanıcı temsili SAS, depolama hesabı anahtarıyla imzalanmış bir SAS için üstün güvenlik sağlar. Microsoft, mümkün olduğunda bir Kullanıcı temsili SAS kullanılmasını önerir. Daha fazla bilgi için bkz. [paylaşılan erişim imzaları (SAS) ile verilere sınırlı erişim verme](../articles/storage/common/storage-sas-overview.md).
