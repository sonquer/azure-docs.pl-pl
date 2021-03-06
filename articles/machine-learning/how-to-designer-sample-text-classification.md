---
title: 'Projektant: zaklasyfikowanie przykładu przeglądów książki'
titleSuffix: Azure Machine Learning
description: Utwórz wieloklasowy klasyfikator regresji logistycznej, aby przewidzieć kategorię firmy z zestawem danych Wikipedia SP 500 przy użyciu programu Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 02/11/2020
ms.openlocfilehash: f15f50e372d0bfe58018b16ebfa5d5d85644ae1a
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137783"
---
# <a name="build-a-classifier-to-predict-company-category-using-azure-machine-learning-designer"></a>Kompiluj klasyfikator, aby przewidzieć kategorię firmy przy użyciu programu Azure Machine Learning Designer.

**Projektant (wersja zapoznawcza) — przykład 7**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Ten przykład ilustruje sposób użycia modułów analizy tekstu do kompilowania potoku klasyfikacji tekstu w programie Azure Machine Learning Designer (wersja zapoznawcza).

Celem klasyfikacji tekstu jest przypisanie pewnego fragmentu tekstu do co najmniej jednej wstępnie zdefiniowanej klasy lub kategorii. Fragment tekstu może być dokumentem, artykułem z wiadomościami, wyszukiwaniem, pocztą e-mail, Tweetem, biletami pomocy technicznej, opiniami dotyczącymi klientów, przeglądem produktów użytkowników itp. Aplikacje klasyfikacji tekstu obejmują kategoryzację artykułów gazetowych i treści wiadomości w postaci tematów, Organizowanie stron sieci Web w kategorie hierarchiczne, filtrowanie wiadomości e-mail z spamem, analiza tonacji, przewidywanie zamiaru użytkownika z zapytań wyszukiwania, routingu Pomoc techniczna biletów oraz analizowanie opinii klientów. 

Ten potok pociąga z **wieloklasowego klasyfikatora regresji logistycznej** w celu przewidywania kategorii firmy z **zestawem danych programu Wikipedia SP 500 pochodzącym z witryny Wikipedia**.  

Podstawowe kroki związane z modelem uczenia maszynowego z danymi tekstowymi są następujące:

1. Pobieranie danych

1. Wstępne przetwarzanie danych tekstowych

1. Inżynieria funkcji

   Konwertuj funkcję tekstu na funkcję liczbową za pomocą modułu wyodrębniania funkcji, takiego jak mieszanie funkcji, Wyodrębnij funkcję n-gramową z danych tekstowych.

1. Uczenie modelu

1. Zestaw danych oceny

1. Oceń model

Oto końcowy, ukończony wykres potoku, nad którym pracujemy. Udostępnimy uzasadnienie dla wszystkich modułów, dzięki czemu będziesz w stanie podjąć podobne decyzje.

