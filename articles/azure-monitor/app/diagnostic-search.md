---
title: Korzystanie z wyszukiwania w usłudze Azure Application Insights | Microsoft Docs
description: Wyszukiwanie i filtrowanie nieprzetworzonej telemetrii wysyłanej przez aplikację sieci Web.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/30/2019
ms.openlocfilehash: 77cd0a8d0c1a93e7dc1db931e987a172d31978ef
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678055"
---
# <a name="using-search-in-application-insights"></a>Korzystanie z wyszukiwania w Application Insights

Wyszukiwanie jest funkcją [Application Insights](../../azure-monitor/app/app-insights-overview.md) , która służy do znajdowania i eksplorowania poszczególnych elementów telemetrii, takich jak widoki stron, wyjątki lub żądania sieci Web. Można także wyświetlać dane śledzenia i zdarzenia dzienników.

(Aby uzyskać bardziej skomplikowane zapytania dotyczące danych, użyj [analizy](../../azure-monitor/log-query/get-started-portal.md)).

## <a name="where-do-you-see-search"></a>Gdzie widzisz wyszukiwanie?

### <a name="in-the-azure-portal"></a>W witrynie Azure Portal

Możesz otworzyć wyszukiwanie diagnostyczne na karcie Przegląd Application Insights aplikacji (znajdującej się na górnym pasku) lub w obszarze Zbadaj po lewej stronie.

![Karta wyszukiwanie](./media/diagnostic-search/view-custom-events.png)

Przejdź do menu rozwijanego typy zdarzeń, aby wyświetlić listę elementów telemetrii — żądania serwera, wyświetlenia stron, zdarzenia niestandardowe, które zostały zakodowane i tak dalej. W górnej części listy wyników jest Wykres podsumowujący przedstawiający liczbę zdarzeń w czasie.

Kliknij menu rozwijane lub Odśwież, aby pobrać nowe zdarzenia.

### <a name="in-visual-studio"></a>W programie Visual Studio

W programie Visual Studio istnieje również okno wyszukiwania Application Insights. Jest to najbardziej przydatne do wyświetlania zdarzeń telemetrii generowanych przez debugowaną aplikację. Ale mogą również wyświetlać zdarzenia zebrane z opublikowanej aplikacji w Azure Portal.

Otwórz okno wyszukiwania w programie Visual Studio:

![Application Insights wyszukiwanie w programie Visual Studio](./media/diagnostic-search/32.png)

Okno wyszukiwania zawiera funkcje podobne do portalu sieci Web:

![Okno wyszukiwania Application Insights programu Visual Studio](./media/diagnostic-search/34.png)

Karta operacja śledzenia jest dostępna podczas otwierania żądania lub widoku strony. "Operation" to sekwencja zdarzeń skojarzonych z pojedynczym żądaniem lub widokiem strony. Na przykład wywołania zależności, wyjątki, dzienniki śledzenia i zdarzenia niestandardowe mogą być częścią pojedynczej operacji. Karta operacja śledzenia przedstawia graficzny czas i czas trwania tych zdarzeń w odniesieniu do żądania lub widoku strony.

## <a name="inspect-individual-items"></a>Inspekcja pojedynczych elementów

Wybierz dowolny element telemetrii, aby wyświetlić pola klucza i powiązane elementy.

![Zrzut ekranu żądania indywidualnej zależności](./media/diagnostic-search/telemetry-item.png)

Spowoduje to uruchomienie widoku szczegółów transakcji end-to-end.

## <a name="filter-event-types"></a>Filtruj typy zdarzeń

Otwórz menu rozwijane typy zdarzeń i wybierz typy zdarzeń, które chcesz wyświetlić. (Jeśli później chcesz przywrócić filtry, kliknij przycisk Resetuj).

Typy zdarzeń są następujące:

