---
title: Skompresowane audio kodera-dekoder strumienia przy użyciu zestawu Speech SDK w systemie iOS
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przesyłać strumieniowo skompresowane audio do usługi mowy przy użyciu zestawu Speech SDK w systemie iOS.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: chlandsi
ms.openlocfilehash: 2089f4191ddd57fa8dc19862bd195756c166f2d4
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805862"
---
# <a name="how-to-use-codec-compressed-audio-input-with-the-speech-sdk-on-ios"></a>Instrukcje: korzystanie z kodera-dekoder kompresji audio przy użyciu zestawu Speech SDK w systemie iOS

Interfejs API **strumieniowych danych wejściowych audio** zestawu Speech SDK umożliwia przesyłanie strumieniowe skompresowanego dźwięku do usługi mowy przy użyciu strumienia ściągania lub wypychania.

> [!IMPORTANT]
> Do przesyłania strumieniowego skompresowanego audio w systemie iOS jest wymagany pakiet Speech SDK w wersji 1.7.0 lub nowszej. Jest ona również obsługiwana dla [ C++systemów C#, i Java w systemie Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9)](how-to-use-codec-compressed-audio-input-streams.md) i [Java w środowisku Android.](how-to-use-codec-compressed-audio-input-streams-android.md)

W przypadku WAV/PCM zapoznaj się z dokumentacją mowy linii głównej. Na zewnątrz WAV/PCM obsługiwane są następujące skompresowane formaty danych wejściowych kodera-dekoder:

- MP3
- OPUS/OGG
- FLAC
- ALAW w kontenerze WAV
- MULAW w kontenerze WAV

## <a name="prerequisites"></a>Wymagania wstępne

Obsługa skompresowanego dźwięku jest implementowana przy użyciu [GStreamer](https://gstreamer.freedesktop.org). Ze względów licencyjnych te funkcje nie mogą być dostarczane z zestawem SDK, ale biblioteka otoki zawierająca te funkcje musi być skompilowana przez deweloperów aplikacji i dostarczana z aplikacjami korzystającymi z zestawu SDK.

Aby skompilować tę bibliotekę otoki, najpierw Pobierz i zainstaluj [zestaw SDK GStreamer](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg). Następnie Pobierz projekt Xcode dla [biblioteki otoki](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper).

Otwórz projekt w programie Xcode i skompiluj go dla **ogólnego celu urządzenia z systemem iOS** — nie będzie on działał w celu skompilowania go dla określonego celu.

Krok kompilacja spowoduje wygenerowanie dynamicznego zestawu struktury z biblioteką dynamiczną dla wszystkich potrzebnych architektur o nazwie `GStreamerWrapper.framework`.

Ta struktura musi być uwzględniona we wszystkich aplikacjach, które używają skompresowanych strumieni audio z zestawem SDK usługi Speech Service.

Zastosuj następujące ustawienia w projekcie Xcode, aby to zrobić:

1. Skopiuj zarówno `GStreamerWrapper.framework`, jak i strukturę zestawu Speech SDK Cognitive Services, który można pobrać z tego [miejsca](https://aka.ms/csspeech/iosbinary), do katalogu zawierającego przykładowy projekt.
1. Dostosuj ścieżki do struktur w _ustawieniach projektu_.
   1. Na karcie **Ogólne** w nagłówku **osadzone pliki binarne** Dodaj bibliotekę zestawu SDK jako strukturę: **Dodaj osadzone pliki binarne** > **Dodaj inne...** > przejdź do wybranego katalogu i wybierz obie platformy.
   1. Przejdź do karty **Build Settings** (Ustawienia kompilacji) i aktywuj ustawienia **All** (Wszystko).
1. Dodaj katalog `$(SRCROOT)/..` do pozycji _Framework Search Paths_ (Ścieżki wyszukiwania struktury) w ramach nagłówka **Search Paths** (Ścieżki wyszukiwania).

## <a name="example-code-using-codec-compressed-audio-input"></a>Przykładowy kod przy użyciu kodera skompresowanego sygnału audio

Aby przesłać strumieniowo w skompresowanym formacie audio do usługi mowy, Utwórz `SPXPullAudioInputStream` lub `SPXPushAudioInputStream`.

Poniższy fragment kodu pokazuje, jak utworzyć `SPXAudioConfiguration` z wystąpienia `SPXPushAudioInputStream`, określając MP3 jako format kompresji strumienia.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=66-77&highlight=2-11)]

W następnym fragmencie kodu pokazano, jak skompresowane dane audio można odczytać z pliku i obwody do `SPXPushAudioInputStream`.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=105-151&highlight=19-44)]

## <a name="next-steps"></a>Następne kroki

- [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
- [Zobacz jak rozpoznać mowę w języku Java](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
