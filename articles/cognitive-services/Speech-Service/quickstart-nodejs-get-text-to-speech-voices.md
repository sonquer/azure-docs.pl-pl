---
title: 'Szybki Start: Lista głosów zamiany tekstu na mowę, Node. js-Speech Service'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start dowiesz się, jak uzyskać pełną listę głosów standardowych i neuronowych dla regionu/punktu końcowego przy użyciu środowiska Node. js. Lista jest zwracana w formacie JSON i dostępność głosu różni się w zależności od regionu.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: 7a929794ffaea4f863ffaef7227e58c7ccf901f0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976574"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Szybki Start: Pobieranie listy głosów zamiany tekstu na mowę przy użyciu środowiska Node. js

W tym przewodniku szybki start dowiesz się, jak uzyskać pełną listę głosów standardowych i neuronowych dla regionu/punktu końcowego przy użyciu środowiska Node. js. Lista jest zwracana w formacie JSON i dostępność głosu różni się w zależności od regionu. Aby zapoznać się z listą obsługiwanych regionów, zobacz [regiony](regions.md).

Ten przewodnik Szybki Start wymaga [konta Cognitive Services platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z zasobem usługi mowy. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](get-started.md), aby uzyskać klucz subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* Środowisko [Node w wersji 8.12.x lub nowszej](https://nodejs.org/en/)
* Program [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) lub ulubiony edytor tekstów
* Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj bezpłatnie!](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Tworzenie projektu i wymaganie zależności

Utwórz nowy projekt node. js przy użyciu ulubionego środowiska IDE lub edytora. Następnie skopiuj ten fragment kodu do swojego projektu do pliku o nazwie `get-voices.js`.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write the list of languages to a file
const fs = require('fs');
```

> [!NOTE]
> Jeśli nie korzystano z tych modułów, konieczne będzie ich zainstalowanie przed uruchomieniem programu. Aby zainstalować te pakiety, uruchom polecenie `npm install request request-promise`.

## <a name="get-an-access-token"></a>Pobranie tokenu dostępu

Zamiany tekstu na mowę interfejsu API REST wymaga tokenu dostępu do uwierzytelniania. Aby uzyskać token dostępu, wymagana jest wymiana. Ta funkcja wymienia klucz subskrypcji usługi mowy dla tokenu dostępu przy użyciu punktu końcowego `issueToken`.

W tym przykładzie przyjęto założenie, że subskrypcja usługi mowy znajduje się w regionie zachodnie stany USA. Jeśli używasz innego regionu, zaktualizuj wartość `uri`. Aby uzyskać pełną listę, zobacz [regionów](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Skopiuj ten kod do projektu:

```javascript
// Gets an access token.
function getAccessToken(subscriptionKey) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    }
    return rp(options);
}
```

> [!NOTE]
> Aby uzyskać więcej informacji na temat uwierzytelniania, zobacz [uwierzytelnianie przy użyciu tokenu dostępu](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

W następnej sekcji utworzymy funkcję w celu pobrania listy głosów i zapisania danych wyjściowych JSON do pliku.

## <a name="make-a-request-and-save-the-response"></a>Tworzenie żądania i zapisać odpowiedź

W tym miejscu zamierzasz skompilować żądanie i zapisać listę zwróconych głosów. W tym przykładzie przyjęto założenie, że używasz punktu końcowego zachodnie stany USA. Jeśli zasób jest zarejestrowany w innym regionie, pamiętaj o zaktualizowaniu `uri`. Aby uzyskać więcej informacji, zobacz [regiony usługi mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Następnie Dodaj wymagane nagłówki dla żądania. Na koniec wprowadzisz żądania do usługi. Jeśli żądanie zakończy się pomyślnie i zostanie zwrócony kod stanu 200, odpowiedź jest zapisywana w pliku.

```javascript
function textToSpeech(accessToken) {
    let options = {
        method: 'GET',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/voices/list',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'Content-Type': 'application/json'
        }
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('voices.json'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

To już prawie koniec. Ostatnim krokiem jest utworzenie funkcji asynchronicznej. Ta funkcja odczyta swój klucz subskrypcji ze zmiennej środowiskowej, Pobierz token, poczekaj na zakończenie żądania, a następnie Napisz odpowiedź JSON do pliku.

Jeśli nie znasz zmiennych środowiskowych ani wolisz testować przy użyciu klucza subskrypcji stałe jako ciąg, Zastąp `process.env.SPEECH_SERVICE_KEY` kluczem subskrypcji jako ciągiem.

```javascript
// Use async and await to get the token before attempting
// to convert text to speech.
async function main() {
    // Reads subscription key from env variable.
    // You can replace this with a string containing your subscription key. If
    // you prefer not to read from an env variable.
    // e.g. const subscriptionKey = "your_key_here";
    const subscriptionKey = process.env.SPEECH_SERVICE_KEY;
    if (!subscriptionKey) {
        throw new Error('Environment variable for your subscription key is not set.')
    };
    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

To wszystko. Teraz możesz uruchomić przykładową aplikację. Z wiersza polecenia (lub sesji terminalowej) przejdź do katalogu projektu, a następnie uruchom:

```console
node get-voices.js
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Upewnij się, że wszystkie poufne informacje, takie jak klucze subskrypcji, zostały usunięte z kodu źródłowego przykładowej aplikacji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Poznaj przykłady dla platformy Node.js w serwisie GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Zobacz także

* [Dokumentacja interfejsu API zamiany tekstu na mowę](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Tworzenie niestandardowych voice czcionek](how-to-customize-voice-font.md)
* [Próbki głosu rekord do utworzenia niestandardowych voice](record-custom-voice-samples.md)
