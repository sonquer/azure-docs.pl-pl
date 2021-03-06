---
title: Konfigurowanie synchronizacji danych
description: W tym samouczku przedstawiono sposób konfigurowania usługi Azure SQL Data Sync
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 01/14/2019
ms.openlocfilehash: 019ddbac1900856666b958d90b4395f25eb5ee84
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461547"
---
# <a name="tutorial-set-up-sql-data-sync-between-azure-sql-database-and-sql-server-on-premises"></a>Samouczek: Konfigurowanie SQL Data Sync między Azure SQL Database i SQL Server lokalnie

W tym samouczku dowiesz się, jak skonfigurować usługę Azure SQL Data Sync, tworząc grupę synchronizacji zawierającą zarówno wystąpienia Azure SQL Database, jak i SQL Server. Grupa synchronizacji jest skonfigurowana jako niestandardowa i synchronizowana zgodnie z ustawionym harmonogramem.

W samouczku założono, że masz co najmniej kilka wcześniejszych doświadczeń z SQL Database i SQL Server.

Aby zapoznać się z omówieniem SQL Data Sync, zobacz [synchronizowanie danych w chmurze i lokalnych bazach danych przy użyciu usługi Azure SQL Data Sync](sql-database-sync-data.md).

Przykłady programu PowerShell dotyczące konfigurowania SQL Data Sync można znaleźć w temacie [Jak synchronizować między bazami danych Azure SQL](scripts/sql-database-sync-data-between-sql-databases.md) lub [Azure SQL Database i SQL Server lokalną bazą danych](scripts/sql-database-sync-data-between-azure-onprem.md)

> [!IMPORTANT]
> Usługa Azure SQL Data Sync **nie** obsługuje obecnie wystąpienia zarządzanego usługi Azure SQL Database.

## <a name="create-sync-group"></a>Utwórz grupę synchronizacji

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby znaleźć bazę danych SQL. Wyszukaj i wybierz pozycję **bazy danych SQL**.

    ![Wyszukiwanie baz danych SQL, Microsoft Azure Portal](media/sql-database-get-started-sql-data-sync/search-for-sql-databases.png)

1. Wybierz bazę danych, która ma być używana jako baza danych usługi Hub do synchronizacji danych.

    ![Wybierz z listy usługi SQL Database, Microsoft Azure Portal](media/sql-database-get-started-sql-data-sync/select-sql-database.png)

    > [!NOTE]
    > Baza danych centrum jest centralnym punktem końcowym topologii synchronizacji, w którym grupa synchronizacji ma wiele punktów końcowych bazy danych. Wszystkie inne bazy danych członków z punktami końcowymi w grupie synchronizacji synchronizują się z centralną bazą danych.

1. W menu **bazy danych SQL** dla wybranej bazy danych wybierz pozycję **Synchronizuj z innymi bazami danych**.

    ![Synchronizuj z innymi bazami danych, bazami danych SQL, Microsoft Azure Portal](media/sql-database-get-started-sql-data-sync/sync-to-other-databases.png)

