---
title: Wykrywanie przecieku pamięci — Inteligentne wykrywanie Application Insights platformy Azure
description: Monitoruj aplikacje za pomocą usługi Azure Application Insights dla potencjalnych przecieków pamięci.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/12/2017
ms.openlocfilehash: cc6074732a86be02a8d8c596c46e154db4e8ad7f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75406464"
---
# <a name="memory-leak-detection-preview"></a>Wykrywanie przecieków pamięci (wersja zapoznawcza)

Application Insights automatycznie analizuje użycie pamięci przez każdy proces w aplikacji, a także ostrzega o potencjalnych przeciekach pamięci lub zwiększeniu zużycia pamięci.

Ta funkcja nie wymaga żadnych specjalnych ustawień, innych niż [Konfigurowanie liczników wydajności](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) dla aplikacji. Jest ona aktywna, gdy aplikacja generuje wystarczające dane telemetryczne liczników wydajności pamięci (na przykład bajty prywatne).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Kiedy mam ten typ powiadomienia inteligentnego wykrywania?
Typowym powiadomieniem będzie postępować ze zwiększonym wzrostem zużycia pamięci przez długi czas, w co najmniej jednym procesie i/lub co najmniej jednej maszynie, która jest częścią Twojej aplikacji. Algorytmy uczenia maszynowego są używane do wykrywania zwiększonych zużycia pamięci, które pasują do wzorca przecieku pamięci.

## <a name="does-my-app-really-have-a-problem"></a>Czy moja aplikacja naprawdę ma problem?
Nie, powiadomienie nie oznacza, że w aplikacji występuje problem. Chociaż wzorce przecieków pamięci zwykle wskazują na problem z aplikacją, wzorce te mogą być typowe dla danego procesu lub mogą mieć naturalne uzasadnienie biznesowe i można je zignorować.

## <a name="how-do-i-fix-it"></a>Jak go naprawić?
Powiadomienia obejmują informacje diagnostyczne do obsługi procesu analizy diagnostycznej:
1. **Klasyfikacja.** Powiadomienie przedstawia ilość zwiększenia pamięci (w GB) i zakres czasu, w którym pamięć została zwiększona. Może to pomóc w przypisaniu priorytetu do problemu.
2. **Scope.** Ile maszyn wykazuje wzorzec przecieku pamięci? Ile wyjątków zostało wyzwolonych podczas potencjalnego przecieku pamięci? Te informacje można uzyskać na podstawie powiadomienia.
3. **Sprawdź.** Wykrywanie zawiera wzorzec przecieku pamięci, pokazując użycie pamięci przez proces w czasie. Możesz również użyć powiązanych elementów i raportów łączących się z informacjami pomocniczymi, aby pomóc w dalszej analizie problemu.
