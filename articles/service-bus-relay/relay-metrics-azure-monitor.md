---
title: Metryki Azure Relay w Azure Monitor (wersja zapoznawcza) | Microsoft Docs
description: Ten artykuł zawiera informacje dotyczące monitorowania stanu Azure Relay za pomocą Azure Monitor.
services: service-bus-relay
documentationcenter: .NET
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 5c548186ec51cf86f34942cb15d8f984afa60268
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514939"
---
# <a name="azure-relay-metrics-in-azure-monitor-preview"></a>Metryki Azure Relay w Azure Monitor (wersja zapoznawcza)
Metryki Azure Relay umożliwiają udostępnienie stanu zasobów w ramach subskrypcji platformy Azure. Dzięki bogatemu zestawowi danych metryk można ocenić ogólną kondycję zasobów przekazywania, nie tylko na poziomie przestrzeni nazw, ale również na poziomie jednostki. Te dane statystyczne mogą być ważne, ponieważ ułatwiają monitorowanie stanu Azure Relay. Metryki może również pomóc główną przyczynę problemów bez konieczności skontaktuj się z działem pomocy technicznej platformy Azure.

Usługa Azure Monitor zapewnia interfejsy użytkownika ujednolicone monitorowanie z przekraczaniem różne usługi platformy Azure. Aby uzyskać więcej informacji, zobacz [monitorowania na platformie Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) i [metryki pobierania usługi Azure Monitor przy użyciu platformy .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) próbki w witrynie GitHub.

> [!IMPORTANT]
> Ten artykuł ma zastosowanie tylko do Połączenia hybrydowe funkcji Azure Relay, a nie do WCF Relay. 

## <a name="access-metrics"></a>Dostęp do metryk

Usługa Azure Monitor zapewnia wiele sposobów, aby dostęp do metryk. Możesz albo metryk dostęp za pośrednictwem [witryny Azure portal](https://portal.azure.com), lub użyj rozwiązań analitycznych, takich jak pakietu Operation Management Suite i usługi Event Hubs i interfejsów API usługi Azure Monitor (REST i .NET). Aby uzyskać więcej informacji, zobacz [dane monitorowania zbierane przez usługi Azure Monitor](../azure-monitor/platform/data-platform.md).

Metryki są domyślnie włączone i możesz uzyskać dostęp z ostatnich 30 dni danych. Jeśli zachodzi potrzeba Zachowaj dane przez dłuższy okres czasu, można archiwizować dane metryk do konta usługi Azure Storage. To ustawienie jest konfigurowane w [ustawień diagnostycznych](../azure-monitor/platform/diagnostic-settings.md) w usłudze Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Dostęp do metryk w portalu

Możesz monitorować metryki, wraz z upływem czasu w [witryny Azure portal](https://portal.azure.com). Jak wyświetlić żądania zakończone powodzeniem i żądania przychodzące na poziomie konta można znaleźć w poniższym przykładzie:

![][1]

Można również uzyskać dostęp do metryk bezpośrednio za pośrednictwem przestrzeni nazw. Aby to zrobić, wybierz przestrzeń nazw, a następnie kliknij pozycję **metryki (wersja zapoznawcza)** . 

W przypadku metryk obsługujących wymiary należy filtrować według żądanej wartości wymiaru.

## <a name="billing"></a>Rozliczenia

Przy użyciu metryk w usłudze Azure Monitor jest aktualnie opłat w wersji zapoznawczej. Jednak jeśli używasz dodatkowych rozwiązań, które pozyskiwania danych metryk, może zostać naliczona według tych rozwiązań. Na przykład opłaty są naliczane przez usługę Azure Storage archiwizowania danych metryk z kontem usługi Azure Storage. Opłaty są naliczane również za pomocą dzienników Azure Monitor, jeśli przesyłasz strumieniowo dane metryk do dzienników Azure Monitor w celu przeprowadzenia zaawansowanej analizy.

Następujące metryki umożliwiają przegląd kondycji usługi. 

> [!NOTE]
> Firma Microsoft jest wycofano kilka metryk, ponieważ są one przenoszone pod inną nazwą. Może to wymagać aktualizacji odwołaniami. Oznaczona za pomocą słowa kluczowego "przestarzałe" metryk nie będą obsługiwane przyszłości.

Wszystkie wartości metryk są wysyłane do usługi Azure Monitor na minutę. Stopień szczegółowości czasu określa interwał czasu, dla której są prezentowane wartości metryk. Obsługiwany przedział czasu dla wszystkich metryk Azure Relay wynosi 1 minutę.

## <a name="connection-metrics"></a>Metryki połączeń

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| ListenerConnections — sukces (wersja zapoznawcza) | Liczba pomyślnych połączeń odbiornika wykonanych w celu Azure Relay w określonym przedziale czasu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|ListenerConnections-błąd clienterror (wersja zapoznawcza)|Liczba błędów klienta w połączeniach odbiornika w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|ListenerConnections-błąd servererror (wersja zapoznawcza)|Liczba błędów serwera w połączeniach odbiornika w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|SenderConnections — sukces (wersja zapoznawcza)|Liczba pomyślnych połączeń nadawcy wykonanych w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|SenderConnections-błąd clienterror (wersja zapoznawcza)|Liczba błędów klienta w połączeniach nadawcy w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|SenderConnections-błąd servererror (wersja zapoznawcza)|Liczba błędów serwera w połączeniach nadawcy w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|ListenerConnections-TotalRequests (wersja zapoznawcza)|Całkowita liczba połączeń odbiornika w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|SenderConnections-TotalRequests (wersja zapoznawcza)|Żądania połączenia wykonywane przez nadawców w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Połączeń ActiveConnections (wersja zapoznawcza)|Liczba aktywnych połączeń w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|ActiveListeners (wersja zapoznawcza)|Liczba aktywnych odbiorników w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|ListenerDisconnects (wersja zapoznawcza)|Liczba odłączonych odbiorników w określonym przedziale czasu.<br/><br/> Jednostka: bajty <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|SenderDisconnects (wersja zapoznawcza)|Liczba odłączonych nadawców w określonym przedziale czasu.<br/><br/> Jednostka: bajty <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|

## <a name="memory-usage-metrics"></a>Metryki użycia pamięci

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|BytesTransferred (wersja zapoznawcza)|Liczba bajtów transferowanych w określonym przedziale czasu.<br/><br/> Jednostka: bajty <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|

## <a name="metrics-dimensions"></a>Wymiary metryki

Azure Relay obsługuje następujące wymiary dla metryk w Azure Monitor. Dodawanie wymiarów do metryk jest opcjonalne. Jeśli nie dodasz wymiarów, metryki są określone na poziomie przestrzeni nazw. 

|Nazwa wymiaru|Opis|
| ------------------- | ----------------- |
|EntityName| Azure Relay obsługuje jednostki obsługi komunikatów w przestrzeni nazw.|

## <a name="next-steps"></a>Następne kroki

Zobacz [monitorowania platformy Azure — omówienie](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