[![Graf potoku](./media/how-to-designer-sample-text-classification/nlp-modules-overall.png)](./media/how-to-designer-sample-text-classification/nlp-modules-overall.png#lightbox)

## <a name="data"></a>Dane

W tym potoku używany jest zestaw danych **Wikipedia SP 500** . Zestaw danych jest tworzony na podstawie witryny Wikipedia (https://www.wikipedia.org/) opartej na artykułach każdej usługi S & P 500 firmy. Przed przekazaniem do programu Azure Machine Learning Designer zestaw danych został przetworzony w następujący sposób:

- Wyodrębnij zawartość tekstową dla każdej konkretnej firmy
- Usuwanie formatowania wiki
- Usuń znaki inne niż alfanumeryczne
- Konwertuj cały tekst na małe litery
- Dodano znane kategorie firmy

Nie można znaleźć artykułów dla niektórych firm, więc liczba rekordów jest mniejsza niż 500.

## <a name="pre-process-the-text-data"></a>Wstępne przetwarzanie danych tekstowych

Moduł **tekstu przetwarzania wstępnego** jest używany do wstępnego przetwarzania danych tekstowych, takich jak wykrywanie zdań, tokenize zdania i tak dalej. Wszystkie obsługiwane opcje można znaleźć w artykule [**wstępnego przetwarzania tekstu**](algorithm-module-reference/preprocess-text.md) . Po wstępnym przetwarzaniu danych tekstowych używamy modułu **Split Data** , aby losowo podzielić dane wejściowe, tak aby zestaw danych szkoleniowy zawierał 50% danych oryginalnych, a test dataset zawiera 50% oryginalnych danych.

## <a name="feature-engineering"></a>Inżynieria funkcji
W tym przykładzie będziemy używać dwóch metod wykonywania zadań inżynieryjnych.

### <a name="feature-hashing"></a>Tworzenie skrótów funkcji
Moduł [**skrótu funkcji**](algorithm-module-reference/feature-hashing.md) został użyty do przekonwertowania zwykłego tekstu artykułów na liczby całkowite i używania wartości całkowitych jako funkcji wejściowych w modelu. 

Moduł **wyznaczania wartości skrótu funkcji** może służyć do konwertowania dokumentów tekstowych o zmiennej długości do wektorów funkcji liczbowych o równej długości, przy użyciu 32-bitowej metody mieszania murmurhash v3 dostarczonej przez bibliotekę Wabbit Vowpal. Celem używania mieszania funkcji jest zmniejszenie liczby wymiarów; Funkcja tworzenia skrótów umożliwia również szybsze wyszukiwanie wag funkcji w czasie klasyfikacji, ponieważ używa porównania wartości skrótu zamiast porównywania ciągów.

W potoku próbkowania ustawiamy liczbę bitów mieszania do 14 i ustawimy liczbę n-gramów na 2. Dzięki tym ustawieniom w tabeli skrótów można przechowywać 2 ^ 14 wpisów, w których każda funkcja tworzenia skrótów reprezentuje jedną lub więcej funkcji n-gramowych, a jej wartość reprezentuje częstotliwość występowania tego n-grama w wystąpieniu tekstu. W przypadku wielu problemów tabela skrótów tego rozmiaru jest większa niż odpowiednia, ale w niektórych przypadkach może być konieczne więcej miejsca, aby uniknąć kolizji. Oceń wydajność rozwiązania uczenia maszynowego przy użyciu innej liczby bitów. 

### <a name="extract-n-gram-feature-from-text"></a>Wyodrębnij funkcję N-gramową z tekstu

N-gram jest ciągłą sekwencją n terminów od danej sekwencji tekstu. N-gram o rozmiarze 1 jest określany jako unigram; n-gram o rozmiarze 2 to dwugramowa; n-gram o rozmiarze 3 to trigram. N-gramy większych rozmiarów są czasami określane przez wartość n, na przykład "cztery-gramy", "pięć-gram" itd.

Używamy [**funkcji Wyodrębnij N-gram z modułu tekstowego**](algorithm-module-reference/extract-n-gram-features-from-text.md) jako innego rozwiązania dla inżynierów funkcji. Ten moduł najpierw wyodrębnia zestaw n-gramów, oprócz n-gramów, liczbę dokumentów, w których każde n-gram pojawia się w tekście jest zliczane (DF). W tym przykładzie Metryka TF-IDF jest używana do obliczania wartości funkcji. Następnie konwertuje niestrukturalne dane tekstowe na wektorowe funkcje liczbowe o stałej długości, gdzie każda funkcja reprezentuje TF-IDF dla n-gram w wystąpieniu tekstu.

Po przeprowadzeniu konwersji danych tekstowych do wektorów funkcji liczbowych, moduł **SELECT Column** służy do usuwania danych tekstowych z zestawu danych. 

## <a name="train-the-model"></a>Uczenie modelu

Wybór algorytmu często zależy od wymagań dotyczących przypadku użycia. Ponieważ celem tego potoku jest przewidywanie kategorii firmy, model klasyfikatora wieloklasowego jest dobrym wyborem. Biorąc pod uwagę, że liczba funkcji jest duża i są one rozrzedzone, używamy modelu **regresji logistycznej** dla tego potoku.

## <a name="test-evaluate-and-compare"></a>Testowanie, obliczanie i porównywanie

 W celu przeprowadzenia oceny modelu zestawu danych i przetestowania modelu należy użyć różnych zestawów

Po przeszkoleniu modelu będziemy używać **modelu oceny** i **oceny modułów modelu** do generowania przewidywanych wyników i oceny modeli. Jednak przed rozpoczęciem korzystania z modułu **modelu oceny** należy przeprowadzić Inżynieria funkcji, ponieważ wymagane jest przeszkolenie. 

W przypadku modułu tworzenia **skrótów funkcji** można łatwo wykonywać inżynierów funkcji przy przepływie oceniania jako przepływu szkoleniowego. Użyj modułu **skrótu funkcji** bezpośrednio, aby przetwarzać dane wejściowe tekstu.

W przypadku **wyodrębnienia funkcji N-gram z modułu tekstowego** , będziemy łączyć **dane wyjściowe słownictwa** z przepływu danych szkoleniowego do **słownika wejściowego** w przepływu danych oceniania, a następnie ustawić dla parametru **tryb słownictwa** wartość **tylko do odczytu**.
[Wykres ![u wyniku n-gram](./media/how-to-designer-sample-text-classification/n-gram.png)](./media/how-to-designer-sample-text-classification/n-gram.png)

Po zakończeniu kroku inżynieryjnego **model wyniku** może być używany do generowania prognoz dla zestawu danych testowych przy użyciu przeszkolonego modelu. Aby sprawdzić wynik, wybierz port wyjściowy **modelu oceny** , a następnie wybierz opcję **Wizualizuj**.

Następnie przekazujemy wyniki do modułu **oceny modelu** , aby generować metryki oceny. **Model oceny** ma dwa porty wejściowe, dzięki czemu możemy obliczyć i porównać zestawy danych z wynikami, które są generowane przy użyciu różnych metod. W tym przykładzie porównamy wydajność wygenerowanego wyniku za pomocą metody skrótu funkcji i metody n-gram.
Aby sprawdzić wynik, wybierz port wyjściowy **oszacowania modelu** , a następnie wybierz opcję **Wizualizuj**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi przykładami dostępnymi dla projektanta:
- [Przykład 1-regresja: przewidywanie ceny za samochód](how-to-designer-sample-regression-automobile-price-basic.md)
- [Przykład 2-Regresja: porównanie algorytmów do prognozowania cen samochodów](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Przykład 3 — Klasyfikacja z wyborem funkcji: prognozowanie dochodu](how-to-designer-sample-classification-predict-income.md)
- [Przykład 4 — Klasyfikacja: przewidywanie ryzyka kredytowego (z uwzględnieniem kosztów)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Przykład 5 — Klasyfikacja: przewidywalność zmian](how-to-designer-sample-classification-churn.md)
- [Przykład 6 — Klasyfikacja: przewidywanie opóźnień lotów](how-to-designer-sample-classification-flight-delay.md)
