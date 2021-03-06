---
title: Limity Azure Cosmos DB Gremlin
description: Dokumentacja referencyjna dotycząca ograniczeń środowiska uruchomieniowego aparatu grafu
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 10/04/2019
ms.author: lbosq
ms.openlocfilehash: 76ad787990c355d29613c05ca9fce31885a2eccc
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029851"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Azure Cosmos DB limity Gremlin
W tym artykule omówiono limity Azure Cosmos DB aparacie Gremlin i wyjaśniono, jak mogą one wpływać na przechodzenie klientów.

Cosmos DB Gremlin jest oparty na infrastrukturze Cosmos DB. Z tego powodu wszystkie limity wyjaśnione w [Azure Cosmos DB limity usług](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) nadal obowiązują. 

## <a name="limits"></a>Limity

Po osiągnięciu limitu Gremlin funkcja przechodzenia zostanie anulowana z **kodem x-MS-status-** 429 wskazującym na błąd ograniczania. Aby uzyskać więcej informacji, zobacz [nagłówki odpowiedzi serwera Gremlin](gremlin-limits.md) .

**Zasób**    | **Limit domyślny** | **Wyjaśnienie**
--- | --- | ---
*Długość skryptu* | **64 KB** | Maksymalna długość skrypt przechodzenia języka Gremlin na żądanie.
*Głębokość operatora* | **400** |  Całkowita liczba unikatowych kroków przechodzenia. Na przykład ```g.V().out()``` ma liczbę operatorów równą 2: V () i out (), ```g.V('label').repeat(out()).times(100)``` ma głębokość operatora 3: V (), Repeat () i out (), ponieważ ```.times(100)``` jest parametrem do ```.repeat()``` operatora.
*Stopień równoległości* | **32** | Maksymalna liczba partycji magazynu, dla których wykonano zapytania w jednym żądaniu skierowanym do warstwy magazynu. Ten limit będzie miał wpływ na wykresy z setkami partycji.
*Limit powtórzeń* | **32** | Maksymalna liczba iteracji, które może wykonywać operator ```.repeat()```. Każda iteracja ```.repeat()``` kroku w większości przypadków uruchamia przechodzenie w pierwszej kolejności, co oznacza, że każde przechodzenie jest ograniczone do maksymalnie 32 przeskoków między wierzchołkami.
*Limit czasu przechodzenia* | **30 sekund** | Przechodzenie zostanie anulowane, gdy przekroczy ten czas. Graf usługi Cosmos DB to baza danych OLTP z większością przejść ukończonych w ciągu milisekund. Aby uruchamiać zapytania OLAP na Cosmos DB grafie, należy użyć [Apache Spark](https://azure.microsoft.com/services/cosmos-db/) z [ramkami danych grafów](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) i [łącznikiem Cosmos DB Spark](https://github.com/Azure/azure-cosmosdb-spark).
*Limit czasu bezczynnego połączenia* | **1 godzina** | Czas, przez jaki usługa Gremlin będzie utrzymywać otwarte połączenia protokołu WebSocket. Pakiety Keep-Alive TCP lub żądania Keep-Alive protokołu HTTP nie zwiększają cykl życia połączeń poza tym limitem. Aparat grafu Cosmos DB uznaje, że połączenia protokołu WebSocket są bezczynne, jeśli na tym komputerze nie ma aktywnych żądań Gremlin.
*Tokeny zasobów na godzinę* | **100** | Liczba unikatowych tokenów zasobów używanych przez klientów języka Gremlin do nawiązywania połączenia z kontem Gremlin w regionie. Gdy aplikacja przekracza limit godzin unikatowych tokenów, w następnym żądaniu uwierzytelniania zostanie zwrócona wartość `"Exceeded allowed resource token limit of 100 that can be used concurrently"`.

## <a name="next-steps"></a>Następne kroki
* [Azure Cosmos DB nagłówki odpowiedzi Gremlin](gremlin-headers.md) 
* [Azure Cosmos DB tokeny zasobów z Gremlin](how-to-use-resource-tokens-gremlin.md)
