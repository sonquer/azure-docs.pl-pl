---
title: Czym jest usługa rozpoznawania mowy?
titleSuffix: Azure Cognitive Services
description: Usługa mowy to zjednoczenie zamiany mowy na tekst, zamiany tekstu na mowę i Tłumaczenie mowy na pojedynczą subskrypcję platformy Azure. Dodaj mowę do aplikacji, narzędzi i urządzeń za pomocą zestawu Speech SDK, zestawu Speech Devices SDK lub interfejsów API REST.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: 7ddfae430e6aa4ec9549e40c937e5edcfd927f6d
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119921"
---
# <a name="what-is-the-speech-service"></a>Czym jest usługa rozpoznawania mowy?

Usługa mowy to zjednoczenie zamiany mowy na tekst, tekstu na mowę i tłumaczenia mowy na jedną subskrypcję platformy Azure. Można łatwo włączyć mowę dla aplikacji, narzędzi i urządzeń za pomocą [zestawu Speech SDK](speech-sdk-reference.md), [zestawu Speech Devices SDK](https://aka.ms/sdsdk-quickstart)lub [interfejsów API REST](rest-apis.md).

> [!IMPORTANT]
> Usługa mowy zastąpiła interfejs API rozpoznawania mowy Bing, tłumaczenie mowy w usłudze Translator i Custom Speech. Aby uzyskać instrukcje dotyczące migracji _, zobacz przewodniki dotyczące > migracji_ .

Te funkcje składają się na usługę Speech. Skorzystaj z łączy w tej tabeli, aby dowiedzieć się więcej na temat typowych przypadków użycia dla każdej funkcji lub przejrzeć odwołanie do interfejsu API.

| Usługa | Cecha | Opis | SDK | REST |
| ------- | ------- | ----------- | --- | ---- |
| [Zamiana mowy na tekst](speech-to-text.md) | Zamiana mowy na tekst | Funkcja zamiany mowy na tekst przekształca strumienie audio do tekstu w czasie rzeczywistym, które mogą być używane przez aplikacje, narzędzia lub urządzenia. Używaj zamiany mowy na tekst za pomocą [Language Understanding (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/) , aby uzyskać intencje użytkownika z uzyskanego mowy i korzystać z poleceń głosowych. | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Transkrypcja partii](batch-transcription.md) | Transkrypcja partii umożliwia asynchroniczne transkrypcja mowy na tekst w dużych ilościach danych. Jest to usługa oparta na protokole REST, która używa tego samego punktu końcowego jako dostosowania i zarządzania modelami. | Nie | [Tak](https://westus.cris.ai/swagger/ui/index) |
| | [Konwersacja z obsługą kilku urządzeń](multi-device-conversation.md) | Łączenie wielu urządzeń lub klientów w konwersacji w celu wysyłania komunikatów na mowę lub tekstowych przy użyciu łatwej obsługi transkrypcji i tłumaczenia| Yes | Nie |
| | [Transkrypcja konwersacji](conversation-transcription-service.md) | Włącza rozpoznawanie mowy w czasie rzeczywistym, identyfikację głosu i diarization. Doskonale nadaje się do jego przepisywaniach spotkań osób z możliwością odróżniania głośników. | Yes | Nie |
| | [Tworzenie modeli Custom Speech](#customize-your-speech-experience) | Jeśli używasz funkcji zamiany mowy na tekst do rozpoznawania i transkrypcji w unikatowym środowisku, możesz tworzyć i uczenia niestandardowych modeli akustycznych, językowych i wymowych w celu obsługi szumów otoczenia lub słownictwa specyficznego dla branż. | Nie | [Tak](https://westus.cris.ai/swagger/ui/index) |
| [Zamiana tekstu na mowę](text-to-speech.md) | Zamiana tekstu na mowę | Zamiana tekstu na mowę polega na konwertowaniu tekstu wejściowego na wymowę przez człowieka, który używa [języka SSML (Speech syntezing Language)](speech-synthesis-markup.md). Wybieraj spośród standardowych głosów i głosów neuronowych (zobacz [Obsługa języka](language-support.md)). | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Tworzenie niestandardowych głosów](#customize-your-speech-experience) | Twórz niestandardowe czcionki głosowe, które są unikatowe dla danej marki lub produktu. | Nie | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Tłumaczenie mowy](speech-translation.md) | Tłumaczenie mowy | Tłumaczenie mowy umożliwia tłumaczenie mowy w czasie rzeczywistym na wiele języków w aplikacjach, narzędziach i urządzeniach. Ta usługa umożliwia tłumaczenie mowy na mowę i zamianę mowy na tekst. | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Nie |
| [Asystenci głosu](voice-assistants.md) | Asystenci głosowi | Asystenci głosu korzystający z usługi mowy umożliwiają deweloperom tworzenie naturalnych, przypominających konwersacje interfejsów dla aplikacji i środowisk. Usługa asystenta głosowego zapewnia szybką i niezawodną interakcję między urządzeniem a implementacją asystenta, która korzysta z kanału mowy linii Direct line lub zintegrowanego polecenia niestandardowego (wersja zapoznawcza) w celu ukończenia zadania. | [Tak](voice-assistants.md) | Nie |

## <a name="try-the-speech-service"></a>Wypróbuj usługę mowy

Oferujemy Przewodniki Szybki Start w najpopularniejszych językach programowania, z których każdy jest przeznaczony do uruchamiania kodu w mniej niż 10 minut. Ta tabela zawiera najpopularniejsze Przewodniki Szybki Start dla każdej funkcji. Użyj nawigacji po lewej stronie, aby poznać dodatkowe języki i platformy.

| Zamiana mowy na tekst (SDK) | Zamiana tekstu na mowę (SDK) | Tłumaczenie (SDK) |
| -------------------- | -------------------- | ----------------- |
| [Rozpoznawanie mowy z pliku dźwiękowego](quickstarts/speech-to-text-from-file.md) | [Wytłumacz mowę na plik audio](quickstarts/text-to-speech-audio-file.md) | [Tłumaczenie zamiany mowy na tekst](quickstarts/translate-speech-to-text.md) |
| [Rozpoznawanie mowy przy użyciu mikrofonu](quickstarts/speech-to-text-from-microphone.md) | [Funkcja syntezy mowy z prelegentem](quickstarts/text-to-speech.md) | [Tłumaczenie mowy na wiele języków docelowych](quickstarts/translate-speech-to-text-multiple-languages.md) |
| [Rozpoznawanie mowy przechowywanej w usłudze BLOB Storage](quickstarts/from-blob.md) | [Synteza asynchroniczna dla długich postaci audio](quickstarts/text-to-speech/async-synthesis-long-form-audio.md) | [Tłumaczenie zamiany mowy na mowę](quickstarts/translate-speech-to-speech.md) |

> [!NOTE]
> Funkcja zamiany mowy na tekst i zamiany tekstu na mowę również ma punkty końcowe REST i skojarzone Przewodniki Szybki Start.

Jeśli masz szansę na korzystanie z usługi Speech, wypróbuj nasz samouczek, który uczy się, jak rozpoznać intencje z mowy przy użyciu zestawu Speech SDK i LUIS.

- [Samouczek: Rozpoznawanie intencji z mowy przy użyciu zestawu Speech SDK i LUIS,C#](how-to-recognize-intents-from-speech-csharp.md)
- [Samouczek: Voice enable the bot with Speech SDKC#](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Samouczek: Tworzenie aplikacji kolby do tłumaczenia tekstu, analizowanie tonacji i syntezowanie przetłumaczonego tekstu na mowę, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Pobieranie przykładowego kodu

Przykładowy kod jest dostępny w witrynie GitHub dla usługi Speech. Te przykłady obejmują typowe scenariusze, takie jak odczytywanie audio z pliku lub strumienia, rozpoznawanie ciągłe i jednokrotne oraz praca z modelami niestandardowymi. Użyj tych linków, aby wyświetlić zestaw SDK i przykłady REST:

- [Przykłady zamiany mowy na tekst, zamiany tekstu na mowę i tłumaczenia mowy (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Przykłady transkrypcji partii (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Przykłady zamiany tekstu na mowę (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Przykłady asystenta głosowego (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Dostosowywanie środowiska mowy

Usługa mowy dobrze współpracuje z wbudowanymi modelami, ale warto dodatkowo dostosowywać i dostrajać środowisko dla danego produktu lub środowiska. Opcje dostosowania przestają być od modelu akustycznego na unikatowe czcionki głosowe dla marki.

| Usługa rozpoznawania mowy | Platforma | Opis |
| -------------- | -------- | ----------- |
| Zamiana mowy na tekst | [Custom Speech](https://aka.ms/customspeech) | Dostosuj modele rozpoznawania mowy do Twoich potrzeb i dostępnych danych. Przezwyciężenie barier rozpoznawania mowy, takich jak styl mowy, słownictwo i hałas w tle. |
| Zamiana tekstu na mowę | [Niestandardowy głos](https://aka.ms/customvoice) | Utwórz rozpoznawalny, unikatowy głos dla aplikacji zamieniających tekst na mowę, które korzystają z Twoich dostępnych danych mowy. Można dokładniej dostosować dane wyjściowe głosu przez dostosowanie zestawu parametrów głosowych. |

## <a name="reference-docs"></a>Dokumentacja dokumentacji

- [Zestaw SDK rozpoznawania mowy](speech-sdk-reference.md)
- [Zestaw SDK urządzeń mowy](speech-devices-sdk.md)
- [Interfejs API REST: zamiana mowy na tekst](rest-speech-to-text.md)
- [Interfejs API REST: zamiana tekstu na mowę](rest-text-to-speech.md)
- [Interfejs API REST: Transkrypcja i dostosowywanie partii](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uzyskaj bezpłatnie klucz subskrypcji usługi mowy](get-started.md)
