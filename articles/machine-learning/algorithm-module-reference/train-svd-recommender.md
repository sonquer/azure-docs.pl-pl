---
title: 'Polecający ciąg SVD: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób używać modułu polecania SVD (pociąg) w Azure Machine Learning, aby szkolić Bayesowskieer przy użyciu algorytmu SVD.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 075216cf7d67aa4d5a04f34a7ae3444a078b4c62
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76313913"
---
# <a name="train-svd-recommender"></a>Trenowanie modułu poleceń SVD

W tym artykule opisano sposób korzystania z modułu polecania SVD w programie Azure Machine Learning Designer. Ten moduł służy do uczenia modelu rekomendacji na podstawie algorytmu pojedynczego rozkładu wartości (SVD).  

Moduł polecający SVD pociąg odczytuje zestaw danych potrójnych ocen elementów użytkownika. Zwraca zalecany polecający SVD. Można następnie użyć przeszkolonego modelu do przewidywania klasyfikacji lub wygenerowania zaleceń przy użyciu modułu [zalecanego do oceny SVD](score-svd-recommender.md) .  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>Więcej informacji na temat modeli rekomendacji i zalecenia dotyczącego SVD  

Głównym celem systemu rekomendacji jest zalecanie co najmniej jednego *elementu* *użytkownikom* systemu. Przykładami elementów może być film, restauracji, książka lub utwór. Użytkownik może być osobą, grupą osób lub inną jednostką z preferencjami elementu.  

Istnieją dwa główne podejścia do zalecanych systemów: 

+ Podejście **oparte na zawartości** umożliwia korzystanie z funkcji zarówno dla użytkowników, jak i elementów. Użytkowników można opisać według właściwości, takich jak wiek i płeć. Elementy można opisać według właściwości, takich jak autor i producent. Typowe przykłady opartych na zawartości systemów rekomendacji można znaleźć w witrynach Matchmaking społecznościowych. 
+ **Filtrowanie do współpracy** korzysta wyłącznie z identyfikatorów użytkowników i elementów. Uzyskuje niejawne informacje o tych jednostkach z macierzy (rozrzedzonej) klasyfikacji przyznanych przez użytkowników do elementów. Możemy dowiedzieć się więcej o użytkowniku z przedmiotowych elementów i od innych użytkowników, którzy oceniali te same elementy.  

Polecający SVD używa identyfikatorów użytkowników i elementów oraz matrycy ocen określonych przez użytkowników do elementów. Jest to *polecana wspólnie*. 

Aby uzyskać więcej informacji na temat zalecenia SVD, zobacz odpowiedni dokument badawczy: [techniki factorization macierzy dla systemów zalecanych](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).


## <a name="how-to-configure-train-svd-recommender"></a>Jak skonfigurować zalecany polecający pociąg  

### <a name="prepare-data"></a>Przygotowywanie danych

Przed użyciem modułu, dane wejściowe muszą być w formacie, którego oczekuje model rekomendacji. Wymagany jest zestaw danych szkoleniowych o trzech klasyfikacjach elementów użytkownika.

+ Pierwsza kolumna zawiera identyfikatory użytkowników.
+ Druga kolumna zawiera identyfikatory elementów.
+ Trzecia kolumna zawiera klasyfikację dla pary elementu User-Item. Wartości klasyfikacji muszą być typu liczbowego.  

Zestaw danych **klasyfikacji restauracji** w projektancie Azure Machine Learning (wybierz pozycję **zapisane zestawy danych** , a następnie **przykłady**) demonstruje oczekiwany format:

|userID|placeID|rating|
|------------|-------------|------------|
|U1077|135085|2|
|U1077|135038|2|

Z tego przykładu można zobaczyć, że jeden użytkownik ocenił dwa osobne Restauracje. 

### <a name="train-the-model"></a>Trenowanie modelu

1.  Dodaj moduł polecający SVD (szkolenie) do potoku w Projektancie i podłącz go do danych szkoleniowych.  
   
2.  Dla **liczby czynników**Określ liczbę czynników, które mają być używane z zaleceniem.  
    
    Każdy czynnik mierzy, jak dużo użytkownik jest odnosił się do elementu. Liczba czynników jest również wymiarem obszaru ukryty obszar. Po zwiększeniu liczby użytkowników i elementów lepiej jest ustawić większą liczbę czynników. Ale jeśli liczba jest zbyt duża, może to spowodować spadek wydajności.
    
3.  **Liczba iteracji algorytmu rekomendacji** wskazuje, ile razy algorytm powinien przetworzyć dane wejściowe. Im wyższy numer to, tym dokładniejsze są przewidywania. Jednak wyższa liczba oznacza wolniejsze szkolenie. Wartość domyślna to 30.

4.  W polu **stawka szkoleniowa**wprowadź liczbę z zakresu od 0,0 do 2,0, która definiuje rozmiar kroku uczenia się.

    Szybkość uczenia określa rozmiar kroku w każdej iteracji. Jeśli rozmiar tego kroku jest zbyt duży, można przekroczyć optymalne rozwiązanie. Jeśli rozmiar tego kroku jest zbyt mały, szkolenie zajmuje więcej czasu na znalezienie najlepszego rozwiązania. 
  
5.  Uruchamianie potoku.  


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
