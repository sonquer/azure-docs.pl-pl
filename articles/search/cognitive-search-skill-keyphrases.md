---
title: wyodrębnianie kluczowych fraz umiejętności poznawcze
titleSuffix: Azure Cognitive Search
description: Oblicza tekst niestrukturalny, a dla każdego rekordu zwraca listę kluczowych fraz w potoku wzbogacenia AI na platformie Azure Wyszukiwanie poznawcze.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ccdd25d82af2b4893260af18dac818816d9e4579
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791973"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>wyodrębnianie kluczowych fraz umiejętności poznawcze

Umiejętność **wyodrębnianie kluczowych fraz** oblicza niestrukturalny tekst, a dla każdego rekordu zwraca listę fraz kluczowych. Ta umiejętność używa modeli uczenia maszynowego zapewnianych przez [Analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) w Cognitive Services.

Ta funkcja jest przydatna, jeśli trzeba szybko identyfikować główne punkty rozmowy w rekordzie. Na przykład w przypadku tekstu wejściowego "żywność została deliciousa i wystąpiła wspaniałe zatrudnienie" usługa zwraca "żywność" i "wspaniałe personel".

> [!NOTE]
> Podczas rozszerzania zakresu przez zwiększenie częstotliwości przetwarzania, Dodawanie większej liczby dokumentów lub Dodawanie algorytmów AI, należy [dołączyć Cognitive Services rozliczanego zasobu](cognitive-search-attach-cognitive-services.md). Opłaty naliczane podczas wywoływania interfejsów API w Cognitive Services oraz do wyodrębniania obrazów w ramach etapu łamania dokumentu w usłudze Azure Wyszukiwanie poznawcze. Nie są naliczane opłaty za Wyodrębnianie tekstu z dokumentów.
>
> Do wykonania wbudowanych umiejętności są naliczane opłaty za istniejące [Cognitive Services cena płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/details/cognitive-services/)użyciem. Cennik wyodrębniania obrazów został opisany na [stronie cennika usługi Azure wyszukiwanie poznawcze](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft. umiejętności. Text. KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Limity danych
Maksymalny rozmiar rekordu powinien składać się z 50 000 znaków mierzonych przez [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Jeśli konieczne jest rozbicie danych przed wysłaniem ich do wyodrębniania kluczowych fraz, rozważ użycie [umiejętności podziału tekstu](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry umiejętności

W parametrach jest rozróżniana wielkość liter.

| Dane wejściowe                | Opis |
|---------------------|-------------|
| defaultLanguageCode | Obowiązkowe Kod języka, który ma zostać zastosowany do dokumentów, które nie określają jawnie języka.  Jeśli kod języka domyślnego nie zostanie określony, jako domyślny kod języka zostanie użyty język angielski (EN). <br/> Zapoznaj się [z pełną listą obsługiwanych języków](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages). |
| maxKeyPhraseCount   | Obowiązkowe Maksymalna liczba kluczowych fraz do wygenerowania. |

## <a name="skill-inputs"></a>Dane wejściowe kwalifikacji

| Dane wejściowe     | Opis |
|--------------------|-------------|
| tekst | Tekst do analizy.|
| languageCode  |  Ciąg wskazujący język rekordów. Jeśli ten parametr nie jest określony, kod języka domyślnego będzie używany do analizowania rekordów. <br/>Zapoznaj się [z pełną listą obsługiwanych języków](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)|

##  <a name="sample-definition"></a>Definicja Przykładowa

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      },
      {
        "name": "languageCode",
        "source": "/document/languagecode" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
      }
    ]
  }
```

##  <a name="sample-input"></a>Przykładowe dane wejściowe

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
             "language": "en"
           }
      }
    ]
```


##  <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "keyPhrases": 
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
           }
      }
    ]
}
```


## <a name="errors-and-warnings"></a>Błędy i ostrzeżenia
Jeśli podano nieobsługiwany kod języka, zostanie wygenerowany błąd i frazy kluczy nie są wyodrębniane.
Jeśli tekst jest pusty, zostanie wygenerowane ostrzeżenie.
Jeśli tekst jest większy niż 50 000 znaków, przeanalizowane zostaną tylko pierwsze 50 000 znaki i zostanie wygenerowane ostrzeżenie.

## <a name="see-also"></a>Zobacz także

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
