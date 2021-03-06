---
title: Utwórz nową aplikację — LUIS
titleSuffix: Azure Cognitive Services
description: Twórz aplikacje i zarządzaj nimi na stronie Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: d3f8696388a33a8ea112aae438c6bbe9af520c61
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904267"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Tworzenie nowej aplikacji LUIS w portalu LUIS
Istnieje kilka sposobów tworzenia aplikacji LUIS. Aplikację LUIS można utworzyć w portalu LUIS lub za pomocą [interfejsów API](developer-reference-resource.md)tworzenia Luis.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="using-the-luis-portal"></a>Korzystanie z portalu LUIS

Nową aplikację można utworzyć w portalu w wersji zapoznawczej na kilka sposobów:

* Zacznij od pustej aplikacji i Utwórz intencje, wyrażenia długości i jednostki.
* Zacznij od pustej aplikacji i Dodaj [prezbudowaną domenę](luis-how-to-use-prebuilt-domains.md).
* Zaimportuj aplikację LUIS z pliku `.lu` lub `.json`, który zawiera już intencje, wyrażenia długości i jednostki.

## <a name="using-the-authoring-apis"></a>Korzystanie z interfejsów API tworzenia
Nową aplikację można utworzyć za pomocą interfejsów API tworzenia na kilka sposobów:

* [Dodaj aplikację](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) — Zacznij od pustej aplikacji i Utwórz intencje, wyrażenia długości i jednostki.
* [Dodaj prekompilowaną aplikację](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5) — Zacznij od wstępnie skompilowanej domeny, w tym intencji, wyrażenia długości i jednostek.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>Utwórz nową aplikację w LUIS

1. Na stronie **Moje aplikacje** wybierz swoją subskrypcję, a następnie **Utwórz**zasób, a następnie kliknij pozycję Włącz. Jeśli używasz bezpłatnego klucza wersji próbnej, Dowiedz się, jak [utworzyć zasób tworzenia](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal).

    ![Lista aplikacji LUIS](./media/create-app-in-portal.png)


1. W oknie dialogowym wprowadź nazwę aplikacji, taką jak `Pizza Tutorial`.

    ![Okno dialogowe Tworzenie nowej aplikacji](./media/create-pizza-tutorial-app-in-portal.png)

1. Wybierz kulturę aplikacji, a następnie wybierz pozycję **gotowe**. Opis i zasób przewidywania są opcjonalne w tym momencie. W każdej chwili możesz ją ustawić w sekcji **Zarządzanie** w portalu.

    > [!NOTE]
    > Kultury nie można zmienić po utworzeniu aplikacji. 

    Po utworzeniu aplikacji Portal LUIS pokazuje listę **intencji** z zamiarem `None` już utworzonym przez użytkownika. Masz teraz pustą aplikację. 
    
    > [!div class="mx-imgBorder"]
    > Lista założeń, dla których nie utworzono żadnego z przykładowych wyrażenia długości, nie została utworzona. ![](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available"></a>Inne dostępne akcje

Pasek narzędzi kontekstowych zawiera inne akcje:

* Zmień nazwę aplikacji
* Importuj z pliku przy użyciu `.lu` lub `.json`
* Eksportuj aplikację jako `.lu` (dla [LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)), `.json`lub `.zip` (dla [kontenera Luis](luis-container-howto.md))
* Importuj dzienniki punktów końcowych kontenera, aby przejrzeć wyrażenia długości punktu końcowego
* Eksportowanie dzienników punktów końcowych `.csv`w celu przeprowadzenia analizy w trybie offline
* Usuń aplikację

## <a name="next-steps"></a>Następne kroki

Jeśli projekt aplikacji obejmuje wykrywanie intencji, [Utwórz nowe intencje](luis-how-to-add-intents.md)i Dodaj przykład wyrażenia długości. Jeśli projekt aplikacji jest tylko do wyodrębnienia danych, Dodaj przykład wyrażenia długości do zamiaru brak, a następnie [Utwórz jednostki](luis-how-to-add-example-utterances.md)i Oznacz przykład wyrażenia długości z tymi jednostkami. 
