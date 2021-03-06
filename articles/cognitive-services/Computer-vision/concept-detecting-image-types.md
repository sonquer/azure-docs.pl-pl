---
title: Wykrywanie typu obrazu — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z funkcją wykrywania typu obrazu interfejs API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: e89fd977129113fa88af1acccd6b05f0bbe90243
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945211"
---
# <a name="detecting-image-types-with-computer-vision"></a>Wykrywanie typów obrazów przy użyciu przetwarzanie obrazów

Korzystając z interfejsu API [analizy obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) , przetwarzanie obrazów może analizować typ zawartości obrazów, wskazując, czy obraz jest obiektem clipart czy rysunkiem.

## <a name="detecting-clip-art"></a>Wykrywanie grafiki clipart

Przetwarzanie obrazów analizuje obraz i ocenia prawdopodobieństwo, że obraz jest przycinania na skalę od 0 do 3, zgodnie z opisem w poniższej tabeli.

| Value | Znaczenie |
|-------|---------|
| 0 | Obiekt inny niż clipart |
| 1 | Udziel |
| 2 | Normalne — clipart |
| 3 | Good-clip-art |

### <a name="clip-art-detection-examples"></a>Przykłady wykrywania obiektów clipart

Poniższe odpowiedzi JSON przedstawiają, co przetwarzanie obrazów zwraca, gdy ocenia prawdopodobieństwo, że przykładowe obrazy są przycinania.

![Obraz clipart wycinka sera](./Images/cheese_clipart.png)

```json
{
    "imageType": {
        "clipArtType": 3,
        "lineDrawingType": 0
    },
    "requestId": "88c48d8c-80f3-449f-878f-6947f3b35a27",
    "metadata": {
        "height": 225,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Niebieska i czołowa Jard](./Images/house_yard.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "a9c8490a-2740-4e04-923b-e8f4830d0e47",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="detecting-line-drawings"></a>Wykrywanie rysunków liniowych

Przetwarzanie obrazów analizuje obraz i zwraca wartość logiczną wskazującą, czy obraz jest rysunkiem liniowym.

### <a name="line-drawing-detection-examples"></a>Przykłady wykrywania rysowania linii

Poniższe odpowiedzi JSON przedstawiają, co przetwarzanie obrazów zwraca, gdy wskazuje, czy przykładowe obrazy są ilustracjami linii.

![Obraz przedstawiający rysowanie linii elementu Lion](./Images/lion_drawing.png)

```json
{
    "imageType": {
        "clipArtType": 2,
        "lineDrawingType": 1
    },
    "requestId": "6442dc22-476a-41c4-aa3d-9ceb15172f01",
    "metadata": {
        "height": 268,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Biały kwiat z zielonym tłem](./Images/flower.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "98437d65-1b05-4ab7-b439-7098b5dfdcbf",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z dokumentacją dotyczącą [analizy obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) , aby dowiedzieć się, jak wykrywać typy obrazów.
