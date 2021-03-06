---
title: Diagnostyka i monitorowanie usługi Azure servicefabric
description: W tym artykule opisano funkcje monitorowania wydajności w Service Fabric niezawodnego środowiska uruchomieniowego servicekomunikacja zdalnego, takie jak liczniki wydajności emitowane przez ten program.
author: suchiagicha
ms.topic: conceptual
ms.date: 06/29/2017
ms.author: pepogors
ms.openlocfilehash: 31095a619fc4d756fa4ef9c29691d1d511d59ece
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426700"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Diagnostyka i monitorowanie wydajności dla niezawodnej komunikacji zdalnej usługi
Niezawodne środowisko uruchomieniowe servicekomunikacji zdalnej nie emituje [liczników wydajności](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Umożliwiają one wgląd w działanie usługi servicekomunikacja zdalna i rozwiązywanie problemów i monitorowanie wydajności.


## <a name="performance-counters"></a>Liczniki wydajności
Niezawodne środowisko uruchomieniowe servicekomunikacji zdalnej definiuje następujące kategorie liczników wydajności:

| Kategoria | Opis |
| --- | --- |
| Usługa Service Fabric |Liczniki specyficzne dla komunikacji zdalnej usługi Azure Service Fabric, na przykład średni czas przetwarzania żądania |
| Service Fabric metoda usługi |Liczniki specyficzne dla metod implementowanych przez usługę komunikacji zdalnej Service Fabric, na przykład częstotliwość wywoływania metody usługi |

Każda z powyższych kategorii ma jeden lub więcej liczników.

Aplikacja [Monitor wydajności systemu Windows](https://technet.microsoft.com/library/cc749249.aspx) , która jest dostępna domyślnie w systemie operacyjnym Windows, może służyć do zbierania i wyświetlania danych licznika wydajności. [Diagnostyka Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) jest kolejną opcją do zbierania danych licznika wydajności i przekazywania ich do tabel platformy Azure.

### <a name="performance-counter-instance-names"></a>Nazwy wystąpień liczników wydajności
Klaster, który ma dużą liczbę usług zdalnej usługi lub partycji, ma dużą liczbę wystąpień liczników wydajności. Nazwy wystąpień licznika wydajności mogą pomóc w zidentyfikowaniu konkretnej metody partycji i usługi (jeśli dotyczy), z którą skojarzone jest wystąpienie licznika wydajności.

#### <a name="service-fabric-service-category"></a>Kategoria usługi Service Fabric
W przypadku kategorii `Service Fabric Service`nazwy wystąpień liczników mają następujący format:

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* to ciąg reprezentujący identyfikator partycji Service Fabric, z którym jest skojarzone wystąpienie licznika wydajności. Identyfikator partycji jest identyfikatorem GUID, a jego reprezentacja w postaci ciągu jest generowana za pomocą metody [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) ze specyfikatorem formatu "D".

*ServiceReplicaOrInstanceId* to ciąg reprezentujący identyfikator Service Fabric repliki/wystąpienia, z którym jest skojarzone wystąpienie licznika wydajności.

*ServiceRuntimeInternalID* to ciąg reprezentujący 64-bitową liczbę całkowitą wygenerowaną przez środowisko uruchomieniowe usługi sieci szkieletowej do użytku wewnętrznego. Ta wartość jest uwzględniona w nazwie wystąpienia licznika wydajności, aby zapewnić jej unikatowość i uniknąć konfliktu z innymi nazwami wystąpień liczników wydajności. Użytkownicy nie powinni próbować interpretować tej części nazwy wystąpienia licznika wydajności.

Poniżej znajduje się przykład nazwy wystąpienia licznika dla licznika, który należy do kategorii `Service Fabric Service`:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

W powyższym przykładzie `2740af29-78aa-44bc-a20b-7e60fb783264` jest reprezentacja identyfikatora partycji Service Fabric, `635650083799324046` jest reprezentacją typu replika/identyfikator wystąpienia, a `5008379932` jest IDENTYFIKATORem 64-bitowym, który jest generowany dla wewnętrznego użycia środowiska uruchomieniowego.

#### <a name="service-fabric-service-method-category"></a>Kategoria metody usługi Service Fabric
W przypadku kategorii `Service Fabric Service Method`nazwy wystąpień liczników mają następujący format:

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*MethodName* to nazwa metody usługi, z którą jest skojarzone wystąpienie licznika wydajności. Format nazwy metody jest określany na podstawie pewnej logiki w środowisku uruchomieniowym usługi sieci szkieletowej, która równoważy czytelność nazwy z ograniczeniami dotyczącymi maksymalnej długości nazw wystąpień liczników wydajności w systemie Windows.

*ServiceRuntimeMethodId* to ciąg reprezentujący 32-bitową liczbę całkowitą wygenerowaną przez środowisko uruchomieniowe usługi sieci szkieletowej do użytku wewnętrznego. Ta wartość jest uwzględniona w nazwie wystąpienia licznika wydajności, aby zapewnić jej unikatowość i uniknąć konfliktu z innymi nazwami wystąpień liczników wydajności. Użytkownicy nie powinni próbować interpretować tej części nazwy wystąpienia licznika wydajności.

*ServiceFabricPartitionID* to ciąg reprezentujący identyfikator partycji Service Fabric, z którym jest skojarzone wystąpienie licznika wydajności. Identyfikator partycji jest identyfikatorem GUID, a jego reprezentacja w postaci ciągu jest generowana za pomocą metody [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) ze specyfikatorem formatu "D".

*ServiceReplicaOrInstanceId* to ciąg reprezentujący identyfikator Service Fabric repliki/wystąpienia, z którym jest skojarzone wystąpienie licznika wydajności.

*ServiceRuntimeInternalID* to ciąg reprezentujący 64-bitową liczbę całkowitą wygenerowaną przez środowisko uruchomieniowe usługi sieci szkieletowej do użytku wewnętrznego. Ta wartość jest uwzględniona w nazwie wystąpienia licznika wydajności, aby zapewnić jej unikatowość i uniknąć konfliktu z innymi nazwami wystąpień liczników wydajności. Użytkownicy nie powinni próbować interpretować tej części nazwy wystąpienia licznika wydajności.

Poniżej znajduje się przykład nazwy wystąpienia licznika dla licznika, który należy do kategorii `Service Fabric Service Method`:

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

W poprzednim przykładzie `ivoicemailboxservice.leavemessageasync` jest nazwą metody, `2` jest 32-bitowy identyfikator wygenerowany dla wewnętrznego użytku środowiska uruchomieniowego, `89383d32-e57e-4a9b-a6ad-57c6792aa521` jest reprezentacją ciągu Service Fabric identyfikatora partycji,`635650083804480486` jest reprezentacją ciągu Service Fabric repliki/wystąpienia, a `5008380` to identyfikator 64-bitowy wygenerowany dla wewnętrznego użycia środowiska uruchomieniowego.

## <a name="list-of-performance-counters"></a>Lista liczników wydajności
### <a name="service-method-performance-counters"></a>Liczniki wydajności metody usługi

Środowisko uruchomieniowe niezawodnej usługi publikuje następujące liczniki wydajności związane z wykonywaniem metod usługi.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Service Fabric metoda usługi |Wywołania/s |Liczba wywoływanych metod usługi na sekundę |
| Service Fabric metoda usługi |Średni czas dla wywołania |Czas wykonania metody usługi w milisekundach |
| Service Fabric metoda usługi |Zgłoszone wyjątki/s |Ile razy metoda usługi zgłosiła wyjątek na sekundę |

### <a name="service-request-processing-performance-counters"></a>Liczniki wydajności przetwarzania żądań obsługi
Gdy klient wywołuje metodę za pośrednictwem obiektu serwera proxy usługi, powoduje wysłanie komunikatu żądania przez sieć do usługi zdalnej. Usługa przetwarza komunikat żądania i wysyła odpowiedź z powrotem do klienta. Niezawodne środowisko uruchomieniowe servicekomunikacji zdalnej publikuje następujące liczniki wydajności związane z przetwarzaniem żądań obsługi.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Usługa Service Fabric |Liczba oczekujących żądań |Liczba żądań przetwarzanych w usłudze |
| Usługa Service Fabric |Średni czas na żądanie |Czas trwania (w milisekundach) przez usługę w celu przetworzenia żądania |
| Usługa Service Fabric |Średni czas deserializacji żądania (w milisekundach) |Czas (w milisekundach) deserializacji komunikatu żądania obsługi po odebraniu w usłudze |
| Usługa Service Fabric |Średni czas serializacji odpowiedzi (w milisekundach) |Czas (w milisekundach) serializacji komunikatu odpowiedzi usługi w usłudze przed wysłaniem odpowiedzi do klienta |

## <a name="next-steps"></a>Następne kroki
* [Przykładowy kod](https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0)
* [Dostawcy EventSource w narzędzia PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
