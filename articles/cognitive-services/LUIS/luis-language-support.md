---
title: Obsługa języków — LUIS
titleSuffix: Azure Cognitive Services
description: Usługa LUIS ma wiele funkcji w ramach usługi. Nie wszystkie funkcje są w tej samej parzystości języka. Upewnij się, że funkcje, których jesteś zainteresowany są obsługiwane w kulturze języka, które są przeznaczone dla. Aplikacją usługi LUIS jest specyficzne dla kultury i nie można zmienić po jej ustawieniu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: f6b95f76af4c83459ac81ff1703d8588f649326c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970546"
---
# <a name="language-and-region-support-for-luis"></a>Obsługa języka i regionu dla usługi LUIS

Usługa LUIS ma wiele funkcji w ramach usługi. Nie wszystkie funkcje są w tej samej parzystości języka. Upewnij się, że funkcje, których jesteś zainteresowany są obsługiwane w kulturze języka, które są przeznaczone dla. Aplikacją usługi LUIS jest specyficzne dla kultury i nie można zmienić po jej ustawieniu.

## <a name="multi-language-luis-apps"></a>Usługa LUIS wielojęzycznych aplikacji

Jeśli potrzebujesz aplikacji klienckiej usługi LUIS wielu języków, takich jak czatbota, masz kilka opcji. Jeśli usługa LUIS obsługuje wszystkie języki, możesz tworzyć aplikacją usługi LUIS dla każdego języka. Każda aplikacja usługi LUIS ma identyfikator unikatowy aplikacji i punkt końcowy dziennika. Jeśli konieczne jest zapewnienie zrozumienia dla języka nie obsługuje usługi LUIS, można użyć języka [interfejsu API usługi Microsoft Translator](../Translator/translator-info-overview.md) tłumaczenie wypowiedź w obsługiwanym języku, Prześlij wypowiedź do punktu końcowego usługi LUIS i odbierania Wynikowy wyniki.

## <a name="languages-supported"></a>Obsługiwane języki

Usługa LUIS rozumie wypowiedzi w następujących językach:

