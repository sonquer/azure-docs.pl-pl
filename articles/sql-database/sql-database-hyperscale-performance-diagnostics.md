---
title: Diagnostyka wydajności w ramach skalowania
description: W tym artykule opisano sposób rozwiązywania problemów z wydajnością skalowania w Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: seo-lt-2019
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: 26bd6ddb9d8255b8e2510133fc4b6aa645f89f68
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2020
ms.locfileid: "75615059"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>Diagnostyka rozwiązywania problemów z wydajnością w ramach skalowania SQL

Aby rozwiązać problemy z wydajnością w bazie danych w ramach skalowania, [ogólna metodologia dostrajania wydajności](sql-database-monitor-tune-overview.md) w węźle obliczeniowym usługi Azure SQL Database jest punktem początkowym badania wydajności. Jednak ze względu na [rozproszoną architekturę](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) w ramach skalowania do pomocy dodano dodatkową diagnostykę. W tym artykule opisano szczegółowe dane diagnostyczne dotyczące skalowania.

## <a name="log-rate-throttling-waits"></a>Ograniczenie liczby dzienników

Każdy poziom usługi Azure SQL Database ma limity szybkości generowania dziennika, które są wymuszane przez [Zarządzanie szybkością rejestrowania](sql-database-resource-limits-database-server.md#transaction-log-rate-governance). W ramach skalowania limit generacji dzienników jest obecnie ustawiony na 100 MB/s, niezależnie od poziomu usługi. Jednak istnieją sytuacje, w których szybkość generowania dzienników w podstawowej replice obliczeniowej musi być ograniczona w celu zapewnienia możliwości odzyskiwania umowy SLA. Takie ograniczenie ma miejsce, gdy [serwer stronicowania lub inna replika obliczeniowa](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) znacznie za pomocą nowych rekordów dziennika z usługi log.

Następujące typy oczekiwania (w tabeli [sys. dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)) opisują przyczyny, dla których można ograniczyć szybkość rejestrowania w podstawowej replice obliczeniowej:

|Typ oczekiwania    |Opis                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | Występuje, gdy szybkość generowania dziennika podstawowego węzła obliczeniowego bazy danych jest ograniczona ze względu na opóźnione użycie dziennika na serwerze stron.         |
|RBIO_RG_DESTAGE        | Występuje, gdy jest ograniczana szybkość generowania dziennika węzła obliczeniowego bazy danych z powodu opóźnionego użycia dziennika przez długoterminowe przechowywanie dzienników.         |
|RBIO_RG_REPLICA        | Występuje, gdy jest ograniczana szybkość generowania dziennika węzła obliczeniowego bazy danych z powodu opóźnionego użycia dziennika przez odczytane repliki pomocnicze.         |
|RBIO_RG_LOCALDESTAGE   | Występuje, gdy jest ograniczana szybkość generowania dziennika węzła obliczeniowego bazy danych z powodu opóźnionego użycia dziennika przez usługę log.         |

## <a name="page-server-reads"></a>Odczyty serwera stronicowania

Repliki obliczeń nie buforują pełnej kopii bazy danych lokalnie. Dane lokalne do repliki obliczeniowej są przechowywane w puli buforów (w pamięci) i w lokalnej pamięci podręcznej rozszerzenia puli buforów (RBPEX), która jest częściową (nieobejmującą) pamięcią podręczną stron danych. Ta lokalna pamięć podręczna RBPEX jest proporcjonalna do rozmiaru obliczeń i jest trzykrotnie większa niż pamięć warstwy obliczeniowej. RBPEX przypomina pulę buforów, ponieważ ma najczęściej używane dane. Każdy serwer stron, z drugiej strony, zawiera pamięć podręczną RBPEXą dla części bazy danych, którą przechowuje.
 
Jeśli odczyt jest wystawiony w replice obliczeniowej, jeśli dane nie istnieją w puli buforów lub lokalnej pamięci podręcznej RBPEX, zostanie wygenerowane wywołanie funkcji GetPage (pageId, LSN), a strona jest pobierana z odpowiedniego serwera stronicowania. Odczyty z serwerów stronicowania to odczyty zdalne i są w tym samym wolniejsze niż odczyt z RBPEX lokalnego. W przypadku rozwiązywania problemów z wydajnością w ramach operacji we/wy musimy mieć możliwość poinformowania o liczbie systemu IOs wykonywanych przez stosunkowo wolniejsze odczyty zdalnego serwera stron.

Kilka zdarzeń widoków DMV i rozszerzonych zawiera kolumny i pola, które określają liczbę odczytów zdalnych z serwera stronicowego, które można porównać z łączną liczbą operacji odczytu. Magazyn zapytań przechwytuje również odczyty zdalne w ramach statystyk czasu wykonywania zapytania.

- Kolumny do operacji odczytywania serwera stron raportów są dostępne w widoków DMV wykonywania i w widokach wykazu, takich jak:
    - [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
    - [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
    - [sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
    - [sys. dm_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
    - [sys. query_store_runtime_stats](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql/)
- Operacje odczytu serwera stronicowania są dodawane do następujących zdarzeń rozszerzonych:
    - sql_statement_completed
    - sp_statement_completed
    - sql_batch_completed
    - rpc_completed
    - scan_stopped
    - query_store_begin_persist_runtime_stat
    - zapytanie-store_execution_runtime_info
- ActualPageServerReads/ActualPageServerReadAheads są dodawane do pliku XML planu zapytania dla rzeczywistych planów. Przykład:

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> Aby wyświetlić te atrybuty w oknie Właściwości planu zapytania, program SSMS 18,3 lub nowszy jest wymagany.

## <a name="virtual-file-stats-and-io-accounting"></a>Statystyka pliku wirtualnego i ewidencjonowanie aktywności we/wy

W Azure SQL Database [sys. dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF to podstawowy sposób monitorowania SQL Server we/wy. Charakterystyki we/wy w ramach skalowania różnią się w zależności od [architektury rozproszonej](sql-database-service-tier-hyperscale.md#distributed-functions-architecture). W tej sekcji skupiamy się na operacjach we/wy (odczyt i zapis) do plików danych, jak pokazano w tym DMF. W ramach skalowania każdy plik danych widoczny w tym DMF odpowiada serwerowi strony zdalnej. Pamięć podręczna RBPEX określona tutaj jest lokalną pamięcią podręczną opartą na dyskach SSD, która nie jest pamięcią podręczną w replice obliczeniowej.

### <a name="local-rbpex-cache-usage"></a>Użycie lokalnej pamięci podręcznej RBPEX

Lokalna pamięć podręczna RBPEX istnieje w replice obliczeniowej na lokalnym magazynie dysków SSD. W ten sposób operacje we/wy względem tej pamięci podręcznej są szybsze niż we/wy względem zdalnych serwerów stron. Obecnie plik [sys. dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) w bazie danych w ramach skalowania zawiera specjalny wiersz zgłaszania we/wy do lokalnej pamięci podręcznej RBPEX w replice obliczeniowej. Ten wiersz ma wartość 0 dla kolumn `database_id` i `file_id`. Na przykład poniższe zapytanie zwraca statystykę użycia RBPEX od momentu uruchomienia bazy danych.

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

Stosunek liczby odczytów wykonanych na RBPEX do zagregowanych odczytów wykonanych na wszystkich innych plikach danych zapewnia Współczynnik trafień pamięci podręcznej RBPEX.

### <a name="data-reads"></a>Odczyty danych

- Gdy odczyty są wydawane przez aparat SQL Server w replice obliczeniowej, mogą one być obsługiwane przez lokalną pamięć podręczną RBPEX lub przez zdalne serwery stron lub przez kombinację dwóch, jeśli odczytywane są wiele stron.
- Gdy replika obliczeniowa odczytuje niektóre strony z określonego pliku, na przykład file_id 1, jeśli dane znajdują się wyłącznie w lokalnej pamięci podręcznej RBPEX, wszystkie operacje we/wy dla tego odczytu są uwzględniane w odniesieniu do file_id 0 (RBPEX). Jeśli niektóre części danych znajdują się w lokalnej pamięci podręcznej RBPEX, a niektóre części znajdują się na zdalnym serwerze stron, w ramach operacji we/wy jest uwzględniana wartość file_id 0 dla części obsługiwanej przez RBPEX, a część obsługiwana przez zdalny serwer strony ma na celu file_id 1. 
- Gdy replika obliczeń żąda strony o określonym [numerze LSN](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/) z serwera stronicowania, jeśli serwer stronicowania nie został przechwycony do żądanego numeru LSN, odczyt w replice obliczeniowej zaczeka, aż serwer stronicowania przejdzie przed zwróceniem strony do repliki obliczeniowej. W przypadku dowolnego odczytu z serwera stronicowania w replice obliczeniowej zobaczysz typ oczekiwania PAGEIOLATCH_ *, jeśli czeka na tę operację we/wy. W ramach skalowania ten czas oczekiwania obejmuje zarówno czas na przechwycenie żądanej strony na serwerze stronicowania, jak i czas wymagany do przetransferowania strony z serwera stronicowania do repliki obliczeniowej.
- Duże odczyty, takie jak Odczyt z wyprzedzeniem, często są wykonywane przy użyciu [odczytu "punktowego zbierania"](/sql/relational-databases/reading-pages/). Pozwala to na odczyt maksymalnie 4 MB stron w danym momencie, uważany za pojedynczy odczyt w aparacie SQL Server. Jednak gdy odczytywane dane są w RBPEX, te odczyty są rozliczane jako wiele pojedynczych odczytów 8 KB, ponieważ pula buforów i RBPEX zawsze używają 8 KB stron. W związku z tym liczba odczytów systemu IOs, które są widoczne dla RBPEX, może być większa niż rzeczywista liczba urządzeń z systemem IOs wykonywanych przez aparat.

### <a name="data-writes"></a>Zapisy danych

- Podstawowa replika obliczeniowa nie zapisuje bezpośrednio na serwerach stron. Zamiast tego rekordy dzienników z usługi log są odtwarzane na odpowiednich serwerach stron. 
- Zapisy, które zachodzą w replice obliczeniowej, są głównie zapisywane w lokalnym RBPEX (file_id 0). W przypadku operacji zapisu w plikach logicznych, które są większe niż 8 KB, innymi słowy, w których wykonywane jest [zbieranie](/sql/relational-databases/writing-pages/)zapisów, każda operacja zapisu jest tłumaczona na wiele pojedynczych zapisów o rozmiarze 8 KB do RBPEX, ponieważ pula buforów i RBPEX zawsze używają 8 KB stron. W związku z tym liczba operacji zapisu dla systemu IOs, które są widoczne dla RBPEX, może być większa niż rzeczywista liczba urządzeń z systemem IOs wykonywanych przez aparat.
- Pliki inne niż RBPEX lub pliki danych inne niż file_id 0, które odpowiadają serwerom stron, również wyświetlają zapisy. W warstwie usług skalowania, te zapisy są symulowane, ponieważ repliki obliczeniowe nigdy nie zapisują bezpośrednio na serwerach stron. Operacje we/wy zapisu są uwzględniane w miarę ich występowania w replice obliczeniowej, ale opóźnienie dla plików danych innych niż file_id 0 nie odzwierciedla rzeczywistego opóźnienia zapisów serwera stronicowania.

### <a name="log-writes"></a>Zapisy dziennika

- W podstawowym obliczeniu zapis w dzienniku jest uwzględniany w file_id 2 programu sys. dm_io_virtual_file_stats. Zapis w dzienniku w podstawowym obliczeniu jest zapisem w strefie wyładunkowej dziennika.
- Rekordy dziennika nie są zaostrzone w replice pomocniczej przy zatwierdzeniu. W ramach skalowania dziennik jest stosowany przez usługę log do asynchronicznych replik pomocniczych. Ponieważ zapisy dziennika nie są faktycznie wykonywane w replikach pomocniczych, wszelkie ewidencjonowanie aktywności systemu IOs w replikach pomocniczych jest tylko do celów śledzenia.

## <a name="data-io-in-resource-utilization-statistics"></a>Operacje we/wy danych w statystyce wykorzystania zasobów

W przypadku bazy danych bez skalowania połączone operacje odczytu i zapisu dla plików danych w odniesieniu do limitu liczby operacji we/wy danych [zarządzania zasobami](/azure/sql-database/sql-database-resource-limits-database-server#resource-governance) są raportowane w obszarze [sys. dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) i [sys. resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) widoki w kolumnie `avg_data_io_percent`. Ta sama wartość jest raportowana w portalu jako _wartość procentowa danych we/wy_. 

W bazie danych w postaci wieloskali ta kolumna zawiera raport dotyczący użycia operacji we/wy na sekundę, w odniesieniu do limitu magazynu lokalnego tylko w przypadku repliki obliczeniowej, w odniesieniu do RBPEX i `tempdb`. Wartość 100% w tej kolumnie wskazuje, że zarządzanie zasobami ogranicza liczbę operacji wejścia/wyjścia magazynu lokalnego. Jeśli jest to skorelowane z problemem z wydajnością, Dostosuj obciążenie w celu wygenerowania mniejszej wartości we/wy lub Zwiększ cel usługi bazy danych, aby zwiększyć maksymalny [Limit](sql-database-vcore-resource-limits-single-databases.md)liczby _IOPS danych_ na potrzeby zarządzania zasobami. W przypadku zarządzania zasobami RBPEX odczytuje i zapisuje system liczy poszczególne 8 KB IOs, a nie większe IOs, które mogą być wystawiane przez aparat SQL Server. 

Operacje we/wy danych względem zdalnych serwerów stron nie są zgłaszane w widokach wykorzystania zasobów lub w portalu, ale są raportowane w pliku [sys. dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) , jak wspomniano wcześniej.


## <a name="additional-resources"></a>Zasoby dodatkowe

- W przypadku limitów zasobów rdzeń wirtualny dla pojedynczej bazy danych na potrzeby skalowania w poziomie można zobaczyć [limity rdzeń wirtualny warstwy usługi](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5) .
- Aby uzyskać Azure SQL Database dostrajania wydajności, zobacz [wydajność zapytań w Azure SQL Database](sql-database-performance-guidance.md)
- Aby dostroić wydajność przy użyciu magazynu zapytań, zobacz [monitorowanie wydajności za pomocą magazynu zapytań](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/)
- Aby poznać skrypty monitorowania DMV, zobacz [monitorowanie wydajności Azure SQL Database przy użyciu dynamicznych widoków zarządzania](sql-database-monitoring-with-dmvs.md)
