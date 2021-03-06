---
title: Skalowanie z wieloma wystąpieniami — usługa Azure Signal Service
description: W wielu scenariuszach skalowania klient często musi zainicjować obsługę wielu wystąpień i skonfigurować go do korzystania ze sobą, aby utworzyć wdrożenie na dużą skalę. Na przykład fragmentowania wymaga obsługi wielu wystąpień.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: 43d703312cbc1fc067a2d51d5623ed028ba01405
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158157"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Jak skalować usługę sygnalizującą z wieloma wystąpieniami?
Najnowsza wersja zestawu SDK usługi sygnalizującej obsługuje wiele punktów końcowych dla wystąpień usługi sygnalizującej. Za pomocą tej funkcji można skalować współbieżne połączenia lub używać ich do obsługi komunikatów między regionami.

## <a name="for-aspnet-core"></a>Dla ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>Jak dodać wiele punktów końcowych z konfiguracji?

Konfiguracja z kluczem `Azure:SignalR:ConnectionString` lub `Azure:SignalR:ConnectionString:` parametrów połączenia usługi sygnalizującego.

Jeśli klucz rozpoczyna się od `Azure:SignalR:ConnectionString:`, powinien mieć format `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`, gdzie `Name` i `EndpointType` są właściwościami obiektu `ServiceEndpoint` i są dostępne z kodu.

Za pomocą następujących poleceń `dotnet` można dodać wiele parametrów połączenia wystąpienia:

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Jak dodać wiele punktów końcowych z kodu?

Klasa `ServicEndpoint` została wprowadzona w celu opisania właściwości punktu końcowego usługi Azure Signal Service.
Można skonfigurować wiele punktów końcowych wystąpienia podczas korzystania z zestawu SDK usługi Azure Signal:
```cs
services.AddSignalR()
        .AddAzureSignalR(options => 
        {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString0>"),
                new ServiceEndpoint("<ConnectionString1>", type: EndpointType.Primary, name: "east-region-a"),
                new ServiceEndpoint("<ConnectionString2>", type: EndpointType.Primary, name: "east-region-b"),
                new ServiceEndpoint("<ConnectionString3>", type: EndpointType.Secondary, name: "backup"),
            };
        });
```

### <a name="how-to-customize-endpoint-router"></a>Jak dostosować router Endpoint Protection?

Domyślnie zestaw SDK używa [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) do pobierania punktów końcowych.

#### <a name="default-behavior"></a>Zachowanie domyślne 
1. Routing żądań klienta

    Gdy klient `/negotiate` z serwerem aplikacji. Domyślnie zestaw SDK **losowo wybiera** jeden punkt końcowy z zestawu dostępnych punktów końcowych usługi.

2. Routing komunikatów serwera

    Gdy * wysyłanie komunikatu do określonego * * Connection * * *, a połączenie docelowe jest kierowane do bieżącego serwera, komunikat przechodzi bezpośrednio do tego połączonego punktu końcowego. W przeciwnym razie komunikaty są rozgłaszane do każdego punktu końcowego usługi Azure Signal.

#### <a name="customize-routing-algorithm"></a>Dostosuj algorytm routingu
Możesz utworzyć własny router, gdy masz specjalną wiedzę, aby określić punkty końcowe, do których powinny przejść komunikaty.

Poniżej określono router niestandardowy, gdy grupy rozpoczynające się od `east-` zawsze przejdą do punktu końcowego o nazwie `east`:

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override IEnumerable<ServiceEndpoint> GetEndpointsForGroup(string groupName, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the group broadcast behavior, if the group name starts with "east-", only send messages to endpoints inside east
        if (groupName.StartsWith("east-"))
        {
            return endpoints.Where(e => e.Name.StartsWith("east-"));
        }

        return base.GetEndpointsForGroup(groupName, endpoints);
    }
}
```

Inny przykład poniżej, który zastępuje domyślne zachowanie negocjowania, aby wybrać punkty końcowe, zależy od tego, gdzie znajduje się serwer aplikacji.

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(HttpContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (endpointName.Count == 0)
        {
            context.Response.StatusCode = 400;
            var response = Encoding.UTF8.GetBytes("Invalid request");
            context.Response.Body.Write(response, 0, response.Length);
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Nie zapomnij zarejestrować routera w programie przy użyciu:

```cs
services.AddSingleton(typeof(IEndpointRouter), typeof(CustomRouter));
services.AddSignalR()
        .AddAzureSignalR(
            options => 
            {
                options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
            });
