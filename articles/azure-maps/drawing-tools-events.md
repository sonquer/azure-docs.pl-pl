---
title: Dodawanie paska narzędzi Rysowanie do mapy | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak dodać pasek narzędzi Rysowanie do mapy przy użyciu zestawu Microsoft Azure Web SDK usługi Maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: cf9c79f608aa3ffd1137be41ff3348f62b890867
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198313"
---
# <a name="drawing-tool-events"></a>Zdarzenia narzędzia do rysowania

W przypadku korzystania z narzędzi do rysowania na mapie warto reagować na niektóre zdarzenia, gdy użytkownik rysuje na mapie. W tej tabeli wymieniono wszystkie zdarzenia obsługiwane przez klasę `DrawingManager`.

| Wydarzenie | Opis |
|-------|-------------|
| `drawingchanged` | Uruchamiany, gdy dodaliśmy lub zmieniono dowolna Współrzędna kształtu. | 
| `drawingchanging` | Uruchamiany, gdy zostanie wyświetlona Współrzędna podglądu dla kształtu. Na przykład to zdarzenie zostanie wyzwolone wiele razy, gdy Współrzędna zostanie przeciągnięta. | 
| `drawingcomplete` | Uruchamiany po zakończeniu rysowania lub wyjęciu kształtu w trybie edycji. |
| `drawingmodechanged` | Uruchamiany po zmianie trybu rysowania. Nowy tryb rysowania jest przenoszona do procedury obsługi zdarzeń. |
| `drawingstarted` | Uruchamiany, gdy użytkownik rozpocznie Rysowanie kształtu lub umieszcza kształt w trybie edycji.  |

Poniższy kod przedstawia sposób działania zdarzeń w module narzędzi do rysowania. Rysuj kształty na mapie i obserwuj je jako pożarowe zdarzenia.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Zdarzenia narzędzi do rysowania" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>zdarzenia narzędzi do rysowania</a> piórem według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="examples"></a>Przykłady

Zapoznaj się z typowymi scenariuszami korzystającymi z zdarzeń narzędzi do rysowania.

### <a name="select-points-in-polygon-area"></a>Wybierz punkty w obszarze wielokąta

Ten kod demonstruje sposób monitorowania zdarzenia kształtów rysowania przez użytkownika. W tym przykładzie kod monitoruje kształty wielokątów, prostokątów i okręgów. Następnie określa, które punkty danych na mapie znajdują się w obszarze rysunku. Zdarzenie `drawingcomplete` służy do wyzwalania logiki SELECT. W ramach logiki SELECT kod przechodzi przez wszystkie punkty danych na mapie. Sprawdza, czy istnieje część przecięcia punktu i obszaru rysowanego kształtu. W tym przykładzie użyto biblioteki typu open source [Turf. js](https://turfjs.org/) do wykonania obliczeń między częściami przestrzennymi.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Wybierz dane w wyrysowanym obszarze wielokątów" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zapoznaj się z piórem <a href='https://codepen.io/azuremaps/pen/XWJdeja'>Wybierz dane w wyrysowanym obszarze wielokątów</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Rysowanie i wyszukiwanie w obszarze wielokąta

Ten kod wyszukuje punkty zainteresowań w obszarze kształtu po zakończeniu rysowania kształtu przez użytkownika. Możesz zmodyfikować i wykonać kod, klikając pozycję "Edytuj przy użyciu pióra kodu" w prawym górnym rogu ramki. Zdarzenie `drawingcomplete` służy do wyzwalania logiki wyszukiwania. Jeśli użytkownik rysuje prostokąt lub Wielokąt, wykonywane jest wyszukiwanie wewnątrz geometrii. Jeśli okrąg jest rysowany, pozycja promień i środek zostanie użyta do przeprowadzenia wyszukiwania w punkcie zainteresowania. Zdarzenie `drawingmodechanged` służy do określenia, kiedy użytkownik przełącza się do trybu rysowania, a to zdarzenie czyści kanwę rysunku.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rysowanie i wyszukiwanie w obszarze wielokąta" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>rysowanie i wyszukiwanie piórem w obszarze wielokąta</a> , Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Tworzenie narzędzia pomiarowego

Poniższy kod pokazuje, jak można użyć zdarzeń rysowania do utworzenia narzędzia pomiarowego. `drawingchanging` służy do monitorowania kształtu w miarę jego rysowania. Gdy użytkownik przesuwa mysz, są obliczane wymiary kształtu. Zdarzenie `drawingcomplete` służy do wykonywania ostatecznego obliczenia na kształcie po jego narysowaniu. Zdarzenie `drawingmodechanged` służy do ustalania, kiedy użytkownik przechodzi do trybu rysowania. Ponadto zdarzenie `drawingmodechanged` czyści kanwę rysowania i czyści informacje o starych pomiarach.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Narzędzie pomiarowe" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>Narzędzie pomiar</a> piórem Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak używać dodatkowych funkcji modułu narzędzi do rysowania:

> [!div class="nextstepaction"]
> [Pobierz dane kształtu](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Typy interakcji i skróty klawiaturowe](drawing-tools-interactions-keyboard-shortcuts.md)

Dowiedz się więcej o module usług:

> [!div class="nextstepaction"]
> [Moduł usług](how-to-use-services-module.md)

Zapoznaj się z dodatkowymi przykładami kodu:

> [!div class="nextstepaction"]
> [Przykładowa strona kodowa](https://aka.ms/AzureMapsSamples)
