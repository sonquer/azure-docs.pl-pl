---
title: Service Fabric platformy Azure z usługą API Management — Omówienie
description: Ten artykuł zawiera wprowadzenie do korzystania z usługi Azure API Management jako bramy do aplikacji Service Fabric.
author: vturecek
ms.topic: conceptual
ms.date: 06/22/2017
ms.author: vturecek
ms.openlocfilehash: 2a331715d4e4538cfdda8d958ff549a81b627b79
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028554"
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Service Fabric z usługą Azure API Management — Omówienie

Aplikacje w chmurze zwykle potrzebują bramy frontonu, aby udostępniać pojedynczy punkt danych przychodzących dla użytkowników, urządzeń lub innych aplikacji. W Service Fabric Brama może być dowolną usługą bezstanową, taką jak [aplikacja ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)lub inna usługa zaprojektowana na potrzeby ruchu przychodzącego, na przykład [Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [IoT Hub](https://docs.microsoft.com/azure/iot-hub/)lub [Azure API Management](https://docs.microsoft.com/azure/api-management/).

Ten artykuł zawiera wprowadzenie do korzystania z usługi Azure API Management jako bramy do aplikacji Service Fabric. API Management integruje się bezpośrednio z Service Fabric, co pozwala na publikowanie interfejsów API z bogatym zestawem reguł routingu do usług Service Fabric zaplecza.

## <a name="availability"></a>Dostępność

> [!IMPORTANT]
> Ta funkcja jest dostępna w warstwach **Premium** i **Developer** API Management ze względu na wymaganą obsługę sieci wirtualnej.

## <a name="architecture"></a>Architektura

Wspólna architektura Service Fabric używa jednostronicowej aplikacji sieci Web, która umożliwia wywołania protokołu HTTP do usług zaplecza, które uwidaczniają interfejsy API protokołu HTTP. Przykładowa [aplikacja do uruchamiania Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) przedstawia przykładowy sposób korzystania z tej architektury.

W tym scenariuszu bezstanowa usługa sieci Web służy jako brama w aplikacji Service Fabric. Takie podejście wymaga napisania usługi sieci Web, która może żądania HTTP serwera proxy do usług zaplecza, jak pokazano na poniższym diagramie:

![Service Fabric z usługą Azure API Management Topology — Omówienie][sf-web-app-stateless-gateway]

Aplikacje zwiększają złożoność, dlatego bramy, które muszą przedstawić interfejs API przed usługami zaplecza wyposażono. Usługa Azure API Management jest przeznaczona do obsługi złożonych interfejsów API z regułami routingu, kontroli dostępu, ograniczania szybkości, monitorowania, rejestrowania zdarzeń i buforowania odpowiedzi z minimalną ilością pracy w Twojej części. Usługa Azure API Management obsługuje odnajdywanie usługi Service Fabric, rozpoznawanie partycji i wybór repliki, aby inteligentnie kierować żądania bezpośrednio do usług zaplecza w Service Fabric, dzięki czemu nie trzeba pisać własnej bezstanowej bramy interfejsu API. 

W tym scenariuszu interfejs użytkownika sieci Web jest nadal obsługiwany przez usługę sieci Web, podczas gdy wywołania interfejsu API protokołu HTTP są zarządzane i przesyłane za pośrednictwem usługi Azure API Management, jak pokazano na poniższym diagramie:

![Service Fabric z usługą Azure API Management Topology — Omówienie][sf-apim-web-app]

## <a name="application-scenarios"></a>Scenariusze aplikacji

Usługi w Service Fabric mogą być bezstanowe lub bezstanowe i mogą być partycjonowane przy użyciu jednego z trzech schematów: singleton, int-64 zakres i o nazwie. Rozwiązanie punktu końcowego usługi wymaga zidentyfikowania określonej partycji określonego wystąpienia usługi. Podczas rozpoznawania punktu końcowego usługi należy określić zarówno nazwę wystąpienia usługi (na przykład `fabric:/myapp/myservice`) jak i konkretną partycję usługi, z wyjątkiem sytuacji, w której jest wymagana partycja singleton.

Usługa Azure API Management może być używana z dowolną kombinacją usług bezstanowych, usług stanowych i dowolnego schematu partycjonowania.

## <a name="send-traffic-to-a-stateless-service"></a>Wyślij ruch do usługi bezstanowej

W najprostszym przypadku ruch jest przekazywany do wystąpienia usługi bezstanowej. Aby to osiągnąć, API Management operacja zawiera zasady przetwarzania przychodzącego z Service Fabric zaplecza, które mapuje do określonego wystąpienia usługi bezstanowej w Service Fabric zaplecza. Żądania wysyłane do tej usługi są wysyłane do losowego wystąpienia usługi.

**Przykład**

W poniższym scenariuszu aplikacja Service Fabric zawiera usługę bezstanową o nazwie `fabric:/app/fooservice`, która uwidacznia wewnętrzny interfejs API protokołu HTTP. Nazwa wystąpienia usługi jest dobrze znana i może być zakodowana bezpośrednio w API Management zasad przetwarzania przychodzącego. 

![Service Fabric z usługą Azure API Management Topology — Omówienie][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Wysyłanie ruchu do usługi stanowej

Podobnie jak w przypadku scenariusza usługi bezstanowej, ruch może być przekazywany do wystąpienia usługi stanowej. W takim przypadku operacja API Management zawiera zasady przetwarzania przychodzącego z Service Fabric zaplecza, które mapuje żądanie na określoną partycję określonego wystąpienia usługi *stanowej* . Partycja, do której są mapowane każde żądanie, jest obliczana za pośrednictwem metody lambda przy użyciu danych wejściowych z przychodzącego żądania HTTP, takiego jak wartość w ścieżce URL. Zasady można skonfigurować do wysyłania żądań tylko do repliki podstawowej lub do losowej repliki dla operacji odczytu.

**Przykład**

W poniższym scenariuszu aplikacja Service Fabric zawiera podzielonej usługi stanowej o nazwie `fabric:/app/userservice`, która uwidacznia wewnętrzny interfejs API protokołu HTTP. Nazwa wystąpienia usługi jest dobrze znana i może być zakodowana bezpośrednio w API Management zasad przetwarzania przychodzącego.  

Usługa jest partycjonowana przy użyciu schematu partycji Int64 z dwiema partycjami i zakresem kluczy, który obejmuje `Int64.MinValue` `Int64.MaxValue`. Zasady zaplecza obliczają klucz partycji w tym zakresie przez przekonwertowanie wartości `id` podanej w ścieżce żądania adresu URL na 64-bitową liczbę całkowitą, chociaż w tym celu można użyć dowolnego algorytmu w celu obliczenia klucza partycji. 

![Service Fabric z usługą Azure API Management Topology — Omówienie][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Wysyłanie ruchu do wielu usług bezstanowych

W bardziej zaawansowanych scenariuszach można zdefiniować operację API Management, która mapuje żądania do więcej niż jednego wystąpienia usługi. W takim przypadku każda operacja zawiera zasady, które mapują żądania do określonego wystąpienia usługi na podstawie wartości z przychodzącego żądania HTTP, takiego jak ścieżka URL lub ciąg zapytania, a w przypadku usług stanowych, partycji w ramach wystąpienia usługi.

Aby to osiągnąć, API Management operacja zawiera zasady przetwarzania przychodzącego z Service Fabric zaplecza, które mapuje do bezstanowego wystąpienia usługi w Service Fabric zaplecza na podstawie wartości pobranych z przychodzącego żądania HTTP. Żądania do usługi są wysyłane do przypadkowego wystąpienia usługi.

**Przykład**

W tym przykładzie nowe wystąpienie usługi bezstanowej jest tworzone dla każdego użytkownika aplikacji z dynamicznie wygenerowaną nazwą przy użyciu następującej formuły:

- `fabric:/app/users/<username>`

  Każda usługa ma unikatową nazwę, ale nazwy nie są znane z góry, ponieważ usługi są tworzone w odpowiedzi na dane wejściowe użytkownika lub administratora i dlatego nie mogą być zakodowane w zasadach APIM lub regułach routingu. Zamiast tego nazwa usługi, do której należy wysłać żądanie, jest generowana w definicji zasad zaplecza z wartości `name` podanej w ścieżce żądania adresu URL. Przykład:

  - Żądanie `/api/users/foo` jest kierowane do wystąpienia usługi `fabric:/app/users/foo`
  - Żądanie `/api/users/bar` jest kierowane do wystąpienia usługi `fabric:/app/users/bar`

![Service Fabric z usługą Azure API Management Topology — Omówienie][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Wysyłanie ruchu do wielu usług stanowych

Podobnie jak w przypadku przykładowej usługi bezstanowej, operacja API Management może mapować żądania do więcej niż jednego wystąpienia usługi **stanowej** . w takim przypadku może być również konieczne przeprowadzenie rozpoznawania partycji dla każdego wystąpienia usługi stanowej.

Aby to osiągnąć, API Management operacja zawiera zasady przetwarzania przychodzącego z Service Fabric zaplecza, które mapuje do wystąpienia usługi stanowej w Service Fabric zaplecza na podstawie wartości pobranych z przychodzącego żądania HTTP. Oprócz mapowania żądania do określonego wystąpienia usługi żądanie może być również mapowane na określoną partycję w ramach wystąpienia usługi i opcjonalnie na replikę podstawową lub losowo replikę pomocniczą w ramach partycji.

**Przykład**

W tym przykładzie tworzone jest nowe wystąpienie usługi stanowej dla każdego użytkownika aplikacji z dynamicznie wygenerowaną nazwą przy użyciu następującej formuły:

- `fabric:/app/users/<username>`

  Każda usługa ma unikatową nazwę, ale nazwy nie są znane z góry, ponieważ usługi są tworzone w odpowiedzi na dane wejściowe użytkownika lub administratora i dlatego nie mogą być zakodowane w zasadach APIM lub regułach routingu. Zamiast tego nazwa usługi, do której należy wysłać żądanie, jest generowana w definicji zasad zaplecza z wartości `name` podanej w ścieżce żądania adresu URL. Przykład:

  - Żądanie `/api/users/foo` jest kierowane do wystąpienia usługi `fabric:/app/users/foo`
  - Żądanie `/api/users/bar` jest kierowane do wystąpienia usługi `fabric:/app/users/bar`

Każde wystąpienie usługi jest również podzielone na partycje przy użyciu schematu partycji Int64 z dwiema partycjami i zakresem kluczy, który obejmuje `Int64.MinValue` do `Int64.MaxValue`. Zasady zaplecza obliczają klucz partycji w tym zakresie przez przekonwertowanie wartości `id` podanej w ścieżce żądania adresu URL na 64-bitową liczbę całkowitą, chociaż w tym celu można użyć dowolnego algorytmu w celu obliczenia klucza partycji. 

![Service Fabric z usługą Azure API Management Topology — Omówienie][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z [samouczkiem](service-fabric-tutorial-deploy-api-management.md) , aby skonfigurować pierwszy klaster Service Fabric z API Management i przepływać żądania przez API Management do usług.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png
