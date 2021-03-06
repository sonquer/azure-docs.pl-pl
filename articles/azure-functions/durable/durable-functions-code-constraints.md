---
title: Trwałe ograniczenia kodu programu Orchestrator — Azure Functions
description: Funkcje aranżacji i ograniczenia kodu dla Durable Functions platformy Azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 5013457aca99a63808077b86f5674460e83fdc41
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232974"
---
# <a name="orchestrator-function-code-constraints"></a>Ograniczenia kodu funkcji programu Orchestrator

Durable Functions jest rozszerzeniem [Azure Functions](../functions-overview.md) , które pozwala tworzyć aplikacje stanowe. Za pomocą funkcji programu [Orchestrator](durable-functions-orchestrations.md) można organizować wykonywanie innych trwałych funkcji w ramach aplikacji funkcji. Funkcje programu Orchestrator są stanowe, niezawodne i potencjalnie długotrwałe.

## <a name="orchestrator-code-constraints"></a>Ograniczenia kodu orkiestratora

Funkcje programu Orchestrator używają określania [źródła zdarzeń](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) , aby zapewnić niezawodne wykonywanie i zachować stan zmiennej lokalnej. [Zachowanie powtarzania](durable-functions-orchestrations.md#reliability) kodu programu Orchestrator tworzy ograniczenia dotyczące typu kodu, który można napisać w funkcji programu Orchestrator. Na przykład funkcje programu Orchestrator muszą być *deterministyczne*: funkcja programu Orchestrator będzie powtarzana wielokrotnie i musi generować ten sam wynik za każdym razem.

### <a name="using-deterministic-apis"></a>Korzystanie z deterministycznych interfejsów API

Ta sekcja zawiera pewne proste wskazówki, które ułatwiają zagwarantowanie, że kod jest deterministyczny.

Funkcje programu Orchestrator mogą wywołać dowolny interfejs API w swoich językach docelowych. Należy jednak pamiętać, że funkcje programu Orchestrator wywołują tylko deterministyczne interfejsy API. *Deterministyczny interfejs API* jest interfejsem API, który zawsze zwraca tę samą wartość, niezależnie od tego, kiedy lub jak często jest wywoływana.

W poniższej tabeli przedstawiono przykłady interfejsów API, które należy unikać, ponieważ *nie* są one deterministyczne. Te ograniczenia mają zastosowanie tylko do funkcji programu Orchestrator. Inne typy funkcji nie mają takich ograniczeń.

| Kategoria interfejsu API | Przyczyna | Obejście |
| ------------ | ------ | ---------- |
| Daty i godziny  | Interfejsy API, które zwracają bieżącą datę lub godzinę, są niejednoznaczne, ponieważ zwracana wartość jest inna dla każdego powtórzenia. | Użyj interfejsu API`CurrentUtcDateTime` w programie .NET lub interfejsu API `currentUtcDateTime` w języku JavaScript, które są bezpieczne do odtworzenia. |
| Identyfikatory GUID i identyfikatory UUID  | Interfejsy API, które zwracają losowy identyfikator GUID lub UUID, są niejednoznaczne, ponieważ wygenerowana wartość jest inna dla każdego powtórzenia. | Użyj `NewGuid` w programie .NET lub `newGuid` w języku JavaScript, aby bezpiecznie generować losowe identyfikatory GUID. |
| Liczby losowe | Interfejsy API, które zwracają liczby losowe, są niejednoznaczne, ponieważ wygenerowana wartość jest inna dla każdego powtórzenia. | Użyj funkcji działania, aby zwrócić liczbę losową do aranżacji. Wartości zwracane funkcji działania są zawsze bezpieczne do powtórzenia. |
| Powiązania | Powiązania wejściowe i wyjściowe zwykle wykonują we/wy i są niejednoznaczne. Funkcja programu Orchestrator nie może bezpośrednio używać nawet powiązania klienta [aranżacji](durable-functions-bindings.md#orchestration-client) i [klienta jednostki](durable-functions-bindings.md#entity-client) . | Użyj powiązań wejściowych i wyjściowych w ramach funkcji klienta lub działania. |
| Sieć | Wywołania sieciowe obejmują systemy zewnętrzne i nie są niejednoznaczne. | Użyj funkcji działania, aby wykonać wywołania sieciowe. Jeśli musisz wykonać wywołanie HTTP z funkcji programu Orchestrator, możesz również użyć [trwałych interfejsów API protokołu HTTP](durable-functions-http-features.md#consuming-http-apis). |
| Blokowanie interfejsów API | Blokowanie interfejsów API, takich jak `Thread.Sleep` w środowisku .NET i podobnych interfejsów API, może spowodować problemy z wydajnością i skalowaniem dla funkcji programu Orchestrator. W planie zużycia Azure Functions mogą nawet spowodować niepotrzebne opłaty w czasie wykonywania. | Używaj alternatyw do blokowania interfejsów API, gdy są one dostępne. Na przykład użyj `CreateTimer`, aby wprowadzić opóźnienia w wykonywaniu aranżacji. [Trwałe opóźnienia czasomierza](durable-functions-timers.md) nie są wliczane do czasu wykonywania funkcji programu Orchestrator. |
| Asynchroniczne interfejsy API | Kod programu Orchestrator nigdy nie może uruchamiać żadnej operacji asynchronicznej, z wyjątkiem interfejsu API `IDurableOrchestrationContext` lub interfejsu API obiektu `context.df`. Nie można na przykład używać `Task.Run`, `Task.Delay`i `HttpClient.SendAsync` w programie .NET ani `setTimeout` i `setInterval` w języku JavaScript. W ramach trwałej struktury zadań jest uruchamiany kod programu Orchestrator w pojedynczym wątku. Nie może ona współistnieć z innymi wątkami, które mogą być wywoływane przez inne asynchroniczne interfejsy API. | Funkcja programu Orchestrator powinna wprowadzać tylko trwałe wywołania asynchroniczne. Funkcje działania powinny wykonywać wszystkie inne asynchroniczne wywołania interfejsu API. |
| Asynchroniczne funkcje języka JavaScript | Nie można zadeklarować funkcji programu Orchestrator języka JavaScript jako `async`, ponieważ środowisko uruchomieniowe Node. js nie gwarantuje, że funkcje asynchroniczne są deterministyczne. | Zadeklaruj funkcje programu Orchestrator w języku JavaScript jako funkcje generatora synchronicznego. |
| Wątkowe interfejsy API | W ramach trwałej struktury zadań działa kod programu Orchestrator w pojedynczym wątku i nie może współdziałać z żadnymi innymi wątkami. Wprowadzenie nowych wątków do wykonywania aranżacji może spowodować niedeterministyczne wykonanie lub zakleszczenia. | Funkcje programu Orchestrator powinny prawie nigdy nie używać interfejsów API wątków. Jeśli takie interfejsy API są niezbędne, należy ograniczyć ich użycie tylko do funkcji działania. |
| Zmienne statyczne | Unikaj używania niestałych zmiennych statycznych w funkcjach programu Orchestrator, ponieważ ich wartości mogą ulec zmianie z upływem czasu, co skutkuje niedeterministycznym zachowaniem środowiska uruchomieniowego. | Używaj stałych lub Ogranicz użycie zmiennych statycznych do funkcji działania. |
| Zmienne środowiskowe | Nie używaj zmiennych środowiskowych w funkcjach programu Orchestrator. Ich wartości mogą ulec zmianie z upływem czasu, co skutkuje niedeterministycznym zachowaniem środowiska uruchomieniowego. | Zmienne środowiskowe muszą być przywoływane tylko z poziomu funkcji klienta lub funkcji działania. |
| Nieskończone pętle | Unikaj nieskończonych pętli w funkcjach programu Orchestrator. Ponieważ trwała struktura zadań zapisuje historię wykonywania w miarę postępów funkcji aranżacji, pętla nieskończona może spowodować, że w wystąpieniu programu Orchestrator brakuje pamięci. | W przypadku scenariuszy pętli nieskończonej Użyj interfejsów API, takich jak `ContinueAsNew` w środowisku .NET lub `continueAsNew` w języku JavaScript, aby ponownie uruchomić wykonywanie funkcji i odrzucić poprzednią historię wykonywania. |

Chociaż stosowanie tych ograniczeń może wydawać się trudne, w przypadku, w którym są one łatwe do wykonania.

Trwała platforma zadań próbuje wykryć naruszenia powyższych reguł. W przypadku znalezienia naruszenia, struktura zgłasza wyjątek **NonDeterministicOrchestrationException** . Jednak takie zachowanie wykrywania nie będzie przechwytywać wszystkich naruszeń i nie powinno być od niego zależne.

## <a name="versioning"></a>Obsługa wersji

Niezawodna aranżacja może działać w sposób ciągły przez dni, miesiące, lata lub nawet [Eternally](durable-functions-eternal-orchestrations.md). Wszelkie aktualizacje kodu wprowadzone do Durable Functions aplikacji, które mają wpływ na nieukończone aranżacje, mogą spowodować przerwanie działania odtwarzania w ramach aranżacji. Dlatego ważne jest, aby dokładnie zaplanować wprowadzanie aktualizacji kodu. Aby uzyskać bardziej szczegółowy opis sposobu tworzenia wersji kodu, zobacz artykuł dotyczący [przechowywania wersji](durable-functions-versioning.md).

## <a name="durable-tasks"></a>Zadania trwałe

> [!NOTE]
> W tej sekcji opisano wewnętrzne szczegóły implementacji dla trwałej struktury zadań. Możesz korzystać z trwałych funkcji bez znajomości tych informacji. Jest ona przeznaczona tylko do zrozumienia zachowania powtarzania.

Zadania, które mogą bezpiecznie oczekiwać w funkcjach programu Orchestrator, są czasami określane jako *zadania trwałe*. W ramach trwałej struktury zadań są tworzone i zarządzane te zadania. Przykłady to zadania zwracane przez **CallActivityAsync**, **WaitForExternalEvent**i **OnTime** w funkcjach programu .NET Orchestrator.

Te zadania trwałe są wewnętrznie zarządzane przez listę obiektów `TaskCompletionSource` w programie .NET. Podczas odtwarzania te zadania są tworzone w ramach wykonywania kodu programu Orchestrator. Są one kończone, ponieważ Dyspozytor wylicza odpowiednie zdarzenia historii.

Zadania są wykonywane synchronicznie przy użyciu jednego wątku, dopóki cała historia nie zostanie powtórzona. Zadania trwałe, które nie zostały zakończone przez zakończenie powtarzania historii, mają odpowiednie działania. Na przykład komunikat może zostać przewidziany do kolejki w celu wywołania funkcji działania.

W tej sekcji opisano zachowanie środowiska uruchomieniowego, które ułatwia zrozumienie, dlaczego funkcja programu Orchestrator nie może używać `await` lub `yield` w nietrwałym zadaniu. Istnieją dwie przyczyny: wątek dyspozytora nie może czekać na zakończenie zadania, a każde wywołanie zwrotne przez to zadanie może potencjalnie uszkodzić stan śledzenia funkcji programu Orchestrator. Niektóre testy środowiska uruchomieniowego są stosowane w celu ułatwienia wykrywania tych naruszeń.

Aby dowiedzieć się więcej o tym, jak usługa trwałej struktury zadań wykonuje funkcje programu Orchestrator, zapoznaj się z [kodem źródłowym zadania trwałego w witrynie GitHub](https://github.com/Azure/durabletask). W szczególności zobacz [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) i [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o sposobie wywoływania podaranżacji](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Dowiedz się, jak obsługiwać przechowywanie wersji](durable-functions-versioning.md)
