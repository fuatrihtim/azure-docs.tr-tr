---
title: Azure statik Web Apps önizlemede kotalar
description: Azure statik Web Apps önizlemesi ile ilişkili kotalar hakkında bilgi edinin
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 1bf9a2cf7af8b18994ef2473c11176b8aea74f6e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042734"
---
# <a name="quotas-in-azure-static-web-apps-preview"></a>Azure statik Web Apps önizlemede kotalar

Azure statik Web Apps önizlemesi için aşağıdaki kotalar mevcuttur.

> [!IMPORTANT]
> Azure statik Web Apps genel önizlemede ve üretim kullanımı için tasarlanmamıştır.

| Özellik                     | Ücretsiz plan        |
|-----------------------------|------------------|
| Dahil edilen bant genişliği          | ayda 100 GB |
| Fazla kullanım bant genişliği           | Kullanılamaz      |
| Azure aboneliği başına uygulamalar | 10               |
| Uygulama boyutu                    | 250 MB           |
| Üretim öncesi ortamları | 3                |
| Özel etki alanları              | 1                |
| Yetkilendirme<br><br>Özel roller ve yönlendirme kuralları ile | En fazla 25 Son Kullanıcı davet edilen ve atanan roller |
| Azure İşlevleri             | Kullanılabilir        |
| SLA                         | Yok             |

## <a name="github-storage"></a>GitHub depolama alanı

GitHub eylemleri ve paketleri, kendi kotalar kümesine sahip GitHub Storage kullanır. Azure statik Web Apps sitesi oluşturduğunuzda GitHub, dağıtımdan sonra bile sitenizin yapıtları depolar.

Daha ayrıntılı bilgi için aşağıdaki kaynaklara bakın:

- [Eylemler depolama alanını yönetme](https://github.community/t5/GitHub-Actions/Managing-Actions-storage-space/td-p/38944)
- [GitHub eylemleri için faturalandırma hakkında](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/about-billing-for-github-actions#about-billing-for-github-actions)
- [GitHub eylemleri için harcama limitinizi yönetme](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/managing-your-spending-limit-for-github-actions)

## <a name="next-steps"></a>Sonraki adımlar

- [Genel Bakış](overview.md)
