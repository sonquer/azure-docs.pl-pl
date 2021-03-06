---
title: Często zadawane pytania — translator niestandardowy
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Cognitive Services Custom translator.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: reference
ms.openlocfilehash: 97d399f4a8ec704fd90eb6c49f0835be7e9e4537
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73836591"
---
# <a name="custom-translator-frequently-asked-questions"></a>Często zadawane pytania dotyczące translatora niestandardowego

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące [translatora niestandardowego](https://portal.customtranslator.azure.ai).

## <a name="what-are-the-current-restrictions-in-custom-translator"></a>Jakie są bieżące ograniczenia w Translatoru niestandardowym?

Istnieją ograniczenia i ograniczenia dotyczące rozmiaru plików, szkoleń modeli i wdrażania modelu. Należy pamiętać o tych ograniczeniach podczas konfigurowania szkolenia, aby utworzyć model w usłudze translator niestandardowym.

- Przesłane pliki muszą mieć rozmiar mniejszy niż 100 MB.
- Dane w postaci dejęzykowej nie są obsługiwane.

## <a name="when-should-i-request-deployment-for-a-translation-system-that-has-been-trained"></a>Kiedy należy zażądać wdrożenia dla systemu tłumaczenia, który został przeszkolony?

Utworzenie optymalnego systemu tłumaczenia dla projektu może zająć kilka szkoleń. Możesz chcieć spróbować użyć większej ilości danych szkoleniowych lub dokładniej przefiltrowanych danych, jeśli wynik BLEU i/lub wyniki testu nie są zadowalające. Należy mieć rygorystyczne i staranne projektowanie zestawu strojenia oraz zestawu testów, aby być w pełni reprezentatywne dla terminologii i stylu materiału, który ma zostać przekształcony. Możesz być bardziej zliberalizowany w tworzeniu danych szkoleniowych i eksperymentować z innymi opcjami. Zażądaj wdrożenia systemu w przypadku zadowalającego tłumaczenia wyników testów systemu, nie dodawaj więcej danych do szkolenia, aby usprawnić przeszkolony system i uzyskać dostęp do przeszkolonego modelu za pośrednictwem interfejsów API.

## <a name="how-many-trained-systems-can-be-deployed-in-a-project"></a>Ile przeszkolonych systemów można wdrożyć w projekcie?

Dla każdego projektu można wdrożyć tylko jeden przeszkolony system. Utworzenie odpowiedniego systemu tłumaczeń dla projektu może zająć kilka szkoleń i zachęcamy do zażądania wdrożenia szkolenia, które daje najlepszy wynik. Możesz określić jakość szkolenia według wyniku BLEU (lepszym rozwiązaniem jest lepsza) i konsultacji z recenzentami przed podjęciem decyzji, że jakość tłumaczeń jest odpowiednia do wdrożenia.

## <a name="when-can-i-expect-my-trainings-to-be-deployed"></a>Kiedy mogę oczekiwać, że moje szkolenia zostaną wdrożone?

Wdrożenie zazwyczaj trwa krócej niż godzinę.

## <a name="how-do-you-access-a-deployed-system"></a>Jak uzyskać dostęp do wdrożonego systemu?

Do wdrożonych systemów można uzyskać dostęp za pośrednictwem programu Microsoft interfejs API tłumaczenia tekstu w usłudze Translator v3, określając IDKategorii. Więcej informacji na temat interfejs API tłumaczenia tekstu w usłudze Translator można znaleźć na stronie sieci Web [dokumentacji interfejsu API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) .

## <a name="how-do-i-skip-alignment-and-sentence-breaking-if-my-data-is-already-sentence-aligned"></a>Jak mogę pominąć wyrównywanie i przerywanie zdania, jeśli moje dane są już wyrównane do zdania?

Niestandardowa translator pomija wyrównanie zdania i przerywanie zdania dla plików TMX oraz dla plików tekstowych z rozszerzeniem `.align`. `.align` pliki zapewniają użytkownikom opcję pomijania procesu rozdzielania i wyrównania w przypadku plików, które są idealnie wyrównane i nie wymagają dalszej obróbki. Zalecamy używanie rozszerzenia `.align` tylko w przypadku plików, które są idealnie wyrównane.

Jeśli liczba wyodrębnionych zdań nie pasuje do obu plików o tej samej nazwie podstawowej, usługa translatora niestandardowego będzie nadal uruchamiać program do `.align` plików.

## <a name="i-tried-uploading-my-tmx-but-it-says-document-processing-failed"></a>Próbowaliśmy przekazać moją TMX, ale jest to komunikat "Przetwarzanie dokumentu nie powiodło się".

Upewnij się, że TMX jest zgodna ze specyfikacją TMX 1.4 b w <https://www.gala-global.org/tmx-14b>.
