---
title: Migruj z indeksatorów V1 i V2 do Azure Media Services Video Indexer | Microsoft Docs
description: W tym temacie omówiono sposób migrowania z Azure Media Indexer V1 i V2 do Azure Media Services Video Indexer.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2019
ms.author: juliako
ms.openlocfilehash: 2268c074480f99ca23117ca2ffd2c87c1dbb10a2
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513239"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>Migrowanie z Media Indexer i Media Indexer 2 do Video Indexer

Procesor multimediów [Azure Media Indexer](media-services-index-content.md) i procesory [Azure Media Indexer 2 w wersji zapoznawczej](media-services-process-content-with-indexer2.md) są wycofywane. Aby uzyskać daty wycofania, zobacz temat ten [starszy składnik](legacy-components.md) . [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) zastępuje te starsze procesory nośników.

Azure Media Services Video Indexer jest oparta na Azure Media Analytics, Wyszukiwanie poznawcze platformy Azure, Cognitive Services (np. interfejs API rozpoznawania twarzy, Microsoft translator, interfejs API przetwarzania obrazów i Custom Speech Service). Umożliwia wyodrębnianie szczegółowych informacji z plików wideo przy użyciu modeli wideo i audio usługi Video Indexer. Aby dowiedzieć się, jakie scenariusze Video Indexer mogą być używane w programie, jakie funkcje oferują i jak zacząć, zobacz [Video Indexer modele wideo i audio](../video-indexer/video-indexer-overview.md). 

Można wyodrębnić szczegółowe informacje z plików wideo i audio, korzystając z [ustawień domyślnych programu Azure Media Services v3 Analyzer](../latest/analyzing-video-audio-files-concept.md) lub bezpośrednio przy użyciu [interfejsów API Video Indexer](https://api-portal.videoindexer.ai/). Obecnie istnieje nakładanie się między funkcjami oferowanymi przez interfejsy API Video Indexer i interfejsy API Media Services v3.

> [!NOTE]
> Aby zrozumieć, Kiedy chcesz użyć Video Indexer a Media Services ustawienia wstępne analizatora, zapoznaj się z [dokumentem porównania](../video-indexer/compare-video-indexer-with-media-services-presets.md). 

W tym artykule omówiono procedurę migrowania z Azure Media Indexer i Azure Media Indexer 2 do Azure Media Services Video Indexer.  

## <a name="migration-options"></a>Opcje migracji 

|Jeśli potrzebujesz  |a następnie wybierz |
|---|---|
|rozwiązanie, które zapewnia transkrypcję zamiany mowy na tekst dla dowolnego formatu pliku multimedialnego w formatach plików z napisami: VTT, narzędzia SRT lub TTML<br/>a także dodatkowe szczegółowe informacje o dźwięku, takie jak: słowa kluczowe, temat inferencing, zdarzenia akustyczne, diarization prelegenta, wyodrębnianie jednostek i tłumaczenie| Zaktualizuj swoje aplikacje, aby używać możliwości usługi Azure Video Indexer za pomocą interfejsu API REST Video Indexer v2 lub zestawu narzędzi Azure Media Services v3 audio.|
|możliwości zamiany mowy na tekst| bezpośrednio Użyj Speech API Cognitive Services.|  

## <a name="getting-started-with-video-indexer"></a>Wprowadzenie do Video Indexer

W poniższej sekcji przedstawiono odpowiednie linki: jak zacząć [korzystać z video Indexer?](https://docs.microsoft.com/azure/media-services/video-indexer/video-indexer-overview#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>Wprowadzenie do interfejsów API Media Services v3

Interfejs API programu Azure Media Services v3 umożliwia wyodrębnienie szczegółowych informacji z plików wideo i audio za pośrednictwem [ustawień wstępnych analizatora Azure Media Services v3](../latest/analyzing-video-audio-files-concept.md). 

**AudioAnalyzerPreset** umożliwia wyodrębnienie wielu szczegółowych informacji audio z pliku audio lub wideo. Dane wyjściowe obejmują plik VTT lub TTML dla transkrypcji audio i pliku JSON (ze wszystkimi dodatkowymi spostrzeżeniami w usłudze audio). Szczegółowe informacje o danych audio obejmują słowa kluczowe, indeksowanie głośników i analizę tonacji mowy. AudioAnalyzerPreset obsługuje również wykrywanie języka dla określonych języków. Aby uzyskać szczegółowe informacje, zobacz [transformacje](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset).

### <a name="get-started"></a>Rozpocznij

Aby rozpocząć, zobacz:

* [Samouczek](../latest/analyze-videos-tutorial-with-api.md)
* Przykłady AudioAnalyzerPreset: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) lub [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* Przykłady VideoAnalyzerPreset: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) lub [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>Wprowadzenie do usługi Cognitive Services Speech Services

Usługa [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/) udostępnia usługę zamiany mowy na tekst, która przekształca strumienie audio na tekst w czasie rzeczywistym, które mogą być używane przez aplikacje, narzędzia lub urządzenia. Możesz użyć zamiany mowy na tekst, aby [dostosować własny model akustyczny, model języka lub model wymowy](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md). Aby uzyskać więcej informacji, zobacz [Cognitive Services zamiany mowy na tekst](../../cognitive-services/speech-service/speech-to-text.md). 

> [!NOTE] 
> Usługa zamiany mowy na tekst nie pobiera formatów plików wideo i pobiera tylko [niektóre formaty audio](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#audio-formats). 

Aby uzyskać więcej informacji o usłudze zamiany tekstu na mowę i sposobach rozpoczynania pracy, zobacz artykuł [co to jest Speech-to-Text?](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-to-text)

## <a name="known-differences-from-deprecated-services"></a>Znane różnice od przestarzałych usług 

Zobaczysz, że Video Indexer, Azure Media Services v3 AudioAnalyzerPreset, a usługi Cognitive Services Speech Services są bardziej niezawodne i generują lepsze wyniki jakości niż wycofane Azure Media Indexer 1 i Azure Media Indexer 2 procesorów.  

Niektóre znane różnice obejmują: 

* Usługi Cognitive Services Speech nie obsługują wyodrębniania słów kluczowych. Jednak Video Indexer i Media Services v3 AudioAnalyzerPreset oferują bardziej niezawodny zestaw słów kluczowych w formacie pliku JSON. 

## <a name="need-help"></a>Potrzebujesz pomocy?

Możesz otworzyć bilet pomocy technicznej, przechodząc do [nowego żądania obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Następne kroki

* [Starsze składniki](legacy-components.md)
* [Strona cennika](https://azure.microsoft.com/pricing/details/media-services/#encoding)


