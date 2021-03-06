---
title: Co nowego w interfejs API analizy tekstu
titleSuffix: Text Analytics - Azure Cognitive Services
description: Ten artykuł zawiera informacje o nowych wersjach i funkcjach usługi Azure Cognitive Services analiza tekstu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 162e60ac8d33dc5d1951a58b0a9643b668608d7b
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188801"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Co nowego w interfejsie API analizy tekstu?

Interfejs API analizy tekstu jest regularnie aktualizowana. Aby zachować aktualność dzięki najnowszym zmianom, ten artykuł zawiera informacje o nowych wersjach i funkcjach.

## <a name="february-2020"></a>Luty 2020

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>Obsługa zestawu SDK dla publicznej wersji zapoznawczej interfejs API analizy tekstu v3

W ramach [ujednoliconej wersji zestawu Azure SDK](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)zestaw interfejs API analizy tekstu v3 SDK jest teraz dostępny jako publiczna wersja zapoznawcza następujących języków programowania:
   * [C#](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (Node. js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat zestawu SDK dla interfejs API analizy tekstu v3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Niestandardowa wersja próbna rozpoznawania jednostek v3

Dodatkowe typy jednostek są teraz dostępne w publicznej wersji zapoznawczej rozpoznawania jednostek (NER) v3, ponieważ rozszerzamy wykrywanie ogólnych i osobistych jednostek informacji znalezionych w tekście. Ta aktualizacja wprowadza `2020-02-01`[wersji modelu](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) , która obejmuje:

* Rozpoznawanie następujących głównych typów jednostek (tylko w języku angielskim):
    * Persontype
    * Product (Produkt)
    * Wydarzenie
    * Jednostka geopolityczna (GPE) jako podtyp w lokalizacji
    * Czy

* Rozpoznawanie następujących typów jednostek informacji osobistych (tylko w języku angielskim):
    * Person (Osoba)
    * Organizacja
    * Wiek jako podtyp w ramach ilości
    * Data jako podtyp w obszarze DateTime
    * Email 
    * Numer telefonu (tylko Stany Zjednoczone)
    * Adres URL
    * Adres IP

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat nazwanego rozpoznawania jednostek v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>Październik 2019 r.

#### <a name="named-entity-recognition-ner"></a>Rozpoznawanie jednostek nazwanych (NER)

* [Nowy punkt końcowy](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) do rozpoznawania typów jednostek informacji osobistych (tylko w języku angielskim)

* Oddziel punkty końcowe do [rozpoznawania jednostek](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) i [łączenia jednostek](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking).

* [Wersja modelu](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2019-10-01`, która obejmuje:
    * Rozwinięte wykrywanie i kategoryzacja jednostek znalezionych w tekście. 
    * Rozpoznawanie następujących typów nowych jednostek:
        * Numer telefonu
        * Adres IP

Łączenie jednostek obsługuje język angielski i hiszpański. Obsługa języka NER zależy od typu jednostki.

#### <a name="sentiment-analysis-v3-public-preview"></a>Publiczna wersja zapoznawcza analiza tonacji v3

* [Nowy punkt końcowy](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) do analizowania tonacji.
* [Wersja modelu](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) `2019-10-01`, która obejmuje:

    * Znaczące ulepszenia dokładności i szczegółowości dotyczące kategoryzacji i oceny tekstu interfejsu API.
    * Automatyczne etykietowanie dla różnych mową w tekście.
    * Tonacji analizę i dane wyjściowe na poziomie dokumentu i zdania. 

Obsługuje język angielski (`en`), japoński (`ja`), chiński uproszczony (`zh-Hans`), chiński tradycyjny (`zh-Hant`), francuski (`fr`), włoski (`it`), hiszpański (`es`), holenderski (`nl`), portugalski (`pt`), niemiecki (`de`) i dostępny w następujących regionach: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`i `West US 2`. 

> [!div class="nextstepaction"]
> [Dowiedz się więcej o analiza tonacji wersji 3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Następne kroki

* [Co to jest interfejs API analizy tekstu?](overview.md)  
* [Przykładowe scenariusze użytkownika](text-analytics-user-scenarios.md)
* [Analiza tonacji](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Wykrywanie języka](how-tos/text-analytics-how-to-language-detection.md)
* [Rozpoznawanie jednostek](how-tos/text-analytics-how-to-entity-linking.md)
* [Wyodrębnianie kluczowych fraz](how-tos/text-analytics-how-to-keyword-extraction.md)
