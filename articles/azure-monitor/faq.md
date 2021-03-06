---
title: Azure Monitor często zadawane pytania | Microsoft Docs
description: Odpowiedzi na często zadawane pytania dotyczące Azure Monitor.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/23/2020
ms.openlocfilehash: 33af9c8f3fbe4de57d65be432f249d4aeb5d3e27
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485339"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Azure Monitor często zadawane pytania

Ta firma Microsoft — często zadawane pytania dotyczące Azure Monitor.

## <a name="general"></a>Ogólne

### <a name="what-is-azure-monitor"></a>Co to jest Azure Monitor?
[Azure monitor](overview.md) to usługa platformy Azure, która zapewnia monitorowanie wydajności i dostępności aplikacji i usług na platformie Azure, w innych środowiskach w chmurze lub lokalnie. Azure Monitor zbiera dane z wielu źródeł na wspólną platformę danych, dzięki czemu można je analizować pod kątem trendów i anomalii. Bogate funkcje w Azure Monitor ułatwiają szybkie identyfikowanie i reagowanie na krytyczne sytuacje, które mogą mieć wpływ na aplikację.

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Jaka jest różnica między Azure Monitor, Log Analytics i Application Insights?
We wrześniu 2018 firma Microsoft połączona Azure Monitor, Log Analytics i Application Insights w ramach jednej usługi, aby zapewnić kompleksowe monitorowanie aplikacji i składników, z których korzystają. Funkcje w Log Analytics i Application Insights nie uległy zmianie, mimo że niektóre funkcje zostały oznaczone jako Azure Monitor w celu lepszego odzwierciedlenia ich nowego zakresu. Aparat danych dziennika i język zapytań Log Analytics są teraz nazywane dziennikami Azure Monitor. Zobacz [Azure monitor aktualizacje terminologii](terminology.md).

### <a name="what-does-azure-monitor-cost"></a>Co Azure Monitor koszty?
Funkcje Azure Monitor, które są automatycznie włączone, takie jak zbieranie metryk i dzienników aktywności, są udostępniane bezpłatnie. Istnieje koszt związany z innymi funkcjami, takimi jak kwerendy dzienników i alerty. Szczegółowe informacje o cenach można znaleźć na [stronie cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) .

### <a name="how-do-i-enable-azure-monitor"></a>Jak mogę włączyć Azure Monitor?
Azure Monitor jest włączona, gdy tworzysz nową subskrypcję platformy Azure, a [metryki](platform/data-platform-metrics.md) [dzienników aktywności](platform/activity-logs-overview.md) i platformy są zbierane automatycznie. Tworzenie [ustawień diagnostycznych](platform/diagnostic-settings.md) w celu zbierania bardziej szczegółowych informacji na temat działania zasobów platformy Azure oraz dodawania [rozwiązań do monitorowania](insights/solutions.md) i [szczegółowych](insights/insights-overview.md) danych w celu zapewnienia dodatkowej analizy zebranych dane dla konkretnych usług. 

### <a name="how-do-i-access-azure-monitor"></a>Jak mogę Azure Monitor dostępu?
Dostęp do wszystkich funkcji Azure Monitor i danych z menu **monitor** w Azure Portal. Sekcja **monitorowanie** w menu dla różnych usług platformy Azure zapewnia dostęp do tych samych narzędzi z danymi filtrowanymi do określonego zasobu. Azure Monitor dane są również dostępne dla różnych scenariuszy przy użyciu interfejsu wiersza polecenia, programu PowerShell i API REST.

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>Czy istnieje lokalna wersja Azure Monitor?
Nie. Azure Monitor to skalowalna usługa w chmurze, która przetwarza i przechowuje duże ilości danych, chociaż Azure Monitor mogą monitorować zasoby, które są lokalnie i w innych chmurach.

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>Czy Azure Monitor monitorować zasoby lokalne?
Tak, oprócz zbierania danych monitorowania z zasobów platformy Azure, Azure Monitor może zbierać dane z maszyn wirtualnych i aplikacji w innych chmurach i lokalnych. Zobacz [źródła danych monitorowania dla Azure monitor](platform/data-sources.md).

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>Czy Azure Monitor integrują się z System Center Operations Manager?
Możesz połączyć istniejącą grupę zarządzania System Center Operations Manager, aby Azure Monitor do zbierania danych z agentów do dzienników Azure Monitor. Dzięki temu można używać zapytań dzienników i rozwiązania do analizowania danych zebranych z agentów. Możesz również skonfigurować istniejących agentów System Center Operations Manager, aby wysyłać dane bezpośrednio do Azure Monitor. Zobacz [Connect Operations Manager, aby Azure monitor](platform/om-agents.md).

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Jakie adresy IP są używane Azure Monitor?
Zobacz [adresy IP używane przez Application Insights i log Analytics](app/ip-addresses.md) , aby uzyskać listę adresów IP i portów wymaganych przez agentów i inne zasoby zewnętrzne w celu uzyskania dostępu do Azure monitor. 

## <a name="monitoring-data"></a>Dane monitorowania

### <a name="where-does-azure-monitor-get-its-data"></a>Gdzie usługa Azure Monitor pobierze swoje dane?
Azure Monitor zbiera dane z różnych źródeł, w tym dzienników i metryk z platformy Azure i zasobów, niestandardowych aplikacji i agentów działających na maszynach wirtualnych. Inne usługi, takie jak Azure Security Center i Network Watcher zbierają dane do obszaru roboczego Log Analytics, dzięki czemu można je analizować przy użyciu Azure Monitor danych. Możesz również wysyłać dane niestandardowe do Azure Monitor przy użyciu interfejsu API REST dla dzienników lub metryk. Zobacz [źródła danych monitorowania dla Azure monitor](platform/data-sources.md).

