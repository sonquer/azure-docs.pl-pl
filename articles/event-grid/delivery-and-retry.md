---
title: Azure Event Grid dostarczania i ponów próbę
description: Opisuje, w jaki sposób Azure Event Grid dostarcza zdarzenia i jak obsługuje niedostarczone komunikaty.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: spelluru
ms.openlocfilehash: 483b8251bf17eaa5fe7aa7cbd86299575535725d
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170040"
---
# <a name="event-grid-message-delivery-and-retry"></a>Event Grid dostarczania komunikatów i ponów próbę

W tym artykule opisano, jak Azure Event Grid obsługuje zdarzenia, gdy dostarczenie nie jest potwierdzone.

Event Grid zapewnia trwałe dostarczanie. Każdy z nich dostarcza każdy komunikat co najmniej raz dla każdej subskrypcji. Zdarzenia są natychmiast wysyłane do zarejestrowanego punktu końcowego dla każdej subskrypcji. Jeśli punkt końcowy nie potwierdzi przyjęcia zdarzenia, Event Grid ponawianie prób dostarczenia zdarzenia.

## <a name="batched-event-delivery"></a>Wsadowe dostarczanie zdarzeń

Event Grid domyślnie wysyłać każde zdarzenie do subskrybentów. Subskrybent otrzymuje tablicę z pojedynczym zdarzeniem. Można skonfigurować Event Grid do zdarzeń wsadowych na potrzeby dostarczania w celu zwiększenia wydajności HTTP w scenariuszach o wysokiej przepływności.

Wsadowe dostarczanie ma dwa ustawienia:

* Maksymalna liczba **zdarzeń na partię** jest maksymalną liczbą zdarzeń, Event Grid będzie dostarczanych na partię. Ta liczba nigdy nie zostanie przekroczona. można jednak dostarczyć mniejszą liczbę zdarzeń, jeśli podczas publikowania nie będą dostępne żadne inne zdarzenia. Event Grid nie opóźnia zdarzeń, aby można było utworzyć partię, jeśli są dostępne mniej zdarzeń. Musi zawierać się w przedziale od 1 do 5 000.
* **Preferowany rozmiar wsadu w kilobajtach** to docelowy limit rozmiaru wsadu w kilobajtach. Podobnie jak w przypadku maksymalnych zdarzeń, rozmiar wsadu może być mniejszy, jeśli podczas publikowania nie są dostępne więcej zdarzeń. Istnieje możliwość, że partia jest większa niż preferowany rozmiar partii, *Jeśli* pojedyncze zdarzenie jest większe niż preferowany rozmiar. Na przykład jeśli preferowany rozmiar to 4 KB, a do Event Grid zostanie wypchnięte zdarzenie o rozmiarze 10 KB, zdarzenie o rozmiarze 10 KB będzie nadal dostarczane do własnej partii, a nie do porzucenia.

Dostarczanie wsadowe skonfigurowane dla każdej subskrypcji zdarzenia za pośrednictwem portalu, interfejsu wiersza polecenia, programu PowerShell lub zestawów SDK.

![Ustawienia dostarczania wsadowego](./media/delivery-and-retry/batch-settings.png)

## <a name="retry-schedule-and-duration"></a>Harmonogram ponownych prób i czas trwania

Event Grid czeka 30 sekund na odpowiedź po dostarczeniu komunikatu. Po 30 sekundach, jeśli punkt końcowy nie odpowiedział, komunikat zostanie umieszczony w kolejce w celu ponowienia próby. W przypadku dostarczania zdarzeń Event Grid są stosowane wykładnicze zasady ponawiania wycofywania. Event Grid ponawianie prób dostarczenia zgodnie z poniższym harmonogramem w oparciu o najlepszą pracę:

- 10 sekund
- 30 sekund
- 1 min
- 5 minut
- 10 minut
- 30 minut
- 1 godzina
- Co godzinę przez maksymalnie 24 godziny

Jeśli punkt końcowy odpowie w ciągu 3 minut, Event Grid podejmie próbę usunięcia zdarzenia z kolejki ponawiania prób, ale duplikaty mogą nadal zostać odebrane.

Event Grid dodaje małe losowe instrukcje do wszystkich ponownych prób i mogą odpowiednio Uzgodnij pominąć pewne ponowne próby, jeśli punkt końcowy jest ciągle w złej kondycji, nie działa przez długi czas lub wydaje się być przeciążony.

W przypadku zachowań deterministycznych Ustaw czas wygaśnięcia i maksymalną liczbę prób dostarczenia w [zasadach ponawiania subskrypcji](manage-event-delivery.md).

Domyślnie Event Grid wygasa wszystkie zdarzenia, które nie zostały dostarczone w ciągu 24 godzin. [Zasady ponawiania można dostosować](manage-event-delivery.md) podczas tworzenia subskrypcji zdarzeń. Podajesz maksymalną liczbę prób dostarczenia (wartość domyślna to 30) i czas wygaśnięcia zdarzenia na żywo (wartość domyślna to 1440 minut).

## <a name="delayed-delivery"></a>Opóźnione dostarczanie

W przypadku niepowodzeń dostarczania punktów końcowych Event Grid zacznie opóźniać dostarczenie i ponawianie zdarzeń do tego punktu końcowego. Na przykład jeśli pierwsze 10 zdarzeń opublikowanych w punkcie końcowym zakończy się niepowodzeniem, Event Grid przyjmie, że punkt końcowy ma problemy i spowoduje to opóźnienie wszystkich kolejnych ponownych prób *i nowych* dostaw przez pewien czas do kilku godzin.