1. Na stronie **Synchronizuj z innymi bazami danych** wybierz pozycję **Nowa grupa synchronizacji**. Zostanie otwarta strona **Nowa grupa synchronizacji** z wyróżnioną pozycją **Utwórz grupę synchronizacji (krok 1)** .

   ![Ustawienia kroku 1](media/sql-database-get-started-sql-data-sync/stepone.png)

   Na stronie **Tworzenie grupy synchronizacji danych** Zmień następujące ustawienia:

   | Ustawienie                        | Opis |
   | ------------------------------ | ------------------------------------------------- |
   | **Nazwa grupy synchronizacji** | Wprowadź nazwę nowej grupy synchronizacji. Ta nazwa różni się od nazwy samej bazy danych. |
   | **Baza danych metadanych synchronizacji** | Wybierz opcję utworzenia bazy danych (zalecane) lub użycia istniejącej bazy danych.<br/><br/>W przypadku wybrania opcji **Nowa baza danych**wybierz pozycję **Utwórz nową bazę danych.** Następnie na stronie **SQL Database** Nadaj nazwę i skonfiguruj nową bazę danych, a następnie wybierz **przycisk OK**.<br/><br/>Jeśli wybierzesz opcję **Użyj istniejącej bazy danych**, wybierz bazę danych z listy. |
   | **Automatyczna synchronizacja** | Wybierz opcję **włączone** lub **wyłączone**.<br/><br/>W przypadku wybrania opcji **włączone**wprowadź liczbę, a następnie wybierz pozycję **sekundy**, **minuty**, **godziny**lub **dni** w sekcji **częstotliwość synchronizacji** . |
   | **Rozwiązywanie konfliktów** | Wybierz pozycję **centrum win** lub **element członkowski win**.<br/><br/>**Centrum win** oznacza, gdy występują konflikty, dane w centralnej bazie danych zastępują dane powodujące konflikt w bazie danych elementu członkowskiego.<br/><br/>**Członek win** oznacza, gdy występują konflikty, dane w bazie danych elementu zastępują dane powodujące konflikt w bazie danych centrum. |

   > [!NOTE]
   > Firma Microsoft zaleca, aby utworzyć nową, pustą bazę danych, która będzie używana jako **baza danych metadanych synchronizacji**. Synchronizacja danych tworzy tabele w tej bazie danych i uruchamia częste obciążenie. Ta baza danych jest udostępniana jako **baza danych metadanych synchronizacji** dla wszystkich grup synchronizacji w wybranym regionie i nie można zmienić bazy danych ani jej nazwy bez usuwania wszystkich grup synchronizacji i agentów synchronizacji w regionie.

   Wybierz **przycisk OK** i poczekaj na utworzenie i wdrożenie grupy synchronizacji.

## <a name="add-sync-members"></a>Dodaj elementy członkowskie synchronizacji

Po utworzeniu i wdrożeniu nowej grupy synchronizacji polecenie **Dodaj elementy członkowskie synchronizacji (krok 2)** zostanie wyróżnione na stronie **Nowa grupa synchronizacji** .

W sekcji **baza danych centrum** wprowadź istniejące poświadczenia dla serwera SQL Database, na którym znajduje się baza danych centrum. W tej sekcji nie wprowadzaj *nowych* poświadczeń.

![Krok 2. ustawienia](media/sql-database-get-started-sql-data-sync/steptwo.png)

### <a name="to-add-an-azure-sql-database"></a>Aby dodać Azure SQL Database

W sekcji **baza danych elementu członkowskiego** opcjonalnie Dodaj Azure SQL Database do grupy synchronizacji, wybierając pozycję **Dodaj Azure SQL Database**. Zostanie otwarta strona **konfigurowanie Azure SQL Database** .

  ![Krok 2 — Konfigurowanie bazy danych](media/sql-database-get-started-sql-data-sync/steptwo-configure.png)

  Na stronie **konfigurowanie Azure SQL Database** Zmień następujące ustawienia:

  | Ustawienie                       | Opis |
  | ----------------------------- | ------------------------------------------------- |
  | **Nazwa elementu członkowskiego synchronizacji** | Podaj nazwę nowego elementu członkowskiego synchronizacji. Ta nazwa różni się od samej nazwy bazy danych. |
  | **Subskrypcja** | Wybierz skojarzoną subskrypcję platformy Azure na potrzeby rozliczeń. |
  | **SQL Server platformy Azure** | Wybierz istniejący serwer SQL Database. |
  | **Azure SQL Database** | Wybierz istniejącą bazę danych SQL. |
  | **Wskazówki dotyczące synchronizacji** | Wybierz opcję **Synchronizacja dwukierunkowa**, z **centrum**lub **z centrum**. |
  | **Nazwa użytkownika** i **hasło** | Wprowadź istniejące poświadczenia dla serwera SQL Database, na którym znajduje się baza danych elementu członkowskiego. W tej sekcji nie wprowadzaj *nowych* poświadczeń. |

  Wybierz **przycisk OK** i poczekaj na utworzenie i wdrożenie nowej synchronizacji elementu członkowskiego.

<a name="add-on-prem"></a>
### <a name="to-add-an-on-premises-sql-server-database"></a>Aby dodać lokalną bazę danych SQL Server

