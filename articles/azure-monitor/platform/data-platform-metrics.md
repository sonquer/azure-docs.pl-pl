---
title: Metryki w Azure Monitor | Microsoft Docs
description: Opisuje metryki w Azure Monitor, które stanowią dane lekkiego monitorowania, które mogą obsługiwać niemal scenariusze w czasie rzeczywistym.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: cd30803735c5453c286788b8669a3d2f02c418a5
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77468052"
---
# <a name="metrics-in-azure-monitor"></a>Metryki w usłudze Azure Monitor

> [!NOTE]
> Platforma danych Azure Monitor opiera się na dwóch podstawowych typach danych: metrykach i dziennikach. W tym artykule opisano metryki. Zapoznaj się z [dziennikami w Azure monitor](data-platform-logs.md) , aby uzyskać szczegółowy opis dzienników i [Azure monitor platformę danych](data-platform.md) w celu porównania dwóch.

Metryki w Azure Monitor są lekkie i mogą obsługiwać niemal scenariusze w czasie rzeczywistym, dzięki czemu są szczególnie przydatne w przypadku alertów i szybkiego wykrywania problemów. W tym artykule opisano sposób, w jaki metryki są strukturalne, co można z nimi zrobić i identyfikuje różne źródła danych, które przechowują dane w metrykach.

## <a name="what-are-metrics"></a>Co to są metryki?
Metryki są wartości liczbowe, które opisują niektóre aspekty systemu w określonym czasie. Metryki są zbierane w regularnych odstępach czasu i są przydatne w przypadku alertów, ponieważ mogą być próbkowane często, a alert może być uruchamiany szybko z stosunkowo prostą logiką.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Co możesz zrobić z metrykami Azure Monitor?
W poniższej tabeli wymieniono różne sposoby używania danych metryk w Azure Monitor.