Celem opóźnionej dostawy jest ochrona punktów końcowych w złej kondycji oraz systemu Event Grid. Bez wycofywania i opóźnień dostarczania w przypadku punktów końcowych w złej kondycji, Event Grid zasady ponawiania prób i możliwości woluminów mogą łatwo zapychać system.

## <a name="dead-letter-events"></a>Zdarzenia utraconych wiadomości

Gdy Event Grid nie można dostarczyć zdarzenia, może wysłać niedostarczone zdarzenie do konta magazynu. Ten proces jest znany jako utracony. Domyślnie Event Grid nie powoduje wyłączenia utraconych wiadomości. Aby je włączyć, należy określić konto magazynu do przechowywania niedostarczonych zdarzeń podczas tworzenia subskrypcji zdarzeń. Zdarzenia z tego konta magazynu są ściągane, aby można było rozpoznać dostawy.

Event Grid wysyła zdarzenie do lokalizacji utraconych, gdy nastąpi próba wszystkich ponownych prób. Jeśli Event Grid otrzymuje kod odpowiedzi 400 (złe żądanie) lub 413 (żądanie jest zbyt duże), natychmiast wysyła zdarzenie do punktu końcowego utraconych wiadomości. Te kody odpowiedzi wskazują, że dostarczenie zdarzenia nigdy nie powiedzie się.

Istnieje pięć minut opóźnienia między ostatnią próbą dostarczenia zdarzenia a jego dostarczeniem do lokalizacji utraconych wiadomości. To opóźnienie jest przeznaczone do zmniejszenia liczby operacji magazynu obiektów BLOB. Jeśli lokalizacja utraconych wiadomości jest niedostępna przez cztery godziny, zdarzenie zostanie odrzucone.

Przed ustawieniem lokalizacji utraconych wiadomości musisz mieć konto magazynu z kontenerem. Należy podać punkt końcowy dla tego kontenera podczas tworzenia subskrypcji zdarzeń. Punkt końcowy ma format: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Możesz chcieć otrzymywać powiadomienia, gdy zdarzenie zostało wysłane do lokalizacji utraconych wiadomości. Aby użyć Event Grid do odpowiadania na zdarzenia niedostarczone, [Utwórz subskrypcję zdarzeń](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) dla utraconego magazynu obiektów BLOB. Za każdym razem, gdy magazyn obiektów BLOB utraconych wiadomości odbiera zdarzenie niedostarczone, Event Grid powiadamia program obsługi. Program obsługi reaguje na akcje, które mają zostać wykonane w celu uzgodnienia niedostarczonych zdarzeń.

Aby zapoznać się z przykładem konfigurowania martwej lokalizacji, zobacz artykuł [utracony i zasady ponawiania](manage-event-delivery.md).

## <a name="message-delivery-status"></a>Stan dostarczania wiadomości

Event Grid używa kodów odpowiedzi HTTP w celu potwierdzenia odbioru zdarzeń. 

### <a name="success-codes"></a>Kody sukcesu

Event Grid traktuje **tylko** następujące kody odpowiedzi HTTP jako pomyślne dostawy. Wszystkie inne kody stanu są uważane za nieudane dostawy i zostaną ponowione lub deadlettered zgodnie z potrzebami. Po otrzymaniu kodu stanu pomyślnego Event Grid uznaje, że dostarczenie zostało zakończone.

- 200 OK
- 201 Utworzono
- 202 zaakceptowane
- 203 informacje nieautorytatywne
- 204 Brak zawartości

### <a name="failure-codes"></a>Kody błędów

Wszystkie inne kody, które nie znajdują się w powyższym zestawie (200-204), są uznawane za niepowodzenia i zostanie ponowione. Niektóre z nich mają określone zasady ponawiania prób powiązane z nimi, które podano poniżej, wszystkie inne przestrzegają standardowego modelu wykładniczego wycofywania. Należy pamiętać, że ze względu na wysoce parallelowy charakter architektury Event Grid, zachowanie ponowienia nie jest deterministyczne. 

| Kod stanu | Zachowanie przy ponowieniu próby |
| ------------|----------------|
| 400 Nieprawidłowe żądanie | Ponów próbę po 5 minutach lub więcej (Utracono wiadomości natychmiast, jeśli konfiguracja utraconych wiadomości) |
| 401 — nieautoryzowane | Ponów próbę po 5 minutach lub dłużej |
| 403 Zabronione | Ponów próbę po 5 minutach lub dłużej |
| 404 — Nie odnaleziono | Ponów próbę po 5 minutach lub dłużej |
| 408 — limit czasu żądania | Ponów próbę po 2 minutach lub więcej |
| Jednostka żądania 413 jest zbyt duża | Ponów próbę po upływie 10 sekund lub więcej (utraconych wiadomości, jeśli konfiguracja utraconych wiadomości) |
| 503 — usługa niedostępna | Ponów próbę po 30 sekundach lub więcej |
| Wszystkie pozostałe | Ponów próbę po 10 sekundach lub więcej |


## <a name="next-steps"></a>Następne kroki

* Aby wyświetlić stan dostaw zdarzeń, zobacz [monitorowanie Event Grid dostarczania komunikatów](monitor-event-delivery.md).
* Aby dostosować opcje dostarczania zdarzeń, zobacz artykuł [utracony i zasady ponawiania](manage-event-delivery.md).
