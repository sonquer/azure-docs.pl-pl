---
title: Niestandardowa umiejętność interfejsu API sieci Web w programie umiejętności
titleSuffix: Azure Cognitive Search
description: Zwiększaj możliwości usługi Azure Wyszukiwanie poznawcze umiejętności, wywołując do interfejsów API sieci Web. Za pomocą niestandardowej funkcji interfejsu API sieci Web Zintegruj swój kod niestandardowy.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 29928d78c2cfc2f21def363341f8383c4efa89d2
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484111"
---
# <a name="custom-web-api-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Niestandardowa umiejętność interfejsu API sieci Web w potoku wzbogacenia Wyszukiwanie poznawcze platformy Azure

Niestandardowa umiejętność **interfejsu API sieci Web** umożliwia zwiększenie wzbogacenia AI przez wywołanie do punktu końcowego internetowego interfejsu API dostarczającego operacje niestandardowe. Podobnie jak w przypadku wbudowanych umiejętności, **niestandardowa umiejętność interfejsu API sieci Web** ma dane wejściowe i wyjściowe. W zależności od danych wejściowych internetowy interfejs API odbiera ładunek JSON podczas uruchamiania indeksatora i wyprowadza ładunek JSON jako odpowiedź wraz z kodem stanu sukcesu. Oczekiwano, że odpowiedź będzie miała dane wyjściowe określone przez niestandardową umiejętność. Jakakolwiek inna odpowiedź jest traktowana jako błąd i nie są wykonywane żadne wzbogacania.

Struktura ładunków JSON została opisana w dalszej części tego dokumentu.

> [!NOTE]
> Indeksator zostanie dwukrotnie ponowiony dla niektórych standardowych kodów stanu HTTP zwróconych z internetowego interfejsu API. Kody stanu HTTP: 
> * `502 Bad Gateway`
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.WebApiSkill

## <a name="skill-parameters"></a>Parametry umiejętności

W parametrach jest rozróżniana wielkość liter.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| adresu | Identyfikator URI internetowego interfejsu API, do którego zostanie wysłany ładunek _JSON_ . Dozwolony jest tylko schemat URI **https** |
| httpMethod | Metoda do użycia podczas wysyłania ładunku. Dozwolone metody to `PUT` lub `POST` |
| httpHeaders | Kolekcja par klucz-wartość, gdzie klucze reprezentują nazwy i wartości nagłówków, reprezentujące wartości nagłówka, które będą wysyłane do internetowego interfejsu API wraz z ładunkiem. Następujące nagłówki nie są dozwolone w tej kolekcji: `Accept`, `Accept-Charset`, `Accept-Encoding`, `Content-Length`, `Content-Type`, `Cookie`, `Host`, `TE`, `Upgrade`, `Via` |
| timeout | Obowiązkowe Gdy ta wartość jest określona, wskazuje limit czasu dla klienta http wykonującego wywołanie interfejsu API. Musi być sformatowana jako wartość XSD "dayTimeDuration" (ograniczony podzbiór wartości [Duration ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Na przykład `PT60S` przez 60 sekund. Jeśli nie zostanie ustawiona, zostanie wybrana wartość domyślna wynosząca 30 sekund. Limit czasu można ustawić na maksymalnie 230 sekund i co najmniej 1 sekundę. |
| batchSize | Obowiązkowe Wskazuje, ile "rekordów danych" (patrz struktura ładunku _JSON_ poniżej) zostanie wysłana na wywołanie interfejsu API. Jeśli nie zostanie ustawiona, zostanie wybrana wartość domyślna 1000. Zalecamy użycie tego parametru, aby osiągnąć odpowiednią kompromis między indeksowaniem przepływności i obciążeniem interfejsu API |
| degreeOfParallelism | Obowiązkowe Gdy ta wartość jest określona, wskazuje liczbę wywołań, które indeksator wykona równolegle do określonego punktu końcowego. Możesz zmniejszyć tę wartość, jeśli punkt końcowy kończy się niepowodzeniem z zbyt dużą liczbą żądań lub zgłoś go, jeśli punkt końcowy jest w stanie zaakceptować więcej żądań i chcesz zwiększyć wydajność indeksatora.  Jeśli nie zostanie ustawiona, zostanie użyta wartość domyślna 5. Dla degreeOfParallelism można ustawić maksymalnie 10 i co najmniej 1. |

## <a name="skill-inputs"></a>Dane wejściowe kwalifikacji

Brak "wstępnie zdefiniowanych" danych wejściowych dla tej umiejętności. Można wybrać jedno lub więcej pól, które będą już dostępne w momencie wykonywania tego umiejętności, ponieważ dane wejściowe i ładunek _JSON_ wysyłane do internetowego interfejsu API będą mieć różne pola.

## <a name="skill-outputs"></a>Wyniki umiejętności

Brak "wstępnie zdefiniowanych" danych wyjściowych dla tej umiejętności. W zależności od odpowiedzi zwracanej przez internetowy interfejs API Dodaj pola wyjściowe, aby można je było pobrać z odpowiedzi _JSON_ .


## <a name="sample-definition"></a>Definicja Przykładowa

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "A custom skill that can identify positions of different phrases in the source text",
        "uri": "https://contoso.count-things.com",
        "batchSize": 4,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/languageCode"
          },
          {
            "name": "phraseList",
            "source": "/document/keyphrases"
          }
        ],
        "outputs": [
          {
            "name": "hitPositions"
          }
        ]
      }
