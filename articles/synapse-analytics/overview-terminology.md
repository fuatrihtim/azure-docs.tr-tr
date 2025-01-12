---
title: Terminoloji-Azure SYNAPSE Analytics
description: Azure SYNAPSE Analytics aracılığıyla başvuru kılavuzu yürüyen Kullanıcı
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 11/18/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 828f37030ae567cacbaad25849b7ba24c561c20c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98132775"
---
# <a name="azure-synapse-analytics-terminology"></a>Azure SYNAPSE Analytics terminolojisi

Bu belge, Azure SYNAPSE Analytics 'in temel kavramlarında size rehberlik eder.

## <a name="basics"></a>Temel Bilgiler

**SYNAPSE çalışma alanı** , Azure 'da bulut tabanlı kurumsal analizler gerçekleştirmek için güvenli kılınabilir bir işbirliği sınırıdır. Çalışma alanı belirli bir bölgeye dağıtılır ve ilişkili bir ADLS 2. hesabına ve dosya sistemine sahiptir (geçici verileri depolamak için). Çalışma alanı bir kaynak grubu altında.

Bir çalışma alanı SQL ve Apache Spark ile analiz gerçekleştirmenize olanak tanır. SQL ve Spark Analytics için kullanılabilen kaynaklar SQL ve Spark **havuzlarında** düzenlenir. 

## <a name="linked-services"></a>Bağlı hizmetler

Bir çalışma alanı, herhangi bir sayıda **bağlı hizmeti** içerebilir ve bu, dış kaynaklara bağlanmak için çalışma alanı için gereken bağlantı bilgilerini tanımlayan bağlantı dizelerini içerir.

## <a name="synapse-sql"></a>Synapse SQL

**SYNAPSE SQL** , SYNAPSE çalışma alanında T-SQL tabanlı analizler yapabilme olanağıdır. SYNAPSE SQL 'in iki tüketim modeli vardır: adanmış ve sunucusuz.  Adanmış model için **ADANMıŞ SQL havuzları** kullanın. Bir çalışma alanı bu havuzlardan herhangi bir sayıda olabilir. Sunucusuz modeli kullanmak için **SUNUCUSUZ SQL havuzlarını** kullanın. Her çalışma alanı bu havuzlardan birine sahiptir.

SYNAPSE Studio içinde **SQL komut dosyaları** OLUŞTURUP çalıştırarak SQL havuzlarıyla çalışabilirsiniz.

## <a name="apache-spark-for-synapse"></a>SYNAPSE için Apache Spark

Spark Analytics 'i kullanmak için SYNAPSE çalışma alanınızda **sunucusuz Apache Spark havuzları** oluşturun ve kullanın. Spark havuzunu kullanmaya başladığınızda, çalışma alanları bu oturumla ilişkili kaynakları işlemek için bir **Spark oturumu** oluşturur. 

SYNAPSE içinde Spark kullanmanın iki yolu vardır:
* Veri veri bilimi ve Mühendisliği yapmak için **Spark Not defterleri** Scala, Pyspark, C# ve mini kullanılan SQL
* Jar dosyalarını kullanarak Batch Spark işlerinin çalıştırılmasına yönelik **Spark iş tanımları** .

## <a name="pipelines"></a>Pipelines

İşlem hatları Azure SYNAPSE 'in veri tümleştirmesi sağladığı, verileri hizmetler arasında taşımanızı ve etkinlikleri düzenlemenizi sağlar.

* İşlem **hattı** , bir görevi birlikte gerçekleştiren etkinliklerin mantıksal gruplandırmasıdır.
* **Etkinlikler** , verileri kopyalama, bir not defteri veya bir SQL betiği çalıştırma gibi veriler üzerinde gerçekleştirilecek eylemleri bir işlem hattı içinde tanımlar.
* **Veri akışları** , SYNAPSE Spark kullanan bir veri dönüştürmesi gerçekleştirmek için kod içermeyen bir deneyim sunan belirli bir etkinlik türüdür.
* **Trigger** -bir işlem hattı yürütür. El ile veya otomatik olarak çalıştırılabilir (zamanlama, pencere veya olay tabanlı)
* **Tümleştirme veri kümesi** -bir etkinlikte giriş ve çıkış olarak kullanılacak verileri işaret eden veya başvuruda bulunan verilerin adlandırılmış görünümü. Bu, bağlı bir hizmete aittir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Synapse Analytics ile çalışmaya başlama](get-started.md)
* [Çalışma alanı oluşturma](quickstart-create-workspace.md)
* [Sunucusuz SQL havuzu kullanma](quickstart-sql-on-demand.md)