```

## <a name="for-aspnet"></a>Dla ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>Jak dodać wiele punktów końcowych z konfiguracji?

Konfiguracja z kluczem `Azure:SignalR:ConnectionString` lub `Azure:SignalR:ConnectionString:` parametrów połączenia usługi sygnalizującego.

Jeśli klucz rozpoczyna się od `Azure:SignalR:ConnectionString:`, powinien mieć format `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`, gdzie `Name` i `EndpointType` są właściwościami obiektu `ServiceEndpoint` i są dostępne z kodu.

Do `web.config`można dodać wiele parametrów połączenia wystąpienia:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <connectionStrings>
    <add name="Azure:SignalR:ConnectionString" connectionString="<ConnectionString1>"/>
    <add name="Azure:SignalR:ConnectionString:en-us" connectionString="<ConnectionString2>"/>
    <add name="Azure:SignalR:ConnectionString:zh-cn:secondary" connectionString="<ConnectionString3>"/>
    <add name="Azure:SignalR:ConnectionString:Backup:secondary" connectionString="<ConnectionString4>"/>
  </connectionStrings>
  ...
</configuration>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Jak dodać wiele punktów końcowych z kodu?

Klasa `ServicEndpoint` została wprowadzona w celu opisania właściwości punktu końcowego usługi Azure Signal Service.
Można skonfigurować wiele punktów końcowych wystąpienia podczas korzystania z zestawu SDK usługi Azure Signal:

```cs
app.MapAzureSignalR(
    this.GetType().FullName, 
    options => {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString1>"),
                new ServiceEndpoint("<ConnectionString2>"),
                new ServiceEndpoint("<ConnectionString3>"),
            }
        });
```

### <a name="how-to-customize-router"></a>Jak dostosować router?

Jedyną różnicą między sygnalizującym ASP.NET i ASP.NET Core sygnalizującym jest typ kontekstu HTTP dla `GetNegotiateEndpoint`. Dla sygnalizującego ASP.NET, jest typu [IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) .

Poniżej znajduje się przykład niestandardowego negocjowania dla ASP.NET sygnalizującego:

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(IOwinContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (string.IsNullOrEmpty(endpointName))
        {
            context.Response.StatusCode = 400;
            context.Response.Write("Invalid request.");
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Nie zapomnij zarejestrować routera w programie przy użyciu:

```cs
var hub = new HubConfiguration();
var router = new CustomRouter();
hub.Resolver.Register(typeof(IEndpointRouter), () => router);
app.MapAzureSignalR(GetType().FullName, hub, options => {
    options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
});
```

## <a name="configuration-in-cross-region-scenarios"></a>Konfiguracja w scenariuszach obejmujących wiele regionów

Obiekt `ServiceEndpoint` ma właściwość `EndpointType` z wartością `primary` lub `secondary`.

punkty końcowe `primary` są preferowanymi punktami końcowymi do odbierania ruchu klienta i są uważane za mające bardziej niezawodne połączenia sieciowe; `secondary` punkty końcowe są uznawane za mniej niezawodne połączenia sieciowe i są używane tylko do przesyłania ruchu z serwera do klienta, na przykład rozgłaszania komunikatów, a nie do przełączania klientów do ruchu z serwera.

W przypadku różnych regionów, Sieć może być niestabilna. W przypadku jednego serwera aplikacji znajdującego się w regionie *Wschodnie stany USA*punkt końcowy usługi sygnalizującej znajdujący się w tym samym regionach *Wschodnie stany USA* można skonfigurować jako `primary` i punkty końcowe w innych regionach oznaczonych jako `secondary`. W tej konfiguracji punkty końcowe usługi w innych regionach mogą **odbierać** komunikaty z tego serwera aplikacji *Wschodnie stany USA* , **ale nie będzie** on kierowany do tego serwera aplikacji. Architektura jest pokazana na poniższym diagramie:

![Infrastruktura geograficzna](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

Gdy klient próbuje `/negotiate` z serwerem aplikacji, przy użyciu domyślnego routera zestaw SDK **losowo wybiera** jeden punkt końcowy z zestawu dostępnych `primary` punktów końcowych. Gdy podstawowy punkt końcowy nie jest dostępny, zestaw SDK **losowo wybiera** ze wszystkich dostępnych `secondary` punktów końcowych. Punkt końcowy jest oznaczony jako **dostępny** , gdy połączenie między serwerem a punktem końcowym usługi jest aktywne.

W scenariuszu obejmującym wiele regionów, gdy klient próbuje `/negotiate` przy użyciu serwera aplikacji hostowanego w regionie *Wschodnie stany USA*, domyślnie zawsze zwraca punkt końcowy `primary` znajdujący się w tym samym regionie. Gdy wszystkie punkty końcowe *Wschodnie stany USA* nie są dostępne, klient zostanie przekierowany do punktów końcowych w innych regionach. W poniższej sekcji poniżej opisano szczegółowo scenariusz.

![Normalne negocjowanie](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Tryb failover

Gdy wszystkie punkty końcowe `primary` są niedostępne, `/negotiate` klienta wybiera z dostępnych `secondary` punktów końcowych. Ten mechanizm pracy awaryjnej wymaga, aby każdy punkt końcowy działał jako punkt końcowy `primary` co najmniej jeden serwer aplikacji.

![Tryb failover](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku wyjaśniono, jak skonfigurować wiele wystąpień w tej samej aplikacji na potrzeby skalowania, fragmentowania i wieloregionowych scenariuszy.

Obsługa wielu punktów końcowych może być również używana w scenariuszach wysokiej dostępności i odzyskiwania po awarii.

> [!div class="nextstepaction"]
> [Usługa sygnalizująca konfigurację na potrzeby odzyskiwania po awarii i wysokiej dostępności](./signalr-concept-disaster-recovery.md)
