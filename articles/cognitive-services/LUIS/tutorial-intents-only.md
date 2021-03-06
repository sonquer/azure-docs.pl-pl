---
title: 'Samouczek: przewidywanie zamiarów — LUIS'
titleSuffix: Azure Cognitive Services
description: W tym samouczku utworzysz niestandardową aplikację, która będzie przewidywać intencje użytkownika. Ta aplikacja to najprostszy rodzaj aplikacji usługi LUIS, ponieważ nie wyodrębnia ona z tekstu wypowiedzi różnych elementów danych, takich jak adresy e-mail czy daty.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 89fb76f8c5cc4323e1211524340c0965a7d0716d
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76262757"
---
# <a name="tutorial-build-a-luis-app-to-determine-user-intentions"></a>Samouczek: Tworzenie aplikacji LUIS w celu określenia zamiarów użytkownika

W tym samouczku utworzysz aplikację niestandardową, która przewiduje zamiar użytkownika w oparciu o wypowiedź (tekst).

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

> [!div class="checklist"]
> * Tworzenie nowej aplikacji
> * Tworzenie intencji
> * Dodawanie przykładowych wypowiedzi
> * Szkolenie aplikacji
> * Publikowanie aplikacji
> * Pobierz prognozowanie intencji z punktu końcowego

## <a name="user-intentions-as-intents"></a>Zamiary użytkownika jako intencje

Zadaniem aplikacji jest ustalenie intencji konwersacyjnego tekstu w języku naturalnym:

`I'd like to order a veggie pizza with a salad on the side.`

Intencje są podzielone na kategorie **intencji**.

|Intencja|Przeznaczenie|
|--|--|
|`ModifyOrder`|Określ kolejność Pizza użytkownika.|
|`Greeting`|Rozpocznij KONWERSACJĘ bot.|
|`ConfirmOrder`|Potwierdź zamówienie Pizza.|
|`None`|Ustal, czy użytkownik jest proszony o to, że aplikacja nie powinna odpowiedzieć. Ta intencja jest dostarczana jako część procesu tworzenia aplikacji i nie można jej usunąć. |

## <a name="create-a-new-app"></a>Tworzenie nowej aplikacji

[!INCLUDE [Follow these steps to create a new LUIS app](includes/create-pizza-app.md)]

## <a name="create-a-new-intent"></a>Tworzenie nowej intencji

1. W portalu w sekcji **kompilacja** aplikacji wybierz pozycję **+ Utwórz**. Wprowadź nazwę nowego celu, `OrderPizza`a następnie wybierz pozycję **gotowe**.

    Zamierzone `OrderPizza` jest przewidywane: użytkownik chce zamówić Pizza.

1. Dodaj kilka przykładowych wypowiedzi do tej intencji — takich, których spodziewasz się ze strony użytkownika:

    |`OrderPizza` przykład wyrażenia długości|
    |--|
    |`can i get a pepperoni pizza and a can of coke please`|
    |`can i get a small pizza with onions peppers and olives`|
    |`delivery for a small pepperoni pizza`|
    |`pickup a cheddar cheese pizza large with extra anchovies`|
    |`i need 2 large cheese pizzas 6 large pepperoni pizzas and 1 large supreme pizza`|
    |`Order a pizza for me`|

    ![Dodawanie przykładowych wypowiedzi](media/tutorial-intents-only/add-example-utterances-for-pizza-order.png)

    Dostarczając _przykładowe wypowiedzi_, trenujesz usługę LUIS, jakiego rodzaju wypowiedzi powinny być przewidywane dla tej intencji.

    [!INCLUDE [Do not use too few utterances](includes/do-not-use-too-few-utterances.md)]

## <a name="create-remaining-intents"></a>Utwórz pozostałe intencje

1. Utwórz zamiar `Greeting` i Dodaj następujący przykład wyrażenia długości. Jest to cel, aby określić, czy użytkownik rozpoczyna nową konwersację z kolejnością Pizza.

    |`Greeting` przykład wyrażenia długości|
    |--|
    |`Hi`|
    |`Hello`|
    |`Hey`|
    |`Start`|
    |`Begin`|

