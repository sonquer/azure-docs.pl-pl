---
title: Klauzula WHERE w Azure Cosmos DB
description: Informacje o klauzuli WHERE języka SQL dla Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: tisande
ms.openlocfilehash: 31653b598f0f3a79bf7f9c09231b1d111f167a16
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982233"
---
# <a name="where-clause-in-azure-cosmos-db"></a>Klauzula WHERE w Azure Cosmos DB

Opcjonalna klauzula WHERE (`WHERE <filter_condition>`) określa warunek (y), które muszą spełniać źródłowe elementy JSON, aby zapytanie zawierało je w wynikach. Element JSON musi oszacować określone warunki, aby `true` do uwzględnienia w wyniku. Warstwa indeksu używa klauzuli WHERE do określenia najmniejszego podzestawu elementów źródłowych, które mogą być częścią wyniku.
  
## <a name="syntax"></a>Składnia
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argumenty

- `<filter_condition>`  
  
   Określa warunek do spełnienia dokumentów, które mają zostać zwrócone.  
  
- `<scalar_expression>`  
  
   Wyrażenie reprezentujące wartość ma zostać obliczony. Aby uzyskać szczegółowe informacje, zobacz [wyrażenia skalarne](sql-query-scalar-expressions.md) .  
  

## <a name="remarks"></a>Uwagi
  
  W kolejności dokumentu, które mają zostać zwrócone wyrażenie określone jako filtr warunek musi zwrócić wartość true. Tylko wartość logiczną PRAWDA będzie spełniać warunek jakakolwiek inna wartość: Niezdefiniowany, null, wartość false, liczby, tablicy lub obiektu nie spełnia warunku. 

## <a name="examples"></a>Przykłady

Następujące zapytanie żąda elementów, które zawierają Właściwość `id` której wartość jest `AndersenFamily`. Wyklucza każdy element, który nie ma właściwości `id` lub którego wartość nie jest zgodna `AndersenFamily`.

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są następujące:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>Wyrażenia skalarne w klauzuli WHERE

W poprzednim przykładzie pokazano proste zapytanie dotyczące równości. Interfejs API SQL obsługuje również różne [wyrażenia skalarne](sql-query-scalar-expressions.md). Najczęściej używane są wyrażenia binarne i jednoargumentowe. Odwołania do właściwości z obiektu JSON źródła są również prawidłowymi wyrażeniami.

Można użyć następujących obsługiwanych operatorów binarnych:  

|**Typ operatora**  | **Wartości** |
|---------|---------|
|Arytmetyczny | +,-,*,/,% |
|Bitowy    | \|, &, ^, <<, >>, >>> (wypełnienie zerami, przesunięcie w prawo) |
|Logiczny    | AND, OR, NOT      |
|Porównanie | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|Ciąg     |  \|\| (łączenie) |

Następujące zapytania używają operatorów binarnych:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

Można również użyć operatorów jednoargumentowych +,-, ~, a nie w zapytaniach, jak pokazano w następujących przykładach:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Można również użyć odwołań do właściwości w zapytaniach. Na przykład `SELECT * FROM Families f WHERE f.isRegistered` zwraca element JSON zawierający Właściwość `isRegistered` o wartości równej `true`. Każda inna wartość, taka jak `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>`lub `<array>`, wyklucza element z wyniku.

## <a name="next-steps"></a>Następne kroki

- [Rozpoczęcie pracy](sql-query-getting-started.md)
- [IN — słowo kluczowe](sql-query-keywords.md#in)
- [FROM — klauzula](sql-query-from.md)