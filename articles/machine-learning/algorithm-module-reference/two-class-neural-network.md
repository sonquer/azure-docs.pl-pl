---
title: 'Dwie klasy sieci neuronowych: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak za pomocą dwuklasowego modułu sieci neuronowych w Azure Machine Learning utworzyć model sieci neuronowych, który może służyć do przewidywania elementu docelowego, który ma tylko dwie wartości.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: c87673fef0079ff1b04b0840da0434b090cda67c
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153659"
---
# <a name="two-class-neural-network-module"></a>Dwuklasowy moduł sieci neuronowych

W tym artykule opisano moduł w programie Azure Machine Learning Designer (wersja zapoznawcza).

Ten moduł służy do tworzenia modelu sieci neuronowych, który może służyć do przewidywania elementu docelowego, który ma tylko dwie wartości.

Klasyfikacja przy użyciu sieci neuronowych to nadzorowana Metoda uczenia i dlatego wymaga *oznakowanego zestawu danych*, który zawiera kolumnę etykieta. Na przykład można użyć tego modelu sieci neuronowych do przewidywania danych binarnych, takich jak to, czy pacjent ma pewną chorobę, czy też może zakończyć się niepowodzeniem w określonym przedziale czasu.  

Po zdefiniowaniu modelu nauczenie go przez udostępnienie oznakowanego zestawu danych i modelu jako dane wejściowe do [uczenia modelu](./train-model.md). Model przeszkolony może być następnie używany do przewidywania wartości dla nowych danych wejściowych.

### <a name="more-about-neural-networks"></a>Więcej informacji o sieciach neuronowych

Sieć neuronowych to zestaw wzajemnie połączonych warstw. Dane wejściowe są pierwszą warstwą i są połączone z warstwą wyjściową przez wykres acykliczne, składający się z ważonych krawędzi i węzłów.

Między warstwami wejściowymi i wyjściowymi można wstawiać wiele ukrytych warstw. Większość zadań predykcyjnych można łatwo wykonać przy użyciu tylko jednej lub kilku ukrytych warstw. Jednak ostatnie badania wykazały, że głębokie neuronowych Networks (DNN) z wieloma warstwami mogą być skuteczne w złożonych zadaniach, takich jak rozpoznawanie obrazów lub mowy. Kolejne warstwy są używane do modelowania rosnącego poziomu głębokości semantycznej.

Relacja między danymi wejściowymi i wyjściowymi jest uzyskiwana z uczenia sieci neuronowych w danych wejściowych. Kierunek wykresu przechodzi z danych wejściowych za pośrednictwem warstwy Ukryta i do warstwy wyjściowej. Wszystkie węzły w warstwie są połączone przy użyciu ważonych krawędzi do węzłów w następnej warstwie.

Aby obliczyć dane wyjściowe sieci dla konkretnych danych wejściowych, wartość jest obliczana w każdym węźle ukrytych warstw i w warstwie wyjściowej. Wartość jest ustawiana przez obliczenie ważonej sumy wartości węzłów z poprzedniej warstwy. Funkcja aktywacji zostanie następnie zastosowana do tej kwoty ważonej.
  
## <a name="how-to-configure"></a>Jak skonfigurować

1.  Dodaj moduł **sieci neuronowych z dwoma klasami** do potoku. Ten moduł można znaleźć w obszarze **Machine Learning**, **zainicjować**, w kategorii **Klasyfikacja** .  
  
2.  Określ, w jaki sposób ma być szkolony model, ustawiając opcję **tworzenia trybu Trainer** .  
  
    -   **Pojedynczy parametr**: Wybierz tę opcję, jeśli już wiesz, jak chcesz skonfigurować model.  

3.  W obszarze **Specyfikacja warstwy ukrytej**wybierz typ architektury sieci do utworzenia.  
  
    -   W **pełni połączony przypadek**: używa domyślnej architektury sieci neuronowych zdefiniowanej dla dwóch klas neuronowych sieci w następujący sposób:
  
        -   Ma jedną ukrytą warstwę.
  
        -   Warstwa wyjściowa jest w pełni połączona z ukrytą warstwą, a Ukryta warstwa jest w pełni podłączona do warstwy wejściowej.
  
        -   Liczba węzłów w warstwie wejściowej jest równa liczbie funkcji w danych szkoleniowych.
  
        -   Liczba węzłów w warstwie ukryta jest ustawiana przez użytkownika. Wartość domyślna to 100.
  
        -   Liczba węzłów jest równa liczbie klas. W przypadku sieci z dwiema klasami neuronowych oznacza to, że wszystkie dane wejściowe muszą być mapowane na jeden z dwóch węzłów w warstwie wyjściowej.

5.  W polu **stawka szkoleniowa**Zdefiniuj rozmiar kroku wykonany dla każdej iteracji przed poprawką. Większa wartość współczynnika uczenia może spowodować szybsze zbieżność modelu, ale może przekroczyć wartości minimalne.

6.  W polu **Liczba iteracji uczenia**Określ maksymalną liczbę przypadków, w których algorytm powinien przetwarzać przypadki szkoleniowe.

7.  W **polu Średnica początkowej wagi uczenia**Określ wagi węzłów na początku procesu uczenia.

8.  Na czas **pędu**Określ wagę do zastosowania podczas uczenia się do węzłów z poprzednich iteracji  

10. Wybierz opcję **losowe przykłady** , aby losowo rozróżnić przypadki między iteracjami. W przypadku zaznaczenia tej opcji przypadki są przetwarzane w dokładnie tym samym porządku przy każdym uruchomieniu potoku.
  
11. W przypadku **liczby losowej inicjatora**wpisz wartość, która ma być używana jako inicjator.
  
     Określanie wartości inicjatora jest przydatne, gdy chcesz zapewnić powtarzalność między przebiegami tego samego potoku.  W przeciwnym razie wartość zegara systemowego jest używana jako inicjator, co może spowodować nieco inne wyniki przy każdym uruchomieniu potoku.
  
13. Dodaj oznakowany zestaw danych do potoku i Połącz jeden z [modułów szkoleniowych](module-reference.md).  
  
    -   W przypadku ustawienia opcji **Utwórz tryb Trainer** na **pojedynczy parametr**Użyj modułu [uczenie modelu](train-model.md) .  
  
14. Uruchamianie potoku.

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby zapisać migawkę przeszkolonego modelu, wybierz kartę dane **wyjściowe** w prawym panelu modułu **uczenie modelu** . Wybierz ikonę **zarejestruj zestaw danych** , aby zapisać model jako moduł wielokrotnego użytku.

+ Aby użyć modelu do oceniania, Dodaj moduł **model oceny** do potoku.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
