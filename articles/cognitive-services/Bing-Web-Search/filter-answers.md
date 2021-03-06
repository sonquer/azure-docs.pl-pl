---
title: Jak filtrować wyniki wyszukiwania — interfejs API wyszukiwania w sieci Web Bing
titleSuffix: Azure Cognitive Services
description: Można filtrować typy odpowiedzi, które obejmują usługi Bing w odpowiedzi (na przykład obrazy, filmy wideo i wiadomości) przy użyciu parametru zapytania "responseFilter".
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: scottwhi
ms.openlocfilehash: 6fa022f181e2061c6a7f3e08d1f2f501ddd9cac3
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111419"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>Filtrowanie odpowiedzi uwzględnionych w odpowiedzi wyszukiwania  

Podczas wykonywania zapytania w sieci Web, Bing zwraca całą zawartość znalezioną dla wyszukiwania. Na przykład, jeśli zapytanie wyszukiwania ma wartość "dinghies +", odpowiedź może zawierać następujące odpowiedzi:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43C...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    },
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA5CA6464E5D...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [...]
        }
    }
}    
```

## <a name="query-parameters"></a>Parametry zapytania

Aby odfiltrować odpowiedzi zwrócone przez usługę Bing, Użyj poniższych parametrów zapytania podczas wywoływania interfejsu API.  

### <a name="responsefilter"></a>ResponseFilter

Można filtrować typy odpowiedzi, które obejmują usługi Bing w odpowiedzi (na przykład obrazy, filmy wideo i wiadomości) przy użyciu parametru zapytania [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) , który jest rozdzielaną przecinkami listą odpowiedzi. Odpowiedź zostanie uwzględniona w odpowiedzi, jeśli Bing znajdzie odpowiednią zawartość. 

Aby wykluczyć określone odpowiedzi z odpowiedzi, takie jak obrazy, poprzedź znak `-` do typu odpowiedzi. Na przykład:

```
&responseFilter=-images,-videos
```

Poniżej pokazano, jak za pomocą `responseFilter` zażądać obrazów, filmów wideo i wiadomości dinghies. Podczas kodowania ciągu zapytania przecinki zmieniają się na% 2C.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Poniżej przedstawiono odpowiedź na poprzednie zapytanie. Ponieważ usługa Bing nie znalazła odpowiednich wyników wideo i wiadomości, odpowiedź nie jest uwzględniana.

```json
{
    "_type" : "SearchResponse",
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3AD78B183C56456C...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            }]
        }
    }
}
```

Mimo że usługa Bing nie zwraca wideo i wiadomości w poprzedniej odpowiedzi, nie oznacza to, że zawartość wideo i wiadomości nie istnieje. Oznacza to po prostu, że strona nie została uwzględniona. Jeśli jednak [Strona](./paging-webpages.md) zawiera więcej wyników, na kolejnych stronach prawdopodobnie zostaną uwzględnione. Ponadto, jeśli wywołasz [interfejs api wyszukiwanie wideo](../bing-video-search/search-the-web.md) i punkty końcowe [interfejsu API wyszukiwanie wiadomości](../bing-news-search/search-the-web.md) bezpośrednio, odpowiedź prawdopodobnie będzie zawierać wyniki.

Nie zaleca się używania `responseFilter`, aby uzyskać wyniki z pojedynczego interfejsu API. Jeśli chcesz uzyskać zawartość z pojedynczego interfejsu API Bing, wywołaj ten interfejs API bezpośrednio. Na przykład, aby odbierać tylko obrazy, Wyślij żądanie do punktu końcowego interfejsu API wyszukiwanie obrazów, `https://api.cognitive.microsoft.com/bing/v7.0/images/search` lub jeden z innych punktów końcowych [obrazów](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#endpoints) . Wywołanie pojedynczego interfejsu API jest ważne nie tylko ze względu na wydajność, ale ponieważ interfejsy API specyficzne dla zawartości zapewniają bogatsze wyniki. Można na przykład użyć filtrów, które nie są dostępne dla interfejsu API wyszukiwanie w sieci Web, aby przefiltrować wyniki.  

### <a name="site"></a>lokacji