| Język |Ustawienia regionalne  |  Wstępnie utworzone domeny | Wstępnie utworzone jednostki | Zalecenia dotyczące listy fraz | **[Analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Tonacji i<br>Słowa kluczowe)|
|--|--|:--:|:--:|:--:|:--:|
| Angielski |`en-US` | ✔ | ✔  |✔|✔|
| Arabski (wersja zapoznawcza — nowoczesny Standard arabski) |`ar-AR`|-|-|-|-|
| *[Chiński](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Holenderski |`nl-NL` |✔|  -   |-|✔|
| Francuski (Francja) |`fr-FR` |✔| ✔ |✔ |✔|
| Francuski (Kanada) |`fr-CA` |-|   -   |-|✔|
| niemiecki |`de-DE` |✔| ✔ |✔ |✔|
| Hindi | `hi-IN`|-|-|-|-|
| Włoski |`it-IT` |✔| ✔ |✔|✔|
| *[Japoński](#japanese-support-notes) |`ja-JP` |✔| ✔ |✔|Tylko frazy kluczowe|
| Koreański |`ko-KR` |✔|   -   |-|Tylko frazy kluczowe|
| Portugalski (Brazylia) |`pt-BR` |✔| ✔ |✔ |nie wszystkie podrzędne kultur|
| Hiszpański (Hiszpania) |`es-ES` |✔| ✔ |✔|✔|
| Hiszpański (Meksyk)|`es-MX` |-|  -   |✔|✔|
| Turecki | `tr-TR` |✔|-|-|Tylko tonacji|

Obsługa języka jest różny dla [ze wstępnie utworzonych jednostek](luis-reference-prebuilt-entities.md) i [ze wstępnie utworzonych domen](luis-reference-prebuilt-domains.md).

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

### <a name="japanese-support-notes"></a>\* Informacje o pomocy technicznej japoński

 - LUIS nie zapewnia analizy składni i nie będzie zrozumieć różnicę między Keigo i japoński nieformalnej, dlatego należy zastosować różne poziomy formalności jako przykłady szkolenia dla aplikacji.
     - でございます nie jest taka sama jak です.
     - です nie jest taka sama jak だ.

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]

### <a name="speech-api-supported-languages"></a>Języki obsługiwane interfejsu API mowy
Zobacz mowy [obsługiwane języki](https://docs.microsoft.com/azure/cognitive-services/Speech/api-reference-rest/supportedlanguages##interactive-and-dictation-mode) dla języków tryb dyktowania mowy.

### <a name="bing-spell-check-supported-languages"></a>Sprawdzanie pisowni Bing obsługiwane języki
Zobacz, sprawdzanie pisowni Bing [obsługiwane języki](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) Aby uzyskać listę obsługiwanych języków i stan.

## <a name="rare-or-foreign-words-in-an-application"></a>Rzadkie lub obce słowa w aplikacji
W `en-us` kultury, uzyskuje informacje o odróżnić najbardziej wyrazami, w tym żargonu usługi LUIS. W `zh-cn` kultury, uzyskuje informacje o odróżnić większości znaków chińskich usługi LUIS. Jeśli używasz rzadkich wyrazu w `en-us` lub znaków w pliku `zh-cn`, zobaczyć LUIS jest prawdopodobnie do odróżnienia tego wyrazu lub znaków, Dodaj ten wyraz lub znak do [funkcji listy fraz](luis-how-to-add-features.md). Na przykład słowa poza kulturę aplikacji — czyli obce słowa — należy dodać do funkcji listy fraz.

<!--This phrase list should be marked non-interchangeable, to indicate that the set of rare words forms a class that LUIS should learn to recognize, but they are not synonyms or interchangeable with each other.-->

### <a name="hybrid-languages"></a>Języki hybrydowe
Języki hybrydowego łączyć wyrazów dwie kultury, takie jak angielski i chińskim. Te języki nie są obsługiwane usługi LUIS, ponieważ aplikacja jest oparta na jednej kulturze.

## <a name="tokenization"></a>Tokenizacji
Do przeprowadzenia uczenia maszynowego, usługa LUIS dzieli wypowiedź na [tokenów](luis-glossary.md#token) na podstawie kultury.

|Język|  Każdy spacji lub znaku specjalnego | poziom znaków|wyrazy złożone|[Jednostka tokenami zwracana](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|Arabski|||||
|Chiński||✔||✔|
|Holenderski|||✔|✔|
|Angielski (en-us)|✔ ||||
|Francuski (fr-FR)|✔||||
|Francuski (fr-CA)|✔||||
|niemiecki|||✔|✔|
| Hindi |✔|-|-|-|-|
|Włoski|✔||||
|Japoński||||✔|
|Koreański||✔||✔|
|Portugalski (Brazylia)|✔||||
|Hiszpański (es-ES)|✔||||
|Hiszpański (es-MX)|✔||||

### <a name="custom-tokenizer-versions"></a>Niestandardowe wersje tokenizatora

Następujące kultury mają niestandardowe wersje tokenizatora:

|Kultura|Wersja|Przeznaczenie|
|--|--|--|
|niemiecki<br>`de-de`|1.0.0|Tokenizes wyrazy, dzieląc je za pomocą tokenizatora opartych na uczeniu maszynowym, które próbują podzielić wyrazy złożone na ich pojedyncze składniki.<br>Jeśli użytkownik wprowadzi `Ich fahre einen krankenwagen` jako wypowiedź, zostanie `Ich fahre einen kranken wagen`. Zezwalanie na oznaczanie `kranken` i `wagen` niezależnie jako różne jednostki.|
|niemiecki<br>`de-de`|1.0.2|Tokenizes wyrazy, dzieląc je na spacje.<br> Jeśli użytkownik wprowadzi `Ich fahre einen krankenwagen` jako wypowiedź, pozostaje pojedynczym tokenem. W ten sposób `krankenwagen` jest oznaczona jako pojedyncza jednostka. |

### <a name="migrating-between-tokenizer-versions"></a>Migrowanie między wersjami tokenizatora
<!--
Your first choice is to change the tokenizer version in the app file, then import the version. This action changes how the utterances are tokenized but allows you to keep the same app ID.

Tokenizer JSON for 1.0.0. Notice the property value for  `tokenizerVersion`.

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.0",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.0",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 23,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```

Tokenizer JSON for version 1.0.1. Notice the property value for  `tokenizerVersion`.

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.1",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.1",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 16,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```
-->

Tokenizacji odbywa się na poziomie aplikacji. Nie ma obsługi tokenizacji poziomu wersji.

[Zaimportuj plik jako nową aplikację](luis-how-to-start-new-app.md)zamiast wersji. Ta akcja oznacza, że nowa aplikacja ma inny identyfikator aplikacji, ale używa wersji tokenizatora określonej w pliku.
