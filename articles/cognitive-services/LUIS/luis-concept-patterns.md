---
title: Wzorce ułatwiają prognozowanie — LUIS
titleSuffix: Azure Cognitive Services
description: Wzorzec pozwala uzyskać większą dokładność dla intencji bez zapewniania wielu dodatkowych wypowiedzi.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 6c1b548de25369c162b4a08dfa20fce62c17f99f
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75890285"
---
# <a name="patterns-improve-prediction-accuracy"></a>Wzorce zwiększenia dokładności prognozy
Wzorce są przeznaczone do zwiększenia dokładności, gdy kilka wypowiedzi są bardzo podobne.  Wzorzec pozwala uzyskać większą dokładność dla intencji bez zapewniania wielu dodatkowych wypowiedzi. 

## <a name="patterns-solve-low-intent-confidence"></a>Wzorce rozwiązać niski ufności intencji
Należy wziąć pod uwagę aplikacja zarządzania zasobami ludzkimi, zawierający raporty dotyczące schematu organizacyjnego w odniesieniu do pracownika. Podana nazwa pracownika i relacji, LUIS zwraca pracowników zaangażowane. Należy wziąć pod uwagę pracownika, Tom, za pomocą Menedżera nazwę Alicja i zespół o nazwie podwładnych: Michael Rebecca i Carl.

![Obraz schematu organizacyjnego](./media/luis-concept-patterns/org-chart.png)

|Wypowiedzi|Przewidywany intencji|Wynik konwersji|
|--|--|--|
|Kto jest podwładnym Tom firmy?|GetOrgChart|.30|
|Kto jest podwładnym Tom?|GetOrgChart|.30|

Jeśli aplikacja ma od 10 do 20 wypowiedzi o różnej długości zdania, inną kolejność słów i nawet innych wyrazów (synonimy "podrzędne", "manage", "raport"), LUIS, mogą zwracać współczynnik ufności niski. Utwórz wzorzec, aby pomóc LUIS zrozumieć znaczenie kolejności wyrazów. 

Wzorce rozwiązać w następujących sytuacjach: 

* Wynik przeznaczenie jest niski
* Poprawna wartość nie jest najważniejszym wynikiem, ale zbyt blisko najwyższego wyniku. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Wzorce nie są gwarancji intencji
Wzorce używać różnych technologii prognozy. Ustawienie przeznaczenie wypowiedź szablonu we wzorcu nie jest gwarancja intencji prognozowania, ale jest silny sygnał. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>Wzorce nie poprawiają wykrywania jednostek wykrywanych przez maszynę

Wzorzec jest głównie przeznaczony do przewidywania intencji i ról. _Wzorzec. Każda_ jednostka służy do wyodrębniania jednostek o dowolnej postaci. Podczas gdy wzorce używają jednostek, wzorzec nie pomaga wykrywać jednostki obsługiwanej przez maszynę.  

Nie oczekuje się, że w przypadku zwinięcia wielu wyrażenia długości do jednego wzorca nie widzisz ulepszonej przewidywania jednostek. Aby proste jednostki były wyzwalane, należy dodać wyrażenia długości lub użyć jednostek listy, ponieważ wzorzec nie zostanie uruchomiony.

## <a name="patterns-use-entity-roles"></a>Wzorce użycia ról jednostki
Jeśli dwa lub więcej jednostek w wzorzec kontekstowe są powiązane, wzorców użycia jednostki [role](luis-concept-roles.md) można wyodrębnić informacje kontekstowe dotyczące jednostek.  

## <a name="prediction-scores-with-and-without-patterns"></a>Wyniki prognozowania z i bez wzorców
Mając wystarczająco dużo wypowiedzi przykładu, LUIS będą mogli zwiększyć ufności prognoz bez wzorców. Wzorce zwiększyć współczynnik ufności bez konieczności podawania tyle wypowiedzi.  

## <a name="pattern-matching"></a>Dopasowanie wzorca
Wzorzec jest takie samo zależnie od wykrywania jednostek w wzorzec najpierw, a następnie weryfikowania reszty słów i kolejność słów w wzorca. Jednostki są wymagane w wzorzec wzorzec do dopasowania. Wzorzec jest stosowany na poziomie token, a nie na poziomie znaków. 

## <a name="pattern-only-apps"></a>Aplikacje tylko dla wzorców
Możesz utworzyć aplikację z intencjami, które nie mają przykładowej wyrażenia długości, o ile istnieje wzorzec dla każdego zamiaru. W przypadku aplikacji tylko dla wzorców wzorzec nie powinien zawierać jednostek maszynowych, ponieważ wymagają one przykładowej wyrażenia długości. 

## <a name="best-practices"></a>Najlepsze rozwiązania
Dowiedz się, [najlepsze praktyki](luis-concept-best-practices.md).

## <a name="pattern-syntax"></a>Składnia wzorca

Poznaj składnię wzorca na podstawie [odwołania do składni wzorców](reference-pattern-syntax.md). 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o wzorcach:

* [Jak dodać wzorce](luis-how-to-model-intent-pattern.md)
* [Jak dodać wzorzec. dowolna jednostka](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Składnia wzorców](reference-pattern-syntax.md)

> [!div class="nextstepaction"]
> [Dowiedz się, jak Implementowanie wzorców w ramach tego samouczka](luis-tutorial-pattern.md)
