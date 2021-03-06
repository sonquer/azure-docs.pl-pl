---
title: Zadania Elastic Database (wersja zapoznawcza)
description: Skonfiguruj zadania Elastic Database (wersja zapoznawcza), aby uruchamiać skrypty Transact-SQL (T-SQL) w zestawie co najmniej jednej bazy danych Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: srinia
ms.author: srinia
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 633c3ffc8e266087c88116a15c43469727a9a50d
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133648"
---
# <a name="create-configure-and-manage-elastic-jobs"></a>Tworzenie, Konfigurowanie i zarządzanie zadaniami elastycznymi

W tym artykule przedstawiono sposób tworzenia i konfigurowania zadań elastycznych oraz zarządzania nimi.

Jeśli nie korzystasz z zadań elastycznych, [Dowiedz się więcej na temat koncepcji automatyzacji zadań w Azure SQL Database](sql-database-job-automation-overview.md).

## <a name="create-and-configure-the-agent"></a>Tworzenie i konfigurowanie agenta

1. Utwórz lub zidentyfikuj pustą bazę danych SQL w warstwie S0 lub wyższej. Ta baza danych będzie używana jako *baza danych zadań* podczas tworzenia agenta zadań elastycznych.
2. Utwórz agenta elastycznego zadania w [portalu](https://portal.azure.com/#create/Microsoft.SQLElasticJobAgent) lub za pomocą [programu PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent).

   ![Tworzenie agenta zadań elastycznych](media/elastic-jobs-overview/create-elastic-job-agent.png)

## <a name="create-run-and-manage-jobs"></a>Tworzenie i uruchamianie zadań oraz zarządzanie nimi

1. Utwórz poświadczenie wykonywania zadania w *bazie danych zadań* przy użyciu [programu PowerShell](elastic-jobs-powershell.md) lub [T-SQL](elastic-jobs-tsql.md#create-a-credential-for-job-execution).
2. Zdefiniuj grupę docelową (bazy danych, dla których chcesz uruchomić zadanie) przy użyciu [programu PowerShell](elastic-jobs-powershell.md) lub [T-SQL](elastic-jobs-tsql.md#create-a-target-group-servers).
3. Utwórz poświadczenia agenta zadań w każdej bazie danych, w której będzie wykonywane zadanie [(dodaj użytkownika lub rolę do każdej bazy danych w grupie)](sql-database-control-access.md). Aby uzyskać przykład, zobacz [samouczek programu PowerShell](elastic-jobs-powershell.md).
4. Utwórz zadanie przy użyciu [programu PowerShell](elastic-jobs-powershell.md) lub [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases).
5. Dodaj kroki zadania za pomocą programu [PowerShell](elastic-jobs-powershell.md) lub języka [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases).
6. Uruchom zadanie przy użyciu [programu PowerShell](elastic-jobs-powershell.md#run-the-job) lub [T-SQL](elastic-jobs-tsql.md#begin-ad-hoc-execution-of-a-job).
7. Monitoruj stan wykonywania zadania za pomocą portalu, [programu PowerShell](elastic-jobs-powershell.md#monitor-status-of-job-executions) lub [T-SQL](elastic-jobs-tsql.md#monitor-job-execution-status).

   ![Portal](media/elastic-jobs-overview/elastic-job-executions-overview.png)

## <a name="credentials-for-running-jobs"></a>Poświadczenia na potrzeby uruchamiania zadań

Za pomocą [poświadczeń o zakresie bazy danych](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) zadania łączą się z bazami danych określonymi przez grupę docelową w momencie wykonania. Jeśli grupa docelowa obejmuje serwery lub pule, te poświadczenia o zakresie bazy danych są używane do łączenia się z bazą danych master w celu wyliczenia dostępnych baz danych.

Konfigurowanie odpowiednich poświadczeń służących do uruchamiania zadania może wydawać się nieco mylące, więc należy mieć na uwadze następujące kwestie:

- Poświadczenia o zakresie bazy danych należy utworzyć w *bazie danych zadań*.
- Aby zadanie zakończyło **się pomyślnie, wszystkie docelowe bazy danych muszą mieć [odpowiednie uprawnienia](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) do ukończenia zadania** (`jobuser` na poniższym diagramie).
- Poświadczenia mogą być ponownie używane między zadaniami, a hasła poświadczeń są szyfrowane i zabezpieczone przez użytkowników, którzy mają dostęp tylko do odczytu do obiektów zadań.

Poniższa ilustracja ułatwia zrozumienie i ustawienie odpowiednich poświadczeń zadań. **Pamiętaj, aby utworzyć użytkownika w każdej bazie danych (wszystkie *docelowe bazy danych użytkowników*), w której ma być uruchamiane zadanie**.

![Poświadczenia zadań elastycznych](media/elastic-jobs-overview/job-credentials.png)

## <a name="security-best-practices"></a>Najlepsze rozwiązania dotyczące zabezpieczeń

Kilka uwag dotyczących najlepszych rozwiązań podczas pracy z zadaniami elastycznymi:

- Ogranicz użycie interfejsów API do tych zaufanych.
- Poświadczenia powinny mieć możliwie najmniejsze uprawnienia niezbędne do wykonania kroku zadania. Aby uzyskać więcej informacji, zobacz [SQL Server autoryzacji i uprawnień](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server).
- W przypadku korzystania z elementu członkowskiego serwera i/lub grupy docelowej puli zdecydowanie zaleca się utworzenie oddzielnego poświadczenia z prawami w bazie danych Master, aby wyświetlić/wyświetlić bazy danych, które są używane do rozszerzania listy baz danych serwerów i/lub pul przed wykonaniem zadania.

## <a name="agent-performance-capacity-and-limitations"></a>Wydajność agenta, pojemność i ograniczenia

Zadania elastyczne używają minimalnych zasobów obliczeniowych podczas oczekiwania na zakończenie długotrwałych zadań.

W zależności od rozmiaru grupy docelowej baz danych i żądanego czasu wykonywania zadania (liczba równoczesnych procesów roboczych) agent wymaga różnej ilości zasobów obliczeniowych i wydajności *bazy danych zadań* (im więcej elementów docelowych i większa liczba zadań, tym wymagana jest większa ilość zasobów obliczeniowych).

Wersja zapoznawcza jest obecnie ograniczona do 100 współbieżnych zadań.

### <a name="prevent-jobs-from-reducing-target-database-performance"></a>Zapobieganie zmniejszania wydajności docelowej bazy danych przez zadania

Aby zapewnić, że zasoby nie będą przeciążone podczas uruchamiania zadań w ramach baz danych w elastycznej puli SQL, możliwe jest skonfigurowanie zadań w taki sposób, aby ograniczana była liczba baz danych, w ramach których mogą one być jednocześnie uruchamiane.

Ustaw liczbę współbieżnych baz danych, dla których uruchomione jest zadanie, ustawiając parametr `@max_parallelism` procedury składowanej `sp_add_jobstep` w języku T-SQL lub `Add-AzSqlElasticJobStep -MaxParallelism` w programie PowerShell.

## <a name="best-practices-for-creating-jobs"></a>Najlepsze rozwiązania dotyczące tworzenia zadań

### <a name="idempotent-scripts"></a>Skrypty idempotentne
Skrypty T-SQL zadania muszą być [idempotentne](https://en.wikipedia.org/wiki/Idempotence). **Idempotentność** oznacza, że jeśli skrypt zakończy się pomyślnie i zostanie uruchomiony ponownie, da to taki sam wynik. Skrypt może zakończyć się niepowodzeniem z powodu przejściowych problemów z siecią. W takim przypadku zadanie automatycznie ponowi próbę uruchomienia skryptu wstępnie zdefiniowaną liczbę razy przed zaniechaniem. Wynik działania skryptu idempotentnego będzie taki sam, nawet jeśli zostanie pomyślnie uruchomiony dwukrotnie (lub większą liczbę razy).

Prostym sposobem jest sprawdzenie istnienia obiektu przed jego utworzeniem.


```sql
IF NOT EXISTS (some_object)
    -- Create the object
    -- If it exists, drop the object before recreating it.
```

Podobnie musi być możliwe pomyślne wykonanie skryptu przez logiczne testowanie wszystkich znalezionych warunków i reagowanie na nie.



## <a name="next-steps"></a>Następne kroki

- [Tworzenie zadań elastycznych i zarządzanie nimi za pomocą programu PowerShell](elastic-jobs-powershell.md)
- [Tworzenie zadań elastycznych i zarządzanie nimi za pomocą języka Transact-SQL (T-SQL)](elastic-jobs-tsql.md)
