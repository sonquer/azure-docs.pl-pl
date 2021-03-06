---
title: Wykrywanie marki — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: W tym artykule omówiono wyspecjalizowany tryb wykrywania obiektów; Wykrywanie marki i/lub logo przy użyciu interfejs API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: pafarley
ms.openlocfilehash: 117beca1284f28c75c1ac772425423f732b8a236
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718631"
---
# <a name="detect-popular-brands-in-images"></a>Wykrywanie popularnych marek w obrazach

Wykrywanie marki to wyspecjalizowany tryb [wykrywania obiektów](concept-object-detection.md) , który używa bazy danych tysięcy globalnych logo do identyfikowania komercyjnych marek w obrazach lub wideo. Za pomocą tej funkcji można na przykład ustalać, które marki są najpopularniejsze w mediach społecznościowych lub najpowszechniej promowane za pomocą lokowania produktów w mediach.

Usługa przetwarzanie obrazów wykrywa, czy istnieją znaki logo marki w danym obrazie; Jeśli tak, zwraca nazwę marki, wynik pewności i współrzędne obwiedni wokół logo.

Wbudowana baza danych logo obejmuje popularne marki w przypadku urządzeń elektronicznych, odzieżowych i innych. Jeśli okaże się, że dana marka nie została wykryta przez usługę przetwarzanie obrazów, można lepiej obsłużyć tworzenie i uczenie własnego detektora logo przy użyciu usługi [Custom Vision](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) .

## <a name="brand-detection-example"></a>Przykład wykrywania marki

Poniższe odpowiedzi JSON pokazują, jakie przetwarzanie obrazów zwracane podczas wykrywania marek w przykładowych obrazach.

![Czerwona koszula z etykietą i logo firmy Microsoft](./Images/red-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":20,
         "y":97,
         "w":62,
         "h":52
      }
   }
]
```

W niektórych przypadkach detektor marki spowoduje pobranie zarówno obrazu logo, jak i nazwy marki oznaczonej jako dwa oddzielne logo.

![Szary sweatshirt z etykietą i logo firmy Microsoft](./Images/gray-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":106,
         "w":55,
         "h":46
      }
   },
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":86,
         "w":202,
         "h":63
      }
   }
]
```

## <a name="use-the-api"></a>Używanie interfejsu API

Funkcja wykrywania marki jest częścią usługi [Analizowanie obrazu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) interfejsu API. Ten interfejs API można wywołać za pomocą natywnego zestawu SDK lub wywołań REST. Uwzględnij `Brands` w parametrze zapytania **visualFeatures** . Po otrzymaniu pełnej odpowiedzi JSON należy po prostu przeanalizować ciąg dla zawartości sekcji `"brands"`.

* [Szybki Start: przetwarzanie obrazów zestawu .NET SDK](./quickstarts-sdk/csharp-sdk.md)
* [Szybki Start: analizowanie obrazu (interfejs API REST)](./quickstarts/csharp-analyze.md)
