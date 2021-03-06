---
title: Istnieje transformacja przepływu danych mapowania
description: Sprawdzanie istniejących wierszy przy użyciu transformacji EXISTS w Azure Data Factory mapowaniu przepływu danych
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: efcc45dcf3565b70305323701810c49c4a720394
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930406"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Istnieje transformacja przepływu danych mapowania

Transformacja EXISTS to transformacja filtrowania wierszy, która sprawdza, czy dane istnieją w innym źródle lub strumieniu. Strumień wyjściowy zawiera wszystkie wiersze w lewym strumieniu, który istnieje lub nie istnieje w odpowiednim strumieniu. Transformacja istnieje podobna do ```SQL WHERE EXISTS``` i ```SQL WHERE NOT EXISTS```.

## <a name="configuration"></a>Konfigurowanie

1. Wybierz, który strumień danych ma być używany na liście rozwijanej **odpowiedniego strumienia** .
1. Określ, czy chcesz, aby dane były już istniejące, czy nie istnieją w ustawieniu **typu istnieją** .
1. Wybierz, czy chcesz utworzyć **wyrażenie niestandardowe**.
1. Wybierz kolumny klucza, które mają zostać porównane jako warunki istnienia. Domyślnie przepływ danych wyszukuje równość między jedną kolumną w każdym strumieniu. Aby porównać przez obliczoną wartość, umieść kursor na liście rozwijanej kolumny i wybierz **kolumnę obliczaną**.

![Istnieją ustawienia](media/data-flow/exists.png "Istnieje 1")

### <a name="multiple-exists-conditions"></a>Istnieje wiele warunków

Aby porównać wiele kolumn z każdego strumienia, Dodaj warunek "istnieje", klikając ikonę znaku plus obok istniejącego wiersza. Każdy dodatkowy warunek jest przyłączony do instrukcji "i". Porównywanie dwóch kolumn jest takie samo jak następujące wyrażenie:

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Wyrażenie niestandardowe

Aby utworzyć wyrażenie o dowolnej postaci zawierające operatory inne niż "i" i "Equals do", zaznacz pole **wyrażenie niestandardowe** . Wprowadź wyrażenie niestandardowe za pośrednictwem konstruktora wyrażeń przepływu danych, klikając niebieskie pole.

![Istnieją ustawienia niestandardowe](media/data-flow/exists1.png "Istnieje niestandardowe")

## <a name="data-flow-script"></a>Skrypt przepływu danych

### <a name="syntax"></a>Składnia

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: {'none' | 'left' | 'right' | 'both'}
    ) ~> <existsTransformationName>
```

### <a name="example"></a>Przykład

Poniższy przykład jest przekształceniem o nazwie `checkForChanges`, które pobiera lewe `NameNorm2` strumienia i właściwe `TypeConversions`przesyłania strumieniowego.  Warunek EXISTS jest wyrażeniem, `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` zwraca wartość true, jeśli zarówno kolumny `EMPID`, jak i `Region` w poszczególnych strumieniach pasują do siebie. Gdy sprawdzimy obecność, `negate` ma wartość false. Nie włączamy żadnej emisji na karcie Optymalizacja, więc `broadcast` ma `'none'`wartości.

W Data Factory środowisku użytkownika Ta transformacja wygląda jak na poniższym obrazie:

![Istnieje przykład](media/data-flow/exists-script.png "Istnieje przykład")

Skrypt przepływu danych dla tego przekształcenia znajduje się w poniższym fragmencie kodu:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'none'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Następne kroki

Podobne przekształcenia to [Wyszukiwanie](data-flow-lookup.md) i [sprzężenie](data-flow-join.md).
