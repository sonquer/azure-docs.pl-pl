---
title: Synonimy dla rozszerzenia zapytania w indeksie wyszukiwania
titleSuffix: Azure Cognitive Search
description: Utwórz mapę synonimów, aby rozszerzyć zakres zapytania wyszukiwania na indeks Wyszukiwanie poznawcze platformy Azure. Zakres jest rozszerzony w celu uwzględnienia odpowiednich warunków na liście.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7c94ad096cf7d0d01bf2076f6748b49cf4ae1bb4
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794221"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Synonimy w usłudze Azure Wyszukiwanie poznawcze

Synonimy w aparatach wyszukiwania kojarzą równoważne warunki, które niejawnie rozszerzają zakres zapytania, bez użytkownika, który ma faktycznie podawać ten termin. Na przykład, uwzględniając termin "Dog" i "synonimy" "Canine" i "Puppy", wszystkie dokumenty zawierające "Dog", "Canine" lub "Puppy" będą należeć do zakresu zapytania.

Na platformie Azure Wyszukiwanie poznawcze rozwinięcie synonimu odbywa się w czasie wykonywania zapytania. Można dodać do usługi mapy synonimów bez zakłócania istniejących operacji. Do definicji pola można dodać właściwość **synonymMaps** bez konieczności odbudowywania indeksu.

## <a name="create-synonyms"></a>Utwórz synonimy

Brak obsługi portalu do tworzenia synonimów, ale można użyć interfejsu API REST lub zestawu .NET SDK. Aby rozpocząć pracę z usługą REST, zalecamy [Korzystanie z programu Poster](search-get-started-postman.md) i formułowanie żądań przy użyciu tego interfejsu API: [Tworzenie map synonimów](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map). W C# przypadku deweloperów można rozpocząć pracę z [dodawaniem synonimów w usłudze Azure poznawcze wyszukiwanie C#przy użyciu ](search-synonyms-tutorial-sdk.md).

Opcjonalnie, jeśli używasz [kluczy zarządzanych przez klienta](search-security-manage-encryption-keys.md) do szyfrowania po stronie usługi — w spoczynku, możesz zastosować tę ochronę do zawartości mapy synonimów.

## <a name="use-synonyms"></a>Użyj synonimów

Na platformie Azure Wyszukiwanie poznawcze obsługa synonimów opiera się na mapach synonimów, które definiujesz i przekazujesz do usługi. Te mapy stanowią zasób niezależny (na przykład indeksy lub źródła danych) i mogą być używane przez dowolne pola do przeszukiwania w dowolnym indeksie w usłudze wyszukiwania.

Mapy synonimów i indeksy są obsługiwane niezależnie. Po zdefiniowaniu mapy synonimów i przekazaniu jej do usługi można włączyć funkcję synonimu w polu, dodając nową właściwość o nazwie **synonymMaps** w definicji pola. Tworzenie, aktualizowanie i usuwanie mapy synonimów jest zawsze operacją całego dokumentu, co oznacza, że nie można tworzyć, aktualizować ani usuwać części mapowania synonimów przyrostowo. Aktualizacja nawet jednego wpisu wymaga ponownego załadowania.

Dołączanie synonimów do aplikacji wyszukiwania jest procesem dwuetapowym:

1.  Dodaj mapę synonimów do usługi wyszukiwania za pomocą poniższych interfejsów API.  

2.  Skonfiguruj pole z możliwością wyszukiwania, aby użyć mapy synonimów w definicji indeksu.

Możesz utworzyć wiele map synonimów dla aplikacji wyszukiwania (na przykład według języka, jeśli aplikacja obsługuje wielojęzykową bazę klienta). Obecnie pole może korzystać tylko z jednego z nich. Właściwość synonymMaps pola można aktualizować w dowolnym momencie.

### <a name="synonymmaps-resource-apis"></a>Interfejsy API zasobów SynonymMaps

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Dodawanie lub aktualizowanie mapy synonimów w ramach usługi przy użyciu funkcji POST lub PUT.

Mapy synonimów są przekazywane do usługi za pośrednictwem funkcji POST lub PUT. Każda reguła musi być rozdzielone znakiem nowego wiersza ("\n"). Można zdefiniować maksymalnie 5 000 reguł na potrzeby mapowania synonimów w ramach bezpłatnej usługi i reguł 10 000 we wszystkich innych jednostkach SKU. Każda reguła może mieć do 20 rozszerzeń.

