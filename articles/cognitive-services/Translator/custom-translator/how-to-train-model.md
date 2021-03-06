---
title: Uczenie modelu — translator niestandardowy
titleSuffix: Azure Cognitive Services
description: Uczenie modelu jest ważnym krokiem podczas kompilowania modelu tłumaczenia. Szkolenia odbywają się w oparciu o dokumenty wybrane dla tych szkoleń.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: a875cc8f5e69be8b940f9d177d3b915bfaa2c973
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595686"
---
# <a name="train-a-model"></a>Szkolenie modelu

Uczenie modelu jest ważnym krokiem do kompilowania modelu tłumaczenia, ponieważ bez uczenia nie można skompilować modelu. Szkolenia odbywają się w oparciu o dokumenty wybrane dla szkoleń.

Aby nauczyć model:

1.  Wybierz projekt, w którym chcesz skompilować model.

2.  Na karcie dane dla projektu zostaną wyświetlone wszystkie odpowiednie dokumenty dla pary języka projektu. Ręcznie wybierz dokumenty, których chcesz użyć do uczenia modelu. Na tym ekranie można wybrać opcję uczenie, dostrajanie i testowanie dokumentów. Po prostu wybierasz zestaw szkoleniowy i masz do niego możliwość utworzenia przez Ciebie zestawu dostrajania i zestawów testów.

    -  Nazwa dokumentu: Nazwa dokumentu.

    -  Skojarzenia Jeśli dokument jest równoległy lub międzyjęzykowy. Dokumenty z obsługą wielu języków nie są obecnie obsługiwane w przypadku szkoleń.

    -  Typ dokumentu: Może być szkoleń, dostrajania, testowania lub słownika.

    -  Para językowa: Spowoduje to wyświetlenie języka źródłowego i docelowego dla projektu.

    -  Zdania źródłowe: Pokazuje liczbę zdań wyodrębnionych z pliku źródłowego.

    -  Zdania docelowe: Pokazuje liczbę zdań wyodrębnionych z pliku docelowego.

    ![Model uczenia](media/how-to/how-to-train-model.png)

3.  Kliknij przycisk uczenie.

4.  W oknie dialogowym Określ nazwę modelu.

5.  Kliknij pozycję uczenie modelu.

    ![Okno dialogowe uczenie modelu](media/how-to/how-to-train-model-2.png)

6.  Tłumaczenie niestandardowe przekaże szkolenie i wyświetli stan szkolenia na karcie modele.

    ![Strona uczenie modelu](media/how-to/how-to-train-model-3.png)

>[!Note]
>Translator niestandardowy obsługuje 10 współbieżnych szkoleń w obszarze roboczym w dowolnym momencie.


## <a name="edit-a-model"></a>Edytowanie modelu

Można edytować model przy użyciu linku Edytuj na stronie Szczegóły modelu.

1.  Kliknij ikonę ołówka.

    ![Edytuj model](media/how-to/how-to-edit-model.png)

2.  W oknie dialogowym zmiany

    1.  Nazwa modelu (wymagana): Nadaj modelowi zrozumiałą nazwę.

        ![Edytuj Więcej okna dialogowego](media/how-to/how-to-edit-model-dialog.png)

3.  Kliknij pozycję Zapisz.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak wyświetlić szczegóły modelu](how-to-view-model-details.md).