* **Śledzenie**  - [dzienników diagnostycznych](../../azure-monitor/app/asp-net-trace-logs.md) , w tym wywołań TrackTrace, Log4Net, nLOG i system. Diagnostics. Trace.
* Żądania **żądania** — http odbierane przez aplikację serwera, w tym strony, skrypty, obrazy, pliki stylów i dane. Te zdarzenia są używane do tworzenia wykresów przeglądów żądań i odpowiedzi.
* **Widok strony**  -  dane[telemetryczne wysyłane przez klienta sieci Web](../../azure-monitor/app/javascript.md), używane do tworzenia raportów widoku strony.
* **Zdarzenie niestandardowe** — w przypadku wstawienia wywołań do poleceń trackEvent () w celu [monitorowania użycia](../../azure-monitor/app/api-custom-events-metrics.md)można wyszukiwać je w tym miejscu.
* Nieprzechwycone [wyjątki na serwerze](../../azure-monitor/app/asp-net-exceptions.md)oraz te, które są rejestrowane za pomocą funkcji trackexception ().
* **Zależność**  - [wywołań z aplikacji serwerowej](../../azure-monitor/app/asp-net-dependencies.md) do innych usług, takich jak interfejsy API REST lub bazy danych, oraz wywołań AJAX z [kodu klienta](../../azure-monitor/app/javascript.md).
* **Dostępność** — wyniki [testów dostępności](../../azure-monitor/app/monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>Filtrowanie wartości właściwości

Można filtrować zdarzenia na wartościach ich właściwości. Dostępne właściwości zależą od wybranych typów zdarzeń. Kliknij ikonę filtru ![Ikona filtru](./media/diagnostic-search/filter-icon.png) do uruchomienia.

Wybór żadnej wartości właściwości nie ma tego samego efektu co wybór wszystkich wartości. Włącza on filtrowanie dla tej właściwości.

Zwróć uwagę, że liczba po prawej stronie wartości filtru pokazuje, ile wystąpień znajduje się w bieżącym filtrowanym zestawie.

## <a name="find-events-with-the-same-property"></a>Znajdź zdarzenia z tą samą właściwością

Aby znaleźć wszystkie elementy z tą samą wartością właściwości, wpisz ją na pasku wyszukiwania lub kliknij pole wyboru podczas przeglądania właściwości na karcie filtr.

![Kliknij pole wyboru właściwości na karcie filtr](./media/diagnostic-search/filter-property.png)

## <a name="search-the-data"></a>Przeszukaj dane

> [!NOTE]
> Aby napisać bardziej złożone zapytania, Otwórz [**dzienniki (analiza)** ](../../azure-monitor/log-query/get-started-portal.md) w górnej części bloku wyszukiwania.
>

Warunki można wyszukać w dowolnych wartościach właściwości. Jest to przydatne, jeśli masz zapisaną [zdarzenia niestandardowe](../../azure-monitor/app/api-custom-events-metrics.md) z wartościami właściwości.

Może zajść potrzeba ustawienia zakresu czasu, ponieważ wyszukiwanie w krótszym zakresie jest szybsze.

![Otwórz wyszukiwanie diagnostyczne](./media/diagnostic-search/search-property.png)

Wyszukaj kompletne słowa, nie podciągi. Znaki specjalne należy ująć w cudzysłów.

| Ciąg | *Nie* znaleziono | Uznan |
| --- | --- | --- |
| HomeController. informacje |`home`<br/>`controller`<br/>`out` | `homecontroller`<br/>`about`<br/>`"homecontroller.about"`|
|Stany Zjednoczone|`Uni`<br/>`ted`|`united`<br/>`states`<br/>`united AND states`<br/>`"united states"`

Oto wyrażenia wyszukiwania, których można użyć:

| Przykładowe zapytanie | Efekt |
| --- | --- |
| `apple` |Znajdź wszystkie zdarzenia w przedziale czasu, których pola zawierają wyraz "Apple" |
| `apple AND banana` <br/>`apple banana` |Znajdź zdarzenia zawierające oba słowa. Użyj wielkiej litery "i", a nie "i". <br/>Krótka forma. |
| `apple OR banana` |Znajdź zdarzenia zawierające dowolny wyraz. Użyj "OR", nie "or". |
| `apple NOT banana` |Znajdź zdarzenia, które zawierają jeden wyraz, ale nie inne. |

## <a name="sampling"></a>Próbkowanie

Jeśli aplikacja generuje dużą ilość danych telemetrycznych (i używasz zestawu ASP.NET SDK w wersji 2.0.0-beta3 lub nowszej), moduł próbkowania adaptacyjnego automatycznie zmniejsza ilość danych wysyłanych do portalu, wysyłając tylko reprezentatywną część zdarzeń. Jednak zdarzenia powiązane z tym samym żądaniem są wybierane lub dewybierane jako Grupa, aby można było nawigować między powiązanymi zdarzeniami.

[Więcej informacji na temat próbkowania](../../azure-monitor/app/sampling.md).

## <a name="create-work-item"></a>Utwórz element roboczy

Usterkę w usłudze GitHub lub Azure DevOps można utworzyć przy użyciu szczegółów z dowolnego elementu telemetrii.

Przejdź do widoku szczegółów transakcji end-to-end, klikając dowolny element telemetrii, a następnie wybierz pozycję **Utwórz element roboczy**.

![Kliknij pozycję Nowy element roboczy, Edytuj pola, a następnie kliknij przycisk OK.](./media/diagnostic-search/work-item.png)

Po raz pierwszy zostanie wyświetlony monit o skonfigurowanie linku do organizacji i projektu usługi Azure DevOps.

(Można również skonfigurować łącze na karcie elementy robocze).

## <a name="send-more-telemetry-to-application-insights"></a>Wyślij więcej danych telemetrycznych do Application Insights

Oprócz wbudowanej telemetrii wysyłanej przez Application Insights SDK można:

* Przechwytuj ślady dzienników z ulubionej struktury rejestrowania w programie [.NET](../../azure-monitor/app/asp-net-trace-logs.md) lub [Java](../../azure-monitor/app/java-trace-logs.md). Oznacza to, że można przeszukiwać dane śledzenia dzienników i skorelować je z widokami stron, wyjątkami i innymi zdarzeniami.
* [Pisanie kodu](../../azure-monitor/app/api-custom-events-metrics.md) w celu wysyłania niestandardowych zdarzeń, wyświetleń stron i wyjątków.

[Dowiedz się, jak wysyłać dzienniki i niestandardową telemetrię do Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).

## <a name="questions"></a>p & A

### <a name="limits"></a>Ile danych jest przechowywanych?

Zobacz [Podsumowanie limitów](../../azure-monitor/app/pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Jak można zobaczyć dane POST w moich żądaniach serwera?

Dane POST nie są rejestrowane automatycznie, ale można użyć [wywołań TrackTrace lub log](../../azure-monitor/app/asp-net-trace-logs.md). Umieść dane POST w parametrze Message. Nie można filtrować wiadomości w taki sam sposób, w jaki można filtrować według właściwości, ale limit rozmiaru jest dłuższy.

## <a name="add"></a>Następne kroki

* [Zapisuj złożone zapytania w analizie](../../azure-monitor/log-query/get-started-portal.md)
* [Wyślij dzienniki i niestandardową telemetrię do Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
* [Konfigurowanie testów dostępności i czasu odpowiedzi](../../azure-monitor/app/monitor-web-app-availability.md)
* [Rozwiązywanie problemów](../../azure-monitor/app/troubleshoot-faq.md)
