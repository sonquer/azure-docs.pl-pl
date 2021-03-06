---
title: pozyskiwanie danych z centrum zdarzeń do usługi Azure Data Explorer
description: W tym artykule dowiesz się, jak pozyskiwanie (ładować) danych do usługi Azure Eksplorator danych z centrum zdarzeń.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: bb9357ca4388bd1fb7ae3e3704cf4112d07c1105
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188186"
---
# <a name="ingest-data-from-event-hub-into-azure-data-explorer"></a>pozyskiwanie danych z centrum zdarzeń do usługi Azure Data Explorer

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Szablon usługi Azure Resource Manager](data-connection-event-hub-resource-manager.md)

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Usługa Azure Data Explorer umożliwia pozyskiwanie (ładowanie) danych z usługi Event Hubs — platformy do strumieniowego przesyłania dużych ilości danych i usługi pozyskiwania zdarzeń. Usługa [Event Hubs](/azure/event-hubs/event-hubs-about) może przetwarzać miliony zdarzeń na sekundę niemal w czasie rzeczywistym. W tym artykule opisano tworzenie centrum zdarzeń, nawiązywanie z nim połączenia z usługi Azure Eksplorator danych i wyświetlanie przepływu danych przez system.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* [Klaster testowy i baza danych](create-cluster-database-portal.md).
* [Przykładowa aplikacja](https://github.com/Azure-Samples/event-hubs-dotnet-ingest), która generuje dane i wysyła je do centrum zdarzeń. Pobierz przykładową aplikację w systemie.
* [Program Visual Studio 2019](https://visualstudio.microsoft.com/vs/) do uruchamiania przykładowej aplikacji.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Azure portal](https://portal.azure.com/).

## <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

W tym artykule opisano generowanie przykładowych danych i wysyłanie ich do centrum zdarzeń. Pierwszym krokiem jest utworzenie centrum zdarzeń. Możesz to zrobić, używając szablonu usługi Azure Resource Manager w witrynie Azure Portal.

1. Aby utworzyć centrum zdarzeń, użyj poniższego przycisku w celu rozpoczęcia wdrażania. Kliknij prawym przyciskiem myszy i wybierz pozycję **Utwórz w nowym oknie**, aby wykonać pozostałe kroki w tym artykule.

    [![Wdrażanie na platformie Azure](media/ingest-data-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

    Przycisk **Wdróż na platformie Azure** powoduje przejście do witryny Azure Portal w celu wypełnienia formularza wdrożenia.

    ![Wdrażanie na platformie Azure](media/ingest-data-event-hub/deploy-to-azure.png)

1. Wybierz subskrypcję, w której chcesz utworzyć centrum zdarzeń, i utwórz grupę zasobów o nazwie *test-hub-rg*.

    ![Utwórz grupę zasobów](media/ingest-data-event-hub/create-resource-group.png)

1. Wypełnij formularz, używając poniższych informacji.

    ![Formularz wdrożenia](media/ingest-data-event-hub/deployment-form.png)

    W przypadku wszystkich ustawień, które nie są wymienione w poniższej tabeli, użyj ustawień domyślnych.

    **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Subskrypcja | Twoja subskrypcja | Wybierz subskrypcję platformy Azure, która ma być używana dla centrum zdarzeń.|
    | Grupa zasobów | *test-hub-rg* | Utwórz nową grupę zasobów. |
    | Lokalizacja | *Zachodnie stany USA* | W tym artykule wybierz pozycję *zachodnie stany USA* . W przypadku systemu produkcyjnego wybierz region, który najlepiej odpowiada Twoim potrzebom. Utwórz przestrzeń nazw centrum zdarzeń w tej samej lokalizacji co klaster Kusto w celu zapewnienia najlepszej wydajności (jest to szczególnie ważne w przypadku przestrzeni nazw centrum zdarzeń o dużej przepływności).
    | Nazwa przestrzeni nazw | Unikatowa nazwa przestrzeni nazw | Wybierz unikatową nazwę, która identyfikuje Twoją przestrzeń nazw. Na przykład *mytestnamespace*. Do podanej nazwy jest dołączana nazwa domeny *servicebus.windows.net*. Nazwa może zawierać tylko litery, cyfry i łączniki. Nazwa musi zaczynać się literą i kończyć literą lub cyfrą. Nazwa musi mieć długość od 6 do 50 znaków.
    | Nazwa centrum zdarzeń | *test-hub* | Centrum zdarzeń znajduje się w przestrzeni nazw, która zapewnia unikatowy kontener określania zakresu. Nazwa centrum zdarzeń musi być unikatowa w obrębie przestrzeni nazw. |
    | Nazwa grupy konsumentów | *test-group* | Dzięki grupom konsumentów każda z wielu aplikacji korzystających z danych może mieć osobny widok strumienia zdarzeń. |
    | | |

1. Wybierz pozycję **Zakup**, która potwierdza, że tworzysz zasoby w ramach swojej subskrypcji.

1. Wybierz pozycję **Powiadomienia** na pasku narzędzi, aby monitorować proces aprowizacji. Pomyślne zakończenie wdrożenia może zająć kilka minut, ale możesz teraz przejść do następnego kroku.

    ![Powiadomienia](media/ingest-data-event-hub/notifications.png)

## <a name="create-a-target-table-in-azure-data-explorer"></a>Tworzenie tabeli docelowej w usłudze Azure Data Explorer

Teraz w usłudze Azure Data Explorer utworzysz tabelę, do której będą wysyłane dane z usługi Event Hubs. Tabela zostanie utworzona w klastrze i bazie danych, które były aprowizowane w sekcji **Wymagania wstępne**.

1. W witrynie Azure Portal przejdź do klastra, a następnie wybierz pozycję **Zapytanie**.

    ![Link do aplikacji Zapytanie](media/ingest-data-event-hub/query-explorer-link.png)

1. Skopiuj poniższe polecenie w oknie, a następnie wybierz pozycję **Uruchom**, aby utworzyć tabelę (TestTable), w której będą umieszczane pozyskiwane dane.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

    ![Uruchamianie zapytania create](media/ingest-data-event-hub/run-create-query.png)

1. Skopiuj poniższe polecenie w oknie, a następnie wybierz pozycję **Uruchom**, aby zamapować przychodzące dane w formacie JSON na nazwy kolumn i typy danych tabeli (TestTable).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.timeStamp","datatype":"datetime"},{"column":"Name","path":"$.name","datatype":"string"},{"column":"Metric","path":"$.metric","datatype":"int"},{"column":"Source","path":"$.source","datatype":"string"}]'
    ```

## <a name="connect-to-the-event-hub"></a>Łączenie z centrum zdarzeń

Teraz połączysz się z centrum zdarzeń z usługi Azure Data Explorer. Po nawiązaniu tego połączenia dane trafiające do centrum zdarzeń będą przesyłane strumieniowo do tabeli testowej utworzonej we wcześniejszej części tego artykułu.

1. Wybierz pozycję **Powiadomienia** na pasku narzędzi, aby sprawdzić, czy wdrożenie centrum zdarzeń zakończyło się pomyślnie.

1. W obszarze utworzonego klastra wybierz pozycję **Bazy danych**, a następnie pozycję **TestDatabase**.

    ![Wybieranie testowej bazy danych](media/ingest-data-event-hub/select-test-database.png)

1. Wybierz kolejno pozycje **Pozyskiwanie danych** i **Dodaj połączenie danych**. Następnie wypełnij formularz, używając poniższych informacji. Po zakończeniu wybierz pozycję **Utwórz**.

    ![Połączenie centrum zdarzeń](media/ingest-data-event-hub/event-hub-connection.png)

    **Źródło danych:**

    **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Nazwa połączenia danych | *test-hub-connection* | Nazwa połączenia, które chcesz utworzyć w usłudze Azure Data Explorer.|
    | Przestrzeń nazw centrum zdarzeń | Unikatowa nazwa przestrzeni nazw | Wybrana wcześniej nazwa, która identyfikuje Twoją przestrzeń nazw. |
    | Centrum zdarzeń | *test-hub* | Utworzone przez Ciebie centrum zdarzeń. |
    | Grupa konsumentów | *test-group* | Grupa konsumentów zdefiniowana w utworzonym przez Ciebie centrum zdarzeń. |
    | Właściwości systemu zdarzeń | Wybierz odpowiednie właściwości | [Właściwości systemu centrum zdarzeń](/azure/service-bus-messaging/service-bus-amqp-protocol-guide#message-annotations). Jeśli istnieje wiele rekordów dla każdego komunikatu o zdarzeniu, właściwości systemu zostaną dodane do pierwszej z nich. Podczas dodawania właściwości systemu [Utwórz](/azure/kusto/management/create-table-command) lub [zaktualizuj](/azure/kusto/management/alter-table-command) schemat i [Mapowanie](/azure/kusto/management/mappings) tabeli w celu uwzględnienia wybranych właściwości. |
    | Kompresja | *Dawaj* | Typ kompresji ładunku komunikatów centrum zdarzeń. Obsługiwane typy kompresji: *Brak, gzip*.|
    | | |

    **Tabela docelowa:**

    Dostępne są dwie opcje routingu pozyskiwanych danych: *statyczne* i *dynamiczne*. 
    W tym artykule należy używać routingu statycznego, w którym można określić nazwę tabeli, format danych i mapowanie. W związku z tym pozostaw pole **Moje dane zawierają informacje o routingu** niezaznaczone.

     **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Tabela | *TestTable* | Tabela utworzona przez Ciebie w obszarze **TestDatabase**. |
    | Format danych | *JSON* | Obsługiwane formaty to Avro, CSV, JSON, WIELOWIERSZOWY kod JSON, PSV, SOHSV, SCSV, TSV, TSVE, TXT, ORC i PARQUET. |
    | Mapowanie kolumn | *TestMapping* | [Mapowanie](/azure/kusto/management/mappings) utworzone w **TestDatabase**, które mapuje przychodzące dane JSON do nazw kolumn i typów **danych.** Wymagane dla notacji JSON lub wielowierszowego kodu JSON oraz opcjonalne dla innych formatów.|
    | | |

    > [!NOTE]
    > * Wybierz pozycję **moje dane zawiera informacje o routingu** , aby użyć routingu dynamicznego, gdzie dane zawierają niezbędne informacje dotyczące routingu, jak pokazano w komentarzach [przykładowych aplikacji](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) . Jeśli są ustawione właściwości static i Dynamic, właściwości dynamiczne zastępują statyczne. 
    > * Zostaną pozyskane tylko zdarzenia znajdujące się w kolejce po utworzeniu połączenia danych.
    > * Możesz również ustawić typ kompresji za pomocą właściwości dynamicznych, jak pokazano w [przykładowej aplikacji](https://github.com/Azure-Samples/event-hubs-dotnet-ingest).
    > * Formaty Avro, ORC i PARQUET, a także właściwości systemu zdarzeń nie są obsługiwane w ładunku kompresji GZip.

[!INCLUDE [data-explorer-container-system-properties](../../includes/data-explorer-container-system-properties.md)]

## <a name="copy-the-connection-string"></a>Kopiowanie parametrów połączenia

Gdy uruchamiasz [przykładową aplikację](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) wymienioną w sekcji Wymagania wstępne, potrzebujesz parametrów połączenia dla przestrzeni nazw centrum zdarzeń.

1. W obszarze utworzonej przez Ciebie przestrzeni nazw centrum zdarzeń wybierz pozycję **Zasady dostępu współużytkowanego**, a następnie pozycję **RootManageSharedAccessKey**.

    ![Zasady dostępu współużytkowanego](media/ingest-data-event-hub/shared-access-policies.png)

1. Skopiuj zawartość pola **Parametry połączenia — klucz podstawowy**. Wkleisz ją w następnej sekcji.

    ![Parametry połączenia](media/ingest-data-event-hub/connection-string.png)

## <a name="generate-sample-data"></a>Generowanie danych przykładowych

Użyj pobranej [przykładowej aplikacji](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) do wygenerowania danych.

1. Otwórz przykładowe rozwiązanie aplikacji w programie Visual Studio.

1. W pliku *program.cs* zaktualizuj stałą `connectionString` tak, aby zawierała parametry połączenia skopiowane z przestrzeni nazw centrum zdarzeń.

    ```csharp
    const string eventHubName = "test-hub";
    // Copy the connection string ("Connection string-primary key") from your Event Hub namespace.
    const string connectionString = @"<YourConnectionString>";
    ```

1. Skompiluj i uruchom aplikację. Aplikacja wysyła komunikaty do centrum zdarzeń i co dziesięć sekund wyświetla stan.

1. Po wysłaniu przez aplikację kilku komunikatów przejdź do następnego kroku: przeglądania przepływu danych do centrum zdarzeń i tabeli testowej.

## <a name="review-the-data-flow"></a>Przeglądanie przepływu danych

Kiedy aplikacja generuje dane, możesz teraz zobaczyć przepływ tych danych z centrum zdarzeń do tabeli w klastrze.

1. W witrynie Azure Portal w obszarze centrum zdarzeń zobaczysz wzrost aktywności, gdy aplikacja jest uruchomiona.

    ![Wykres centrum zdarzeń](media/ingest-data-event-hub/event-hub-graph.png)

1. Aby sprawdzić, ile komunikatów zostało przekazanych do tej pory do bazy danych, uruchom poniższe zapytanie w testowej bazie danych.

    ```Kusto
    TestTable
    | count
    ```

1. Aby sprawdzić zawartość komunikatów, uruchom następujące zapytanie:

    ```Kusto
    TestTable
    ```

    Zestaw wyników powinien wyglądać podobnie do następującego:

    ![Zestaw wyników komunikatów](media/ingest-data-event-hub/message-result-set.png)

    > [!NOTE]
    > * W systemie Azure Data Explorer istnieją zasady agregacji (dzielenie na partie) dotyczące pozyskiwania danych opracowane w celu optymalizacji procesu pozyskiwania. Zasady są domyślnie skonfigurowane do 5 minut lub 500 MB danych, dzięki czemu mogą wystąpić opóźnienia. Zobacz temat [zasady tworzenia wsadowego](/azure/kusto/concepts/batchingpolicy) dla opcji agregacji. 
    > * Pozyskanie centrum zdarzeń obejmuje czas odpowiedzi z centrum zdarzeń wynoszący 10 sekund lub 1 MB. 
    > * Skonfiguruj tabelę do obsługi przesyłania strumieniowego i Usuń opóźnienie w czasie odpowiedzi. Zobacz [zasady przesyłania strumieniowego](/azure/kusto/concepts/streamingingestionpolicy). 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz ponownie używać centrum zdarzeń, wyczyść grupę zasobów **test-hub-rg**, aby uniknąć ponoszenia kosztów.

1. W witrynie Azure Portal wybierz **grupy zasobów** daleko po lewej stronie, a następnie wybierz utworzoną grupę zasobów.  

    Jeśli menu po lewej stronie jest zwinięte, wybierz ![przycisk Rozwiń,](media/ingest-data-event-hub/expand.png) aby je rozwinąć.

   ![Wybieranie grupy zasobów do usunięcia](media/ingest-data-event-hub/delete-resources-select.png)

1. W obszarze **test-resource-group** wybierz pozycję **Usuń grupę zasobów**.

1. W nowym oknie wpisz nazwę grupy zasobów do usunięcia (*test-hub-rg*), a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

* [Wykonywanie zapytań dotyczących danych w usłudze Azure Eksplorator danych](web-query-data.md)
