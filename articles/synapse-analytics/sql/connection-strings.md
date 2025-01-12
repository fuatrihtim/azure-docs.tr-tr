---
title: SYNAPSE SQL için bağlantı dizeleri
description: SYNAPSE SQL için bağlantı dizeleri
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 5ce411f3a2f3be162f9af16422d20b3e8b536fee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98120929"
---
# <a name="connection-strings-for-synapse-sql"></a>SYNAPSE SQL için bağlantı dizeleri

SYNAPSE SQL 'e, [ADO.net](/dotnet/framework/data/adonet/), [ODBC](/sql/connect/odbc/windows/microsoft-odbc-driver-for-sql-server-on-windows), [php](/sql/connect/php/overview-of-the-php-sql-driver?f=255&MSPPError=-2147217396)ve [JDBC](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server)gibi çeşitli farklı uygulama protokolleriyle bağlanabilirsiniz. Her protokol için bazı bağlantı dizesi örnekleri aşağıda verilmiştir. 

Bağlantı dizenizi oluşturmak için Azure portal de kullanabilirsiniz.  Bağlantı dizenizi Azure portal kullanarak derlemek için, veritabanı dikey penceresine gidin, *temel parçalar* altında *veritabanı bağlantı dizelerini göster*' i seçin.

## <a name="sample-adonet-connection-string"></a>Örnek ADO.NET bağlantı dizesi

```csharp
Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>Örnek ODBC bağlantı dizesi

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>Örnek PHP bağlantı dizesi

```PHP
Server: {your_server}.sql.azuresynapse.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.sql.azuresynapse.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.sql.azuresynapse.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>Örnek JDBC bağlantı dizesi

```Java
jdbc:sqlserver://yourserver.sql.azuresynapse.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.sql.azuresynapse.net;loginTimeout=30;
```

> [!NOTE]
> Bağlantı zaman aşımı süresini 300 saniye olarak ayarlamayı göz önünde bulundurun.

## <a name="recommendations"></a>Öneriler

**Sunucusuz SQL havuzu** sorgularını yürütmek için önerilen Araçlar [Azure Data Studio](get-started-azure-data-studio.md) ve Azure SYNAPSE Studio.

## <a name="next-steps"></a>Sonraki adımlar

Visual Studio ve diğer uygulamalarla analizlerinizi sorgulamaya başlamak için bkz. [Visual Studio Ile sorgulama](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).