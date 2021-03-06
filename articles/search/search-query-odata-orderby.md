---
title: Zamówienie OData — według odwołania
titleSuffix: Azure Cognitive Search
description: Dokumentacja dotycząca składni i języka dla korzystania z polecenia order-by w usłudze Azure Wyszukiwanie poznawcze zapytania.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 99ec639b88f3334530243242aadfa0ab52a40df0
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113146"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>Składnia $orderby OData na platformie Azure Wyszukiwanie poznawcze

 Możesz użyć [parametru **$OrderBy** OData](query-odata-filter-orderby-syntax.md) , aby zastosować niestandardową kolejność sortowania dla wyników wyszukiwania w usłudze Azure wyszukiwanie poznawcze. W tym artykule opisano szczegółowo składnię **$OrderBy** . Aby uzyskać ogólne informacje na temat używania **$OrderBy** podczas prezentowania wyników wyszukiwania, zobacz jak korzystać [z wyników wyszukiwania w usłudze Azure wyszukiwanie poznawcze](search-pagination-page-layout.md).

## <a name="syntax"></a>Składnia

Parametr **$OrderBy** akceptuje listę rozdzielonych przecinkami, do 32 **klauzul ORDER-by**. Składnia klauzuli Order-by została opisana przez następujący EBNF ([Extended back-Naura form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

Dostępny jest również interaktywny diagram składni:

> [!div class="nextstepaction"]
> [Diagram składni OData dla Wyszukiwanie poznawcze platformy Azure](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Zapoznaj się z informacjami o [składni wyrażenia OData dla usługi Azure wyszukiwanie poznawcze](search-query-odata-syntax-reference.md) , aby uzyskać pełną EBNF.

Każda klauzula ma kryteria sortowania, opcjonalnie po której następuje kierunek sortowania (`asc` do rosnącego lub `desc` dla malejąco). Jeśli nie określisz kierunku, wartość domyślna to Ascending. Kryterium sortowania może być ścieżką pola `sortable` lub wywołaniem [`geo.distance`](search-query-odata-geo-spatial-functions.md) lub funkcji [`search.score`](search-query-odata-search-score-function.md) .

Jeśli wiele dokumentów ma takie same kryteria sortowania, a funkcja `search.score` nie jest używana (na przykład jeśli sortujesz według pola liczbowego `Rating` i trzy wszystkie dokumenty mają klasyfikację 4), powiązania będą podzielone przez ocenę dokumentu w kolejności malejącej. Gdy wyniki dokumentu są takie same (na przykład w przypadku braku zapytania wyszukiwania pełnotekstowego określonego w żądaniu), względna kolejność dokumentów jest nieokreślona.

Można określić wiele kryteriów sortowania. Kolejność wyrażeń określa ostateczną kolejność sortowania. Na przykład, aby sortować Malejąco według wyniku, po którym następuje klasyfikacja, składnia będzie `$orderby=search.score() desc,Rating desc`.

Składnia `geo.distance` w **$OrderBy** jest taka sama jak w **$Filter**. W przypadku używania `geo.distance` w **$OrderBy**, pole, którego ma dotyczyć, musi być typu `Edm.GeographyPoint` i musi być `sortable`.

Składnia `search.score` w **$OrderBy** jest `search.score()`. Funkcja `search.score` nie przyjmuje żadnych parametrów.

## <a name="examples"></a>Przykłady

Sortuj Hotele rosnąco według stawki bazowej:

    $orderby=BaseRate asc

Sortuj Hotele Malejąco według klasyfikacji, a następnie rosnąco według stawki bazowej (należy pamiętać, że rosnąco jest wartością domyślną):

    $orderby=Rating desc,BaseRate

Sortuj Hotele Malejąco według klasyfikacji, a następnie rosnąco według odległości od danego współrzędnych:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

Sortuj Hotele w kolejności malejącej według wyszukiwania. Ocena i ocena, a następnie w kolejności rosnącej według odległości od danego współrzędnych. Między dwiema hoteli z identycznymi wynikami i ocenami przydatności poniżej znajduje się pierwsza z nich:

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Następne kroki  

- [Jak korzystać z wyników wyszukiwania w usłudze Azure Wyszukiwanie poznawcze](search-pagination-page-layout.md)
- [Omówienie języka wyrażeń OData dla platformy Azure Wyszukiwanie poznawcze](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Wyszukiwanie poznawcze](search-query-odata-syntax-reference.md)
- [Wyszukaj dokumenty &#40;w interfejsie API REST usługi Azure wyszukiwanie poznawcze&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
