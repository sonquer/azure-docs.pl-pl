---
title: 'Partycja i przykład: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać partycji i przykładowego modułu w Azure Machine Learning, aby wykonać próbkowanie na zestawie danych lub utworzyć partycje z zestawu danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 772c16dc292d8bce4b927c9c2ce3ff6ee0ed399d
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152129"
---
# <a name="partition-and-sample-module"></a>Partycja i Przykładowa moduł

W tym artykule opisano moduł w programie Azure Machine Learning Designer (wersja zapoznawcza).

Ten moduł służy do wykonywania próbkowania w zestawie danych lub tworzenia partycji z zestawu danych.

Próbkowanie jest ważnym narzędziem w uczeniu maszynowym, ponieważ umożliwia zmniejszenie rozmiaru zestawu danych przy zachowaniu tego samego stosunku wartości. Ten moduł obsługuje kilka powiązanych zadań, które są ważne w uczeniu maszynowym: 

- Dzielenie danych na wiele podczęści o takim samym rozmiarze. 

    Możesz użyć partycji na potrzeby wzajemnego sprawdzania poprawności lub przypisać przypadki do grup losowych.

- Oddzielanie danych do grup, a następnie praca z danymi z określonej grupy. 

    Po losowo przypisaniu przypadków do różnych grup może zajść potrzeba zmodyfikowania funkcji, które są skojarzone tylko z jedną grupą.

- Sond. 

    Można wyodrębnić procent danych, zastosować Próbkowanie losowe lub wybrać kolumnę, która ma być używana do równoważenia zestawu danych i wykonać stratified próbkowania na jego wartości.

- Tworzenie mniejszego zestawu danych do testowania. 

    Jeśli masz dużo danych, możesz chcieć użyć tylko pierwszych *n* wierszy podczas konfigurowania potoku, a następnie przełączyć się do korzystania z pełnego zestawu danych podczas kompilowania modelu. Możesz również użyć próbkowania, aby utworzyć s mniejszego zestawu danych do użycia podczas tworzenia.

## <a name="configure-partition-and-sample"></a>Konfigurowanie partycji i przykładu

Ten moduł obsługuje wiele metod dzielenia danych na partycje lub do próbkowania. Najpierw wybierz metodę, a następnie ustaw dodatkowe opcje wymagane przez metodę.

- Head
- Próbkowanie
- Przypisz do zagięć
- Wybierz złożenie

### <a name="get-top-n-rows-from-a-dataset"></a>Pobierz N pierwszych wierszy z zestawu danych

Użyj tego trybu, aby uzyskać tylko pierwsze *n* wierszy. Ta opcja jest przydatna, jeśli chcesz przetestować potok w niewielkiej liczbie wierszy i nie potrzebujesz, aby dane były równoważone lub próbkowane w jakikolwiek sposób.

1. Dodaj **partycję i przykładowy** moduł do potoku w interfejsie i Połącz zestaw danych.  

2. **Tryb partycji lub próbki**: Ustaw tę opcję na wartość **główna**.

3. **Liczba wierszy do wybrania**: wpisz liczbę wierszy do zwrócenia.

    Określona liczba wierszy musi być nieujemną liczbą całkowitą. Jeśli liczba wybranych wierszy jest większa niż liczba wierszy w zestawie danych, zwracany jest cały zestaw danych.

4. Uruchamianie potoku.

Moduł wyprowadza pojedynczy zestaw danych zawierający tylko określoną liczbę wierszy. Wiersze są zawsze odczytywane z góry zestawu danych.

### <a name="create-a-sample-of-data"></a>Tworzenie przykładu danych

Ta opcja obsługuje proste Próbkowanie losowe lub losowe próbkowanie stratified. Jest to przydatne, jeśli chcesz utworzyć mniejszy reprezentatywny przykładowy zestaw danych do testowania.

1. Dodawanie **partycji i przykładowego** modułu do potoku oraz łączenie zestawu danych.

2. **Tryb partycji lub próbki**: Ustaw tę wartość na **próbkowanie**.

