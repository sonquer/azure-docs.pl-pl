---
title: Monitorowanie stanu, Wyświetlanie historii i Konfigurowanie alertów
description: Rozwiązywanie problemów z aplikacjami logiki przez sprawdzenie stanu uruchomienia, przejrzenie historii wyzwalacza i włączenie alertów w Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 495877f1c839de2cf3583a37180054c91bd9f139
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76907775"
---
# <a name="monitor-run-status-review-trigger-history-and-set-up-alerts-for-azure-logic-apps"></a>Monitoruj stan przebiegu, przejrzyj historię wyzwalacza i skonfiguruj alerty dla Azure Logic Apps

Po [utworzeniu i uruchomieniu aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)można sprawdzić stan uruchomienia aplikacji logiki, [historię](#review-runs-history)uruchamiania, [historię wyzwalacza](#review-trigger-history)i wydajność. Aby otrzymywać powiadomienia o błędach lub innych możliwych problemach, skonfiguruj [alerty](#add-azure-alerts). Na przykład można utworzyć alert, który wykrywa "gdy więcej niż pięć przebiegów zakończy się niepowodzeniem w ciągu godziny".

Aby monitorować zdarzenia w czasie rzeczywistym i bogatsze debugowanie, skonfiguruj rejestrowanie diagnostyki dla aplikacji logiki przy użyciu [dzienników Azure monitor](../azure-monitor/overview.md). Ta usługa platformy Azure ułatwia monitorowanie środowisk w chmurze i lokalnych, dzięki czemu można łatwiej zachować swoją dostępność i wydajność. Następnie można znaleźć i wyświetlić zdarzenia, takie jak zdarzenia wyzwalania, zdarzenia uruchamiania i zdarzenia akcji. Przechowując te informacje w [dziennikach Azure monitor](../azure-monitor/platform/data-platform-logs.md), można utworzyć [zapytania dziennika](../azure-monitor/log-query/log-query-overview.md) , które ułatwiają znalezienie i przeanalizowanie tych informacji. Możesz również używać tych danych diagnostycznych z innymi usługami platformy Azure, takimi jak Azure Storage i Azure Event Hubs. Aby uzyskać więcej informacji, zobacz [monitorowanie aplikacji logiki przy użyciu Azure monitor](../logic-apps/monitor-logic-apps-log-analytics.md).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

<a name="review-runs-history"></a>

## <a name="review-runs-history"></a>Przejrzyj historię uruchomień

Za każdym razem, gdy wyzwalacz jest wyzwalany dla elementu lub zdarzenia, aparat Logic Apps tworzy i uruchamia oddzielne wystąpienie przepływu pracy dla każdego elementu lub zdarzenia. Domyślnie każde wystąpienie przepływu pracy jest uruchamiane równolegle, aby nie czekać na uruchomienie przepływu pracy. Możesz sprawdzić, co się stało w trakcie tego uruchomienia, w tym o stanie dla każdego kroku w przepływie pracy oraz dane wejściowe i wyjściowe dla każdego kroku.

1. W [Azure Portal](https://portal.azure.com)Znajdź i Otwórz aplikację logiki w Projektancie aplikacji logiki.

   Aby znaleźć aplikację logiki, w głównym polu wyszukiwania platformy Azure wprowadź `logic apps`, a następnie wybierz pozycję **Logic Apps**.

   ![Znajdź i wybierz usługę "Logic Apps"](./media/monitor-logic-apps/find-your-logic-app.png)

   W Azure Portal są wyświetlane wszystkie aplikacje logiki, które są skojarzone z subskrypcjami platformy Azure. Tę listę można filtrować na podstawie nazwy, subskrypcji, grupy zasobów, lokalizacji i tak dalej.

   ![Wyświetlanie aplikacji logiki skojarzonych z subskrypcjami](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Wybierz aplikację logiki, a następnie wybierz pozycję **Przegląd**.

   W okienku Przegląd w obszarze **historia uruchamiania**zostanie wyświetlona cała przeszła, bieżąca i wszystkie oczekujące uruchomienia aplikacji logiki. Jeśli lista zawiera wiele przebiegów i nie można znaleźć żądanego wpisu, spróbuj przefiltrować listę. Jeśli nie znajdziesz danych, których oczekujesz, spróbuj wybrać pozycję **Odśwież** na pasku narzędzi.

   ![Przegląd, historia uruchamiania i inne informacje o aplikacji logiki](./media/monitor-logic-apps/overview-pane-logic-app-details-run-history.png)

   Oto możliwe stany uruchomienia aplikacji logiki:

   | Stan | Opis |
   |--------|-------------|
   | **Zerwan** | Przepływ pracy został uruchomiony, ale Otrzymano żądanie anulowania |
   | **Niepowodzenie** | Co najmniej jedna akcja nie powiodła się i nie skonfigurowano żadnych późniejszych akcji w przepływie pracy w celu obsługi niepowodzenia |
   | **Uruchomiono** | Przepływ pracy jest obecnie uruchomiony. <p>Ten stan może również występować w przypadku przepływów pracy z ograniczeniami lub z powodu bieżącego planu cenowego. Aby uzyskać więcej informacji, zobacz [limity akcji na stronie cennika](https://azure.microsoft.com/pricing/details/logic-apps/). W przypadku skonfigurowania [rejestrowania diagnostycznego](../logic-apps/monitor-logic-apps.md)można uzyskać informacje o wszelkich zdarzeniach związanych z ograniczeniami, które wystąpiły. |
   | **Powodzenie** | Wszystkie akcje zostały wykonane pomyślnie. <p>**Uwaga**: Jeśli wystąpią jakiekolwiek błędy w określonej akcji, późniejsza akcja w przepływie pracy obsłuży ten błąd. |
   | **Oczekiwanie na** | Przepływ pracy nie został uruchomiony lub został wstrzymany, na przykład ze względu na wcześniejszy przepływ pracy, który nadal działa. |
   |||

1. Aby zapoznać się z krokami i innymi informacjami dotyczącymi określonego uruchomienia, w obszarze **historia uruchamiania**wybierz ten przebieg.

   ![Wybierz konkretny przebieg do przejrzenia](./media/monitor-logic-apps/select-specific-logic-app-run.png)

   Okienko **uruchamiania aplikacji logiki** pokazuje każdy krok w wybranym przebiegu, stan uruchomienia każdego kroku i czas trwania każdego kroku do uruchomienia, na przykład:

   ![Każda akcja w konkretnym przebiegu](./media/monitor-logic-apps/logic-app-run-pane.png)

   Aby wyświetlić te informacje w formularzu listy, na pasku narzędzi **uruchamiania aplikacji logiki** wybierz pozycję **Uruchom szczegóły**.

   ![Na pasku narzędzi wybierz pozycję "Uruchom szczegóły".](./media/monitor-logic-apps/select-run-details-on-toolbar.png)

   Widok Szczegóły uruchamiania przedstawia każdy krok, ich stan i inne informacje.

   ![Przejrzyj szczegółowe informacje o każdym kroku w przebiegu](./media/monitor-logic-apps/review-logic-app-run-details.png)

   Można na przykład uzyskać Właściwość **identyfikatora korelacji** uruchomienia, która może być potrzebna [w przypadku Logic Apps](https://docs.microsoft.com/rest/api/logic).

1. Aby uzyskać więcej informacji na temat określonego kroku, wybierz jedną z opcji:

   * W okienku **uruchamiania aplikacji logiki** zaznacz ten krok, aby kształt został rozwinięty. Możesz teraz wyświetlać informacje, takie jak dane wejściowe, wyjścia i wszystkie błędy, które wystąpiły w tym kroku, na przykład:

     ![W okienku uruchamiania aplikacji logiki Wyświetl krok zakończony niepowodzeniem](./media/monitor-logic-apps/specific-step-inputs-outputs-errors.png)

   * W okienku **szczegółów przebiegu aplikacji logiki** wybierz żądany krok.

     ![W okienku szczegółów uruchamiania Wyświetl krok zakończony niepowodzeniem](./media/monitor-logic-apps/select-failed-step-in-failed-run.png)

     Teraz możesz wyświetlić informacje, takie jak dane wejściowe i wyjściowe dla tego kroku, na przykład:

   > [!NOTE]
   > Wszystkie szczegóły i zdarzenia środowiska uruchomieniowego są szyfrowane w ramach usługi Logic Apps. Są one odszyfrowywane tylko wtedy, gdy użytkownik zażąda wyświetlania tych danych. Można [ukryć dane wejściowe i wyjściowe w historii uruchamiania](../logic-apps/logic-apps-securing-a-logic-app.md#obfuscate) lub kontrolować dostęp użytkowników do tych informacji przy użyciu [Access Control opartego na rolach (RBAC) na platformie Azure](../role-based-access-control/overview.md).

<a name="review-trigger-history"></a>

## <a name="review-trigger-history"></a>Przejrzyj historię wyzwalacza

Każdy przebieg aplikacji logiki rozpoczyna się od wyzwalacza. Historia wyzwalacza zawiera wszystkie próby uruchomienia przez aplikację logiki oraz informacje o danych wejściowych i wyjściowych dla każdej próby wyzwalacza.

1. W [Azure Portal](https://portal.azure.com)Znajdź i Otwórz aplikację logiki w Projektancie aplikacji logiki.

   Aby znaleźć aplikację logiki, w głównym polu wyszukiwania platformy Azure wprowadź `logic apps`, a następnie wybierz pozycję **Logic Apps**.

   ![Znajdź i wybierz usługę "Logic Apps"](./media/monitor-logic-apps/find-your-logic-app.png)

   W Azure Portal są wyświetlane wszystkie aplikacje logiki, które są skojarzone z subskrypcjami platformy Azure. Tę listę można filtrować na podstawie nazwy, subskrypcji, grupy zasobów, lokalizacji i tak dalej.

   ![Wyświetlanie aplikacji logiki skojarzonych z subskrypcjami](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Wybierz aplikację logiki, a następnie wybierz pozycję **Przegląd**.

1. W menu aplikacji logiki wybierz pozycję **Przegląd**. W sekcji **Podsumowanie** w obszarze **Ocena**wybierz pozycję **Zobacz historię wyzwalacza**.

   ![Wyświetlanie historii wyzwalania dla aplikacji logiki](./media/monitor-logic-apps/overview-pane-logic-app-details-trigger-history.png)

   W okienku historia wyzwalacza są wyświetlane wszystkie próby uruchomienia aplikacji logiki. Za każdym razem, gdy wyzwalacz jest wyzwalany dla elementu lub zdarzenia, aparat Logic Apps tworzy oddzielne wystąpienie aplikacji logiki, które uruchamia przepływ pracy. Domyślnie każde wystąpienie jest uruchamiane równolegle, aby nie czekać na uruchomienie przepływu pracy. Dlatego jeśli aplikacja logiki jest wyzwalana na wielu elementach, w tym samym czasie zostanie wyświetlony wpis wyzwalacza o tej samej dacie i godzinie dla każdego elementu.

   ![Wiele prób wyzwalacza dla różnych elementów](./media/monitor-logic-apps/logic-app-trigger-history.png)

   Oto możliwe stany próby uruchomienia wyzwalacza:

   | Stan | Opis |
   |--------|-------------|
   | **Niepowodzenie** | Wystąpił błąd. Aby przejrzeć wszystkie wygenerowane komunikaty o błędach dla wyzwalacza, wybierz tę próbę, a następnie wybierz pozycję dane **wyjściowe**. Na przykład mogą znajdować się dane wejściowe, które nie są prawidłowe. |
   | **Pominięto** | Wyzwalacz sprawdzał punkt końcowy, ale nie znalazł danych. |
   | **Powodzenie** | Wyzwalacz sprawdzał punkt końcowy i znalazł dostępne dane. Zwykle pojawia się również stan "uruchomił" obok tego stanu. W przeciwnym razie definicja wyzwalacza może mieć warunek lub `SplitOn` polecenie, które nie zostało spełnione. <p>Ten stan może dotyczyć wyzwalacza ręcznego, wyzwalacza cyklu lub wyzwalacza sondowania. Wyzwalacz może zostać uruchomiony pomyślnie, ale uruchomienie może zakończyć się niepowodzeniem, gdy akcje generują nieobsłużone błędy. |
   |||

   > [!TIP]
   > Wyzwalacz można ponownie sprawdzić bez czekania na następny cykl. Na pasku narzędzi przegląd wybierz pozycję **Uruchom wyzwalacz**, a następnie wybierz wyzwalacz, który wymusza sprawdzenie. Lub zaznacz opcję **Uruchom** na pasku narzędzi projektanta Logic Apps.

1. Aby wyświetlić informacje dotyczące konkretnej próby wyzwalacza, w okienku wyzwalacz wybierz to zdarzenie wyzwalacza. Jeśli lista zawiera wiele prób wyzwalacza i nie można znaleźć żądanego wpisu, spróbuj przefiltrować listę. Jeśli nie znajdziesz danych, których oczekujesz, spróbuj wybrać pozycję **Odśwież** na pasku narzędzi.

   ![Wyświetl próbkę określonego wyzwalacza](./media/monitor-logic-apps/select-trigger-event-for-review.png)

   Teraz możesz przejrzeć informacje o wybranym zdarzeniu wyzwalacza, na przykład:

   ![Wyświetl informacje dotyczące określonego wyzwalacza](./media/monitor-logic-apps/view-specific-trigger-details.png)

<a name="add-azure-alerts"></a>

## <a name="set-up-monitoring-alerts"></a>Konfigurowanie alertów monitorowania

Aby otrzymywać alerty w oparciu o konkretne metryki lub przekroczenia progów dla aplikacji logiki, skonfiguruj [alerty w Azure monitor](../azure-monitor/platform/alerts-overview.md). Dowiedz się więcej o [metrykach na platformie Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md). Aby skonfigurować alerty bez używania [Azure monitor](../log-analytics/log-analytics-overview.md), wykonaj następujące kroki.

1. W menu aplikacji logiki w obszarze **monitorowanie**wybierz pozycję **alerty** > **Nowa reguła alertu**.

   ![Dodawanie alertu dla aplikacji logiki](./media/monitor-logic-apps/add-new-alert-rule.png)

1. W okienku **Tworzenie reguły** w obszarze **zasób**wybierz aplikację logiki, jeśli nie została jeszcze wybrana. W obszarze **warunek**wybierz pozycję **Dodaj** , aby można było zdefiniować warunek wyzwalający alert.

   ![Dodaj warunek dla reguły](./media/monitor-logic-apps/add-condition-for-rule.png)

1. W okienku **Konfigurowanie logiki sygnału** Znajdź i wybierz sygnał, dla którego chcesz uzyskać alert. Możesz użyć pola wyszukiwania lub aby posortować sygnały alfabetycznie, wybierz nagłówek kolumny **Nazwa sygnału** .

   Jeśli na przykład chcesz wysłać Alert w przypadku niepowodzenia wyzwalacza, wykonaj następujące czynności:

   1. W kolumnie **Nazwa sygnału** Znajdź i wybierz sygnał **wyzwalacze zakończone niepowodzeniem** .

      ![Wybierz sygnał do tworzenia alertu](./media/monitor-logic-apps/find-and-select-signal.png)

   1. W okienku informacji, które zostanie otwarte dla wybranego sygnału, w obszarze **logika alertu**Skonfiguruj swój stan, na przykład:

   1. Dla **operatora**wybierz wartość **większą lub równą**.

   1. W obszarze **typ agregacji**wybierz pozycję **Liczba**.

   1. W obszarze **wartość progowa**wprowadź `1`.

   1. W obszarze **Podgląd warunku**upewnij się, że Twój warunek jest prawidłowy.

   1. W obszarze **oceniane na podstawie**Skonfiguruj interwał i częstotliwość uruchamiania reguły alertu. W celu uzyskania **stopnia szczegółowości agregacji (okres)** Wybierz okres grupowania danych. W polu **częstotliwość oceny**wybierz, jak często chcesz sprawdzić warunek.

   1. Gdy wszystko będzie gotowe, wybierz pozycję **gotowe**.

   Oto warunek zakończenia:

   ![Skonfiguruj warunek dla alertu](./media/monitor-logic-apps/set-up-condition-for-alert.png)

   Na stronie **Tworzenie reguły** zostanie wyświetlony utworzony warunek i koszt uruchomienia tego alertu.

   ![Nowy alert na stronie "Tworzenie reguły"](./media/monitor-logic-apps/finished-alert-condition-cost.png)

1. Określ nazwę, opcjonalny opis i poziom ważności alertu. Pozostaw ustawienie **Włącz regułę po** włączeniu lub wyłącz je do momentu, aż wszystko będzie gotowe do włączenia reguły.

1. Gdy skończysz, wybierz pozycję **Utwórz regułę alertu**.

> [!TIP]
> Aby uruchomić aplikację logiki z poziomu alertu, można dołączyć [wyzwalacz żądania](../connectors/connectors-native-reqres.md) w przepływie pracy, który umożliwia wykonywanie zadań takich jak następujące przykłady:
> 
> * [Opublikuj do zapasu czasu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Wyślij tekst](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Dodawanie komunikatu do kolejki](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie aplikacji logiki przy użyciu Azure Monitor](../logic-apps/monitor-logic-apps-log-analytics.md)