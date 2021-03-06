---
title: Azure Event Grid powiązanie danych wyjściowych dla Azure Functions
description: Dowiedz się, jak wysłać Zdarzenie Event Grid w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: e7a2611312ffc33703dd5cc9d0a2d7142ddb0532
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368950"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Azure Event Grid powiązanie danych wyjściowych dla Azure Functions

Użyj powiązania danych wyjściowych Event Grid do zapisywania zdarzeń w temacie niestandardowym. Musisz mieć prawidłowy [klucz dostępu dla tematu niestandardowego](../event-grid/security-authentication.md#custom-topic-publishing).

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](./functions-bindings-event-grid.md).

> [!NOTE]
> Powiązanie danych wyjściowych Event Grid nie obsługuje sygnatur dostępu współdzielonego (tokeny SAS). Musisz użyć klucza dostępu tematu.

> [!IMPORTANT]
> Powiązanie danych wyjściowych Event Grid jest dostępne tylko dla funkcji 2. x i wyższych.

## <a name="example"></a>Przykład

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

W poniższym przykładzie pokazano [ C# funkcję](functions-dotnet-class-library.md) , która zapisuje komunikat do Event Grid niestandardowego tematu przy użyciu metody zwracanej wartości jako dane wyjściowe:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

W poniższym przykładzie pokazano, jak za pomocą interfejsu `IAsyncCollector` wysyłać partie komunikatów.

```csharp
[FunctionName("EventGridAsyncOutput")]
public static async Task Run(
    [TimerTrigger("0 */5 * * * *")] TimerInfo myTimer,
    [EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]IAsyncCollector<EventGridEvent> outputEvents,
    ILogger log)
{
    for (var i = 0; i < 3; i++)
    {
        var myEvent = new EventGridEvent("message-id-" + i, "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
        await outputEvents.AddAsync(myEvent);
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

Poniższy przykład przedstawia dane wyjściowe powiązania Event Grid w pliku *Function. JSON* .

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Kod C# skryptu, który tworzy jedno zdarzenie:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out EventGridEvent outputEvent, ILogger log)
{
    outputEvent = new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

Kod C# skryptu, który tworzy wiele zdarzeń:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ICollector<EventGridEvent> outputEvent, ILogger log)
{
    outputEvent.Add(new EventGridEvent("message-id-1", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
    outputEvent.Add(new EventGridEvent("message-id-2", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Poniższy przykład przedstawia dane wyjściowe powiązania Event Grid w pliku *Function. JSON* .

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Oto kod JavaScript, który tworzy pojedyncze zdarzenie:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = {
        id: 'message-id',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    };
    context.done();
};
```

Oto kod JavaScript, który tworzy wiele zdarzeń:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = [];

    context.bindings.outputEvent.push({
        id: 'message-id-1',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.bindings.outputEvent.push({
        id: 'message-id-2',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Powiązanie danych wyjściowych Event Grid nie jest dostępne dla języka Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Powiązanie danych wyjściowych Event Grid nie jest dostępne dla języka Java.

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

W przypadku [ C# bibliotek klas](functions-dotnet-class-library.md)Użyj atrybutu [EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs) .

Konstruktor atrybutu przyjmuje nazwę ustawienia aplikacji, która zawiera nazwę tematu niestandardowego i nazwę ustawienia aplikacji, która zawiera klucz tematu. Aby uzyskać więcej informacji na temat tych ustawień, zobacz [Output-Configuration](#configuration). Oto przykład `EventGrid` atrybutu:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Aby zapoznać się z pełnym przykładem, zobacz [przykład](#example).

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

Atrybuty nie są obsługiwane przez C# skrypt.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Powiązanie danych wyjściowych Event Grid nie jest dostępne dla języka Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Powiązanie danych wyjściowych Event Grid nie jest dostępne dla języka Java.

---

## <a name="configuration"></a>Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań ustawiane w pliku *Function. JSON* i `EventGrid` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | Musi być ustawiona na wartość "eventGrid". |
|**direction** | Nie dotyczy | Musi być równa "out". Ten parametr jest ustawiany automatycznie podczas tworzenia powiązania w Azure Portal. |
|**Nazwij** | Nie dotyczy | Nazwa zmiennej używana w kodzie funkcji, która reprezentuje zdarzenie. |
|**topicEndpointUri** |**TopicEndpointUri** | Nazwa ustawienia aplikacji, która zawiera identyfikator URI tematu niestandardowego, na przykład `MyTopicEndpointUri`. |
|**topicKeySetting** |**TopicKeySetting** | Nazwa ustawienia aplikacji, która zawiera klucz dostępu dla tematu niestandardowego. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Upewnij się, że wartość właściwości konfiguracja `TopicEndpointUri` jest ustawiona na nazwę ustawienia aplikacji, która zawiera identyfikator URI tematu niestandardowego. Nie określaj identyfikatora URI tematu niestandardowego bezpośrednio w tej właściwości.

## <a name="usage"></a>Sposób użycia

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Wysyłaj komunikaty przy użyciu parametru metody, takiego jak `out EventGridEvent paramName`. Aby pisać wiele komunikatów, można użyć `ICollector<EventGridEvent>` lub `IAsyncCollector<EventGridEvent>` zamiast `out EventGridEvent`.

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

Wysyłaj komunikaty przy użyciu parametru metody, takiego jak `out EventGridEvent paramName`. W C# skrypcie `paramName` jest wartością określoną we właściwości `name` *funkcji Function. JSON*. Aby pisać wiele komunikatów, można użyć `ICollector<EventGridEvent>` lub `IAsyncCollector<EventGridEvent>` zamiast `out EventGridEvent`.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Dostęp do zdarzenia wyjściowego przy użyciu `context.bindings.<name>`, gdzie `<name>` jest wartością określoną we właściwości `name` *funkcji Function. JSON*.

# <a name="pythontabpython"></a>[Python](#tab/python)

Powiązanie danych wyjściowych Event Grid nie jest dostępne dla języka Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Powiązanie danych wyjściowych Event Grid nie jest dostępne dla języka Java.

---

## <a name="next-steps"></a>Następne kroki

* [Wysyłanie zdarzenia Event Grid](./functions-bindings-event-grid-trigger.md)