1. Utwórz zamiar `Confirm` i Dodaj następujący przykład wyrażenia długości. Jest to cel, aby określić, czy użytkownik ukończy porządkowanie i akceptuje szczegóły zamówienia.

    |`Confirm` przykład wyrażenia długości|
    |--|
    |`Go ahead`|
    |`ok`|
    |`Yes`|
    |`Sure`|


## <a name="none-intent-example-utterances"></a>Brak przykładu zamiaru wyrażenia długości

[!INCLUDE [Follow these steps to add the None intent to the app](includes/add-example-utterances-none-intent.md)]

## <a name="train-the-app"></a>Uczenie aplikacji

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app"></a>Publikowanie aplikacji

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-prediction"></a>Pobierz prognozowanie intencji

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Przejdź na koniec adresu URL na pasku adresu i wprowadź:

    `get a medium vegetarian pizza for delivery`

    Nie jest to dokładnie takie samo, jak przykład wypowiedź, dlatego jest dobrym testem, aby sprawdzić, czy LUIS może dowiedzieć się, co należy przewidzieć w tym zamierzeniu.

    Ostatni parametr ciągu zapytania to `query`, czyli **query** (zapytanie) wypowiedzi. Ta wypowiedź nie przypomina żadnej z przykładowych wypowiedzi. Jest to dobry test, który powinien zwrócić intencję `OrderPizza` jako najwyżej ocenioną.

    ```JSON
    {
        "query": "get a medium vegetarian pizza for delivery",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.6488959
                },
                "None": {
                    "score": 0.139966831
                },
                "Confirm": {
                    "score": 0.00736504374
                },
                "Greeting": {
                    "score": 0.003970454
                }
            },
            "entities": {}
        }
    }
    ```

    Tablica jednostek jest pusta, ponieważ ta aplikacja nie ma obecnie żadnych jednostek (jednostka danych wewnątrz wypowiedź do wyodrębnienia).

    Wynik w formacie JSON określa najwyżej ocenioną intencję jako właściwość **`prediction.topIntent`** . Wszystkie wyniki są z zakresu od 1 do 0, a lepszy wynik jest zbliżony do 1.

1. Zmień parametr **zapytania** adresu URL, aby określić cel **powitania** :

    `Howdy`

    Nie jest to dokładnie takie samo, jak przykład wypowiedź, dlatego jest dobrym testem, aby sprawdzić, czy LUIS może dowiedzieć się, co należy przewidzieć w tym zamierzeniu.

    ```json
    {
        "query": "howdy",
        "prediction": {
            "topIntent": "Greeting",
            "intents": {
                "Greeting": {
                    "score": 0.446016937
                },
                "Confirm": {
                    "score": 0.2390079
                },
                "None": {
                    "score": 0.09119555
                },
                "OrderPizza": {
                    "score": 0.00109590159
                }
            },
            "entities": {}
        }
    }
    ```

    To prognozowanie ma 44% wyniku ufności. Aby zwiększyć wynik pewności, Dodaj od 15 do 30 przykład wyrażenia długości.

## <a name="client-application-next-steps"></a>Następne czynności dotyczące aplikacji klienckiej

Gdy usługa LUIS zwróci odpowiedź w formacie JSON, żądanie zostanie wykonane. Usługa LUIS nie odpowiada na wypowiedzi użytkowników, tylko określa, o jakie informacje proszą oni w języku naturalnym. Dalsza konwersacja jest udostępniana przez aplikację kliencką, na przykład Azure Bot.


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informacje pokrewne

* [Typy jednostek](luis-concept-entity-types.md)
* [Jak trenować](luis-how-to-train.md)
* [Jak opublikować](luis-how-to-publish-app.md)
* [Jak przeprowadzać testy w portalu usługi LUIS](luis-interactive-test.md)
* [Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono aplikację LUIS, utworzono intencje, dodano przykład wyrażenia długości do każdego zamiaru, dodaliśmy przykład wyrażenia długości do zamiaru brak, przeszkolony, opublikowany i przetestowany w punkcie końcowym. Są to podstawowe kroki tworzenia modelu usługi LUIS.

> [!div class="nextstepaction"]
> [Dodaj jednostkę, do której ma zostać utworzona aplikacja](tutorial-machine-learned-entity.md)
