---
title: Przegląd rozszerzenia Diagnostyka Azure
description: Korzystaj z diagnostyki platformy Azure na potrzeby debugowania, mierzenia wydajności, monitorowania, analizy ruchu w usługach Cloud Services, Virtual Machines i Service Fabric
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: d9db4b4c8e6d82f29d227b9f8afe528e000c651e
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77468001"
---
# <a name="azure-diagnostics-extension-overview"></a>Przegląd rozszerzenia Diagnostyka Azure
Diagnostyka Azure rozszerzenie jest [agentem w Azure monitor](agents-overview.md) , który zbiera dane monitorowania z systemu operacyjnego gościa zasobów obliczeniowych platformy Azure, w tym maszyn wirtualnych. Ten artykuł zawiera Omówienie rozszerzenia Diagnostyka Azure, w tym określonych funkcji, które obsługuje, oraz opcji instalacji i konfiguracji. 

> [!NOTE]
> Diagnostyka Azure rozszerzenie jest jednym z agentów dostępnych do zbierania danych monitorowania z systemu operacyjnego gościa zasobów obliczeniowych. Zapoznaj się z [omówieniem agentów Azure monitor](agents-overview.md) , aby uzyskać opis różnych agentów i wskazówki dotyczące wybierania odpowiednich agentów w celu spełnienia określonych wymagań.

## <a name="comparison-to-log-analytics-agent"></a>Porównanie z agentem Log Analytics
Agent Log Analytics w Azure Monitor może być również używany do zbierania danych monitorowania z systemu operacyjnego gościa maszyn wirtualnych. W zależności od potrzeb można użyć lub obu tych opcji. Szczegółowe porównanie agentów Azure Monitor można znaleźć w temacie [Omówienie agentów Azure monitor](agents-overview.md) . 

Kluczowe różnice, które należy wziąć pod uwagę:

