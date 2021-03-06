---
title: Agregacja zdarzeń Service Fabric platformy Azure za pomocą użyciu struktury eventflow
description: Dowiedz się więcej na temat agregowania i zbierania zdarzeń przy użyciu użyciu struktury eventflow do monitorowania i diagnostyki klastrów Service Fabric platformy Azure.
author: srrengar
ms.topic: conceptual
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: cde24657cc8ed78b91e72df16d51df4077a6e030
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463095"
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>Agregacja i zbieranie zdarzeń przy użyciu użyciu struktury eventflow

[Użyciu struktury eventflow diagnostyki firmy Microsoft](https://github.com/Azure/diagnostics-eventflow) może kierować zdarzenia z węzła do co najmniej jednego miejsca docelowego monitorowania. Ponieważ jest on uwzględniony jako pakiet NuGet w projekcie usługi, użyciu struktury eventflow kod i konfigurację są przesyłane z usługą, eliminując wymieniony wcześniej problem z konfiguracją dla każdego węzła na Diagnostyka Azure. Użyciu struktury eventflow działa w ramach procesu usługi i łączy się bezpośrednio ze skonfigurowanymi wynikami. Ze względu na połączenie bezpośrednie użyciu struktury eventflow działa dla wdrożeń platformy Azure, kontenerów i usług lokalnych. Należy zachować ostrożność w przypadku uruchamiania użyciu struktury eventflow w scenariuszach o wysokiej gęstości, takich jak w przypadku kontenera, ponieważ każdy potok użyciu struktury eventflow wykonuje połączenie zewnętrzne. Dlatego w przypadku hostowania kilku procesów uzyskasz kilka połączeń wychodzących. Nie ma to znaczenia dla Service Fabric aplikacji, ponieważ wszystkie repliki `ServiceType` są uruchamiane w tym samym procesie i ograniczają liczbę połączeń wychodzących. Użyciu struktury eventflow oferuje także filtrowanie zdarzeń, dzięki czemu wysyłane są tylko zdarzenia zgodne z określonym filtrem.

## <a name="set-up-eventflow"></a>Konfigurowanie użyciu struktury eventflow

Pliki binarne użyciu struktury eventflow są dostępne jako zbiór pakietów NuGet. Aby dodać użyciu struktury eventflow do projektu usługi Service Fabric, kliknij prawym przyciskiem myszy projekt w Eksplorator rozwiązań i wybierz polecenie "Zarządzaj pakietami NuGet". Przejdź do karty "Przeglądaj" i wyszukaj ciąg "`Diagnostics.EventFlow`":

![Użyciu struktury eventflow pakiety NuGet w interfejsie użytkownika Menedżera pakietów NuGet programu Visual Studio](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

Zostanie wyświetlona lista różnych pakietów, które są wyświetlane z etykietą "dane wejściowe" i "wyjścia". Użyciu struktury eventflow obsługuje różnych różnych dostawców rejestrowania i analizatorów. Użyciu struktury eventflow hostingu usługi powinna zawierać odpowiednie pakiety w zależności od źródła i miejsca docelowego dzienników aplikacji. Oprócz podstawowego pakietu servicefabric potrzebne są również co najmniej jedno skonfigurowane dane wejściowe i wyjściowe. Na przykład można dodać następujące pakiety, aby wysłać zdarzenia EventSource do Application Insights:

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource` przechwytywania danych z klasy EventSource usługi i ze standardowych zdarzeń EventSource, takich jak *Microsoft-servicefabric-Services* i *Microsoft-Servicefabric-aktors*)
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` (będziemy wysyłać dzienniki do zasobu Application Insights platformy Azure)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric`(umożliwia inicjowanie potoku użyciu struktury eventflow z poziomu konfiguracji usługi Service Fabric i zgłasza wszelkie problemy z wysyłaniem danych diagnostycznych jako Service Fabric raportów kondycji)

>[!NOTE]
>Pakiet `Microsoft.Diagnostics.EventFlow.Inputs.EventSource` wymaga, aby projekt usługi był przeznaczony dla .NET Framework 4,6 lub nowszego. Przed zainstalowaniem tego pakietu upewnij się, że we właściwościach projektu została ustawiona odpowiednia struktura docelowa.

Po zainstalowaniu wszystkich pakietów następnym krokiem jest skonfigurowanie i włączenie użyciu struktury eventflow w usłudze.

## <a name="configure-and-enable-log-collection"></a>Konfigurowanie i Włączanie zbierania dzienników
Potok użyciu struktury eventflow odpowiedzialny za wysyłanie dzienników jest tworzony na podstawie specyfikacji przechowywanej w pliku konfiguracji. Pakiet `Microsoft.Diagnostics.EventFlow.ServiceFabric` instaluje początkowy plik konfiguracji użyciu struktury eventflow w folderze rozwiązania `PackageRoot\Config` o nazwie `eventFlowConfig.json`. Ten plik konfiguracji należy zmodyfikować, aby przechwytywać dane z domyślnej klasy usługi `EventSource`, a także wszelkich innych danych wejściowych, które mają zostać skonfigurowane, i wysyłać dane do odpowiednich miejsc.

>[!NOTE]
>Jeśli plik projektu ma format VisualStudio 2017, plik `eventFlowConfig.json` nie zostanie automatycznie dodany. Aby rozwiązać ten problem, Utwórz plik w folderze `Config` i ustaw akcję kompilacji na `Copy if newer`. 

Oto przykładowy plik *eventFlowConfig. JSON* oparty na wymienionych powyżej pakietach NuGet:
```json
{
  "inputs": [
    {
      "type": "EventSource",
      "sources": [
        { "providerName": "Microsoft-ServiceFabric-Services" },
        { "providerName": "Microsoft-ServiceFabric-Actors" },
        // (replace the following value with your service's ServiceEventSource name)
        { "providerName": "your-service-EventSource-name" }
      ]
    }
  ],
  "filters": [
    {
      "type": "drop",
      "include": "Level == Verbose"
    }
  ],
  "outputs": [
    {
      "type": "ApplicationInsights",
      // (replace the following value with your AI resource's instrumentation key)
      "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
  ],
  "schemaVersion": "2016-08-11"
}
```

Nazwa elementu serviceeventsource usługi jest wartością właściwości nazwy `EventSourceAttribute` stosowanej do klasy serviceeventsource. To wszystko jest określone w pliku `ServiceEventSource.cs`, który jest częścią kodu usługi. Na przykład w poniższym fragmencie kodu nazwa elementu serviceeventsource to *Application1-Stateless1*:

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

Należy pamiętać, że plik `eventFlowConfig.json` jest częścią pakietu konfiguracji usługi. Zmiany w tym pliku mogą być zawarte w pełnych lub tylko konfiguracyjnych uaktualnieniach usługi, z zastrzeżeniem Service Fabric uaktualnienia kondycji i automatycznego wycofywania w przypadku niepowodzenia uaktualnienia. Aby uzyskać więcej informacji, zobacz [Service Fabric uaktualniania aplikacji](service-fabric-application-upgrade.md).

Sekcja *filtry* konfiguracji umożliwia dalsze dostosowywanie informacji, które przechodzą przez potok użyciu struktury eventflow do danych wyjściowych, co pozwala na upuszczanie lub dołączenie określonych informacji lub zmianę struktury danych zdarzeń. Aby uzyskać więcej informacji na temat filtrowania, zobacz [filtry użyciu struktury eventflow](https://github.com/Azure/diagnostics-eventflow#filters).

Ostatnim krokiem jest utworzenie wystąpienia potoku użyciu struktury eventflow w kodzie początkowym usługi, znajdującym się w pliku `Program.cs`:

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Services.Runtime;

// **** EventFlow namespace
using Microsoft.Diagnostics.EventFlow.ServiceFabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>
        private static void Main()
        {
            try
            {
                // **** Instantiate log collection via EventFlow
                using (var diagnosticsPipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MyApplication-MyService-DiagnosticsPipeline"))
                {

                    ServiceRuntime.RegisterServiceAsync("Stateless1Type",
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}
```

Nazwa przenoszona jako parametr metody `CreatePipeline` `ServiceFabricDiagnosticsPipelineFactory` jest nazwą *jednostki kondycji* reprezentującej potok zbierania dzienników użyciu struktury eventflow. Ta nazwa jest używana, jeśli użyciu struktury eventflow napotyka i błęduje i raportuje go za pomocą podsystemu kondycji Service Fabric.

### <a name="use-service-fabric-settings-and-application-parameters-in-eventflowconfig"></a>Używanie ustawień Service Fabric i parametrów aplikacji w eventFlowConfig

Użyciu struktury eventflow obsługuje używanie ustawień Service Fabric i parametrów aplikacji do konfigurowania ustawień użyciu struktury eventflow. Można odwołać się do parametrów ustawień Service Fabric przy użyciu tej specjalnej składni dla wartości:

```json
servicefabric:/<section-name>/<setting-name>
```

`<section-name>` jest nazwą sekcji konfiguracji Service Fabric, a `<setting-name>` jest ustawieniem konfiguracji dostarczającym wartość, która będzie używana do konfigurowania ustawienia użyciu struktury eventflow. Aby dowiedzieć się więcej o tym, jak to zrobić, przejdź do [pomocy technicznej dotyczącej ustawień Service Fabric i parametrów aplikacji](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters).

## <a name="verification"></a>Weryfikacja

Uruchom usługę i zaobserwuj okno dane wyjściowe debugowania w programie Visual Studio. Po uruchomieniu usługi należy zacząć wyświetlać dowód, że usługa wysyła rekordy do danych wyjściowych, które zostały skonfigurowane. Przejdź do platformy analizy zdarzeń i wizualizacji, a następnie upewnij się, że dzienniki zostały uruchomione w celu wyświetlenia (może to potrwać kilka minut).

## <a name="next-steps"></a>Następne kroki

* [Analiza zdarzeń i wizualizacja przy użyciu Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Analiza zdarzeń i wizualizacja z dziennikami Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md)
* [Dokumentacja użyciu struktury eventflow](https://github.com/Azure/diagnostics-eventflow)
