---
title: Wykrywanie oszustw w czasie rzeczywistym przy użyciu Azure Stream Analytics
description: Dowiedz się, jak utworzyć rozwiązanie do wykrywania oszustw w czasie rzeczywistym za pomocą Stream Analytics. Użyj centrum zdarzeń do przetwarzania zdarzeń w czasie rzeczywistym.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 0273a0a729d39de27b9e417c23624992d1d55b42
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064404"
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Wprowadzenie do korzystania z Azure Stream Analytics: wykrywanie oszustw w czasie rzeczywistym

Ten samouczek zawiera kompleksową ilustrację sposobu korzystania z Azure Stream Analytics. Omawiane kwestie: 

* Przenieś zdarzenia przesyłania strumieniowego do wystąpienia usługi Azure Event Hubs. W tym samouczku zostanie użyta aplikacja, która symuluje strumień rekordów metadanych urządzeń przenośnych.

* Zapisuj zapytania Stream Analytics, podobne do kodu SQL, w celu przekształcania danych, agregowania informacji lub szukania wzorców. Zobaczysz, jak użyć zapytania, aby sprawdzić strumień przychodzący i wyszukać wywołania, które mogą być fałszywe.

* Wyślij wyniki do ujścia danych wyjściowych (magazynu), które można analizować pod kątem dodatkowych szczegółowych informacji. W takim przypadku należy wysłać podejrzane dane wywołania do usługi Azure Blob Storage.

W tym samouczku zastosowano przykład wykrywania oszustw w czasie rzeczywistym na podstawie danych połączeń telefonicznych. Zilustrowana technika jest również odpowiednia dla innych rodzajów wykrycia oszustw, takich jak oszustwa kart kredytowych lub kradzież tożsamości. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Scenariusz: wykrywanie oszustwa telekomunikacyjnego i karty SIM w czasie rzeczywistym

Firma telekomunikacyjna ma dużą ilość danych dla wywołań przychodzących. Firma chce wykrywać oszukańcze wywołania w czasie rzeczywistym, aby mogły powiadamiać klientów lub wyłączać usługi pod kątem określonego numeru. Jeden typ oszustwa SIM obejmuje wiele wywołań z tej samej tożsamości w tym samym czasie, ale w lokalizacjach geograficznych. Aby wykryć ten rodzaj oszustw, firma musi sprawdzić przychodzące rekordy telefonu i poszukać określonych wzorców — w tym przypadku dla wywołań wykonanych w różnych krajach/regionach. Wszystkie rekordy telefonów, które znajdują się w tej kategorii, są zapisywane w magazynie na potrzeby dalszej analizy.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku Zasymulowano dane połączeń telefonicznych za pomocą aplikacji klienckiej, która generuje przykładowe metadane połączeń telefonicznych. Niektóre rekordy generowane przez aplikację wyglądają jak fałszywe wywołania. 

Przed rozpoczęciem upewnij się, że dysponujesz następującymi elementami:

* Konto platformy Azure.
* Aplikacja generatora zdarzeń wywołania [TelcoGenerator. zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip), którą można pobrać z centrum pobierania Microsoft. Rozpakuj ten pakiet do folderu na komputerze. Jeśli chcesz zobaczyć kod źródłowy i uruchomić aplikację w debugerze, możesz pobrać kod źródłowy aplikacji z usługi [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator). 

    >[!NOTE]
    >System Windows może zablokować pobrany plik. zip. Jeśli nie możesz go rozpakować, kliknij prawym przyciskiem myszy plik i wybierz polecenie **Właściwości**. Jeśli zostanie wyświetlony komunikat "ten plik pochodzi z innego komputera i może zostać zablokowany, aby pomóc w ochronie tego komputera", wybierz opcję **Odblokuj** , a następnie kliknij przycisk **Zastosuj**.

