---
title: Monitoruj usługi Azure Storage za pomocą Azure Monitor dla magazynu (wersja zapoznawcza) | Microsoft Docs
description: W tym artykule opisano Azure Monitor funkcji Storage, która zapewnia administratorom magazynu szybkie zrozumienie problemów z wydajnością i wykorzystaniem ich kont usługi Azure Storage.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/15/2019
ms.openlocfilehash: 790d6ad9fb26253697ea36b402799d3eb488ca47
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75401133"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage-preview"></a>Monitorowanie usługi magazynu za pomocą Azure Monitor dla magazynu (wersja zapoznawcza)

Usługa Azure Monitor for Storage (wersja zapoznawcza) umożliwia kompleksowe monitorowanie kont usługi Azure Storage, zapewniając ujednolicony wgląd w wydajność, wydajność i dostępność usług Azure Storage. Można obserwować pojemność magazynu i wydajność na dwa sposoby, przeglądać bezpośrednio z poziomu konta magazynu lub widoku Azure Monitor, aby zobaczyć w różnych grupach kont magazynu. 

Ten artykuł ułatwi zapoznanie się z Azure Monitor środowiska w ramach usługi Storage (wersja zapoznawcza) w celu uzyskania informacji na temat kondycji i wydajności kont magazynu na dużą skalę, z możliwością skoncentrowania się na hotspotach i zdiagnozowaniu opóźnień, ograniczania przepustowości, i problemy z dostępnością.

## <a name="introduction-to-azure-monitor-for-storage-preview"></a>Wprowadzenie do Azure Monitor usługi Storage (wersja zapoznawcza)

Przed wprowadzeniem do środowiska należy zrozumieć, jak prezentuje i wizualizuje informacje. Bez względu na to, czy funkcja magazynu jest wybierana bezpośrednio z konta magazynu, czy z Azure Monitor, Azure Monitor dla magazynu będzie mieć spójne środowisko pracy. 

Połączona z nim oferuje:

* **W perspektywie skalowania** przedstawia widok migawki ich dostępności na podstawie kondycji usługi magazynu lub operacji interfejsu API, wykorzystania pokazującego łączną liczbę żądań odbieranych przez usługę magazynu oraz opóźnienie pokazujące średni czas, w którym usługa magazynu lub typ operacji interfejsu API przetwarza żądania. Możesz również wyświetlić wydajność według obiektów blob, plików, tabel i kolejek.

* **Przechodzenie do szczegółów** konkretnego konta magazynu w celu ułatwienia zdiagnozowania problemów lub wykonywania szczegółowej analizy według kategorii dostępności, wydajności, błędów i pojemności. Wybranie jednej z tych opcji zapewnia szczegółowy wgląd w metryki.  

* **Dostosowywalne** miejsce, w którym można zmienić metryki, które mają być wyświetlane, zmodyfikować lub ustawić progi, które są wyrównane wraz z limitami, i zapisać jako własny skoroszyt. Wykresy w skoroszycie można przypinać do pulpitu nawigacyjnego platformy Azure.  

Ta funkcja nie wymaga włączenia ani skonfigurowania żadnych informacji. metryki magazynu z kont magazynu są domyślnie zbierane. Jeśli nie znasz metryk dostępnych w usłudze Azure Storage, zobacz opis i definicję w obszarze metryki usługi Azure Storage, przeglądając [metryki usługi Azure Storage](../../storage/common/storage-metrics-in-azure-monitor.md).

>[!NOTE]
>Dostęp do tej funkcji nie jest naliczany, a opłaty są naliczane tylko za Azure Monitor podstawowe funkcje, które konfigurujesz lub włączasz, zgodnie z opisem na stronie [szczegóły cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) .

