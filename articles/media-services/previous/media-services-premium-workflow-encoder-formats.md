---
title: Formaty Media Encoder Premium Workflow i kodery Microsoft Docs
description: Ten temat zawiera omówienie formatów formatu i koderów Media Encoder Premium Workflow.
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
ms.date: 03/19/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 87cd7c63939331190530a46071a6b4c40480562f
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792585"
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>Formaty Media Encoder Premium Workflow i kodeki

> [!NOTE]
> Procesor multimediów Media Encoder Premium Workflow omawiany w tym temacie nie jest dostępny w Chinach. 

Ten dokument zawiera listę formatów plików wejściowych i wyjściowych oraz kodeków, które są obsługiwane przez publiczną wersję zapoznawczą kodera **Media Encoder Premium Workflow** .

[Media Encoder Premium Workflow formaty wejściowe i kodery-dekoder](#input_formats)

Media Encoder Premium Workflow formatów danych wyjściowych i kodery-dekoder

**Media Encoder Premium Workflow** obsługuje napisy kodowane opisane w [tej](#closed_captioning) sekcji. 

## <a id="input_formats"></a>Media Encoder Premium Workflow formaty wejściowe i kodery-dekoder

W poniższej sekcji przedstawiono kodery-dekoder i formaty plików obsługiwane przez ten procesor multimediów jako dane wejściowe.

### <a name="input-containerfile-formats"></a>Dane wejściowe w formacie kontenera/pliku

* Adobe® Flash® F4V
* MXF/SMPTE 377M
* GXF
* Strumienie transportowe MPEG-2
* Strumienie programu MPEG-2
* MPEG-4/MP4
* Windows Media/ASF
* AVI (nieskompresowany 8bit/10bit)

### <a name="input-video-codecs"></a>Kodery-dekoder wideo

* AVC 8-bitowy/10-bitowy, do 4:2:2, włącznie z AVCIntra
* Avid DNxHD (w MXF)
* DVCPro/DVCProHD (w MXF)
* HEVC/H. 265, główny i główny 10 profil
* JPEG2000
* MPEG-2 (do 422 profilu i wysokiego poziomu), w tym różne warianty, takie jak XDCAM, XDCAM HD, XDCAM IMX, CableLabs® i D10)
* MPEG-1
* Windows Media Video/VC-1

### <a name="input-audio-codecs"></a>Kodery-dekoder Audio Input

* AES (SMPTE 331M i 302M, AES3-2003)
* Dolby® E
* Dolby® Digital (AC3)
* AAC (AAC-LC, AAC-IT i AAC-HEv2; do 5,1)
* MPEG Layer 2
* MP3 (warstwa audio MPEG-1)
* Dźwięk Windows Media
* WAV/PCM

## <a id="output_format"></a>Media Encoder Premium Workflow formatów danych wyjściowych i kodery-dekoder

W poniższej sekcji przedstawiono kodery-dekoder i formaty plików, które są obsługiwane jako dane wyjściowe z tego procesora multimediów.

### <a name="output-containerfile-formats"></a>Pliki/kontenery wyjściowe

* Adobe® Flash® F4V
* MXF (OP1a, XDCAM i AS02)
* DPP (łącznie z AS11)
* GXF
* MPEG-4/MP4
* Windows Media/ASF
* AVI (nieskompresowany 8bit/10bit)
* Smooth Streaming format pliku (PIFF 1,3)
* MPEG-TS 

### <a name="output-video-codecs"></a>Wyjściowe kodery-dekoder wideo

* AVC (H. 264; 8-bitowy; maksymalnie wysoki profil, poziom 5,2; 4K Ultra HD; AVC — Intra)
* Avid DNxHD (w MXF)
* DVCPro/DVCProHD (w MXF)
* MPEG-2 (do 422 profilu i wysokiego poziomu), w tym różne warianty, takie jak XDCAM, XDCAM HD, XDCAM IMX, CableLabs® i D10)
* MPEG-1
* Windows Media Video/VC-1
* Tworzenie miniatur JPEG
* HEVC (H. 265; 8 bitów i 10 bitów, główny i główny 10 profilów)


### <a name="output-audio-codecs"></a>Wyjściowe kodery-dekoder audio

* AES (SMPTE 331M i 302M, AES3-2003)
* Dolby® Digital (AC3)
* Dolby® Digital Plus (E-AC3) do 7,1
* AAC (AAC-LC, AAC-IT i AAC-HEv2; do 5,1)
* MPEG Layer 2
* MP3 (warstwa audio MPEG-1)
* Dźwięk Windows Media

>[!NOTE]
>W przypadku kodowania do Dolby® Digital (AC3) dane wyjściowe mogą być zapisywane tylko w pliku MP4 ISO.

## <a id="closed_captioning"></a>Obsługa napisów kodowanych

Przy przyjmowaniu **Media Encoder Premium Workflow** obsługuje:

1. Pliki SCC
2. Pliki SMPTE-TT
3. CEA-608/CEA-708 — przenoszone jako dane użytkownika (SEI wiadomości o strumieniach podstawowych H. 264, ATSC/53, SCTE20) lub przenoszone jako dane pomocnicze w plikach MXF/GXF
4. Pliki napisów STL

W przypadku danych wyjściowych dostępne są następujące opcje:

1. CEA-608 do CEA-708 tłumaczenia
2. CEA-608/CEA-708 Pass (osadzony w komunikatach o SEIach podstawowych strumieni H. 264 lub przenoszony jako dane pomocnicze w plikach MXF)
3. SCC
4. Tekst w trybie SMPTE (od źródła CEA-608 na SMPTE RP2052;, w tym DFXP tworzenia pliku)
5. Plik podtytuł narzędzia SRT
6. Strumienie napisów w formacie DVB

> [!NOTE]
> Nie wszystkie powyższe formaty danych wyjściowych są obsługiwane w przypadku dostarczania za pośrednictwem przesyłania strumieniowego w Azure Media Services.

## <a name="known-issues"></a>Znane problemy

Jeśli wejściowy film wideo nie zawiera napisów kodowanych, element zawartości wyjściowej nadal będzie zawierał pusty plik TTML. 

## <a name="need-help"></a>Potrzebujesz pomocy?

Możesz otworzyć bilet pomocy technicznej, przechodząc do [nowego żądania obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)
## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Prześlij opinię

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