W sekcji **baza danych elementu członkowskiego** opcjonalnie Dodaj SQL Server lokalny do grupy synchronizacji, wybierając pozycję **Dodaj lokalną bazę danych**. Zostanie otwarta strona **Konfiguracja lokalna** , w której można wykonać następujące czynności:

1. Wybierz pozycję **Wybierz bramę agenta synchronizacji**. Zostanie otwarta strona **Wybieranie agenta synchronizacji** .

   ![Tworzenie agenta synchronizacji](media/sql-database-get-started-sql-data-sync/steptwo-agent.png)

1. Na stronie **Wybierz agenta synchronizacji** wybierz, czy chcesz użyć istniejącego agenta, czy utworzyć agenta.

   W przypadku wybrania opcji **istniejący agenci**wybierz z listy istniejącego agenta.

   W przypadku wybrania opcji **Utwórz nowego agenta**wykonaj następujące czynności:

   1. Pobierz agenta synchronizacji danych z dostarczonego linku i zainstaluj go na komputerze, na którym znajduje się SQL Server. Agenta można także pobrać bezpośrednio z [agenta synchronizacji danych SQL Azure](https://www.microsoft.com/download/details.aspx?id=27693).

      > [!IMPORTANT]
      > Musisz otworzyć wychodzący port TCP 1433 w zaporze, aby umożliwić agentowi klienckiemu komunikowanie się z serwerem.

   1. Wprowadź nazwę agenta.

   1. Wybierz pozycję **Utwórz i Wygeneruj klucz** i skopiuj klucz agenta do Schowka.

   1. Wybierz **przycisk OK** , aby zamknąć stronę **Wybieranie agenta synchronizacji** .

1. Na komputerze SQL Server zlokalizuj i uruchom aplikację agent synchronizacji klienta.

   ![Aplikacja agenta klienta synchronizacji danych](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    1. W aplikacji Agent synchronizacji wybierz pozycję **Prześlij klucz agenta**. Zostanie otwarte okno dialogowe **Konfiguracja bazy danych metadanych synchronizacji** .

    1. W oknie dialogowym **Konfiguracja bazy danych metadanych synchronizacji** Wklej klucz agenta skopiowany z Azure Portal. Podaj również istniejące poświadczenia serwera Azure SQL Database, na którym znajduje się baza danych metadanych. (Jeśli utworzono bazę danych metadanych, ta baza danych znajduje się na tym samym serwerze, na którym znajduje się baza danych centrum). Wybierz **przycisk OK** i poczekaj na zakończenie konfiguracji.

        ![Wprowadź klucz agenta i poświadczenia serwera](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Jeśli wystąpi błąd zapory, Utwórz regułę zapory na platformie Azure, aby zezwalać na ruch przychodzący z komputera SQL Server. Regułę można utworzyć ręcznie w portalu lub w SQL Server Management Studio (SSMS). W programie SSMS Połącz się z centralną bazą danych na platformie Azure, wprowadzając jej nazwę jako < hub_database_name >. Database. Windows. NET.

    1. Wybierz pozycję **zarejestruj** , aby zarejestrować bazę danych SQL Server z agentem. Zostanie otwarte okno dialogowe **konfiguracja SQL Server** .

        ![Dodawanie i Konfigurowanie bazy danych SQL Server](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    1. W oknie dialogowym **konfiguracja SQL Server** wybierz opcję nawiązywania połączenia przy użyciu uwierzytelniania SQL Server lub uwierzytelniania systemu Windows. W przypadku wybrania opcji uwierzytelnianie SQL Server wprowadź istniejące poświadczenia. Podaj nazwę SQL Server i nazwę bazy danych, która ma zostać zsynchronizowana, a następnie wybierz pozycję **Test connection** , aby przetestować ustawienia. Następnie wybierz pozycję **Zapisz** , a na liście zostanie wyświetlona zarejestrowana baza danych.

        ![Baza danych SQL Server została już zarejestrowana](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    1. Zamknij aplikację agent synchronizacji klienta.

1. W portalu na stronie **Konfiguracja lokalna** wybierz **pozycję Wybierz bazę danych**.

1. Na stronie **Wybieranie bazy danych** w polu **Nazwa elementu członkowskiego synchronizacji** Podaj nazwę nowego elementu członkowskiego synchronizacji. Ta nazwa różni się od nazwy samej bazy danych. Wybierz bazę danych z listy. W polu **instrukcje synchronizacji** wybierz opcję **Synchronizacja dwukierunkowa**, **z centrum**lub **z centrum**.

    ![Wybierz lokalną bazę danych](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

1. Wybierz **przycisk OK** , aby zamknąć stronę **Wybierz bazę danych** . Następnie wybierz przycisk **OK** , aby zamknąć stronę **Konfiguruj lokalną** i zaczekaj na utworzenie i wdrożenie nowej synchronizacji elementu członkowskiego. Na koniec wybierz **przycisk OK** , aby zamknąć stronę **Wybieranie elementów członkowskich synchronizacji** .

> [!NOTE]
> Aby nawiązać połączenie z usługą SQL Data Sync i agentem lokalnym, Dodaj nazwę użytkownika do *DataSync_Executor*roli. Synchronizacja danych tworzy tę rolę w wystąpieniu SQL Server.

## <a name="configure-sync-group"></a>Konfiguruj grupę synchronizacji

Po utworzeniu i wdrożeniu nowych członków grupy synchronizacji na stronie **Nowa grupa synchronizacji** zostanie wyróżniona pozycja **Konfiguruj grupę synchronizacji (krok 3)** .

![Krok 3. ustawienia](media/sql-database-get-started-sql-data-sync/stepthree.png)

1. Na stronie **tabele** wybierz bazę danych z listy członków grupy synchronizacji i wybierz pozycję **Odśwież schemat**.

1. Z listy wybierz tabele, które chcesz synchronizować. Domyślnie zaznaczone są wszystkie kolumny, dlatego należy wyłączyć pole wyboru dla kolumn, które nie mają być synchronizowane. Pamiętaj, aby pozostawić wybraną kolumnę klucza podstawowego.

1. Wybierz pozycję **Zapisz**.

1. Domyślnie bazy danych nie są synchronizowane do czasu zaplanowanego ani ręcznego uruchomienia. Aby uruchomić synchronizację ręczną, przejdź do bazy danych SQL w Azure Portal, wybierz pozycję **Synchronizuj z innymi bazami danych**i wybierz grupę synchronizacji. Zostanie otwarta strona **Synchronizacja danych** . Wybierz pozycję **Synchronizuj**.

    ![Synchronizacja ręczna](media/sql-database-get-started-sql-data-sync/datasync-sync.png)

## <a name="faq"></a>Często zadawane pytania

**Jak często synchronizacja danych może synchronizować moje dane?**

Minimalny czas trwania synchronizacji wynosi pięć minut.

**Czy SQL Data Sync w pełni tworzyć tabele?**

Jeśli w docelowej bazie danych brakuje tabel schematu synchronizacji, usługa SQL Data Sync tworzy je z wybranymi kolumnami. Nie powoduje to jednak schematu o pełnej wierności z następujących powodów:

- W tabeli docelowej są tworzone tylko wybrane kolumny. Niewybrane kolumny są ignorowane.
- W tabeli docelowej są tworzone tylko wybrane indeksy kolumn. Indeksy niewybranych kolumn są ignorowane.
- Indeksy w kolumnach typu XML nie są tworzone.
- Nie utworzono ograniczeń CHECK.
- Wyzwalacze w tabelach źródłowych nie są tworzone.
- Widoki i procedury składowane nie są tworzone.

Ze względu na te ograniczenia zalecamy następujące rozwiązania:

- W środowiskach produkcyjnych należy samodzielnie utworzyć schemat pełnej wierności.
- Podczas eksperymentowania z usługą należy skorzystać z funkcji autoaprowizacji.

**Dlaczego widzę tabele, które nie zostały utworzone?**

Synchronizacja danych tworzy dodatkowe tabele w bazie danych na potrzeby śledzenia zmian. Nie usuwaj tych lub synchronizacja danych przestaje działać.

**Czy moje dane są Convergent po zakończeniu synchronizacji?**

Niekoniecznie. Utwórz grupę synchronizacji z koncentratorem i trzema szprychami (A, B i C), w przypadku których synchronizacje są piastami z centrum, do B i Hub do dysku C. W przypadku zmiany w bazie danych A *po przeprowadzeniu* synchronizacji przez centrum zmiana ta nie jest zapisywana w bazie danych B lub w bazie danych C do momentu następnego zadania synchronizacji.

**Jak mogę pobrać zmian schematu do grupy synchronizacji?**

Twórz i Propaguj wszystkie zmiany schematu ręcznie.

1. Ręcznie Replikuj zmiany schematu do centrum i wszystkich elementów członkowskich synchronizacji.
1. Zaktualizuj schemat synchronizacji.

Dodawanie nowych tabel i kolumn:

Nowe tabele i kolumny nie wpływają na bieżącą synchronizację, a synchronizacja danych ignoruje je, dopóki nie zostaną dodane do schematu synchronizacji. Podczas dodawania nowych obiektów bazy danych postępuj zgodnie z następującą sekwencją:

1. Dodaj nowe tabele lub kolumny do centrum oraz do wszystkich elementów członkowskich synchronizacji.
1. Dodaj nowe tabele lub kolumny do schematu synchronizacji.
1. Rozpocznij wstawianie wartości do nowych tabel i kolumn.

Aby zmienić typ danych kolumny:

Gdy zmienisz typ danych istniejącej kolumny, synchronizacja danych będzie nadal działała, o ile nowe wartości pasują do oryginalnego typu danych zdefiniowanego w schemacie synchronizacji. Na przykład jeśli zmienisz typ w źródłowej bazie danych z **int** na **bigint**, synchronizacja danych będzie nadal działała do momentu wstawienia wartości za duże dla typu danych **int** . Aby zakończyć zmianę, należy ręcznie zreplikować zmiany schematu do centrum i wszystkich elementów członkowskich synchronizacji, a następnie zaktualizować schemat synchronizacji.

**Jak mogę wyeksportować i zaimportować bazę danych z synchronizacją danych?**

Po wyeksportowaniu bazy danych jako pliku *. BACPAC* i zaimportowaniu pliku w celu utworzenia bazy danych wykonaj następujące czynności, aby użyć synchronizacji danych w nowej bazie danych:

1. Wyczyść obiekty synchronizacji danych i dodatkowe tabele w nowej bazie danych przy użyciu [tego skryptu](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql). Skrypt usuwa wszystkie wymagane obiekty synchronizacji danych z bazy danych.
1. Utwórz ponownie grupę synchronizacji z nową bazą danych. Jeśli stara Grupa synchronizacji nie jest już potrzebna, usuń ją.

**Gdzie można znaleźć informacje na temat agenta klienta?**

W przypadku często zadawanych pytań dotyczących agenta klienta zobacz [często zadawane](sql-database-data-sync-agent.md#agent-faq)pytania dotyczące agenta.

## <a name="next-steps"></a>Następne kroki

Gratulacje. Utworzono grupę synchronizacji obejmującą zarówno wystąpienie SQL Database, jak i bazę danych SQL Server.

Aby uzyskać więcej informacji na temat usługi SQL Data Sync, zobacz:

- [Agent synchronizacji danych dla platformy Azure SQL Data Sync](sql-database-data-sync-agent.md)
- [Najlepsze rozwiązania](sql-database-best-practices-data-sync.md) i [Rozwiązywanie problemów z usługą Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)
- [Monitorowanie SQL Data Sync przy użyciu dzienników Azure Monitor](sql-database-sync-monitor-oms.md)
- [Aktualizowanie schematu synchronizacji przy użyciu języka Transact-SQL](sql-database-update-sync-schema.md) lub [programu PowerShell](scripts/sql-database-sync-update-schema.md)

Aby uzyskać więcej informacji na temat usługi SQL Database, zobacz:

- [Omówienie usługi SQL Database](sql-database-technical-overview.md)
- [Database Lifecycle Management (Zarządzanie cyklem życia bazy danych)](https://msdn.microsoft.com/library/jj907294.aspx)