Mapy synonimów muszą znajdować się w formacie Apache Solr, który wyjaśniono poniżej. Jeśli masz istniejący słownik synonimów w innym formacie i chcesz go używać bezpośrednio, daj nam znać w usłudze [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

Można utworzyć nową mapę synonimów przy użyciu protokołu HTTP POST, jak w poniższym przykładzie:

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

    {
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

Alternatywnie można użyć PUT i określić nazwę mapy synonimów w identyfikatorze URI. Jeśli mapa synonimu nie istnieje, zostanie utworzona.

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

    {
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Format synonimu Apache Solr

Format Solr obsługuje równoważne i jawne mapowania synonimów. Reguły mapowania są zgodne ze specyfikacją filtra synonimu typu "open source" w artykule Apache Solr, opisanym w tym dokumencie: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Poniżej znajduje się przykładowa reguła dotycząca odpowiedników synonimów.
```
USA, United States, United States of America
```

Po powyższej regule zapytanie wyszukiwania "USA" zostanie rozszerzone na "USA" lub "Stany Zjednoczone" lub "Stany Zjednoczone Ameryki".

Jawne mapowanie jest oznaczone strzałką "= >". W przypadku określenia warunku sekwencja zapytania wyszukiwania odpowiadającego lewej stronie elementu "= >" zostanie zastąpiona alternatywami po prawej stronie. Mając poniższą regułę, Wyszukaj zapytania "Waszyngton", "pranie". lub "WA" wszystkie zostaną ponownie wpisane do "WA". Jawne mapowanie ma zastosowanie tylko w określonym kierunku i nie zapisuje w tym przypadku zapytania "WA" do "Waszyngton".
```
Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>Wyświetl listę list synonimów w ramach usługi.

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>Uzyskaj mapę synonimów w ramach usługi.

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>Usuń mapę synonimów w ramach usługi.

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Skonfiguruj pole z możliwością wyszukiwania, aby użyć mapy synonimów w definicji indeksu.

Nowe właściwości pola **synonymMaps** można użyć do określenia mapy synonimów, która ma być używana dla pola z możliwością wyszukiwania. Mapy synonimów to zasoby poziomu usług i mogą być przywoływane przez dowolne pola indeksu w ramach usługi.

    POST https://[servicename].search.windows.net/indexes?api-version=2019-05-06
    api-key: [admin key]

    {
       "name":"myindex",
       "fields":[
          {
             "name":"id",
             "type":"Edm.String",
             "key":true
          },
          {
             "name":"name",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"en.lucene",
             "synonymMaps":[
                "mysynonymmap"
             ]
          },
          {
             "name":"name_jp",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"ja.microsoft",
             "synonymMaps":[
                "japanesesynonymmap"
             ]
          }
       ]
    }

**synonymMaps** można określić dla pól z możliwością wyszukiwania typu "EDM. String" lub "Collection (EDM. String").

> [!NOTE]
> Można mieć tylko jedną mapę synonimów dla każdego pola. Jeśli chcesz używać wielu map synonimów, daj nam znać w usłudze [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="impact-of-synonyms-on-other-search-features"></a>Wpływ synonimów na inne funkcje wyszukiwania

Funkcja synonimy ponownie zapisuje oryginalne zapytanie przy użyciu synonimów z operatorem OR. Z tego powodu wyróżnianie trafień i profile oceniania traktują pierwotny termin i synonimy jako równoważne.

Funkcja synonimu dotyczy zapytań wyszukiwania i nie ma zastosowania do filtrów ani aspektów. Podobnie sugestie są oparte tylko na oryginalnym okresie; w odpowiedzi nie pojawiają się dopasowania synonimów.

Rozszerzenia synonimów nie mają zastosowania do terminów wyszukiwania w postaci symboli wieloznacznych; wyrażenia prefix, rozmyte i wyrażeń regularnych nie są rozwinięte.

Jeśli trzeba wykonać pojedyncze zapytanie, które stosuje rozwinięcie synonimu i symbole wieloznaczne, wyrażenie regularne lub rozmyte, można połączyć zapytania przy użyciu składni lub. Na przykład, aby połączyć synonimy z symbolami wieloznacznymi dla prostej składni zapytania, termin będzie `<query> | <query>*`.

Jeśli masz istniejący indeks w środowisku programistycznym (nieprodukcyjnym), eksperymentuj z małym słownikiem, aby dowiedzieć się, jak dodanie synonimów zmienia środowisko wyszukiwania, w tym wpływ na profile oceniania, wyróżnianie trafień i sugestie.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie mapy synonimów](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)