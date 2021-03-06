---
title: Co nowego — Language Understanding (LUIS)
description: Ten artykuł jest regularnie aktualizowany za pomocą wiadomości o interfejs API usługi Language Understanding usługi Azure Cognitive Services.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 716860b54e7d8e75984c0365cac61d14153c09ff
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137807"
---
# <a name="whats-new-in-language-understanding"></a>Co nowego w Language Understanding

Dowiedz się, co nowego w usłudze. Te elementy obejmują informacje o wersji, klipy wideo, wpisy w blogu i inne rodzaje informacji. Oznacz Tę stronę zakładką, aby zapewnić aktualność usługi.

## <a name="release-notes"></a>Informacje o wersji

### <a name="november-4-2019---ignite"></a>4 listopada 2019 — zapłon

* [Language Understanding zaawansowane modele wideo (NLU) z Luis i Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* Ulepszona produktywność dla deweloperów
    * Ogólna dostępność naszego [punktu końcowego przewidywania v3](luis-migration-api-v3.md).
    * Możliwość importowania i eksportowania aplikacji z formatem. lu ([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)). To paves sposób na efektywny proces ciągłej integracji/ciągłego dostarczania.
* Rozwinięcie języka
    * [Język arabski i hindi](luis-language-support.md) w publicznej wersji zapoznawczej.
* Wstępnie utworzone modele
    * [Wstępnie skompilowane domeny](luis-reference-prebuilt-domains.md) są teraz ogólnie dostępne (ga)
    * Japońskie wstępnie [skompilowane jednostki](luis-reference-prebuilt-entities.md#japanese-entity-support) — wiek, waluta, liczba i procent nie są obsługiwane w wersji 3.
    * Włoskie wstępnie [skompilowane jednostki](luis-reference-prebuilt-entities.md#italian-entity-support) — wiek, waluta, wymiar, numer i rozdzielczość procentową zmieniono z wersji 2.
* Ulepszone środowisko użytkownika w [portalu Preview.Luis.AI](https://preview.luis.ai) — środowisko etykietowania odnowionych umożliwiające tworzenie i debugowanie modeli złożonych. Wypróbuj samouczki dotyczące portalu w wersji zapoznawczej:
    * [Tylko intencje](tutorial-intents-only.md)
    * [Odtworzona jednostka pouczenia maszynowego](tutorial-machine-learned-entity.md)
* Dokładniejsze Omówienie języka — [tworzenie zaawansowanych modeli języków](luis-concept-entity-types.md) z mniejszym nakładem pracy.
* Zdefiniuj funkcje uczenia maszynowego na poziomie modelu i Włącz modele, które mają być używane jako sygnały do innych modeli, na przykład użycie jednostek jako funkcji do intencji i innych jednostek.
* Nowe, rozwinięte [limity](luis-boundaries.md) — większa liczba list fraz i łączna liczba fraz, nowy model jako limity funkcji
* Wyodrębnij informacje z tekstu w formacie głębokiej struktury hierarchii, co sprawia, że aplikacje do konwersacji są bardziej wydajne.

    ![obraz jednostki o Poznaniu maszynowym](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>3 września, 2019

* Tworzenie zasobu platformy Azure — [Migrowanie teraz](luis-migration-authoring.md).
    * 500 aplikacji na zasób platformy Azure
    * 100 wersji na aplikację
* Wsparcie tureckie dla wstępnie utworzonych jednostek
* Obsługa języka włoskiego dla datetimeV2

### <a name="july-23-2019"></a>23 lipca 2019

* Aktualizowanie [aparatów rozpoznawania tekstu](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) do 1.2.3
    * Aparaty rozpoznawania wieku, temperatury, wymiaru i waluty w języku włoskim.
    * Poprawa rozpoznawania dni w języku angielskim w celu poprawnego obliczenia dat opartych na świętach.
    * Ulepszenia w francuskim elemencie DateTime, aby zmniejszyć liczbę fałszywych wartości niebędących nieaktualnymi i niebędącymi jednostkami czasu.
    * Obsługa kalendarza/szkoły/roku obrachunkowego i akronimów w języku angielskim DateRange.
    * Ulepszone rozpoznawanie telefonu w języku chińskim i japońskim.
    * Ulepszona obsługa NumberRange w języku angielskim.
    * Ulepszenia wydajności.

### <a name="june-24-2019"></a>24 czerwca 2019

* [OrdinalV2 prekompilowana jednostka](luis-reference-prebuilt-ordinal-v2.md) do obsługi kolejności takich jak Next, Previous i Last. Tylko kultura w języku angielskim.

### <a name="may-6-2019---build-conference"></a>6 maja 2019 — Konferencja Build

Następujące funkcje zostały wydane na konferencji Build 2019:

* [Przewodnik migracji interfejsu API v3](luis-migration-api-v3.md)
* [Udoskonalony pulpit nawigacyjny analizy](luis-how-to-use-dashboard.md)
* [Ulepszone prebudowane domeny](luis-reference-prebuilt-domains.md)
* [Jednostki listy dynamicznej](luis-migration-api-v3.md#dynamic-lists-passed-in-at-prediction-time)
* [Jednostki zewnętrzne](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Blogi

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>Filmy wideo

### <a name="2019-ignite-videos"></a>wideo o zapłonie 2019

[Zaawansowane modele naturalnych Language Understanding (NLU) korzystające z LUIS i platformy Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>wideo kompilacji 2019

[Jak skalować swoją firmę do nowej generacji przy użyciu funkcji Azure Conversation AI](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Aktualizacje usług

[Anonse aktualizacji platformy Azure dla Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
