---
title: Monitoruj kondycję i użycie aplikacji przy użyciu Application Insights
description: Rozpocznij pracę z Application Insights. Analizowanie użycia, dostępności i wydajności aplikacji lokalnych lub Microsoft Azure.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/10/2018
ms.reviewer: sdash
ms.openlocfilehash: ebf6fa6d3dac6c63dfaa349a77a08bc81d402ef8
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899301"
---
# <a name="monitor-performance-in-web-applications"></a>Monitorowanie wydajności w aplikacjach internetowych


Upewnij się, że aplikacja działa prawidłowo, i Dowiedz się szybko o wszelkich błędach. [Application Insights][start] poinformuje o wszelkich problemach z wydajnością i wyjątkach oraz ułatwia znalezienie i zdiagnozowanie głównych przyczyn.

Application Insights może monitorować zarówno aplikacje i usługi sieci Web w języku Java, jak i ASP.NET, usługi WCF. Mogą być hostowane lokalnie, na maszynach wirtualnych lub jako Microsoft Azure witryn sieci Web. 

Po stronie klienta Application Insights może przyjmować dane telemetryczne ze stron sieci Web i różne urządzenia, w tym aplikacje dla systemów iOS, Android i Windows.

## <a name="setup"></a>Konfigurowanie monitorowania wydajności
Jeśli Application Insights nie zostały jeszcze dodane do projektu (oznacza to, że jeśli nie ma pliku ApplicationInsights. config), wybierz jedną z następujących metod, aby rozpocząć pracę:

* [ASP.NET aplikacje sieci Web](../../azure-monitor/app/asp-net.md)
  * [Dodaj Monitorowanie wyjątków](../../azure-monitor/app/asp-net-exceptions.md)
  * [Dodaj monitorowanie zależności](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Aplikacje sieci Web Java EE](../../azure-monitor/app/java-get-started.md)
  * [Dodaj monitorowanie zależności](../../azure-monitor/app/java-agent.md)

