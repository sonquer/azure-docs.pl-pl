---
title: 'Szybki Start: wyszukiwanie filmów wideo przy użyciu zestawu SDK dla środowiska Node. js-wyszukiwanie wideo Bing'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki start, aby wysyłać żądania wyszukiwania wideo za pomocą zestawu SDK wyszukiwania wideo Bing dla języka Node.js.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 10d59da26c4ad583e3cb80b7d5cfc0d569f83ac1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75382620"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-nodejs"></a>Szybki Start: wykonywanie wyszukiwania wideo przy użyciu zestawu SDK wyszukiwanie wideo Bing dla środowiska Node. js

Użyj tego przewodnika Szybki start, aby rozpocząć wyszukiwanie wiadomości za pomocą zestawu SDK wyszukiwania wiadomości Bing dla języka Node.js. Chociaż wyszukiwanie wideo Bing ma interfejs API REST zgodny z większością języków programowania, zestaw SDK umożliwia łatwe integrowanie usługi z Twoimi aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js). Zawiera on więcej adnotacji i funkcji.

## <a name="prerequisites"></a>Wymagania wstępne

- [Node.js](https://www.nodejs.org/)

Aby skonfigurować aplikację konsolową przy użyciu zestawu SDK wyszukiwania wideo Bing:
* Uruchom polecenie `npm install ms-rest-azure` w środowisku programistycznym.
* Uruchom polecenie `npm install azure-cognitiveservices-videosearch` w środowisku programistycznym.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik JavaScript w swoim ulubionym środowisku IDE lub edytorze, a następnie dodaj instrukcję `require()` na potrzeby zestawu SDK wyszukiwania wideo Bing oraz moduł `CognitiveServicesCredentials`. Utwórz zmienną dla klucza subskrypcji. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
    ```

2. Utwórz wystąpienie obiektu `CognitiveServicesCredentials`, korzystając z klucza. Następnie użyj go do utworzenia wystąpienia klienta wyszukiwania wideo.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let client = new VideoSearchAPIClient(credentials);
    ```

## <a name="send-the-search-request"></a>Wysyłanie żądania wyszukiwania

1. Użyj funkcji `client.videosOperations.search()`, aby wysłać żądanie wyszukiwania do interfejsu API wyszukiwania wideo Bing. Jeśli zostaną zwrócone wyniki wyszukiwania, użyj funkcji `.then()`, aby je zarejestrować.
    
    ```javascript
    client.videosOperations.search('Interstellar Trailer').then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie internetowej aplikacji jednostronicowej](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Zobacz także 

* [Co to jest interfejs API wyszukiwania wideo Bing?](../overview.md)
* [Cognitive services .NET SDK samples (Przykłady zestawów SKD .NET usług Cognitive Services)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)