### <a name="what-data-is-collected-by-azure-monitor"></a>Jakie dane są zbierane przez Azure Monitor? 
Azure Monitor zbiera dane z różnych źródeł w [dziennikach](platform/data-platform-logs.md) lub [metrykach](platform/data-platform-metrics.md). Każdy typ danych ma własne względne zalety, a każdy z nich obsługuje konkretny zestaw funkcji w Azure Monitor. Istnieje jedna baza danych metryk dla każdej subskrypcji platformy Azure, podczas gdy można utworzyć wiele obszarów roboczych Log Analytics do zbierania dzienników w zależności od wymagań. Zobacz [Azure monitor platformę danych](platform/data-platform.md).

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>Czy istnieje maksymalna ilość danych, które mogę zebrać w Azure Monitor?
Nie ma żadnego limitu ilości zbieranych danych metryki, ale dane te są przechowywane przez maksymalnie 93 dni. Zobacz [przechowywanie metryk](platform/data-platform-metrics.md#retention-of-metrics). Nie ma żadnego limitu ilości danych dziennika, które można zebrać, ale może to mieć wpływ na warstwę cenową wybraną dla obszaru roboczego Log Analytics. Zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>Jak mogę dostęp do danych zbieranych przez Azure Monitor?
Szczegółowe informacje i rozwiązania zapewniają niestandardowe środowisko pracy z danymi przechowywanymi w Azure Monitor. Możesz bezpośrednio korzystać z danych dziennika przy użyciu zapytania dziennika zarejestrowanego w Kusto Query Language (KQL). W Azure Portal można pisać i uruchamiać zapytania oraz interaktywnie analizować dane przy użyciu Log Analytics. Analizuj metryki w Azure Portal przy użyciu Eksplorator metryk. Zobacz [Analizowanie danych dziennika w Azure monitor](log-query/log-query-overview.md) i rozpoczynanie [pracy z usługą Azure Eksplorator metryk](platform/metrics-getting-started.md).





## <a name="solutions-and-insights"></a>Rozwiązania i szczegółowe informacje

### <a name="what-is-an-insight-in-azure-monitor"></a>Co to jest szczegółowe informacje w Azure Monitor?
Szczegółowe informacje zapewniają dostosowane środowisko monitorowania dla określonych usług platformy Azure. Korzystają one z tych samych metryk i dzienników co inne funkcje w Azure Monitor ale mogą zbierać dodatkowe dane i zapewniać unikatowe środowisko w Azure Portal. Zobacz [szczegółowe informacje w Azure monitor](insights/insights-overview.md).

Aby wyświetlić szczegółowe informacje w Azure Portal, zobacz sekcję **szczegółowe** informacje w menu **monitor** lub sekcji **monitorowanie** w menu usługi.

### <a name="what-is-a-solution-in-azure-monitor"></a>Co to jest rozwiązanie w Azure Monitor?
Rozwiązania do monitorowania są spakowanymi zestawami logiki do monitorowania określonej aplikacji lub usługi na podstawie Azure Monitor funkcji. Zbierają one dane dzienników w Azure Monitor i zapewniają kwerendy dzienników i widoki do analizy przy użyciu typowego środowiska Azure Portal. Zobacz [monitorowanie rozwiązań w Azure monitor](insights/solutions.md).

Aby wyświetlić rozwiązania w Azure Portal, kliknij przycisk **więcej** **w sekcji szczegółowe dane w menu** **monitor** . Kliknij przycisk **Dodaj** , aby dodać dodatkowe rozwiązania do obszaru roboczego.






## <a name="logs"></a>Dzienniki

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Jaka jest różnica między dziennikami Azure Monitor i Eksplorator danych platformy Azure?
Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Dzienniki Azure Monitor są oparte na usłudze Azure Eksplorator danych i korzystają z tego samego Kusto Query Language (KQL) z niewielkimi różnicami. Zobacz temat [różnice w języku zapytań dzienników Azure monitor](log-query/data-explorer-difference.md).

### <a name="how-do-i-retrieve-log-data"></a>Jak mogę pobrać danych dziennika?
Wszystkie dane są pobierane z obszaru roboczego Log Analytics przy użyciu zapytania dziennika, które zapisano przy użyciu języka zapytań Kusto (KQL). Możesz pisać własne zapytania lub używać rozwiązań i szczegółowych informacji, które obejmują zapytania dzienników dla określonej aplikacji lub usługi. Zobacz [Omówienie zapytań dzienników w Azure monitor](log-query/log-query-overview.md).

### <a name="what-is-a-log-analytics-workspace"></a>Co to jest obszar roboczy Log Analytics?
Wszystkie dane dzienników zbierane przez Azure Monitor są przechowywane w Log Analytics obszarze roboczym. Obszar roboczy jest zasadniczo kontenerem, w którym dane dzienników są zbierane z różnych źródeł. Może istnieć pojedynczy obszar roboczy Log Analytics dla wszystkich danych monitorowania lub może mieć wymagania dotyczące wielu obszarów roboczych. Zobacz [projektowanie wdrożenia dzienników Azure monitor](platform/design-logs-deployment.md).

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>Czy można przenieść istniejący obszar roboczy Log Analytics do innej subskrypcji platformy Azure?
Obszar roboczy można przenieść między grupami zasobów lub subskrypcjami, ale nie do innego regionu. Zobacz [przenoszenie obszaru roboczego log Analytics do innej subskrypcji lub grupy zasobów](platform/move-workspace.md).

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>Dlaczego nie widzę Eksploratora zapytań i nie zapisuj przycisków w Log Analytics?

**Eksplorator zapytań**, przyciski **Zapisz** i **nowe reguły alertu** nie są dostępne, gdy [zakres zapytania](log-query/scope.md) jest ustawiony na określony zasób. Aby utworzyć alerty, Zapisz lub Załaduj zapytanie, Log Analytics musi być objęty zakresem obszaru roboczego. Aby otworzyć Log Analytics w kontekście obszaru roboczego, wybierz pozycję **dzienniki** z menu **Azure monitor** . Wybrano ostatni używanych obszaru roboczego, ale można wybrać inny obszar roboczy. Zobacz [zakres zapytań dzienników i zakres czasu w Azure Monitor Log Analytics](log-query/scope.md)

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>Dlaczego otrzymuję błąd: "Zarejestruj dostawcę zasobów" Microsoft. Insights "dla tej subskrypcji, aby włączyć to zapytanie" podczas otwierania Log Analytics z maszyny wirtualnej? 
Wielu dostawców zasobów jest automatycznie rejestrowanych, ale może być konieczne ręczne zarejestrowanie niektórych dostawców zasobów. Zakres do rejestracji jest zawsze subskrypcji. Aby uzyskać więcej informacji, zobacz [Dostawcy zasobów i ich typy](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

### <a name="why-am-i-am-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>Dlaczego otrzymuję komunikat o błędzie dostępu podczas otwierania Log Analytics z maszyny wirtualnej? 
Aby wyświetlić dzienniki maszyn wirtualnych, musisz mieć uprawnienia do odczytu w obszarze roboczym, w którym są przechowywane dzienniki maszyn wirtualnych. W takich przypadkach administrator musi udzielić użytkownikowi uprawnień na platformie Azure.




## <a name="alerts"></a>Alerty

### <a name="what-is-an-alert-in-azure-monitor"></a>Co to jest alert w Azure Monitor?
Alerty z wyprzedzeniem powiadamiają Cię, gdy w danych monitorowania zostaną znalezione ważne warunki. Umożliwiają identyfikowanie i rozwiązywanie problemów przed zapisaniem ich przez użytkowników systemu. Istnieje wiele rodzajów alertów:

- Metryka — wartość metryki przekracza próg.
- Zapytanie dziennika — wyniki zapytania dziennika odpowiadające zdefiniowanym kryteriom.
- Dziennik aktywności — zdarzenie dziennika aktywności odpowiada zdefiniowanym kryteriom.
- Test sieci Web — wyniki testu dostępności zdefiniowane dla kryteriów.


Zobacz [Omówienie alertów w Microsoft Azure](platform/alerts-overview.md).


### <a name="what-is-an-action-group"></a>Co to jest grupa akcji?
Grupa akcji to zbiór powiadomień i akcji, które mogą być wyzwalane przez alert. Wiele alertów może korzystać z jednej grupy akcji, dzięki czemu można korzystać ze wspólnych zestawów powiadomień i akcji. Zobacz [Tworzenie grup akcji i zarządzanie nimi w Azure Portal](platform/action-groups.md).


### <a name="what-is-an-action-rule"></a>Co to jest reguła akcji?
Reguła akcji umożliwia modyfikowanie zachowania zestawu alertów spełniających określone kryteria. Dzięki temu można wykonać takie wymagania jak wyłączenie akcji alertów w oknie obsługi. Można również zastosować grupę akcji do zestawu alertów, zamiast stosować je bezpośrednio do reguł alertów. Zobacz [reguły akcji](platform/alerts-action-rules.md).


## <a name="agents"></a>Agenci

### <a name="does-azure-monitor-require-an-agent"></a>Czy Azure Monitor wymaga agenta?
Agent jest wymagany tylko do zbierania danych z systemu operacyjnego i obciążeń na maszynach wirtualnych. Maszyny wirtualne mogą znajdować się na platformie Azure, innym środowisku w chmurze lub lokalnie. Zobacz [Omówienie agentów Azure monitor](platform/agents-overview.md).


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Jaka jest różnica między agentami Azure Monitor?
Rozszerzenie diagnostyki platformy Azure dotyczy maszyn wirtualnych platformy Azure i zbiera dane do Azure Monitor metryk, usługi Azure Storage i platformy Azure Event Hubs. Agent Log Analytics jest przeznaczony dla maszyn wirtualnych platformy Azure, innego środowiska chmury lub lokalnego i zbiera dane do dzienników Azure Monitor. Agent zależności wymaga, aby Agent Log Analytics i szczegóły zebranych procesów i zależności. Zobacz [Omówienie agentów Azure monitor](platform/agents-overview.md).


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>Czy mój ruch agenta używa mojego połączenia ExpressRoute?
Ruch do Azure Monitor używa obwodu komunikacji równorzędnej firmy Microsoft. Aby uzyskać opis różnych typów ruchu ExpressRoute, zobacz [dokumentację ExpressRoute](../expressroute/expressroute-faqs.md#supported-services) . 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>Jak upewnić się, że Agent Log Analytics może komunikować się z usługą Azure Monitor?
W panelu sterowania na komputerze agenta wybierz pozycję **zabezpieczenia & ustawienia**, **Microsoft Monitoring Agent** . Na karcie **Azure log Analytics (OMS)** zielona ikona znacznika wyboru potwierdza, że Agent jest w stanie komunikować się z Azure monitor. Żółta ikona ostrzeżenia oznacza, że agent ma problemy. Jednym z typowych przyczyn jest zatrzymanie usługi **Microsoft Monitoring Agent** . Aby ponownie uruchomić usługę, należy użyć Menedżera sterowania usługami.

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>Jak mogę zatrzymać komunikację agenta Log Analytics z Azure Monitor?
W przypadku agentów podłączonych do Log Analytics bezpośrednio Otwórz Panel sterowania i wybierz pozycję **zabezpieczenia & ustawienia**, **Microsoft Monitoring Agent**. Na karcie **Azure log Analytics (OMS)** Usuń wszystkie obszary robocze wymienione na liście. W System Center Operations Manager Usuń komputer z listy Log Analytics komputery zarządzane. Programu Operations Manager zaktualizuje konfigurację agenta nie jest już raport do usługi Log Analytics. 

### <a name="how-much-data-is-sent-per-agent"></a>Jak dużo danych jest wysyłany do jednego agenta?
Ilość danych wysyłanych na agenta zależy od:

* Rozwiązania, które mają włączone
* Liczba dzienniki i liczniki wydajności są zbierane
* Ilość danych w dziennikach

Aby uzyskać szczegółowe informacje [, zobacz Zarządzanie użyciem i kosztami za pomocą dzienników Azure monitor](platform/manage-cost-storage.md) .

Dla komputerów, które można uruchomić agenta danych o komunikacji sieciowej użyj następującego zapytania, aby zobaczyć, jak dużo danych jest wysyłanych:

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>Jaka przepustowość sieci jest używana przez program Microsoft Management Agent (MMA) podczas wysyłania danych do Azure Monitor?
Przepustowość jest funkcja na ilość wysyłanych danych. Dane są kompresowane podczas ich przesyłania przez sieć.


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>Jak mogę otrzymywać powiadomienia, gdy zbieranie danych z agenta Log Analytics zostało zatrzymane?

Wykonaj kroki opisane w sekcji [Tworzenie nowego alertu dziennika](platform/alerts-metric.md) , aby otrzymywać powiadomienia o zatrzymaniu zbierania danych. Użyj następujących ustawień dla reguły alertu:

- **Zdefiniuj warunek alertu**: Określ obszar roboczy log Analytics jako obiekt docelowy zasobu.
- **Kryteria alertu** 
   - **Nazwa sygnału**: *niestandardowe wyszukiwanie w dzienniku*
   - **Zapytanie wyszukiwania**: `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Logika alertu**: **w oparciu o** *liczbę wyników*, **warunek** *większy niż*, **wartość progowa** *0*
   - **Oceniane na podstawie**: **okres (w minutach)** *30*, **częstotliwość (w minutach)** *10*
- **Zdefiniuj szczegóły alertu** 
   - **Nazwa**: *zatrzymano zbieranie danych*
   - **Ważność**: *Ostrzeżenie*

Określ istniejącą lub nową [grupę akcji](platform/action-groups.md) w taki sposób, że gdy alert dziennika spełnia kryteria, otrzymasz powiadomienie, jeśli brakuje pulsu przez ponad 15 minut.


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Jakie są wymagania dotyczące zapory dla Azure Monitor agentów?
Szczegółowe informacje na temat wymagań zapory można znaleźć w temacie [wymagania dotyczące zapory sieciowej](platform/log-analytics-agent.md#network-firewall-requirements).


## <a name="visualizations"></a>Wizualizacje

### <a name="why-cant-i-cant-see-view-designer"></a>Dlaczego nie mogę zobaczyć projektanta widoków?

Projektant widoków jest dostępny tylko dla użytkowników przypisanych z uprawnieniami współautora lub wyższych w obszarze roboczym Log Analytics.


## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>Problemy z konfiguracją
*Mam problem z konfigurowaniem:*

* [Aplikacja platformy .NET](app/asp-net-troubleshoot-no-data.md)
* [Monitorowanie już uruchomionej aplikacji](app/monitor-performance-live-website-now.md#troubleshoot)
* [Diagnostyka platformy Azure](platform/diagnostics-extension-to-application-insights.md)
* [Aplikacje internetowe w języku Java](app/java-troubleshoot.md)

*Nie otrzymuję żadnych danych z mojego serwera*

* [Ustawianie wyjątków zapory](app/ip-addresses.md)
* [Konfigurowanie serwera ASP.NET](app/monitor-performance-live-website-now.md)
* [Konfigurowanie serwera Java](app/java-agent.md)

### <a name="can-i-use-application-insights-with-"></a>Czy można użyć Application Insights z...?

* [Aplikacje sieci Web na serwerze IIS na maszynie wirtualnej platformy Azure lub w zestawie skalowania maszyn wirtualnych platformy Azure](app/azure-vm-vmss-apps.md)
* [Aplikacje sieci Web na serwerze IIS — lokalnie lub na maszynie wirtualnej](app/asp-net.md)
* [Aplikacje sieci Web Java](app/java-get-started.md)
* [Aplikacje Node.js](app/nodejs.md)
* [Aplikacje sieci Web na platformie Azure](app/azure-web-apps.md)
* [Cloud Services na platformie Azure](app/cloudservices.md)
* [Serwery aplikacji działające w programie Docker](app/docker.md)
* [Jednostronicowe aplikacje sieci Web](app/javascript.md)
* [SharePoint](app/sharepoint.md)
* [Aplikacja klasyczna systemu Windows](app/windows-desktop.md)
* [Inne platformy](app/platforms.md)

### <a name="is-it-free"></a>Czy jest to bezpłatne?

Tak, w przypadku eksperymentalnego użycia. W podstawowym planie cenowym aplikacja może bezpłatnie wysyłać pewne opłaty za dane. Bezpłatny limit jest wystarczająco duży, aby pokryć rozwój i opublikować aplikację dla niewielkiej liczby użytkowników. Można ustawić limit, aby zapobiec przetwarzaniu większej ilości danych.

W przypadku większych ilości danych telemetrycznych są naliczone opłaty za GB. Oferujemy pewne porady dotyczące [ograniczania opłat](app/pricing.md).

Plan przedsiębiorstwa wiąże się z opłatą za każdy dzień, w którym każdy węzeł serwera sieci Web wysyła dane telemetryczne. Jest to przydatne, jeśli chcesz używać eksportu ciągłego na dużą skalę.

[Zapoznaj się z planem cenowym](https://azure.microsoft.com/pricing/details/application-insights/).

### <a name="how-much-does-it-cost"></a>Ile kosztuje?

* Otwórz **stronę użycie i szacowane koszty** w zasobie Application Insights. Istnieje wykres przedstawiający ostatnie użycie. Jeśli chcesz, możesz ustawić limit ilości danych.
* Otwórz [blok rozliczeń platformy Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) , aby wyświetlić rachunki dla wszystkich zasobów.

### <a name="q14"></a>Co Application Insights modyfikować w moim projekcie?
Szczegóły są zależne od typu projektu. Dla aplikacji sieci Web:

* Dodaje te pliki do projektu:
  * Plik ApplicationInsights.config
  * ai.js
* Instaluje te pakiety NuGet:
  * *Application INSIGHTS API* — podstawowy interfejs API
  * *Application INSIGHTS API dla aplikacji sieci Web* — służy do wysyłania danych telemetrycznych z serwera
  * *Application INSIGHTS API dla aplikacji JavaScript* — służy do wysyłania danych telemetrycznych z klienta
* Pakiety obejmują następujące zestawy:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Wstawia elementy do:
  * Web.config
  * packages.config
* (Tylko nowe projekty — w przypadku [dodania Application Insights do istniejącego projektu][start]należy to zrobić ręcznie). Wstawia fragmenty kodu klienta i serwera, aby je zainicjować przy użyciu identyfikatora zasobu Application Insights. Na przykład w aplikacji MVC kod jest wstawiany do widoku strony głównej/Shared/\_Layout. cshtml

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Jak mogę uaktualnić ze starszych wersji zestawu SDK?
Zapoznaj się z [informacjami o wersji](app/release-notes.md) zestawu SDK, które są odpowiednie dla danego typu aplikacji.

### <a name="update"></a>Jak mogę zmienić zasób platformy Azure, do którego mój projekt wysyła dane?
W Eksplorator rozwiązań kliknij prawym przyciskiem myszy `ApplicationInsights.config` i wybierz polecenie **aktualizuj Application Insights**. Dane można wysyłać do istniejącego lub nowego zasobu na platformie Azure. Kreator aktualizacji zmienia klucz Instrumentacji w pliku ApplicationInsights. config, który określa, gdzie zestaw SDK serwera wysyła dane. Chyba że usuniesz opcję "Aktualizuj wszystko", zostanie również zmieniony klucz, w którym pojawia się na stronach sieci Web.

### <a name="what-is-status-monitor"></a>Co to jest monitor stanu?

Aplikacja klasyczna, która może być używana na serwerze sieci Web usług IIS w celu ułatwienia konfigurowania Application Insights w usłudze Web Apps. Nie zbiera ona danych telemetrycznych: możesz ją zatrzymać, gdy nie skonfigurujesz aplikacji. 

[Dowiedz się więcej](app/monitor-performance-live-website-now.md#questions).

### <a name="what-telemetry-is-collected-by-application-insights"></a>Jakie dane telemetryczne są zbierane przez Application Insights?

Z aplikacji serwer sieci Web:

* Żądania HTTP
* [Zależności](app/asp-net-dependencies.md). Wywołania do: bazy danych SQL; Wywołania HTTP do usług zewnętrznych; Azure Cosmos DB, tabela, Magazyn obiektów blob i kolejka. 
* [Wyjątki](app/asp-net-exceptions.md) i ślady stosu.
* [Liczniki wydajności](app/performance-counters.md) — jeśli używasz [Monitor stanu](app/monitor-performance-live-website-now.md), [monitorowanie platformy Azure dla App Services](app/azure-web-apps.md), [monitorowanie platformy Azure dla maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych](app/azure-vm-vmss-apps.md)oraz [Application Insights zebrany moduł zapisujący](app/java-collectd.md).
* [Niestandardowe zdarzenia i metryki](app/api-custom-events-metrics.md) , które kodują.
* [Dzienniki śledzenia](app/asp-net-trace-logs.md) w przypadku skonfigurowania odpowiedniego modułu zbierającego.

Ze [stron sieci Web klienta](app/javascript.md):

* [Liczba wyświetleń stron](app/usage-overview.md)
* [Wywołania AJAX](app/asp-net-dependencies.md) Żądania wykonane z uruchomionego skryptu.
* Dane ładowania danych w widoku strony
* Liczba użytkowników i sesji
* [Identyfikatory uwierzytelnionych użytkowników](app/api-custom-events-metrics.md#authenticated-users)

Z innych źródeł, w przypadku ich skonfigurowania:

* [Diagnostyka platformy Azure](platform/diagnostics-extension-to-application-insights.md)
* [Importuj do analizy](platform/data-collector-api.md)
* [Log Analytics](platform/data-collector-api.md)
* [Logstash](platform/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>Czy mogę odfiltrować lub zmodyfikować niektóre dane telemetryczne?

Tak, na serwerze można napisać:

* Procesor telemetrii do filtrowania lub dodawania właściwości do wybranych elementów telemetrii przed ich wysłaniem z aplikacji.
* Inicjator telemetrii, aby dodać właściwości do wszystkich elementów telemetrii.

Dowiedz się więcej na temat [ASP.NET](app/api-filtering-sampling.md) lub [Java](app/java-filter-telemetry.md).

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Jak są obliczane dane miejscowości, kraju/regionu i innych lokalizacji geograficznych?

Wyszukujemy adres IP (IPv4 lub IPv6) klienta sieci Web za pomocą [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/).

* Telemetria przeglądarki: Zbieramy adres IP nadawcy.
* Dane telemetryczne serwera: moduł Application Insights zbiera adres IP klienta. Nie jest zbierane, jeśli `X-Forwarded-For` jest ustawiony.
* Aby dowiedzieć się więcej o sposobie zbierania danych o adresie IP i geolokalizacji w Application Insights zapoznaj się z tym [artykułem](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection).


Można skonfigurować `ClientIpHeaderTelemetryInitializer`, aby uzyskać adres IP z innego nagłówka. W niektórych systemach jest to na przykład przenoszone przez serwer proxy, moduł równoważenia obciążenia lub sieć CDN do `X-Originating-IP`. [Dowiedz się więcej](https://apmtips.com/blog/2016/07/05/client-ip-address/).

Możesz [użyć Power BI](app/export-power-bi.md ) , aby wyświetlić dane telemetryczne żądania na mapie.


### <a name="data"></a>Jak długo dane są przechowywane w portalu? Czy jest zabezpieczony?
Zapoznaj się z [przechowywaniem i prywatnością danych][data].

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>Co się stanie z danymi telemetrycznymi usługi Application Insight, gdy serwer lub urządzenie utraci połączenie z platformą Azure?

Wszystkie nasze zestawy SDK, w tym zestaw SDK sieci Web, obejmują "niezawodny transport" lub "niezawodny transport". Gdy serwer lub urządzenie utraci połączenie z platformą Azure, dane telemetryczne są [przechowywane lokalnie w systemie plików](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#does-the-sdk-create-temporary-local-storage) (zestawy SDK serwera) lub w magazynie sesji HTML5 (Web SDK). Zestaw SDK będzie okresowo ponawiać próbę wysłania tej telemetrii do momentu, gdy nasza usługa pozyskiwania uzna, że "nieodświeżone" (48 godzin w przypadku dzienników, 30 minut dla metryk). Nieodświeżona Telemetria zostanie porzucona. W niektórych przypadkach, na przykład gdy magazyn lokalny jest pełny, ponowienie próby nie nastąpi.


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>Czy dane osobowe mogą być wysyłane w ramach telemetrii?

Jest to możliwe, jeśli kod wysyła takie dane. Może również wystąpić, jeśli zmienne w śladach stosu obejmują dane osobowe. Zespół programistyczny powinien przeprowadzić ocenę ryzyka, aby upewnić się, że dane osobowe są prawidłowo obsługiwane. [Dowiedz się więcej o przechowywaniu i ochronie danych](app/data-retention-privacy.md).

**Wszystkie** oktety adresu internetowego klienta zawsze mają ustawioną wartość 0 po wyszukiwaniu atrybutów lokalizacji geograficznej.

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>Mój klucz Instrumentacji jest widoczny w źródle strony sieci Web. 

* Jest to typowa Metoda monitorowania rozwiązań.
* Nie można jej użyć do kradzieży danych.
* Może służyć do pochylania alertów dotyczących danych lub wyzwalaczy.
* Nie wiemy, że każdy klient miał takie problemy.

Możesz:

* Użyj dwóch oddzielnych kluczy Instrumentacji (oddzielnych zasobów Application Insights) dla danych klienta i serwera. Lub
* Napisz serwer proxy, który działa na serwerze i czy klient sieci Web wyśle dane za pomocą tego serwera proxy.

### <a name="post"></a>Jak mogę zobaczyć dane POST w wyszukiwaniu diagnostycznym?
Nie rejestrujemy danych do OPUBLIKOWANia automatycznie, ale można użyć wywołania TrackTrace: Umieść dane w parametrze komunikatu. Ten limit rozmiaru jest dłuższy niż limity właściwości ciągu, ale nie można go filtrować.

### <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Czy należy używać jednego lub wielu zasobów Application Insights?

Użyj pojedynczego zasobu dla wszystkich składników lub ról w jednym systemie biznesowym. Używaj oddzielnych zasobów do tworzenia, testowania i wydawania wersji oraz dla niezależnych aplikacji.

* [Zapoznaj się z dyskusjami tutaj](app/separate-resources.md)
* [Przykład — usługa w chmurze z rolami procesów roboczych i sieci Web](app/cloudservices.md)

### <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Jak mogę dynamicznie zmienić klucz Instrumentacji?

* [Dyskusja w tym miejscu](app/separate-resources.md)
* [Przykład — usługa w chmurze z rolami procesów roboczych i sieci Web](app/cloudservices.md)

### <a name="what-are-the-user-and-session-counts"></a>Jakie są liczby użytkowników i sesji?

* Zestaw SDK języka JavaScript ustawia plik cookie użytkownika na kliencie sieci Web, aby zidentyfikować zwracających użytkowników i plik cookie sesji w celu pogrupowania działań.
* Jeśli nie istnieje skrypt po stronie klienta, można [ustawić pliki cookie na serwerze](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Jeśli jeden z rzeczywistych użytkowników korzysta z witryny w różnych przeglądarkach lub korzysta z funkcji przeglądania w trybie prywatnym/incognito lub różnych maszyn, zostaną one zliczone więcej niż jeden raz.
* Aby zidentyfikować zalogowanego użytkownika na maszynach i w przeglądarkach, należy dodać wywołanie do [setAuthenticatedUserContext ()](app/api-custom-events-metrics.md#authenticated-users).

### <a name="q17"></a>Czy mam wszystko, co jest dostępne w Application Insights?
| Co powinno zostać wyświetlone | Jak uzyskać | Dlaczego chcesz |
| --- | --- | --- |
| Wykresy dostępności |[Testy sieci Web](app/monitor-web-app-availability.md) |Znajomość aplikacji sieci Web |
| Wydajność aplikacji serwera: czasy odpowiedzi,... |[Dodaj Application Insights do projektu](app/asp-net.md) lub [Zainstaluj Monitor stanu AI na serwerze](app/monitor-performance-live-website-now.md) (lub napisz własny kod w celu [śledzenia zależności](app/api-custom-events-metrics.md#trackdependency)) |Wykrywanie problemów wydajności |
| Telemetria zależności |[Zainstaluj monitor stanu AI na serwerze](app/monitor-performance-live-website-now.md) |Diagnozowanie problemów z bazami danych lub innymi składnikami zewnętrznymi |
| Pobierz ślady stosu z wyjątków |[Wstaw wywołania metody trackexception w kodzie](app/asp-net-exceptions.md) (ale niektóre są raportowane automatycznie) |Wykrywanie i diagnozowanie wyjątków |
| Wyszukaj ślady dziennika |[Dodawanie karty rejestrowania](app/asp-net-trace-logs.md) |Diagnozowanie wyjątków, problemy z wydajnością |
| Podstawowe informacje dotyczące użycia klientów: wyświetlenie stron, sesje,... |[Inicjator JavaScript na stronach sieci Web](app/javascript.md) |Analiza użycia |
| Metryki niestandardowe klienta |[Śledzenie wywołań na stronach sieci Web](app/api-custom-events-metrics.md) |Ulepszanie środowiska użytkownika |
| Metryki niestandardowe serwera |[Śledzenie wywołań na serwerze](app/api-custom-events-metrics.md) |Analiza biznesowa |

### <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Dlaczego liczniki na wykresach wyszukiwania i metryk są nierówne?

[Próbkowanie](app/sampling.md) zmniejsza liczbę elementów telemetrycznych (żądań, zdarzeń niestandardowych itd.), które są faktycznie wysyłane z aplikacji do portalu. W obszarze wyszukiwanie zostanie wyświetlona liczba elementów, które faktycznie zostały odebrane. Na wykresach metryk, które wyświetlają liczbę zdarzeń, zobaczysz liczbę oryginalnych zdarzeń, które wystąpiły. 

Każdy przesłany element ma właściwość `itemCount`, która pokazuje, ile oryginalnych zdarzeń reprezentuje element. Aby obserwować próbkowanie w operacji, można uruchomić to zapytanie w analizie:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


### <a name="automation"></a>Automatyzacja

#### <a name="configuring-application-insights"></a>Konfigurowanie Application Insights

[Skrypty programu PowerShell można napisać](app/powershell.md) przy użyciu usługi Azure Monitor zasobów:

* Tworzenie i aktualizowanie zasobów Application Insights.
* Ustaw plan cenowy.
* Pobierz klucz Instrumentacji.
* Dodaj alert dotyczący metryki.
* Dodaj Test dostępności.

Nie można skonfigurować raportu Eksploratora metryk ani skonfigurować eksportu ciągłego.

#### <a name="querying-the-telemetry"></a>Wykonywanie zapytania dotyczącego telemetrii

Użyj [interfejsu API REST](https://dev.applicationinsights.io/) , aby uruchomić zapytania [analityczne](app/analytics.md) .

### <a name="how-can-i-set-an-alert-on-an-event"></a>Jak ustawić alert dla zdarzenia?

Alerty platformy Azure są tylko na metrykach. Utwórz metrykę niestandardową, która przecina próg wartości przy każdym wystąpieniu zdarzenia. Następnie ustaw alert dla metryki. Gdy Metryka przekroczy próg w dowolnym kierunku, otrzymasz powiadomienie. nie otrzymasz powiadomienia do momentu pierwszego przekroczenia, niezależnie od tego, czy wartość początkowa jest wysoka, czy niska; zawsze trwa opóźnienie wynoszący kilka minut.

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Czy są naliczane opłaty za transfer danych między aplikacją internetową platformy Azure i Application Insights?

* Jeśli Twoja aplikacja internetowa platformy Azure jest hostowana w centrum danych, w którym znajduje się punkt końcowy kolekcji Application Insights, nie jest naliczana opłata. 
* Jeśli w centrum danych hosta nie ma punktu końcowego kolekcji, dane telemetryczne Twojej aplikacji będą powodować naliczanie [opłat za wychodzące platformy Azure](https://azure.microsoft.com/pricing/details/bandwidth/).

Nie zależy to od lokalizacji zasobów Application Insights. Zależy to tylko od dystrybucji naszych punktów końcowych.

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Czy mogę wysłać dane telemetryczne do portalu Application Insights?

Zalecamy używanie naszych zestawów SDK i korzystanie z [interfejsu API zestawu SDK](app/api-custom-events-metrics.md). Istnieje wiele wariantów zestawu SDK dla różnych [platform](app/platforms.md). Te zestawy SDK obsługują buforowanie, kompresję, ograniczanie, ponawianie prób i tak dalej. Jednak [schemat](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) pozyskiwania i [Protokół punktu końcowego](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) są publiczne.

### <a name="can-i-monitor-an-intranet-web-server"></a>Czy mogę monitorować intranetowy serwer sieci Web?

Tak, ale musisz zezwolić na ruch do naszych usług przez wyjątki zapory lub przekierowania serwera proxy.
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


Zapoznaj się z pełną listą usług i adresów IP w [tym miejscu](app/ip-addresses.md).

#### <a name="firewall-exception"></a>Wyjątek zapory

Zezwól serwerowi sieci Web na wysyłanie danych telemetrycznych do naszych punktów końcowych. 

#### <a name="gateway-redirect"></a>Przekierowanie bramy

Kierowanie ruchu z serwera do bramy w intranecie przez zastępowanie punktów końcowych w konfiguracji. Jeśli te właściwości "punkt końcowy" nie są obecne w konfiguracji, te klasy będą używały domyślnych wartości przedstawionych poniżej w przykładowym pliku ApplicationInsights. config. 

Twoja Brama powinna kierować ruch do adresu podstawowego tego punktu końcowego. W konfiguracji Zastąp wartości domyślne wartością `http://<your.gateway.address>/<relative path>`.


##### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Przykład ApplicationInsights. config z domyślnymi punktami końcowymi:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

> [!NOTE]
> ApplicationIdProvider jest dostępna od 2.6.0.



#### <a name="proxy-passthrough"></a>Przekazywanie serwera proxy

Przekazywanie serwerów proxy można osiągnąć przez skonfigurowanie serwera proxy poziomu komputera lub aplikacji.
Aby uzyskać więcej informacji, zobacz artykuł dotnet w witrynie [defaultProxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
 
 Przykład Web. config:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

### <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Czy można uruchamiać testy sieci Web dostępności na serwerze intranetowym?

Nasze [testy sieci Web](app/monitor-web-app-availability.md) działają w punktach obecności rozmieszczonych na całym świecie. Istnieją dwa rozwiązania:

* Drzwi zapory — Zezwalaj na żądania kierowane do serwera od [długiej i możliwej do zmiany listy agentów testów sieci Web](app/ip-addresses.md).
* Napisz własny kod, aby wysyłać okresowe żądania do serwera z wnętrza intranetu. W tym celu można uruchomić testy sieci Web programu Visual Studio. Tester może wysłać wyniki do Application Insights przy użyciu interfejsu API TrackAvailability ().

### <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Jak długo trwa zbieranie danych telemetrycznych?

Większość Application Insights danych ma opóźnienie poniżej 5 minut. Niektóre dane mogą trwać dłużej; zwykle większe pliki dziennika. Aby uzyskać więcej informacji, zobacz [Application INSIGHTS SLA](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).



<!--Link references-->

[data]: app/data-retention-privacy.md
[platforms]: app/platforms.md
[start]: app/app-insights-overview.md
[windows]: app/app-insights-windows-get-started.md



## <a name="azure-monitor-for-containers"></a>Usługa Azure Monitor dla kontenerów

Ta firma Microsoft — często zadawane pytania dotyczące Azure Monitor kontenerów. Jeśli masz dodatkowe pytania dotyczące rozwiązania, przejdź do [forum dyskusyjnego](https://feedback.azure.com/forums/34192--general-feedback) i Opublikuj swoje pytania. Gdy zadawane pytanie dodajemy go do tego artykułu tak, aby możliwe było szybkie i łatwe.

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>Nie widzę wartości właściwości obraz i nazwa wypełniane podczas wykonywania zapytania względem tabeli ContainerLog.

W przypadku agenta w wersji ciprod12042019 i nowszych domyślnie te dwie właściwości nie są wypełniane dla każdego wiersza dziennika, aby zminimalizować koszty związane z zbieranymi danymi dzienników. Istnieją dwie opcje zapytania do tabeli zawierającej te właściwości z ich wartościami:

#### <a name="option-1"></a>Opcja 1 

Dołącz inne tabele, aby uwzględnić te wartości właściwości w wynikach.

Zmodyfikuj zapytania, aby uwzględnić właściwości obrazu i ImageTag z tabeli ```ContainerInventory``` przez dołączenie do właściwości ContainerID. Możesz dołączyć Właściwość Name (jak wcześniej pojawiła się w tabeli ```ContainerLog```) z pola ContaineName tabeli KubepodInventory, dołączając Właściwość ContainerID. Jest to zalecana opcja.

Poniższy przykład to przykładowe zapytanie szczegółowe objaśniające, jak uzyskać te wartości pól za pomocą sprzężeń.

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

#### <a name="option-2"></a>Opcja 2

Ponownie włącz kolekcję dla tych właściwości dla każdego wiersza dziennika kontenera.

Jeśli pierwsza opcja jest niewygodna ze względu na zmiany zapytania, można ponownie włączyć zbieranie tych pól, włączając ustawienie ```log_collection_settings.enrich_container_logs``` na mapie konfiguracji agenta zgodnie z opisem w [ustawieniach konfiguracji zbierania danych](insights/container-insights-agent-config.md).

> [!NOTE]
> Druga opcja nie jest zalecana w przypadku dużych klastrów, które mają więcej niż 50 węzłów, ponieważ generuje ona wywołania serwera interfejsu API z każdego węzła w klastrze w celu przeprowadzenia tego wzbogacania. Ta opcja umożliwia również zwiększenie rozmiaru danych dla każdej zebranej linii dziennika.

### <a name="can-i-view-metrics-collected-in-grafana"></a>Czy można wyświetlić metryki zebrane w Grafana?

Azure Monitor dla kontenerów obsługuje wyświetlanie metryk przechowywanych w obszarze roboczym Log Analytics na pulpitach nawigacyjnych Grafana. Podano szablon, który można pobrać z [repozytorium pulpitu nawigacyjnego](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) Grafana, aby rozpocząć pracę i zapoznać się z pomocą techniczną, aby dowiedzieć się, jak wykonywać zapytania dotyczące dodatkowych danych z monitorowanych klastrów w celu wizualizowania niestandardowych pulpitów nawigacyjnych Grafana. 

### <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Czy mogę monitorować klaster AKS-Engine z Azure Monitor dla kontenerów?

Azure Monitor dla kontenerów obsługuje monitorowanie obciążeń kontenerów wdrożonych na platformie AKS (wcześniej znanych jako klastry ACS) hostowanych w systemie Azure. Aby uzyskać szczegółowe informacje i zapoznać się z omówieniem kroków wymaganych do włączenia monitorowania dla tego scenariusza, zobacz [używanie Azure monitor dla kontenerów dla aparatu AKS](https://github.com/microsoft/OMS-docker/tree/aks-engine).

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Dlaczego nie widzę danych w obszarze roboczym Log Analytics?

Jeśli nie widzisz żadnych danych w obszarze roboczym Log Analytics w określonym czasie codziennie, być może osiągnięto domyślny limit 500 MB lub dzienne zakończenie określony w celu kontrolowania ilości danych do zebrania dziennie. Gdy limit zostanie osiągnięty przez dzień, zbieranie danych zostanie zatrzymane i wznowione tylko w następnym dniu. Aby sprawdzić użycie danych i zaktualizować do innej warstwy cenowej na podstawie przewidywanych wzorców użycia, zobacz temat [użycie i koszt danych dziennika](platform/manage-cost-storage.md). 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Jakie Stany kontenerów określono w tabeli ContainerInventory?

Tabela ContainerInventory zawiera informacje o zatrzymanych i uruchomionych kontenerach. Tabela jest wypełniana przez przepływ pracy wewnątrz agenta, który wysyła zapytanie do platformy Docker dla wszystkich kontenerów (uruchomionych i zatrzymanych) oraz przekazuje te dane do obszaru roboczego Log Analytics.
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Jak mogę rozwiązać *brakującego błędu rejestracji subskrypcji* ?

Jeśli wystąpi błąd **braku rejestracji subskrypcji dla Microsoft. OperationsManagement**, możesz rozwiązać ten problem, rejestrując dostawcę zasobów **Microsoft. OperationsManagement** w subskrypcji, w której zdefiniowano obszar roboczy. Dokumentację dotyczącą sposobu wykonywania tej czynności można znaleźć [tutaj](../azure-resource-manager/templates/error-register-resource-provider.md).

### <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Czy obsługiwane są klastry AKS z włączoną funkcją RBAC?

Rozwiązanie do monitorowania kontenerów nie obsługuje RBAC, ale jest obsługiwane w przypadku kontenerów Azure Monitor. Na stronie Szczegóły rozwiązania mogą nie być wyświetlane odpowiednie informacje w blokach, które pokazują dane dla tych klastrów.

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Jak mogę włączyć zbieranie dzienników dla kontenerów w przestrzeni nazw polecenia-system za pomocą Helm?

Kolekcja dzienników z kontenerów w przestrzeni nazw polecenia-system jest domyślnie wyłączona. Zbieranie dzienników można włączyć przez ustawienie zmiennej środowiskowej w omsagent. Aby uzyskać więcej informacji, zobacz stronę [Azure monitor for Containers w](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) serwisie GitHub. 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Jak mogę zaktualizować omsagent do najnowszej wydanej wersji?

Aby dowiedzieć się, jak uaktualnić agenta, zobacz [Zarządzanie agentem](insights/container-insights-manage-agent.md).

### <a name="how-do-i-enable-multi-line-logging"></a>Jak mogę włączyć rejestrowanie wielowierszowe?

Obecnie Azure Monitor dla kontenerów nie obsługuje rejestrowania wielowierszowego, ale są dostępne obejścia. Można skonfigurować wszystkie usługi do zapisu w formacie JSON, a następnie Docker/Moby zapisze je jako jeden wiersz.

Na przykład możesz otoczyć dziennik jako obiekt JSON, jak pokazano w poniższym przykładzie dla przykładowej aplikacji node. js:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Te dane będą wyglądać podobnie do poniższego przykładu w Azure Monitor dla dzienników podczas wykonywania zapytania dotyczącego:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Aby zapoznać się z szczegółowym opisem problemu, zapoznaj się z następującym [linkiem](https://github.com/moby/moby/issues/22920)w witrynie GitHub.

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Jak mogę rozwiązać problemy z usługą Azure AD po włączeniu dzienników na żywo? 

Może zostać wyświetlony następujący błąd: **adres URL odpowiedzi określony w żądaniu nie pasuje do adresów URL odpowiedzi skonfigurowanych dla aplikacji: "< identyfikator aplikacji\>"** . Rozwiązanie, które ma zostać rozwiązane, można znaleźć w artykule [jak wyświetlać dane kontenerów w czasie rzeczywistym za pomocą Azure monitor dla kontenerów](insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication). 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Dlaczego nie mogę uaktualnić klastra po dołączeniu?

Jeśli po włączeniu Azure Monitor dla kontenerów dla klastra AKS zostanie usunięty obszar roboczy Log Analytics, do którego klaster wysłał swoje dane, podczas próby uaktualnienia klastra zakończy się niepowodzeniem. Aby obejść ten krok, należy wyłączyć monitorowanie, a następnie włączyć go ponownie, odwołując się do innego prawidłowego obszaru roboczego w ramach subskrypcji. Podczas próby ponownego wykonania uaktualnienia klastra należy przetworzyć i zakończyć pracę.  

### <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Które porty i domeny muszę otworzyć/dozwolonych dla agenta?

Zapoznaj się z [wymaganiami dotyczącymi zapory sieciowej](insights/container-insights-onboard.md#network-firewall-requirements) dotyczącymi informacji o konfiguracji serwera proxy i zapory wymaganych przez agenta kontenerów z platformą Azure oraz z PLATFORMą Microsoft Azure dla instytucji rządowych i Azure Chiny.

## <a name="azure-monitor-for-vms-preview"></a>Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)
Ta firma Microsoft — często zadawane pytania dotyczące Azure Monitor dla maszyn wirtualnych. Jeśli masz dodatkowe pytania dotyczące rozwiązania, przejdź do [forum dyskusyjnego](https://feedback.azure.com/forums/34192--general-feedback) i Opublikuj swoje pytania. Gdy zadawane pytanie dodajemy go do tego artykułu tak, aby możliwe było szybkie i łatwe.

### <a name="can-i-onboard-to-an-existing-workspace"></a>Czy mogę dołączyć do istniejącego obszaru roboczego?
Jeśli Twoje maszyny wirtualne są już połączone z obszarem roboczym Log Analytics, można nadal korzystać z tego obszaru roboczego podczas dołączania do Azure Monitor dla maszyn wirtualnych, pod warunkiem, że znajduje się on w jednym z obsługiwanych regionów wymienionych w [tym miejscu](insights/vminsights-enable-overview.md#prerequisites).

Podczas dołączania konfigurujemy liczniki wydajności dla obszaru roboczego, które spowodują, że wszystkie maszyny wirtualne będą zgłaszać dane do obszaru roboczego, aby rozpocząć zbieranie tych informacji do wyświetlania i analizy w Azure Monitor dla maszyn wirtualnych.  W efekcie zobaczysz dane wydajności ze wszystkich maszyn wirtualnych połączonych z wybranym obszarem roboczym.  Funkcje kondycji i mapy są włączone tylko dla maszyn wirtualnych, które zostały określone do dołączenia.

Aby uzyskać więcej informacji o włączonych licznikach wydajności, zapoznaj się z artykułem [Omówienie włączania](insights/vminsights-enable-overview.md#performance-counters-enabled) .

### <a name="can-i-onboard-to-a-new-workspace"></a>Czy mogę dołączyć do nowego obszaru roboczego? 
Jeśli maszyny wirtualne nie są obecnie połączone z istniejącym obszarem roboczym Log Analytics, musisz utworzyć nowy obszar roboczy do przechowywania danych. Tworzenie nowego domyślnego obszaru roboczego jest wykonywane automatycznie, jeśli skonfigurowano jedną maszynę wirtualną platformy Azure dla Azure Monitor dla maszyn wirtualnych za pomocą Azure Portal.

Jeśli zdecydujesz się na użycie metody opartej na skrypcie, te kroki zostaną omówione w artykule [włączanie Azure monitor dla maszyn wirtualnych (wersja zapoznawcza) przy użyciu szablonu Azure PowerShell lub Menedżer zasobów](insights/vminsights-enable-at-scale-powershell.md) . 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Co zrobić, jeśli moja maszyna wirtualna jest już raportowana w istniejącym obszarze roboczym?
Jeśli dane są już zbierane z maszyn wirtualnych, być może zostały już skonfigurowane do raportowania danych do istniejącego obszaru roboczego Log Analytics.  O ile ten obszar roboczy znajduje się w jednym z obsługiwanych regionów, możesz włączyć Azure Monitor dla maszyn wirtualnych do tego istniejącego obszaru roboczego.  Jeśli obszar roboczy, którego już używasz, nie znajduje się w jednym z obsługiwanych regionów, nie będziesz w stanie teraz dołączyć do Azure Monitor dla maszyn wirtualnych.  Pracujemy nad do obsługi dodatkowych regionów.

>[!NOTE]
>Konfigurujemy liczniki wydajności dla obszaru roboczego, które mają wpływ na wszystkie maszyny wirtualne, które są zgłaszane do obszaru roboczego, niezależnie od tego, czy wybrano, aby dołączyć je do Azure Monitor dla maszyn wirtualnych. Aby uzyskać więcej informacji na temat konfiguracji liczników wydajności dla obszaru roboczego, zapoznaj się z naszą [dokumentacją](platform/data-sources-performance-counters.md). Informacje o licznikach skonfigurowanych dla Azure Monitor dla maszyn wirtualnych można znaleźć w artykule dotyczącym [włączania Azure monitor dla maszyn wirtualnych](insights/vminsights-enable-overview.md#performance-counters-enabled) .  

### <a name="why-did-my-vm-fail-to-onboard"></a>Dlaczego moja maszyna wirtualna nie mogła dołączyć?
Podczas dołączania maszyny wirtualnej platformy Azure z Azure Portal należy wykonać następujące czynności:

* Domyślnego obszaru roboczego usługi Log Analytics zostanie utworzony, jeśli wybrano opcję.
* Liczniki wydajności zostały skonfigurowane dla wybranego obszaru roboczego. Jeśli ten krok zakończy się niepowodzeniem, Zauważ, że niektóre wykresy i tabele wydajności nie wyświetlają danych dla dodanej maszyny wirtualnej. Aby rozwiązać ten problem, można uruchomić skrypt programu PowerShell opisany [tutaj](insights/vminsights-enable-at-scale-powershell.md#enable-performance-counters).
* Agent Log Analytics jest instalowany na maszynach wirtualnych platformy Azure przy użyciu rozszerzenia maszyny wirtualnej, o ile jest to wymagane.  
* Agent Azure Monitor dla maszyn wirtualnych zależności mapy jest instalowany na maszynach wirtualnych platformy Azure przy użyciu rozszerzenia, o ile jest to wymagane.  
* W razie potrzeby konfigurowane są składniki Azure Monitor obsługujące kondycję, a maszyna wirtualna jest skonfigurowana do raportowania danych o kondycji.

Podczas procesu dołączania sprawdzimy stan każdego z powyższych, aby zwrócić do Ciebie status powiadomienia w portalu. Konfiguracja obszaru roboczego i podczas instalacji agenta zwykle trwa 5 – 10 minut. Wyświetlanie danych monitorowania i kondycji w portalu zajmuje więcej niż 5 – 10 minut.  

W przypadku zainicjowania dołączania i wyświetlania komunikatów wskazujących, że maszyna wirtualna musi zostać dołączona, możesz ukończyć proces przez maksymalnie 30 minut. 

### <a name="i-only-enabled-azure-monitor-for-vms-why-do-i-see-all-my-vms-monitored-by-the-health-feature"></a>Tylko włączone Azure Monitor dla maszyn wirtualnych, Dlaczego widzę wszystkie moje maszyny wirtualne monitorowane przez funkcję kondycji?
Funkcja kondycji jest włączona dla wszystkich maszyn wirtualnych, które są połączone z obszarem roboczym Log Analytics, nawet gdy akcja zostanie zainicjowana dla jednej maszyny wirtualnej.

### <a name="can-i-modify-the-schedule-for-when-health-criteria-evaluates-a-condition"></a>Czy mogę zmodyfikować harmonogram, dla którego kryteria kondycji szacują warunek?
Nie, nie można zmodyfikować okresu i częstotliwości kryteriów kondycji w tej wersji. 

### <a name="can-i-disable-health-criteria-for-a-condition-i-dont-need-to-monitor"></a>Czy można wyłączyć kryteria kondycji dla warunku, który nie musi być monitorowany?
W tej wersji nie można wyłączyć kryteriów kondycji.

### <a name="are-the-health-alert-severities-configurable"></a>Czy skonfigurowano alerty kondycji?  
Nie można zmodyfikować ważności alertu kondycji. można go włączyć lub wyłączyć. Ponadto niektóre serwery alertów są aktualizowane na podstawie stanu kryteriów kondycji. 

### <a name="if-i-reconfigure-the-settings-of-a-particular-health-criteria-can-it-be-scoped-to-a-specific-instance"></a>Jeśli ponownie skonfigurujemy ustawienia określonych kryteriów kondycji, można je ograniczyć do określonego wystąpienia?  
W przypadku zmodyfikowania dowolnego ustawienia wystąpienia kryterium kondycji są modyfikowane wszystkie wystąpienia kryteriów kondycji tego samego typu na maszynie wirtualnej platformy Azure. Na przykład w przypadku modyfikowania próg wystąpienia kryterium kondycji wolnego miejsca na dysku, które odnosi się do dysku logicznego C: tego progu ma zastosowanie do wszystkich innych dysków logicznych, które są odnajdywane i monitorowane pod kątem tej samej maszyny Wirtualnej.

### <a name="does-the-health-feature-monitor-logical-processors-and-cores"></a>Czy funkcja kondycji monitoruje procesory logiczne i rdzenie?
Nie, w przypadku systemu Windows nie są uwzględniane kryteria kondycji poszczególnych procesorów i logicznych poziomów procesora, a domyślnie tylko całkowite użycie procesora CPU jest monitorowane w celu efektywnego oszacowania użycia procesora na podstawie łącznej liczby procesorów logicznych dostępnych dla maszyny wirtualnej platformy Azure. 

### <a name="are-all-health-criteria-thresholds-configurable"></a>Czy wszystkie progi kryteriów kondycji są konfigurowalne?  
Progi dla kryteriów kondycji przeznaczonych dla maszyny wirtualnej z systemem Windows nie można modyfikować, ponieważ ich Stany kondycji są ustawione na *Uruchamianie* lub *dostępne*. Po zbadaniu kondycji w [interfejsie API monitora obciążenia](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)jest wyświetlana wartość *operatorporównania* **LessThan** lub **GreaterThan** z wartością *progową* **4** dla usługi lub jednostki, jeśli:
   - Kondycja usługi klienta DNS — usługa nie jest uruchomiona. 
   - Kondycja usługi klienta DHCP — usługa nie jest uruchomiona. 
   - Kondycja usługi RPC — usługa nie jest uruchomiona. 
   - Kondycja usługi zapory Windows — usługa nie jest uruchomiona.
   - Kondycja usługi dziennika zdarzeń Windows — usługa nie jest uruchomiona. 
   - Kondycja usługi Serwer — usługa nie jest uruchomiona. 
   - Kondycja usługi zdalnego zarządzania Windows — usługi nie jest uruchomiony. 
   - Błąd systemu plików lub uszkodzeniem — dysku logicznego jest niedostępny.

Progi dla następujących kryteriów kondycji systemu Linux nie można modyfikować, ponieważ ich stan kondycji jest już ustawiony na *wartość true*. Stan kondycji przedstawia *operatorporównania* z wartością **LessThan** i wartością *progową* **1** w przypadku zapytania z interfejsu API monitorowania obciążenia dla jednostki, w zależności od jego kontekstu:
   - Stan dysku logicznego — dysk logiczny nie jest online / dostępne
   - Stan dysku — dysku nie jest online / dostępne
   - Stan karty sieciowej — karty sieciowej jest wyłączona.

### <a name="how-do-i-modify-alerts-that-are-included-with-the-health-feature"></a>Jak mogę modyfikować alerty dołączone do funkcji kondycji?
Reguły alertów, które są zdefiniowane dla każdego kryterium kondycji nie są wyświetlane w witrynie Azure portal. Regułę alertu kondycji można włączyć lub wyłączyć tylko w [interfejsie API monitora obciążenia](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Ponadto nie można przypisać [Azure monitor grupy akcji](platform/action-groups.md) dla alertów dotyczących kondycji w Azure Portal. Za pomocą interfejsu API ustawienia powiadomienia można skonfigurować grupę akcji, która ma być wyzwalana za każdym razem, gdy zostanie wyzwolony alert kondycji. Obecnie można przypisać grupy akcji do maszyny wirtualnej, aby wszystkie *alerty kondycji* zostały wyzwolone dla maszyny wirtualnej wyzwalają te same grupy akcji. W przeciwieństwie do tradycyjnych alertów platformy Azure nie obowiązuje koncepcja grupy osobną akcję dla każdej reguły alertu kondycji. Ponadto podczas są wyzwalane przez alerty dotyczące kondycji są obsługiwane tylko grupy akcji, które są skonfigurowane do dostarczania poczty e-mail lub powiadomienia SMS. 

### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Nie widzę żadnych danych w przypadku wykresów wydajności dla mojej maszyny Wirtualnej
Nasze wykresy wydajności zostały zaktualizowane pod kątem korzystania z danych przechowywanych w tabeli *InsightsMetrics* .  Aby wyświetlić dane na tych wykresach, należy przeprowadzić uaktualnienie, aby użyć nowego rozwiązania usługi VM Insights.  Więcej informacji można znaleźć w naszej [sekcji często zadawanych pytań](insights/vminsights-ga-release-faq.md) .

Jeśli nie widzisz danych dotyczących wydajności w tabeli dysku lub na niektórych wykresach wydajności, liczniki wydajności mogą nie być skonfigurowane w obszarze roboczym. Aby rozwiązać ten problem, uruchom następujący [skrypt programu PowerShell](insights/vminsights-enable-at-scale-powershell.md#enable-with-powershell).


### <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Jak funkcja mapowania Azure Monitor dla maszyn wirtualnych różni się od Service Map?
Funkcja mapy Azure Monitor dla maszyn wirtualnych jest oparta na Service Map, ale ma następujące różnice:

* Dostęp do widoku mapy można uzyskać z bloku VM i z Azure Monitor dla maszyn wirtualnych w obszarze Azure Monitor.
* Połączenia na mapie są teraz klikane i wyświetlają widok danych metryk połączenia w panelu bocznym dla wybranego połączenia.
* Istnieje nowy interfejs API, który jest używany do tworzenia map, aby lepiej obsługiwać bardziej złożone mapy.
* Monitorowane maszyny wirtualne są teraz uwzględnione w węźle Grupa klientów, a wykres pierścieniowy przedstawia proporcje monitorowanych i niemonitorowanych maszyn wirtualnych w grupie.  Można go również użyć do filtrowania listy maszyn, gdy grupa jest rozwinięta.
* Monitorowane maszyny wirtualne znajdują się teraz w węzłach grupy portów serwera, a wykres pierścieniowy przedstawia część monitorowanych i niemonitorowanych maszyn w grupie.  Można go również użyć do filtrowania listy maszyn, gdy grupa jest rozwinięta.
* Styl mapy został zaktualizowany tak, aby był bardziej spójny z mapą aplikacji z usługi Application Insights.
* Panele boczne zostały zaktualizowane i nie mają pełnego zestawu integracji, które były obsługiwane w Service Map-Update Management, Change Tracking, zabezpieczeniach i usług. 
* Opcja wyboru grup i maszyn do mapowania została zaktualizowana i obecnie obsługuje subskrypcje, grupy zasobów, zestawy skalowania maszyn wirtualnych platformy Azure i usługi w chmurze.
* W funkcji mapy Azure Monitor dla maszyn wirtualnych nie można tworzyć nowych grup maszyn Service Map.  

### <a name="why-do-my-performance-charts-show-dotted-lines"></a>Dlaczego mój wykresy wydajności są wyświetlane linie kropkowane?
Może się to zdarzyć z kilku powodów.  W przypadkach, gdy istnieje przerwy w zbieraniu danych, przedstawiamy linie jako kropkowane.  Jeśli zmodyfikowano częstotliwość próbkowania danych dla włączonych liczników wydajności (ustawienie domyślne to zbiera dane co 60 sekund), można zobaczyć kropkowane linie na wykresie, jeśli wybierzesz wąski zakres czasu dla wykresu, a częstotliwość próbkowania jest mniejsza niż rozmiar zasobnika używany na wykresie (na przykład częstotliwość próbkowania jest co 10 minut, a każdy zasobnik na wykresie to 5 minut).  Wybranie szerszego zakresu czasu do wyświetlenia powinno spowodować, że linie wykresu będą widoczne jako linie kryjące zamiast kropek w tym przypadku.

### <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Grupy obsługujące z usługą Azure Monitor dla maszyn wirtualnych?
Tak, po zainstalowaniu agenta zależności Zbieramy informacje z maszyn wirtualnych w celu wyświetlenia grup na podstawie subskrypcji, grupy zasobów, zestawów skalowania maszyn wirtualnych i usług Cloud Services.  Jeśli używasz Service Map i utworzono grupy maszyn, są one również wyświetlane.  Grupy komputerów będą również wyświetlane w filtr grup, jeśli zostały utworzone dla obszaru roboczego, który jest wyświetlany. 

### <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Jak mogę wyświetlić szczegółowe informacje o tym, co jest związane z wierszem percentylu używany 95. na zagregowanych wykresach wydajności?
Domyślnie lista jest sortowana w celu wyświetlenia maszyn wirtualnych o najwyższej wartości używany 95. percentylu dla wybranej metryki, z wyjątkiem dostępnego wykresu pamięci, który pokazuje komputery z najniższą wartością piątego percentylu.  Kliknięcie wykresu spowoduje otwarcie widoku **listy pierwszych N** z wybraną odpowiednią metryką.

### <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Jak funkcja mapy obsługuje zduplikowane adresy IP w różnych sieci wirtualnychach i podsieciach?
W przypadku duplikowania zakresów adresów IP z maszynami wirtualnymi lub zestawami skalowania maszyn wirtualnych platformy Azure w różnych podsieciach i sieci wirtualnych może dojść do wyświetlania nieprawidłowych informacji na mapie Azure Monitor dla maszyn wirtualnych. Jest to znany problem i badamy opcje w celu ulepszenia tego środowiska.

### <a name="does-map-feature-support-ipv6"></a>Czy funkcja map obsługuje protokół IPv6?
Funkcja map obsługuje obecnie tylko protokół IPv4 i badamy obsługę protokołu IPv6. Obsługujemy również protokół IPv4, który jest tunelowany w protokole IPv6.

### <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Gdy ładujemy mapę dla grupy zasobów lub innej dużej grupy, mapowanie jest trudne do wyświetlenia
Chociaż wprowadzono ulepszenia do mapowania w celu obsługi dużych i złożonych konfiguracji, wiemy, że mapa może mieć wiele węzłów, połączeń i węzłów działających jako klaster.  Ponadto zobowiązujemy się do zwiększenia pomocy technicznej w celu zwiększenia skalowalności w dalszym ciągu.   

### <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Dlaczego wykres sieciowy na karcie wydajność wygląda inaczej niż wykres sieciowy na stronie Przegląd maszyny wirtualnej platformy Azure?

Na stronie Przegląd dla maszyny wirtualnej platformy Azure są wyświetlane wykresy oparte na pomiarach aktywności hosta na maszynie wirtualnej gościa.  W przypadku wykresu sieciowego na maszynie wirtualnej platformy Azure przegląd jest wyświetlany tylko ruch sieciowy, który zostanie rozliczony.  Nie obejmuje to ruchu między sieciami wirtualnymi.  Dane i wykresy wyświetlane dla Azure Monitor dla maszyn wirtualnych opierają się na danych z maszyny wirtualnej gościa, a wykres sieciowy wyświetla cały ruch TCP/IP, który jest przychodzący i wychodzący do tej maszyny wirtualnej, w tym z sieci wirtualnej.

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Jak jest mierzony czas odpowiedzi dla danych przechowywanych w VMConnection i wyświetlany w panelu połączenia i w skoroszytach?

Czas odpowiedzi to przybliżenie. Ponieważ nie ma Instrumentacji kodu aplikacji, firma Microsoft nie wie, kiedy rozpocznie się żądanie i kiedy nadejdzie odpowiedź. Zamiast tego zauważymy, że dane są wysyłane w ramach połączenia, a następnie dane zostaną przywrócone z tego połączenia. Nasz Agent śledzi te wysyłanie i odbieranie i próbuje je sparować: sekwencja wysyłania, a następnie sekwencja odbierania jest interpretowana jako para żądania/odpowiedzi. Czas odpowiedzi między tymi operacjami to godzina reakcji. Obejmuje to opóźnienie sieci i czas przetwarzania serwera.

To zbliżenie sprawdza się w przypadku protokołów, na których oparto żądania/odpowiedzi: jedno żądanie jest wykonywane w ramach połączenia i odbierane są pojedyncze odpowiedzi. Dotyczy to w przypadku protokołu HTTP (bez przetwarzania potokowego), ale nie jest spełniony dla innych protokołów.

### <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Czy istnieją ograniczenia dotyczące bezpłatnego planu cenowego Log Analytics?
Jeśli skonfigurowano Azure Monitor z obszarem roboczym Log Analytics za pomocą warstwy cenowej *bezpłatna* , funkcja mapy Azure monitor dla maszyn wirtualnych będzie obsługiwać tylko pięć połączonych maszyn połączonych z obszarem roboczym. Jeśli masz pięć maszyn wirtualnych połączonych z bezpłatnym obszarem roboczym, odłączysz jedną z maszyn wirtualnych, a następnie nawiążesz połączenie nowej maszyny wirtualnej, Nowa maszyna wirtualna nie będzie monitorowana i odzwierciedlona na stronie Mapa.  

W tym stanie zostanie wyświetlony monit z opcją **Wypróbuj teraz** po otwarciu maszyny wirtualnej i wybraniu **szczegółowych informacji (wersja zapoznawcza)** w okienku po lewej stronie, nawet po zainstalowaniu go już na maszynie wirtualnej.  Nie jest jednak wyświetlany monit z opcjami, które zwykle wystąpią, jeśli ta maszyna wirtualna nie została dołączona do Azure Monitor dla maszyn wirtualnych. 


## <a name="next-steps"></a>Następne kroki
Jeśli na pytanie nie ma odpowiedzi, możesz zapoznać się z następującymi forami, aby uzyskać dodatkowe pytania i odpowiedzi.

- [Log Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)
- [Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)

Aby uzyskać ogólne informacje na temat Azure Monitor odwiedź [forum opinii](https://feedback.azure.com/forums/34192--general-feedback).
