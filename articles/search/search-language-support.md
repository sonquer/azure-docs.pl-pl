---
title: Indeksowanie wielu języków dla zapytań wyszukiwania innych niż angielskie
titleSuffix: Azure Cognitive Search
description: Platforma Azure Wyszukiwanie poznawcze obsługuje języki 56, wykorzystując analizatory języka od firmy Microsoft i technologii przetwarzania w języku naturalnym.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ca2bc66c755da2011cc7016f37b194caa6200d9a
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793590"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Jak utworzyć indeks dla wielu języków w usłudze Azure Wyszukiwanie poznawcze

Indeksy mogą zawierać pola zawierające zawartość z wielu języków, na przykład tworząc poszczególne pola dla ciągów specyficznych dla języka. W celu uzyskania najlepszych wyników podczas indeksowania i wykonywania zapytań należy przypisać Analizator języka, który zapewnia odpowiednie reguły językowe. 

Usługa Azure Wyszukiwanie poznawcze oferuje duży wybór analizatorów języka z zarówno Lucene, jak i firmy Microsoft, które można przypisać do poszczególnych pól przy użyciu właściwości analizatora. Możesz również określić Analizator języka w portalu, zgodnie z opisem w tym artykule.

## <a name="add-analyzers-to-fields"></a>Dodawanie analizatorów do pól

Analizator języka jest określany podczas tworzenia pola. Dodanie analizatora do istniejącej definicji pola wymaga zastąpienia (i ponownego załadowania) indeksu lub utworzenia nowego pola identycznego z oryginałem, ale z przypisaniem analizatora. Następnie można usunąć nieużywane pole ze swojej wygody.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i Znajdź usługę wyszukiwania.
1. Kliknij przycisk **Dodaj indeks** na pasku poleceń w górnej części pulpitu nawigacyjnego usługi, aby uruchomić nowy indeks, lub Otwórz istniejący indeks, aby ustawić analizator dla nowych pól dodawanych do istniejącego indeksu.
1. Rozpocznij Definiowanie pola, podając nazwę.
1. Wybierz typ danych EDM. String. Tylko pola ciągów są wyszukiwaniem pełnotekstowym.
1. Ustaw atrybut z **możliwością wyszukiwania** , aby włączyć właściwość Analizator. Aby można było używać analizatora języka, pole musi być oparte na tekście.
1. Wybierz jeden z dostępnych analizatorów. 

![Przypisz analizatory języka podczas definiowania pola](media/search-language-support/select-analyzer.png "Przypisz analizatory języka podczas definiowania pola")

Domyślnie wszystkie pola z możliwością wyszukiwania korzystają ze [standardowego analizatora Lucene](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) , który jest językiem niezależny od. Aby wyświetlić pełną listę obsługiwanych analizatorów, zobacz [Dodawanie analizatorów języka do indeksu wyszukiwanie poznawcze platformy Azure](index-add-language-analyzers.md).

Analizatory są przeznaczone do użycia w portalu. Jeśli wymagane jest dostosowanie lub określona konfiguracja filtrów i tokenizatory, należy [utworzyć Analizator niestandardowy](index-add-custom-analyzers.md) w kodzie. Portal nie obsługuje wybierania ani konfigurowania analizatorów niestandardowych.

## <a name="query-language-specific-fields"></a>Zapytania dotyczące pól specyficznych dla języka

Po wybraniu analizatora języka dla pola będzie on używany z każdym indeksem i żądaniem wyszukiwania dla tego pola. Gdy zapytanie jest wydawane dla wielu pól przy użyciu różnych analizatorów, kwerenda zostanie przetworzona niezależnie od przypisanych analizatorów dla każdego pola.

Jeśli jest znany język agenta wysyłającego zapytanie, żądanie wyszukiwania można ograniczyć do określonego pola przy użyciu parametru zapytania **searchFields** . Następujące zapytanie zostanie wygenerowane tylko w odniesieniu do opisu w Polski:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=PolishContent&api-version=2019-05-06`

Możesz wysyłać zapytania do indeksu z portalu przy użyciu [**Eksploratora wyszukiwania**](search-explorer.md) do wklejenia zapytania podobnego do przedstawionego powyżej.

## <a name="boost-language-specific-fields"></a>Zwiększ pola specyficzne dla języka

Czasami język agenta wystawiającego zapytanie nie jest znany, w takim przypadku zapytanie można wystawić dla wszystkich pól jednocześnie. W razie potrzeby Preferencja dla wyników w określonym języku można zdefiniować za pomocą [profilów oceniania](index-add-scoring-profiles.md). W poniższym przykładzie dopasowań Znalezione w opisie w języku angielskim będą oceniane w sposób wyższy względem dopasowań w języku polskim i francuskim:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2019-05-06`

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś deweloperem platformy .NET, pamiętaj, że możesz skonfigurować analizatory języka przy użyciu [zestawu Azure wyszukiwanie poznawcze .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Search) i właściwości [Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet) . 