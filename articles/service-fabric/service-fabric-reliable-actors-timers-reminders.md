---
title: Reliable Actors czasomierze i przypomnienia
description: Wprowadzenie do czasomierzy i przypomnień dla Reliable Actors Service Fabric, w tym wskazówki dotyczące używania każdego z nich.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 02d6220b31ee9c991e8450759bf46759af6177a3
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639619"
---
# <a name="actor-timers-and-reminders"></a>Czasomierze aktora i przypomnienia
Aktory mogą zaplanować okresowe prace na siebie przez zarejestrowanie czasomierza lub przypomnień. W tym artykule przedstawiono sposób korzystania z czasomierzy i przypomnień oraz wyjaśniono różnice między nimi.

## <a name="actor-timers"></a>Czasomierze aktora
Czasomierze aktora zapewniają prostą otokę wokół czasomierza programu .NET lub języka Java, aby zapewnić, że metody wywołania zwrotnego uwzględniają gwarancje współbieżności oparte na włączeniu, które udostępnia aktory.

Aktory mogą używać metod `RegisterTimer`C#() lub `registerTimer`(Java) i `UnregisterTimer`C#() lub `unregisterTimer`(Java), aby rejestrować i wyrejestrować ich czasomierze. W poniższym przykładzie pokazano użycie interfejsów API czasomierza. Interfejsy API są bardzo podobne do czasomierza .NET lub czasomierza Java. W tym przykładzie, gdy czasomierz jest zaległy, środowisko uruchomieniowe aktorów wywoła metodę `MoveObject`C#() lub `moveObject`(Java). Metoda ma na celu uwzględnienie współbieżności opartej na włączeniu. Oznacza to, że żadne inne metody aktora lub wywołania zwrotne czasomierz/przypomnienie nie będą w toku do momentu zakończenia wykonywania tego wywołania zwrotnego.

```csharp
class VisualObjectActor : Actor, IVisualObject
{
    private IActorTimer _updateTimer;

    public VisualObjectActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    protected override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // Callback method
            null,                           // Parameter to pass to the callback method
            TimeSpan.FromMilliseconds(15),  // Amount of time to delay before the callback is invoked
            TimeSpan.FromMilliseconds(15)); // Time interval between invocations of the callback method

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
    {
        if (_updateTimer != null)
        {
            UnregisterTimer(_updateTimer);
        }

        return base.OnDeactivateAsync();
    }

    private Task MoveObject(object state)
    {
        ...
        return Task.FromResult(true);
    }
}
```
```Java
public class VisualObjectActorImpl extends FabricActor implements VisualObjectActor
{
    private ActorTimer updateTimer;

    public VisualObjectActorImpl(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    @Override
    protected CompletableFuture onActivateAsync()
    {
        ...

        return this.stateManager()
                .getOrAddStateAsync(
                        stateName,
                        VisualObject.createRandom(
                                this.getId().toString(),
                                new Random(this.getId().toString().hashCode())))
                .thenApply((r) -> {
                    this.registerTimer(
                            (o) -> this.moveObject(o),                        // Callback method
                            "moveObject",
                            null,                                             // Parameter to pass to the callback method
                            Duration.ofMillis(10),                            // Amount of time to delay before the callback is invoked
                            Duration.ofMillis(timerIntervalInMilliSeconds));  // Time interval between invocations of the callback method
                    return null;
                });
    }

    @Override
    protected CompletableFuture onDeactivateAsync()
    {
        if (updateTimer != null)
        {
            unregisterTimer(updateTimer);
        }

        return super.onDeactivateAsync();
    }

    private CompletableFuture moveObject(Object state)
    {
        ...
        return this.stateManager().getStateAsync(this.stateName).thenCompose(v -> {
            VisualObject v1 = (VisualObject)v;
            v1.move();
            return (CompletableFuture<?>)this.stateManager().setStateAsync(stateName, v1).
                    thenApply(r -> {
                      ...
                      return null;});
        });
    }
}
```

Następny okres czasomierza rozpocznie się po wykonaniu wywołania zwrotnego. Oznacza to, że czasomierz jest zatrzymany podczas wykonywania wywołania zwrotnego i uruchamiany po zakończeniu wywołania zwrotnego.

Środowisko uruchomieniowe aktorów zapisuje zmiany wprowadzone do menedżera stanu aktora po zakończeniu wywołania zwrotnego. Jeśli wystąpi błąd podczas zapisywania stanu, ten obiekt aktora zostanie zdezaktywowany i zostanie aktywowane nowe wystąpienie.

Wszystkie czasomierze są przerywane, gdy aktor zostanie zdezaktywowany w ramach odzyskiwania pamięci. Żadne wywołania zwrotne czasomierza nie są wywoływane po tym elemencie. Ponadto środowisko uruchomieniowe aktorów nie zachowuje żadnych informacji na temat czasomierzy, które były uruchomione przed dezaktywacją. Do aktora można rejestrować wszelkie czasomierze, których potrzebuje, gdy zostanie ona ponownie aktywowana w przyszłości. Aby uzyskać więcej informacji, zobacz sekcję dotyczącą [zbierania elementów bezużytecznych aktora](service-fabric-reliable-actors-lifecycle.md).

