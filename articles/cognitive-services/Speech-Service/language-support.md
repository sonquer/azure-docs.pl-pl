---
title: Obsługa języka — usługa mowy
titleSuffix: Azure Cognitive Services
description: Usługa mowy obsługuje wiele języków w przypadku konwersji mowy na tekst i zamiany tekstu na mowę oraz Tłumaczenie mowy. Ten artykuł zawiera kompleksową listę obsługi języków według funkcji usługi.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 20b99cfffdaa0d942ccd4d954909810342cbfcb8
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935342"
---
# <a name="language-and-region-support-for-the-speech-service"></a>Obsługa języka i regionu dla usługi mowy

Obsługa języka zależy od funkcjonalności usługi mowy. W poniższych tabelach przedstawiono obsługę języka dla ofert [zamiany mowy na tekst](#speech-to-text), zamiany [tekstu na mowę](#text-to-speech)i usługi [tłumaczenia mowy](#speech-translation) .

## <a name="speech-to-text"></a>Zamiana mowy na tekst

Zarówno zestaw Microsoft Speech SDK, jak i interfejs API REST obsługują następujące języki (ustawienia regionalne). W celu poprawienia dokładności, dostosowanie jest oferowane dla podzestawu języków za pomocą przekazywania zapisu audio + z etykietami ludzkimi lub powiązanego tekstu: zdania. Dostosowanie wymowy jest obecnie dostępne tylko dla `en-US` i `de-DE`. Więcej informacji na temat dostosowywania [znajdziesz tutaj](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

 Ustawienia regionalne | Język | Obsługiwane | Dostosowania
------|------------|-----------|-------------
`ar-AE` | Arabski (Zjednoczone Emiraty Arabskie) | Tak | Nie
`ar-BH` | Arabski (Bahrajn) | Tak | Model języka
`ar-EG` | Arabski (Egipt), standard nowoczesne | Tak | Model języka
`ar-KW` | Arabski (Kuwejt) | Tak | Nie
`ar-QA` | Arabski (katar) | Tak | Nie
`ar-SA` | Arabski (Arabia Saudyjska) | Tak | Nie
`ca-ES` | Kataloński | Tak | Model języka
`da-DK` | Duński (Dania) | Tak | Model języka
`de-DE` | Niemiecki (Niemcy) | Tak | Model akustyczny<br>Model języka<br>Wymowa
`en-AU` | Angielski (Australia) | Tak | Model akustyczny<br>Model języka
`en-CA` | Angielski (Kanada) | Tak | Model akustyczny<br>Model języka
`en-GB` | Angielski (Zjednoczone Królestwo) | Tak | Model akustyczny<br>Model języka<br>Wymowa
`en-IN` | English (India) | Tak | Model akustyczny<br>Model języka
`en-NZ` | Angielski (Nowa Zelandia) | Tak | Model akustyczny<br>Model języka
`en-US` | Angielski (Stany Zjednoczone) | Tak | Model akustyczny<br>Model języka<br>Wymowa
`es-ES` | Hiszpański (Hiszpania) | Tak | Model akustyczny<br>Model języka
`es-MX` | Hiszpański (Meksyk) | Tak | Model akustyczny<br>Model języka
`fi-FI` | Fiński (Finlandia) | Tak | Model języka
`fr-CA` | Francuski (Kanada) | Tak | Model akustyczny<br>Model języka
`fr-FR` | Francuski (Francja) | Tak | Model akustyczny<br>Model języka<br>Wymowa
`gu-IN` | Gudżarati (Indyjski) | Tak | Model języka
`hi-IN` | Hindi (India) | Tak | Model akustyczny<br>Model języka
`it-IT` | Włoski (Włochy) | Tak | Model akustyczny<br>Model języka<br>Wymowa
`ja-JP` | Japoński (Japonia) | Tak | Model języka
`ko-KR` | Koreański (Korea) | Tak | Model języka
`mr-IN` | Marathi (Indie) | Tak | Model języka
`nb-NO` | Norweski (Bokmal) (Norwegia) | Tak | Model języka
`nl-NL` | Holenderski (Holandia) | Tak | Model języka
`pl-PL` | Polski (Polska) | Tak | Model języka
`pt-BR` | Portugalski (Brazylia) | Tak | Model akustyczny<br>Model języka<br>Wymowa
`pt-PT` | Portugalski (Portugalia) | Tak | Model języka
`ru-RU` | Rosyjski (Rosja) | Tak | Model akustyczny<br>Model języka
`sv-SE` | Szwedzki (Szwecja) | Tak | Model języka
`ta-IN` | Tamilski (Indie) | Tak | Model języka
`te-IN` | Telugu (Indie) | Tak | Nie
`th-TH` | Tajski (Tajlandia) | Tak | Nie
`tr-TR` | Turecki (Turcja) | Tak | Nie
`zh-CN` | Chiński (mandaryński uproszczony) | Tak | Model akustyczny<br>Model języka
`zh-HK` | Chiński (kantoński, tradycyjny) | Tak | Model języka
`zh-TW` | Chiński (mandaryński tajwańskie) | Tak | Model języka

## <a name="text-to-speech"></a>Zamiana tekstu na mowę

Zestawy Microsoft Speech SDK i interfejsy API REST obsługują te głosy, z których każdy obsługuje określony język i dialekt, identyfikowane przez ustawienia regionalne.

> [!IMPORTANT]
> Ceny różnią się w zależności od standardowych, niestandardowych i neuronowychych głosów. Aby uzyskać dodatkowe informacje, odwiedź stronę z [cennikiem](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

### <a name="neural-voices"></a>Głosy neuronowych

Neuronowych text-to-Speech to nowy typ syntezy mowy obsługiwanej przez głębokie sieci neuronowych. W przypadku korzystania z głosu neuronowych, synteza mowy jest niemal nieczytelna w odróżnieniu od nagrań ludzkich.

Głosy neuronowych mogą służyć do współdziałania z rozszerzenie czatbotów i asystentów głosowych bardziej naturalnych i atrakcyjnych, dzięki czemu można konwertować cyfrowe teksty, takie jak książki elektroniczne, na Audiobooks i ulepszać systemy nawigacyjne w samodzielnym zakresie. Podobnie jak naturalna prosodya i wyraźny zbiór wyrazów, głosy neuronowych znacząco zmniejszają zmęczenie nasłuchiwania, gdy użytkownicy współpracują z systemami AI.

Aby uzyskać więcej informacji na temat dostępności regionalnej, zobacz [regiony](regions.md#standard-and-neural-voices).

Ustawienia regionalne | Język | Płeć | Pełne Mapowanie nazw usług | Krótka nazwa głosu
--------|----------|--------|---------|------------
`de-DE` | Niemiecki (Niemcy) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (de-DE, KatjaNeural)" | "de-DE-KatjaNeural"
`en-US` | English (US) | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-US, GuyNeural)" | "pl-US-GuyNeural"
`en-US` | English (US) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-US, JessaNeural)" | "pl-US-JessaNeural"
`it-IT` | Włoski (Włochy) | Kobieta |"Microsoft Server Speech zamiana tekstu na mowę Voice (ElsaNeural)" | "IT-ElsaNeural"
`zh-CN` | Chiński (kontynent) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (zh-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

Aby dowiedzieć się, jak można konfigurować i dostosowywać neuronowych głosów, zobacz Language [syntezowania znaczników](speech-synthesis-markup.md#adjust-speaking-styles).

> [!NOTE]
> Możesz użyć pełnego mapowania nazw usług lub krótkiej nazwy głosu w żądaniach syntezy mowy.

### <a name="standard-voices"></a>Głosy standardowe

Ponad 75 standardowych głosów jest dostępnych w ponad 45 językach i ustawieniach regionalnych, które umożliwiają konwertowanie tekstu na mowę. Aby uzyskać więcej informacji na temat dostępności regionalnej, zobacz [regiony](regions.md#standard-and-neural-voices).

Ustawienia regionalne | Język | Płeć | Pełne Mapowanie nazw usług | Krótka nazwa
-------|----------|---------|----------|----------
<sup>1</sup>`ar-EG` | Arabski (Egipt) | Kobieta | "Microsoft Server mowy tekstu na głos mowy (ar np Hoda)" | "AR-EG-Hoda"
`ar-SA` | Arabski (Arabia Saudyjska) | Mężczyzna | "Microsoft Server mowy Text na głos mowy (ar-SA Naayf)" | "ar-SA-Naayf"
`bg-BG` | Bułgarski | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (bg-BG Ivanowi)" | "BG-BG-Ivan"
`ca-ES` | Kataloński | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (Kanada ES, HerenaRUS)" | "CA-ES-HerenaRUS"
`cs-CZ` | Czeski | Mężczyzna | "Microsoft Server mowy tekstu na głos mowy (cs-CZ, Jakub)" | "CS-CZ-Jakub"
`da-DK` | Duński | Kobieta | "Microsoft Server mowy Text na głos mowy (da-DK HelleRUS)" | "da-DK-HelleRUS"
`de-AT` | Niemiecki (Austria) | Mężczyzna | "Microsoft Server mowy Text na głos mowy (de-AT, Michael)" | "de-AT-Michael"
`de-CH` | Niemiecki (Szwajcaria) | Mężczyzna | "Microsoft Server mowy Text na głos mowy (de-CH, Karsten)" | "de-CH-Karsten"
`de-DE` | Niemiecki (Niemcy) | Kobieta | "Microsoft Server mowy Text na głos mowy (de-DE, Hedda)" | "de-DE-Hedda"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (de-DE, HeddaRUS)" | "de-DE-HeddaRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (de-DE, Stefan, Apollo)" | "de-DE-Stefan-Apollo"
`el-GR` | Grecki | Mężczyzna | "Microsoft Server mowy Text na głos mowy (el-GR Stefanos)" | "El-GR-Stefanos"
`en-AU` | Angielski (Australia) | Kobieta | "Microsoft Server mowy Text na głos mowy (en-AU Catherine)" | "en-AU-Catherine"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (en-AU HayleyRUS)" | "en-AU-HayleyRUS"
`en-CA` | Angielski (Kanada) | Kobieta | "Microsoft Server mowy Text na głos mowy (en-CA Anna)" | "en-CA-Linda"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (en-CA HeatherRUS)" | "en-CA-HeatherRUS"
`en-GB` | English (UK) | Kobieta | "Microsoft Server mowy Text na głos mowy (en-GB Susan, Apollo)" | "pl-GB-Susan-Apollo"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (en-GB HazelRUS)" | "pl-GB-HazelRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (en-GB George, Apollo)" | "pl-GB-George-Apollo"
`en-IE` | Angielski (Irlandia) | Mężczyzna | "Microsoft Server mowy Text na głos mowy (en-IE, Sean)" | "EN-IE-Janusz"
`en-IN` | English (India) | Kobieta | "Microsoft Server mowy Text na głos mowy (en-IN, Heera, Apollo)" | "pl-IN-Heera-Apollo"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (en-IN PriyaRUS)" | "pl-IN-PriyaRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (en-IN, Ravi, Apollo)" | "pl-IN-Ravi-Apollo"
`en-US` | English (US) | Kobieta | "Microsoft Server mowy Text na głos mowy (en US, ZiraRUS)" | "pl-US-ZiraRUS"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (en US, JessaRUS)" | "pl-US-JessaRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (en US, BenjaminRUS)" | "pl-US-BenjaminRUS"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (en US, Jessa24kRUS)" | "en-US-Jessa24kRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (en US, Guy24kRUS)" | "pl-US-Guy24kRUS"
`es-ES` | Hiszpański (Hiszpania) |Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo"
| | | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (es-ES, HelenaRUS)" | "es-ES-HelenaRUS"
| | | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (es-ES, urządzenia Pablo, Apollo)" | "es-ES-Pablo-Apollo"
`es-MX` | Hiszpański (Meksyk) | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosu (es-MX, HildaRUS)" | "es-MX-HildaRUS"
| | | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosu (es-MX, Raul, Apollo)" | "es-MX-Raul-Apollo"
`fi-FI` | Fiński | Kobieta | "Microsoft Server mowy Text na głos mowy (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS"
`fr-CA` | Francuski (Kanada) |Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-CA Caroline)" | "fr-CA-Caroline"
| | | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-CA HarmonieRUS)" | "fr-CA-HarmonieRUS"
`fr-CH` | Francuski (Szwajcaria)| Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-CH, Guillaume)" | "fr-CH-Guillaume"
`fr-FR` | Francuski (Francja)| Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-FR, Julie, Apollo)" | "fr-FR-Julie-Apollo"
| | | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS"
| | | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo"
`he-IL` | Hebrajski (Izrael) | Mężczyzna| "Microsoft Server mowy Text na głos mowy (he-IL Asaf)" | "IT-IL-Asaf"
`hi-IN` | Hindi (India) | Kobieta | "Microsoft Server mowy Text na głos mowy (hi-IN, Kalpana, Apollo)" | "Hi-IN-Kalpana-Apollo"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (cześć IN, Kalpana)" | "Witaj w Kalpana"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (cześć IN, Hemant)" | "Witaj w Hemant"
`hr-HR` | Chorwacki | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (hr-HR Matej)" | "HR-HR-Matej"
`hu-HU` | węgierski | Mężczyzna | "Microsoft Server mowy Text na głos mowy (hu-HU, Szabolcs)" | "hu-HU-Szabolcs"
`id-ID` | Indonezyjski| Mężczyzna | "Microsoft Server mowy Text na głos mowy (identyfikator ID, Andika)" | "ID-ID-andika"
`it-IT` | Włoski | Mężczyzna | "Microsoft Server mowy Text na głos mowy (it-IT, Cosimo, Apollo)" | "IT-Cosimo-Apollo"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (it-IT, LuciaRUS)" | "IT-LuciaRUS"
`ja-JP` | Japoński | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo"
| | | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo"
| | | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS"
`ko-KR` | Koreański | Kobieta | "Microsoft Server mowy Text na głos mowy (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS"
`ms-MY` | Malajski | Mężczyzna | "Głosu zamiany tekstu na mowę mowy serwera firmy Microsoft (ms Moje, Rizwan)" | "MS-MY-Rizwan"
`nb-NO` | Norweski | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosu (nb — bez HuldaRUS)" | "nb-NO-HuldaRUS"
`nl-NL` | Holenderski | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (nl-NL, HannaRUS)" | "nl-NL-HannaRUS"
`pl-PL` | Polski | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS"
`pt-BR` | Portugalski (Brazylia) | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (pt-BR, HeloisaRUS)" | "pt-BR-HeloisaRUS"
| | | Mężczyzna |"Microsoft Server mowy zamiany tekstu na mowę głosowych (pt-BR, Daniel, Apollo)" | "pt-BR-Daniel-Apollo"
`pt-PT` | Portugalski (Portugalia) | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (pt-PT, HeliaRUS)" | "pt-PT-HeliaRUS"
`ro-RO` | Rumuński | Mężczyzna | "Microsoft Server mowy Text na głos mowy (ro-RO Andrei)" | "RO-RO-Andrei"
`ru-RU` |Rosyjski| Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo"
| | | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (Apollo Pavel, ru-RU)" | "ru-RU-Pavel-Apollo"
| | | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (ru-RU, EkaterinaRUS)" | ru — RU — EkaterinaRUS
`sk-SK` | Słowacki | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (sk-SK Filip)" | "sk-SK-Filip"
`sl-SI` | Słoweński | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (sl-SI Lado)" | "SL-SI-Lado"
`sv-SE` | Szwedzki | Kobieta | "Microsoft Server mowy Text na głos mowy (sv-SE, HedvigRUS)" | "SV-SE-HedvigRUS"
`ta-IN` | Tamilski (Indie) | Mężczyzna | "Microsoft Server mowy Text na głos mowy (ta-IN, Valluvar)" | "Ta-IN-Valluvar"
`te-IN` | Telugu (Indie) | Kobieta | "Microsoft Server mowy Text na głos mowy (t IN Chitra)" | "te w Chitra"
`th-TH` | Tajlandzki | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (th-TH Pattara)" | "th-TH-Pattara"
`tr-TR` | Turecki (Turcja) | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (tr-TR, SedaRUS)" | "TR-TR-SedaRUS"
`vi-VN` | Wietnamski | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosu (vi-VN)" | "VI-VN-an"
`zh-CN` | Chiński (kontynent) | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo"
`zh-HK` | Chiński (SRA Hongkong) | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-HK Tracy, Apollo)" | "zh-HK-Tracy-Apollo"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-HK TracyRUS)" | "zh-HK-TracyRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (zh-HK Danny, Apollo)" | "zh-HK-Danny-Apollo"
`zh-TW` | Chiński (Tajwan) | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-TW HanHanRUS)" | "zh-TW-HanHanRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo"

