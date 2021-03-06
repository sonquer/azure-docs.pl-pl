---
title: Omówienie rozszerzenia Durable Functions — Azure
description: Wprowadzenie do rozszerzenia Durable Functions dla usługi Azure Functions.
author: cgillum
ms.topic: overview
ms.date: 08/07/2019
ms.author: cgillum
ms.reviewer: azfuncdf
ms.openlocfilehash: 5d454aefaba89bef9dc9009ff442fa5543dae2ef
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76756147"
---
# <a name="what-are-durable-functions"></a>Co to jest Durable Functions?

*Durable Functions* jest rozszerzeniem [Azure Functions](../functions-overview.md) , które pozwala pisać funkcje stanowe w środowisku obliczeniowym bezserwerowym. Rozszerzenie pozwala definiować stanowe przepływy pracy, pisząc [*funkcje programu Orchestrator*](durable-functions-orchestrations.md) i jednostki stanowe, pisząc [*funkcje jednostki*](durable-functions-entities.md) przy użyciu Azure Functions modelu programowania. W tle rozszerzenie zarządza stanem, punktami kontrolnymi i ponownymi uruchomieniami, co pozwala skupić się na logice biznesowej.

## <a name="language-support"></a>Obsługiwane języki

Rozszerzenie Durable Functions obsługuje obecnie następujące języki:

* **C#** : zarówno [prekompilowane biblioteki klas](../functions-dotnet-class-library.md), jak i [skrypt języka C#](../functions-reference-csharp.md).
* **JavaScript**: obsługiwany tylko w przypadku wersji 2.x środowiska uruchomieniowego usługi Azure Functions. Wymaga rozszerzenia Durable Functions w wersji 1.7.0 lub nowszej. 
* **F#** : prekompilowane biblioteki klas i skrypt języka F#. Skrypt języka F# jest obsługiwany tylko w przypadku wersji 1.x środowiska uruchomieniowego usługi Azure Functions.

