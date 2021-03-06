---
title: Jak skalować środowisko — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak skalować środowisko Azure Time Series Insights przy użyciu Azure Portal.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 23efda2793ef5d323089ee5b72fb1ea873de6b20
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310989"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Jak skalować środowisko Time Series Insights

W tym artykule opisano sposób zmiany pojemności środowiska Time Series Insights przy użyciu [Azure Portal](https://portal.azure.com). Pojemność to mnożnik stosowany do szybkości transferu danych przychodzących, pojemności magazynu i kosztu związanego z wybraną jednostką SKU.

Za pomocą Azure Portal można zwiększyć lub zmniejszyć pojemność w ramach danej jednostki SKU cenowej.

Jednak zmiana jednostki SKU warstwy cenowej jest niedozwolona. Na przykład środowisko z jednostką SKU cen S1 nie może być konwertowane na S2 lub odwrotnie.

## <a name="ga-limits"></a>Limity GA

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>Zmień pojemność środowiska

1. W Azure Portal Znajdź i wybierz środowisko Time Series Insights.

1. W menu środowiska Time Series Insights wybierz pozycję **Konfiguracja magazynu**.

   [![skonfigurować pojemność Time Series Insights](media/scale-your-environment/scale-your-environment-configure.png)](media/scale-your-environment/scale-your-environment-configure.png#lightbox)

1. Dostosuj suwak **pojemności** , aby wybrać pojemność, która spełnia wymagania dotyczące stawek za transfer danych przychodzących i pojemności magazynu. Zwróć uwagę, że **szybkość**transferu danych przychodzących, **pojemność magazynu**i **Szacowana aktualizacja kosztu** są dynamiczne, aby pokazać wpływ zmiany.

   [![skonfigurować środowisko przy użyciu suwaka pojemności](media/scale-your-environment/scale-your-environment-slider.png)](media/scale-your-environment/scale-your-environment-slider.png#lightbox)

   Alternatywnie można wpisać liczbę mnożnika pojemności do pola tekstowego po prawej stronie suwaka.

1. Wybierz pozycję **Zapisz** , aby skalować środowisko. Wskaźnik postępu jest wyświetlany do momentu, aż zmiana zostanie zatwierdzona.

1. Sprawdź, czy nowa pojemność jest [wystarczająca, aby zapobiec ograniczaniu wydajności](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, zapoznaj się [z tematem przechowywanie w Time Series Insights](time-series-insights-concepts-retention.md).

- Dowiedz się więcej o [konfigurowaniu przechowywania danych w Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

- Dowiedz się więcej [na temat planowania środowiska](time-series-insights-environment-planning.md).