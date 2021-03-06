---
title: Rozpoznawanie różnic w języku T-SQL — migracja
description: Instrukcje języka Transact-SQL, które nie są w pełni obsługiwane w usłudze Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/03/2018
ms.openlocfilehash: 15c661a1ef917dcf73b5a86cd450c94a35b08c88
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822494"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Rozwiązywanie różnic w języku Transact-SQL podczas migracji do SQL Database

W przypadku [migrowania bazy danych](sql-database-single-database-migrate.md) z programu SQL Server do usługi Azure SQL Server można stwierdzić, że baza danych wymaga przeprowadzenia kilku ponownych migracji przed migracją SQL Server. Ten artykuł zawiera wskazówki ułatwiające wykonywanie tych czynności w ramach tego zadania i zrozumienie podstawowych przyczyn, dla których konieczne jest ponowne inżynierowanie. Aby wykryć niezgodności, użyj [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Omówienie

Większość funkcji języka Transact-SQL, które są używane przez aplikacje, jest w pełni obsługiwana zarówno w Microsoft SQL Server, jak i Azure SQL Database. Na przykład podstawowe składniki SQL, takie jak typy danych, operatory, ciągi, operacje arytmetyczne, logiczne i kursorowe, działają identycznie w SQL Server i SQL Database. Istnieją jednak pewne różnice w języku T-SQL w przypadku elementów języka DDL (definicja danych) i DML (Language operowania danymi), które wystąpiły w instrukcjach i zapytaniach języka T-SQL, które są tylko częściowo obsługiwane (które omówiono w dalszej części tego artykułu).

Ponadto istnieją pewne funkcje i składnia, które nie są obecnie obsługiwane, ponieważ Azure SQL Database zaprojektowano w celu izolowania funkcji z zależności od bazy danych Master i systemu operacyjnego. W związku z tym większość działań na poziomie serwera jest nieodpowiedni dla SQL Database. Instrukcje i Opcje języka T-SQL są niedostępne, jeśli konfigurują opcje na poziomie serwera, składniki systemu operacyjnego lub określają konfigurację systemu plików. Gdy takie możliwości są wymagane, odpowiednia alternatywa jest często dostępna w inny sposób od SQL Database lub z innej funkcji lub usługi platformy Azure.

Na przykład wysoka dostępność jest wbudowana w Azure SQL Database przy użyciu technologii podobnej do [zawsze dostępnych grup dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Instrukcje języka T-SQL powiązane z grupami dostępności nie są obsługiwane przez SQL Database, a dynamiczne widoki zarządzania powiązane z zawsze włączonymi grupami dostępności również nie są obsługiwane.

Aby zapoznać się z listą funkcji obsługiwanych i nieobsługiwanych przez SQL Database, zobacz [Azure SQL Database porównanie funkcji](sql-database-features.md). Lista na tej stronie zawiera uzupełnienie artykułu wskazówki i funkcje oraz koncentruje się na instrukcjach języka Transact-SQL.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Instrukcje języka Transact-SQL zawierające częściowe różnice

Są dostępne podstawowe instrukcje języka DDL (Data Definition Language), ale niektóre instrukcje języka DDL mają rozszerzenia związane z rozmieszczeniem dysku i nieobsługiwanymi funkcjami.

- Instrukcje CREATE i ALTER DATABASE zawierają ponad trzy opcje. Instrukcje obejmują umieszczanie plików, FILESTREAM i opcje brokera usług, które mają zastosowanie tylko do SQL Server. Może to nie mieć znaczenia w przypadku tworzenia baz danych przed migracją, ale Jeśli migrujesz kod T-SQL, który tworzy bazy danych, należy porównać [Tworzenie bazy danych (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) z składnią SQL Server w programie [create Database (SQL Server Transact-SQL) ](https://msdn.microsoft.com/library/ms176061.aspx)aby upewnić się, że wszystkie opcje, które są używane, są obsługiwane. Opcja Utwórz bazę danych dla Azure SQL Database ma także cele usługi i elastyczne skalowanie, które mają zastosowanie tylko do SQL Database.
- Instrukcje CREATE i ALTER TABLE zawierają opcje obiektu FileTable, których nie można używać na SQL Database, ponieważ nie jest obsługiwany element FILESTREAM.
- Instrukcje CREATE i ALTER login są obsługiwane, ale SQL Database nie oferuje wszystkich opcji. Aby zapewnić lepszą przenośność bazy danych, SQL Database zachęca do korzystania z użytkowników zawartej bazy danych zamiast nazw logowania, gdy tylko jest to możliwe. Aby uzyskać więcej informacji, zobacz [Tworzenie/zmiana nazwy logowania](https://msdn.microsoft.com/library/ms189828.aspx) oraz [kontrolowanie i udzielanie dostępu do bazy danych](sql-database-manage-logins.md).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Składnia języka Transact-SQL nie jest obsługiwana w Azure SQL Database

Oprócz instrukcji języka Transact-SQL związanych z nieobsługiwanymi funkcjami opisanymi w [Azure SQL Database porównaniu funkcji](sql-database-features.md), następujące instrukcje i grupy instrukcji nie są obsługiwane. W związku z tym, jeśli baza danych, która ma zostać zmigrowana, korzysta z jednej z następujących funkcji, należy odtworzyć swój język T-SQL w celu wyeliminowania tych funkcji i instrukcji języka T-SQL.

- Sortowanie obiektów systemu
- Powiązane połączenie: punkty końcowe. Usługa SQL Database nie obsługuje uwierzytelniania systemu Windows, ale obsługuje podobne uwierzytelnianie usługi Azure Active Directory. Niektóre typy uwierzytelniania wymagają najnowszej wersji usługi SSMS. Aby uzyskać więcej informacji, zobacz artykuł [Connecting to SQL Database or SQL Data Warehouse By Using Azure Active Directory Authentication](sql-database-aad-authentication.md) (Nawiązywanie połączeń z usługami SQL Database lub SQL Data Warehouse przy użyciu uwierzytelniania usługi Azure Active Directory).
- Zapytania obejmujące wiele baz danych, korzystające z nazw trój- i czteroczęściowych. (Zapytania tylko do odczytu obejmujące wiele baz danych są obsługiwane za pomocą [zapytania elastycznej bazy danych](sql-database-elastic-query-overview.md)).
- Tworzenie łańcucha własności między wieloma bazami danych, ustawienie `TRUSTWORTHY`
- `EXECUTE AS LOGIN` Zamiast tego użyj instrukcji „EXECUTE AS USER”.
- Szyfrowanie jest obsługiwane z wyjątkiem rozszerzonego zarządzania kluczami
- Zdarzenia: zdarzenia, powiadomienia o zdarzeniach, powiadomienia o zapytaniach
- Położenie pliku: Składnia związana z rozmieszczeniem, rozmiarem i plikami bazy danych, które są automatycznie zarządzane przez Microsoft Azure.
- Wysoka dostępność: Składnia związana z wysoką dostępnością, która jest zarządzana przy użyciu konta Microsoft Azure. Obejmuje to składnię kopii zapasowych, przywracania, funkcji Always On, dublowania bazy danych, wysyłania dziennika oraz trybów odzyskiwania.
- Czytnik dzienników: Składnia, która opiera się na czytniku dziennika, który nie jest dostępny w SQL Database: replikacja wypychana, zmiana przechwytywania danych. Baza danych SQL Database może być subskrybentem artykułu replikacji wypychanej.
- Funkcje: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Sprzęt: Składnia dotycząca ustawień serwera związanych z sprzętem: takich jak pamięć, wątki robocze, koligacja procesora, flagi śledzenia. Zamiast nich używaj warstw usług i rozmiarów obliczeniowych.
- `KILL STATS JOB`
- nazwy `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE`i czterech części
- .NET Framework: integracja środowiska CLR z SQL Server
- Wyszukiwanie semantyczne
- Poświadczenia serwera: zamiast tego użyj [poświadczeń w zakresie bazy danych](https://msdn.microsoft.com/library/mt270260.aspx) .
- Elementy na poziomie serwera: role serwera, `sys.login_token`. Instrukcje `GRANT`, `REVOKE` i `DENY` w odniesieniu do uprawnień na poziomie serwera nie są dostępne, chociaż niektóre zostały zastąpione przez uprawnienia na poziomie bazy danych. Niektóre przydatne dynamiczne widoki zarządzania na poziomie serwera mają swoje odpowiedniki na poziomie bazy danych.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- Opcje polecenia `sp_configure` i instrukcja `RECONFIGURE`. Niektóre opcje są dostępne po użyciu instrukcji [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- Agent SQL Server: Składnia, która opiera się na agencie SQL Server lub bazie danych MSDB: alerty, operatory, centralne serwery zarządzania. Zamiast tego używaj skryptów, takich jak Azure PowerShell.
- Inspekcja SQL Server: zamiast tego użyj inspekcji SQL Database.
- Śledzenie programu SQL Server
- Flagi śledzenia: niektóre elementy flagi śledzenia zostały przeniesione do trybu zgodności.
- Debugowanie języka Transact-SQL
- Wyzwalacze: wyzwalacze zakresu serwera lub wyzwalacze logowania
- Instrukcja `USE`: aby zmienić kontekst bazy danych na inną bazę danych, należy wprowadzić nowe połączenie z nową bazą danych.

## <a name="full-transact-sql-reference"></a>Pełna dokumentacja języka Transact-SQL

Aby uzyskać więcej informacji na temat gramatyki Transact-SQL, użycia i przykładów, zobacz [Dokumentacja języka Transact-SQL (aparat bazy danych)](https://msdn.microsoft.com/library/bb510741.aspx) w SQL Server Books Online.

### <a name="about-the-applies-to-tags"></a>Tagi „Applies to” (Dotyczy)

Dokumentacja języka Transact-SQL zawiera artykuły powiązane z SQL Server wersjami 2008. Poniżej tytułu artykułu znajduje się pasek ikon zawierający cztery SQL Server platformy i wskazujące na zastosowanie. Na przykład grupy dostępności zostały wprowadzone w programie SQL Server 2012. Artykuł [Tworzenie grupy dostępności](https://msdn.microsoft.com/library/ff878399.aspx) wskazuje, że instrukcja ma zastosowanie do **SQL Server (Zaczynając od 2012)** . Instrukcja nie dotyczy programu SQL Server 2008, SQL Server 2008 R2, usługi Azure SQL Database, programu Azure SQL Data Warehouse ani Parallel Data Warehouse.

W niektórych przypadkach ogólna część artykułu może być używana w produkcie, ale istnieją niewielkie różnice między produktami. Różnice są wskazane w środkach w artykule stosownie do potrzeb. W niektórych przypadkach ogólna część artykułu może być używana w produkcie, ale istnieją niewielkie różnice między produktami. Różnice są wskazane w środkach w artykule stosownie do potrzeb. Na przykład artykuł Tworzenie WYZWALACZa jest dostępny w SQL Database. Jednak opcja **wszystkie serwery** dla wyzwalaczy na poziomie serwera wskazuje, że w SQL Database nie można używać wyzwalaczy na poziomie serwera. Zamiast tego Użyj wyzwalaczy na poziomie bazy danych.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z listą funkcji obsługiwanych i nieobsługiwanych przez SQL Database, zobacz [Azure SQL Database porównanie funkcji](sql-database-features.md). Lista na tej stronie zawiera uzupełnienie artykułu wskazówki i funkcje oraz koncentruje się na instrukcjach języka Transact-SQL.
