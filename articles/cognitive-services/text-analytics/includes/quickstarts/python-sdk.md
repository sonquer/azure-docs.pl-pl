---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 02/14/2019
ms.author: aahi
ms.openlocfilehash: b553cd2bed68bc8df7241f4f843b32ec13b1a08a
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2020
ms.locfileid: "77372133"
---
<a name="HOLTop"></a>

#### <a name="version-30-previewtabversion-3"></a>[Wersja 3,0-Preview](#tab/version-3)

[dokumentacja referencyjna v3](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-textanalytics/1.0.0b2/azure.ai.textanalytics.html) | [kod źródłowy biblioteki v3](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [v3 (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [v3](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

#### <a name="version-21tabversion-2"></a>[Wersja 2,1](#tab/version-2)

[wersja 2 dokumentacja referencyjna](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [wersja 2 kod źródłowy](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [v2 pakiet (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [v2](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

---

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

## <a name="setting-up"></a>Konfigurowanie

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Po zainstalowaniu języka Python można zainstalować bibliotekę kliencką z:

#### <a name="version-30-previewtabversion-3"></a>[Wersja 3,0-Preview](#tab/version-3)

```console
pip install azure-ai-textanalytics
```

#### <a name="version-21tabversion-2"></a>[Wersja 2,1](#tab/version-2)

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

---

### <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

Utwórz nowy plik w języku Python i Utwórz zmienne dla punktu końcowego platformy Azure i klucza subskrypcji zasobu.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Model obiektów

#### <a name="version-30-previewtabversion-3"></a>[Wersja 3,0-Preview](#tab/version-3)

Klient analiza tekstu jest obiektem `TextAnalyticsClient`, który jest uwierzytelniany na platformie Azure przy użyciu klucza. Klient udostępnia kilka metod analizowania tekstu jako partii. 

Gdy tekst przetwarzania wsadowego jest wysyłany do interfejsu API jako lista `documents`, które są `dictionary` obiektów zawierających kombinację `id`, `text`i `language` atrybutów, w zależności od używanej metody. Atrybut `text` przechowuje tekst, który ma być analizowany w `language`źródłowym, a `id` może być dowolną wartością. 

Obiekt Response jest listą zawierającą informacje o analizie dla każdego dokumentu. 

#### <a name="version-21tabversion-2"></a>[Wersja 2,1](#tab/version-2)

Klient analiza tekstu jest obiektem [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) , który jest uwierzytelniany na platformie Azure przy użyciu klucza. Klient udostępnia kilka metod analizowania tekstu, w postaci pojedynczego ciągu lub partii. 

Tekst jest wysyłany do interfejsu API jako lista `documents`, które są `dictionary` obiektów zawierających kombinację atrybutów `id`, `text`i `language` w zależności od używanej metody. Atrybut `text` przechowuje tekst, który ma być analizowany w `language`źródłowym, a `id` może być dowolną wartością. 

---

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań za pomocą biblioteki klienta analiza tekstu dla języka Python:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [analiza tonacji](#sentiment-analysis)
* [Wykrywanie języka](#language-detection)
* [Rozpoznawanie jednostek nazwanych](#named-entity-recognition-ner) 
* [Łączenie jednostek](#entity-linking)
* [Wyodrębnianie kluczowych fraz](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

#### <a name="version-30-previewtabversion-3"></a>[Wersja 3,0-Preview](#tab/version-3)

Utwórz funkcję, aby utworzyć wystąpienie obiektu `TextAnalyticsClient` przy użyciu `key` i `endpoint` utworzonego powyżej. Następnie Utwórz nowego klienta. 

```python
from azure.ai.textanalytics import TextAnalyticsClient, TextAnalyticsApiKeyCredential

def authenticate_client():
    ta_credential = TextAnalyticsApiKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

#### <a name="version-21tabversion-2"></a>[Wersja 2,1](#tab/version-2)

[!code-python[imports statements](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=imports)]

Utwórz funkcję, aby utworzyć wystąpienie obiektu `TextAnalyticsClient` przy użyciu `key` i `endpoint` utworzonego powyżej. Następnie Utwórz nowego klienta. 

[!code-python[version 2 authentication](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=authentication)]

--- 

## <a name="sentiment-analysis"></a>Analiza tonacji

#### <a name="version-30-previewtabversion-3"></a>[Wersja 3,0-Preview](#tab/version-3)

Utwórz nową funkcję o nazwie `sentiment_analysis_example()`, która przyjmuje klienta jako argument, a następnie wywołuje funkcję `analyze_sentiment()`. Zwrócony obiekt odpowiedzi będzie zawierać etykietę tonacji i ocenę całego dokumentu wejściowego, a także analizę tonacji dla każdego zdania.


```python
def sentiment_analysis_example(client):

    document = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(inputs=document)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.3f}; neutral={1:.3f}; negative={2:.3f} \n".format(
        response.sentiment_scores.positive,
        response.sentiment_scores.neutral,
        response.sentiment_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("[Offset: {}, Length: {}]".format(sentence.offset, sentence.length))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.3f}\nNeutral={1:.3f}\nNegative={2:.3f}\n".format(
            sentence.sentiment_scores.positive,
            sentence.sentiment_scores.neutral,
            sentence.sentiment_scores.negative,
        ))

            
sentiment_analysis_example(client)
```

### <a name="output"></a>Dane wyjściowe

```console
Document Sentiment: positive
Overall scores: positive=1.000; neutral=0.000; negative=0.000 

[Offset: 0, Length: 30]
Sentence 1 sentiment: positive
Sentence score:
Positive=1.000
Neutral=0.000
Negative=0.000

[Offset: 31, Length: 30]
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.210
Neutral=0.770
Negative=0.020
```

#### <a name="version-21tabversion-2"></a>[Wersja 2,1](#tab/version-2)

Uwierzytelnij obiekt klienta i wywołaj funkcję [tonacji ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) . Wykonaj iterację w wynikach i wydrukuj identyfikatory każdego dokumentu oraz tonacji ocenę. Wynik zbliżony do 0 wskazuje negatywną tonacji, natomiast wynik zbliżony do 1 wskazuje pozytywny tonacji.

[!code-python[sentiment analysis](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=sentimentAnalysis)]

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

---

## <a name="language-detection"></a>Wykrywanie języka

#### <a name="version-30-previewtabversion-3"></a>[Wersja 3,0-Preview](#tab/version-3)

Utwórz nową funkcję o nazwie `language_detection_example()`, która przyjmuje klienta jako argument, a następnie wywołuje funkcję `detect_language()`. Zwrócony obiekt odpowiedzi będzie zawierać wykryty język w `primary_language`, jeśli się powiedzie, i `error`, jeśli nie.

> [!Tip]
> W niektórych przypadkach może być trudno odróżnić Języki w oparciu o dane wejściowe. Można użyć parametru `country_hint`, aby określić 2-literowy kod kraju. Domyślnie interfejs API używa "US" jako domyślnego countryHint, aby usunąć to zachowanie, można zresetować ten parametr, ustawiając tę wartość na pusty ciąg `country_hint : ""`. 

```python
def language_detection_example(client):
    try:
        document = ["Ce document est rédigé en Français."]
        response = client.detect_language(inputs = document, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>Dane wyjściowe

```console
Language:  French
```

#### <a name="version-21tabversion-2"></a>[Wersja 2,1](#tab/version-2)

Korzystając z utworzonego wcześniej klienta, wywołaj [detect_language ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) i Pobierz wynik. Następnie można wykonać iterację w wynikach i wydrukować identyfikator każdego dokumentu oraz pierwszy zwracany język.

[!code-python[language detection](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=languageDetection)]


### <a name="output"></a>Dane wyjściowe

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Rozpoznawanie jednostek nazwanych (NER)

#### <a name="version-30-previewtabversion-3"></a>[Wersja 3,0-Preview](#tab/version-3)

> [!NOTE]
> W wersji `3.0-preview`:
> * NER obejmuje oddzielne metody wykrywania informacji osobistych. 
> * Łączenie jednostek to oddzielne żądanie niż NER.

Utwórz nową funkcję o nazwie `entity_recognition_example`, która przyjmuje klienta jako argument, a następnie wywołuje funkcję `recognize_entities()` i wykonuje iterację przez wyniki. Zwrócony obiekt odpowiedzi będzie zawierać listę wykrytych jednostek w `entity`, jeśli się powiedzie, i `error`, jeśli nie. Dla każdej wykrytej jednostki Wydrukuj jej kategorię i podkategorię, jeśli istnieje.

```python
def entity_recognition_example(client):

    try:
        document = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(inputs= document)[0]

        print("Named Entities:\n")
        for entity in result.entities:
                print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                      "\n\tOffset: \t", entity.offset, "\tLength: \t", entity.offset, 
                      "\tConfidence Score: \t", round(entity.score, 3), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Dane wyjściowe

```console
Named Entities:

    Text:    Seattle    Category:    Location   SubCategory:     GPE 
    Offset:      26     Length:      26     Confidence Score:    0.92 

    Text:    last week  Category:    DateTime   SubCategory:     DateRange 
    Offset:      34     Length:      34     Confidence Score:    0.8 
```

## <a name="using-ner-to-detect-personal-information"></a>Wykrywanie informacji osobistych za pomocą NER

Utwórz nową funkcję o nazwie `entity_pii_example()`, która przyjmuje klienta jako argument, a następnie wywołuje funkcję `recognize_pii_entities()` i pobiera wynik. Następnie wykonaj iterację w wynikach i wydrukuj jednostki.

```python
def entity_pii_example(client):

        document = ["Insurance policy for SSN on file 123-12-1234 is here by approved."]


        result = client.recognize_pii_entities(inputs= document)[0]
        
        print("Personally Identifiable Information Entities: ")
        for entity in result.entities:
            print("\tText: ",entity.text,"\tCategory: ", entity.category,"\tSubCategory: ", entity.subcategory)
            print("\t\tOffset: ", entity.offset, "\tLength: ", entity.length, "\tScore: {0:.3f}".format(entity.score), "\n")
        
entity_pii_example(client)
```

### <a name="output"></a>Dane wyjściowe

```console
Personally Identifiable Information Entities: 
    Text:  123-12-1234  Category:  U.S. Social Security Number (SSN)    SubCategory:  None
        Offset:  33     Length:  11     Score: 0.850 
```


## <a name="entity-linking"></a>Łączenie jednostek

Utwórz nową funkcję o nazwie `entity_linking_example()`, która przyjmuje klienta jako argument, a następnie wywołuje funkcję `recognize_linked_entities()` i wykonuje iterację przez wyniki. Zwrócony obiekt odpowiedzi będzie zawierać listę wykrytych jednostek w `entities`, jeśli się powiedzie, i `error`, jeśli nie. Ponieważ połączone jednostki są jednoznacznie identyfikowane, wystąpienia tej samej jednostki są pogrupowane pod obiektem `entity` jako lista obiektów `match`.

```python
def entity_linking_example(client):

    try:
        document = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(inputs= document)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tScore: {0:.3f}".format(match.score), "\tOffset: ", match.offset, 
                      "\tLength: {}\n".format(match.length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Dane wyjściowe

```console
Linked Entities:

    Name:  Altair 8800  Id:  Altair 8800    Url:  https://en.wikipedia.org/wiki/Altair_8800 
    Data Source:  Wikipedia
    Matches:
        Text: Altair 8800
        Score: 0.777    Offset:  125    Length: 11

    Name:  Bill Gates   Id:  Bill Gates     Url:  https://en.wikipedia.org/wiki/Bill_Gates 
    Data Source:  Wikipedia
    Matches:
        Text: Bill Gates
        Score: 0.555    Offset:  25     Length: 10

        Text: Gates
        Score: 0.555    Offset:  179    Length: 5

    Name:  Paul Allen   Id:  Paul Allen     Url:  https://en.wikipedia.org/wiki/Paul_Allen 
    Data Source:  Wikipedia
    Matches:
        Text: Paul Allen
        Score: 0.533    Offset:  40     Length: 10

    Name:  Microsoft    Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft 
    Data Source:  Wikipedia
    Matches:
        Text: Microsoft
        Score: 0.469    Offset:  0  Length: 9

        Text: Microsoft
        Score: 0.469    Offset:  168    Length: 9

    Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4 
    Data Source:  Wikipedia
    Matches:
        Text: April 4
        Score: 0.248    Offset:  54     Length: 7

    Name:  BASIC    Id:  BASIC  Url:  https://en.wikipedia.org/wiki/BASIC 
    Data Source:  Wikipedia
    Matches:
        Text: BASIC
        Score: 0.281    Offset:  98     Length: 5

```

#### <a name="version-21tabversion-2"></a>[Wersja 2,1](#tab/version-2)

> [!NOTE]
> W wersji 2,1, konsolidacja jednostki jest uwzględniona w odpowiedzi NER.

Za pomocą utworzonego wcześniej klienta wywołaj funkcję [Entities ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) i Pobierz wynik. Następnie można wykonać iterację w wynikach i wydrukować identyfikator każdego dokumentu oraz jednostki zawarte w nim.

[!code-python[Entity recognition](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=entityRecognition)]

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

---

## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz


#### <a name="version-30-previewtabversion-3"></a>[Wersja 3,0-Preview](#tab/version-3)

Utwórz nową funkcję o nazwie `key_phrase_extraction_example()`, która przyjmuje klienta jako argument, a następnie wywołuje funkcję `extract_key_phrases()`. Wynik będzie zawierać listę wykrytych fraz kluczy w `key_phrases`, jeśli się powiedzie, i `error`, jeśli nie. Drukuj wszystkie wykryte frazy kluczy.

```python
def key_phrase_extraction_example(client):

    try:
        document = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(inputs= document)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>Dane wyjściowe

```console
    Key Phrases:
         cat
         veterinarian
```

#### <a name="version-21tabversion-2"></a>[Wersja 2,1](#tab/version-2)

Za pomocą utworzonego wcześniej klienta wywołaj funkcję [key_phrases ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) i uzyskaj wynik. Następnie można wykonać iterację w wynikach i wydrukować identyfikator każdego dokumentu oraz zawarte w nim kluczowe frazy.

[!code-python[key phrase extraction](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=keyPhrases)]


### <a name="output"></a>Dane wyjściowe

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```

---