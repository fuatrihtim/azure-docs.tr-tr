---
title: "Öğretici: olay verilerini Azure SYNAPSE Analytics 'e geçirme-Azure Event Hubs"
description: Event Hubs yakalanan verileri Azure SYNAPSE Analytics 'e geçirmek için Azure Event Grid ve Işlevlerinin nasıl kullanılacağını açıklar.
services: event-hubs
ms.date: 12/07/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 77a2b35dea57c71e9e6f07056bd106df2ac109b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96854426"
---
# <a name="tutorial-migrate-captured-event-hubs-data-to-azure-synapse-analytics-using-event-grid-and-azure-functions"></a>Öğretici: Event Grid ve Azure Işlevlerini kullanarak yakalanan Event Hubs verilerini Azure SYNAPSE Analytics 'e geçirme
Azure Event Hubs [Capture](./event-hubs-capture-overview.md) , akış verilerini bir Azure Blob depolamada veya Azure Data Lake Storage Event Hubs otomatik olarak yakalamanızı sağlar. Bu öğreticide, [Event Grid](../event-grid/overview.md)tarafından tetiklenen bir Azure işlevi kullanarak yakalanan Event Hubs verilerinin depolama alanından Azure SYNAPSE Analytics 'e nasıl geçirileceği gösterilmektedir.

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](../../includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>Sonraki adımlar 
Eyleme dönüştürülebilir içgörüler edinmek için veri ambarınızla güçlü veri görselleştirme araçları kullanabilirsiniz.

Bu makalede [Azure SYNAPSE Analytics ile Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect) nasıl kullanılacağı gösterilmektedir