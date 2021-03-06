---
title: Przypisz zmienne
description: Wskazówki dotyczące przypisywania zmiennych T-SQL w Azure SQL Data Warehouse tworzenia rozwiązań.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 1ae5285a8d1cf6fa391c082d0196b213e6b6a9c5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692760"
---
# <a name="assigning-variables-in-azure-sql-data-warehouse"></a>Przypisywanie zmiennych w Azure SQL Data Warehouse

Wskazówki dotyczące przypisywania zmiennych T-SQL w Azure SQL Data Warehouse tworzenia rozwiązań.

## <a name="setting-variables-with-declare"></a>Ustawianie zmiennych przy użyciu deklaracji DECLARE

Zmienne w SQL Data Warehouse są ustawiane przy użyciu instrukcji `DECLARE` lub instrukcji `SET`. Inicjowanie zmiennych przy użyciu deklaracji jest jednym z najbardziej elastycznych metod ustawiania wartości zmiennej w SQL Data Warehouse.

```sql
DECLARE @v  int = 0
;
```

Można również użyć DECLARE, aby ustawić więcej niż jedną zmienną jednocześnie. Nie można użyć opcji SELECT ani UPDATE, aby wykonać następujące czynności:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Nie można zainicjować i użyć zmiennej w tej samej instrukcji DECLARE. W celu zilustrowania punktu Poniższy przykład jest **niedozwolony,** ponieważ @p1 jest inicjowany i używany w tej samej instrukcji DECLARE. Poniższy przykład zawiera błąd.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Ustawianie wartości przy użyciu zestawu

SET to wspólna metoda ustawiania pojedynczej zmiennej.

Następujące instrukcje są prawidłowymi sposobami ustawiania zmiennej przy użyciu zestawu:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Można ustawić tylko jedną zmienną naraz z ZESTAWem. Jednak operatory złożone są dozwolone.

## <a name="limitations"></a>Ograniczenia

Nie można używać aktualizacji do przypisywania zmiennych.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej porad programistycznych, zobacz [Omówienie projektowania](sql-data-warehouse-overview-develop.md).
