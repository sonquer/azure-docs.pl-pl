---
title: Linki do dokumentacji dotyczące wzbogacania AI
titleSuffix: Azure Cognitive Search
description: Lista artykułów, samouczków, przykładów i wpisów w blogu związanych z obciążeniami wzbogacenia AI na platformie Azure Wyszukiwanie poznawcze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: e73e69f90b1228154d7f209c54c6b52cc03d5eb4
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76837794"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Zasoby dokumentacji dotyczące wzbogacenia AI na platformie Azure Wyszukiwanie poznawcze

Wzbogacanie AI to funkcja indeksowania Wyszukiwanie poznawcze platformy Azure, która umożliwia znajdowanie ukrytych informacji w źródłach nietekstowych i niezróżnicowanym tekście, przekształcając je w zawartość tekstową z możliwością wyszukiwania pełnotekstowego na platformie Azure Wyszukiwanie poznawcze.

Poniższe artykuły stanowią kompletną dokumentację dotyczącą wzbogacania AI.

## <a name="getting-started"></a>Wprowadzenie
+ [Wprowadzenie do AI na platformie Azure Wyszukiwanie poznawcze](cognitive-search-concept-intro.md)
+ [Szybki Start: Tworzenie zestawu umiejętności poznawczych w Azure Portal](cognitive-search-quickstart-blob.md)
+ [Samouczek: wzbogacone indeksowanie za pomocą AI](cognitive-search-tutorial-blob.md)
+ [Przykład: Tworzenie niestandardowej umiejętności dla wzbogacania AI](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Wskazówki dotyczące porady
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Jak odwoływać się do adnotacji w zestawie umiejętności](cognitive-search-concept-annotations-syntax.md)
+ [Jak mapować pola na indeks](cognitive-search-output-field-mapping.md)
+ [Jak przetwarzać i wyodrębniać informacje z obrazów](cognitive-search-concept-image-scenarios.md)
+ [Jak skompilować indeks Wyszukiwanie poznawcze platformy Azure](search-howto-reindex.md)
+ [Jak zdefiniować niestandardowy interfejs umiejętności](cognitive-search-custom-skill-interface.md)
+ [Wskazówki dotyczące rozwiązywania problemów](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Informacje ogólne

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
  + [Microsoft. umiejętności. Text. KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft. umiejętności. Text. PIIDetectionSkill](cognitive-search-skill-pii-detection.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft. umiejętności. Text. TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft. umiejętności. util. ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft. umiejętności. util. DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ Umiejętności niestandardowe
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Przestarzałe umiejętności](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [Interfejs API REST](https://docs.microsoft.com/rest/api/searchservice/)
  + [Create zestawu umiejętności (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Create indeksator (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Zobacz także

+ [Interfejs API REST usługi Azure Wyszukiwanie poznawcze](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indeksatory w usłudze Azure Cognitive Search](search-indexer-overview.md)
+ [Co to jest platforma Azure Wyszukiwanie poznawcze?](search-what-is-azure-search.md)