## <a name="actor-reminders"></a>Przypomnienia aktora
Przypomnienia są mechanizmem do wyzwalania trwałych wywołań zwrotnych w aktorze o określonych godzinach. Ich funkcje są podobne do czasomierzy. Ale w przeciwieństwie do czasomierzy, przypomnienia są wyzwalane w każdym przypadku, dopóki aktor jawnie wyrejestruje je lub aktor został jawnie usunięty. W każdym przypadku przypomnienia są wyzwalane przez dezaktywacje aktora i tryb failover, ponieważ środowisko wykonawcze aktorów utrzymuje informacje o przypomnieniach aktora przy użyciu dostawcy stanu aktora. Należy pamiętać, że niezawodność przypomnień jest związana z gwarancją niezawodności stanu zapewnioną przez dostawcę stanu aktora. Oznacza to, że dla uczestników, których stan trwałości jest ustawiony na None, przypomnienia nie będą wyzwalane po przejściu w tryb failover. 

Aby zarejestrować przypomnienie, aktor wywołuje metodę `RegisterReminderAsync` podaną w klasie bazowej, jak pokazano w następującym przykładzie:

```csharp
protected override async Task OnActivateAsync()
{
    string reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    IActorReminder reminderRegistration = await this.RegisterReminderAsync(
        reminderName,
        BitConverter.GetBytes(amountInDollars),
        TimeSpan.FromDays(3),    //The amount of time to delay before firing the reminder
        TimeSpan.FromDays(1));    //The time interval between firing of reminders
}
```

```Java
@Override
protected CompletableFuture onActivateAsync()
{
    String reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    ActorReminder reminderRegistration = this.registerReminderAsync(
            reminderName,
            state,
            dueTime,    //The amount of time to delay before firing the reminder
            period);    //The time interval between firing of reminders
}
```

W tym przykładzie `"Pay cell phone bill"` jest nazwą przypomnienia. Jest to ciąg, którego aktor używa do unikatowego identyfikowania przypomnienia. `BitConverter.GetBytes(amountInDollars)`(C#) to kontekst, który jest skojarzony z przypomnieniem. Zostanie ona przeniesiona z powrotem do aktora jako argument do wywołania zwrotnego przypomnienia, np. `IRemindable.ReceiveReminderAsync`C#() lub `Remindable.receiveReminderAsync`(Java).

Aktory korzystające z przypomnień muszą implementować interfejs `IRemindable`, jak pokazano w poniższym przykładzie.

```csharp
public class ToDoListActor : Actor, IToDoListActor, IRemindable
{
    public ToDoListActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task ReceiveReminderAsync(string reminderName, byte[] context, TimeSpan dueTime, TimeSpan period)
    {
        if (reminderName.Equals("Pay cell phone bill"))
        {
            int amountToPay = BitConverter.ToInt32(context, 0);
            System.Console.WriteLine("Please pay your cell phone bill of ${0}!", amountToPay);
        }
        return Task.FromResult(true);
    }
}
```
```Java
public class ToDoListActorImpl extends FabricActor implements ToDoListActor, Remindable
{
    public ToDoListActor(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture receiveReminderAsync(String reminderName, byte[] context, Duration dueTime, Duration period)
    {
        if (reminderName.equals("Pay cell phone bill"))
        {
            int amountToPay = ByteBuffer.wrap(context).getInt();
            System.out.println("Please pay your cell phone bill of " + amountToPay);
        }
        return CompletableFuture.completedFuture(true);
    }

```

Po wyzwoleniu przypomnienia środowisko uruchomieniowe Reliable Actors wywoła metodę `ReceiveReminderAsync`(C#) lub `receiveReminderAsync`(Java) dla aktora. Aktor może rejestrować wiele przypomnień, a metoda `ReceiveReminderAsync`(C#) lub `receiveReminderAsync`(Java) jest wywoływana, gdy zostanie wyzwolone dowolne z tych przypomnień. Aktor może użyć nazwy przypomnienia, która została przeniesiona do metody `ReceiveReminderAsync`(C#) lub `receiveReminderAsync`(Java), aby ustalić, które przypomnienie zostało wyzwolone.

Środowisko wykonawcze aktorów zapisuje stan aktora po zakończeniu wywołaniaC#`ReceiveReminderAsync`() lub `receiveReminderAsync`(Java). Jeśli wystąpi błąd podczas zapisywania stanu, ten obiekt aktora zostanie zdezaktywowany i zostanie aktywowane nowe wystąpienie.

Aby wyrejestrować przypomnienie, aktor wywołuje metodę `UnregisterReminderAsync`(C#) lub `unregisterReminderAsync`(Java), jak pokazano w poniższych przykładach.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

Jak pokazano powyżej, Metoda `UnregisterReminderAsync`(C#) lub `unregisterReminderAsync`(Java) akceptuje interfejs `IActorReminder`(C#) lub `ActorReminder`(Java). Klasa bazowa aktora obsługuje metodę `GetReminder`(C#) lub `getReminder`(Java), która może być używana do pobierania interfejsu `IActorReminder`(C#) lub `ActorReminder`(Java) przez przekazanie nazwy przypomnienia. Jest to wygodne, ponieważ aktor nie musi utrwalać interfejsu `IActorReminder`(C#) lub `ActorReminder`(Java), który został zwrócony z wywołania metody `RegisterReminder`(C#) lub `registerReminder`(Java).

## <a name="next-steps"></a>Następne kroki
Poznaj niezawodne zdarzenia aktora i współużytkowania wątkowości:
* [Zdarzenia aktora](service-fabric-reliable-actors-events.md)
* [Współużytkowania wątkowości aktora](service-fabric-reliable-actors-reentrancy.md)
