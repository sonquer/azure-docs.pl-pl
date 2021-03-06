---
title: Uczenie aplikacji — LUIS
titleSuffix: Azure Cognitive Services
description: Szkolenie to proces uczenia usługi Language Understanding (LUIS) wersji aplikacji, aby zwiększyć jego interpretacja języka naturalnego. Szkolenie aplikacją usługi LUIS po aktualizacji do modelu, takich jak dodawanie, edytowanie, etykietowania lub usunięcie jednostki, intencji lub wypowiedzi.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 1da8ab3015730c6b3e1962301a34b1ad43b1aad6
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143690"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Uczenie aktywnej wersji aplikacji LUIS 

Szkolenie to proces uczenia aplikacji Language Understanding (LUIS), aby zwiększyć jego interpretacja języka naturalnego. Szkolenie aplikacją usługi LUIS po aktualizacji do modelu, takich jak dodawanie, edytowanie, etykietowania lub usunięcie jednostki, intencji lub wypowiedzi. 

Szkolenia i [testowania](luis-concept-test.md) aplikacji jest procesem iteracyjnym. Po uczenie jest aplikacją usługi LUIS, testowanie za pomocą wypowiedzi próbki, aby sprawdzić, czy intencje i podmioty są rozpoznawane prawidłowo. Jeśli nie jesteś, aktualizowanie aplikacją usługi LUIS, szkolenie i test ponownie. 

Szkolenie jest stosowany do aktywnej wersji w portalu usługi LUIS. 

## <a name="how-to-train-interactively"></a>Sposób trenowania interaktywnie

Aby rozpocząć proces iteracyjny w [portal usługi LUIS](https://www.luis.ai), najpierw musisz uczyć aplikacją usługi LUIS w co najmniej raz. Upewnij się, że każdy celem ma co najmniej jeden wypowiedź przed szkolenia.

1. Dostęp do aplikacji, wybierając jego nazwę na **Moje aplikacje** strony. 

1. W swojej aplikacji, wybierz **Train** w górnym panelu. 

1. Po zakończeniu szkolenia w górnej części przeglądarki pojawia się powiadomienie.

## <a name="training-date-and-time"></a>Data i godzina szkolenia

Data i godzina szkolenia to GMT + 2. 

## <a name="train-with-all-data"></a>Szkolenie przy użyciu wszystkich danych

Szkolenie używa niewielką część ujemna próbkowania. Jeśli chcesz używać wszystkich danych zamiast małego próbkowania negatywnego, użyj [interfejsu API](#version-settings-api-use-of-usealltrainingdata).

### <a name="version-settings-api-use-of-usealltrainingdata"></a>Ustawienia wersji użycie interfejsu API UseAllTrainingData

Aby wyłączyć tę funkcję, użyj [interfejsu API ustawień wersji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) z ustawieniem `UseAllTrainingData` na wartość true. 

## <a name="unnecessary-training"></a>Niepotrzebne szkolenia

Nie musisz uczyć się po każdej zmianie jednego. Szkolenie powinno się odbywać po grupę zmian są stosowane do modelu, i następnym krokiem, jaki chcesz przeprowadzić test lub opublikować. Jeśli nie potrzebujesz do testów lub opublikować, szkolenie nie jest konieczne. 

## <a name="training-with-the-rest-apis"></a>Szkolenie przy użyciu interfejsów API REST

Szkolenie w portalu usługi LUIS jest w jednym kroku naciśnięcie **Train** przycisku. Szkolenie przy użyciu interfejsów API REST jest procesem dwuetapowym. Pierwsza to [żądania szkolenia](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) za pomocą żądania HTTP POST. Następnie zażądać [stan szkolenia](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) za pomocą protokołu HTTP Get. 

Aby dowiedzieć się, po zakończeniu szkolenia, należy wykonać sondowanie stanu, dopóki wszystkie modele pomyślnie są uczone. 

## <a name="next-steps"></a>Następne kroki

* [Testowanie interaktywne](luis-interactive-test.md)
* [Testy wsadowe](luis-how-to-batch-test.md)
