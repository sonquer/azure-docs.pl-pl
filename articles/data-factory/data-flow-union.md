---
title: Mapowanie przekształceń Unii danych
description: Azure Data Factory mapowanie nowej gałęzi przepływu danych
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/12/2019
ms.openlocfilehash: adba1eb61676dbebcb356490b14b279ebe69c644
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930159"
---
# <a name="azure-data-factory-mapping-data-flow-union-transformation"></a>Przekształcenie Unii danych mapowania Azure Data Factory

Unia łączy wiele strumieni danych w jeden, wraz z Unią SQL dla tych strumieni jako nowe dane wyjściowe z transformacji Union. Cały schemat z każdego strumienia wejściowego zostanie połączony wewnątrz przepływu danych, bez konieczności posiadania klucza sprzężenia.

Można połączyć n-liczbowe strumienie w tabeli ustawień, wybierając ikonę "+" obok każdego skonfigurowanego wiersza, w tym zarówno dane źródłowe, jak i strumienie z istniejących transformacji w przepływie danych.

![Przekształcenie Unii](media/data-flow/union.png "Unia")

W takim przypadku można połączyć różne metadane z wielu źródeł (w tym przykładzie trzy różne pliki źródłowe) i połączyć je w jeden strumień:

![Przegląd transformacji Union](media/data-flow/union111.png "Unia 1")

Aby to osiągnąć, Dodaj dodatkowe wiersze w ustawieniach Unii, dołączając wszystkie źródła, które chcesz dodać. Nie ma potrzeby wspólnego wyszukiwania lub klucza sprzężenia:

![Ustawienia transformacji Union](media/data-flow/unionsettings.png "Ustawienia Unii")

Jeśli ustawisz transformację wybraną po Unii, będziesz mieć możliwość zmiany nazwy nakładających się pól lub pól, które nie zostały nazwane ze źródeł bez nagłówka. Kliknij pozycję "Zbadaj", aby zobaczyć kolumny łącz łączne z 132 w tym przykładzie z trzech różnych źródeł:

![Ostatni przekształcenie Unii](media/data-flow/union333.png "Unia 3")

## <a name="name-and-position"></a>Nazwa i pozycja

Po wybraniu opcji "Sumuj według nazwy" Każda wartość kolumny zostanie połączona z odpowiednią kolumną z każdego źródła, z nowym połączonym schematem metadanych.

Jeśli wybierzesz pozycję "Union by position", każda wartość kolumny zostanie połączona do oryginalnej pozycji z każdego odpowiadającego źródła, co spowoduje powstanie nowego połączonego strumienia danych, w którym dane z każdego źródła są dodawane do tego samego strumienia:

![Dane wyjściowe złożenia](media/data-flow/unionoutput.png "Dane wyjściowe złożenia")

## <a name="next-steps"></a>Następne kroki

Eksploruj podobne przekształcenia, w tym [Join](data-flow-join.md) i [EXISTS](data-flow-exists.md).
