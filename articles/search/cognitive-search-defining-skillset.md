---
title: Tworzenie zestawu umiejętności
titleSuffix: Azure Cognitive Search
description: Zdefiniuj wyodrębnianie danych, przetwarzanie języka naturalnego lub procedurę analizy obrazów, aby wzbogacać i wyodrębnić informacje o strukturze z danych do użycia w usłudze Azure Wyszukiwanie poznawcze.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 43251783cbcd6501562913b7b9cafb4f9f7cb3f1
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754569"
---
# <a name="how-to-create-a-skillset-in-an-ai-enrichment-pipeline-in-azure-cognitive-search"></a>Jak utworzyć zestawu umiejętności w potoku wzbogacenia AI na platformie Azure Wyszukiwanie poznawcze 

Wyodrębnianie i wzbogacanie danych w celu przeszukiwania ich w usłudze Azure Wyszukiwanie poznawcze. Wywołajemy możliwości wydobywania i wzbogacania *umiejętności poznawcze*, połączone w *zestawu umiejętności* , do których odwołuje się podczas indeksowania. Zestawu umiejętności może używać [wbudowanych umiejętności](cognitive-search-predefined-skills.md) lub umiejętności niestandardowych (zobacz [przykład: Tworzenie niestandardowej umiejętności w potoku wzbogacania AI](cognitive-search-create-custom-skill-example.md) , aby uzyskać więcej informacji).

W tym artykule dowiesz się, jak utworzyć potok wzbogacania dla umiejętności, których chcesz użyć. Zestawu umiejętności jest dołączany do [indeksatora](search-indexer-overview.md)wyszukiwanie poznawcze platformy Azure. Jedną z części projektu potoku, omówionego w tym artykule, jest konstrukcja zestawu umiejętności. 