3. **Częstotliwość próbkowania**: wpisz wartość z zakresu od 0 do 1. Ta wartość określa wartość procentową wierszy ze źródłowego zestawu danych, która powinna być uwzględniona w wyjściowym zestawie danych.

    Na przykład, jeśli chcesz tylko połowę oryginalnego zestawu danych, wpisz `0.5`, aby wskazać, że częstotliwość próbkowania powinna wynosić 50%.

    Wiersze wejściowego zestawu danych są przesunięte i wybiórczo umieszczane w wyjściowym zestawie danych, zgodnie z określonym wskaźnikiem.

4. **Losowy inicjator do próbkowania**: opcjonalnie wpisz liczbę całkowitą, która ma być używana jako wartość inicjatora.

    Ta opcja jest ważna, jeśli wiersze mają być dzielone w taki sam sposób co czas. Wartość domyślna to 0, co oznacza, że początkowy inicjator jest generowany na podstawie zegara systemowego. Może to spowodować nieco inne wyniki przy każdym uruchomieniu potoku.

5. **Stratified podzielona na próbkowanie**: zaznacz tę opcję, jeśli ważne jest, aby wiersze w zestawie danych były dzielone równomiernie przez niektóre kolumny klucza przed próbkowanie.

    Dla **kolumny klucza stratyfikacji do próbkowania**wybierz jedną *kolumnę* , która ma być używana podczas dzielenia zestawu danych. Wiersze w zestawie danych są następnie podzielone w następujący sposób:

    1. Wszystkie wiersze wejściowe są pogrupowane (stratified) przez wartości w określonej kolumnie.

    2. Wiersze są losowo w poszczególnych grupach.

    3. Każda grupa jest wybiórczo dodawana do wyjściowego zestawu danych w celu spełnienia określonego stosunku.


6. Uruchamianie potoku.

    Po wybraniu tej opcji moduł wyprowadza pojedynczy zestaw danych, który zawiera reprezentatywne próbkowanie danych. Pozostała część zestawu danych nie jest wyjściowa. 

## <a name="split-data-into-partitions"></a>Dzielenie danych na partycje

Użyj tej opcji, jeśli chcesz podzielić zestaw danych na podzbiory danych. Ta opcja jest również przydatna, gdy chcesz utworzyć niestandardową liczbę zagięć do krzyżowego sprawdzania poprawności lub podzielić wiersze na kilka grup.

1. Dodawanie **partycji i przykładowego** modułu do potoku oraz łączenie zestawu danych.

2. W obszarze **partycja lub przykład**wybierz pozycję **Przypisz do zgięcia**.

3. **Użyj zamiany na partycjonowanie**: zaznacz tę opcję, jeśli chcesz, aby wiersz próbkowany został umieszczony z powrotem w puli wierszy do ponownego użycia. W związku z tym ten sam wiersz może być przypisany do kilku zagięć.

    Jeśli nie używasz zastąpienia (opcja domyślna), wiersz próbkowania nie zostanie ponownie umieszczony w puli wierszy do ponownego użycia. W związku z tym każdy wiersz może być przypisany tylko do jednego złożenia.

4. **Losowy podział**: zaznacz tę opcję, jeśli chcesz, aby wiersze były losowo przypisywane do składania.

    Jeśli nie wybierzesz tej opcji, wiersze są przypisywane do składania przy użyciu metody okrężnej.

5. **Losowy inicjator**: opcjonalnie wpisz liczbę całkowitą, która ma być używana jako wartość inicjatora. Ta opcja jest ważna, jeśli wiersze mają być dzielone w taki sam sposób co czas. W przeciwnym razie wartość domyślna 0 oznacza, że zostanie użyty losowy początkowy inicjator.

