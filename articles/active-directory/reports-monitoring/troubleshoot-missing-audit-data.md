---
title: Etkinlik günlüklerinde eksik verileri giderme | Microsoft Docs
description: Azure Active Directory etkinlik günlüklerindeki eksik verilere yönelik bir çözüm sağlar.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f48c26a65314e2f23513fba155f07db3805a516
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93123073"
---
# <a name="troubleshoot-missing-data-in-the-azure-active-directory-activity-logs"></a>Sorun giderme: Azure Active Directory etkinlik günlüklerindeki eksik veriler 

## <a name="i-cant-find-audit-logs-for-recent-actions-in-the-azure-portal"></a>Azure portalda son eylemlerin denetim günlüklerini bulamıyorum

### <a name="symptoms"></a>Belirtiler

Azure portalında bazı eylemler gerçekleştirdim ve bu eylemlerin denetim günlüklerini `Activity logs > Audit Logs` dikey penceresinde görmeyi umuyordum, ancak bulamıyorum.

 ![Ekran görüntüsü denetim günlüğü girdilerini gösterir.](./media/troubleshoot-missing-audit-data/01.png)
 
### <a name="cause"></a>Nedeni

Eylemler, etkinlik günlüklerinde hemen görünmez. Aşağıdaki tabloda etkinlik günlüklerinin gecikme süreleri gösterilmiştir. 

| Rapor | Gecikme süresi (P95) | Gecikme süresi (P99) |
|--------|---------------|---------------|
| Dizin denetimi | 2 dk. | 5 dk. |
| Oturum açma etkinliği | 2 dk. | 5 dk. |

### <a name="resolution"></a>Çözüm

15 dakika ile iki saat arasında bekleyin ve eylemlerin günlükte görüntülenip görüntülenmediğine bakın. İki saatten sonra da günlükler görünmüyorsa sorunla ilgilenebilmemiz için [destek bileti oluşturun](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="i-cant-find-recent-user-sign-ins-in-the-azure-active-directory-sign-ins-activity-log"></a>Azure Active Directory oturum açma etkinlik günlüğünde son kullanıcı oturum açma işlemlerini bulamıyorum

### <a name="symptoms"></a>Belirtiler

Azure portalında kısa bir süre önce oturum açtım ve bu oturum açma işleminin günlük girişlerini `Activity logs > Sign-ins` dikey penceresinde görmeyi umuyordum, ancak bulamıyorum.

 ![Ekran görüntüsü etkinlik günlüğündeki oturum açma işlemlerini gösterir.](./media/troubleshoot-missing-audit-data/02.png)
 
### <a name="cause"></a>Nedeni

Eylemler, etkinlik günlüklerinde hemen görünmez. Aşağıdaki tabloda etkinlik günlüklerinin gecikme süreleri gösterilmiştir. 

| Rapor | Gecikme süresi (P95) | Gecikme süresi (P99) |
|--------|---------------|---------------|
| Dizin denetimi | 2 dk. | 5 dk. |
| Oturum açma etkinliği 2 dakika | 5 dk. |

### <a name="resolution"></a>Çözüm

15 dakika ile iki saat arasında bekleyin ve eylemlerin günlükte görüntülenip görüntülenmediğine bakın. İki saatten sonra da günlükler görünmüyorsa sorunla ilgilenebilmemiz için [destek bileti oluşturun](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Azure portalda 30 günden daha eski rapor verilerini görüntüleyemiyorum

### <a name="symptoms"></a>Belirtiler

Azure portalda 30 günden daha eski oturum açma ve denetim verilerini görüntüleyemiyorum. Neden? 

 ![Ekran görüntüsü Tarih menüsünü gösterir.](./media/troubleshoot-missing-audit-data/03.png)

### <a name="cause"></a>Nedeni

Lisansınıza bağlı olarak, etkinlik raporları Azure Active Directory Actions tarafından aşağıdaki sürelerde depolanır:

| Rapor           | Azure AD Ücretsiz | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ---           | ---                 | ---                 |
| Dizin Denetimi  |  7 gün       | 30 gün             | 30 gün             |
| Oturum Açma Etkinliği | Kullanılamıyor. Kendi oturum açma etkinliklerinize bireysel kullanıcı profili dikey penceresinden 7 gün boyunca erişebilirsiniz | 30 gün | 30 gün             |

Daha fazla bilgi için bkz. [Azure Active Directory rapor bekletme ilkeleri](reference-reports-data-retention.md).  

### <a name="resolution"></a>Çözüm

Verileri 30 günden daha uzun bir süre boyunca saklamak için iki seçeneğiniz vardır. [Azure AD Raporlama API'lerini](concept-reporting-api.md) kullanarak verileri program aracılığıyla alabilir ve bir veritabanında kaydedebilirsiniz. Alternatif olarak denetim günlüklerini Splunk veya SumoLogic gibi bir üçüncü taraf SIEM sistemiyle tümleştirebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD rapor saklama süresi](reference-reports-data-retention.md).
* [Azure Active Directory raporlama gecikmeleri](reference-reports-latencies.md).
* [Raporlama hakkında sss Azure Active Directory](reports-faq.md).

