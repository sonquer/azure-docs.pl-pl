---
title: Wprowadzenie do inspekcji
description: Funkcja inspekcji usługi Azure SQL Database umożliwia śledzenie zdarzeń bazy danych w dzienniku inspekcji.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/11/2020
ms.openlocfilehash: 686e426ef0b7706eff168e42ffc67417b2c5c743
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212897"
---
# <a name="get-started-with-sql-database-auditing"></a>Wprowadzenie do inspekcji bazy danych SQL

Inspekcja usługi Azure [SQL Database](sql-database-technical-overview.md) i [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie usługi Azure Storage, w obszarze roboczym log Analytics lub Event Hubs. Inspekcja również:

- Pomaga zachować zgodność z przepisami, zrozumieć aktywność bazy danych oraz uzyskać wgląd w niezgodności i anomalie, które mogą wskazywać na problemy biznesowe lub podejrzane naruszenia zabezpieczeń.

- Włącza i ułatwia przestrzeganie standardów zgodności, chociaż nie gwarantuje zgodności. Aby uzyskać więcej informacji na temat programów platformy Azure, które obsługują zgodność ze standardami, zobacz [Centrum zaufania Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , gdzie można znaleźć najbardziej aktualną listę certyfikatów zgodności SQL Database.


> [!NOTE] 
> Ten temat dotyczy serwera Azure SQL oraz baz danych zarówno usługi SQL Database, jak i SQL Data Warehouse utworzonych na serwerze Azure SQL. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database.

## <a id="subheading-1"></a>Omówienie inspekcji usługi Azure SQL Database

Inspekcji bazy danych SQL można użyć do:

- **Zachowaj** dziennik inspekcji dla wybranych zdarzeń. Można definiować kategorie akcji bazy danych, które mają być poddane inspekcji.
- **Raport** dotyczący działania bazy danych. Możesz użyć wstępnie skonfigurowanych raportów i pulpitu nawigacyjnego, aby szybko rozpocząć pracę z raportowaniem działań i zdarzeń.
- **Analizuj** raporty. Można znaleźć podejrzane zdarzenia, nietypową aktywność i trendy.

> [!IMPORTANT]
> Dzienniki inspekcji są zapisywane w celu **dołączania obiektów BLOB** w usłudze Azure Blob Storage w ramach subskrypcji platformy Azure.
>
> - Obsługiwane są wszystkie rodzaje magazynów (v1, v2, BLOB).
> - Wszystkie konfiguracje replikacji magazynu są obsługiwane.
> - Magazyn za siecią wirtualną i zaporą jest obsługiwana.
> - Usługa **Premium Storage** nie jest obecnie **obsługiwana**.
> - **Hierarchiczna przestrzeń nazw** dla **konta usługi Azure Data Lake Storage Gen2 Storage** nie jest obecnie **obsługiwana**.
> - Włączanie inspekcji wstrzymanej **Azure SQL Data Warehouse** nie jest obsługiwane. Aby włączyć inspekcję, Wznów działanie hurtowni danych.
   
## <a id="subheading-8"></a>Definiowanie zasad inspekcji na poziomie serwera i na poziomie bazy danych

Zasady inspekcji można zdefiniować dla konkretnej bazy danych lub jako domyślne zasady serwera:

- Zasady serwera dotyczą wszystkich istniejących i nowo utworzonych baz danych na serwerze.

- Jeśli *Inspekcja obiektów BLOB serwera jest włączona*, *zawsze ma zastosowanie do bazy danych programu*. Baza danych będzie poddawana inspekcji, niezależnie od ustawień inspekcji bazy danych.

- Włączenie inspekcji obiektów BLOB w bazie danych lub hurtowni danych (oprócz włączenia jej na serwerze) *nie przesłania ani* nie zmienia żadnych ustawień inspekcji obiektów BLOB serwera. Obie inspekcje będą istnieć obok siebie. Inaczej mówiąc, baza danych jest monitorowana dwukrotnie. raz przez zasady serwera i jeden raz przez zasady bazy danych.

   > [!NOTE]
   > Należy unikać jednoczesnego włączania inspekcji obiektów BLOB serwera i inspekcji obiektów BLOB bazy danych, chyba że:
    > - Chcesz użyć innego *konta magazynu* lub *okresu przechowywania* dla określonej bazy danych.
    > - Chcesz przeprowadzić inspekcję typów zdarzeń lub kategorii dla określonej bazy danych, która różni się od reszty baz danych na serwerze. Na przykład można mieć wstawienia tabeli, które muszą być poddane inspekcji tylko dla określonej bazy danych.
   >
   > W przeciwnym razie zalecamy włączenie tylko inspekcji obiektów BLOB na poziomie serwera i pozostawienie wyłączonej inspekcji na poziomie bazy danych dla wszystkich baz danych.

## <a id="subheading-2"></a>Skonfiguruj inspekcję serwera

W poniższej sekcji opisano konfigurację inspekcji przy użyciu Azure Portal.

  > [!NOTE]
   >Teraz masz wiele opcji konfigurowania lokalizacji dzienników inspekcji. Dzienniki można zapisywać na koncie usługi Azure Storage, w obszarze roboczym Log Analytics do użycia przez dzienniki Azure Monitor lub do centrum zdarzeń w celu użycia przy użyciu centrum zdarzeń. Można skonfigurować dowolną kombinację tych opcji, a dzienniki inspekcji będą zapisywane w każdym z nich.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Przejdź do opcji **Inspekcja** pod nagłówkiem zabezpieczenia w okienku baza danych SQL/serwer.
3. Jeśli wolisz skonfigurować zasady inspekcji serwera, możesz wybrać łącze **Wyświetl ustawienia serwera** na stronie Inspekcja bazy danych. Następnie można wyświetlić lub zmodyfikować ustawienia inspekcji serwera. Zasady inspekcji serwera dotyczą wszystkich istniejących i nowo utworzonych baz danych na tym serwerze.

    ![Okienko nawigacji][2]

4. Jeśli wolisz włączyć inspekcję na poziomie bazy danych, przełącz **inspekcję** na wartość **włączone**. Jeśli Inspekcja serwera jest włączona, inspekcja skonfigurowana dla bazy danych będzie istnieć równolegle z inspekcją serwera.

    ![Okienko nawigacji][3]

### <a id="audit-storage-destination">Inspekcja w miejscu docelowym magazynu</a>

Aby skonfigurować zapisywanie dzienników inspekcji na koncie magazynu, wybierz pozycję **Magazyn** i Otwórz **szczegóły magazynu**. Wybierz konto usługi Azure Storage, na którym będą zapisywane dzienniki, a następnie wybierz okres przechowywania. Następnie kliknij przycisk **OK**. Dzienniki starsze niż okres przechowywania są usuwane.

   > [!IMPORTANT]
   > - Wartość domyślna okresu przechowywania to 0 (nieograniczony czas przechowywania). Tę wartość można zmienić, przesuwając suwak **przechowywanie (dni)** w **ustawieniach magazynu** podczas konfigurowania konta magazynu na potrzeby inspekcji.
   > - Jeśli zmienisz okres przechowywania z 0 (nieograniczony czas przechowywania) na inną wartość, należy pamiętać, że przechowywanie będzie dotyczyło tylko dzienników pisanych po zmianie wartości przechowywania (dzienniki zapisane w okresie, w którym czas przechowywania był ustawiony na nieograniczone, są zachowywane nawet po przechowywanie jest włączone)

   ![konto magazynu](./media/sql-database-auditing-get-started/auditing_select_storage.png)

Aby skonfigurować konto magazynu w ramach sieci wirtualnej lub zapory, na serwerze musi być [Active Directory administrator](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell#provision-an-azure-active-directory-administrator-for-your-managed-instance) , Włącz opcję **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu** na koncie magazynu. Ponadto należy mieć uprawnienie "Microsoft. Authorization/roleAssignments/Write" na wybranym koncie magazynu.

Zalecamy, aby być [administratorem dostępu użytkowników](../role-based-access-control/built-in-roles.md#user-access-administrator) w celu udzielenia tożsamości zarządzanej roli "Współautor danych obiektu blob magazynu". Aby dowiedzieć się więcej o uprawnieniach i kontroli dostępu opartej na rolach, zobacz [co to jest kontrola dostępu oparta na rolach (RBAC) dla zasobów platformy Azure?](../role-based-access-control/overview.md) i [Dodawanie lub usuwanie przypisań ról przy użyciu funkcji RBAC platformy Azure i Azure Portal](../role-based-access-control/role-assignments-portal.md)

### <a id="audit-log-analytics-destination">Inspekcja w celu Log Analytics lokalizacji docelowej</a>
  
Aby skonfigurować zapisywanie dzienników inspekcji do obszaru roboczego Log Analytics, wybierz pozycję **log Analytics (wersja zapoznawcza)** i Otwórz **log Analytics szczegóły**. Wybierz lub Utwórz obszar roboczy Log Analytics, w którym będą zapisywane dzienniki, a następnie kliknij przycisk **OK**.

   ![LogAnalyticsworkspace](./media/sql-database-auditing-get-started/auditing_select_oms.png)
    
  > [!WARNING]
   > Włączenie inspekcji do Log Analytics będzie powodować naliczanie kosztów na podstawie stawek za pozyskiwanie. Zapoznaj się z powiązanym kosztem przy użyciu tej [opcji](https://azure.microsoft.com/pricing/details/monitor/)lub Rozważ przechowywanie dzienników inspekcji na koncie usługi Azure Storage.

### <a id="audit-event-hub-destination">Inspekcja w miejscu docelowym centrum zdarzeń</a>

Aby skonfigurować zapisywanie dzienników inspekcji do centrum zdarzeń, wybierz pozycję **centrum zdarzeń (wersja zapoznawcza)** i Otwórz **szczegóły centrum zdarzeń**. Wybierz centrum zdarzeń, w którym będą zapisywane dzienniki, a następnie kliknij przycisk **OK**. Upewnij się, że centrum zdarzeń znajduje się w tym samym regionie, w którym znajduje się baza danych i serwer.

   ![Eventhub](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

## <a id="subheading-3"></a>Analizowanie dzienników i raportów inspekcji

W przypadku wybrania opcji zapisania dzienników inspekcji do Azure Monitor dzienników:

- Użyj [Azure Portal](https://portal.azure.com).  Otwórz odpowiednią bazę danych. W górnej części strony **Inspekcja** bazy danych kliknij pozycję **Wyświetl dzienniki inspekcji**.

    ![Wyświetlanie dzienników inspekcji](./media/sql-database-auditing-get-started/auditing-view-audit-logs.png)

- Następnie masz dwa sposoby wyświetlania dzienników:
    
    Kliknięcie **log Analytics** w górnej części strony **rekordy inspekcji** spowoduje otwarcie widoku dzienniki w obszarze roboczym log Analytics, gdzie można dostosować zakres czasu i zapytanie wyszukiwania.
    
    ![Otwórz w obszarze roboczym Log Analytics](./media/sql-database-auditing-get-started/auditing-log-analytics.png)

    Kliknięcie przycisku **Wyświetl pulpit nawigacyjny** w górnej części strony **rekordy inspekcji** spowoduje otwarcie pulpitu nawigacyjnego wyświetlającego informacje o dziennikach inspekcji, w którym można przejść do szczegółowych informacji o zabezpieczeniach, uzyskać dostęp do poufnych danych i nie tylko. Ten pulpit nawigacyjny umożliwia uzyskanie szczegółowych informacji o zabezpieczeniach danych.
    Możesz również dostosować zakres czasu i zapytanie wyszukiwania. 
    ![widoku pulpit nawigacyjny Log Analytics](media/sql-database-auditing-get-started/auditing-view-dashboard.png)

    ![Pulpit nawigacyjny Log Analytics](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard.png)

    ![Log Analytics szczegółowe informacje o zabezpieczeniach](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard-data.png)
 

- Alternatywnie można również uzyskać dostęp do dzienników inspekcji w bloku Log Analytics. Otwórz obszar roboczy Log Analytics i w sekcji **Ogólne** kliknij pozycję **dzienniki**. Możesz zacząć od prostego zapytania, takiego jak: *Search "SQLSecurityAuditEvents"* , aby wyświetlić dzienniki inspekcji.
    W tym miejscu możesz również użyć [dzienników Azure monitor](../log-analytics/log-analytics-log-search.md) , aby uruchomić zaawansowane wyszukiwania w danych dziennika inspekcji. Dzienniki Azure Monitor udostępniają usługi operacyjne w czasie rzeczywistym przy użyciu zintegrowanego wyszukiwania i niestandardowych pulpitów nawigacyjnych, które umożliwiają łatwe analizowanie milionów rekordów na wszystkich obciążeniach i serwerach. Aby uzyskać dodatkowe informacje dotyczące Azure Monitor języka i poleceń wyszukiwania dzienników, zobacz artykuł [Azure monitor Logs Search Reference](../log-analytics/log-analytics-log-search.md).

W przypadku wybrania opcji zapisania dzienników inspekcji do centrum zdarzeń:

- Aby korzystać z danych inspekcji dzienników z centrum zdarzeń, należy skonfigurować strumień, który będzie korzystał z zdarzeń i zapisywać je w celu. Aby uzyskać więcej informacji, zobacz [dokumentację usługi Azure Event Hubs](../event-hubs/index.yml).
- Dzienniki inspekcji w centrum zdarzeń są przechwytywane w treści zdarzeń [Apache Avro](https://avro.apache.org/) i przechowywane przy użyciu formatowania JSON przy użyciu kodowania UTF-8. Aby odczytać dzienniki inspekcji, można użyć [narzędzi Avro](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) lub podobnych narzędzi, które przetwarzają ten format.

W przypadku wybrania opcji zapisania dzienników inspekcji na koncie usługi Azure Storage istnieje kilka metod wyświetlania dzienników:

> [!NOTE] 
> Inspekcja [replik tylko do odczytu](sql-database-read-scale-out.md) jest włączana automatycznie. Aby uzyskać więcej informacji na temat hierarchii folderów magazynu, konwencji nazewnictwa i formatu dziennika, zobacz [format dziennika inspekcji SQL Database](sql-database-audit-log-format.md). 

- Dzienniki inspekcji są agregowane na koncie wybranym podczas instalacji. Dzienniki inspekcji można eksplorować przy użyciu narzędzia, takiego jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/). W usłudze Azure Storage dzienniki inspekcji są zapisywane jako kolekcja plików obiektów BLOB w kontenerze o nazwie **sqldbauditlogs**. Aby uzyskać więcej informacji na temat hierarchii folderów magazynu, konwencji nazewnictwa i formatu dziennika, zobacz [format dziennika inspekcji SQL Database](https://go.microsoft.com/fwlink/?linkid=829599).

- Użyj [Azure Portal](https://portal.azure.com).  Otwórz odpowiednią bazę danych. W górnej części strony **Inspekcja** bazy danych kliknij pozycję **Wyświetl dzienniki inspekcji**.

    ![Okienko nawigacji][7]

    **Rekordy inspekcji** są otwierane, z poziomu którego będziesz mieć możliwość wyświetlania dzienników.

  - Aby wyświetlić określone daty, kliknij pozycję **Filtruj** w górnej części strony **Rejestrowanie inspekcji** .
  - Można przełączać się między rekordami inspekcji, które zostały utworzone przez *zasady inspekcji serwera* i *zasad inspekcji bazy danych* przez przełączenie **źródła inspekcji**.
  - Można wyświetlić tylko rekordy inspekcji powiązane z iniekcją SQL, zaznaczając pole wyboru **Pokaż tylko rekordy inspekcji dla iniekcji kodu SQL** .

       ![Okienko nawigacji][8]

- Użyj funkcji system **sys. fn_get_audit_file** (T-SQL), aby zwrócić dane dziennika inspekcji w formacie tabelarycznym. Aby uzyskać więcej informacji na temat korzystania z tej funkcji, zobacz [sys. fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Użyj **plików inspekcji scalania** w SQL Server Management Studio (począwszy od programu SSMS 17):
    1. Z menu programu SSMS wybierz pozycję **plik** > **Otwórz** > **Scal pliki inspekcji**.

        ![Okienko nawigacji][9]
    2. Zostanie otwarte okno dialogowe **Dodawanie plików inspekcji** . Wybierz jedną z opcji **dodawania** , aby wybrać, czy pliki inspekcji mają być scalane z dysku lokalnego, czy też zaimportować je z usługi Azure Storage. Musisz podać szczegóły i klucz konta usługi Azure Storage.

    3. Po dodaniu wszystkich plików do scalenia kliknij przycisk **OK** , aby ukończyć operację scalania.

    4. Scalony plik zostanie otwarty w programie SSMS, gdzie można go wyświetlić i przeanalizować, a także wyeksportować do pliku XEL lub CSV lub do tabeli.

- Użyj Power BI. Można wyświetlać i analizować dane dziennika inspekcji w Power BI. Aby uzyskać więcej informacji i uzyskać dostęp do możliwego do pobrania szablonu, zobacz [Analizowanie danych dziennika inspekcji w Power BI](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/).
- Pobierz pliki dziennika z kontenera obiektów BLOB usługi Azure Storage za pośrednictwem portalu lub za pomocą narzędzia, takiego jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).
  - Po pobraniu pliku dziennika lokalnie kliknij dwukrotnie plik, aby otworzyć, wyświetlić i analizować dzienniki w programie SSMS.
  - Możesz również pobrać wiele plików jednocześnie za pośrednictwem Eksplorator usługi Azure Storage. Aby to zrobić, kliknij prawym przyciskiem myszy określony podfolder i wybierz polecenie **Zapisz jako** do zapisania w folderze lokalnym.

- Dodatkowe metody:

  - Po pobraniu kilku plików lub podfolderu, który zawiera pliki dziennika, można scalić je lokalnie zgodnie z opisem w artykule informacje o plikach inspekcji scalania programu SSMS opisane wcześniej.
  - Programowe Wyświetlanie dzienników inspekcji obiektów blob:

    - [Wykonywanie zapytań dotyczących plików zdarzeń rozszerzonych](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/) za pomocą programu PowerShell.

## <a id="subheading-5"></a>Praktyki produkcyjne

<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">Inspekcja replikowanych geograficznie baz danych</a>

Po włączeniu inspekcji podstawowej bazy danych przy użyciu baz danych z replikacją geograficzną pomocnicza baza danych będzie miała identyczne zasady inspekcji. Istnieje również możliwość skonfigurowania inspekcji pomocniczej bazy danych przez włączenie inspekcji na **serwerze pomocniczym**niezależnie od podstawowej bazy danych.

- Poziom serwera (**zalecane**): Włącz inspekcję zarówno na **serwerze podstawowym** , jak i na **serwerze pomocniczym** — podstawowe i pomocnicze bazy danych zostaną poddane inspekcji niezależnie od ich odpowiednich zasad na poziomie serwera.
- Poziom bazy danych: Inspekcja na poziomie bazy danych dla pomocniczych baz danych można skonfigurować tylko przy użyciu ustawień inspekcji podstawowej bazy danych.
  - Inspekcja musi być włączona w *podstawowej bazie danych*, a nie na serwerze.
  - Po włączeniu inspekcji w podstawowej bazie danych zostanie ona również włączona w pomocniczej bazie danych.

    >[!IMPORTANT]
    >W przypadku inspekcji na poziomie bazy danych ustawienia magazynu dla pomocniczej bazy danych będą takie same, jak w przypadku ruchu międzyregionalnego. Zalecamy włączenie tylko inspekcji na poziomie serwera i pozostawienie wyłączonej inspekcji na poziomie bazy danych dla wszystkich baz danych.

### <a id="subheading-6">Ponowne generowanie klucza magazynu</a>

W środowisku produkcyjnym można okresowo odświeżać klucze magazynu. Podczas zapisywania dzienników inspekcji w usłudze Azure Storage należy ponownie zapisać zasady inspekcji podczas odświeżania kluczy. Proces przebiega w następujący sposób:

1. Otwórz **szczegóły magazynu**. W polu **klucz dostępu do magazynu** wybierz pozycję **pomocniczy**, a następnie kliknij przycisk **OK**. Następnie kliknij pozycję **Zapisz** w górnej części strony Konfiguracja inspekcji.

    ![Okienko nawigacji][5]
2. Przejdź do strony Konfiguracja magazynu i ponownie Wygeneruj podstawowy klucz dostępu.

    ![Okienko nawigacji][6]
3. Wróć do strony Konfiguracja inspekcji, przełącz klucz dostępu do magazynu z pomocnicza na podstawowy, a następnie kliknij przycisk **OK**. Następnie kliknij pozycję **Zapisz** w górnej części strony Konfiguracja inspekcji.
4. Wróć do strony Konfiguracja magazynu i Wygeneruj ponownie pomocniczy klucz dostępu (w przygotowaniu dla cyklu odświeżania następnego klucza).

## <a name="additional-information"></a>Dodatkowe informacje

- Jeśli chcesz dostosować zdarzenia poddane inspekcji, możesz to zrobić za pomocą [poleceń cmdlet programu PowerShell](#subheading-7) lub [interfejsu API REST](#subheading-9).

- Po skonfigurowaniu ustawień inspekcji można włączyć nową funkcję wykrywania zagrożeń i skonfigurować wiadomości e-mail w celu otrzymywania alertów zabezpieczeń. W przypadku korzystania z wykrywania zagrożeń otrzymywane są aktywne alerty dotyczące nietypowych działań bazy danych, które mogą wskazywać na potencjalne zagrożenia bezpieczeństwa. Aby uzyskać więcej informacji, zobacz [wprowadzenie do wykrywania zagrożeń](sql-database-threat-detection-get-started.md).
- Aby uzyskać szczegółowe informacje na temat formatu dziennika, hierarchii folderu magazynu i konwencji nazewnictwa, zobacz [dokumentacja formatu dziennika inspekcji obiektów BLOB](https://go.microsoft.com/fwlink/?linkid=829599).

    > [!IMPORTANT]
    > Azure SQL Database inspekcji przechowuje 4000 znaków danych dla pól znaków w rekordzie inspekcji. Gdy **instrukcja** lub **data_sensitivity_information** wartości zwracane z akcji objętej inspekcją zawierają więcej niż 4000 znaków, wszystkie dane spoza pierwszych 4000 znaków będą **obcinane i nieobjęte inspekcją**.

- Dzienniki inspekcji są zapisywane w celu **dołączania obiektów BLOB** w usłudze Azure Blob Storage w ramach subskrypcji platformy Azure:
  - **Premium Storage** nie jest obecnie **obsługiwana** przez dołączenie obiektów BLOB.

- Domyślne zasady inspekcji obejmują wszystkie akcje i następujący zestaw grup akcji, który przeprowadzi inspekcję wszystkich zapytań i procedur składowanych wykonywanych w bazie danych, jak również pomyślnie i nieudanych logowań:

    BATCH_COMPLETED_GROUP<br>
    SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP<br>
    FAILED_DATABASE_AUTHENTICATION_GROUP

    Inspekcję dla różnych typów akcji i grup akcji można skonfigurować przy użyciu programu PowerShell, zgodnie z opisem w sekcji [Zarządzanie inspekcją usługi SQL Database przy użyciu Azure PowerShell](#subheading-7) .

- W przypadku korzystania z uwierzytelniania usługi AAD rekordy nieudanych logowań *nie* będą widoczne w dzienniku inspekcji SQL. Aby wyświetlić nieudane rekordy inspekcji logowania, należy odwiedzić [portal Azure Active Directory]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md), w którym znajdują się szczegóły dotyczące tych zdarzeń.

- Inspekcja Azure SQL Database jest zoptymalizowana pod kątem dostępności & wydajności. Podczas bardzo dużego działania Azure SQL Database umożliwia wykonywanie operacji i może nie rejestrować niektórych zdarzeń poddawanych inspekcji.

- Aby skonfigurować niezmienne inspekcje na koncie magazynu, zobacz [Zezwalanie na chronione Dodawanie obiektów BLOB](../storage/blobs/storage-blob-immutable-storage.md#allow-protected-append-blobs-writes). Należy pamiętać, że nazwa kontenera do inspekcji to **sqldbauditlogs**.

    > [!IMPORTANT]
    > Ustawienie Zezwalaj na chronione Dodawanie obiektów BLOB w obszarze przechowywanie na podstawie czasu jest obecnie dostępne i widoczne tylko w następujących regionach:
    > - Wschodnie stany USA
    > - Południowo-środkowe stany USA
    > - Zachodnie stany USA 2


## <a id="subheading-7"></a>Zarządzanie usługą Azure SQL Server i inspekcją bazy danych przy użyciu Azure PowerShell

**Polecenia cmdlet programu PowerShell (w tym obsługa dodatkowych funkcji filtrowania)** :

- [Tworzenie lub aktualizowanie zasad inspekcji bazy danych (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Tworzenie lub aktualizowanie zasad inspekcji serwera (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [Pobierz zasady inspekcji bazy danych (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Pobierz zasady inspekcji serwera (Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [Usuń zasady inspekcji bazy danych (Remove-AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Usuń zasady inspekcji serwera (Remove-AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

Aby zapoznać się z przykładem skryptu, zobacz [Konfigurowanie inspekcji i wykrywania zagrożeń przy użyciu programu PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a id="subheading-8"></a>Zarządzanie usługą Azure SQL Server i inspekcją bazy danych przy użyciu interfejsu API REST

**REST API**:

- [Utwórz lub zaktualizuj zasady inspekcji bazy danych](/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Tworzenie lub aktualizowanie zasad inspekcji serwera](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Pobierz zasady inspekcji bazy danych](/rest/api/sql/database%20auditing%20settings/get)
- [Pobierz zasady inspekcji serwera](/rest/api/sql/server%20auditing%20settings/get)

Rozszerzone zasady z klauzulą WHERE obsługują dodatkowe filtrowanie:

- [Utwórz lub zaktualizuj zasady *rozszerzonej* inspekcji bazy danych](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Tworzenie lub aktualizowanie zasad inspekcji *rozszerzonej* serwera](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Pobierz zasady inspekcji *rozszerzonej* bazy danych](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Pobieranie zasad inspekcji *rozszerzonej* serwera](/rest/api/sql/server%20auditing%20settings/get)

## <a id="subheading-9"></a>Zarządzanie usługą Azure SQL Server i inspekcją bazy danych przy użyciu szablonów Azure Resource Manager

Inspekcją usługi Azure SQL Database można zarządzać przy użyciu szablonów [Azure Resource Manager](../azure-resource-manager/management/overview.md) , jak pokazano w poniższych przykładach:

- [Wdrożenie SQL Server platformy Azure z włączoną inspekcją w celu zapisania dzienników inspekcji na koncie usługi Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Wdróż SQL Server platformy Azure z włączoną inspekcją w celu zapisania dzienników inspekcji do Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Wdróż SQL Server platformy Azure z włączoną inspekcją w celu zapisania dzienników inspekcji do Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> Połączone przykłady znajdują się w zewnętrznym repozytorium publicznym i są dostarczane w postaci "AS IS", bez rękojmi i nie są obsługiwane w ramach żadnego programu lub usługi pomocy technicznej firmy Microsoft.

<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Manage Azure SQL Server and Database auditing using Azure PowerShell]: #subheading-7
[Manage SQL database auditing using REST API]: #subheading-8
[Manage Azure SQL Server and Database auditing using ARM templates]: #subheading-9

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_ssms_2.png 
