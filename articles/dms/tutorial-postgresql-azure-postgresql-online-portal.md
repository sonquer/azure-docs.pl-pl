---
title: 'Samouczek: Migrowanie PostgreSQL do usługi Azure DB dla PostgreSQL online za pośrednictwem Azure Portal'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzić migrację online z PostgreSQL lokalnego do Azure Database for PostgreSQL przy użyciu Azure Database Migration Service za pośrednictwem Azure Portal.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/17/2020
ms.openlocfilehash: 7c496d4c1bdc29064e4ec1d3d0150cf5186ca701
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472780"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>Samouczek: Migrowanie PostgreSQL do usługi Azure DB dla PostgreSQL online przy użyciu systemu DMS za pośrednictwem Azure Portal

Za pomocą Azure Database Migration Service można migrować bazy danych z lokalnego wystąpienia PostgreSQL, aby [Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/) z minimalnym czasem przestoju aplikacji. W tym samouczku przeprowadzisz migrację przykładowej bazy danych **najmu dysku DVD** z lokalnego wystąpienia PostgreSQL 9,6 do Azure Database for PostgreSQL przy użyciu działania migracji w trybie online w programie Azure Database Migration Service.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> * Migruj Przykładowy schemat przy użyciu narzędzia pg_dump.
> * Utwórz wystąpienie Azure Database Migration Service.
> * Utwórz projekt migracji w Azure Database Migration Service.
> * Uruchamianie migracji.
> * Monitorowanie migracji.
> * Przeprowadź migrację uruchomienie produkcyjne.

> [!NOTE]
> Użycie Azure Database Migration Service do przeprowadzenia migracji w trybie online wymaga utworzenia wystąpienia na podstawie warstwy cenowej Premium.

