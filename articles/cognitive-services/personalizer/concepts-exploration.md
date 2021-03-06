---
title: Eksploracja — Personalizuj
titleSuffix: Azure Cognitive Services
description: Dzięki eksploracji program Personalizujer może kontynuować dostarczanie dobrych wyników, nawet w przypadku zmiany zachowania użytkownika. Wybór ustawienia eksploracji to decyzja biznesowa dotycząca proporcji interakcji użytkowników, która umożliwia Eksplorowanie w programie w celu ulepszenia modelu.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 0b69c1fb070431ad61858322dce461f6496c35d7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490809"
---
# <a name="exploration-and-exploitation"></a>Eksploracja i wykorzystanie

Dzięki eksploracji program Personalizujer może kontynuować dostarczanie dobrych wyników, nawet w przypadku zmiany zachowania użytkownika.

Gdy Personalizacja odbiera wywołanie rangi, zwraca RewardActionID, że:
* Używa luki w zabezpieczeniach, aby dopasować najbardziej prawdopodobne zachowanie użytkownika w oparciu o bieżący model uczenia maszynowego.
* Używa eksploracji, która nie jest zgodna z akcją, która ma największe prawdopodobieństwo w rangi.

Personalizacja obecnie używa algorytmu o nazwie *Epsilon zachłanne* do eksplorowania. 

## <a name="choosing-an-exploration-setting"></a>Wybieranie ustawienia eksploracji

Należy skonfigurować procent ruchu, który ma być używany do eksploracji na stronie **konfiguracji** Azure Portal dla personalizacji. To ustawienie określa procent wywołań rangi, które wykonują eksplorację. 

Personalizowanie decyduje o tym, czy należy eksplorować lub wykorzystywać to prawdopodobieństwo dla każdego wywołania rangi. Różni się to od zachowania w niektórych strukturach A/B, które zablokują traktowanie określonych identyfikatorów użytkownika.

## <a name="best-practices-for-choosing-an-exploration-setting"></a>Najlepsze rozwiązania dotyczące wybierania ustawienia eksploracji

Wybór ustawienia eksploracji to decyzja biznesowa dotycząca proporcji interakcji użytkowników, która umożliwia Eksplorowanie w programie w celu ulepszenia modelu. 

Ustawienie wartości zero będzie Negate wiele zalet personalizacji. Dzięki temu ustawieniu Personalizacja nie używa interakcji użytkownika do odnajdywania lepszych interakcji użytkownika. Prowadzi to do modelowania stagnation, dryfowania i ostatecznie mniejszej wydajności.

Ustawienie, które jest zbyt wysokie, będzie Negate zalety uczenia się z zachowaniem użytkownika. Ustawienie tej opcji na 100% oznacza stałe generowanie losowe, a wszelkie zdobyte działania użytkowników nie wpłyną na wyniki.

Należy pamiętać, aby nie zmieniać działania aplikacji w zależności od tego, czy program Personalizowający jest w trakcie eksplorowania, czy wykorzystania. Może to prowadzić do uczenia się, że ostatecznie zmniejszy potencjalną wydajność.

## <a name="next-steps"></a>Następne kroki

[Uczenie wzmacniające](concepts-reinforcement-learning.md) 