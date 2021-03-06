---
title: Obsługa kontenerów
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 3886777b283af35e84683480a59097584b537fea
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523532"
---
## <a name="create-an-face-resource"></a>Tworzenie zasobu kroju

1. Zaloguj się do [Azure Portal](https://portal.azure.com)
1. Kliknij pozycję [Utwórz zasób **czołowy** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)
1. Wprowadź wszystkie wymagane ustawienia:

    |Ustawienie|Wartość|
    |--|--|
    |Nazwa|Wymagana nazwa (2-64 znaków)|
    |Subskrypcja|Wybierz odpowiednią subskrypcję|
    |Lokalizacja|Wybierz dowolną lokalizację w pobliżu i dostępną|
    |Warstwa cenowa|`F0` — minimalna warstwa cenowa|
    |Grupa zasobów|Wybierz dostępną grupę zasobów|

1. Kliknij przycisk **Utwórz** i poczekaj na utworzenie zasobu. Po jego utworzeniu przejdź do strony zasobów
1. Zbierz skonfigurowane `endpoint` i klucz interfejsu API:

    |Karta zasób w portalu|Ustawienie|Wartość|
    |--|--|--|
    |**Omówienie**|Endpoint|Skopiuj punkt końcowy. Wygląda podobnie do `https://face.cognitiveservices.azure.com/face/v1.0`|
    |**Ponownie**|Klucz interfejsu API|Kopiuj 1 z dwóch kluczy. Jest to 32 ciąg znaków alfanumerycznych bez spacji ani kresek, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