> [!NOTE]
> Inna część projektu potoku określa indeksator, pokryty w [następnym kroku](#next-step). Definicja indeksatora zawiera odwołanie do zestawu umiejętności oraz mapowania pól, które są używane do łączenia danych wejściowych z docelowym indeksem.

Najważniejsze kwestie do zapamiętania:

+ Można mieć tylko jeden zestawu umiejętności na indeksator.
+ Zestawu umiejętności musi mieć co najmniej jedną umiejętność.
+ Można utworzyć wiele umiejętności tego samego typu (na przykład wariantów umiejętności analizy obrazów).

## <a name="begin-with-the-end-in-mind"></a>Zacznij od zakończenia

Zalecany początkowy krok polega na tym, które dane mają zostać wyodrębnione z danych pierwotnych, oraz w jaki sposób używać tych danych w rozwiązaniu wyszukiwania. Utworzenie ilustracji całego potoku wzbogacania może pomóc w zidentyfikowaniu niezbędnych kroków.

Załóżmy, że interesuje Cię przetwarzanie zestawu komentarzy analityków finansowych. Dla każdego pliku, chcesz wyodrębnić nazwy firmowe i ogólne tonacji komentarzy. Warto również napisać niestandardowy wzbogacający, który używa usługi wyszukiwanie jednostek Bing, aby znaleźć dodatkowe informacje o firmie, takie jak rodzaj firmy, w której jest zaangażowana firma. Zasadniczo, chcesz wyodrębnić informacje takie jak następujące, które są indeksowane dla każdego dokumentu:

| rekord — tekst | towarzystw | tonacji | opisy firmy |
|--------|-----|-----|-----|
|sample-record| ["Microsoft", "LinkedIn"] | 0,99. | ["Microsoft Corporation to amerykańska firma wielonarodowych technologii...", "LinkedIn to sieć społecznościowa zorientowana na działalność biznesową i"... "]

Poniższy diagram ilustruje hipotetyczny potok wzbogacania:

![Hipotetyczny potok wzbogacania](media/cognitive-search-defining-skillset/sample-skillset.png "Hipotetyczny potok wzbogacania")


Po uzyskaniu odpowiedniego pomysłu w potoku można wyrazić zestawu umiejętności, który zawiera te kroki. Funkcja zestawu umiejętności jest wyrażana w przypadku przekazywania definicji indeksatora do usługi Azure Wyszukiwanie poznawcze. Aby dowiedzieć się więcej na temat przekazywania indeksatora, zapoznaj się z [dokumentacją indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer).


Na diagramie krok *łamania dokumentu* odbywa się automatycznie. Zasadniczo usługa Azure Wyszukiwanie poznawcze wie, jak otworzyć dobrze znane pliki i utworzyć pole *zawartości* zawierające tekst wyodrębniony z każdego dokumentu. Białe pola są wbudowanymi wzbogacami, a kropkowane pole "wyszukiwanie jednostek Bing" reprezentuje obiekt wzbogacany, który tworzysz. Tak jak pokazano, zestawu umiejętności zawiera trzy umiejętności.

## <a name="skillset-definition-in-rest"></a>Definicja zestawu umiejętności w REST

Zestawu umiejętności jest definiowana jako tablica umiejętności. Każda umiejętność definiuje źródło danych wejściowych i nazwę wygenerowanego wyjścia. Za pomocą [interfejsu API REST Create zestawu umiejętności](https://docs.microsoft.com/rest/api/searchservice/create-skillset)można zdefiniować zestawu umiejętności, który odpowiada poprzedniemu diagramowi: 

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

```json
{
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "Calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
  ]
}
```

## <a name="create-a-skillset"></a>Tworzenie zestawu umiejętności

Podczas tworzenia zestawu umiejętności można podać opis, aby zestawu umiejętności samodzielny dokument. Opis jest opcjonalny, ale przydatny do śledzenia zawartości zestawu umiejętności. Ponieważ zestawu umiejętności jest dokumentem JSON, który nie zezwala na komentarze, należy użyć elementu `description`.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

Następny element w zestawu umiejętności jest tablicą umiejętności. Każdą umiejętność można traktować jako pierwotną wersję wzbogacania. Każda umiejętność wykonuje niewielkie zadanie w tym potoku wzbogacania. Każdy z nich przyjmuje dane wejściowe (lub zestaw danych wejściowych) i zwraca niektóre wyniki. W następnych sekcjach zawarto informacje na temat sposobu określania umiejętności wbudowanych i niestandardowych, a także do łączenia się z danymi wejściowymi i wyjściowymi. Dane wejściowe mogą pochodzić z danych źródłowych lub z innej umiejętności. Wyniki można mapować do pola w indeksie wyszukiwania lub użyć jako danych wejściowych w celu uzyskania kwalifikacji podrzędnych.

## <a name="add-built-in-skills"></a>Dodawanie wbudowanych umiejętności

Przyjrzyjmy się pierwszej umiejętności, która stanowi wbudowaną [umiejętność rozpoznawania jednostek](cognitive-search-skill-entity-recognition.md):

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    }
```

* Każda wbudowana umiejętność ma `odata.type`, `input`i `output` właściwości. Właściwości specyficzne dla umiejętności zawierają dodatkowe informacje dotyczące tej umiejętności. W przypadku rozpoznawania jednostek `categories` jest jedną jednostką dla ustalonego zestawu typów jednostek, które może rozpoznać przedmieszczony model.

* Każda umiejętność powinna mieć ```"context"```. Kontekst reprezentuje poziom, na którym operacje mają miejsce. W powyższej umiejętności kontekst jest całym dokumentem, co oznacza, że umiejętność rozpoznawania jednostki jest wywoływana raz dla dokumentu. Dane wyjściowe są również tworzone na tym poziomie. Dokładniej, ```"organizations"``` są generowane jako element członkowski ```"/document"```. W obszarze umiejętności podrzędne można odwołać się do nowo utworzonych informacji jako ```"/document/organizations"```.  Jeśli pole ```"context"``` nie jest jawnie ustawione, domyślnym kontekstem jest dokument.

* Umiejętność ma jedno wejście o nazwie "text" ze źródłowym wejściem ustawionym na ```"/document/content"```. Umiejętność (rozpoznawanie jednostek) działa w polu *zawartość* każdego dokumentu, który jest standardowym polem utworzonym przez indeksator usługi Azure Blob. 

* Umiejętność ma jedno wyjście o nazwie ```"organizations"```. Dane wyjściowe istnieją tylko podczas przetwarzania. Aby połączyć te dane wyjściowe z danymi wejściowymi w celu uzyskania kwalifikacji podrzędnych, należy odwołać się do danych wyjściowych jako ```"/document/organizations"```.

* W przypadku określonego dokumentu wartość ```"/document/organizations"``` jest tablicą organizacji wyodrębnionych z tekstu. Przykład:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

Niektóre sytuacje odwołują się do każdego elementu tablicy osobno. Załóżmy na przykład, że chcesz przekazać każdy element ```"/document/organizations"``` niezależnie do innej umiejętności (na przykład niestandardowego wzbogacania wyszukiwania jednostek Bing). Można odwołać się do każdego elementu tablicy, dodając gwiazdkę do ścieżki: ```"/document/organizations/*"``` 

Druga umiejętność wyodrębniania tonacji jest zgodna z tym samym wzorcem, co pierwszy wzbogacający. Przyjmuje ```"/document/content"``` jako dane wejściowe i zwraca wynik tonacji dla każdego wystąpienia zawartości. Ponieważ nie ustawiono jawnie pola ```"context"```, dane wyjściowe (mySentiment) są teraz elementami podrzędnymi ```"/document"```.

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
```

## <a name="add-a-custom-skill"></a>Dodaj niestandardową umiejętność

Odwołaj strukturę niestandardowego elementu wzbogacania wyszukiwania jednostek Bing:

```json
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "This skill calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
```

Ta definicja to [niestandardowa umiejętność](cognitive-search-custom-skill-web-api.md) wywołująca internetowy interfejs API w ramach procesu wzbogacania. W przypadku każdej organizacji identyfikowanej przez funkcję rozpoznawania jednostek ta umiejętność wywołuje internetowy interfejs API, aby znaleźć opis tej organizacji. Aranżacja, kiedy należy wywołać interfejs API sieci Web i jak przepływać otrzymane informacje, jest obsługiwana wewnętrznie przez aparat wzbogacania. Jednak Inicjalizacja niezbędna do wywołania tego niestandardowego interfejsu API musi być podana w formacie JSON (na przykład identyfikator URI, httpHeaders i oczekiwane dane wejściowe). Aby uzyskać wskazówki dotyczące tworzenia niestandardowego interfejsu API sieci Web dla potoku wzbogacania, zobacz [How to define a Custom Interface](cognitive-search-custom-skill-interface.md).

Zwróć uwagę, że pole "context" jest ustawione na ```"/document/organizations/*"``` przy użyciu gwiazdki, co oznacza, że krok wzbogacania jest wywoływany *dla każdej* organizacji w obszarze ```"/document/organizations"```. 

Dane wyjściowe — w tym przypadku opis firmy jest generowany dla każdej identyfikowanej organizacji. W przypadku odwoływania się do opisu w kroku podrzędnym (na przykład w przypadku wyodrębniania kluczowych fraz) użyj ścieżki ```"/document/organizations/*/description"```. 

## <a name="add-structure"></a>Dodaj strukturę

Zestawu umiejętności generuje strukturalne informacje z danych bez struktury. Rozważmy następujący przykład:

*"W czwartym kwartale firma Microsoft zarejestrował $1 100 000 000 w przychodach z serwisu LinkedIn, firma sieci społecznościowej ją zakupiła w ubiegłym roku. Pozyskiwanie umożliwia firmie Microsoft łączenie możliwości serwisu LinkedIn z funkcjami CRM i Office. Akcjonariusze są przyjemnością z postępem do tej pory ".*

Prawdopodobnie wynik będzie wygenerował strukturę podobną do poniższej ilustracji:

![Przykładowa struktura wyjściowa](media/cognitive-search-defining-skillset/enriched-doc.png "Przykładowa struktura wyjściowa")

Do tej pory Ta struktura była tylko wewnętrzna, tylko pamięć i używana tylko w indeksach Wyszukiwanie poznawcze platformy Azure. Dodanie sklepu z bazami danych umożliwia zapisanie wzbogacania w celu użycia poza wyszukiwaniem.

## <a name="add-a-knowledge-store"></a>Dodawanie sklepu merytorycznego

[Sklep merytoryczny](knowledge-store-concept-intro.md) jest funkcją w wersji zapoznawczej na platformie Azure wyszukiwanie poznawcze do zapisywania wzbogaconego dokumentu. Magazyn wiedzy tworzony przez użytkownika w ramach konta usługi Azure Storage jest repozytorium, w którym są używane wzbogacone dane. 

Definicja sklepu merytorycznego jest dodawana do zestawu umiejętności. Przewodnik po całym procesie można znaleźć [w temacie Tworzenie sklepu z bazami danych w usłudze REST](knowledge-store-create-rest.md).

```json
"knowledgeStore": {
  "storageConnectionString": "<an Azure storage connection string>",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
          "storageContainer": "containername",
          "source": "/document/EnrichedShape/",
          "key": "/document/Id"
        }
      ]
    }
  ]
}
```

Można zapisać wzbogacone dokumenty jako tabele z niehierarchicznymi relacjami zachowywane lub jako dokumenty JSON w usłudze BLOB Storage. Dane wyjściowe z dowolnego umiejętności w zestawu umiejętności mogą być źródłem jako dane wejściowe dla projekcji. Jeśli szukasz danych w określonym kształcie, zaktualizowana [umiejętność kształtu](cognitive-search-skill-shaper.md) może teraz modelować typy złożone do użycia. 

<a name="next-step"></a>

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już potok wzbogacania i umiejętności, Kontynuuj, [jak odwoływać się do adnotacji w zestawu umiejętności](cognitive-search-concept-annotations-syntax.md) lub [Jak mapować dane wyjściowe do pól w indeksie](cognitive-search-output-field-mapping.md). 