|  |  |
|:---|:---|
| Analiza | Użyj [Eksploratora metryk](metrics-charts.md) do analizowania zebranych metryk na wykresie i porównywania metryk z różnych zasobów. |
| Wizualizacja | Przypinanie wykresu z Eksploratora metryk do [pulpitu nawigacyjnego platformy Azure](../learn/tutorial-app-dashboards.md).<br>Utwórz [skoroszyt](../app/usage-workbooks.md) , aby połączyć się z wieloma zestawami danych w raporcie interaktywnym. Eksportuj wyniki zapytania do [Grafana](grafana-plugin.md) , aby wykorzystać jego pulpit nawigacyjny i połączyć się z innymi źródłami danych. |
| Alerty | Skonfiguruj [regułę alertu metryki](alerts-metric.md) , która wysyła powiadomienie lub wykonuje [automatyczne działanie](action-groups.md) , gdy wartość metryki przekroczy próg. |
| Automatyzacja |  Użyj funkcji [automatycznego skalowania](autoscale-overview.md) , aby zwiększyć lub zmniejszyć zasoby na podstawie wartości metryki przekraczającej próg. |
| Eksportowanie | [Kierowanie metryk do dzienników](resource-logs-collect-storage.md) w celu analizowania danych w Azure monitor metryki wraz z danymi w dziennikach Azure monitor i przechowywanie wartości metryk przez dłużej niż 93 dni.<br>Metryki strumienia do [centrum zdarzeń](stream-monitoring-data-event-hubs.md) w celu kierowania ich do systemów zewnętrznych. |
| Odczytać | Uzyskiwanie dostępu do wartości metryk z wiersza polecenia przy użyciu [poleceń cmdlet programu PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights)<br>Dostęp do wartości metryk z aplikacji niestandardowej przy użyciu [interfejsu API REST](rest-api-walkthrough.md).<br>Uzyskaj dostęp do wartości metryk z wiersza polecenia przy użyciu [interfejsu CLI](/cli/azure/monitor/metrics). |
| Archiwum | [Archiwizuj](..//learn/tutorial-archive-data.md) historię wydajności lub kondycji zasobu w celu zapewnienia zgodności, inspekcji lub raportowania w trybie offline. |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Jak są zorganizowane dane w Azure Monitor metryki?
Dane zbierane przez metryki Azure Monitor są przechowywane w bazie danych szeregów czasowych, która jest zoptymalizowana pod kątem analizowania danych z sygnaturami czasowymi. Każdy zestaw wartości metryk jest szeregiem czasowym o następujących właściwościach:

* Godzina zebrania wartości
* Zasób, z którym jest skojarzona wartość
* Przestrzeń nazw, która działa jak kategoria dla metryki
* Nazwa metryki
* Sama wartość
* Niektóre metryki mogą mieć wiele wymiarów, zgodnie z opisem w [wielowymiarowych metrykach](#multi-dimensional-metrics). Metryki niestandardowe mogą mieć maksymalnie 10 wymiarów.

## <a name="multi-dimensional-metrics"></a>Metryk wielowymiarowych
Jednym z wyzwań związanych z danymi metryk jest to, że często zawiera ona ograniczone informacje w celu zapewnienia kontekstu zbieranych wartości. Azure Monitor rozwiązuje to wyzwanie przy użyciu metryk wielowymiarowych. Wymiary metryki są pary nazwa wartość, zawierających dodatkowe dane do opisania wartość metryki. Na przykład _ilość dostępnego miejsca na dysku_ może mieć wymiar o nazwie _dysk_ z wartościami _C:_ , _D:_ , co zezwoli na wyświetlanie dostępnego miejsca na dysku na wszystkich dyskach lub poszczególnych dyskach osobno.

Poniższy przykład ilustruje dwa zestawy danych dla hipotetycznej metryki o nazwie _przepływność sieci_. Pierwszy zestaw danych nie ma żadnych wymiarów. Drugi zestaw danych pokazuje wartości z dwoma wymiarami, _adresem IP_ i _kierunkiem_:

### <a name="network-throughput"></a>Przepływność sieci

| Znacznik czasu     | Wartość metryki |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1,331.8 Kb/s |
| 8/9/2017 8:15 | 1,141.4 Kb/s |
| 8/9/2017 8:16 | 1,110.2 Kb/s |

Ta metryka-wymiarowej można tylko odpowiedzi na podstawowe pytania, takie jak "jaki był Moje przepustowość sieci w danym momencie?"

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Przepływność sieci i dwa wymiary ("Adres IP" i "Direction")

| Znacznik czasu     | Wymiar "Adres IP"   | Wymiar "Direction" | Wartość metryki|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP = "192.168.5.2" | Kierunek = "Wyślij"    | 646.5 kb/s |
| 8/9/2017 8:14 | IP = "192.168.5.2" | Kierunek = "Otrzymywać" | 420.1 KB/s |
| 8/9/2017 8:14 | IP = "10.24.2.15"  | Kierunek = "Wyślij"    | 150.0 kb/s |
| 8/9/2017 8:14 | IP = "10.24.2.15"  | Kierunek = "Otrzymywać" | 115.2 Kb/s |
| 8/9/2017 8:15 | IP = "192.168.5.2" | Kierunek = "Wyślij"    | 515.2 Kb/s |
| 8/9/2017 8:15 | IP = "192.168.5.2" | Kierunek = "Otrzymywać" | 371.1 KB/s |
| 8/9/2017 8:15 | IP = "10.24.2.15"  | Kierunek = "Wyślij"    | 155.0 kb/s |
| 8/9/2017 8:15 | IP = "10.24.2.15"  | Kierunek = "Otrzymywać" | 100.1 KB/s |

Ta metryka może odpowiedzieć na pytania, takie jak "jaki był przepływności sieci dla każdego adresu IP?" i "ile danych zostało wysłane i odebrane?" Metryk wielowymiarowych wykonać dodatkową wartość analitycznych i diagnostyki w porównaniu do-wymiarowej metryki.

## <a name="interacting-with-azure-monitor-metrics"></a>Posługiwanie się metrykami Azure Monitor
Użyj [Eksplorator metryk](metrics-charts.md) , aby interaktywnie analizować dane w bazie danych metryk i wykresy wartości z wielu metryk w czasie. Możesz przypiąć wykresy do pulpitu nawigacyjnego, aby wyświetlić je z innymi wizualizacjami. Metryki można także pobrać przy użyciu [interfejsu API REST usługi Azure Monitoring](rest-api-walkthrough.md).

![Eksplorator metryk](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Źródła metryk Azure Monitor
Istnieją trzy podstawowe źródła metryki zebrane przez usługi Azure Monitor. Po zebraniu tych metryk w bazie danych metryk Azure Monitor można je ocenić razem niezależnie od ich źródła.

**Metryki platformy** są tworzone przez zasoby platformy Azure i zapewniają wgląd w ich kondycję i wydajność. Każdy typ zasobu tworzy [odrębny zestaw metryk](metrics-supported.md) , nie wymaga żadnej konfiguracji. Metryki platformy są zbierane z zasobów platformy Azure w częstotliwości jednej minuty, chyba że określono inaczej w definicji metryki. 

**Metryki systemu operacyjnego gościa** są zbierane z systemu operacyjnego gościa maszyny wirtualnej. Włącz metryki systemu operacyjnego gościa dla maszyn wirtualnych z systemem Windows z [rozszerzeniem diagnostyki systemu Windows (funkcji wad)](../platform/diagnostics-extension-overview.md) i maszyn wirtualnych z systemem Linux przy użyciu [agenta InfluxData telegraf](https://www.influxdata.com/time-series-platform/telegraf/).

**Metryki aplikacji** są tworzone przez Application Insights dla monitorowanych aplikacji i pomagają wykrywać problemy z wydajnością oraz śledzić trendy w sposobie używania aplikacji. Obejmuje to takie wartości jak _czas odpowiedzi serwera_ i _wyjątki przeglądarki_.

**Metryki niestandardowe** to metryki zdefiniowane jako uzupełnienie standardowych metryk, które są automatycznie dostępne. Możesz [definiować niestandardowe metryki w aplikacji](../app/api-custom-events-metrics.md) monitorowane przez Application Insights lub tworzyć niestandardowe metryki dla usługi platformy Azure przy użyciu [interfejsu API metryk niestandardowych](metrics-store-custom-rest-api.md).

## <a name="retention-of-metrics"></a>Przechowywanie metryk
W przypadku większości zasobów na platformie Azure metryki są przechowywane przez 93 dni. Istnieją pewne wyjątki:

**Metryki systemu operacyjnego gościa**
-   **Metryki klasycznych systemów operacyjnych gościa**. Są to liczniki wydajności zbierane przez [rozszerzenie diagnostyki systemu Windows (funkcji wad)](../platform/diagnostics-extension-overview.md) lub [rozszerzenie diagnostyki Linux (lad)](../../virtual-machines/extensions/diagnostics-linux.md) i kierowane do konta usługi Azure Storage. Przechowywanie tych metryk wynosi 14 dni.
-   **Metryki systemu operacyjnego gościa wysyłane do metryk Azure monitor**. Są to liczniki wydajności zbierane przez [rozszerzenie diagnostyki systemu Windows (funkcji wad)](diagnostics-extension-overview.md) i wysyłane do [ujścia danych Azure monitor](diagnostics-extension-overview.md#data-destinations)lub za pośrednictwem [agenta InfluxData telegraf](https://www.influxdata.com/time-series-platform/telegraf/) na maszynach z systemem Linux. Przechowywanie tych metryk to 93 dni.
-   **Metryki systemu operacyjnego gościa zebrane przez agenta log Analytics**. Są to liczniki wydajności zbierane przez agenta Log Analytics i wysyłane do Log Analytics obszaru roboczego. Przechowywanie tych metryk wynosi 31 dni i można je przedłużyć do 2 lat.

**Application Insights metryki opartych na dziennikach**. 
- Za sceną [metryki oparte na dziennikach](../app/pre-aggregated-metrics-log-metrics.md) są tłumaczone na zapytania dziennika. Ich przechowywanie jest zgodne z przechowywaniem zdarzeń w dziennikach bazowych. W przypadku zasobów Application Insights dzienniki są przechowywane przez 90 dni.


> [!NOTE]
> [Metryki platformy dla Azure Monitor zasobów można wysyłać do log Analytics obszaru roboczego](resource-logs-collect-storage.md) w celu uzyskania długoterminowych trendów.





## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [platformie danych Azure monitor](data-platform.md).
- Dowiedz się więcej o [danych dziennika w Azure monitor](data-platform-logs.md).
- Poznaj [dane monitorowania dostępne](data-sources.md) dla różnych zasobów na platformie Azure.
