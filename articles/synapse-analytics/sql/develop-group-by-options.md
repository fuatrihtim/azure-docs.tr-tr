---
title: SYNAPSE SQL 'de GROUP BY seçeneklerini kullanma
description: SYNAPSE SQL, farklı gruplandırma seçenekleri uygulayarak çözüm geliştirmeye olanak sağlar.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: c2e1ddbfb87df40a0e3683e7bca7539c26191a7b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671306"
---
# <a name="group-by-options-in-synapse-sql"></a>SYNAPSE SQL 'de gruplandırma ölçütü seçenekleri

SYNAPSE SQL, farklı gruplandırma seçenekleri uygulayarak çözüm geliştirmeye olanak sağlar. 

## <a name="what-group-by-does"></a>Gruplandırma ölçütü

[Group By](/sql/t-sql/queries/select-group-by-transact-sql?view=azure-sqldw-latest&preserve-view=true) T-SQL yan tümcesi, verileri bir Özet satır kümesine toplar.

Sunucusuz SQL havuzu tüm gruplandırma ölçütü seçeneklerini destekler. Adanmış SQL havuzu sınırlı sayıda GROUP BY seçeneğini destekler.

## <a name="group-by-options-supported-in-dedicated-sql-pool"></a>Adanmış SQL havuzunda desteklenen gruplandırma seçenekleri

GROUP BY, adanmış SQL havuzunun desteklemediği bazı seçeneklere sahiptir. Bu seçeneklerde aşağıdaki gibi geçici çözümler bulunur:

* TOPLAMASıYLA gruplandırma ölçütü
* GRUPLANDıRMA KÜMELERI
* KÜP ile gruplandırma ölçütü

### <a name="rollup-and-grouping-sets-options"></a>Toplama ve gruplandırma kümeleri seçenekleri

Buradaki en basit seçenek, açık sözdizimine güvenmek yerine, toplamayı yürütmek için UNıON ALL kullanmaktır. Sonuç tamamen aynıdır

Aşağıdaki örnek, toplama seçeneği ile GROUP BY ifadesini kullanır:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

Önceki örnek, ROLLUP kullanarak aşağıdaki toplamaları ister:

* Ülke ve bölge
* Ülke
* Genel Toplam

TOPLAMAYı değiştirmek ve aynı sonuçları döndürmek için UNıON ALL ' ı kullanabilir ve gerekli toplamaları açıkça belirtebilirsiniz:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

Gruplandırma KÜMELERINI değiştirmek için, örnek ilke uygulanır. Yalnızca görmek istediğiniz toplama düzeyleri için UNıON ALL bölümlerini oluşturmanız yeterlidir.

### <a name="cube-options"></a>Küp seçenekleri

UNıON ALL yaklaşımını kullanarak KÜPLE bir grup oluşturmak mümkündür. Bu sorun, kodun hızlı bir şekilde hızla ve farkında hale gelmesine neden olabilir. Bu sorunu azaltmak için daha gelişmiş bir yaklaşım kullanabilirsiniz.

İlk adım, oluşturmak istediğimiz tüm toplama düzeylerini tanımlayan ' Cube ' öğesini tanımlamaktır. Tüm düzeyleri ürettiği için, iki türetilmiş tablonun çapraz BIRLEŞTIĞI yere göz atın. Kodun geri kalanı biçimlendirme için mevcuttur.

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

Aşağıdaki görüntüde [Create Table sonuçları şöyle](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)gösterilmektedir:

![Küpe göre Gruplandır](./media/develop-group-by-options/develop-group-by-cube.png)

İkinci adım, geçici sonuçları depolamak için bir hedef tablo belirtmektir:

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

Üçüncü adım, toplama işlemi gerçekleştiren sütun küpünün üzerine döngüdür. Sorgu #Cube geçici tablodaki her satır için bir kez çalışır. Sonuçlar #Results geçici tabloda depolanır:

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

Son olarak, #Results geçici tablodan okurken sonuçları döndürebilirsiniz:

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Kodu bölümlere ayırarak ve bir döngü yapısı oluşturarak, kod daha yönetilebilir ve sürdürülebilir hale gelir.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla geliştirme ipucu için bkz. [geliştirmeye genel bakış](develop-overview.md).
