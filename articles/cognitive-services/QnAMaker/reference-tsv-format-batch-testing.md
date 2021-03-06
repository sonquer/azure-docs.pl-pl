---
title: Format TSV testu wsadowego — QnA Maker
titleSuffix: Azure Cognitive Services
description: Informacje o formacie TSV dla testów wsadowych
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: dccf034b6497651db70b6cc09fff3f1b1d645a1b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507826"
---
# <a name="batch-testing-tsv-format"></a>Format TSV testów wsadowych

Testowanie wsadowe jest dostępne z [kodu źródłowego](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting) lub jako [plik wykonywalny do pobrania](https://aka.ms/qna_btzip). Format polecenia do uruchomienia testu wsadowego to:

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|Param|Oczekiwana wartość|
|--|--|
|1|Nazwa pliku TSV sformatowana przy użyciu [pól wejściowych TSV](#tsv-input-fields)|
|2|Identyfikator URI dla punktu końcowego z HOSTem — ze strony publikowania w portalu QnA Maker.|
|3|KLUCZ punktu końcowego znajduje się na stronie publikowania w portalu QnA Maker.|
|4|Nazwa pliku TSV utworzonego przez test wsadowy dla wyników.|

Skorzystaj z poniższych informacji, aby zrozumieć i zaimplementować format TSV dla testów wsadowych. 

## <a name="tsv-input-fields"></a>Pola wejściowe TSV

|Pola wejściowe plików TSV|Uwagi|
|--|--|
|KBID|Na stronie publikowania znaleziono identyfikator bazy wiedzy.|
|Pytanie|Pytanie, które użytkownik wprowadzi.|
|Tagi metadanych|Obowiązkowe|
|Górny parametr|Obowiązkowe| 
|Oczekiwany identyfikator odpowiedzi|Obowiązkowe|

![Format danych wejściowych dla pliku TSV na potrzeby testowania wsadowego.](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>Pola wyjściowe TSV 

|Parametry pliku wyjściowego TSV|Uwagi|
|--|--|
|KBID|Na stronie publikowania znaleziono identyfikator bazy wiedzy.|
|Pytanie|Pytanie wprowadzone w pliku wejściowym.|
|Odpowiedź|Najważniejsze odpowiedzi z bazy wiedzy.|
|Identyfikator odpowiedzi|Identyfikator odpowiedzi|
|Wynik|Wynik przewidywania dla odpowiedzi. |
|Tagi metadanych|skojarzone z zwróconą odpowiedzią|
|Oczekiwany identyfikator odpowiedzi|opcjonalne (tylko gdy jest podawany oczekiwany identyfikator odpowiedzi)|
|Etykieta orzeczenia|Opcjonalne wartości mogą być następujące: poprawne lub nieprawidłowe (tylko w przypadku podanej oczekiwanej odpowiedzi)|
