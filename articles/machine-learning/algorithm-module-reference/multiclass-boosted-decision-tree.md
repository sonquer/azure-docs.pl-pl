---
title: 'Drzewo decyzyjnej grupy wieloklasowej: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać wieloklasowego modułu drzewa decyzyjnego w Azure Machine Learning do tworzenia klasyfikatora przy użyciu danych z etykietami.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 0bcca16bd89781428773eda168e6ee3c2f5784ef
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152180"
---
# <a name="multiclass-boosted-decision-tree"></a>Wieloklasowe wzmocnione drzewo decyzyjne

W tym artykule opisano moduł w programie Azure Machine Learning Designer (wersja zapoznawcza).

Ten moduł służy do tworzenia modelu uczenia maszynowego, który jest oparty na algorytmie drzew z podwyższaną decyzją.

Wzmocnione drzewo decyzyjne to metoda uczenia się, w której drugie drzewo jest zgodne z błędami pierwszego drzewa, trzecie drzewo jest odpowiednie dla błędów pierwszego i drugiego drzewa i tak dalej. Przewidywania opierają się na rozwiązaniu wszystkich drzew jednocześnie.

## <a name="how-to-configure"></a>Jak skonfigurować 

Ten moduł tworzy niepociąg model klasyfikacji. Ponieważ klasyfikacja jest metodą uczenia nadzorowanego, potrzebny jest *oznaczony zestaw danych* , który zawiera kolumnę etykiety z wartością dla wszystkich wierszy.

Ten typ modelu można przeszkolić przy użyciu [modelu uczenia](././train-model.md). 

1.  Dodaj moduł **drzewa decyzyjnej wieloklasowej** do potoku.

1.  Określ sposób, w jaki model ma być szkolony przez ustawienie opcji **tworzenia trybu Trainer** .

    + **Pojedynczy parametr**: Jeśli wiesz, jak chcesz skonfigurować model, możesz podać określony zestaw wartości jako argumenty.


    *  **Maksymalna liczba liści na drzewo** ogranicza maksymalną liczbę węzłów terminalu (liści), które można utworzyć w dowolnym drzewie.
    
        Zwiększając tę wartość, można zwiększyć rozmiar drzewa i osiągnąć wyższą precyzję w przypadku ryzyka przekroczenia i dłuższego czasu uczenia się.
  
    * **Minimalna liczba próbek na węzeł liścia** wskazuje liczbę przypadków wymaganych do utworzenia dowolnego węzła terminalu w drzewie.  

         Zwiększenie tej wartości spowoduje zwiększenie wartości progowej tworzenia nowych reguł. Na przykład, z wartością domyślną 1, nawet pojedynczy przypadek może spowodować utworzenie nowej reguły. W przypadku zwiększenia wartości do 5 dane szkoleniowe muszą zawierać co najmniej pięć przypadków, które spełniają te same warunki.

    * **Szybkość uczenia** definiuje rozmiar kroku podczas uczenia się. Wprowadź liczbę z zakresu od 0 do 1.

         Szybkość uczenia określa, jak szybko lub wolno dowiedzieć się zbieżność z optymalnym rozwiązaniem. Jeśli rozmiar tego kroku jest zbyt duży, można przekroczyć optymalne rozwiązanie. Jeśli rozmiar tego kroku jest zbyt mały, szkolenie trwa dłużej niż w przypadku najlepszego rozwiązania.

    * **Liczba skonstruowanych drzew** wskazuje całkowitą liczbę drzew decyzyjnych do utworzenia w całości. Przez utworzenie większej liczby drzew decyzyjnych można potencjalnie uzyskać lepszy zakres, ale zwiększy się czas uczenia.

    *  **Inicjator liczb losowych** opcjonalnie ustawia nieujemną liczbę całkowitą do użycia jako losową wartość inicjatora. Określenie inicjatora zapewnia odtwarzalność w przebiegach, które mają te same dane i parametry.  

         Losowy inicjator jest domyślnie ustawiony na 42. Kolejne uruchomienia przy użyciu różnych losowych nasion mogą mieć różne wyniki.

> [!Note]
> Jeśli ustawisz **tryb tworzenia Trainer** na **pojedynczy parametr**, Połącz znacznikowy zestaw danych i moduł [uczenie modelu](./train-model.md) .

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