Docelowo rozszerzenie Durable Functions ma obsługiwać wszystkie [języki obsługiwane w usłudze Azure Functions](../supported-languages.md). Zobacz [listę problemów z rozszerzeniem Durable Functions](https://github.com/Azure/azure-functions-durable-extension/issues), aby poznać aktualny stan prac nad obsługą dodatkowych języków.

Podobnie jak Azure Functions, istnieją szablony ułatwiające tworzenie Durable Functions przy użyciu [programu Visual Studio 2019](durable-functions-create-first-csharp.md), [Visual Studio Code](quickstart-js-vscode.md)i [Azure Portal](durable-functions-create-portal.md).

## <a name="application-patterns"></a>Wzorce aplikacji

Podstawowym zastosowaniem rozszerzenia Durable Functions jest uproszczenie złożonych wymagań związanych z koordynacją stanową w aplikacjach bezserwerowych. W poniższych sekcjach opisano typowe wzorce aplikacji, które mogą korzystać z Durable Functions:

* [Łańcuch funkcji](#chaining)
* [Model fan-out/fan-in](#fan-in-out)
* [Interfejsy API protokołu HTTP Async](#async-http)
* [Monitorowanie](#monitoring)
* [Interakcja z użytkownikami](#human)
* [Agregator (jednostki stanowe)](#aggregator)

### <a name="chaining"></a>#1 wzorca: łańcuch funkcji

W wzorcu łańcucha funkcji sekwencja funkcji jest wykonywana w określonej kolejności. W tym wzorcu dane wyjściowe jednej funkcji są stosowane do danych wejściowych innej funkcji.

![Diagram wzorca łańcucha funkcji](./media/durable-functions-concepts/function-chaining.png)

Można użyć Durable Functions do zaimplementowania wzorca łańcucha funkcji zwięzłie, jak pokazano w poniższym przykładzie.

W tym przykładzie wartości `F1`, `F2`, `F3`i `F4` są nazwami innych funkcji w tej samej aplikacji funkcji. Przepływ sterowania można zaimplementować przy użyciu zwykłych konstrukcji kodowania. Kod jest wykonywany z góry. Kod może dotyczyć istniejącej semantyki przepływu sterowania języka, takich jak warunkowe i pętle. Logikę obsługi błędów można uwzględnić w `try`/`catch`/`finally` bloków.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Chaining")]
public static async Task<object> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1", null);
        var y = await context.CallActivityAsync<object>("F2", x);
        var z = await context.CallActivityAsync<object>("F3", y);
        return  await context.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // Error handling or compensation goes here.
    }
}
```

Za pomocą parametru `context` można wywołać inne funkcje według nazwy, przekazywania parametrów i zwracanych danych wyjściowych funkcji. Za każdym razem, gdy kod wywołuje `await`, Durable Functions Framework punkty kontrolne postępu bieżącego wystąpienia funkcji. Jeśli proces lub maszyna wirtualna odzyskuje w połowie wykonywania, wystąpienie funkcji zostanie wznowione od poprzedniego wywołania `await`. Aby uzyskać więcej informacji, zobacz następną sekcję, wzorzec #2: wentylator/wentylator w.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    try {
        const x = yield context.df.callActivity("F1");
        const y = yield context.df.callActivity("F2", x);
        const z = yield context.df.callActivity("F3", y);
        return    yield context.df.callActivity("F4", z);
    } catch (error) {
        // Error handling or compensation goes here.
    }
});
```

Można użyć obiektu `context.df`, aby wywołać inne funkcje według nazwy, przekazywania parametrów i zwracanych danych wyjściowych funkcji. Za każdym razem, gdy kod wywołuje `yield`, Durable Functions Framework punkty kontrolne postępu bieżącego wystąpienia funkcji. Jeśli proces lub maszyna wirtualna odzyskuje w połowie wykonywania, wystąpienie funkcji zostanie wznowione od poprzedniego wywołania `yield`. Aby uzyskać więcej informacji, zobacz następną sekcję, wzorzec #2: wentylator/wentylator w.

> [!NOTE]
> Obiekt `context` w języku JavaScript reprezentuje cały [kontekst funkcji](../functions-reference-node.md#context-object). Uzyskaj dostęp do kontekstu Durable Functions przy użyciu właściwości `df` w kontekście głównym.

---

### <a name="fan-in-out"></a>#2 wzorca: wentylator/wentylator w

W wzorcu wentylator/wentylator w wzorcem wykonujesz równolegle wiele funkcji, a następnie poczekaj na zakończenie wszystkich funkcji. Często pewne zadania agregacji są wykonywane na wynikach, które są zwracane przez funkcje.

![Diagram z wzorcem wentylator/wentylator](./media/durable-functions-concepts/fan-out-fan-in.png)

Dzięki normalnym funkcjom można wyrównać, że funkcja wysyła wiele komunikatów do kolejki. Fanning z powrotem w programie jest znacznie bardziej trudne. Aby obwentylatorować, w normalnej funkcji napiszesz kod do śledzenia, gdy zakończy się funkcje wyzwalane przez kolejkę, a następnie przechowują dane wyjściowe funkcji.

Rozszerzenie Durable Functions obsługuje ten wzorzec z stosunkowo prostym kodem:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("FanOutFanIn")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1", null);
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

Wentylator-out Work jest dystrybuowany do wielu wystąpień funkcji `F2`. Zadanie jest śledzone przy użyciu dynamicznej listy zadań. `Task.WhenAll` jest wywoływana, aby poczekać na zakończenie wszystkich wywoływanych funkcji. Następnie dane wyjściowe funkcji `F2` są agregowane z listy zadań dynamicznych i przesyłane do funkcji `F3`.

Automatyczne tworzenie punktów kontrolnych, które odbywa się w wywołaniu `await` na `Task.WhenAll` zapewnia, że potencjalne awarie w Midway lub ponowny rozruch nie wymagają ponownego uruchomienia już wykonanego zadania.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // Get a list of N work items to process in parallel.
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

Wentylator-out Work jest dystrybuowany do wielu wystąpień funkcji `F2`. Zadanie jest śledzone przy użyciu dynamicznej listy zadań. Interfejs API `context.df.Task.all` jest wywoływany, aby poczekać na zakończenie wszystkich wywoływanych funkcji. Następnie dane wyjściowe funkcji `F2` są agregowane z listy zadań dynamicznych i przesyłane do funkcji `F3`.

Automatyczne tworzenie punktów kontrolnych, które odbywa się w wywołaniu `yield` na `context.df.Task.all` zapewnia, że potencjalne awarie w Midway lub ponowny rozruch nie wymagają ponownego uruchomienia już wykonanego zadania.

---

> [!NOTE]
> W rzadkich przypadkach może wystąpić awaria w oknie po zakończeniu funkcji działania, ale przed jej ukończeniem zostanie zapisana w historii aranżacji. W takim przypadku funkcja działania zostanie ponownie uruchomiona od początku po zakończeniu procesu odzyskiwania.

### <a name="async-http"></a>#3 wzorca: asynchroniczne interfejsy API protokołu HTTP

Wzorzec asynchronicznego interfejsu API protokołu HTTP rozwiązuje problem związany z uzgadnianiem stanu długotrwałych operacji z klientami zewnętrznymi. Typowym sposobem implementacji tego wzorca jest posiadanie, że punkt końcowy HTTP wyzwala długotrwałą akcję. Następnie Przekieruj klienta do punktu końcowego stanu, który będzie sondował klient, aby dowiedzieć się, kiedy operacja zostanie zakończona.

![Diagram wzorca interfejsu API protokołu HTTP](./media/durable-functions-concepts/async-http-api.png)

Durable Functions zapewnia **wbudowaną obsługę** tego wzorca, upraszczając lub nawet usuwając kod potrzebny do zapisu w celu współdziałania z długotrwałymi wykonaniami funkcji. Przykładowo przykłady szybkiego startu Durable Functions ([C#](durable-functions-create-first-csharp.md) i [JavaScript](quickstart-js-vscode.md)) pokazują proste polecenie REST, za pomocą którego można uruchamiać nowe wystąpienia funkcji programu Orchestrator. Po rozpoczęciu wystąpienia rozszerzenie uwidacznia interfejsy API HTTP elementu webhook, które wykonują zapytania o stan funkcji programu Orchestrator. 

Poniższy przykład przedstawia polecenia REST, które uruchamiają koordynatora i badają jego stan. Dla jasności niektóre szczegóły protokołu zostały pominięte w przykładzie.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2019-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2019-03-16T21:20:57Z", ...}
```

Ponieważ środowisko uruchomieniowe Durable Functions zarządza przez Ciebie stanem, nie trzeba implementować własnego mechanizmu śledzenia stanu.

Rozszerzenie Durable Functions udostępnia wbudowane interfejsy API protokołu HTTP, które zarządzają długotrwałymi aranżacjami. Możesz Alternatywnie zaimplementować ten wzorzec przy użyciu własnych wyzwalaczy funkcji (takich jak HTTP, Queue lub Azure Event Hubs) i [powiązania klienta aranżacji](durable-functions-bindings.md#orchestration-client). Na przykład możesz użyć komunikatu kolejki, aby wyzwolić zakończenie. Lub można użyć wyzwalacza HTTP, który jest chroniony przez zasady uwierzytelniania Azure Active Directory zamiast wbudowanych interfejsów API protokołu HTTP, które używają wygenerowanego klucza do uwierzytelniania.

Aby uzyskać więcej informacji, zobacz artykuł [funkcje http](durable-functions-http-features.md) , w którym wyjaśniono, jak można uwidocznić asynchroniczne, długotrwałe procesy za pośrednictwem protokołu HTTP przy użyciu rozszerzenia Durable Functions.

### <a name="monitoring"></a>#4 wzorca: Monitor

Wzorzec monitora odnosi się do elastycznego, cyklicznego procesu w przepływie pracy. Przykład jest sondowany, dopóki nie zostaną spełnione określone warunki. Można użyć [wyzwalacza regularnego czasomierza](../functions-bindings-timer.md) , aby zająć się podstawowym scenariuszem, takim jak okresowe zadanie oczyszczania, ale jego interwał jest statyczny i zarządzanie okresami istnienia wystąpienia stanie się skomplikowany. Za pomocą Durable Functions można tworzyć Elastyczne interwały cykliczne, zarządzać okresami istnienia zadań oraz tworzyć wiele procesów monitorowania z jednej aranżacji.

Przykładem wzorca monitora jest odwrócenie wcześniejszego scenariusza asynchronicznego interfejsu API HTTP. Zamiast uwidaczniać punkt końcowy dla klienta zewnętrznego do monitorowania długotrwałej operacji, długotrwały monitor zużywa zewnętrzny punkt końcowy, a następnie czeka na zmianę stanu.

![Diagram wzorca monitora](./media/durable-functions-concepts/monitor.png)

W kilku wierszach kodu można użyć Durable Functions, aby utworzyć wiele monitorów, które obserwują dowolne punkty końcowe. Monitory mogą kończyć wykonywanie, gdy spełniony jest warunek lub inna funkcja może użyć klienta nietrwałej aranżacji do zakończenia monitorów. Można zmienić interwał `wait` monitora na podstawie określonego warunku (na przykład wykładniczy wycofywania). 

Poniższy kod implementuje podstawowy Monitor:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("MonitorJobStatus")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int jobId = context.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();

    while (context.CurrentUtcDateTime < expiryTime)
    {
        var jobStatus = await context.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform an action when a condition is met.
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform more work here, or let the orchestration end.
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInterval = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform an action when a condition is met.
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform more work here, or let the orchestration end.
});
```

---

Po odebraniu żądania dla tego identyfikatora zadania zostanie utworzone nowe wystąpienie aranżacji. Wystąpienie sonduje stan do momentu spełnienia warunku, a pętla zostanie zakończona. Trwały czasomierz steruje interwałem sondowania. Następnie można wykonać więcej pracy lub zorganizować. Gdy `nextCheck` przekroczy `expiryTime`, Monitor zostanie zakończony.

### <a name="human"></a>#5 wzorca: interakcja ludzka

Wiele zautomatyzowanych procesów dotyczy pewnego rodzaju interakcji przez człowieka. W przypadku ludzi w zautomatyzowanym procesie jest to trudne, ponieważ ludzie nie są w pełni dostępni i jako usługi w chmurze. Zautomatyzowany proces może pozwolić na interakcję przy użyciu limitów czasu i logiki kompensacji.

Proces zatwierdzania jest przykładem procesu biznesowego, który obejmuje interakcje człowieka. Dla raportu wydatków, który przekracza określoną kwotę dolara, może być wymagane zatwierdzenie przez Menedżera. Jeśli Menedżer nie zatwierdzi raportu wydatków w ciągu 72 godzin (być może Menedżer przeszedł do urlopu), proces eskalacji zostanie rozpoczęty w celu uzyskania zatwierdzenia od kogoś innego (być może menedżerem).

![Diagram wzorca interakcji człowieka](./media/durable-functions-concepts/approval.png)

Wzorzec można zaimplementować w tym przykładzie przy użyciu funkcji programu Orchestrator. Program Orchestrator używa [trwałego czasomierza](durable-functions-timers.md) , aby zażądać zatwierdzenia. Koordynator zostanie przekazany w przypadku wystąpienia limitu czasu. Koordynator czeka na [zdarzenie zewnętrzne](durable-functions-external-events.md), takie jak powiadomienie, które zostało wygenerowane przez interakcję przez człowieka.

Te przykłady umożliwiają utworzenie procesu zatwierdzania w celu zademonstrowania wzorca interakcji człowieka:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ApprovalWorkflow")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval", null);
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = context.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = context.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = context.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await context.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await context.CallActivityAsync("Escalate", null);
        }
    }
}
```

Aby utworzyć trwały czasomierz, wywołaj `context.CreateTimer`. Powiadomienie jest odbierane przez `context.WaitForExternalEvent`. Następnie `Task.WhenAny` jest wywoływana, aby zdecydować, czy należy eskalować (przekroczenie limitu czasu) lub przetworzyć zatwierdzenie (zatwierdzenie zostanie odebrane przed upływem limitu czasu).

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require('moment');

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("RequestApproval");

    const dueTime = moment.utc(context.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = context.df.createTimer(dueTime.toDate());

    const approvalEvent = context.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield context.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield context.df.callActivity("ProcessApproval", approvalEvent.result);
    } else {
        yield context.df.callActivity("Escalate");
    }
});
```

Aby utworzyć trwały czasomierz, wywołaj `context.df.createTimer`. Powiadomienie jest odbierane przez `context.df.waitForExternalEvent`. Następnie `context.df.Task.any` jest wywoływana, aby zdecydować, czy należy eskalować (przekroczenie limitu czasu) lub przetworzyć zatwierdzenie (zatwierdzenie zostanie odebrane przed upływem limitu czasu).

---

Klient zewnętrzny może dostarczyć powiadomienie o zdarzeniu do oczekującej funkcji programu Orchestrator przy użyciu [wbudowanych interfejsów API protokołu HTTP](durable-functions-http-api.md#raise-event):

```bash
curl -d "true" http://localhost:7071/runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/ApprovalEvent -H "Content-Type: application/json"
```

Zdarzenie może być również zgłaszane przy użyciu nietrwałego klienta aranżacji z innej funkcji w tej samej aplikacji funkcji:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEventToOrchestration")]
public static async Task Run(
    [HttpTrigger] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

---

### <a name="aggregator"></a>Wzorzec #6: agregator (jednostki stanowe)

Szósty wzorzec polega na agregowaniu danych zdarzeń w danym okresie do pojedynczej, adresowanej *jednostki*. W tym wzorcu dane agregowane mogą pochodzić z wielu źródeł, mogą być dostarczane w partiach lub mogą być rozproszone w długim okresie czasu. Agregator może wymagać podjęcia działania względem danych zdarzenia w miarę ich nadejścia, a klienci zewnętrzni mogą potrzebować zapytania do zagregowanych danych.

![Diagram agregatora](./media/durable-functions-concepts/aggregator.png)

W celu zaimplementowania tego wzorca przy użyciu normalnych, bezstanowych funkcji Kontrola współbieżności jest bardzo trudne. Nie tylko trzeba martwić się o wiele wątków modyfikujących te same dane w tym samym czasie, należy również pamiętać o zapewnieniu, że agregator działa tylko na jednej maszynie wirtualnej naraz.

Za pomocą [jednostek trwałych](durable-functions-entities.md) można łatwo zaimplementować ten wzorzec jako pojedynczą funkcję.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            ctx.SetState(currentValue + amount);
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }
}
```

Trwałe jednostki mogą być również modelowane jako klasy w programie .NET. Ten model może być przydatny, jeśli lista operacji jest stała i będzie duża. Poniższy przykład jest równoważną implementacją jednostki `Counter` przy użyciu klas i metod platformy .NET.

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

---

Klienci mogą umieścić w kolejce *operacje* dla (zwane także "sygnalizacją") funkcji jednostki przy użyciu [powiązania klienta jednostki](durable-functions-bindings.md#entity-client).

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [DurableClient] IDurableOrchestrationClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

> [!NOTE]
> Dynamicznie generowane serwery proxy są również dostępne w programie .NET do sygnalizowania jednostek w sposób bezpieczny dla typów. Oprócz sygnalizowania klienci mogą również wysyłać zapytania dotyczące stanu funkcji jednostki przy użyciu [metod bezpiecznych typu](durable-functions-bindings.md#entity-client-usage) dla powiązania klienta aranżacji.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

---

Funkcje jednostki są dostępne w [Durable Functions 2,0](durable-functions-versions.md) i nowszych.

## <a name="the-technology"></a>Technologia

W tle rozszerzenie Durable Functions jest tworzone na podstawie [trwałej struktury zadań](https://github.com/Azure/durabletask), biblioteki typu open source w usłudze GitHub, która jest używana do tworzenia przepływów pracy w kodzie. Podobnie jak Azure Functions jest ewolucją bezserwerową Azure WebJobs, Durable Functions jest bezserwerowym ewolucją struktury zadań trwałych. Firma Microsoft i inne organizacje często wykorzystują strukturę zadań trwałych w celu zautomatyzowania procesów o kluczowym znaczeniu. Jest to naturalne dopasowanie do bezserwerowego środowiska Azure Functionsowego.

## <a name="code-constraints"></a>Ograniczenia kodu

Aby zapewnić niezawodne i długotrwałe gwarancje wykonywania, funkcje programu Orchestrator mają zestaw reguł kodowania, które muszą być spełnione. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [ograniczeń kodu funkcji programu Orchestrator](durable-functions-code-constraints.md) .

## <a name="billing"></a>Rozliczenia

Opłaty za rozszerzenie Durable Functions są naliczane tak samo, jak w przypadku usługi Azure Functions. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Functions](https://azure.microsoft.com/pricing/details/functions/). Podczas wykonywania funkcji programu Orchestrator w [planie zużycia](../functions-scale.md#consumption-plan)Azure Functions należy pamiętać o rozliczeniach. Aby uzyskać więcej informacji na temat tych zachowań, zobacz artykuł dotyczący [rozliczeń Durable Functions](durable-functions-billing.md) .

## <a name="jump-right-in"></a>Błyskawicznie rozpocznij pracę

Ukończ jeden z tych samouczków Szybki start dotyczących poszczególnych języków, aby rozpocząć korzystanie z rozszerzenia Durable Functions w niecałe 10 minut:

* [C#Korzystanie z programu Visual Studio 2019](durable-functions-create-first-csharp.md)
* [JavaScript w programie Visual Studio Code](quickstart-js-vscode.md)

W obu przewodnikach Szybki start utworzysz lokalnie i przetestujesz funkcję trwałą „hello world”. Kod funkcji zostanie następnie opublikowany na platformie Azure. Utworzona przez Ciebie funkcja aranżuje i łączy w łańcuchy wywołania do innych funkcji.

## <a name="learn-more"></a>Dowiedz się więcej

Poniższy klip wideo prezentuje zalety rozszerzenia Durable Functions:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Aby zapoznać się z bardziej szczegółowym omówieniem Durable Functions i podstawowej technologii, zobacz następujący film wideo (koncentruje się na platformie .NET, ale koncepcje dotyczą także innych obsługiwanych języków):

> [!VIDEO https://channel9.msdn.com/Events/dotnetConf/2018/S204/player]

Ponieważ Durable Functions to zaawansowane rozszerzenie usługi [Azure Functions](../functions-overview.md), nie jest odpowiednie dla wszystkich aplikacji. Aby zapoznać się z porównaniem technologii orkiestracji dostępnych na platformie Azure, zobacz [Porównanie usług Azure Functions i Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Durable Functions funkcje i typy funkcji](durable-functions-types-features-overview.md)
