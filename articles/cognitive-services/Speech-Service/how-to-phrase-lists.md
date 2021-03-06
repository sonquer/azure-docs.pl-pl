---
title: Listy fraz — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak dostarczać usługę mowy z listą fraz przy użyciu obiektu `PhraseListGrammar`, aby poprawić wyniki rozpoznawania zamiany mowy na tekst.
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: rhurey
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 2ceb53b50810aef501278710ae990c57fc45030c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74971676"
---
# <a name="phrase-lists-for-speech-to-text"></a>Listy fraz dla zamiany mowy na tekst

Dostarczając usługę mowy z listą fraz, można poprawić dokładność rozpoznawania mowy. Listy fraz są używane do identyfikowania znanych fraz w danych audio, takich jak nazwa osoby lub określonej lokalizacji.

Jeśli na przykład masz polecenie "Przenieś do" i możliwe miejsce docelowe "do wewnątrz", które może być wymawiane, możesz dodać wpis "przejdź do samego". Dodanie frazy spowoduje zwiększenie prawdopodobieństwa, że gdy dźwięk zostanie rozpoznany, że "Przenieś do wewnątrz" zostanie rozpoznany zamiast "Przenieś w stronę".

Pojedyncze słowa lub kompletne wyrażenia można dodać do listy fraz. Podczas rozpoznawania, jest używany wpis na liście frazy, jeśli dokładne dopasowanie dla całej frazy jest dołączane do dźwięku jako oddzielnej frazy. Jeśli nie znaleziono dokładnego dopasowania do frazy, rozpoznawanie nie jest wspierane.

>[!Note]
> Obecnie listy fraz obsługują tylko język angielski dla zamiany mowy na tekst.

## <a name="how-to-use-phrase-lists"></a>Jak używać list fraz

Poniższe przykłady ilustrują sposób tworzenia listy fraz przy użyciu obiektu `PhraseListGrammar`.

::: zone pivot="programming-language-csharp"

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

::: zone-end

::: zone pivot="programming-language-more"

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

>[!Note]
> Maksymalna liczba list fraz, które będą używane przez usługę mowy do dopasowania mowy, to 1024 fraz.

Możesz również wyczyścić frazy skojarzone z `PhraseListGrammar` przez wywołanie Clear ().

::: zone pivot="programming-language-csharp"

```cs
phraseList.Clear();
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
phraselist->Clear();
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
phraseListGrammar.clear();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar.clear()
```

::: zone-end

::: zone pivot="programming-language-more"

```JavaScript
phraseListGrammar.clear();
```

::: zone-end

> [!NOTE]
> Zmiany w obiekcie `PhraseListGrammar` zaczną obowiązywać przy następnym rozpoznaniu lub po ponownym nawiązaniu połączenia z usługą mowy.

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja referencyjna zestawu Speech SDK](speech-sdk.md)
