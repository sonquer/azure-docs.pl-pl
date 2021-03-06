---
title: 'Projektant: przykład przewidywania opóźnień lotów'
titleSuffix: Azure Machine Learning
description: Tworzenie klasyfikatora i używanie niestandardowego kodu języka R do przewidywania opóźnień lotów za pomocą projektanta Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: d459350572d68cc5dcbbfd56933483404b94f918
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963283"
---
# <a name="build-a-classifier--use-r-to-predict-flight-delays-with-azure-machine-learning-designer"></a>Kompilowanie klasyfikatora & użycie R do przewidywania opóźnień lotów za pomocą projektanta Azure Machine Learning

**Projektant (wersja zapoznawcza) — przykład 6**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Ten potok używa historycznych danych lot i Pogoda do przewidywania, czy zaplanowany lot pasażera zostanie opóźniony o ponad 15 minut. Ten problem można rozwiązać jako problem klasyfikacji, przewidzieć dwie klasy: opóźnione lub w czasie.

Oto wykres końcowego potoku dla tego przykładu:

[![Graf potoku](media/how-to-designer-sample-classification-flight-delay/pipeline-graph.png)](media/how-to-designer-sample-classification-flight-delay/pipeline-graph.png#lightbox)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Kliknij pozycję przykład 6, aby ją otworzyć.

## <a name="get-the-data"></a>Pobieranie danych

Ten przykład używa zestawu **danych opóźnienia lotu** . Jest częścią zbierania danych TranStats z Departamentu transportu USA. Zestaw danych zawiera informacje o opóźnieniu lotów od kwietnia do października 2013. Zestaw danych został wstępnie przetworzony w następujący sposób:

* Filtrowany w celu uwzględnienia portów lotniczych najgorętszym 70 w kontynentalnej Stany Zjednoczone.
* Z oznaczeniem anulowanych lotów jako opóźnionych o ponad 15 minut.
* Odfiltrowane przekierowane loty.
* Wybrano 14 kolumn.

Aby uzupełnić dane lotu, używany jest **zestaw danych pogody** . Dane pogodowe zawierają co godzinę, oparte na lądzie obserwacje pogody z NOAA i reprezentuje obserwacje z stacji pogodowych dotyczących portów lotniczych, obejmujących ten sam okres czasu, który jest zestawem danych lotów. Został wstępnie przetworzony w następujący sposób:

* Identyfikatory stacji pogody zostały zamapowane na odpowiednie identyfikatory portów lotniczych.
* Nie zostały usunięte stacje pogody z najgorętszym portów lotniczych 70.
* Kolumna Date została podzielona na oddzielne kolumny: Year, month i Day.
* Wybrano 26 kolumn.

## <a name="pre-process-the-data"></a>Wstępnie przetwórz dane

Zestaw danych wymaga zwykle przetworzenia wstępnego, zanim będzie można go przeanalizować.

![przetwarzanie danych](./media/how-to-designer-sample-classification-flight-delay/data-process.png)

### <a name="flight-data"></a>Dane lotu

Kolumny **Carrier**, **OriginAirportID**i **DestAirportID** są zapisywane jako liczby całkowite. Jednak są one atrybutami kategorii, za pomocą modułu **Edytowanie metadanych** można konwertować je na kategorii.

![edit-metadata](./media/how-to-designer-sample-classification-flight-delay/edit-metadata.png)

Następnie użyj modułu **Wybieranie kolumn** w zestawie danych, aby wykluczyć z kolumn zestawu danych, które są możliwymi przeciekami docelowymi: **DepDelay**, **DepDel15**, **ArrDelay**, **anulowane**, **Year**. 

Aby dołączać rekordy lotu z rekordami godzinową pogody, użyj zaplanowanego czasu wyruszenia jako jednego z kluczy sprzężenia. Aby wykonać sprzężenie, kolumna CSRDepTime musi zostać zaokrąglona w dół do najbliższej godziny, która jest wykonywana w module **wykonywania skryptu języka R** . 

### <a name="weather-data"></a>Dane pogodowe

Kolumny, które mają dużą część brakujących wartości, są wykluczone przy użyciu modułu **kolumn projektu** . Te kolumny zawierają wszystkie kolumny wartości ciągów: **ValueForWindCharacter**, **WetBulbFarenheit**, **WetBulbCelsius**, **PressureTendency**, **PressureChange**, **SeaLevelPressure**i **StationPressure**.

**Czysty, nieobecny moduł danych** zostanie zastosowany do pozostałych kolumn w celu usunięcia wierszy z brakującymi danymi.

Czasy obserwacji pogody są zaokrąglane do najbliższej pełnej godziny. Czasy zaplanowanych lotów i czas obserwacji pogodowej są zaokrąglane w przeciwnych kierunkach, aby zapewnić, że model używa tylko pogody przed czasem lotu. 

Ponieważ dane pogody są raportowane w czasie lokalnym, są uwzględniane różnice strefy czasowej przez odjęcie kolumn strefy czasowej od zaplanowanego czasu wyruszenia oraz czasu obserwacji pogodowej. Te operacje są wykonywane za pomocą modułu **skryptu Execute języka R** .

### <a name="joining-datasets"></a>Sprzęganie zestawów danych

Rekordy lotu są **dołączane** do danych pogodowych, które znajdują się na początku lotu (**OriginAirportID**) przy użyciu modułu Join Data.

 ![Dołącz do lotu i pogody według pochodzenia](./media/how-to-designer-sample-classification-flight-delay/join-origin.png)


Rekordy lotu są przyłączone do danych pogody przy użyciu miejsca docelowego lotu (**DestAirportID**).

 ![Dołącz do lotu i pogody według miejsca docelowego](./media/how-to-designer-sample-classification-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>Przygotowywanie szkoleń i próbek testowych

Moduł **Split Data** dzieli dane na rekordy z kwietnia na potrzeby szkoleń i rejestrów z października na potrzeby testu.

 ![Podziel dane szkoleniowe i testowe](./media/how-to-designer-sample-classification-flight-delay/split.png)

Kolumny Year, month i timezone są usuwane z zestawu danych szkoleniowych przy użyciu modułu SELECT Columns.

## <a name="define-features"></a>Definiowanie funkcji

W uczeniu maszynowym funkcje są indywidualnymi właściwościami interesujących rzeczy. Znalezienie silnego zestawu funkcji wymaga eksperymentowania i znajomości domeny. Pewne cechy lepiej nadają się do prognozowania danych docelowych. Ponadto niektóre funkcje mogą mieć silną korelację z innymi funkcjami i nie będą dodawać do modelu nowych informacji. Te funkcje można usunąć.

Aby skompilować model, możesz użyć wszystkich dostępnych funkcji lub wybrać podzestaw funkcji.

## <a name="choose-and-apply-a-learning-algorithm"></a>Wybieranie i stosowanie algorytmu uczenia

Utwórz model przy użyciu **dwuklasowego modułu regresji logistycznej** i pouczenie go w zestawie danych szkoleniowych. 

Wynik modułu **uczenie modelu** jest przeszkolonym modelem klasyfikacji, który może służyć do oceny nowych próbek do prognozowania. Użyj zestawu testów, aby wygenerować wyniki z modeli szkolonych. Następnie należy użyć modułu **Oceń model** do analizowania i porównywania jakości modeli.
Potok po uruchomieniu potoku możesz wyświetlić dane wyjściowe z modułu **wyniku modelu** , klikając port wyjściowy i wybierając opcję **Wizualizuj**. Dane wyjściowe zawierają etykiety z wynikami i prawdopodobieństwa etykiet.

Na koniec w celu przetestowania jakości wyników Dodaj moduł **Oceń model** do kanwy potoku, a następnie połącz lewy port wejściowy z danymi wyjściowymi modułu wynikowego modelu. Uruchom potok i Wyświetl dane wyjściowe modułu **Oceń model** , klikając port wyjściowy i wybierając opcję **Wizualizuj**.

## <a name="evaluate"></a>Oceń
Model regresji logistycznej ma AUC 0,631 w zestawie testów.

 ![evaluate](media/how-to-designer-sample-classification-flight-delay/sample6-evaluate-1225.png)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi przykładami dostępnymi dla projektanta:

- [Przykład 1-regresja: przewidywanie ceny za samochód](how-to-designer-sample-regression-automobile-price-basic.md)
- [Przykład 2-Regresja: porównanie algorytmów do prognozowania cen samochodów](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Przykład 3 — Klasyfikacja z wyborem funkcji: prognozowanie dochodu](how-to-designer-sample-classification-predict-income.md)
- [Przykład 4 — Klasyfikacja: przewidywanie ryzyka kredytowego (z uwzględnieniem kosztów)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Przykład 5 — Klasyfikacja: przewidywalność zmian](how-to-designer-sample-classification-churn.md)
- [Przykład 7 — Klasyfikacja tekstu: zestaw danych witryny Wikipedia SP 500](how-to-designer-sample-text-classification.md)