---
title: Pojęcia dotyczące strumienia danych wejściowych audio zestawu Speech SDK
titleSuffix: Azure Cognitive Services
description: Przegląd możliwości interfejsu API wejściowego strumienia usługi Speech SDK.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 3039276a49e7bb41660d114e78ca047a3f77f279
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109932"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>Informacje o interfejsie API strumieniowego wejścia audio zestawu mowy SDK

Interfejs API **wejścia audio** zestawu mowy SDK umożliwia przesyłanie strumieniowe audio do aparatów rozpoznawania zamiast używania mikrofonu lub interfejsów API plików wejściowych.

Następujące kroki są wymagane w przypadku korzystania ze strumieni danych wejściowych audio:

- Określ format strumienia audio. Ten format musi być obsługiwany przez zestaw mowy SDK i usługę mowy. Obecnie obsługiwana jest tylko następująca konfiguracja:

  Próbki audio w formacie PCM, jeden kanał, 16000 próbek na sekundę, 32000 bajtów na sekundę, Wyrównaj do dwóch bloków (16 bitów włącznie z uzupełnieniem dla próbki), 16 bitów na próbkę.

  Odpowiedni kod w zestawie SDK, aby utworzyć format audio wygląda następująco:

  ```csharp
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000;
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- Upewnij się, że kod może dostarczyć nieprzetworzone dane audio zgodnie z tymi specyfikacjami. Jeśli dane źródłowe audio nie są zgodne z obsługiwanymi formatami, dźwięk musi być przekształcony w wymagany format.

- Utwórz własną klasę strumienia wejściowego audio pochodzącą z `PullAudioInputStreamCallback`. Zaimplementuj elementy członkowskie `Read()` i `Close()`. Dokładna sygnatura funkcji jest zależna od języka, ale kod będzie wyglądać podobnie do tego przykładu kodu:

  ```csharp
   public class ContosoAudioStream : PullAudioInputStreamCallback {
      ContosoConfig config;

      public ContosoAudioStream(const ContosoConfig& config) {
          this.config = config;
      }

      public int Read(byte[] buffer, uint size) {
          // returns audio data to the caller.
          // e.g. return read(config.YYY, buffer, size);
      }

      public void Close() {
          // close and cleanup resources.
      }
   };
  ```

- Utwórz konfigurację audio w oparciu o format audio i strumień wejściowy. Podczas tworzenia aparatu rozpoznawania należy przekazać zwykłą konfigurację mowy i konfigurację wejściową audio. Na przykład:

  ```csharp
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>Następne kroki

- [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
- [Zobacz, jak rozpoznawanie mowy w języku C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
