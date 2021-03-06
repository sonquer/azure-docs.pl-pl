---
title: Metoda interfejs API tłumaczenia tekstu w usłudze Translator Languages
titleSuffix: Azure Cognitive Services
description: Język Metoda pobiera zestaw języków aktualnie obsługiwanych przez inne operacje interfejs API tłumaczenia tekstu w usłudze Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 37f70399e8125db559098869cdfffdf4533498d7
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73835828"
---
# <a name="translator-text-api-30-languages"></a>Interfejs API tłumaczenia tekstu w usłudze Translator 3,0: Języki

Pobiera zestaw języków aktualnie obsługiwanych przez inne operacje interfejs API tłumaczenia tekstu w usłudze Translator. 

## <a name="request-url"></a>Adres URL żądania

Wyślij żądanie `GET` do:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Parametry żądania przesłane na ciągu zapytania są następujące:

<table width="100%">
  <th width="20%">Parametr zapytania</th>
  <th>Opis</th>
  <tr>
    <td>wersja interfejsu API</td>
    <td><em>Wymagany parametr</em>.<br/>Wersja interfejsu API żądana przez klienta. Wartość musi być `3.0`.</td>
  </tr>
  <tr>
    <td>scope</td>
    <td>*Opcjonalny parametr*.<br/>Rozdzielana przecinkami lista nazw definiująca grupę języków do zwrócenia. Dozwolone nazwy grup to: `translation`, `transliteration` i `dictionary`. Jeśli żaden zakres nie zostanie określony, zwracane są wszystkie grupy, co jest równoznaczne z przekazywaniem `scope=translation,transliteration,dictionary`. Aby zdecydować, który zestaw obsługiwanych języków jest odpowiedni dla danego scenariusza, zobacz Opis [obiektu Response](#response-body).</td>
  </tr>
</table> 

Nagłówki żądań to:

<table width="100%">
  <th width="20%">Nagłówki</th>
  <th>Opis</th>
  <tr>
    <td>Zaakceptuj — język</td>
    <td>*Opcjonalny nagłówek żądania*.<br/>Język ciągów interfejsu użytkownika. Niektóre pola w odpowiedzi są nazwami języków lub nazw regionów. Ten parametr służy do definiowania języka, w którym są zwracane te nazwy. Język jest określony przez udostępnienie poprawnie sformułowanego znacznika języka BCP 47. Na przykład użyj wartości `fr`, aby zażądać nazw w języku francuskim, lub użyj `zh-Hant` wartości, aby zażądać nazw w języku chińskim tradycyjnym.<br/>Nazwy są udostępniane w języku angielskim, gdy nie określono języka docelowego lub lokalizacja jest niedostępna.
    </td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Opcjonalny nagłówek żądania*.<br/>Wygenerowany przez klienta identyfikator GUID służący do unikatowej identyfikacji żądania.</td>
  </tr>
</table> 

Uwierzytelnianie nie jest wymagane w celu pobrania zasobów językowych.

## <a name="response-body"></a>Treść odpowiedzi

Klient używa `scope` parametru zapytania, aby określić, które grupy języków są interesujące.

* `scope=translation` udostępnia Języki obsługujące tłumaczenie tekstu z jednego języka do innego języka;

* `scope=transliteration` zapewnia możliwości konwertowania tekstu w jednym języku z jednego skryptu na inny skrypt;

* `scope=dictionary` zawiera pary językowe, dla których operacje `Dictionary` zwracają dane.

Klient może pobrać kilka grup jednocześnie, określając rozdzieloną przecinkami listę nazw. Na przykład `scope=translation,transliteration,dictionary` zwróci obsługiwane języki dla wszystkich grup.

Pomyślna odpowiedź to obiekt JSON z jedną właściwością dla każdej z żądanych grup:

```json
{
    "translation": {
        //... set of languages supported to translate text (scope=translation)
    },
    "transliteration": {
        //... set of languages supported to convert between scripts (scope=transliteration)
    },
    "dictionary": {
        //... set of languages supported for alternative translations and examples (scope=dictionary)
    }
}
```

Wartość każdej właściwości jest następująca.

* `translation` Właściwość

  Wartość właściwości `translation` jest słownikiem par (klucz, wartość). Każdy klucz jest tagiem języka BCP 47. Klucz określa język, w którym można przetłumaczyć lub przetłumaczyć tekst. Wartość skojarzona z kluczem jest obiektem JSON z właściwościami opisującymi język:

  * `name`: Nazwa wyświetlana języka w ustawieniach regionalnych żądanych za pośrednictwem `Accept-Language` nagłówka.

  * `nativeName`: Nazwa wyświetlana języka w ustawieniach regionalnych natywnych dla tego języka.

  * `dir`: kierunkowość, która jest `rtl` dla języków pisanych od prawej do lewej lub `ltr` dla języków z lewej strony.

  Przykład:
          
  ```json
  {
    "translation": {
      ...
      "fr": {
        "name": "French",
        "nativeName": "Français",
        "dir": "ltr"
      },
      ...
    }
  }
  ```

* `transliteration` Właściwość

  Wartość właściwości `transliteration` jest słownikiem par (klucz, wartość). Każdy klucz jest tagiem języka BCP 47. Klucz określa język, dla którego tekst może być konwertowany z jednego skryptu na inny. Wartość skojarzona z kluczem jest obiektem JSON z właściwościami opisującymi język i obsługiwane skrypty:

  * `name`: Nazwa wyświetlana języka w ustawieniach regionalnych żądanych za pośrednictwem `Accept-Language` nagłówka.

  * `nativeName`: Nazwa wyświetlana języka w ustawieniach regionalnych natywnych dla tego języka.

  * `scripts`: lista skryptów do przekonwertowania. Każdy element listy `scripts` ma właściwości:

    * `code`: kod identyfikujący skrypt.

    * `name`: Nazwa wyświetlana skryptu w ustawieniach regionalnych żądanych za pośrednictwem `Accept-Language` header.

    * `nativeName`: Nazwa wyświetlana języka w ustawieniach regionalnych natywnych dla języka.

    * `dir`: kierunkowość, która jest `rtl` dla języków pisanych od prawej do lewej lub `ltr` dla języków z lewej strony.

    * `toScripts`: lista skryptów dostępnych do przekonwertowania tekstu na. Każdy element listy `toScripts` ma właściwości `code`, `name`, `nativeName`i `dir` zgodnie z wcześniejszym opisem.

  Przykład:

  ```json
  {
    "transliteration": {
      ...
      "ja": {
        "name": "Japanese",
        "nativeName": "日本語",
        "scripts": [
          {
            "code": "Jpan",
            "name": "Japanese",
            "nativeName": "日本語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Latn",
                "name": "Latin",
                "nativeName": "ラテン語",
                "dir": "ltr"
              }
            ]
          },
          {
            "code": "Latn",
            "name": "Latin",
            "nativeName": "ラテン語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Jpan",
                "name": "Japanese",
                "nativeName": "日本語",
                "dir": "ltr"
              }
            ]
          }
        ]
      },
      ...
    }
  }
  ```

* `dictionary` Właściwość

  Wartość właściwości `dictionary` jest słownikiem par (klucz, wartość). Każdy klucz jest tagiem języka BCP 47. Klucz identyfikuje język, dla którego są dostępne alternatywne tłumaczenia i zwrotne. Wartość jest obiektem JSON opisującym język źródłowy i Języki docelowe z dostępnymi tłumaczeniami:

  * `name`: Nazwa wyświetlana języka źródłowego w ustawieniach regionalnych żądanych za pośrednictwem `Accept-Language` nagłówka.

  * `nativeName`: Nazwa wyświetlana języka w ustawieniach regionalnych natywnych dla tego języka.

  * `dir`: kierunkowość, która jest `rtl` dla języków pisanych od prawej do lewej lub `ltr` dla języków z lewej strony.

  * `translations`: lista języków z tłumaczeniami alterative i przykłady dla zapytania wyrażone w języku źródłowym. Każdy element listy `translations` ma właściwości:

    * `name`: Nazwa wyświetlana języka docelowego w ustawieniach regionalnych żądanych za pośrednictwem `Accept-Language` nagłówka.

    * `nativeName`: Nazwa wyświetlana języka docelowego w ustawieniach regionalnych w języku docelowym.

    * `dir`: kierunkowość, która jest `rtl` dla języków pisanych od prawej do lewej lub `ltr` dla języków z lewej strony.
    
    * `code`: kod języka identyfikujący język docelowy.

  Przykład:

  ```json
  "es": {
    "name": "Spanish",
    "nativeName": "Español",
    "dir": "ltr",
    "translations": [
      {
        "name": "English",
        "nativeName": "English",
        "dir": "ltr",
        "code": "en"
      }
    ]
  },
  ```

Struktura obiektu Response nie ulegnie zmianie bez zmiany w wersji interfejsu API. W przypadku tej samej wersji interfejsu API lista dostępnych języków może ulec zmianie w miarę upływu czasu, ponieważ usługa Microsoft Translator ciągle rozszerza listę języków obsługiwanych przez jej usługi.

Lista obsługiwanych języków nie będzie często zmieniana. Aby zaoszczędzić przepustowość sieci i zwiększyć czas odpowiedzi, aplikacja kliencka powinna rozważyć użycie pamięci podręcznej i odpowiedniego znacznika jednostki (`ETag`). Następnie aplikacja kliencka może okresowo (na przykład co 24 godziny) wysyłać zapytania do usługi w celu pobrania najnowszego zestawu obsługiwanych języków. Przekazanie bieżącej wartości `ETag` w polu nagłówka `If-None-Match` pozwoli usłudze na optymalizację odpowiedzi. Jeśli zasób nie został zmodyfikowany, usługa zwróci kod stanu 304 i pustą treść odpowiedzi.

## <a name="response-headers"></a>Nagłówki odpowiedzi

<table width="100%">
  <th width="20%">Nagłówki</th>
  <th>Opis</th>
  <tr>
    <td>Element ETag</td>
    <td>Bieżąca wartość tagu Entity dla żądanych grup obsługiwanych języków. Aby kolejne żądania były bardziej wydajne, klient może wysłać wartość `ETag` w `If-None-Match` polu nagłówka.
    </td>
  </tr>
  <tr>
    <td>X-IdentyfikatorŻądania</td>
    <td>Wartość wygenerowana przez usługę w celu zidentyfikowania żądania. Służy do rozwiązywania problemów.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Kody stanu odpowiedzi

Oto możliwe kody stanu HTTP zwracane przez żądanie. 

<table width="100%">
  <th width="20%">Kod stanu</th>
  <th>Opis</th>
  <tr>
    <td>200</td>
    <td>Powodzenie.</td>
  </tr>
  <tr>
    <td>304</td>
    <td>Zasób nie został zmodyfikowany od wersji określonej przez nagłówki żądania `If-None-Match`.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Brakuje jednego z parametrów zapytania lub jest on nieprawidłowy. Popraw parametry żądania przed ponowną próbą.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Serwer odrzucił żądanie z powodu przekroczenia limitów żądań przez klienta.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Wystąpił nieoczekiwany błąd. Jeśli błąd będzie się powtarzać, zgłoś go z: Data i godzina błędu, identyfikator żądania z nagłówka odpowiedzi `X-RequestId`i identyfikator klienta z nagłówka żądania `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Serwer jest tymczasowo niedostępny. Ponów żądanie. Jeśli błąd będzie się powtarzać, zgłoś go z: Data i godzina błędu, identyfikator żądania z nagłówka odpowiedzi `X-RequestId`i identyfikator klienta z nagłówka żądania `X-ClientTraceId`.</td>
  </tr>
</table> 

Jeśli wystąpi błąd, żądanie zwróci także odpowiedź na błąd JSON. Kod błędu to 6-cyfrowy numer łączący 3-cyfrowy kod stanu HTTP, a następnie 3-cyfrowy numer do dalszej kategoryzacji błędu. Typowe kody błędów można znaleźć na [stronie odniesienia interfejs API tłumaczenia tekstu w usłudze translator v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Przykłady

Poniższy przykład pokazuje, jak pobrać języki obsługiwane w przypadku tłumaczenia tekstu.

```curl
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```
