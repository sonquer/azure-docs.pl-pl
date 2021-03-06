---
title: Azure Event Hubs — wysyłanie i odbieranie zdarzeń przy użyciu .NET Framework
description: 'Szybki Start: Ten artykuł zawiera Przewodnik dotyczący tworzenia aplikacji .NET Framework, która wysyła zdarzenia do usługi Azure Event Hubs.'
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 12/20/2019
ms.author: shvija
ms.openlocfilehash: 385430d993afe8b7a0ad57991d3c93eebd46ddcb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437263"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-net-framework"></a>Szybki Start: wysyłanie zdarzeń do i odbieranie zdarzeń z usługi Azure Event Hubs przy użyciu .NET Framework
Azure Event Hubs to platforma do pozyskiwania i strumieniowego przesyłania danych, która umożliwia odbieranie i przetwarzanie milionów zdarzeń na sekundę. Usługa Event Hubs pozwala przetwarzać i przechowywać zdarzenia, dane lub dane telemetryczne generowane przez rozproszone oprogramowanie i urządzenia. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Aby zapoznać się ze szczegółowym omówieniem usługi Event Hubs, zobacz [Omówienie usługi Event Hubs](event-hubs-about.md) i [Funkcje usługi Event Hubs](event-hubs-features.md).

W tym samouczku pokazano, jak utworzyć aplikacje konsolowe .NET Framework w programie w C# celu wysyłania zdarzeń do zdarzeń lub odbierania ich z centrum eventhub. 

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

