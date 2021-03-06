---
title: Wyświetlanie stanu zadań importu/eksportu platformy Azure | Microsoft Docs
description: Dowiedz się, jak wyświetlić stan zadań importu/eksportu i używanych dysków.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 222c893f06d9adf77f8a8124af18bc03c5d20bdf
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821431"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Wyświetlanie stanu zadań importu/eksportu platformy Azure

Ten artykuł zawiera informacje dotyczące sposobu wyświetlania stanu dysku i zadania na potrzeby zadań importu/eksportu platformy Azure. Usługa Azure Import/Export służy do bezpiecznego transferu dużych ilości danych do obiektów blob platformy Azure i Azure Files. Usługa jest również używana do eksportowania danych z usługi Azure Blob Storage.  

## <a name="view-job-and-drive-status"></a>Wyświetlanie stanu zadania i dysku
Można śledzić stan zadań importu lub eksportu z Azure Portal. Kliknij kartę **Import/Export** . Na stronie zostanie wyświetlona lista zadań.

![Wyświetl stan zadania](./media/storage-import-export-service/jobstate.png)

## <a name="view-job-status"></a>Wyświetlanie stanu zadania

Zostanie wyświetlony jeden z następujących stanów zadań w zależności od miejsca, w którym znajduje się dysk.

| Stan zadania | Opis |
|:--- |:--- |
| Tworzenie | Po utworzeniu zadania jego stan jest ustawiany na **Tworzenie**. Gdy zadanie jest w stanie **tworzenia** , usługa importu/eksportu zakłada, że dyski nie zostały dostarczone do centrum danych. Zadanie może pozostawać w tym stanie przez maksymalnie dwa tygodnie, po upływie którego zostanie automatycznie usunięte przez usługę. |
| Towarzystwo | Po wysłaniu pakietu należy zaktualizować informacje o śledzeniu w Azure Portal.  Spowoduje to zamianę zadania w stan **wysyłki** . Zadanie pozostaje w stanie **wysyłki** przez maksymalnie dwa tygodnie. 
| Odebrano | Po odebraniu wszystkich dysków w centrum danych stan zadania jest ustawiany na **odebrane**. |
| Przekazując | Po rozpoczęciu przetwarzania co najmniej jednego dysku stan zadania jest ustawiany na **transfer**. Aby uzyskać więcej informacji, przejdź do pozycji [dysk Stany](#view-drive-status). |
| Tworzenie pakietów | Po zakończeniu przetwarzania na wszystkich dyskach zadanie jest umieszczane w stanie **pakowania** , dopóki dyski nie zostaną wysłane z powrotem do użytkownika. |
| Zakończone | Po wysłaniu wszystkich dysków z powrotem do użytkownika, jeśli zadanie zostało ukończone bez błędów, to zadanie jest ustawione na **ukończone**. Zadanie jest automatycznie usuwane po 90 dniach w stanie **ukończone** . |
| napis | Po wysłaniu wszystkich dysków do użytkownika, jeśli wystąpią błędy podczas przetwarzania zadania, zadanie jest ustawione na **zamknięte**. Zadanie jest automatycznie usuwane po 90 dniach w stanie **zamkniętym** . |

## <a name="view-drive-status"></a>Wyświetlanie stanu dysku

W poniższej tabeli opisano cykl życia poszczególnych stacji podczas przejścia przez zadanie importowania lub eksportowania. Bieżący stan każdego dysku w zadaniu jest widoczny w Azure Portal.

W poniższej tabeli opisano każdy stan, w którym można przechodzić poszczególne dyski w zadaniu.

| Stan dysku | Opis |
|:--- |:--- |
| Wyszczególnion | W przypadku zadania importowania, gdy zadanie jest tworzone na podstawie Azure Portal, zostanie **określony**stan początkowy dla dysku. W przypadku zadania eksportu, ponieważ nie określono dysku podczas tworzenia zadania, zostanie **odebrany**stan dysku początkowego. |
| Odebrano | Dysk przechodzi do stanu **otrzymanego** , gdy usługa importu/eksportu przetworzyła dyski, które zostały odebrane przez firmę wysyłkową dla zadania importu. W przypadku zadania eksportu stan początkowej stacji jest stan **odebrany** . |
| NeverReceived | Dysk przechodzi do stanu **NeverReceived** , gdy pakiet dla zadania dociera, ale pakiet nie zawiera dysku. Dysk jest również przenoszony do tego stanu, jeśli miał dwa tygodnie od momentu odebrania przez usługę informacji o wysyłce, ale pakiet nie dotarł jeszcze do centrum danych. |
| Przekazując | Dysk przechodzi do stanu **transferu** , gdy usługa zaczyna transferować dane z dysku do usługi Azure Storage. |
| Zakończone | Dysk przechodzi do stanu **ukończenia** , gdy usługa pomyślnie przekazała wszystkie dane bez błędów.
| CompletedMoreInfo | Dysk przechodzi do stanu **CompletedMoreInfo** , gdy usługa napotkała problemy podczas kopiowania danych z lub do dysku. Informacje mogą zawierać błędy, ostrzeżenia i komunikaty informacyjne dotyczące zastępowania obiektów BLOB.
| ShippedBack | Dysk przechodzi do stanu **ShippedBack** , gdy został wysłany z centrum danych z powrotem do adresu zwrotnego. |

Ten obraz z Azure Portal zawiera stan dysku przykładowego zadania:

![Wyświetl stan dysku](./media/storage-import-export-service/drivestate.png)

W poniższej tabeli opisano Stany awarii stacji i akcje podejmowane dla każdego stanu.

| Stan dysku | Wydarzenie | Rozwiązanie/następny krok |
|:--- |:--- |:--- |
| NeverReceived | Dysk oznaczony jako **NeverReceived** (ponieważ nie został odebrany jako część wysyłki zadania) dociera do innej wysyłki. | Zespół operacyjny przenosi dysk do **odbierania**. |
| ND | Dysk, który nie jest częścią żadnego zadania, dociera do centrum danych jako część innego zadania. | Dysk jest oznaczony jako dodatkowy dysk i jest zwracany do użytkownika, gdy zostanie zakończone zadanie skojarzone z oryginalnym pakietem. |

## <a name="time-to-process-job"></a>Czas przetwarzania zadania
Czas przetwarzania zadania importu/eksportu zależy od wielu czynników, takich jak:

-  Czas dostawy
-  Ładowanie w centrum danych
-  Typ zadania i rozmiar kopiowanych danych
-  Liczba dysków w zadaniu. 

Usługa Import/Export nie ma umowy SLA, ale usługa dokłada starań, aby zakończyć kopię w dniach od 7 do 10 dni po odebraniu dysków. Oprócz stanu opublikowanego w witrynie Azure Portal interfejsy API REST mogą służyć do śledzenia postępów zadań. Parametr procent ukończenia w wywołaniu interfejsu API operacji [listy zadań](/previous-versions/azure/dn529083(v=azure.100)) zapewnia procentowy postęp kopiowania.


## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie narzędzia WAImportExport](storage-import-export-tool-how-to.md)
* [Transferowanie danych za pomocą narzędzia wiersza polecenia AzCopy](storage-use-azcopy.md)
* [Przykład interfejsu API REST usługi Azure Import Export](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)
