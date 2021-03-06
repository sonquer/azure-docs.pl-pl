---
title: Rozwiązywanie problemów z danych wejściowych dla usługi Azure Stream Analytics
description: W tym artykule opisano techniki rozwiązywania problemów z połączeniami danych wejściowych w zadań usługi Azure Stream Analytics.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: dac3037f82c38980c9ac16685aa7fddac68a2e7b
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720303"
---
# <a name="troubleshoot-input-connections"></a>Rozwiązywanie problemów z połączeniami wejściowymi

Ta strona zawiera opis typowych problemów z połączeniami danych wejściowych i sposób rozwiązać ten problem.

## <a name="input-events-not-received-by-job"></a>Zdarzenia wejściowe nie są odbierane przez zadania 
1.  Przetestuj połączenie. Sprawdź łączność z wejściami i wyjściami przy użyciu przycisku **Testuj połączenie** dla każdego wejścia i wyjścia.

2.  Sprawdź dane wejściowe.

    1. Aby sprawdzić, czy dane wejściowe są przepływane do centrum zdarzeń, użyj [eksploratora Service Bus](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) , aby nawiązać połączenie z usługą Azure Event Hub (jeśli jest używane wejście do centrum zdarzeń).
        
    1. Użyj przycisku [**przykładowe dane**](stream-analytics-sample-data-input.md) dla każdego elementu wejściowego. Pobierz przykładowe dane wejściowe.
        
    1. Sprawdź przykładowe dane, aby zrozumieć kształt danych — to oznacza, że schemat i [typy danych](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics).

3.  Upewnij się, że wybrano zakres czasu w podglądzie danych wejściowych. Wybierz **pozycję Wybierz zakres czasu**, a następnie wprowadź przykładowy czas trwania przed przetestowaniem zapytania.

## <a name="malformed-input-events-causes-deserialization-errors"></a>Źle sformułowane zdarzenia wejściowe powodują błędy deserializacji 
Deserializacji problemy są spowodowane, gdy strumień wejściowy zadania usługi Stream Analytics zawiera źle sformułowane komunikaty. Na przykład nieprawidłowo sformułowany komunikat może być spowodowane przez brak nawiasu lub nawiasów w obiekcie JSON lub format sygnatury czasowej niepoprawne, w polu czas. 
 
Gdy zadanie usługi Stream Analytics otrzymuje nieprawidłowo sformułowany komunikat z danych wejściowych, porzuca wiadomość i powiadamia użytkownika z ostrzeżeniem. Na kafelku **dane wejściowe** zadania Stream Analytics zostanie wyświetlony symbol ostrzegawczy. Znak to ostrzeżenie występuje, tak długo, jak długo zadanie jest w stanie uruchomienia:

![Kafelek usługi Azure Stream Analytics danych wejściowych.](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Włącz dzienniki diagnostyki wyświetlić szczegóły ostrzeżenia. Źle sformułowane zdarzenia wejściowe dzienniki wykonywania zawiera wpis z komunikat, który wygląda następująco: 
```
Could not deserialize the input event(s) from resource <blob URI> as json.
```

### <a name="what-caused-the-deserialization-error"></a>Co spowodowało błąd deserializacji
Można wykonać poniższe kroki, aby analizować zdarzenia wejściowe szczegółowo zapoznanie co spowodowało błąd deserializacji. Następnie można ustalić źródła zdarzeń do generowania zdarzeń w odpowiednim formacie, aby zapobiec osiągnięciu ten problem, ponownie.

1. Przejdź do kafelka danych wejściowych i kliknij pozycję symbole ostrzeżenia, aby zapoznać się z listą problemów.

2. Na kafelku dane wejściowe Wyświetla listę ostrzeżeń ze szczegółowymi informacjami o poszczególnych problemów. Przykładowy komunikat ostrzegawczy poniżej zawiera partycję, przesunięcie i numery sekwencyjne w przypadku, gdy są źle sformułowane dane JSON. 

   ![Stream Analytics komunikat ostrzegawczy z przesunięciem](media/stream-analytics-malformed-events/warning-message-with-offset.png)
   
3. Aby znaleźć dane JSON z nieprawidłowym formatem, uruchom kod CheckMalformedEvents.cs dostępny w [repozytorium przykładów usługi GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Ten kod odczytuje identyfikator partycji: przesunięcie i drukuje dane, które znajdują się w tym przesunięciu. 

4. Po odczytaniu danych możesz przeanalizować i poprawić format serializacji.

5. Można również [odczytywać zdarzenia z IoT Hub za pomocą eksploratora Service Bus](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b).

## <a name="job-exceeds-maximum-event-hub-receivers"></a>Zadania przekracza maksymalny odbiorcy usługi Event Hubs
Najlepszym rozwiązaniem jest dotyczące korzystania z usługi Event Hubs jest na potrzeby zapewnienia skalowalności zadania przez wiele grup odbiorców. Liczbę czytników w zadaniu Stream Analytics określone dane wejściowe wpływa na liczbę czytników w grupie jednego konsumenta. Dokładną liczbę odbiorników opiera się na szczegóły wewnętrznej implementacji logiki topologii skalowalnego w poziomie i nie jest widoczna zewnętrznie. Po uruchomieniu zadania lub podczas uaktualniania zadania, można zmienić liczbę czytników.

Błąd wyświetlany w przypadku przekroczenia maksymalnej liczby odbiorników to: `The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Po zmianie podczas uaktualniania zadania liczbę czytników przejściowy ostrzeżenia są zapisywane do dzienników inspekcji. Zadania usługi Stream Analytics automatycznie odzyskać te problemy przejściowe.

### <a name="add-a-consumer-group-in-event-hubs"></a>Dodaj grupę odbiorców w usłudze Event Hubs
Aby dodać nową grupę odbiorców w wystąpieniu usługi Event Hubs, wykonaj następujące kroki:

1. Zaloguj się do Portalu Azure.

2. Znajdź usługi Event Hubs.

3. Wybierz **Event Hubs** w obszarze nagłówka **jednostki** .

4. Wybierz Centrum zdarzeń według nazwy.

5. Na stronie **wystąpienie Event Hubs** w obszarze nagłówka **jednostki** wybierz pozycję **grupy odbiorców**. Zostanie wyświetlona Grupa odbiorców o nazwie **$default** .

6. Wybierz pozycję **+ Grupa odbiorców** , aby dodać nową grupę odbiorców. 

   ![Dodaj grupę odbiorców w usłudze Event Hubs](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Po utworzeniu danych wejściowych w ramach zadania usługi Stream Analytics, aby wskazać Centrum zdarzeń jest określona grupa odbiorców. $Default jest używany, gdy nie jest określona. Po utworzeniu nowej grupy konsumentów, edytować danych wejściowych Centrum zdarzeń w ramach zadania usługi Stream Analytics i określ nazwę nowej grupy odbiorców.


## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>Czytelnicy dla każdej partycji przekracza limit usługi Event Hubs

Jeśli przesyłania strumieniowego składnia zapytania odwołuje się do tego samego zasobu Centrum zdarzeń wejściowych wiele razy, aparat zadania można użyć wielu elementów odczytujących na zapytanie z tej samej grupy odbiorców. W przypadku zbyt wiele odwołań do tej samej grupy odbiorców może przekroczyć limit pięciu, zadania i zgłoszony błąd. W tych okolicznościach można dalej podzielić przy użyciu wielu danych wejściowych przez wiele grup odbiorców przy użyciu rozwiązania opisane w poniższej sekcji. 

Następujące scenariusze, w których liczbę czytników w jednej partycji przekracza limit usługi Event Hubs do 5:

* Wielokrotne instrukcje SELECT: w przypadku używania wielu instrukcji SELECT odwołujących się do **tego samego** danych wejściowych centrum zdarzeń każda instrukcja SELECT powoduje utworzenie nowego odbiornika.
* Unia: w przypadku korzystania z Unii możliwe jest posiadanie wielu danych wejściowych odwołujących się do tego **samego** centrum zdarzeń i grupy konsumentów.
* Samosprzężenie: w przypadku korzystania z operacji samosprzężenia można odwoływać się do tego **samego** centrum zdarzeń wiele razy.

Poniższe najlepsze rozwiązania może pomóc zmniejszyć scenariusze, w których liczbę czytników w jednej partycji przekracza limit usługi Event Hubs do 5.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Dzielenie zapytanie na wiele kroków za pomocą klauzuli WITH

Klauzula WITH Określa tymczasowy nazwany zestaw wyników, które mogą być przywoływane przez klauzuli FROM w kwerendzie. Należy zdefiniować klauzuli WITH w zakresie wykonywania pojedynczej instrukcji SELECT.

Na przykład, zamiast tego zapytania:

```SQL
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Skorzystaj z tej kwerendy:

```SQL
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Upewnij się, że dane wejściowe powiązać z różnych grup konsumenckich

Dla zapytań, w których co najmniej trzech danych wejściowych są podłączone do tej samej grupy konsumentów usługi Event Hubs należy utworzyć grupy konsumentów oddzielne. Ta migracja wymaga utworzenia dodatkowych danych wejściowych usługi Stream Analytics.

## <a name="get-help"></a>Uzyskiwanie pomocy

Aby uzyskać dalszą pomoc, wypróbuj nasze [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