- Rozszerzenia Diagnostyka Azure można używać tylko z maszynami wirtualnymi platformy Azure. Agent Log Analytics może być używany z maszynami wirtualnymi platformy Azure, innymi chmurami i lokalnymi.
- Diagnostyka Azure rozszerzenie wysyła dane do usługi Azure Storage, [Azure monitor metryk](data-platform-metrics.md) (tylko system Windows) i Event Hubs. Agent Log Analytics zbiera dane do [dzienników Azure monitor](data-platform-logs.md).
- Agent Log Analytics jest wymagany w przypadku [rozwiązań](../monitor-reference.md#insights-and-core-solutions), [Azure monitor dla maszyn wirtualnych](../insights/vminsights-overview.md)i innych usług, takich jak [Azure Security Center](/azure/security-center/).

## <a name="costs"></a>Koszty
Usługa Azure Diagnostic Extension nie ma kosztu, ale opłaty za dane pozyskiwane mogą być naliczane. Sprawdź [ceny Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) lokalizacji docelowej, w której zbierane są dane.

## <a name="data-collected"></a>Zebrane dane
W poniższej tabeli wymieniono dane, które mogą być zbierane przez rozszerzenie diagnostyki systemu Windows i Linux.

### <a name="windows-diagnostics-extension-wad"></a>Rozszerzenie diagnostyki systemu Windows (funkcji wad)

| Źródło danych | Opis |
| --- | --- |
| Dzienniki zdarzeń Windows   | Zdarzenia z dziennika zdarzeń systemu Windows. |
| Liczniki wydajności | Wartości liczbowe mierzące wydajność różnych aspektów systemu operacyjnego i obciążeń. |
| Dzienniki usług IIS             | Informacje o użyciu witryn sieci Web usług IIS działających w systemie operacyjnym gościa. |
| Dzienniki aplikacji     | Komunikaty śledzenia zapisywane przez aplikację. |
| Dzienniki zdarzeń platformy .NET |Kod pisania zdarzeń przy użyciu klasy [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) platformy .NET |
| [Dzienniki ETW oparte na manifestach](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |Śledzenie zdarzeń dla zdarzeń systemu Windows generowanych przez dowolny proces. |
| Zrzuty awaryjne (dzienniki)   | Informacje o stanie procesu w przypadku awarii aplikacji. |
| Dzienniki na podstawie plików    | Dzienniki utworzone przez aplikację lub usługę. |
| Dzienniki diagnostyczne agenta | Informacje dotyczące Diagnostyka Azure samego siebie. |


### <a name="linux-diagnostics-extension-lad"></a>Rozszerzenie diagnostyki systemu Linux (LAD)

| Źródło danych | Opis |
| --- | --- |
| Dziennik systemu | Zdarzenia wysyłane do systemu rejestrowania zdarzeń w systemie Linux.   |
| Liczniki wydajności  | Wartości liczbowe mierzące wydajność różnych aspektów systemu operacyjnego i obciążeń. |
| Pliki dziennika | Wpisy wysyłane do dziennika na podstawie plików.  |

## <a name="data-destinations"></a>Miejsca docelowe danych
Rozszerzenie diagnostyki platformy Azure dla systemów Windows i Linux zawsze zbiera dane na konto usługi Azure Storage. Zapoznaj się z tematem [Instalowanie i Konfigurowanie rozszerzenia Windows Azure Diagnostics (funkcji wad)](diagnostics-extension-windows-install.md) i [Używanie rozszerzenia diagnostyki systemu Linux do monitorowania metryk i dzienników](../../virtual-machines/extensions/diagnostics-linux.md) w poszukiwaniu listy określonych tabel i obiektów blob, w których są zbierane dane.

Skonfiguruj co najmniej jeden *ujścia danych* w celu wysyłania danych do innych dodatkowych miejsc docelowych. W poniższych sekcjach wymieniono ujścia dostępne dla rozszerzenia Diagnostyka systemu Windows i Linux.

### <a name="windows-diagnostics-extension-wad"></a>Rozszerzenie diagnostyki systemu Windows (funkcji wad)

| Element docelowy | Opis |
|:---|:---|
| Metryki Azure Monitor | Zbieranie danych wydajności do metryk Azure Monitor. Zobacz [wysyłanie metryk systemu operacyjnego gościa do bazy danych metryk Azure monitor](collect-custom-metrics-guestos-resource-manager-vm.md).  |
| Usługa Event Hubs | Wyślij dane poza platformą Azure przy użyciu usługi Azure Event Hubs. Zobacz [przesyłanie strumieniowe danych Diagnostyka Azure do Event Hubs](diagnostics-extension-stream-event-hubs.md) |
| Obiekty blob usługi Azure Storage | Oprócz tabel Zapisz dane w obiektach Blob w usłudze Azure Storage. |
| Application Insights | Zbierz dane z aplikacji działających na maszynie wirtualnej, aby Application Insights integrację z innym monitorowaniem aplikacji. Zobacz [wysyłanie danych diagnostycznych do Application Insights](diagnostics-extension-to-application-insights.md). |

Możesz również zbierać dane funkcji wad z magazynu w obszarze roboczym Log Analytics, aby przeanalizować je za pomocą Azure Monitor dzienników, chociaż Agent Log Analytics jest zazwyczaj używany w tej funkcji. Może wysyłać dane bezpośrednio do obszaru roboczego Log Analytics i obsługuje rozwiązania i szczegółowe informacje zapewniające dodatkowe funkcje.  Zobacz [zbieranie dzienników diagnostycznych platformy Azure z usługi Azure Storage](diagnostics-extension-logs.md). 


### <a name="linux-diagnostics-extension-lad"></a>Rozszerzenie diagnostyki systemu Linux (LAD)
LAD zapisuje dane w tabelach w usłudze Azure Storage. Obsługuje ona ujścia w poniższej tabeli.

| Element docelowy | Opis |
|:---|:---|
| Usługa Event Hubs | Wyślij dane poza platformą Azure przy użyciu usługi Azure Event Hubs. |
| Obiekty blob usługi Azure Storage | Oprócz tabel Zapisz dane w obiektach Blob w usłudze Azure Storage. |
| Metryki Azure Monitor | Oprócz LAD należy zainstalować agenta telegraf. Zobacz [zbieranie niestandardowych metryk dla maszyny wirtualnej z systemem Linux za pomocą agenta InfluxData telegraf](collect-custom-metrics-linux-telegraf.md).


## <a name="installation-and-configuration"></a>Instalacja i konfiguracja
Rozszerzenie diagnostyczne jest zaimplementowane jako [rozszerzenie maszyny wirtualnej](/virtual-machines/extensions/overview) na platformie Azure, dlatego obsługuje te same opcje instalacji przy użyciu szablonów Menedżer zasobów, programu PowerShell i interfejsu wiersza polecenia. Aby uzyskać ogólne informacje na temat instalowania i konserwowania rozszerzeń maszyn wirtualnych, zobacz [rozszerzenia i funkcje maszyny wirtualnej dla systemu Windows](/virtual-machines/extensions/features-windows) i [rozszerzeń i funkcji maszyn wirtualnych](/virtual-machines/extensions/features-linux) w systemie Linux.

Można także zainstalować i skonfigurować rozszerzenie diagnostyczne systemu Windows i Linux w Azure Portal w obszarze **Ustawienia diagnostyczne** w sekcji **monitorowanie** w menu maszyny wirtualnej.

Zobacz następujące artykuły, aby uzyskać szczegółowe informacje na temat instalowania i konfigurowania rozszerzenia diagnostyki dla systemów Windows i Linux.

- [Instalowanie i Konfigurowanie rozszerzenia diagnostyki systemu Windows Azure (funkcji wad)](diagnostics-extension-windows-install.md)
- [Monitorowanie metryk i dzienników przy użyciu rozszerzenia diagnostycznego systemu Linux](../../virtual-machines/extensions/diagnostics-linux.md)

## <a name="other-documentation"></a>Inna dokumentacja

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Role sieć Web i proces roboczy w usłudze Azure Cloud Service (klasyczny)
- [Wprowadzenie do monitorowania usługi w chmurze](../../cloud-services/cloud-services-how-to-monitor.md)
- [Włączanie Diagnostyka Azure na platformie Azure Cloud Services](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [Application Insights dla usług Azure Cloud Services](../app/cloudservices.md)<br>[Śledzenie przepływu aplikacji Cloud Services z Diagnostyka Azure](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [Monitor and diagnose services in a local machine development setup (Monitorowanie i diagnozowanie usług w konfiguracji środowiska deweloperskiego na maszynie lokalnej)](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>Następne kroki


* Dowiedz się, jak [używać liczników wydajności w Diagnostyka Azure](../../cloud-services/diagnostics-performance-counters.md).
* Jeśli masz problemy z uruchamianiem diagnostyki lub wyszukiwaniem danych w tabelach usługi Azure Storage, zobacz [Rozwiązywanie problemów Diagnostyka Azure](diagnostics-extension-troubleshooting.md)

