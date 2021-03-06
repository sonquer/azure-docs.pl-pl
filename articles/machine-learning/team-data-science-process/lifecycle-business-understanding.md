---
title: Poznawanie firmy w procesie nauki o danych zespołu
description: Cele, zadania i elementy dostarczane dla etapu opis firm Twoje projekty do nauki o danych w procesie nauki o danych zespołu.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a7aaed519f8f97a9be77a263568aeed5257c16d6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76710333"
---
# <a name="the-business-understanding-stage-of-the-team-data-science-process-lifecycle"></a>Na etapie opis firm cyklu życia zespołowego danych dla celów naukowych

W tym artykule opisano cele, zadania i cele do zrealizowania skojarzone z etapem opis firm procesu do nauki o danych zespołu (TDSP). Ten proces obejmuje zalecane cyklu życia, który umożliwia tworzenie struktury projektów do nauki o danych. Cykl życia przedstawia główne etapy, które projekty zazwyczaj są wykonywane, często iteracyjne:

   1. **Zrozumienie biznesowe**
   2. **Pozyskiwanie i zrozumienie danych**
   3. **Modelu**
   4. **Wdrożenie**
   5. **Akceptacja klienta**

Oto wizualnej reprezentacji cyklu przetwarzania TDSP: 

![Cykl życia przetwarzania TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cele
* Określ klucza zmienne, które mają służyć jako obiekty docelowe modelu i którego powiązane metryki są używane ustalenia powodzenia projektu.
* Określenie odpowiednich źródeł danych, które ma dostęp do firmy, lub musi uzyskać.

## <a name="how-to-do-it"></a>Jak to zrobić
Istnieją dwa główne zadania, które zostały rozwiązane podczas tego etapu: 

   * **Zdefiniuj cele**: Współpracuj z klientem i innymi uczestnikami projektu, aby zrozumieć i zidentyfikować problemy biznesowe. Formułowanie pytania, które definiują cele biznesowe, przeznaczonych dla metody do nauki o danych.
   * **Identyfikowanie źródeł danych**: Znajdź odpowiednie dane, które pomagają odpowiedzieć na pytania, które definiują cele projektu.

### <a name="define-objectives"></a>Zdefiniuj cele
1. Głównym celem tego kroku jest do identyfikowania zmienne klucza biznesowych, które niezbędne do analizy do prognozowania. Nazywamy te zmienne jako *elementy docelowe modelu*i używamy metryk skojarzonych z nimi w celu ustalenia sukcesu projektu. Dwa przykłady takich celów są prognoz sprzedaży lub prawdopodobieństwo, że kolejność jest fałszywe.

2. Zdefiniuj cele projektu, pytania i rafinacja "ostrych" zadawane pytania, które są istotne, określonej i jednoznaczna. Do nauki o danych jest procesem, który używa nazwy i numery odpowiedzi na takie pytania. Zazwyczaj używasz do nauki o danych i uczenia maszynowego, aby odpowiedzieć pięć typów pytań:
 
   * Ile lub ile? (Regresja)
   * Jakiej kategorii? (Klasyfikacja)
   * Która grupa? (klastrowania)
   * Jest to nieco dziwne? (wykrywanie anomalii)
   * Opcję, która ma być wykonywana? (zalecenie)

   Określ, który z tych pytań jest wysyłane żądanie i jak odpowiadający powoduje to osiągnięcie celów biznesowych.

3. Definiowanie zespołu projektu, określając ról i obowiązków, jego członków. Opracowywanie planu wysokiego poziomu punktu kontrolnego, który możesz powtarzanie czynności w odkrywaniu większej ilości informacji. 

4. Zdefiniuj metryki sukcesu. Na przykład można osiągnąć przewidywanie zmienności klientów. Szybkość dokładność "x" procent jest konieczne do końca tego projektu trzech miesięcy. Dzięki tym danym może zaoferować, postęp dokonany w promocji klienta, aby zmniejszyć. Metryki muszą być **inteligentne**: 

   * Pecific **S** 
   * Easurable **M**
   * **Chievable** 
   * Elevant **R** 
   * **T**— powiązane z edytorem IME 

### <a name="identify-data-sources"></a>Identyfikowanie źródeł danych
Identyfikowanie źródeł danych, które zawierają przykłady znanych odpowiedzi na swoje pytania sharp. Wyszukaj następujące dane:

* Dane, która jest odpowiednia do pytania. Czy masz miary docelowej i funkcje, które są powiązane z obiektem docelowym?
* Dane, które jest dokładne miary docelowej modelu i funkcji.

Może na przykład, okaże się, że istniejące systemy trzeba zbierać i rejestrować dodatkowych typów danych, aby rozwiązać problem i osiągać cele projektu. W takiej sytuacji można szukać zewnętrznych źródeł danych lub zaktualizować systemy zbierania nowych danych.

## <a name="artifacts"></a>Artefakty
Oto elementy dostarczane podczas tego etapu:

   * [Dokument czarteru](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): w definicji struktury projektu przetwarzania TDSP jest dostępny standardowy szablon. Dokument karty jest życia. Należy zaktualizować szablon w całym projekcie, wprowadzić nowe operacje odnajdywania, a jako firma zmienią się wymagania. Klucz jest do iteracji na tego dokumentu, dodając więcej szczegółów, w czasie wykonywania procesu odnajdywania. Zachowaj klienta i innych uczestników projektu w wprowadzania zmian oraz wyraźnego powiadomienia powody zmiany do nich.  
   * [Źródła danych](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources): sekcja **nieprzetworzone źródła danych** w raporcie **definicje danych** , która znajduje się w folderze **raportów danych** projektu przetwarzania TDSP, zawiera źródła danych. W tej sekcji Określa lokalizacje oryginału i docelowego dla nieprzetworzonych danych. W późniejszym etapie należy wypełnić dodatkowe szczegóły, takie jak skrypty, aby przenieść dane do środowiska analityczne.  
   * [Słowniki danych](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries): ten dokument zawiera opisy danych dostarczanych przez klienta. Opisy te obejmują informacje schematu (typy danych i informacji na temat reguł sprawdzania poprawności, jeśli istnieje) i diagramy relacji jednostki, jeśli jest dostępny.

## <a name="next-steps"></a>Następne kroki

Poniżej podano linki do każdego kroku w cyklu życia przetwarzania TDSP:

   1. [Zrozumienie biznesowe](lifecycle-business-understanding.md)
   2. [Pozyskiwanie i zrozumienie danych](lifecycle-data.md)
   3. [Modelu](lifecycle-modeling.md)
   4. [Wdrożenie](lifecycle-deployment.md)
   5. [Akceptacja klienta](lifecycle-acceptance.md)

Oferujemy pełne instruktaże, które pokazują wszystkie kroki procesu dla konkretnych scenariuszy. [Przykładowy artykuł instruktażowy](walkthroughs.md) zawiera listę scenariuszy z linkami i opisami miniatur. Przewodniki pokazują, jak połączyć chmury, lokalnego narzędzia i usługi w przepływie pracy lub potoku do tworzenia inteligentnych aplikacji. 