Aby uzyskać wyniki wyszukiwania z określonej domeny, należy uwzględnić parametr zapytania `site:` w ciągu zapytania.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> W zależności od zapytania, jeśli używasz operatora kwerendy `site:`, istnieje możliwość, że odpowiedź może zawierać zawartość dla dorosłych niezależnie od ustawienia [bezpieczne wyszukiwanie](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch) . Operatora `site:` używaj tylko wtedy, gdy znasz zawartość witryny i w swoim scenariuszu uwzględniasz możliwość pojawienia się zawartości dla dorosłych.

### <a name="freshness"></a>Aktualność

Aby ograniczyć wyniki odpowiedzi sieci Web do stron internetowych, które zostały odnalezione przez usługę Bing w określonym przedziale czasu, ustaw parametr zapytania [Aktualności](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#freshness) na jedną z następujących wartości bez uwzględniania wielkości liter:

* `Day` — zwraca strony sieci Web odnalezione przez usługę Bing w ciągu ostatnich 24 godzin
* `Week` — zwraca strony sieci Web odnalezione przez usługę Bing w ciągu ostatnich 7 dni
* `Month` — zwraca strony sieci Web, które zostały odnalezione w ciągu ostatnich 30 dni

Możesz również ustawić ten parametr na niestandardowy zakres dat w formularzu, `YYYY-MM-DD..YYYY-MM-DD`. 

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-01..2019-05-30`

Aby ograniczyć wyniki do pojedynczej daty, ustaw parametr Aktualności na określoną datę:

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-04`

Wyniki mogą obejmować strony sieci Web, które wykraczają poza określony czas, jeśli liczba stron sieci Web, które pasują do kryteriów filtrowania, jest mniejsza niż liczba żądanych stron internetowych (lub domyślna liczba zwracanych przez usługę Bing).

## <a name="limiting-the-number-of-answers-in-the-response"></a>Ograniczanie liczby odpowiedzi w odpowiedzi

Bing może zwracać wiele typów odpowiedzi w odpowiedzi JSON. Na przykład w przypadku wysyłania zapytań do usługi *dinghies*, Bing może zwracać `webpages`, `images`, `videos`i `relatedSearches`.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "relatedSearches" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Aby ograniczyć liczbę odpowiedzi zwracanych przez usługę Bing do dwóch pierwszych odpowiedzi (stron sieci Web i obrazów), ustaw parametr zapytania [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) na 2.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Odpowiedź zawiera tylko `webPages` i `images`.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "rankingResponse" : {...}
}
```

Jeśli dodasz `responseFilter` parametr zapytania do poprzedniego zapytania i ustawisz go na Webpages i News, odpowiedź będzie zawierać tylko strony sieci Web, ponieważ wiadomości nie są klasyfikowane.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "rankingResponse" : {...}
}
```

## <a name="promoting-answers-that-are-not-ranked"></a>Promowanie odpowiedzi, które nie są klasyfikowane

Jeśli najwyższej rangi odpowiedzi dla zapytania są zwracane przez usługę Bing, są to strony sieci Web, obrazy, wideo i relatedSearches, odpowiedzi będą zawierać takie odpowiedzi. Jeśli ustawisz [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) na dwa (2), Bing zwróci dwie pierwsze odpowiedzi w rankingu: strony sieci Web i obrazy. Jeśli chcesz, aby w usłudze Bing umieścić obrazy i filmy wideo w odpowiedzi, określ parametr [podwyższanie poziomu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) zapytania i ustaw go na obrazy i wideo.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Poniżej znajduje się odpowiedź na powyższe żądanie. Bing zwraca dwie pierwsze odpowiedzi, strony sieci Web i obrazy oraz promuje wideo do odpowiedzi.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailiing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

W przypadku skonfigurowania `promote` do wiadomości, odpowiedź nie obejmuje odpowiedzi na wiadomość, ponieważ nie jest to propozycja odpowiedzi&mdash;można podwyższyć poziom odpowiedzi na ranking.

Odpowiedzi, które chcesz podwyższyć, nie są wliczane do limitu `answerCount`. Jeśli na przykład w rankingu odpowiedzi są wiadomości, obrazy i wideo, a dla `answerCount` wartość 1 i `promote` do wiadomości, odpowiedź zawiera wiadomości i obrazy. Jeśli jednak rankingowe odpowiedzi to wideo, obrazy i wiadomości, odpowiedź zawiera wideo i wiadomości.

`promote` można używać tylko wtedy, gdy określisz parametr `answerCount` Query.
