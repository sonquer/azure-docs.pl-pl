---
title: Typ jednostki wyrażenia regularnego — LUIS
titleSuffix: Azure Cognitive Services
description: Wyrażenie regularne jest najlepsze dla nieprzetworzonego tekstu wypowiedź. On ignoruje wielkość liter i ignoruje wariant kultury.  Dopasowywanie wyrażeń regularnych są stosowane po sprawdzania pisowni zmiany na poziomie znak, a nie na poziomie tokenu.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: b9da76a80183f353a74d43e667bf6c9219eb6c05
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74841221"
---
# <a name="regular-expression-entity"></a>Jednostka wyrażenia regularnego

Jednostka wyrażenia regularnego wyodrębnia jednostkę na podstawie podania wzorca wyrażenia regularnego.

Wyrażenie regularne jest najlepsze dla nieprzetworzonego tekstu wypowiedź. On ignoruje wielkość liter i ignoruje wariant kultury.  Dopasowywanie wyrażeń regularnych są stosowane po sprawdzania pisowni zmiany na poziomie znak, a nie na poziomie tokenu. Jeśli wyrażenie regularne jest zbyt złożone, na przykład przy użyciu wielu nawiasów, nie można dodać wyrażenia do modelu. Używa części, ale nie całej biblioteki [wyrażeń regularnych programu .NET](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) .

**Jednostka jest dobrym dopasowaniem w przypadku:**

* Dane są spójne z uwzględnieniem spójnych zmian.
* Wyrażenie regularne nie wymaga więcej niż 2 poziomów zagnieżdżenia.

![Jednostka wyrażenia regularnego](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Zagadnienia dotyczące użycia

Wyrażenia regularne mogą odpowiadać więcej niż oczekiwano. Przykładem jest numeryczne Dopasowywanie wyrazów, takie jak `one` i `two`. Przykładem jest następujące wyrażenie regularne, które pasuje do liczby `one` wraz z innymi liczbami:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

To wyrażenie regularne również dopasowuje wszystkie wyrazy kończące się tymi liczbami, takie jak `phone`. Aby rozwiązać te problemy, należy się upewnić, że dopasowuje wyrażenie regularne uwzględnia granice wyrazu. Wyrażenie regularne do używania granic wyrazów dla tego przykładu jest używane w następujących wyrażeniach regularnych:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>Przykładowy plik JSON

W przypadku używania `kb[0-9]{6}`, jako definicji jednostki wyrażenia regularnego następująca odpowiedź JSON to przykład wypowiedź z zwracanymi jednostkami wyrażenia regularnego dla zapytania:

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

```JSON
"entities": [
  {
    "entity": "kb123456",
    "type": "KB number",
    "startIndex": 9,
    "endIndex": 16
  }
]
```


#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)


Jest to kod JSON, jeśli `verbose=false` jest ustawiony w ciągu zapytania:

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Jest to kod JSON, jeśli `verbose=true` jest ustawiony w ciągu zapytania:

```json
"entities": {
    "KB number": [
        "kb123456"
    ],
    "$instance": {
        "KB number": [
            {
                "type": "KB number",
                "text": "kb123456",
                "startIndex": 9,
                "length": 8,
                "modelTypeId": 8,
                "modelType": "Regex Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>Następne kroki

W tym [samouczku](tutorial-regex-entity.md)utworzysz aplikację do wyodrębniania spójnie sformatowanych danych z wypowiedź przy użyciu jednostki **wyrażenia regularnego** .