>[!NOTE]
>Azure Monitor dla magazynu nie obsługuje [kont ogólnego przeznaczenia w wersji 1](../../storage/common/storage-account-overview.md#general-purpose-v1-accounts).
>

## <a name="view-from-azure-monitor"></a>Wyświetl od Azure Monitor

Z poziomu Azure Monitor można wyświetlać szczegóły transakcji, opóźnień i pojemności z wielu kont magazynu w ramach subskrypcji oraz identyfikować wydajność, problemy z pojemnością i błędy.

Aby wyświetlić wykorzystanie i dostępność kont magazynu we wszystkich subskrypcjach, wykonaj następujące czynności.

1. Zaloguj się do [portalu Azure](https://portal.azure.com).

2. Wybierz pozycję **monitor** w okienku po lewej stronie w Azure Portal i w sekcji **szczegółowe informacje** wybierz pozycję **konta magazynu (wersja zapoznawcza)** .

    ![Widok wielu kont magazynu](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>Skoroszyt omówienia

W skoroszycie **Przegląd** dla wybranej subskrypcji w tabeli są wyświetlane metryki magazynu interaktywnego i stan dostępności usługi dla maksymalnie 10 kont magazynu pogrupowanych w ramach subskrypcji. Wyniki można filtrować na podstawie opcji wybranych z następujących list rozwijanych:

* Są wyświetlane subskrypcje obsługujące tylko **subskrypcje** z kontami magazynu.  

* **Konta magazynu** — domyślnie wybrane są 10 kont magazynu. W przypadku wybrania w selektorze zakresu wszystkich lub wielu kont magazynu zostaną zwrócone maksymalnie 200 kont magazynu. Jeśli na przykład masz łącznie 573 kont magazynu w ramach trzech wybranych subskrypcji, zostaną wyświetlone tylko konta 200. 

* **Zakres czasu** — domyślnie wyświetla ostatnie 4 godziny informacji na podstawie odpowiednich opcji.

Kafelek licznika pod listą rozwijaną podsumowuje łączną liczbę kont magazynu w subskrypcji i odzwierciedla liczbę wybranych opcji. Istnieje warunkowe kodowanie kolorami lub map cieplnych dla kolumn w skoroszycie, które raportują metryki lub błędy transakcji. Optymalny kolor ma najwyższą wartość, a jaśniejszy kolor jest oparty na najniższych wartościach. W przypadku kolumn opartych na błędach wartość jest czerwona i dla kolumn opartych na metrykach wartość jest niebieska.

Wybierz wartość w polu **dostępność**kolumn, **opóźnienie E2E**, **opóźnienie serwera**i **Typ błędu transakcji/błędy** kierujesz raport dostosowany do określonego typu metryk magazynu, które pasują do kolumny wybranej dla tego konta magazynu. Aby uzyskać więcej informacji na temat skoroszytów dla każdej kategorii, zobacz sekcję [szczegółowy skoroszyty magazynu](#detailed-storage-workbooks) poniżej. 

>[!NOTE]
>Aby uzyskać szczegółowe informacje o błędach, które mogą być wyświetlane w raporcie, zobacz [schemat typu odpowiedzi](../../storage/common/storage-metrics-in-azure-monitor.md#metrics-dimensions) i Wyszukaj typy odpowiedzi, takie jak **ServerOtherError**, **ClientOtherError**, **ClientThrottlingError**. W zależności od wybranych kont magazynu, jeśli zgłoszono więcej niż trzy typy błędów, wszystkie inne błędy są reprezentowane w kategorii **innych**.

Domyślny próg **dostępności** :

* Ostrzeżenie — 99%
* Krytyczny — 90%

Aby ustawić próg dostępności na podstawie wyników obserwacji lub wymagań, przejrzyj temat [Modyfikowanie progu dostępności](#modify-the-availability-threshold). 

### <a name="capacity-workbook"></a>Skoroszyt pojemności

Wybierz pozycję **pojemność** w górnej części strony, a zostanie otwarty skoroszyt **pojemności** . Przedstawia ona łączną ilość miejsca do magazynowania używanego w ramach konta i pojemności używanej przez każdą usługę danych na koncie, aby ułatwić identyfikację i użycie magazynu.

![Skoroszyt pojemności wielu kont magazynu](./media/storage-insights-overview/storage-account-capacity-02.png) 

Istnieje warunkowe kodowanie kolorami lub map cieplnych dla kolumn w skoroszycie, które raportują metryki wydajności z niebieską wartością. Optymalny kolor ma najwyższą wartość, a jaśniejszy kolor jest oparty na najniższych wartościach.

Po wybraniu wartości w jednej z kolumn w skoroszycie przejdź do arkusza **pojemności** dla konta magazynu. Więcej szczegółowych informacji o raporcie przechodzenia do szczegółów znajduje się w sekcji [szczegółowe skoroszyty magazynu](#detailed-storage-workbooks) poniżej. 

## <a name="view-from-a-storage-account"></a>Wyświetlanie z konta magazynu

Aby uzyskać dostęp do Azure Monitor dla maszyn wirtualnych bezpośrednio z konta magazynu:

1. W Azure Portal wybierz pozycję konta magazynu.

2. Z listy wybierz konto magazynu. W sekcji monitorowanie wybierz pozycję szczegółowe dane (wersja zapoznawcza).

    ![Strona przeglądu wybranego konta magazynu](./media/storage-insights-overview/storage-account-direct-overview-01.png)

W skoroszycie **Przegląd** dla konta magazynu przedstawiono kilka metryk wydajności magazynu, które pomagają szybko ocenić:

* Kondycja usługi magazynu do natychmiastowego sprawdzenia, czy problem poza formantem ma wpływ na usługę magazynu w regionie, w którym jest wdrożona, która jest określona w kolumnie **Podsumowanie** .

* Interaktywne wykresy wydajności pokazujące najważniejsze szczegóły związane z pojemnością, dostępnością, transakcjami i opóźnieniem magazynu.  

* Kafelki metryk i Stanów wyróżniają dostępność usługi, łączną liczbę transakcji do usługi Storage, opóźnienie E2E i opóźnienie serwera.

Wybranie jednego z przycisków dla **niepowodzeń**, **wydajności**, **dostępności**i **pojemności** spowoduje otwarcie odpowiedniego skoroszytu. 

![Strona przeglądu wybranego konta magazynu](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>Szczegółowe skoroszyty magazynu

Niezależnie od tego, czy wybrano wartość w skoroszycie " **dostępność**kolumn, **opóźnienie E2E**, **opóźnienie serwera**i **Typ błędu transakcji/błędy** " z arkusza z **wieloma kontami** magazynu, lub wybierając jeden z przycisków dla **niepowodzeń**, **wydajności**, **dostępności**i **pojemności** ze skoroszytu **przeglądu** na określonym koncie magazynu, każdy dostarcza zestaw interaktywnych informacji związanych z magazynem, które są dostosowane do tej kategorii.  

* **Dostępność** powoduje otwarcie skoroszytu **dostępności** . Przedstawia bieżący stan kondycji usługi Azure Storage, tabelę przedstawiającą dostępny stan kondycji każdego obiektu skategoryzowanego według usługi danych zdefiniowanego w ramach konta magazynu z linią trendu reprezentującą wybrany zakres czasu oraz wykres trendu dostępności dla Każda usługa danych na koncie.  

    ![Przykład raportu dostępności](./media/storage-insights-overview/storage-account-availability-01.png)

* **Opóźnienie E2E** i **opóźnienie serwera** otwierają skoroszyt **wydajności** . Zawiera kafelek stan zestawienia przedstawiający opóźnienie E2E i opóźnienie serwera, wykres wydajności E2E, a także opóźnienia serwera, a także zmniejszenie liczby pomyślnych wywołań przez interfejs API pogrupowane według usługi danych zdefiniowanej na koncie magazynu.

    ![Przykład raportu wydajności](./media/storage-insights-overview/storage-account-performance-01.png)

* Wybranie dowolnej kategorii błędów wymienionej w siatce Otwórz skoroszyt **niepowodzeń** . Raport przedstawia kafelki metryk wszystkich innych błędów po stronie klienta, z wyjątkiem opisanych i zakończonych powodzeniem żądań, błędów ograniczania liczby klientów, wykresu wydajności dla **typu odpowiedzi** transakcji dla metryki wymiaru dotyczącego atrybutu ClientOtherError oraz dwóch tabel — **transakcji według nazwy interfejsu API** i **transakcji według typu odpowiedzi**.

   ![Przykładowy raport o błędach](./media/storage-insights-overview/storage-account-failures-01.png)

* **Pojemność** otwiera skoroszyt **pojemności** . Pokazuje łączną ilość miejsca do magazynowania używanego dla każdego obiektu danych magazynu na koncie na kafelkach i wykresie oraz liczbę obiektów danych przechowywanych na koncie.  

    ![Wybrana strona pojemności konta magazynu](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>Przypnij i Eksportuj

Możesz przypiąć każdą z sekcji metryk do pulpitu nawigacyjnego platformy Azure, wybierając ikonę pinezki w prawym górnym rogu sekcji.

![Przykład numeru PIN sekcji Metryka na pulpit nawigacyjny](./media/storage-insights-overview/workbook-pin-example.png)

**Omówienie** konta wielosubskrypcji i magazynu oraz skoroszyty **pojemności** obsługują Eksportowanie wyników w formacie programu Excel, wybierając ikonę strzałki w dół z prawej strony ikony pinezki.

![Przykład eksportu wyników siatki skoroszytu](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage-preview"></a>Dostosowywanie Azure Monitor dla magazynu (wersja zapoznawcza)

W tej sekcji przedstawiono typowe scenariusze edytowania skoroszytu w celu dostosowania ich do potrzeb związanych z analizą danych:

* Ustaw zakres skoroszytu w taki sposób, aby zawsze wybierał określoną subskrypcję lub konta magazynu
* Zmień metryki w siatce
* Zmiana progu dostępności
* Zmiana renderowania koloru

Dostosowania są zapisywane w niestandardowym skoroszycie, aby zapobiec zastąpieniu konfiguracji domyślnej w naszym opublikowanym skoroszycie. Skoroszyty są zapisywane w obrębie grupy zasobów, w sekcji **Moje raporty** prywatnej dla użytkownika lub w sekcji **raporty udostępnione** dostępne dla wszystkich użytkowników mających dostęp do grupy zasobów. Po zapisaniu skoroszytu niestandardowego musisz przejść do galerii skoroszytów, aby go uruchomić.

![Uruchom galerię skoroszytów z poziomu paska poleceń](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>Określanie subskrypcji lub konta magazynu

Można skonfigurować wiele subskrypcji i **przeglądanie** konta magazynu oraz skoroszyty **pojemności** do określonych subskrypcji lub kont magazynu na każdym uruchomieniu, wykonując następujące kroki.

1. Wybierz pozycję **monitor** z portalu, a następnie wybierz pozycję **konta magazynu (wersja zapoznawcza)** w okienku po lewej stronie.

2. W skoroszycie **Przegląd** , na pasku poleceń wybierz pozycję **Edytuj**.

3. Z listy rozwijanej **subskrypcje** wybierz co najmniej jedną subskrypcję, do której ma zostać ona domyślnie wybrana. Pamiętaj, że skoroszyt obsługuje wybieranie maksymalnie 10 subskrypcji.  

4. Wybierz z listy rozwijanej **konta magazynu** co najmniej jedno konto, na które ma być domyślnie. Pamiętaj, że skoroszyt obsługuje wybieranie maksymalnie 200 kont magazynu. 

5. Wybierz pozycję **Zapisz jako** na pasku poleceń, aby zapisać kopię skoroszytu z własnymi dostosowaniami, a następnie kliknij pozycję **Zakończono edycję** , aby powrócić do trybu odczytu.  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>Modyfikuj metryki i kolory w skoroszycie

Wstępnie skompilowane skoroszyty zawierają dane metryk i można modyfikować lub usuwać dowolne wizualizacje i dostosowywać je do konkretnych potrzeb zespołu.

W naszym przykładzie pracujemy z skoroszytem pojemności dla wielosubskrypcji i konta magazynu, aby zademonstrować, jak:

* Usuń metrykę
* Zmiana renderowania koloru

Te same zmiany można wykonać w odniesieniu do jednego ze wstępnie skompilowanych skoroszytów **błędów**, **wydajności**, **dostępności**i **pojemności** .

1. Wybierz pozycję **monitor** z portalu, a następnie wybierz pozycję **konta magazynu (wersja zapoznawcza)** w okienku po lewej stronie.

2. Wybierz pozycję **pojemność** , aby przełączyć się na skoroszyt pojemności, a następnie na pasku poleceń wybierz pozycję **Edytuj** na pasku poleceń.

    ![Wybierz pozycję Edytuj, aby zmodyfikować skoroszyt](./media/storage-insights-overview/workbook-edit-workbook.png)

3. Obok sekcji metryki wybierz pozycję **Edytuj**.

    ![Wybierz pozycję Edytuj, aby zmodyfikować metryki skoroszytu dyspozycyjności](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. Będziemy usuwać kolumnę **oś czasu o używanej pojemności** , więc wybierz pozycję **Ustawienia kolumny** w siatce metryk.

    ![Edytowanie ustawień kolumny](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. W okienku **Edytowanie ustawień kolumny** wybierz pozycję w sekcji **kolumny** **Microsoft. Storage/Storageaccounts-pojemność-UsedCapacity oś czasu $ | Oś czasu o używanej pojemności $** , a w obszarze **renderowania kolumna** listy rozwijanej wybierz pozycję **ukryty**.

6. Wybierz pozycję **Zapisz i Zamknij,** aby zatwierdzić zmianę.

Teraz Zmień motyw kolorów dla metryk pojemności w raporcie, tak aby używał zielonki zamiast niebieską.

1. Wybierz pozycję **Ustawienia kolumny** w siatce metryk.

2. W okienku **Edytowanie ustawień kolumny** wybierz pozycję w sekcji **kolumny** **Microsoft. Storage/storageaccounts-pojemność-UsedCapacity $ | Microsoft. Storage/storageaccounts/blobservices-pojemność-BlobCapacity $ | Microsoft. Storage**/storageaccounts/FileServices-pojemność-FileCapacity $ | Microsoft. Storage/storageaccounts/queueservices-pojemność-QueueCapacity $ | Microsoft. Storage/Storageaccounts/tableservices-pojemność-TableCapacity $. Na **palecie kolorów**listy rozwijanej wybierz pozycję **zielony**.

3. Wybierz pozycję **Zapisz i Zamknij,** aby zatwierdzić zmianę.

4. Wybierz pozycję **Zapisz jako** na pasku poleceń, aby zapisać kopię skoroszytu z własnymi dostosowaniami, a następnie kliknij pozycję **Zakończono edycję** , aby powrócić do trybu odczytu.  

### <a name="modify-the-availability-threshold"></a>Modyfikowanie progu dostępności

W tym przykładzie pracujemy ze skoroszytem pojemności konta magazynu i ukazującym sposób modyfikacji progu dostępności. Domyślnie wartość procentowa dostępności raportu kafelków i siatki jest konfigurowana z minimalnym progiem 90 i maksymalnym progiem 99. Będziemy zmieniać minimalną wartość progową **dostępności%** w siatce **nazw interfejsów API** na 85%, co oznacza, że stan kondycji zmieni się na krytyczny, jeśli wartość progowa jest mniejsza niż 85 procent. 

1. Wybierz pozycję **konta magazynu** z portalu, a następnie wybierz konto magazynu z listy.

2. Wybierz pozycję **Insights (wersja zapoznawcza)** w okienku po lewej stronie.

3. W skoroszycie wybierz opcję **dostępność** , aby przełączyć się do skoroszytu dostępności, a następnie wybierz pozycję **Edytuj** na pasku poleceń. 

4. Przewiń w dół do dołu strony i po lewej stronie obok pozycji **dostępność według siatki interfejsu API** wybierz pozycję **Edytuj**.

    ![Edytuj dostępność według ustawień siatki nazw interfejsów API](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. Wybierz pozycję **Ustawienia kolumny** , a następnie w okienku **Edytowanie ustawień kolumny** w obszarze **kolumny** wybierz **dostępność (%) (Progi + sformatowane)** .

6. Zmień wartość **krytycznego** stanu kondycji z **90** na **85** , a następnie kliknij przycisk **Zapisz i Zamknij**.

    ![Modyfikowanie wartości progowej dostępności dla stanu krytycznego](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. Wybierz pozycję **Zapisz jako** na pasku poleceń, aby zapisać kopię skoroszytu z własnymi dostosowaniami, a następnie kliknij pozycję **Zakończono edycję** , aby powrócić do trybu odczytu.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Ta sekcja pomoże Ci w diagnozowaniu i rozwiązywaniu problemów z typowymi problemami, które mogą wystąpić podczas korzystania z usługi Azure Monitor for Storage (wersja zapoznawcza). Skorzystaj z poniższej listy, aby znaleźć informacje istotne dla danego problemu.

### <a name="resolving-performance-capacity-or-availability-issues"></a>Rozwiązywanie problemów z wydajnością, pojemnością lub dostępnością

Aby ułatwić rozwiązywanie problemów związanych z magazynem, które są identyfikowane za pomocą Azure Monitor for Storage (wersja zapoznawcza), zobacz [wskazówki dotyczące rozwiązywania problemów](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)z usługą Azure Storage  

### <a name="why-can-i-only-see-200-storage-accounts"></a>Dlaczego można zobaczyć tylko konta magazynu 200?

Liczba wybranych kont magazynu ma limit 200, niezależnie od liczby wybranych subskrypcji.

### <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>Co się stanie po kliknięciu ostatnio przypiętego kafelka na pulpicie nawigacyjnym?

* Jeśli klikniesz w dowolnym miejscu kafelka, przejdziesz do karty, z której został przypięty kafelek. Na przykład w przypadku przypinania grafu na karcie "przegląd konta magazynu" po kliknięciu tego kafelka na pulpicie nawigacyjnym zostanie otwarty ten widok domyślny, jednak w przypadku przypinania grafu ze swojej zapisanej kopii zostanie otwarty widok zapisanej kopii.
* Ikona filtru w lewym górnym rogu tytułu otwiera kartę "Konfigurowanie ustawień kafelków".
* Ikona wielokropka w prawym górnym rogu będzie zawierać opcje "Dostosowywanie danych tytułu", "Dostosowywanie", "Odśwież" i "Usuwanie z pulpitu nawigacyjnego".

### <a name="what-happens-when-i-save-a-workbook"></a>Co się stanie, gdy zapisuję skoroszyt?

* Po zapisaniu skoroszytu można utworzyć nową kopię skoroszytu z wprowadzonymi zmianami i zmienić tytuł. Zapisywanie nie zastępuje skoroszytu, bieżący skoroszyt będzie zawsze widokiem domyślnym.
* **Niezapisany** skoroszyt jest tylko widokiem domyślnym.


### <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>Dlaczego nie widzę wszystkich moich subskrypcji w portalu?

W portalu zostaną wyświetlone tylko dane dla wybranych subskrypcji podczas uruchamiania portalu. Aby zmienić wybrane subskrypcje, przejdź do prawego górnego rogu i kliknij Notes z ikoną filtru. Spowoduje to wyświetlenie karty katalog i subskrypcje.

![Katalog i subskrypcja](./media/storage-insights-overview/fqa3.png)

### <a name="how-to-change-the-coloring-and-threshold-for-availability"></a>Jak zmienić kolorowanie i próg dostępności?

Szczegółowe instrukcje dotyczące sposobu zmiany kolorowania i progów dostępności można znaleźć w sekcji [Modyfikowanie progu dostępności](storage-insights-overview.md#modify-the-availability-threshold) .

### <a name="how-to-analyze-and-troubleshoot-the-data-shown-in-azure-monitor-for-storage"></a>Jak analizować i rozwiązywać problemy z danymi wyświetlanymi w Azure Monitor dla magazynu?

 Zapoznaj się z artykułem [monitorowanie, diagnozowanie i rozwiązywanie Microsoft Azure Storage problemów](https://docs.microsoft.com/azure/storage/common/storage-monitoring-diagnosing-troubleshooting) , aby uzyskać szczegółowe informacje na temat sposobu analizowania i rozwiązywania problemów z danymi usługi Azure Storage przedstawionymi w temacie Azure monitor for Storage.

### <a name="why-dont-i-see-all-the-types-of-errors-in-metrics"></a>Dlaczego nie widzę wszystkich typów błędów w metrykach?

Obecnie pokazywane są maksymalnie trzy różne typy błędów, a pozostałe błędy są pogrupowane w pojedynczym przedziale. Jest on kontrolowany przy użyciu splitByLimit i może być modyfikowany. Aby zmienić tę właściwość:

1. Kliknij pozycję Edytuj skoroszyt.
2. Przejdź do pozycji metryki, kliknij pozycję Edytuj, a następnie wybierz pozycję **transakcje, suma** lub wszystkie metryki, które chcesz edytować.

    ![Przejdź do metryk i kliknij pozycję Edytuj w pozycji "transakcje, sumy"](./media/storage-insights-overview/fqa7.png)

1. Następnie zmień liczbę podziałów.

    ![Wybierz parametry metryki "](./media/storage-insights-overview/fqa7-2.png)

Jeśli chcesz zobaczyć n różne typy błędów niż określa splitByLimit jako n + 1, 1 dodatkowe dla reszty błędów.

###  <a name="i-saved-my-workbook-while-on-some-storage-account-why-cant-i-find-it-now"></a>Mój skoroszyt został zapisany na pewnym koncie magazynu. Dlaczego nie mogę znaleźć tego teraz?

Każdy skoroszyt jest zapisywany na koncie magazynu, w którym został zapisany. Spróbuj znaleźć określone konto magazynu, w którym zapisano skoroszyt. W przeciwnym razie nie ma możliwości znalezienia określonego skoroszytu bez znajomości zasobu (konto magazynu).

### <a name="what-is-time-range"></a>Co to jest zakres czasu?

Zakres czasu pokazuje dane z określonego przedziału czasu. Na przykład jeśli zakres czasu wynosi 24 godziny, to pokazuje dane z ostatnich 24 godzin.

### <a name="what-is-time-granularity-time-grain"></a>Co to jest stopień szczegółowości czasu (ziarno czasu)?

Stopień szczegółowości czasu to różnica czasu między dwoma punktami danych. Na przykład, jeśli ziarno czasu jest ustawione na 1 sekundę, oznacza to, że metryki są zbierane w każdej sekundzie.

### <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>Jaki jest stopień szczegółowości czasu, gdy przypinam wszystkie części skoroszytów do pulpitu nawigacyjnego?

Domyślny stopień szczegółowości czasu jest ustawiony na automatyczny, obecnie nie można go zmienić.

### <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>Jak mogę zmienić przedział czasu/godziny dla kroku skoroszytu na moim pulpicie nawigacyjnym?

Domyślnie zakres TimeSpan/czasu na kafelku pulpitu nawigacyjnego jest ustawiony na 24 godziny, aby zmienić Kliknięcie wielokropka w prawym górnym rogu, wybierz pozycję **Dostosuj dane kafelka**, zaznacz pole wyboru Zastąp ustawienia czasowe pulpitu nawigacyjnego na poziomie tytułu, a następnie wybierz przedział czasu przy użyciu menu rozwijanego.  

![Wybierz wielokropek w prawym górnym rogu kafelka i wybierz pozycję Dostosuj te dane](./media/storage-insights-overview/fqa-data-settings.png)

![Na stronie Konfiguruj ustawienia kafelków wybierz listę rozwijaną TimeSpan, aby zmienić zakres czasu/godziny](./media/storage-insights-overview/fqa-timespan.png)

### <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>Jak mogę zmienić tytuł skoroszytu lub krok, który został przypięty do pulpitu nawigacyjnego?

Tytuł lub krok skoroszytu, który jest przypięty do pulpitu nawigacyjnego, zachowuje tę samą nazwę w skoroszycie. Aby zmienić tytuł, musisz zapisać własną kopię skoroszytu. Następnie będzie można nazwać skoroszyt przed naciśnięciem przycisku Zapisz.

![Wybierz pozycję Zapisz u góry, aby zapisać kopię skoroszytu i zmienić jej nazwę](./media/storage-insights-overview/fqa-change-workbook-name.png)

Aby zmienić nazwę kroku w zapisanym skoroszycie, wybierz opcję Edytuj w obszarze tego kroku, a następnie wybierz koła zębatego u dołu ustawień.

![wybierz pozycję Edytuj u dołu kroku skoroszytu, aby otworzyć ustawienia](./media/storage-insights-overview/fqa-edit.png)
![w obszarze Ustawienia wybierz koła zębatego u dołu, aby zmienić nazwę kroku](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>Następne kroki

* Konfigurowanie [alertów metryk](../platform/alerts-metric.md) i [powiadomień o kondycji usług](../../service-health/alerts-activity-log-service-notifications.md) w celu skonfigurowania zautomatyzowanego alertu w celu uzyskania pomocy w wykrywaniu problemów.

* Dowiedz się, jakie scenariusze skoroszyty są przeznaczone do obsługi, jak tworzyć nowe i dostosowywać istniejące raporty, a inne dzięki przeglądowi [Tworzenie interaktywnych raportów przy użyciu skoroszytów Azure monitor](../app/usage-workbooks.md).

* Aby uzyskać szczegółowy przewodnik dotyczący używania analityka magazynu i innych narzędzi do identyfikowania, diagnozowania i rozwiązywania problemów związanych z usługą Azure Storage, zobacz [monitorowanie, diagnozowanie i rozwiązywanie problemów z Microsoft Azure Storage](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md).
