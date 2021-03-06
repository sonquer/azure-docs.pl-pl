---
title: Analizowanie dzienników i metryk w chmurze Azure wiosennej | Microsoft Docs
description: Dowiedz się, jak analizować dane diagnostyczne w chmurze Azure wiosennej
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.openlocfilehash: fc1f81c616dc6ee664bb5be924f2a1586646d16d
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279159"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Analizowanie dzienników i metryk przy użyciu ustawień diagnostycznych

Korzystając z funkcji diagnostyki chmury wiosennej platformy Azure, można analizować dzienniki i metryki przy użyciu dowolnej z następujących usług:

* Użyj usługi Azure Log Analytics, w której dane są zapisywane w usłudze Azure Storage. Podczas eksportowania dzienników do Log Analytics istnieje opóźnienie.
* Zapisz dzienniki na koncie magazynu, aby przeprowadzić inspekcję lub inspekcję ręczną. Możesz określić czas przechowywania (w dniach).
* Przesyłaj strumieniowo dzienniki do centrum zdarzeń w celu pozyskiwania przez usługę innej firmy lub rozwiązanie do analizy niestandardowej.

Wybierz kategorię dziennika i kategorię metryki, którą chcesz monitorować.

## <a name="logs"></a>Dzienniki

|Dziennik | Opis |
|----|----|
| **ApplicationConsole** | Dziennik konsoli wszystkich aplikacji klienta. | 
| **SystemLogs** | Obecnie w tej kategorii znajdują się tylko [źródła dzienników serwera konfiguracji chmury](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) . |

## <a name="metrics"></a>Metryki

Aby uzyskać pełną listę metryk, zobacz [metryki w chmurze wiosennej](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-concept-metrics#user-portal-metrics-options)

Aby rozpocząć, włącz jedną z tych usług, aby otrzymywać dane. Aby dowiedzieć się więcej o konfigurowaniu Log Analytics, zobacz Wprowadzenie do [log Analytics w Azure monitor](../azure-monitor/log-query/get-started-portal.md). 

## <a name="configure-diagnostics-settings"></a>Konfigurowanie ustawień diagnostycznych

1. W Azure Portal przejdź do wystąpienia chmury wiosennej platformy Azure.
1. Wybierz opcję **Ustawienia diagnostyczne** , a następnie wybierz pozycję **Dodaj ustawienie diagnostyczne**.
1. Wprowadź nazwę ustawienia, a następnie wybierz miejsce, w którym chcesz wysłać dzienniki. Można wybrać dowolną kombinację następujących trzech opcji:
    * **Archiwizowanie na koncie magazynu**
    * **Przesyłanie strumieniowe do centrum zdarzeń**
    * **Wyślij do Log Analytics**

1. Wybierz kategorię dziennika i kategorię metryki, którą chcesz monitorować, a następnie określ czas przechowywania (w dniach). Czas przechowywania ma zastosowanie tylko do konta magazynu.
1. Wybierz pozycję **Zapisz**.

> [!NOTE]
> Od momentu, gdy dzienniki lub metryki są emitowane i pojawiają się na koncie magazynu, centrum zdarzeń lub Log Analytics.

## <a name="view-the-logs-and-metrics"></a>Wyświetlanie dzienników i metryk
Istnieją różne metody wyświetlania dzienników i metryk zgodnie z opisem w poniższych nagłówkach.

### <a name="use-logs-blade"></a>Użyj bloku dzienników

1. W Azure Portal przejdź do wystąpienia chmury wiosennej platformy Azure.
1. Aby otworzyć okienko **przeszukiwania dzienników** , wybierz pozycję **dzienniki**.
1. W polu wyszukiwania w **dzienniku**
   * Aby wyświetlić dzienniki, wprowadź proste zapytanie, takie jak:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * Aby wyświetlić metryki, wprowadź proste zapytanie, takie jak:

    ```sql
    AzureMetrics
    | limit 50
    ```
1. Aby wyświetlić wyniki wyszukiwania, wybierz pozycję **Uruchom**.

### <a name="use-log-analytics"></a>Korzystanie z usługi Log Analytics

1. W Azure Portal w lewym okienku wybierz pozycję **log Analytics**.
1. Wybierz obszar roboczy Log Analytics, który został wybrany podczas dodawania ustawień diagnostycznych.
1. Aby otworzyć okienko **przeszukiwania dzienników** , wybierz pozycję **dzienniki**.
1. W polu wyszukiwania w **dzienniku**
   * Aby wyświetlić dzienniki, wprowadź proste zapytanie, takie jak:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * Aby wyświetlić metryki, wprowadź proste zapytanie, takie jak:

    ```sql
    AzureMetrics
    | limit 50
    ```

1. Aby wyświetlić wyniki wyszukiwania, wybierz pozycję **Uruchom**.
1. Dzienniki konkretnej aplikacji lub wystąpienia można wyszukać, ustawiając warunek filtru:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]  
> `==` uwzględnia wielkość liter, ale `=~` nie jest.

