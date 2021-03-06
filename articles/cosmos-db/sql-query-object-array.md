---
title: Praca z tablicami i obiektami w Azure Cosmos DB
description: Poznaj składnię SQL, aby utworzyć tablice i obiekty w Azure Cosmos DB. W tym artykule przedstawiono również przykłady wykonywania operacji na obiektach Array
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 5b2801b0a71f04803955e9d8bc18a97133019996
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870925"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Praca z tablicami i obiektami w Azure Cosmos DB

Kluczową funkcją interfejsu API SQL Azure Cosmos DB jest tworzenie tablic i obiektów.

## <a name="arrays"></a>Tablice

Można skonstruować tablice, jak pokazano w następującym przykładzie:

```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

Wyniki są następujące:

```json
    [
      {
        "CityState": [
          "Seattle",
          "WA"
        ]
      },
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```

Możesz również użyć [wyrażenia Array](sql-query-subquery.md#array-expression) , aby utworzyć tablicę z wyników [podzapytania](sql-query-subquery.md) . To zapytanie pobiera wszystkie różne nazwy elementów podrzędnych w tablicy.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a id="Iteration"></a>Iteracja

Interfejs API SQL zapewnia obsługę iteracji w tablicach JSON, a nowa konstrukcja dodana za pośrednictwem [słowa kluczowego in](sql-query-keywords.md#in) w źródle from. W poniższym przykładzie:

```sql
    SELECT *
    FROM Families.children
```

Wyniki są następujące:

```json
    [
      [
        {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        }, 
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

Następne zapytanie wykonuje iterację przez `children` w kontenerze `Families`. Tablica wyjściowa różni się od powyższego zapytania. Ten przykład dzieli `children`i spłaszcza wyniki do pojedynczej tablicy:  

```sql
    SELECT *
    FROM c IN Families.children
```

Wyniki są następujące:

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]
```

Można filtrować więcej według poszczególnych wpisów tablicy, jak pokazano w następującym przykładzie:

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

Wyniki są następujące:

```json
    [{
      "givenName": "Lisa"
    }]
```

Można również agregować wynik iteracji tablicy. Na przykład następujące zapytanie liczy liczbę elementów podrzędnych między wszystkimi rodzinami:

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

Wyniki są następujące:

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a name="next-steps"></a>Następne kroki

- [Rozpoczęcie pracy](sql-query-getting-started.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Łącze](sql-query-join.md)