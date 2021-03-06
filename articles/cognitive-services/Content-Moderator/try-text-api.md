---
title: Umiarkowany tekst przy użyciu interfejsu API moderowania tekstu — Content Moderator
titleSuffix: Azure Cognitive Services
description: Moderowanie tekstu dysku testowego za pomocą interfejsu API moderowania tekstu w konsoli online.
services: cognitive-services
author: PatrickFarley
ms.author: pafarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: e0930558f31b27a77fa2cd6b44fcea2fe9091086
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538824"
---
# <a name="moderate-text-from-the-api-console"></a>Umiarkowany tekst z konsoli interfejsu API

Użyj [interfejsu API moderowania tekstu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) w usłudze Azure Content moderator, aby przeskanować zawartość tekstową pod kątem niewulgarności i porównać ją z niestandardowymi i udostępnionymi listami.

## <a name="get-your-api-key"></a>Pobierz klucz interfejsu API

Aby można było przetestować interfejs API w konsoli online, musisz mieć swój klucz subskrypcji. Ta lokalizacja znajduje się na karcie **Ustawienia** w polu **OCP-APIM-Subscription-Key** . Aby uzyskać więcej informacji, zobacz [Omówienie](overview.md).

## <a name="navigate-to-the-api-reference"></a>Przejdź do odwołania do interfejsu API

Przejdź do [dokumentacji interfejsu API moderowania tekstu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  Zostanie otwarta strona **ekran tekstu** .

## <a name="open-the-api-console"></a>Otwórz konsolę interfejsu API

W obszarze **Otwórz konsolę testowania interfejsu API**wybierz region, który najlepiej opisuje lokalizację. 

  ![Wybór obszaru strony ekranu tekstu](images/test-drive-region.png)

  Zostanie otwarta konsola interfejsu API **tekstu** .

## <a name="select-the-inputs"></a>Wybierz dane wejściowe

### <a name="parameters"></a>Parametry

Wybierz parametry zapytania, które mają być używane na ekranie tekstowym. W tym przykładzie Użyj wartości domyślnej dla **języka**. Możesz również pozostawić to pole puste, ponieważ operacja automatycznie wykryje prawdopodobnie język w ramach jego wykonywania.

> [!NOTE]
> W polu parametr **języka** Przypisz `eng` lub pozostaw to pole puste, aby wyświetlić odpowiedź **klasyfikacji** z obsługą maszyn (funkcja w wersji zapoznawczej). **Ta funkcja obsługuje tylko język angielski**.
>
> W przypadku wykrywania **warunków wulgarnych** Użyj [kodu ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) dla obsługiwanych języków wymienionych w tym artykule lub pozostaw to pole puste.

W przypadku funkcji **Autokorekta**, dane **osobowe**i **klasyfikowanie (wersja zapoznawcza)** wybierz pozycję **prawda**. Pozostaw puste pole **ListId** .

  ![Parametry zapytania konsoli ekranowej](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Typ zawartości

W **polu Typ zawartości**wybierz typ zawartości, która ma być wykorzystana. Na potrzeby tego przykładu Użyj domyślnego typu zawartości **tekst/zwykły** . W polu **OCP-APIM-Subscription-Key** wprowadź swój klucz subskrypcji.

### <a name="sample-text-to-scan"></a>Przykładowy tekst do skanowania

W polu **treść żądania** wprowadź tekst. W poniższym przykładzie przedstawiono zamierzone literówki w tekście.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

## <a name="analyze-the-response"></a>Analizowanie odpowiedzi

W poniższej odpowiedzi przedstawiono różne informacje z interfejsu API. Zawiera potencjalne nieżywotność, dane osobowe, klasyfikację (wersja zapoznawcza) i automatycznie poprawioną wersję.

> [!NOTE]
> Funkcja klasyfikacji oparta na maszynach jest w wersji zapoznawczej i obsługuje tylko język angielski.

```json
{"OriginalText":"Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.\r\nThese are all UK phone numbers: +44 123 456 7890 or 0234 567 8901 or 0456 789 0123.\r\nAlso, 999-99-9999 looks like a social security number (SSN).",
"NormalizedText":"Is this a grabage or crap email abcdef@ abcd. com, phone: 4255550111, IP: 255. 255. 255. 255, 1234 Main Boulevard, Panapolis WA 96555. \r\nThese are all UK phone numbers: +44 123 456 7890 or 0234 567 8901 or 0456 789 0123. \r\nAlso, 999- 99- 9999 looks like a social security number ( SSN) .",
"Misrepresentation":null,
"PII":{  
  "Email":[  
    {  
      "Detected":"abcdef@abcd.com",
      "SubType":"Regular",
      "Text":"abcdef@abcd.com",
      "Index":32
    }
  ],
  "IPA":[  
    {  
      "SubType":"IPV4",
      "Text":"255.255.255.255",
      "Index":72
    }
  ],
  "Phone":[  
    {  
      "CountryCode":"US",
      "Text":"4255550111",
      "Index":56
    },
    {  
      "CountryCode":"US",
      "Text":"425 555 0111",
      "Index":211
    },
    {  
      "CountryCode":"UK",
      "Text":"+44 123 456 7890",
      "Index":207
    },
    {  
      "CountryCode":"UK",
      "Text":"0234 567 8901",
      "Index":227
    },
    {  
      "CountryCode":"UK",
      "Text":"0456 789 0123",
      "Index":244
    }
  ],
  "Address":[  
    {  
      "Text":"1234 Main Boulevard, Panapolis WA 96555",
      "Index":89
    }
  ],
  "SSN":[  
    {  
      "Text":"999999999",
      "Index":56
    },
    {  
      "Text":"999-99-9999",
      "Index":266
    }
  ]
},
"Classification":{  
  "ReviewRecommended":true,
  "Category1":{  
    "Score":1.5113095059859916E-06
  },
  "Category2":{  
    "Score":0.12747249007225037
  },
  "Category3":{  
    "Score":0.98799997568130493
  }
},
"Language":"eng",
"Terms":[  
  {  
    "Index":21,
    "OriginalIndex":21,
    "ListId":0,
    "Term":"crap"
  }
],
"Status":{  
  "Code":3000,
  "Description":"OK",
  "Exception":null
},
"TrackingId":"2eaa012f-1604-4e36-a8d7-cc34b14ebcb4"
}
```

Szczegółowe wyjaśnienie wszystkich sekcji odpowiedzi JSON można znaleźć w przewodniku koncepcyjnym [moderowania tekstu](text-moderation-api.md) .

## <a name="next-steps"></a>Następne kroki

Użyj interfejsu API REST w kodzie lub postępuj zgodnie z [przewodnikiem Szybki Start dla zestawu .NET SDK](dotnet-sdk-quickstart.md) , aby przeprowadzić integrację z aplikacją.
