---
title: Tworzenie kopii zapasowych i przywracanie migawek, geograficznie nadmiarowych
description: Dowiedz się, jak wykonywanie kopii zapasowych i przywracanie działa w Azure SQL Data Warehouse. Użyj kopii zapasowych magazynu danych, aby przywrócić magazyn danych do punktu przywracania w regionie podstawowym. Użyj geograficznie nadmiarowych kopii zapasowych do przywrócenia w innym regionie geograficznym.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 10/21/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019"
ms.openlocfilehash: f37ca56f7875dcb6ab254a11b859c3e85f6a1dd0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686152"
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Tworzenie kopii zapasowych i przywracanie w Azure SQL Data Warehouse

Dowiedz się, jak używać funkcji tworzenia kopii zapasowych i przywracania w programie Azure SQL Data Warehouse. Za pomocą punktów przywracania magazynu danych Odzyskaj lub skopiuj magazyn danych do poprzedniego stanu w regionie podstawowym. Użyj geograficznie nadmiarowych kopii zapasowych magazynu danych do przywrócenia w innym regionie geograficznym.

## <a name="what-is-a-data-warehouse-snapshot"></a>Co to jest migawka magazynu danych

*Migawka magazynu danych* tworzy punkt przywracania, którego można użyć do odzyskania lub skopiowania magazynu danych do poprzedniego stanu.  Ponieważ SQL Data Warehouse jest systemem rozproszonym, migawka magazynu danych składa się z wielu plików znajdujących się w usłudze Azure Storage. Migawki przechwytują zmiany przyrostowe z danych przechowywanych w magazynie danych.

*Przywracanie magazynu danych* jest nowym magazynem danych, który jest tworzony na podstawie punktu przywracania istniejącego lub usuniętego magazynu danych. Przywrócenie magazynu danych jest istotną częścią strategii ciągłości działania i odzyskiwania po awarii, ponieważ powoduje ponowne utworzenie danych po przypadkowe uszkodzenia lub usunięciu. Magazyn danych jest również zaawansowanym mechanizmem tworzenia kopii magazynu danych na potrzeby testowania lub projektowania.  Stawki za SQL Data Warehouse przywracania mogą się różnić w zależności od rozmiaru bazy danych i lokalizacji źródłowego i docelowego magazynu danych. 

## <a name="automatic-restore-points"></a>Automatyczne punkty przywracania

Migawki to wbudowana funkcja usługi, która tworzy punkty przywracania. Nie musisz włączać tej funkcji. Jednak magazyn danych powinien znajdować się w stanie aktywnym do tworzenia punktów przywracania. Jeśli magazyn danych jest wstrzymany często, automatyczne punkty przywracania mogą nie zostać utworzone, dlatego należy utworzyć punkt przywracania zdefiniowany przez użytkownika przed wstrzymaniem magazynu danych. Automatyczne punkty przywracania obecnie nie mogą zostać usunięte przez użytkowników, ponieważ usługa używa tych punktów przywracania do obsługi umowy SLA na potrzeby odzyskiwania.

SQL Data Warehouse wykonuje migawki magazynu danych przez cały dzień tworzenia punktów przywracania dostępnych przez siedem dni. Nie można zmienić tego okresu przechowywania. SQL Data Warehouse obsługuje osiem godzin cel punktu odzyskiwania (RPO). Magazyn danych można przywrócić w regionie podstawowym z jednej z migawek wykonanych w ciągu ostatnich siedmiu dni.

Aby sprawdzić, kiedy Ostatnia migawka została rozpoczęta, Uruchom to zapytanie na SQL Data Warehouse online.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Punkty przywracania zdefiniowane przez użytkownika

