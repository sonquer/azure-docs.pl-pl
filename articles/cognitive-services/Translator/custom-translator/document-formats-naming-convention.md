---
title: Formaty dokumentów i konwencje nazewnictwa — translator niestandardowy
titleSuffix: Azure Cognitive Services
description: Jest to przewodnik dotyczący formatów dokumentów i konwencji nazewnictwa w Translatoru niestandardowym. Takie koncepcje ułatwiają zarządzanie nazwami dokumentów lepszy Abd unikanie konfliktów nazw.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 41b15cc998a7bacd033ef2fe083fc99f1bff0286
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595854"
---
# <a name="document-formats-and-naming-convention-guidance"></a>Wskazówki dotyczące formatów dokumentów i konwencji nazewnictwa

Każdy plik używany do tłumaczenia niestandardowego musi mieć co najmniej **cztery** znaki.

Ta tabela zawiera wszystkie obsługiwane formaty plików, których można użyć do kompilowania systemu Tłumaczenia:

| Format            | Rozszerzenia   | Opis                                                                                                                                                                                                                                                                    |
|-------------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| XLIFF             | . XLF, . XLIFF | Format dokumentu równoległego, eksportowanie systemów pamięci translacji. Używane języki są zdefiniowane w pliku.                                                                                                                                                              |
| TMX               | .TMX         | Format dokumentu równoległego, eksportowanie systemów pamięci translacji. Używane języki są zdefiniowane w pliku.                                                                                                                                                              |
| ZIP               | .ZIP         | ZIP to format pliku archiwum.                                                                                                                                                                                                        |
| Locstudio         | . LCL         | Format Microsoft dla dokumentów równoległych                                                                                                                                                                                                                                      |
| Microsoft Word    | .DOCX        | Dokument programu Microsoft Word                                                                                                                                                                                                                                                        |
| Program Adobe Acrobat     | .PDF         | Przenośny dokument programu Adobe Acrobat                                                                                                                                                                                                                                                |
| HTML              | . HTML,. HTM  | Dokument HTML                                                                                                                                                                                                                                                                  |
| Plik tekstowy         | .TXT         | Pliki tekstowe kodowane w formacie UTF-16 lub UTF-8. Nazwa pliku nie może zawierać znaków japońskich.                                                                                                                                                                                        |
| Wyrównany plik tekstowy | . DOSTOSOWANIA       | Rozszerzenie `.ALIGN` jest specjalnym rozszerzeniem, którego można użyć, Jeśli wiesz, że zdania w parze dokumentu są idealnie wyrównane. Jeśli postanowisz `.ALIGN` plik, translator niestandardowy nie będzie wyrównuje zdań. |
| Plik programu Excel        | .XLSX        | Plik programu Excel (2013 lub nowszy). Pierwszym wierszem/wierszem arkusza kalkulacyjnego powinien być kod języka.                                                                                                                                                                                                                                                      |

## <a name="dictionary-formats"></a>Formaty słownika

W przypadku słowników translator niestandardowy obsługuje wszystkie formaty plików, które są obsługiwane w przypadku zestawów szkoleniowych. Jeśli używasz słownika programu Excel, pierwszym wierszem/wierszem arkusza kalkulacyjnego powinien być kod języka.

## <a name="zip-file-formats"></a>Formaty plików zip

Dokumenty mogą być pogrupowane w jednym pliku zip i przekazywane. Niestandardowa aplikacja obsługuje formaty plików zip (ZIP, GZ i TGZ).

Każdy dokument w pliku zip z rozszerzeniem TXT, HTML, HTM, PDF, DOCX, ALIGN musi być zgodny z tą konwencją nazewnictwa:

{Nazwa dokumentu} \_{Language Code}, gdzie {Document Name} jest nazwą dokumentu, {language Code} to ISO LanguageID (dwa znaki) wskazujący, że dokument zawiera zdania w tym języku. Przed kodem języka musi istnieć podkreślenie (_).

Na przykład aby przekazać dwa dokumenty równoległe w pliku ZIP dla języka angielskiego do hiszpańskiego, pliki powinny mieć nazwę "data_en" i "data_es".

Pliki pamięci translacji (TMX, XLF, XLIFF, LCL, XLSX) nie są wymagane do przestrzegania określonej konwencji nazewnictwa języka.  

## <a name="next-steps"></a>Następne kroki

- Przeczytaj o [projekcie](workspace-and-project.md#what-is-a-custom-translator-project) , aby utworzyć i zarządzać nimi.