**1** *AR-EG obsługuje nowoczesne standardowe arabski (MSA).*

> [!NOTE]
> Możesz użyć pełnego mapowania nazw usług lub krótkiej nazwy głosu w żądaniach syntezy mowy.

### <a name="customization"></a>Dostosowywanie

Dostosowanie głosu jest dostępne dla `de-DE`, `en-GB`, `en-IN`, `en-US`, `es-MX`, `fr-FR`, `it-IT`, `pt-BR`i `zh-CN`. Wybierz odpowiednie ustawienia regionalne, które pasują do danych szkoleniowych, które są potrzebne do uczenia niestandardowego modelu głosu. Na przykład jeśli dane dotyczące nagrywania są wymawiane w języku angielskim z akcentem brytyjskim, wybierz pozycję `en-GB`.

> [!NOTE]
> Nie obsługujemy szkolenia modelu dwujęzykowego w głosowaniu niestandardowym, z wyjątkiem języka chińskiego w języku angielskim. Wybierz pozycję "dwujęzyczne w języku chińskim English", jeśli chcesz nauczyć się nauczenie języka chińskiego w języku angielskim. Szkolenia głosowe we wszystkich ustawieniach regionalnych zaczynają się od zestawu danych 2000 + wyrażenia długości, z wyjątkiem `en-US` i `zh-CN`, gdzie można zacząć od dowolnego rozmiaru danych szkoleniowych.