```
## <a name="sample-input-json-structure"></a>Przykładowa wejściowa struktura JSON

Ta struktura _JSON_ reprezentuje ładunek, który zostanie wysłany do internetowego interfejsu API.
Zawsze będą przestrzegane następujące ograniczenia:

* Jednostka najwyższego poziomu jest nazywana `values` i będzie tablicą obiektów. Liczba takich obiektów będzie największa `batchSize`
* Każdy obiekt w tablicy `values` będzie miał
    * Właściwość `recordId`, która jest **unikatowym** ciągiem używanym do identyfikowania tego rekordu.
    * Właściwość `data`, która jest obiektem _JSON_ . Pola właściwości `data` będą odpowiadać nazwie "names" określonej w sekcji `inputs` definicji umiejętności. Wartość tych pól będzie się znajdować na podstawie `source` tych pól (które mogą pochodzić z pola w dokumencie lub potencjalnie z innej umiejętności)

```json
{
    "values": [
      {
        "recordId": "0",
        "data":
           {
             "text": "Este es un contrato en Inglés",
             "language": "es",
             "phraseList": ["Este", "Inglés"]
           }
      },
      {
        "recordId": "1",
        "data":
           {
             "text": "Hello world",
             "language": "en",
             "phraseList": ["Hi"]
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Hello world, Hi world",
             "language": "en",
             "phraseList": ["world"]
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "Test",
             "language": "es",
             "phraseList": []
           }
      }
    ]
}
```

## <a name="sample-output-json-structure"></a>Przykładowa wyjściowa struktura JSON

"Wynik" odpowiada odpowiedzi zwróconej przez internetowy interfejs API. Internetowy interfejs API powinien zwrócić ładunek _JSON_ (zweryfikowany, przeglądając nagłówek odpowiedzi `Content-Type`) i powinien spełniać następujące ograniczenia:

* Powinna istnieć jednostka najwyższego poziomu o nazwie `values`, która powinna być tablicą obiektów.
* Liczba obiektów w tablicy powinna być taka sama jak liczba obiektów wysyłanych do internetowego interfejsu API.
* Każdy obiekt powinien mieć:
   * Właściwość `recordId`
   * Właściwość `data`, która jest obiektem, w którym pola są wzbogacane pasujące do "names" w `output` i którego wartość jest uważana za wzbogacanie.
   * Właściwość `errors`, tablica zawierająca wszystkie napotkane błędy, które zostaną dodane do historii wykonywania indeksatora. Ta właściwość jest wymagana, ale może mieć wartość `null`.
   * Właściwość `warnings`, tablica zawierająca wszystkie wykryte ostrzeżenia, które zostaną dodane do historii wykonywania indeksatora. Ta właściwość jest wymagana, ale może mieć wartość `null`.
* Obiekty w tablicy `values` nie muszą znajdować się w tej samej kolejności, co obiekty w tablicy `values` wysyłane jako żądanie do internetowego interfejsu API. Jednak `recordId` jest używany do korelacji, dlatego każdy rekord w odpowiedzi zawierający `recordId`, która nie jest częścią oryginalnego żądania do internetowego interfejsu API, zostanie odrzucony.

```json
{
    "values": [
        {
            "recordId": "3",
            "data": {
            },
            "errors": [
              {
                "message" : "'phraseList' should not be null or empty"
              }
            ],
            "warnings": null
        },
        {
            "recordId": "2",
            "data": {
                "hitPositions": [6, 16]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "0",
            "data": {
                "hitPositions": [0, 23]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "1",
            "data": {
                "hitPositions": []
            },
            "errors": null,
            "warnings": {
                "message": "No occurrences of 'Hi' were found in the input text"
            }
        },
    ]
}

```

## <a name="error-cases"></a>Przypadki błędów
Oprócz nieprawidłowego interfejsu API sieci Web lub wysyłania kodów stanu, które nie zostały pomyślnie, są uważane za błędne przypadki:

* Jeśli internetowy interfejs API zwraca kod stanu sukcesu, ale odpowiedź wskazuje, że nie jest `application/json`, odpowiedź jest uznawana za nieprawidłową i nie zostaną wykonane żadne wzbogacania.
* Jeśli istnieją **nieprawidłowe** (z `recordId` nie w oryginalnym żądaniu lub ze zduplikowanymi wartościami) w tablicy odpowiedzi `values`, dla **tych** rekordów nie zostanie wykonane wzbogacanie.

W przypadku niedostępności internetowego interfejsu API lub zwrócenie błędu HTTP, przyjazny błąd ze wszystkimi dostępnymi szczegółami dotyczącymi błędu HTTP zostanie dodany do historii wykonywania indeksatora.

## <a name="see-also"></a>Zobacz też

+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Dodaj niestandardową umiejętność do potoku wzbogacania AI](cognitive-search-custom-skill-interface.md)
+ [Przykład: Tworzenie niestandardowej umiejętności dla wzbogacania AI](cognitive-search-create-custom-skill-example.md)