Aby dowiedzieć się więcej o języku zapytań używanym w Log Analytics, zobacz [Azure monitor dziennika zapytań](../azure-monitor/log-query/query-language.md).

### <a name="use-your-storage-account"></a>Korzystanie z konta magazynu 

1. W Azure Portal w okienku po lewej stronie wybierz pozycję **konta magazynu**.

1. Wybierz konto magazynu wybrane podczas dodawania ustawień diagnostycznych.
1. Aby otworzyć okienko **kontenera obiektów BLOB** , wybierz pozycję **obiekty blob**.
1. Aby przejrzeć dzienniki aplikacji, Wyszukaj kontener o nazwie **Insights-Logs-applicationconsole**.
1. Aby przejrzeć metryki aplikacji, Wyszukaj kontener o nazwie **Insights-Metrics-pt1m**.

Aby dowiedzieć się więcej o wysyłaniu informacji diagnostycznych do konta magazynu, zobacz temat [Zapisywanie i wyświetlanie danych diagnostycznych w usłudze Azure Storage](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-to-storage).

### <a name="use-your-event-hub"></a>Korzystanie z centrum zdarzeń

1. W Azure Portal w lewym okienku wybierz pozycję **Event Hubs**.

1. Wyszukaj i wybierz centrum zdarzeń wybrane podczas dodawania ustawień diagnostycznych.
1. Aby otworzyć okienko **Lista centrów zdarzeń** , wybierz pozycję **Event Hubs**.
1. Aby przejrzeć dzienniki aplikacji, Wyszukaj centrum zdarzeń o nazwie **Insights-Logs-applicationconsole**.
1. Aby przejrzeć metryki aplikacji, Wyszukaj centrum zdarzeń o nazwie **Insights-Metrics-pt1m**.

Aby dowiedzieć się więcej o wysyłaniu informacji diagnostycznych do centrum zdarzeń, zobacz [przesyłanie strumieniowe danych Diagnostyka Azure w ścieżce gorąca przy użyciu Event Hubs](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs).

## <a name="analyze-the-logs"></a>Analizowanie dzienników

Usługa Azure Log Analytics działa z aparatem Kusto, dzięki czemu możesz wysyłać zapytania do dzienników w celu analizy. Aby zapoznać się z krótkim wprowadzeniem do wykonywania zapytań dotyczących dzienników przy użyciu Kusto, zapoznaj się z [samouczkiem log Analytics](../azure-monitor/log-query/get-started-portal.md).

Dzienniki aplikacji zawierają informacje krytyczne i pełne dzienniki dotyczące kondycji, wydajności i innych aplikacji. W następnych sekcjach znajdują się pewne proste zapytania, które pomagają zrozumieć bieżące i wcześniejsze Stany aplikacji.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Wyświetlanie dzienników aplikacji z chmury Azure wiosennej

Aby zapoznać się z listą dzienników aplikacji z chmury Azure wiosennej, posortowanej według czasu z najnowszymi wyświetlanymi dziennikami, uruchom następujące zapytanie:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Pokaż wpisy dzienników zawierające błędy lub wyjątki

Aby przejrzeć niesortowane wpisy dziennika wskazujące błąd lub wyjątek, uruchom następujące zapytanie:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Użyj tego zapytania, aby znaleźć błędy lub zmodyfikować terminy zapytania, aby znaleźć określone kody błędów lub wyjątki. 

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Pokaż liczbę błędów i wyjątków zgłoszonych przez aplikację w ciągu ostatniej godziny

Aby utworzyć wykres kołowy, który wyświetla liczbę błędów i wyjątków zarejestrowanych przez aplikację w ciągu ostatniej godziny, uruchom następujące zapytanie:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Dowiedz się więcej o wysyłaniu zapytań do dzienników aplikacji

Azure Monitor zapewnia szeroką pomoc techniczną dotyczącą wykonywania zapytań dotyczących dzienników aplikacji przy użyciu Log Analytics. Aby dowiedzieć się więcej na temat tej usługi, zobacz [Rozpoczynanie pracy z kwerendami dzienników w Azure monitor](../azure-monitor/log-query/get-started-queries.md). Aby uzyskać więcej informacji na temat tworzenia zapytań w celu analizowania dzienników aplikacji, zobacz [Omówienie zapytań dzienników w Azure monitor](../azure-monitor/log-query/log-query-overview.md).
