---
title: Visual Studio 2019 'yi yükler
description: SYNAPSE SQL için Visual Studio ve SQL Server geliştirme araçları 'nı (SSDT) yükler
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/11/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.openlocfilehash: ab70ec4f7f2c313c2ae4efcc6d1ad994fbec8b03
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585579"
---
# <a name="getting-started-with-visual-studio-2019"></a>Visual Studio 2019’u kullanmaya başlama

Visual Studio **2019** SQL Server veri araçları (SSDT), aşağıdakileri yapabilmenizi sağlayan tek bir araçtır:

- Uygulamaları bağlama, sorgulama ve geliştirme
- Tablolar, görünümler, saklı yordamlar ve vb. dahil olmak üzere veri modelinizdeki tüm nesneleri görsel olarak araştırmak için Nesne Gezgini 'nden yararlanın.
- Nesneleriniz için T-SQL veri tanımlama dili (DDL) betikleri oluşturun
- SSDT veritabanı projeleriyle durum tabanlı bir yaklaşım kullanarak veri Ambarınızı geliştirme
- Veritabanı projenizi git gibi kaynak denetim sistemleriyle tümleştirin Azure Repos
- Azure DevOps gibi Otomasyon sunucularıyla sürekli tümleştirme ve dağıtım işlem hatları ayarlama

## <a name="install-visual-studio-2019"></a>Visual Studio 2019 'yi yükler

Visual Studio **16,3 ve üstünü** indirmek ve yüklemek için bkz. [Visual Studio 2019 'yi indirme](https://visualstudio.microsoft.com/downloads/) . Yüklemesi sırasında veri depolama ve işleme iş yükünü seçin. Visual Studio 2019 için tek başına SSDT yüklemesi artık gerekli değildir.

## <a name="unsupported-features-in-ssdt"></a>SSDT’de desteklenmeyen özellikler

SYNAPSE SQL için özellik sürümlerinin SSDT desteğinin olmadığı durumlar vardır. Şu özellikler Şu anda desteklenmiyor:


- [Iş yükü yönetimi](sql-data-warehouse-workload-management.md) -iş yükü grupları ve sınıflandırıcılar
- [Satır düzeyi güvenlik](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (tablo değerli işlevler dahil)
  - Desteklenen özelliği almak için bir [destek bileti veya oy](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) verin.
  - Desteklenen özelliği almak için bir [destek bileti veya oy](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) verin.
- [Kimlik sütunu](/sql/t-sql/statements/create-table-transact-sql-identity-property?view=azure-sqldw-latest&preserve-view=true) olan tablolar
- Belirli T-SQL özellikleri, örneğin:
   - [STRING_AGG](/sql/t-sql/functions/string-agg-transact-sql) DIZE işlevindeki Group yan tümcesi *içinde* .

## <a name="next-steps"></a>Sonraki adımlar

SSDT 'nin en son sürümüne sahip olduğunuza göre, SQL havuzunuza [bağlanmaya](sql-data-warehouse-query-visual-studio.md) hazırsınız demektir.