> [!IMPORTANT]
> Aby zapewnić optymalne środowisko migracji, firma Microsoft zaleca Tworzenie wystąpienia Azure Database Migration Service w tym samym regionie świadczenia usługi Azure jako docelowej bazie danych. Przenoszenie danych między regionami lub lokalizacjami geograficznymi może spowalniać proces migracji i powodować błędy.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Pobierz i zainstaluj [PostgreSQL community edition](https://www.postgresql.org/download/) 9,4, 9,5, 9,6 lub 10. Źródłowa wersja serwera PostgreSQL musi być 9,4, 9,5, 9,6, 10 lub 11. Aby uzyskać więcej informacji, zobacz artykuł [Obsługiwane wersje bazy danych PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).

    Ponadto lokalna wersja programu PostgreSQL musi być zgodna z wersją usługi Azure Database for PostgreSQL. Na przykład PostgreSQL 9,6 można migrować tylko do Azure Database for PostgreSQL 9,6, 10 lub 11, ale nie do Azure Database for PostgreSQL 9,5.

* [Utwórz serwer Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) lub [Utwórz serwer Azure Database for PostgreSQL-Citus](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal).
* Utwórz Microsoft Azure Virtual Network dla Azure Database Migration Service przy użyciu modelu wdrażania Azure Resource Manager, który zapewnia łączność między lokacjami z lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zapoznaj się z [dokumentacją Virtual Network](https://docs.microsoft.com/azure/virtual-network/), a w szczególności artykuły szybkiego startu z szczegółowymi szczegółami.

    > [!NOTE]
    > Jeśli podczas konfigurowania sieci wirtualnej używasz usługi ExpressRoute z usługą Komunikacja równorzędna z firmą Microsoft, Dodaj następujące [punkty końcowe](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) do podsieci, w której zostanie zainicjowana usługa:
    >
    > * Docelowy punkt końcowy bazy danych (na przykład punkt końcowy SQL, Cosmos DB punkt końcowy itd.)
    > * Punkt końcowy magazynu
    > * Punkt końcowy usługi Service Bus
    >
    > Ta konfiguracja jest konieczna, ponieważ Azure Database Migration Service brak łączności z Internetem.

* Upewnij się, że reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) dla sieci wirtualnej nie blokują następujących portów komunikacji przychodzącej do Azure Database Migration Service: 443, 53, 9354, 445, 12000. Aby uzyskać więcej szczegółów na temat filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej, zobacz artykuł [Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otwórz Zaporę systemu Windows, aby umożliwić Azure Database Migration Service dostęp do źródłowego serwera PostgreSQL, który domyślnie jest portem TCP 5432.
* W przypadku korzystania z urządzenia zapory przed źródłową bazą danych konieczne może być dodanie reguł zapory, aby zezwolić usłudze Azure Database Migration Service na dostęp do źródłowej bazy danych podczas migracji.
* Utwórz [regułę zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) na poziomie serwera dla Azure Database for PostgreSQL, aby umożliwić Azure Database Migration Service dostęp do docelowych baz danych. Podaj zakres podsieci sieci wirtualnej używanej na potrzeby Azure Database Migration Service.
* Włącz replikację logiczną w pliku postgresql.config i ustaw następujące parametry:

  * wal_level = **logical**
  * max_replication_slots = [liczba gniazd], zalecamy ustawienie na **pięć gniazd**
  * max_wal_senders = [liczba współbieżnych zadań] — parametr max_wal_senders ustawia liczbę współbieżnych zadań, które można uruchomić, przy czym zalecane ustawienie to **10 zadań**

> [!IMPORTANT]
> Wszystkie tabele w istniejącej bazie danych muszą mieć klucz podstawowy, aby upewnić się, że zmiany można synchronizować z docelową bazą danych.

## <a name="migrate-the-sample-schema"></a>Migrowanie przykładowego schematu

Aby utworzyć wszystkie obiekty bazy danych, takie jak schematy tabel, indeksy oraz procedury składowane, należy wyodrębnić schemat ze źródłowej bazy danych i zastosować go w bazie danych.

1. Użyj polecenia -s pg_dump, aby utworzyć plik zrzutu schematu dla bazy danych.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Na przykład, aby utworzyć plik zrzutu schematu dla bazy danych **dvdrental** :

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    Aby uzyskać więcej informacji na temat korzystania z narzędzia pg_dump, zobacz przykłady w samouczku narzędzia [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Utwórz pustą bazę danych w swoim środowisku docelowym, czyli w usłudze Azure Database for PostgreSQL.

    Aby uzyskać szczegółowe informacje na temat nawiązywania połączenia i tworzenia bazy danych, zobacz artykuł [Tworzenie serwera Azure Database for PostgreSQL w Azure Portal](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) lub [Tworzenie serwera Azure Database for PostgreSQL-Citus w Azure Portal](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal).

    > [!NOTE]
    > Wystąpienie Azure Database for PostgreSQL-Citus) ma tylko jedną bazę danych: **Citus**.

3. Zaimportuj schemat do docelowej bazy danych utworzonej przez przywrócenie pliku zrzutu schematu.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Na przykład:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Aby wyodrębnić skrypt klucza obcego i dodać go do miejsca docelowego (Azure Database for PostgreSQL), w PgAdmin lub PSQL, uruchom następujący skrypt.

   > [!IMPORTANT]
   > Klucze obce w schemacie spowodują, że wstępne ładowanie i ciągła synchronizacja migracji zakończą się niepowodzeniem.

    ```
    SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ',
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema,
        tc.constraint_name as foreignkey,
        tc.table_name as tableName,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
    FROM
        information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    ```

5. Uruchom docelowy klucz obcy (znajduje się w drugiej kolumnie) w wyniku zapytania.

6. Aby wyłączyć Wyzwalacze w docelowej bazie danych, uruchom poniższy skrypt.

   > [!IMPORTANT]
   > Wyzwalacze (INSERT lub Update) w danych wymuszają integralność danych w miejscu docelowym przed danymi replikowanymi ze źródła. W związku z tym zaleca się wyłączenie wyzwalaczy we wszystkich tabelach w **miejscu docelowym** podczas migracji, a następnie ponowne włączenie wyzwalaczy po zakończeniu migracji.

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

   ![Wyświetlanie subskrypcji w portalu](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie Azure Database Migration Service, a następnie wybierz pozycję **dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Wyszukaj „migration”, a następnie po prawej stronie pozycji **Microsoft.DataMigration** wybierz pozycję **Zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Tworzenie wystąpienia usługi DMS

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Azure Marketplace](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. Na ekranie **Tworzenie usługi migracji** Określ nazwę, subskrypcję, nową lub istniejącą grupę zasobów oraz lokalizację usługi.

4. Wybierz istniejącą sieć wirtualną lub Utwórz nową.

    Sieć wirtualna zapewnia Azure Database Migration Service z dostępem do źródłowego serwera PostgreSQL i docelowego wystąpienia Azure Database for PostgreSQL.

    Aby uzyskać więcej informacji na temat sposobu tworzenia sieci wirtualnej w Azure Portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu Azure Portal](https://aka.ms/DMSVnet).

5. Wybierz warstwę cenową.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    ![Konfigurowanie ustawień wystąpienia usługi Azure Database Migration Service](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Wybierz pozycję **Przegląd + Utwórz** , aby utworzyć usługę.

   Tworzenie usługi zakończy się w ciągu około 10 do 15 minut.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu usługi znajdź ją w witrynie Azure Portal, otwórz ją, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

      ![Znajdź wszystkie wystąpienia Azure Database Migration Service](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. Na ekranie **usługi Azure Database Migration Services** Wyszukaj nazwę utworzonego wystąpienia Azure Database Migration Service, wybierz wystąpienie, a następnie wybierz pozycję + **Nowy projekt migracji**.

3. Na ekranie **Nowy projekt migracji** Określ nazwę projektu, w polu tekstowym **Typ serwera źródłowego** wybierz pozycję **PostgresSQL**, w polu tekstowym **Typ serwera docelowego** wybierz pozycję **Azure Database for PostgreSQL**.

4. W sekcji **Wybierz typ działania** wybierz pozycję **Migracja danych w trybie online**.

    ![Utwórz projekt Azure Database Migration Service](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > Alternatywnie możesz wybrać opcję **Utwórz projekt tylko** w celu utworzenia projektu migracji, a następnie wykonać migrację później.

5. Wybierz pozycję **Zapisz**, zwróć uwagę na wymagania, aby pomyślnie użyć Azure Database Migration Service do migracji danych, a następnie wybierz pozycję **Utwórz i uruchom działanie**.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

1. Na ekranie **Dodaj szczegóły źródła** Określ szczegóły połączenia dla wystąpienia źródła PostgreSQL.

    ![Ekran Dodawanie szczegółów źródła](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. Wybierz pozycję **Zapisz**.

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Na ekranie **szczegóły elementu docelowego** Określ szczegóły połączenia dla serwera docelowego Citus, który jest wstępnie zainicjowanym wystąpieniem funkcji Citus, do którego wdrożono schemat **wynajmu dysku DVD** przy użyciu pg_dump.

    ![Ekran Szczegóły elementu docelowego](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. Wybierz polecenie **Zapisz**, a następnie na ekranie **Mapuj do docelowych baz danych**, określ mapowanie źródłowej i docelowej bazy danych na potrzeby migracji.

    Jeśli docelowa baza danych zawiera tę samą nazwę bazy danych co źródłowa baza danych, Azure Database Migration Service domyślnie wybiera docelową bazę danych.

    ![Ekran mapowania do docelowej bazy danych](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Wybierz pozycję **Zapisz**, a następnie na ekranie **Ustawienia migracji** zaakceptuj wartości domyślne.

    ![Ekran ustawień migracji](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Wybierz pozycję **Zapisz**. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji, a następnie sprawdź podsumowanie, aby upewnić się, że szczegółowe informacje źródłowe i docelowe pasują do wcześniej podanych.

    ![Ekran podsumowania migracji](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>Uruchamianie migracji

* Wybierz polecenie **Uruchom migrację**.

    Zostanie wyświetlone okno działanie migracji, a **stan** działania powinna zostać zaktualizowana, aby pokazać **Tworzenie kopii zapasowej w toku**.

## <a name="monitor-the-migration"></a>Monitorowanie migracji

1. Na ekranie działania migracji wybieraj polecenie **Odśwież**, aby zaktualizować ekran, aż do momentu, gdy **Stan** migracji zmieni się na **Ukończono**.

     ![Monitorowanie procesu migracji](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Po zakończeniu migracji w obszarze **Nazwa bazy danych**Wybierz konkretną bazę danych, aby przejść do stanu migracji **pełnych danych** i **przyrostowych operacji synchronizacji danych** .

   > [!NOTE]
   > **Pełne ładowanie danych** pokazuje stan początkowej migracji ładowania, podczas gdy **Synchronizacja danych przyrostowych** pokazuje stan funkcji przechwytywania zmian danych.

     ![Pełne szczegóły ładowania danych](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Szczegóły synchronizacji danych przyrostowych](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Przeprowadzanie migracji jednorazowej

Po zakończeniu początkowego pełnego ładowania bazy danych są oznaczone jako **Gotowe do migracji jednorazowej**.

1. Gdy wszystko jest gotowe do zakończenia migracji bazy danych, wybierz pozycję **Rozpocznij migrację jednorazową**.

2. Poczekaj, aż licznik **oczekujące zmiany** wyświetli **wartość 0** , aby upewnić się, że wszystkie przychodzące transakcje do źródłowej bazy danych zostaną zatrzymane, zaznacz pole wyboru **Potwierdź** , a następnie wybierz pozycję **Zastosuj**.

    ![Pełny ekran uruchomienie produkcyjne](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Gdy stan migracji bazy danych zostanie **zakończony**, Połącz swoje aplikacje z nowym docelowym wystąpieniem Azure Database for PostgreSQL.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje o znanych problemach i ograniczeniach związanych z wykonywaniem migracji w trybie online do usługi Azure Database for PostgreSQL, zobacz artykuł [Znane problemy i obejścia problemów związanych z migracjami online usługi Azure Database for PostgreSQL](known-issues-azure-postgresql-online.md).
* Aby uzyskać informacje o usłudze Azure Database Migration Service, zobacz artykuł [Czym jest usługa Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Aby uzyskać informacje na temat Azure Database for PostgreSQL, zobacz artykuł [co to jest Azure Database for PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