- [Microsoft Visual Studio 2019](https://visualstudio.com).
- **Utwórz przestrzeń nazw Event Hubs i centrum zdarzeń**. Pierwszym krokiem jest skorzystanie z witryny [Azure Portal](https://portal.azure.com) w celu utworzenia przestrzeni nazw typu Event Hubs i uzyskania poświadczeń zarządzania wymaganych przez aplikację do komunikacji z centrum zdarzeń. Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md). Następnie Pobierz **Parametry połączenia dla przestrzeni nazw centrum zdarzeń** , wykonując instrukcje podane w artykule: [pobieranie parametrów połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Te parametry połączenia będą potrzebne w dalszej części tego samouczka.

## <a name="send-events"></a>Wysyłanie zdarzeń 
W tej sekcji przedstawiono sposób tworzenia .NET Framework aplikacji konsolowej do wysyłania zdarzeń do centrum zdarzeń. 

### <a name="create-a-console-application"></a>Tworzenie aplikacji konsolowej

W programie Visual Studio utwórz nowy projekt aplikacji klasycznej Visual C# za pomocą szablonu projektu **Aplikacja konsoli**. Nazwij projekt **Nadawca**.
   
![Tworzenie aplikacji konsolowej](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)

### <a name="add-the-event-hubs-nuget-package"></a>Dodawanie pakietu NuGet usługi Event Hubs

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **Nadawca**, a następnie kliknij pozycję **Zarządzaj pakietami NuGet rozwiązania**. 
2. Kliknij kartę **Przeglądanie**, a następnie wyszukaj ciąg `WindowsAzure.ServiceBus`. Kliknij pozycję **Zainstaluj** i zaakceptuj warunki użytkowania. 
   
    ![Zainstaluj pakiet NuGet usługi Service Bus](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp2.png)
   
    Program Visual Studio pobierze, zainstaluje i doda odniesienia do [pakietu NuGet biblioteki usługi Azure Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus).

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Pisanie kodu w celu wysyłania komunikatów do centrum zdarzeń

1. Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:
   
    ```csharp
    using System.Threading;
    using Microsoft.ServiceBus.Messaging;
    ```
2. Dodaj następujące pola do klasy **Program**, zastępując symbole zastępcze nazwą centrum zdarzeń utworzonego w poprzedniej sekcji oraz zapisanymi wcześniej parametrami połączenia na poziomie przestrzeni nazw. Możesz skopiować parametry połączenia Centrum zdarzeń z **podstawowe parametry połączenia** klucza w ramach **RootManageSharedAccessKey** na stronie Centrum zdarzeń w witrynie Azure portal. Aby uzyskać szczegółowe instrukcje, zobacz [pobieranie parametrów połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal).
   
    ```csharp
    static string eventHubName = "Your Event Hub name";
    static string connectionString = "namespace connection string";
    ```
3. Dodaj następującą metodę do klasy **Program**:
   
      ```csharp
      static void SendingRandomMessages()
      {
          var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
          while (true)
          {
              try
              {
                  var message = Guid.NewGuid().ToString();
                  Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                  eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
              }
              catch (Exception exception)
              {
                  Console.ForegroundColor = ConsoleColor.Red;
                  Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                  Console.ResetColor();
              }
       
              Thread.Sleep(200);
          }
      }
      ```
   
      Ta metoda stale wysyła zdarzenia do centrum zdarzeń z opóźnieniem 200 ms.
4. Na koniec dodaj następujące wiersze do metody **Główne**:
   
      ```csharp
      Console.WriteLine("Press Ctrl-C to stop the sender process");
      Console.WriteLine("Press Enter to start now");
      Console.ReadLine();
      SendingRandomMessages();
      ```
5. Uruchom program i upewnij się, że nie ma w nim żadnych błędów.
  
## <a name="receive-events"></a>Odbieranie zdarzeń
W tej sekcji utworzysz aplikację konsolową .NET Framework, która odbiera komunikaty z centrum zdarzeń za pomocą [hosta procesora zdarzeń](event-hubs-event-processor-host.md). [Host procesora zdarzeń](event-hubs-event-processor-host.md) jest klasą .NET, która upraszcza odbieranie zdarzeń z centrów zdarzeń przez zarządzanie trwałymi punktami kontrolnymi i równoległymi odbiorami z tych centrów zdarzeń. Za pomocą hosta procesora zdarzeń można podzielić zdarzenia między wieloma odbiornikami, nawet w przypadku hostowania w różnych węzłach. 

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

### <a name="create-a-console-application"></a>Tworzenie aplikacji konsolowej

W programie Visual Studio utwórz nowy projekt aplikacji klasycznej Visual C# za pomocą szablonu projektu **Aplikacja konsoli**. Nazwij projekt **Odbiornik**.
   
![Tworzenie aplikacji konsolowej](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)

### <a name="add-the-event-hubs-nuget-package"></a>Dodawanie pakietu NuGet usługi Event Hubs

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **Odbiornik**, a następnie kliknij pozycję **Zarządzaj pakietami NuGet rozwiązania**.
2. Kliknij kartę **Przeglądanie**, a następnie wyszukaj ciąg `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Kliknij pozycję **Zainstaluj** i zaakceptuj warunki użytkowania.
   
    ![Wyszukaj pakiet NuGet hosta procesora zdarzeń](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    Program Visual Studio pobierze, zainstaluje i doda odniesienie do [centrum zdarzeń usługi Azure Service Bus — pakiet NuGet EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost) wraz ze wszystkimi jego zależnościami.

### <a name="implement-the-ieventprocessor-interface"></a>Implementowanie interfejsu IEventProcessor

1. Kliknij prawym przyciskiem myszy projekt **Odbiornik**, kliknij przycisk **Dodaj**, a następnie kliknij opcję **Klasa**. Nadaj nowej klasie nazwę **SimpleEventProcessor**, a następnie kliknij przycisk **Dodaj**, aby utworzyć klasę.
   
    ![Dodawanie klasy SimpleEventProcessor](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
2. W górnej części pliku SimpleEventProcessor.cs dodaj następujące instrukcje:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      using System.Diagnostics;
      ```
    
3. Zastąp następujący kod treścią klasy:
    
      ```csharp
      class SimpleEventProcessor : IEventProcessor
      {
        Stopwatch checkpointStopWatch;
        
        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }
        
        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }
        
        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
        
                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                    context.Lease.PartitionId, data));
            }
        
            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
      }
      ```
    
      Klasa ta zostanie wywołana przez klasę **EventProcessorHost** do przetwarzania zdarzeń odebranych z centrum zdarzeń. Klasa `SimpleEventProcessor` używa stopera, aby okresowo wywoływać metodę punktu kontrolnego w kontekście klasy **EventProcessorHost**. Takie przetwarzanie daje gwarancję, że jeśli odbiorca zostanie ponownie uruchomiony, nie straci więcej niż pięć minut operacji przetwarzania.

### <a name="update-the-main-method-to-use-simpleeventprocessor"></a>Aktualizowanie metody Main w celu użycia klasy SimpleEventProcessor

1. W klasie **Program** dodaj następującą instrukcję `using` w górnej części pliku:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      ```
    
2. Zastąp `Main` method in Class metoda `Program` klasy następujący kod, zastępując nazwę Centrum zdarzeń i parametry połączenia na poziomie przestrzeni nazw, które zostały zapisane wcześniej oraz konto magazynu i klucz skopiowane we wcześniejszych sekcjach. 
    
      ```csharp
      static void Main(string[] args)
      {
        string eventHubConnectionString = "{Event Hubs namespace connection string}";
        string eventHubName = "{Event Hub name}";
        string storageAccountName = "{storage account name}";
        string storageAccountKey = "{storage account key}";
        string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);
        
        string eventProcessorHostName = Guid.NewGuid().ToString();
        EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
        Console.WriteLine("Registering EventProcessor...");
        var options = new EventProcessorOptions();
        options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
        eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();
        
        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();
        eventProcessorHost.UnregisterEventProcessorAsync().Wait();
      }
      ```
    
3. Uruchom program i upewnij się, że nie ma w nim żadnych błędów.
  
## <a name="next-steps"></a>Następne kroki
Przeczytaj następujące artykuły: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkcje i terminologia na platformie Azure Event Hubs](event-hubs-features.md).
- [Event Hubs — często zadawane pytania](event-hubs-faq.md)


<!-- Links -->
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Event Hubs overview]: event-hubs-about.md
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]:../storage/common/storage-create-storage-account.md
[Event Processor Host]: event-hubs-event-processor-host.md
[Azure portal]: https://portal.azure.com