Ta funkcja umożliwia ręczne wyzwalanie migawek do tworzenia punktów przywracania magazynu danych przed i po dużych modyfikacjach. Ta funkcja zapewnia, że punkty przywracania są logicznie spójne, co zapewnia dodatkową ochronę danych w przypadku wszelkich przerw w obciążeniu lub błędów użytkowników w celu szybkiego odzyskiwania. Punkty przywracania zdefiniowane przez użytkownika są dostępne przez siedem dni i są automatycznie usuwane w Twoim imieniu. Nie można zmienić okresu przechowywania zdefiniowanych przez użytkownika punktów przywracania. **42 punkty przywracania zdefiniowane przez użytkownika** są gwarantowane w dowolnym momencie, dlatego należy je [usunąć](https://go.microsoft.com/fwlink/?linkid=875299) przed utworzeniem innego punktu przywracania. Możesz wyzwolić migawki, aby utworzyć punkty przywracania zdefiniowane przez użytkownika za pomocą [programu PowerShell](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint#examples) lub Azure Portal.

> [!NOTE]
> Jeśli wymagane jest przywracanie punktów dłużej niż 7 dni, należy zagłosować na [tę funkcję.](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points) Można również utworzyć punkt przywracania zdefiniowany przez użytkownika i przywrócić go z nowo utworzonego punktu przywracania do nowego magazynu danych. Po przywróceniu będziesz mieć magazyn danych w trybie online i można go wstrzymać w nieskończoność, aby zaoszczędzić koszty obliczeniowe. Wstrzymana baza danych wiąże się z opłatami za magazyn według stawki za Premium Storage platformy Azure. Jeśli potrzebujesz aktywnej kopii przywróconego magazynu danych, możesz wznowić działanie, które powinno trwać tylko kilka minut.

### <a name="restore-point-retention"></a>Przechowywanie punktów przywracania

Poniżej znajdują się szczegółowe informacje dotyczące okresów przechowywania punktów przywracania:

1. SQL Data Warehouse usuwa punkt przywracania, gdy trafi na 7-dniowy okres przechowywania **i** ma co najmniej 42 całkowitej liczby punktów przywracania (w tym zdefiniowane przez użytkownika i automatyczne)
2. Migawki nie są wykonywane, gdy magazyn danych jest wstrzymany
3. Wiek punktu przywracania jest mierzony przez bezwzględne dni kalendarzowe od momentu, w którym następuje punkt przywracania, w tym gdy magazyn danych jest wstrzymany
4. W dowolnym momencie magazyn danych ma możliwość przechowywania do 42 punktów przywracania zdefiniowanych przez użytkownika i 42 punktów przywracania automatycznego, o ile te punkty przywracania nie osiągnęły 7-dniowego okresu przechowywania
5. Jeśli wykonywana jest migawka, magazyn danych jest wstrzymywany przez więcej niż 7 dni, a następnie wznawia, możliwe jest przywrócenie punktu przywracania do momentu, aż będzie 42 całkowite punkty przywracania (w tym zdefiniowane przez użytkownika i automatyczne)

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>Przechowywanie migawek w przypadku porzucenia magazynu danych

Podczas upuszczania magazynu danych SQL Data Warehouse tworzy ostateczną migawkę i zapisuje ją przez siedem dni. Magazyn danych można przywrócić do końcowego punktu przywracania utworzonego podczas usuwania. Jeśli magazyn danych zostanie porzucony w stanie wstrzymania, migawka nie jest wykonywana. W tym scenariuszu należy utworzyć zdefiniowany przez użytkownika punkt przywracania przed usunięciem magazynu danych.

> [!IMPORTANT]
> Usunięcie logicznego wystąpienia programu SQL Server spowoduje również usunięcie wszystkich baz danych, które należą do wystąpienia, i nie będzie można ich odzyskać. Nie można przywrócić usuniętego serwera.

## <a name="geo-backups-and-disaster-recovery"></a>Tworzenie kopii zapasowych i odzyskiwanie po awarii

SQL Data Warehouse wykonuje geograficzną kopię zapasową raz dziennie w [sparowanym centrum danych](../best-practices-availability-paired-regions.md). Cel punktu odzyskiwania w przypadku przywracania geograficznego wynosi 24 godziny. Można przywrócić geograficzną kopię zapasową do serwera w dowolnym innym regionie, w którym SQL Data Warehouse jest obsługiwana. Geograficzna kopia zapasowa zapewnia możliwość przywrócenia magazynu danych na wypadek, gdyby nie można było uzyskać dostępu do punktów przywracania w regionie podstawowym.

> [!NOTE]
> Jeśli potrzebujesz krótszego celu punktu odzyskiwania dla geograficznie kopii zapasowych, zagłosuj na tę funkcję [tutaj](https://feedback.azure.com/forums/307516-sql-data-warehouse). Można również utworzyć zdefiniowany przez użytkownika punkt przywracania i przywrócić go z nowo utworzonego punktu przywracania do nowego magazynu danych w innym regionie. Po przywróceniu będziesz mieć magazyn danych w trybie online i można go wstrzymać w nieskończoność, aby zaoszczędzić koszty obliczeniowe. Wstrzymana baza danych wiąże się z opłatami za magazyn według stawki za Premium Storage platformy Azure. Jeśli potrzebujesz aktywnej kopii hurtowni danych, możesz wznowić działanie, które powinno trwać tylko kilka minut.

## <a name="backup-and-restore-costs"></a>Koszty tworzenia kopii zapasowej i przywracania

Na rachunku za platformę Azure zostanie wystawiony element line for Storage i element line dla magazynu odzyskiwania po awarii. Opłata za magazyn to łączny koszt przechowywania danych w regionie podstawowym wraz ze zmianami przyrostowymi przechwytywanymi przez migawki. Aby uzyskać bardziej szczegółowy opis sposobu, w jaki są rozliczane migawki, zapoznaj się z tematem [jak naliczanie opłat za migawki](https://docs.microsoft.com/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?redirectedfrom=MSDN#snapshot-billing-scenarios). Opłata za nadmiarowy geograficznie obejmuje koszt przechowywania kopii zapasowych.  

Łączny koszt podstawowego magazynu danych i siedem dni zmian migawek jest zaokrąglany do najbliższej TB. Na przykład jeśli magazyn danych to 1,5 TB, a migawki przechwytuje 100 GB, opłaty są naliczane za 2 TB danych w ramach stawek za usługę Azure Premium Storage.

W przypadku korzystania z magazynu geograficznie nadmiarowego otrzymujesz oddzielną opłatą za magazyn. Magazyn Geograficznie nadmiarowy jest rozliczany zgodnie ze standardową szybkością magazynowania geograficznego do odczytu (RA-GRS).

Aby uzyskać więcej informacji na temat cennika SQL Data Warehouse, zobacz [SQL Data Warehouse Cennik](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/). Nie jest naliczana opłata za dane wychodzące podczas przywracania między regionami.

## <a name="restoring-from-restore-points"></a>Przywracanie z punktów przywracania

Każda migawka tworzy punkt przywracania, który reprezentuje czas rozpoczęcia migawki. Aby przywrócić magazyn danych, należy wybrać punkt przywracania i wydać polecenie przywracania.  

Możesz zachować przywrócony magazyn danych i bieżący lub usunąć jeden z nich. Jeśli chcesz zamienić bieżący magazyn danych na przywrócony magazyn danych, możesz zmienić jego nazwę przy użyciu [polecenia ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) z opcją Modify Name.

Aby przywrócić magazyn danych, zobacz [przywracanie magazynu danych przy użyciu Azure Portal](sql-data-warehouse-restore-database-portal.md), [przywracanie magazynu danych przy użyciu programu PowerShell](sql-data-warehouse-restore-database-powershell.md)lub [przywracanie magazynu danych przy użyciu interfejsów API REST](sql-data-warehouse-restore-database-rest-api.md).

Aby przywrócić usunięty lub wstrzymany magazyn danych, możesz [utworzyć bilet pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="cross-subscription-restore"></a>Przywracanie między subskrypcjami

Jeśli musisz bezpośrednio przywrócić subskrypcję, zagłosuj na tę funkcję w [tym miejscu](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore). Przywróć na inny serwer logiczny i ["Przenieś"](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) serwer między subskrypcjami, aby przeprowadzić przywracanie między subskrypcjami. 

## <a name="geo-redundant-restore"></a>Przywracanie nadmiarowe geograficznie

[Magazyn danych można przywrócić](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-from-geo-backup#restore-from-an-azure-geographical-region-through-powershell) do dowolnego regionu obsługującego SQL Data Warehouse na wybranym poziomie wydajności.

> [!NOTE]
> Aby wykonać przywracanie Geograficznie nadmiarowy, nie trzeba wymusić tej funkcji.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat planowania awarii, zobacz temat [ciągłość](../sql-database/sql-database-business-continuity.md) działania — Omówienie
