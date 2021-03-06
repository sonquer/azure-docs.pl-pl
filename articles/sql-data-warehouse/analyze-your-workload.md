---
title: Analizowanie obciążenia
description: Techniki analizowania priorytetyzacji zapytań dla obciążenia w Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 14e53c1ebe63fac0f7c8e29f66ee5aa0cb3b9526
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693119"
---
# <a name="analyze-your-workload-in-azure-sql-data-warehouse"></a>Analizowanie obciążenia w Azure SQL Data Warehouse

Techniki analizowania obciążeń w Azure SQL Data Warehouse.

## <a name="resource-classes"></a>Klasy zasobów

SQL Data Warehouse udostępnia klasy zasobów do przypisywania zasobów systemowych do zapytań.  Aby uzyskać więcej informacji na temat klas zasobów, zobacz [klasy zasobów & zarządzanie obciążeniami](resource-classes-for-workload-management.md).  Zapytania zaczekają, jeśli klasa zasobów przypisana do zapytania będzie potrzebowała więcej zasobów niż jest to obecnie dostępne.

## <a name="queued-query-detection-and-other-dmvs"></a>Wykrywanie zapytań w kolejce i inne widoków DMV

Aby identyfikować zapytania oczekujące w kolejce współbieżności, można użyć `sys.dm_pdw_exec_requests` DMV. Zapytania oczekujące na gniazdo współbieżności mają stan **zawieszone**.

```sql
SELECT  r.[request_id]                           AS Request_ID
,       r.[status]                               AS Request_Status
,       r.[submit_time]                          AS Request_SubmitTime
,       r.[start_time]                           AS Request_StartTime
,       DATEDIFF(ms,[submit_time],[start_time])  AS Request_InitiateDuration_ms
,       r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

Role zarządzania obciążeniami można przeglądać za pomocą `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

Następujące zapytanie pokazuje, do której roli każdy użytkownik jest przypisany.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

SQL Data Warehouse ma następujące typy oczekiwania:

* **LocalQueriesConcurrencyResourceType**: zapytania, które znajdują się poza platformą miejsc współbieżności. Zapytania DMV i funkcje systemowe, takie jak `SELECT @@VERSION` są przykładami zapytań lokalnych.
* **UserConcurrencyResourceType**: zapytania, które znajdują się wewnątrz struktury gniazda współbieżności. Zapytania dotyczące tabel użytkowników końcowych przedstawiają przykłady, które mogłyby używać tego typu zasobu.
* **DmsConcurrencyResourceType**: czeka na wyniki operacji przenoszenia danych.
* **BackupConcurrencyResourceType**: to poczekanie wskazuje, że tworzona jest kopia zapasowa bazy danych. Maksymalna wartość dla tego typu zasobu to 1. Jeśli w tym samym czasie zażądano wielu kopii zapasowych, kolejka innych. Ogólnie rzecz biorąc zalecamy minimalny czas między kolejnymi migawkami 10 minut. 

`sys.dm_pdw_waits` DMV może służyć do sprawdzenia, które zasoby oczekują na żądanie.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]                                           AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,       SESSION_ID()                                       AS Current_session
,       s.[status]                                         AS Session_status
,       s.[login_name]
,       s.[query_count]
,       s.[client_id]
,       s.[sql_spid]
,       r.[command]                                        AS Request_command
,       r.[label]
,       r.[status]                                         AS Request_status
,       r.[submit_time]
,       r.[start_time]
,       r.[end_compile_time]
,       r.[end_time]
,       DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,       DATEDIFF(ms,r.[start_time],r.[end_compile_time])   AS Request_compile_time_ms
,       DATEDIFF(ms,r.[end_compile_time],r.[end_time])     AS Request_execution_time_ms
,       r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID()
;
```

`sys.dm_pdw_resource_waits` DMV pokazuje informacje o poczekaniu dla danego zapytania. Czas oczekiwania zasobu mierzy czas oczekiwania na udostępnienie zasobów. Czas oczekiwania na sygnał to czas potrzebny na bazowe serwery SQL do zaplanowania zapytania na procesor CPU.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID()
;
```

Można również użyć `sys.dm_pdw_resource_waits` DMV obliczenia liczby miejsc współbieżności przyznanych.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots
FROM    sys.[dm_pdw_resource_waits]
WHERE   [state]           = 'Granted'
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

`sys.dm_pdw_wait_stats` DMV może służyć do analizy trendu historycznego oczekiwania.

```sql
SELECT   w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w
;
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zarządzania użytkownikami i zabezpieczeniami bazy danych, zobacz temat [Zabezpieczanie bazy danych w SQL Data Warehouse](sql-data-warehouse-overview-manage-security.md). Aby uzyskać więcej informacji o tym, jak większe klasy zasobów mogą ulepszyć jakość klastrowanego indeksu magazynu kolumn, zobacz Ponowne [Kompilowanie indeksów w celu zwiększenia jakości segmentu](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).
