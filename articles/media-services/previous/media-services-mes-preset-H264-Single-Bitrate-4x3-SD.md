---
title: Pojedynczy szybkość transmisji bitów H264 4 x 3 SD Media Encoder Standard ustawienie — Azure | Dokumentacja firmy Microsoft
description: Temat zawiera omówienie **pojedynczego szybkość transmisji bitów H264 4 x 3 SD** ustawienie wstępne zadania.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 171689fe-7c4f-4d5a-b48e-281136d8ac97
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: a1359bacdf6735ca49e22d7c7b6d0014d56f1735
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61129478"
---
# <a name="h264-single-bitrate-4x3-sd"></a>Pojedyncza szybkość transmisji bitów H264 4x3 SD
`Media Encoder Standard` definiuje zestaw kodowania ustawienia wstępne, których można użyć podczas tworzenia zadań kodowania. Można użyć `preset name` do określenia do formatu, który chcesz zakodować pliku multimedialnego. Lub można utworzyć własne JSON lub XML na podstawie ustawień wstępnych (przy użyciu kodowania UTF-8 lub UTF-16. Następnie należy wprowadzić niestandardowego ustawienia wstępnego z koderem. Aby uzyskać listę wszystkich wstępnie zdefiniowanych nazw obsługiwanych przez to `Media Encoder Standard` encoder, zobacz [ustawienia wstępne zadań usługi Media Encoder Standard](media-services-mes-presets-overview.md).  
  
 W tym temacie przedstawiono `H264 Single Bitrate 4x3 SD` wstępnie ustawione w formacie XML i JSON.  
  
 Ten plik wstępnie tworzy pojedynczy MP4 z 1800 KB/s i stereo AAC audio szybkość transmisji bitów. Aby uzyskać szczegółowe informacje o profilu szybkości transmisji bitów, próbkowanie wskaźnik, itp., to ustawień, sprawdź zdefiniowane poniżej w formacie JSON lub XML. Objaśnienia każdego elementu w tych oznacza, że ustawienia i prawidłowe wartości dla każdego elementu, zobacz [schemat usługi Media Encoder Standard](media-services-mes-schema.md) tematu.  
  
 XML  
  
```  
<?xml version="1.0" encoding="utf-16"?>  
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">  
  <Encoding>  
    <H264Video>  
      <KeyFrameInterval>00:00:02</KeyFrameInterval>  
      <SceneChangeDetection>true</SceneChangeDetection>  
      <H264Layers>  
        <H264Layer>  
          <Bitrate>1800</Bitrate>  
          <Width>640</Width>  
          <Height>480</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>1800</MaxBitrate>  
        </H264Layer>  
      </H264Layers>  
      <Chapters />  
    </H264Video>  
    <AACAudio>  
      <Profile>AACLC</Profile>  
      <Channels>2</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>128</Bitrate>  
    </AACAudio>  
  </Encoding>  
  <Outputs>  
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">  
      <MP4Format />  
    </Output>  
  </Outputs>  
</Preset>  
```  
  
 JSON  
  
```  
{  
  "Version": 1.0,  
  "Codecs": [  
    {  
      "KeyFrameInterval": "00:00:02",  
      "SceneChangeDetection": true,  
      "H264Layers": [  
        {  
          "Profile": "Auto",  
          "Level": "auto",  
          "Bitrate": 1800,  
          "MaxBitrate": 1800,  
          "BufferWindow": "00:00:05",  
          "Width": 640,  
          "Height": 480,  
          "BFrames": 3,  
          "ReferenceFrames": 3,  
          "AdaptiveBFrame": true,  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        }  
      ],  
      "Type": "H264Video"  
    },  
    {  
      "Profile": "AACLC",  
      "Channels": 2,  
      "SamplingRate": 48000,  
      "Bitrate": 128,  
      "Type": "AACAudio"  
    }  
  ],  
  "Outputs": [  
    {  
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",  
      "Format": {  
        "Type": "MP4Format"  
      }  
    }  
  ]  
}  
```