Jeśli chcesz przejrzeć wyniki zadania analizy przesyłania strumieniowego, potrzebujesz również narzędzia do wyświetlania zawartości kontenera Blob Storage platformy Azure. Jeśli używasz programu Visual Studio, możesz użyć [narzędzi platformy Azure dla programu Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) lub [programu Visual Studio Cloud Explorer](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer). Alternatywnie można zainstalować autonomiczne narzędzia, takie jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/) lub [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>Tworzenie Event Hubs platformy Azure w celu pozyskiwania zdarzeń

Aby analizować strumień danych, pozyskasz *go na* platformie Azure. Typowym sposobem przyjmowania danych jest korzystanie z [usługi Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md), która pozwala na pozyskiwanie milionów zdarzeń na sekundę, a następnie przetwarzanie i przechowywanie informacji o zdarzeniach. W tym samouczku utworzysz centrum zdarzeń, a następnie aplikacja generatora zdarzeń będzie wysyłać dane wywołania do tego centrum zdarzeń. Więcej informacji na temat centrów zdarzeń znajduje się w [dokumentacji Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).

>[!NOTE]
>Aby uzyskać bardziej szczegółową wersję tej procedury, zobacz [Tworzenie przestrzeni nazw Event Hubs i centrum zdarzeń przy użyciu Azure Portal](../event-hubs/event-hubs-create.md). 

### <a name="create-a-namespace-and-event-hub"></a>Tworzenie przestrzeni nazw i Centrum zdarzeń
W tej procedurze należy najpierw utworzyć przestrzeń nazw centrum zdarzeń, a następnie dodać centrum zdarzeń do tej przestrzeni nazw. Przestrzenie nazw centrum zdarzeń są używane do logicznego pogrupowania powiązanych wystąpień magistrali zdarzeń. 

1. Zaloguj się do Azure Portal, a następnie kliknij pozycję **Utwórz zasób** w lewym górnym rogu ekranu.

2. Wybierz pozycję **Wszystkie usługi** w menu po lewej stronie, a następnie wybierz **gwiazdkę (`*`)** obok pozycji **Event Hubs** w kategorii **Analiza**. Upewnij się, że usługa **Event Hubs** została dodana do kategorii **ULUBIONE** w menu nawigacji po lewej stronie. 

   ![Wyszukiwanie usługi Event Hubs](./media/stream-analytics-real-time-fraud-detection/select-event-hubs-menu.png)

3. Wybierz pozycję **Event Hubs** w obszarze **ULUBIONE** w menu nawigacji po lewej stronie, a następnie wybierz pozycję **Dodaj** na pasku narzędzi.

   ![Przycisk Dodaj](./media/stream-analytics-real-time-fraud-detection/event-hubs-add-toolbar.png)

4. W okienku **Tworzenie przestrzeni nazw** wprowadź nazwę przestrzeni nazw, taką jak `<yourname>-eh-ns-demo`. Możesz użyć dowolnej nazwy dla przestrzeni nazw, ale nazwa musi być prawidłowa dla adresu URL i musi być unikatowa na platformie Azure. 
    
5. Wybierz subskrypcję i Utwórz lub wybierz grupę zasobów, a następnie kliknij przycisk **Utwórz**.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png" alt="Create event hub namespace in Azure portal" width="300px"/>

6. Po zakończeniu wdrażania obszaru nazw Znajdź obszar nazw centrum zdarzeń na liście zasobów platformy Azure. 

7. Kliknij nową przestrzeń nazw, a następnie w okienku przestrzeń nazw kliknij pozycję **centrum zdarzeń**.

   ![Przycisk Dodaj centrum zdarzeń służący do tworzenia nowego centrum zdarzeń](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
8. Nazwij nowe centrum zdarzeń `asa-eh-frauddetection-demo`. Możesz użyć innej nazwy. Jeśli to zrobisz, zanotuj je, ponieważ potrzebujesz tej nazwy później. Nie musisz teraz ustawiać żadnych innych opcji centrum zdarzeń.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png" alt="Name event hub in Azure portal" width="400px"/>
    
9. Kliknij pozycję **Utwórz**.

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Udzielanie dostępu do centrum zdarzeń i pobieranie parametrów połączenia

Aby proces mógł wysyłać dane do centrum zdarzeń, centrum zdarzeń musi mieć zasady zezwalające na odpowiedni dostęp. Zasady dostępu generują parametry połączenia, które zawierają informacje o autoryzacji.

1.  W okienku przestrzeń nazw zdarzenia kliknij **Event Hubs** a następnie kliknij nazwę nowego centrum zdarzeń.

2.  W okienku centrum zdarzeń kliknij pozycję **zasady dostępu współdzielonego** , a następnie kliknij pozycję **+&nbsp;Dodaj**.

    >[!NOTE]
    >Upewnij się, że pracujesz z centrum zdarzeń, a nie z przestrzeni nazw centrum zdarzeń.

3.  Dodaj zasady o nazwie `sa-policy-manage-demo` i dla usługi **Claims**, a następnie wybierz pozycję **Zarządzaj**.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png" alt="Create shared access policy for Stream Analytics" width="300px"/>
 
4.  Kliknij pozycję **Utwórz**.

5.  Po wdrożeniu zasad kliknij je na liście zasad dostępu współdzielonego.

6.  Znajdź pole z etykietą **Parametry połączenia — klucz podstawowy** i kliknij przycisk kopiowania obok parametrów połączenia. 

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png" alt="Stream Analytics shared access policy" width="300px"/>
 
7.  Wklej parametry połączenia do edytora tekstu. Te parametry połączenia są potrzebne dla następnej sekcji, po wprowadzeniu niewielkich zmian.

    Parametry połączenia wyglądają następująco:

        Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=asa-eh-frauddetection-demo

    Należy zauważyć, że parametry połączenia zawierają wiele par klucz-wartość, rozdzielone średnikami: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`i `EntityPath`.  

## <a name="configure-and-start-the-event-generator-application"></a>Konfigurowanie i uruchamianie aplikacji generatora zdarzeń

Przed uruchomieniem aplikacji TelcoGenerator należy ją skonfigurować tak, aby wysyłali rekordy wywołań do tworzonego centrum zdarzeń.

### <a name="configure-the-telcogenerator-app"></a>Konfigurowanie aplikacji TelcoGenerator

1. W edytorze, w którym skopiowano parametry połączenia, zanotuj wartość `EntityPath`, a następnie usuń parę `EntityPath` (nie zapomnij usunąć średnika, który poprzedza go). 

2. W folderze, w którym został rozpakowany plik TelcoGenerator. zip, Otwórz plik telcodatagen. exe. config w edytorze. (Istnieje więcej niż jeden plik config. Upewnij się, że otwarto jeden z nich).

3. W `<appSettings>` elementu:

   * Ustaw wartość klucza `EventHubName` na nazwę centrum zdarzeń (czyli wartość ścieżki jednostki).
   * Ustaw wartość klucza `Microsoft.ServiceBus.ConnectionString` na parametry połączenia. 

   Sekcja `<appSettings>` będzie wyglądać podobnie do poniższego przykładu. (W przypadku przejrzystości wiersze są opakowane i niektóre znaki zostały usunięte z tokenu autoryzacji).

   ![Plik konfiguracji TelcoGenerator zawiera nazwę centrum zdarzeń i parametry połączenia](./media/stream-analytics-real-time-fraud-detection/stream-analytics-telcogenerator-config-file-app-settings.png)
 
4. Zapisz plik. 

### <a name="start-the-app"></a>Uruchom aplikację
1.  Otwórz okno polecenia i przejdź do folderu, w którym znajduje się aplikacja TelcoGenerator.

2.  Wprowadź następujące polecenie:

   ```cmd
   telcodatagen.exe 1000 0.2 2
   ```

   Parametry są następujące: 

   * Liczba CDRs na godzinę. 
   * Prawdopodobieństwo oszustwa kart SIM: jak często jako procent wszystkich wywołań, aplikacja powinna symulować fałszywe wywołanie. Wartość 0,2 oznacza, że około 20% rekordów połączeń będzie wyglądać na fałszywe.
   * Czas trwania w godzinach. Liczba godzin uruchomienia aplikacji. Możesz również zatrzymać aplikację w dowolnym momencie, naciskając klawisze CTRL + C w wierszu polecenia.

   Po kilku sekundach aplikacja rozpocznie wyświetlanie rekordów połączeń telefonicznych na ekranie w miarę wysyłania ich do centrum zdarzeń.

Niektóre z najważniejszych pól, które będą używane w tej aplikacji do wykrywania oszustw w czasie rzeczywistym, są następujące:

|**Rekord**|**Definicja**|
|----------|--------------|
|`CallrecTime`|Znacznik czasu godziny rozpoczęcia połączenia. |
|`SwitchNum`|Centrala telefoniczna używana do wykonania połączenia. W tym przykładzie przełączniki są ciągami reprezentującymi kraj/region pochodzenia (USA, Chiny, Zjednoczone Królestwo, Niemcy lub Australia). |
|`CallingNum`|Numer telefonu dzwoniącego. |
|`CallingIMSI`|Numer IMSI (International Mobile Subscriber Identity). To jest unikatowy identyfikator obiektu wywołującego. |
|`CalledNum`|Numer telefonu odbiorcy połączenia. |
|`CalledIMSI`|Numer IMSI (International Mobile Subscriber Identity). To jest unikatowy identyfikator odbiorcy rozmowy. |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>Utwórz zadanie Stream Analytics, aby zarządzać danymi przesyłanymi strumieniowo

Teraz, gdy masz strumień zdarzeń wywołań, możesz skonfigurować zadanie Stream Analytics. Zadanie będzie odczytywać dane z centrum zdarzeń, które zostało skonfigurowane. 

### <a name="create-the-job"></a>Tworzenie zadania 

1. W Azure Portal kliknij pozycję **Utwórz zasób** > **Internet rzeczy** > **zadanie Stream Analytics**.

2. Nazwij `asa_frauddetection_job_demo`zadania, określ subskrypcję, grupę zasobów i lokalizację.

    Dobrym pomysłem jest umieszczenie zadania i centrum zdarzeń w tym samym regionie w celu uzyskania najlepszej wydajności, aby nie zapłaciła za transfer danych między regionami.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png" alt="Create Stream Analytics job in portal" width="300px"/>

3. Kliknij pozycję **Utwórz**.

    Zadanie zostanie utworzone, a w portalu zostaną wyświetlone szczegóły zadania. Nic nie działa jeszcze, ale musisz skonfigurować zadanie, aby można było je uruchomić.

### <a name="configure-job-input"></a>Konfigurowanie danych wejściowych zadania

1. W okienku pulpit nawigacyjny lub **wszystkie zasoby** Znajdź i wybierz zadanie `asa_frauddetection_job_demo` Stream Analytics. 
2. W sekcji **Przegląd** okienka zadania Stream Analytics kliknij pole **dane wejściowe** .

   ![Pole wejściowe w obszarze Topologia w okienku Zadania usługi Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. Kliknij pozycję **Dodaj strumień wejściowy** i wybierz pozycję **centrum zdarzeń**. Następnie wypełnij nową stronę wejściową, podając następujące informacje:

   |**Ustawienie**  |**Sugerowana wartość**  |**Opis**  |
   |---------|---------|---------|
   |Alias danych wejściowych  |  CallStream   |  Wprowadź nazwę identyfikującą dane wejściowe zadania.   |
   |Subskrypcja   |  \<Twoja subskrypcja\> |  Wybierz subskrypcję platformy Azure z utworzonym centrum zdarzeń.   |
   |Przestrzeń nazw centrum zdarzeń  |  ASA-EH-NS-Demonstracja |  Wprowadź nazwę przestrzeni nazw centrum zdarzeń.   |
   |Nazwa centrum zdarzeń  | asa-eh-frauddetection-demo | Wybierz nazwę centrum zdarzeń.   |
   |Nazwa zasad centrum zdarzeń  | ASA — zasady — zarządzanie — Demonstracja | Wybierz utworzone wcześniej zasady dostępu.   |

    </br>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png" alt="Create Stream Analytics input in portal" width="300px"/>


4. Kliknij pozycję **Utwórz**.

## <a name="create-queries-to-transform-real-time-data"></a>Tworzenie zapytań w celu przekształcania danych w czasie rzeczywistym

W tym momencie masz zadanie Stream Analytics skonfigurowane do odczytywania przychodzącego strumienia danych. Następnym krokiem jest utworzenie zapytania, które analizuje dane w czasie rzeczywistym. Stream Analytics obsługuje prosty, deklaratywny model zapytań, który opisuje przekształcenia do przetwarzania w czasie rzeczywistym. Zapytania korzystają z języka przypominającego SQL, który ma Niektóre rozszerzenia specyficzne dla Stream Analytics. 

Proste zapytanie może po prostu odczytać wszystkie dane przychodzące. Jednak często tworzone są zapytania, które szukają określonych danych lub relacji w danych. W tej części samouczka utworzysz i testujesz kilka zapytań, aby dowiedzieć się więcej na temat tego, w jaki sposób można przekształcić strumień wejściowy na potrzeby analizy. 

Zapytania utworzone w tym miejscu będą po prostu wyświetlać przekształcone dane na ekranie. W dalszej części skonfigurujesz ujścia danych wyjściowych i zapytanie, które zapisuje przekształcone dane do tego ujścia.

Aby dowiedzieć się więcej o języku, zobacz [informacje dotyczące języka zapytań Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

### <a name="get-sample-data-for-testing-queries"></a>Pobierz przykładowe dane do testowania zapytań

Aplikacja TelcoGenerator wysyła rekordy wywołań do centrum zdarzeń, a zadanie Stream Analytics jest skonfigurowane do odczytywania danych z centrum zdarzeń. Można użyć zapytania, aby przetestować zadanie, aby upewnić się, że jest ono prawidłowo odczytywane. Aby przetestować zapytanie w konsoli platformy Azure, potrzebne są przykładowe dane. W tym instruktażu wyodrębnisz przykładowe dane ze strumienia, który jest przychodzący do centrum zdarzeń.

1. Upewnij się, że aplikacja TelcoGenerator jest uruchomiona i generuje rekordy wywołań.
2. W portalu Wróć do okienka zadania usługi Stream Analytics. (Jeśli okienko zostało zamknięte, Wyszukaj `asa_frauddetection_job_demo` w okienku **wszystkie zasoby** ).
3. Kliknij pole **zapytanie** . Na platformie Azure są wyświetlane dane wejściowe i wyjściowe, które są skonfigurowane dla zadania, a także można utworzyć zapytanie, które pozwala przekształcić strumień wejściowy w taki sposób, w jaki jest wysyłany do danych wyjściowych.
4. W okienku **zapytania** kliknij punkty obok `CallStream` dane wejściowe, a następnie wybierz pozycję **dane przykładowe z danych wejściowych**.

   ![Opcje menu służące do korzystania z przykładowych danych dla wpisu zadania usługi Stream Analytics z wybraną opcją "przykładowe dane z danych wejściowych"](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)


5. Ustaw **minuty** na 3, a następnie kliknij przycisk **OK**. 
    
   ![Opcje próbkowania strumienia wejściowego z 3 minutach wybranych](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    Dane z strumienia wejściowego są dostępne 3 minuty i powiadamiane, gdy dane przykładowe są gotowe. (Trwa to krótko). 

Przykładowe dane są przechowywane tymczasowo i są dostępne, kiedy okno zapytania jest otwarte. Jeśli zamkniesz okno zapytania, przykładowe dane zostaną usunięte i konieczne będzie utworzenie nowego zestawu przykładowych danych. 

Alternatywnie można pobrać plik JSON, który zawiera przykładowe dane [z usługi GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json), a następnie przekazać plik JSON do użycia jako przykładowe dane wejściowe `CallStream`. 

### <a name="test-using-a-pass-through-query"></a>Testowanie przy użyciu kwerendy przekazującej

Jeśli chcesz zarchiwizować każde zdarzenie, możesz użyć zapytania przekazującego, aby odczytać wszystkie pola w ładunku zdarzenia.

1. W oknie zapytania wprowadź następujące zapytanie:
        
   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >Podobnie jak w przypadku języka SQL słowa kluczowe nie są rozróżniane wielkości liter, a odstępy nie są istotne.

    W tym zapytaniu `CallStream` jest alias, który został określony podczas tworzenia danych wejściowych. Jeśli użyto innego aliasu, Użyj tej nazwy zamiast tego.

2. Kliknij przycisk **Testuj**.

    Zadanie Stream Analytics uruchamia zapytanie względem przykładowych danych i wyświetla dane wyjściowe w dolnej części okna. Wyniki wskazują, że centrum zdarzeń i zadanie usługi Stream Analytics są prawidłowo skonfigurowane. (Jak wspomniano w dalszej części, utworzysz obiekt ujścia danych wyjściowych, do którego zapytanie może zapisywać dane).

   ![Stream Analytics dane wyjściowe zadania, wyświetlane są 73 rekordów](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    Dokładna liczba rekordów, które widzisz, zależy od liczby rekordów przechwyconych w przykładowym przykładzie 3-minutowym.
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Zmniejsz liczbę pól przy użyciu projekcji kolumny

W wielu przypadkach analiza nie potrzebuje wszystkich kolumn ze strumienia wejściowego. Można użyć zapytania do zaprojektowania mniejszego zestawu zwracanych pól niż w zapytaniu przekazującym.

1. Zmień zapytanie w edytorze kodu na następujące:

   ```SQL
   SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
   FROM 
       CallStream
   ```

2. Kliknij ponownie przycisk **Testuj** . 

   ![Dane wyjściowe zadania Stream Analytics dla projekcji pokazują 25 rekordów](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Liczba wywołań przychodzących według regionu: okno wirowania z agregacją

Załóżmy, że chcesz policzyć liczbę wywołań przychodzących na region. W przypadku przesyłania strumieniowego danych, gdy chcesz wykonać funkcje agregujące, takie jak zliczanie, musisz podzielić strumień na jednostki danych czasowych (ponieważ sam strumień danych jest efektywnie nieskończony). Można to zrobić przy użyciu [funkcji okna](stream-analytics-window-functions.md)analizy przesyłania strumieniowego. Następnie można współpracować z danymi wewnątrz tego okna jako jednostką.

W przypadku tej transformacji potrzebna jest sekwencja okien czasowych, które nie nakładają się — każde okno będzie miało dyskretny zestaw danych, które można zgrupować i agregować. Ten typ okna jest określany mianem *okna wirowania*. W oknie wirowania można uzyskać liczbę wywołań przychodzących pogrupowanych według `SwitchNum`, która reprezentuje kraj/region, w którym wywołano wywołanie. 

1. Zmień zapytanie w edytorze kodu na następujące:

        ```SQL
        SELECT 
            System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
        FROM
            CallStream TIMESTAMP BY CallRecTime 
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
        ```

    To zapytanie używa słowa kluczowego `Timestamp By` w klauzuli `FROM` do określenia pola sygnatury czasowej w strumieniu wejściowym, który ma być używany do definiowania okna wirowania. W takim przypadku okno dzieli dane na segmenty według pola `CallRecTime` w każdym rekordzie. (Jeśli nie określono żadnego pola, operacja okna używa czasu, w którym każde zdarzenie dociera do centrum zdarzeń. Zobacz "godzina dostarczenia a czas aplikacji" w [Stream Analytics Dokumentacja języka zapytań](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). 

    Projekcja zawiera `System.Timestamp`, która zwraca sygnaturę czasową końca każdego okna. 

    Aby określić, że chcesz użyć okna wirowania, użyj funkcji [TUMBLINGWINDOW](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) w klauzuli `GROUP BY`. W funkcji należy określić jednostkę czasu (w dowolnym miejscu od mikrosekundowych do dnia) i rozmiar okna (liczbę jednostek). W tym przykładzie okno wirowania składa się z 5-sekundowych interwałów, dzięki czemu będzie można obliczyć liczbę według kraju/regionu dla każdego 5-sekundowego wywołania.

2. Kliknij ponownie przycisk **Testuj** . W wynikach należy zauważyć, że sygnatury czasowe w obszarze **WindowEnd** są w odstępach 5-sekundowych.

   ![Dane wyjściowe zadania Stream Analytics dla agregacji pokazujące 13 rekordów](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>Wykrywanie oszustwa karty SIM przy użyciu samosprzężenia

W tym przykładzie Rozważ użycie fałszywych wywołań, które pochodzą z tego samego użytkownika, ale w różnych lokalizacjach w ciągu 5 sekund od siebie. Na przykład ten sam użytkownik nie może rzeczywiście wykonywać w tym samym czasie połączeń ze Stanów Zjednoczonych i Australii. 

Aby sprawdzić te przypadki, można użyć samosprzężenia danych przesyłanych strumieniowo w celu dołączenia strumienia do samego siebie w oparciu o wartość `CallRecTime`. Następnie można wyszukać rekordy wywołań, w których wartość `CallingIMSI` (numer źródłowy) jest taka sama, ale wartość `SwitchNum` (kraj/region pochodzenia) nie jest taka sama.

W przypadku użycia sprzężenia z danymi przesyłania strumieniowego, sprzężenie musi zawierać pewne ograniczenia dotyczące czasu, w którym można oddzielić pasujące wiersze. (Jak wspomniano wcześniej, dane przesyłane strumieniowo są efektywnie nieograniczone). Granice czasu dla relacji są określone w klauzuli `ON` sprzężenia przy użyciu funkcji `DATEDIFF`. W takim przypadku sprzężenie jest oparte na 5-sekundowym interwale danych wywołań.

1. Zmień zapytanie w edytorze kodu na następujące: 

        ```SQL
        SELECT  System.Timestamp as Time, 
            CS1.CallingIMSI, 
            CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, 
            CS1.SwitchNum as Switch1, 
            CS2.SwitchNum as Switch2 
        FROM CallStream CS1 TIMESTAMP BY CallRecTime 
            JOIN CallStream CS2 TIMESTAMP BY CallRecTime 
            ON CS1.CallingIMSI = CS2.CallingIMSI 
            AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5 
        WHERE CS1.SwitchNum != CS2.SwitchNum
        ```

    To zapytanie jest podobne do żadnego sprzężenia SQL, z wyjątkiem funkcji `DATEDIFF` w sprzężeniu. Ta wersja `DATEDIFF` jest specyficzna dla analizy strumieniowej i musi znajdować się w klauzuli `ON...BETWEEN`. Parametry są jednostką czasu (w sekundach) i aliasy dwóch źródeł dla sprzężenia. Różni się to od standardowej funkcji `DATEDIFF` SQL.

    Klauzula `WHERE` obejmuje warunek, który flaguje oszukańcze wywołanie: Przełączniki źródłowe nie są takie same. 

2. Kliknij ponownie przycisk **Testuj** . 

   ![Stream Analytics dane wyjściowe zadania do samosprzężenia, pokazując 6 rekordów wygenerowanych](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. Kliknij przycisk **Zapisz** , aby zapisać kwerendę do samosprzężenia w ramach zadania usługi Stream Analytics. (Dane przykładowe nie są zapisywane).

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png" alt="Save Stream Analytics query in portal" width="300px"/>

## <a name="create-an-output-sink-to-store-transformed-data"></a>Utwórz ujścia danych wyjściowych, aby przechowywać przekształcone dane

Zdefiniowano strumień zdarzeń, dane wejściowe centrum zdarzeń do zdarzeń pozyskiwania oraz zapytanie, aby wykonać transformację strumienia. Ostatnim krokiem jest zdefiniowanie ujścia danych wyjściowych dla zadania — to znaczy miejsca, w którym ma zostać zapisany przekształcony strumień. 

Możesz użyć wielu zasobów jako ujścia danych wyjściowych — bazy danych SQL Server, magazynu tabel, Data Lake magazynu, Power BI, a nawet innego centrum zdarzeń. W tym samouczku utworzysz strumień do Blob Storage platformy Azure, który jest typowym wyborem do zbierania informacji o zdarzeniach na potrzeby późniejszej analizy, ponieważ uwzględnia on dane bez struktury.

Jeśli masz istniejące konto magazynu obiektów blob, możesz go użyć. W tym samouczku dowiesz się, jak utworzyć nowe konto magazynu.

### <a name="create-an-azure-blob-storage-account"></a>Utwórz konto usługi Azure Blob Storage

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób** > **Magazyn** > **Konto magazynu**. Wypełnij stronę zadania magazynu konto z **nazwą** ustawioną na "asaehstorage", w **lokalizacji** ustawionej na "Wschodnie stany USA", **grupy zasobów** ustawionej na "ASA-EH-NS-RG" (hostowanie konta magazynu w tej samej grupie zasobów co zadanie przesyłania strumieniowego w celu zwiększenia wydajności). W przypadku pozostałych ustawień można pozostawić ich wartości domyślne.  

   ![Utwórz konto magazynu w Azure Portal](./media/stream-analytics-real-time-fraud-detection/stream-analytics-storage-account-create.png)

2. W Azure Portal Wróć do okienka zadania usługi Stream Analytics. (Jeśli okienko zostało zamknięte, Wyszukaj `asa_frauddetection_job_demo` w okienku **wszystkie zasoby** ).

3. W sekcji **topologia zadania** kliknij pole **dane wyjściowe** .

4. W okienku dane **wyjściowe** kliknij pozycję **Dodaj** i wybierz pozycję **Magazyn obiektów BLOB**. Następnie wypełnij nową stronę wyjściową, podając następujące informacje:

   |**Ustawienie**  |**Sugerowana wartość**  |**Opis**  |
   |---------|---------|---------|
   |Alias danych wyjściowych  |  CallStream-FraudulentCalls   |  Wprowadź nazwę identyfikującą dane wyjściowe zadania.   |
   |Subskrypcja   |  \<Twoja subskrypcja\> |  Wybierz subskrypcję platformy Azure, która ma utworzone konto magazynu. Konto magazynu może być w tej samej lub innej subskrypcji. W tym przykładzie przyjęto założenie, że konto magazynu zostało utworzone w tej samej subskrypcji. |
   |Konto magazynu  |  asaehstorage |  Wprowadź nazwę utworzonego konta magazynu. |
   |Kontener  | asa-fraudulentcalls-demo | Wybierz pozycję Utwórz nową i wprowadź nazwę kontenera. |

    <br/>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png" alt="Create blob output for Stream Analytics job" width="300px"/>
    
5. Kliknij przycisk **Save** (Zapisz). 


## <a name="start-the-streaming-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

Zadanie jest teraz skonfigurowane. Określono dane wejściowe (centrum zdarzeń), przekształcenie (zapytanie do wyszukiwania fałszywych wywołań) i wyjście (BLOB Storage). Teraz możesz uruchomić zadanie. 

1. Upewnij się, że aplikacja TelcoGenerator jest uruchomiona.

2. W okienku Zadania kliknij przycisk **Uruchom**. W okienku **Uruchamianie zadania** , w polu czas rozpoczęcia danych wyjściowych zadania, wybierz pozycję **teraz**. 

   ![Uruchamianie zadania Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start.png)



## <a name="examine-the-transformed-data"></a>Sprawdzanie przekształconych danych

Masz teraz kompletne zadanie usługi Stream Analytics. Zadanie bada strumień metadanych połączeń telefonicznych, szukając fałszywych połączeń telefonicznych w czasie rzeczywistym i pisząc informacje o tych fałszywych wywołaniach do magazynu. 

Aby ukończyć ten samouczek, warto sprawdzić dane przechwycone przez zadanie usługi Stream Analytics. Dane są zapisywane w magazynie w blogu platformy Azure w fragmentach (plikach). Możesz użyć dowolnego narzędzia, które odczytuje Blob Storage platformy Azure. Jak wspomniano w sekcji wymagania wstępne, można użyć rozszerzeń platformy Azure w programie Visual Studio lub użyć narzędzia, takiego jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/) lub [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

Gdy sprawdzisz zawartość pliku w usłudze BLOB Storage, zobaczysz coś podobne do następujących:

   ![Magazyn obiektów blob platformy Azure z danymi wyjściowymi usługi Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Istnieją dodatkowe artykuły, które kontynuują pracę z scenariuszem wykrywania oszustw i używają zasobów utworzonych w tym samouczku. Jeśli chcesz kontynuować, zapoznaj się z sugestiami w sekcji **następne kroki**.

Jeśli jednak skończysz i nie potrzebujesz zasobów, które zostały utworzone, możesz je usunąć, aby nie ponosić niepotrzebnych opłat na korzystanie z platformy Azure. W takim przypadku zaleca się wykonanie następujących czynności:

1. Zatrzymaj zadanie usługi Stream Analytics. W okienku **zadania** kliknij pozycję **Zatrzymaj** u góry.
2. Zatrzymaj aplikację generatora odpływ. W oknie polecenia, w którym uruchomiono aplikację, naciśnij klawisze CTRL + C.
3. Jeśli utworzono nowe konto usługi BLOB Storage tylko dla tego samouczka, usuń je. 
4. Usuń zadanie usługi Stream Analytics.
5. Usuń centrum zdarzeń.
6. Usuń przestrzeń nazw centrum zdarzeń.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Aby uzyskać dalszą pomoc, wypróbuj [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Następne kroki

Ten samouczek można kontynuować z następującym artykułem:

* [Stream Analytics i Power BI: pulpit nawigacyjny analizy w czasie rzeczywistym dla danych przesyłanych strumieniowo](stream-analytics-power-bi-dashboard.md). W tym artykule opisano sposób wysyłania odpływ danych wyjściowych zadania Stream Analytics do Power BI do wizualizacji i analizy w czasie rzeczywistym.

Aby uzyskać więcej informacji na temat ogólnie Stream Analytics, zobacz następujące artykuły:

* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