## <a name="speech-translation"></a>Tłumaczenie mowy

**Tłumaczenia mowy** API obsługę innych języków tłumaczenia mowy do rozpoznawania mowy i rozpoznawania mowy na tekst. Język źródłowy musi być zawsze z tabeli języka zamiany mowy na tekst. Dostępne języki docelowej zależą od tego, czy element docelowy tłumaczenia jest mowy lub tekstu. Może być tłumaczenie mowy przychodzących do więcej niż [60 języków](https://www.microsoft.com/translator/business/languages/). Podzbiór języków jest dostępny dla [syntezy mowy](language-support.md#text-languages).

### <a name="text-languages"></a>Tekstu/wszystkie języki

| Język tekstu    | Kod języka |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Arabski       | `ar`          |
| Bengalski      | `bn`          |
| Bośniacki (łaciński)      | `bs`          |
| Bułgarski      | `bg`          |
| Kantoński (tradycyjny)      | `yue`          |
| Kataloński      | `ca`          |
| Chiński uproszczony      | `zh-Hans`          |
| Chiński tradycyjny      | `zh-Hant`          |
| Chorwacki      | `hr`          |
| Czeski      | `cs`          |
| Duński      | `da`          |
| Holenderski      | `nl`          |
| Polski      | `en`          |
| Estoński      | `et`          |
| Fidżi      | `fj`          |
| Filipino      | `fil`          |
| Fiński      | `fi`          |
| Francuski      | `fr`          |
| Niemiecki      | `de`          |
| Grecki      | `el`          |
| Haitański      | `ht`          |
| Hebrajski      | `he`          |
| Hindi      | `hi`          |
| Hmong Daw      | `mww`          |
| węgierski      | `hu`          |
| Indonezyjski      | `id`          |
| Irlandzki      | `ga`          |
| Włoski      | `it`          |
| Japoński      | `ja`          |
| Kannada      | `kn`          |
| Suahili      | `sw`          |
| Klingon      | `tlh`          |
| Klingon (plqaD)      | `tlh-Qaak`          |
| Koreański      | `ko`          |
| Łotewski      | `lv`          |
| Litewski      | `lt`          |
| Malgaski      | `mg`          |
| Malajski      | `ms`          |
| Malajalam      | `ml`          |
| Maltański      | `mt`          |
| Norweski      | `nb`          |
| Perski      | `fa`          |
| Polski      | `pl`          |
| Portugalski (Brazylia)      | `pt-br`          |
| Portugalski (Portugalia)      | `pt-pt`          |
| Punjabi      | `pa`          |
| Queretaro Otomi      | `otq`          |
| Rumuński      | `ro`          |
| Rosyjski      | `ru`          |
| (Samoa)      | `sm`          |
| Serbski (Cyrylica)      | `sr-Cyrl`          |
| Serbski (łaciński)      | `sr-Latn`          |
| Słowacki     | `sk`          |
| Słoweński      | `sl`          |
| Hiszpański      | `es`          |
| Szwedzki      | `sv`          |
| Tahitian      | `ty`          |
| Tamilski      | `ta`          |
| Telugu      | `te`          |
| Tajlandzki      | `th`          |
| Pa'anga      | `to`          |
| Turecki      | `tr`          |
| Ukraiński      | `uk`          |
| Urdu      | `ur`          |
| Wietnamski      | `vi`          |
| Walijski      | `cy`          |
| Yucatec Maya      | `yua`          |


## <a name="next-steps"></a>Następne kroki

* [Pobierz subskrypcję wersji próbnej usługi Speech Service](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz, jak rozpoznawanie mowy w języku C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