6. **Określ metodę partycjonowania**: wskaż, w jaki sposób dane mają zostać rozdzielone na każdą partycję przy użyciu następujących opcji:

    - **Podziel na partycje równomiernie**: Użyj tej opcji, aby umieścić równą liczbę wierszy w każdej partycji. Aby określić liczbę partycji wyjściowych, wpisz liczbę całkowitą w polu tekstowym **Określ liczbę zagięć, które mają być równo podzielone** .

    - **Podziel na partycje przy użyciu dostosowanych proporcji**: Użyj tej opcji, aby określić rozmiar każdej partycji jako listę rozdzieloną przecinkami.

        Jeśli na przykład chcesz utworzyć trzy partycje z pierwszą partycją zawierającą 50% danych, a pozostałe dwie partycje zawierające 25% danych, kliknij **listę proporcji rozdzielonych przecinkami** , a następnie wpisz następujące numery: `.5, .25, .25`

        Suma wszystkich rozmiarów partycji musi być dokładnie równa 1.

        - Jeśli wprowadzisz liczby, które dodają do **mniej niż 1**, zostanie utworzona dodatkowa partycja do przechowywania pozostałych wierszy. Na przykład, jeśli wpiszesz wartości .2 i .3, zostanie utworzona trzecia partycja, która przechowuje pozostałe 50 procent wszystkich wierszy.

        - Jeśli wprowadzisz liczby, które dodają do **więcej niż 1**, zostanie zgłoszony błąd podczas uruchamiania potoku.

7. **Stratified**: zaznacz tę opcję, jeśli chcesz, aby wiersze były Stratified podczas dzielenia, a następnie wybierz _kolumnę strata_.

8. Uruchamianie potoku.

    Po wybraniu tej opcji moduł wyprowadza wiele zestawów danych z podziałem na partycje przy użyciu określonych reguł.

### <a name="use-data-from-a-predefined-partition"></a>Korzystanie z danych ze wstępnie zdefiniowanej partycji  

Ta opcja jest używana, jeśli zestaw danych został podzielony na wiele partycji i teraz chcesz załadować każdą partycję w celu przeprowadzenia dalszej analizy lub przetwarzania.

1. Dodawanie **partycji i przykładowego** modułu do potoku.

2. Podłącz go do danych wyjściowych poprzedniego wystąpienia **partycji i przykładu**. To wystąpienie musi używać opcji **Assign to zgięcia** w celu wygenerowania pewnej liczby partycji.

3. **Tryb partycji lub próbki**: wybierz pozycję Utwórz **złożenie**.

4. **Określ, z których składanie ma być próbkowane**: wybierz partycję, która ma zostać użyta, wpisując jej indeks. Indeksy partycji są oparte na 1. Na przykład jeśli zestaw danych został podzielony na trzy części, partycje będą miały indeksy 1, 2 i 3.

    W przypadku wpisania nieprawidłowej wartości indeksu zostanie zgłoszony błąd czasu projektowania: "Error 0018: DataSet zawiera nieprawidłowe dane".

    Oprócz grupowania zestawu danych przez zgięcia można rozdzielić zestaw danych na dwie grupy: zgięcie docelowe i wszystko inne. Aby to zrobić, wpisz indeks pojedynczego zgięcia, a następnie wybierz opcję, wybierz **uzupełnienie wybranego zgięcia**, aby uzyskać wszystko, ale dane w określonej ścieżce.

5. Jeśli pracujesz z wieloma partycjami, musisz dodać dodatkowe wystąpienia **partycji i przykładowego** modułu, aby obsługiwać każdą partycję.

    Załóżmy na przykład, że wcześniej podzielone na partycje pacjente na cztery zgięcia przy użyciu wieku. Aby współpracować z każdym indywidualnym zgięciem, potrzebne są cztery kopie **partycji i przykładowego** modułu, a w każdym z nich wybiera się inne zgięcie, jak pokazano poniżej. Nie jest to poprawne użycie elementu **Assign do bezpośredniego składania** danych wyjściowych.  

    [![partycja i przykład](./media/partition-and-sample/partition-and-sample.png)](./media/partition-and-sample/partition-and-sample-lg.png#lightbox)

5. Uruchamianie potoku.

    Po wybraniu tej opcji moduł wyprowadza pojedynczy zestaw danych zawierający tylko wiersze przypisane do tego złożenia.

> [!NOTE]
>  Nie można bezpośrednio przeglądać oznaczeń składania; są one obecne tylko w metadanych.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 