## <a name="view"></a>Eksplorowanie metryk wydajności
W [Azure Portal](https://portal.azure.com)przejdź do zasobu Application Insights, który został skonfigurowany dla aplikacji. W bloku przeglądu są wyświetlane podstawowe dane dotyczące wydajności:

Kliknij dowolny wykres, aby wyświetlić więcej szczegółów i wyświetlić wyniki dłuższy okres. Na przykład kliknij kafelek żądania, a następnie wybierz zakres czasu:

![Kliknij, aby uzyskać więcej danych i wybrać zakres czasu](./media/web-monitor-performance/appinsights-48metrics.png)

Kliknij wykres, aby wybrać metryki, które są wyświetlane, lub Dodaj nowy wykres i wybierz jego metryki:

![Kliknij wykres, aby wybrać metryki](./media/web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Usuń zaznaczenie wszystkich metryk** , aby zobaczyć pełne dostępne opcje. Metryki wchodzą w zakres grup; w przypadku wybrania dowolnego elementu członkowskiego grupy pojawiają się tylko inni członkowie tej grupy.

## <a name="metrics"></a>Co to znaczy? Kafelki i raporty wydajności
Istnieją różne metryki wydajności, które można uzyskać. Zacznijmy od tych, które są domyślnie wyświetlane w bloku aplikacji.

### <a name="requests"></a>Żądania
Liczba odebranych żądań HTTP w określonym przedziale czasu. Porównaj ją z wynikami innych raportów, aby zobaczyć, jak działa aplikacja, gdy obciążenie jest różne.

Żądania HTTP obejmują wszystkie żądania GET lub POST dla stron, danych i obrazów.

Kliknij kafelek, aby uzyskać liczniki dla określonych adresów URL.

### <a name="average-response-time"></a>Średni czas odpowiedzi
Mierzy czas między żądaniem sieci Web wprowadzającym aplikację a zwracaną odpowiedzią.

Punkty pokazują średnią wartość przesunięcia. W przypadku wielu żądań może istnieć kilka odchyleń od średniej bez oczywistego szczytu lub wartości DIP na wykresie.

Poszukaj nietypowych szczytów. Ogólnie rzecz biorąc, oczekiwany jest czas odpowiedzi na wzrost liczby żądań. Jeśli wzrost jest nieproporcjonalny, aplikacja może być powodem limitu zasobów, takiego jak procesor CPU lub pojemność usługi, której używa.

Kliknij kafelek, aby uzyskać czasy dla określonych adresów URL.

![](./media/web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Najwolniejsze żądania
![](./media/web-monitor-performance/appinsights-44slowest.png)

Pokazuje, które żądania mogą wymagać dostrajania wydajności.

### <a name="failed-requests"></a>Żądania zakończone niepowodzeniem
![](./media/web-monitor-performance/appinsights-46failed.png)

Liczba żądań, które wywołały nieprzechwycone wyjątki.

Kliknij kafelek, aby wyświetlić szczegóły określonych błędów, a następnie wybierz pojedyncze żądanie, aby wyświetlić jego szczegóły. 

Tylko reprezentatywna próbka błędów jest zachowywana na potrzeby indywidualnej inspekcji.

### <a name="other-metrics"></a>Inne metryki
Aby zobaczyć, jakie inne metryki można wyświetlić, kliknij wykres, a następnie usuń zaznaczenie wszystkich metryk, aby zobaczyć pełny dostępny zestaw. Kliknij (i), aby zobaczyć definicję każdej metryki.

![Usuń zaznaczenie wszystkich metryk, aby wyświetlić cały zestaw](./media/web-monitor-performance/appinsights-62allchoices.png)

Wybranie dowolnej metryki powoduje wyłączenie innych, które nie są wyświetlane na tym samym wykresie.

## <a name="set-alerts"></a>Ustawianie alertów
Aby otrzymywać powiadomienia pocztą e-mail o nietypowych wartościach dowolnej metryki, Dodaj alert. Możesz wybrać opcję wysłania wiadomości e-mail do administratorów konta lub do określonych adresów e-mail.

![](./media/web-monitor-performance/appinsights-413setMetricAlert.png)

Ustaw zasób przed innymi właściwościami. Nie wybieraj zasobów WebTest, jeśli chcesz ustawić alerty dotyczące wydajności lub metryk użycia.

Należy zwrócić uwagę na jednostki, w których zostanie wyświetlony monit o wprowadzenie wartości progowej.

*Nie widzę przycisku Dodaj alert.* -Czy to konto grupy, do którego masz dostęp tylko do odczytu? Skontaktuj się z administratorem konta.

## <a name="diagnosis"></a>Diagnozowanie problemów
Oto kilka porad dotyczących znajdowania i diagnozowania problemów z wydajnością:

* Skonfiguruj [testy sieci Web][availability] , aby otrzymywać alerty, jeśli witryna sieci Web ulegnie awarii lub reaguje niepoprawnie lub powoli. 
* Porównaj liczbę żądań z innymi metrykami, aby sprawdzić, czy występują błędy lub powolna odpowiedź do załadowania.
* [Wstawianie i przeszukiwanie instrukcji Trace][diagnostic] w kodzie, aby pomóc w wykrywaniu problemów.
* Monitoruj aplikację sieci Web w ramach operacji przy użyciu [Live Metrics Stream][livestream].
* Przechwyć stan aplikacji .NET za pomocą [Snapshot Debugger][snapshot].

## <a name="find-and-fix-performance-bottlenecks-with-performance-investigation-experience"></a>Znajdowanie i rozwiązywanie wąskich gardeł wydajności przy użyciu środowiska badania wydajności

Możesz użyć środowiska badania wydajności, aby przejrzeć wolno wykonywane operacje w aplikacji sieci Web. Można szybko wybrać konkretną operację powolnej i użyć [profilera](../../azure-monitor/app/profiler.md) do katalogu głównego, co spowoduje spowolnienie operacji w kodzie. Korzystając z nowego rozkładu czasu trwania podanego dla wybranej operacji, możesz szybko ocenić, jak to jest niewłaściwe dla klientów. Można sprawdzić, jak wiele interakcji użytkownika miało wpływ na każdą powolne działanie. W poniższym przykładzie poprosimy o zapoznaniu się z doświadczeniem w zakresie pobierania klientów/szczegółów operacji. W dystrybucji czasu trwania można zobaczyć, że istnieją trzy skoki. Lewy skok to około 400 MS i reprezentuje doskonałe środowisko reagowania. Drugie przybliżenie jest około 1,2 s i przedstawia środowisko mediocre. Na koniec 3,6 s mamy kolejną małą liczbę, która reprezentuje 99 percentyl, co może spowodować niespełnienie naszych klientów. To środowisko działa dziesięć razy wolniej niż doskonałe środowisko dla tej samej operacji. 

![Uzyskaj klientów/szczegóły trzech skoków czasu trwania](./media/web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

Aby lepiej zrozumieć środowisko użytkownika dla tej operacji, można wybrać większy zakres czasu. Możemy również zawęzić w czasie w określonym przedziale czasu, w którym operacja była niska. W poniższym przykładzie przejdziemy od domyślnego 24-godzinnego zakresu czasowego do 7 dni, a następnie powiększamy do przedziału od 9:47 do 12:47 czasu między WT a od 13. Zarówno rozkład czasu trwania, jak i liczba śladów przykładu i profilera zostały zaktualizowane po prawej stronie.

![Uzyskaj klientów/szczegóły trzech okresów trwania w ciągu 7 dni zakresu z przedziałem czasu](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

Aby zawęzić w przypadku wolnych środowisk, należy dalej powiększyć czas trwania między używany 95. a 99 percentylu. Reprezentują one 4% interakcji z użytkownikami, które były powolne.

![Uzyskaj klientów/szczegóły trzech okresów trwania w ciągu 7 dni zakresu z przedziałem czasu](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

Teraz możemy przyjrzeć się reprezentatywnym przykładom, klikając przycisk przykłady lub na reprezentatywnych śladów profilera, klikając przycisk ślady profilera. W tym przykładzie wprowadzono cztery ślady, które zostały zebrane w celu uzyskania klientom/szczegóły w przedziale czasu i przedziały okresu zainteresowania.

Czasami problem nie będzie znajdował się w kodzie, ale raczej w zależności od kodu. Aby zbadać takie wolne zależności, możesz przełączyć się na kartę zależności w widoku Klasyfikacja wydajności. Domyślnie widok wydajności jest trendem średnim, ale na pewno chcesz się zapoznać z używany 95. percentylem (lub 99, w przypadku monitorowania usługi dla dorosłych). W poniższym przykładzie koncentrujemy się na powolnej zależności obiektów BLOB platformy Azure, w której wywoływana jest funkcja PUT fabrikamaccount. Klaster dobrego środowiska działa wokół 40 MS, podczas gdy powolne wywołania tej samej zależności są trzy razy wolniejsze, klaster wokół 120 MS. Nie przyjmuje wielu z tych wywołań, aby można było dodać do nich nieprzerwane działanie. Możesz przejść do szczegółów reprezentatywnych przykładów i śladów profilera, tak jak w przypadku karty operacje.

![Uzyskaj klientów/szczegóły trzech okresów trwania w ciągu 7 dni zakresu z przedziałem czasu](./media/web-monitor-performance/SlowDependencies95thTrend.png)

Środowisko badania wydajności pokazuje odpowiednie szczegółowe informacje po stronie zestawu przykładowego, na którym zamierzasz się skupić. Najlepszym sposobem, aby poznać wszystkie dostępne informacje, jest przełączenie na 30-dniowy zakres czasu, a następnie wybierz pozycję Ogólne, aby zobaczyć szczegółowe informacje na temat wszystkich operacji w ostatnim miesiącu.

![Uzyskaj klientów/szczegóły trzech okresów trwania w ciągu 7 dni zakresu z przedziałem czasu](./media/web-monitor-performance/Performance30DayOveralllnsights.png)


## <a name="next"></a>Następne kroki
[Testy sieci Web][availability] — żądania sieci Web wysyłane do aplikacji w regularnych odstępach czasu z całego świata.

[Przechwytywanie i wyszukiwanie śladów diagnostycznych][diagnostic] — Wstawianie wywołań śledzenia i przechodzenie przez wyniki do lokalizowania problemów.

[Śledzenie użycia][usage] — Dowiedz się, jak użytkownicy korzystają z Twojej aplikacji.

[Rozwiązywanie problemów][qna] — i Q & a



<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[usage]: usage-overview.md
[livestream]: ../../azure-monitor/app/live-stream.md
[snapshot]: ../../azure-monitor/app/snapshot-debugger.md



