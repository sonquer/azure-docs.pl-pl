---
title: Obsługa zdarzeń mapy | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak utworzyć interaktywną mapę sieci Web SDK ze zdarzeniami mapy przy użyciu zestawu Microsoft Azure Web SDK mapy.
author: jingjing-z
ms.author: jinzh
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 06fdb9733ad9c173c88936e5502214b58f5d10ee
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985921"
---
# <a name="interact-with-the-map"></a>Korzystanie z mapy

W tym artykule pokazano, jak używać [klasy zdarzeń map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events). Właściwość wyróżniania zdarzeń na mapie i na różnych warstwach mapy. Możesz również wyróżnić zdarzenia podczas korzystania z znacznika HTML.

## <a name="interact-with-the-map"></a>Korzystanie z mapy

Odtwórz przy użyciu mapy poniżej i zobacz odpowiednie zdarzenia myszy wyróżnione po prawej stronie. Możesz kliknąć **kartę js** , aby wyświetlić i edytować kod JavaScript. Możesz również kliknąć pozycję **Edytuj na CodePen** , aby zmodyfikować kod w CodePen.

<br/>

<iframe height='600' scrolling='no' title='Korzystanie z mapy — zdarzenia myszy' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z piórem, korzystając <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>z zdarzeń dotyczących mapy — wskaźnik myszy</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Korzystanie z warstw mapy

Poniższy kod wyróżnia wywoływane zdarzenie podczas korzystania z warstwy symboli. Warstwa symbol, bąbelki, linia i Wielokąt obsługują ten sam zestaw zdarzeń. Warstwy mapy cieplnej i kafelki nie obsługują żadnego z tych zdarzeń.

<br/>

<iframe height='600' scrolling='no' title='Korzystanie z zdarzeń mapy — zdarzenia warstwy' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z piórem, korzystając <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>z zdarzeń mapy — zdarzenia warstwy</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-html-marker"></a>Korzystanie z znacznika HTML

Poniższy kod dodaje zdarzenia mapy JavaScript do znacznika HTML. Wyróżnia także nazwę zdarzeń, które są wyzwalane podczas korzystania z znacznika HTML.

<br/>

<iframe height='500' scrolling='no' title='Korzystanie z zdarzeń mapy — znaczniki HTML' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz, jak <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>korzystać z pióra z zdarzeniami znacznika mapy kodu HTML</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

W poniższej tabeli wymieniono wszystkie obsługiwane zdarzenia klas mapy.

| Wydarzenie               | Opis |
|---------------------|-------------|
| `boxzoomend`        | Uruchamiany po zakończeniu interakcji "pole Powiększ".|
| `boxzoomstart`      | Uruchamiany po rozpoczęciu interakcji "Box zoom".|
| `click`             | Uruchamiany, gdy urządzenie wskazujące zostanie naciśnięte i wydane w tym samym punkcie na mapie.|
| `close`             | Uruchamiany, gdy menu podręczne jest zamknięte ręcznie lub programowo.|
| `contextmenu`       | Uruchamiany po kliknięciu prawego przycisku myszy.|
| `data`              | Uruchamiany, gdy dowolne dane mapy są ładowane lub zmieniane. |
| `dataadded`         | Uruchamiany, gdy kształty są dodawane do `DataSource`.|
| `dataremoved`       | Uruchamiany, gdy kształty zostaną usunięte z `DataSource`.|
| `datasourceupdated` | Uruchamiany po zaktualizowaniu obiektu `DataSource`.|
| `dblclick`          | Uruchamiany, gdy urządzenie wskazujące jest kliknięte dwukrotnie w tym samym punkcie na mapie.|
| `drag`              | Wywoływane wielokrotnie podczas interakcji "przeciągnij do kadrowania" w znaczniku mapy, popup lub HTML.|
| `dragend`           | Uruchamiany po zakończeniu interakcji "przeciągnij do kadrowania" w znaczniku mapy, popup lub HTML.|
| `dragstart`         | Uruchamiany, gdy interakcja "przeciągnij do panoram" zaczyna się w znaczniku mapy, popup lub HTML.|
| `error`             | Uruchamiany, gdy wystąpi błąd.|
| `idle`              | <p>Uruchamiany po ostatniej klatce renderowanej przed przejściem do stanu "bezczynności":<ul><li>Brak przejść do aparatu.</li><li>Wszystkie aktualnie żądane kafelki zostały załadowane.</li><li>Wszystkie Animacje zanikania/przejścia zostały ukończone.</li></ul></p>|
| `keydown`           | Uruchamiany po naciśnięciu klawisza.|
| `keypress`          | Uruchamiany, gdy zostanie naciśnięty klucz generujący znak typable (klucz ANSI).|
| `keyup`             | Uruchamiany po wydaniu klucza.|
| `layeradded`        | Uruchamiany, gdy warstwa zostanie dodana do mapy.|
| `layerremoved`      | Uruchamiany, gdy warstwa zostanie usunięta z mapy.|
| `load`              | Uruchamiany natychmiast po pobraniu wszystkich niezbędnych zasobów i wykonaniu pierwszego wizualnego renderowania mapy.|
| `mousedown`         | Uruchamiany, gdy urządzenie wskazujące zostało naciśnięte w obrębie mapy lub gdy znajduje się on na górze elementu.|
| `mouseenter`        | Uruchamiany, gdy urządzenie wskazujące jest początkowo przenoszone za pośrednictwem mapy lub elementu. |
| `mouseleave`        | Uruchamiany, gdy urządzenie wskazujące przeniesiono mapę lub element. |
| `mousemove`         | Uruchamiany, gdy urządzenie wskazujące jest przenoszone w ramach mapy lub elementu.|
| `mouseout`          | Uruchamiany, gdy urządzenie punktowe opuszcza kanwę mapy, opuszcza element.|
| `mouseover`         | Uruchamiany, gdy urządzenie wskazujące jest przenoszone za pośrednictwem mapy lub elementu.|
| `mouseup`           | Uruchamiany, gdy urządzenie wskazujące znajduje się w obrębie mapy lub gdy znajduje się na szczycie elementu.|
| `move`              | Uruchamiany wielokrotnie podczas animowanego przejścia z jednego widoku do drugiego w wyniku interakcji z użytkownikiem lub metod.|
| `moveend`           | Uruchamiany tuż po zakończeniu przez mapę przejścia z jednego widoku do drugiego w wyniku interakcji z użytkownikiem lub metod.|
| `movestart`         | Uruchamiany tuż przed rozpoczęciem przez mapę przejścia z jednego widoku do drugiego w wyniku interakcji z użytkownikiem lub metod.|
| `open`              | Uruchamiany po ręcznym otwarciu okna podręcznego lub programowo.|
| `pitch`             | Uruchamiany zawsze, gdy nastąpi zmiana skoku mapy (pochylenie) w wyniku interakcji z użytkownikiem lub metod.|
| `pitchend`          | Uruchamiany natychmiast po pochylenie mapy (nachylenie) zakończy się zmianami w wyniku interakcji z użytkownikiem lub metodami.|
| `pitchstart`        | Uruchamiany zawsze, gdy gęstość mapy (nachylenie) rozpoczyna zmianę w wyniku interakcji z użytkownikiem lub metod.|
| `ready`             | Uruchamiany, gdy minimalne wymagane zasoby mapy są ładowane, zanim mapa będzie gotowa do programistycznego manipulowania.|
| `render`            | <p>Uruchamiany za każdym razem, gdy mapa zostanie narysowana na ekranie, w wyniku:<ul><li>Zmiana pozycji mapy, powiększenia, skoku lub nanoszenia.</li><li>Zmiana stylu mapy.</li><li>Zmiana w źródle `DataSource`.</li><li>Ładowanie kafelka wektorowego, pliku GEOJSON, symbolu lub Sprite.</li></ul></p>|
| `resize`            | Uruchamiany natychmiast po zmianie rozmiaru mapy.|
| `rotate`            | Wywoływane wielokrotnie podczas interakcji "przeciągnij, aby obrócić".|
| `rotateend`         | Uruchamiany po zakończeniu interakcji "przeciągnij do obrotu".|
| `rotatestart`       | Uruchamiany po rozpoczęciu interakcji "przeciągnij do obrotu".|
| `shapechanged`      | Uruchamiany, gdy zostanie zmieniona właściwość obiektu kształtu.|
| `sourcedata`        | Uruchamiany, gdy jeden ze źródeł mapy jest ładowany lub zmienia się, w tym, czy kafelek należący do źródła jest ładowany lub zmieniany. |
| `sourceadded`       | Uruchamiany, gdy do mapy zostanie dodany `DataSource` lub `VectorTileSource`.|
| `sourceremoved`     | Uruchamiany po usunięciu `DataSource` lub `VectorTileSource` z mapy.|
| `styledata`         | Uruchamiany, gdy styl mapy zostanie załadowany lub zmieniony.|
| `styleimagemissing` | Uruchamiany, gdy warstwa próbuje załadować obraz z Sprite obrazu, który nie istnieje |
| `tokenacquired`     | Uruchamiany, gdy zostanie uzyskany token dostępu usługi AAD.|
| `touchcancel`       | Uruchamiany, gdy w mapie wystąpi zdarzenie touchcancel.|
| `touchend`          | Uruchamiany, gdy w mapie wystąpi zdarzenie touchend.|
| `touchmove`         | Uruchamiany, gdy w mapie wystąpi zdarzenie TouchMove.|
| `touchstart`        | Uruchamiany, gdy w mapie wystąpi zdarzenie touchstart.|
| `wheel`             | Uruchamiany, gdy na mapie wystąpi zdarzenie kółka myszy.|
| `zoom`              | Uruchamiany wielokrotnie podczas animowanego przejścia z jednego poziomu powiększenia na inny w wyniku interakcji z użytkownikiem lub metod.|
| `zoomend`           | Uruchamiany tuż po zakończeniu przez mapę przejścia z jednego poziomu powiększenia na inny w wyniku interakcji z użytkownikiem lub metod.|
| `zoomstart`         | Uruchamiany tuż przed rozpoczęciem przez mapę przejścia z jednego poziomu powiększenia na inny w wyniku interakcji z użytkownikiem lub metod.|


## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby zapoznać się z pełnymi przykładami kodu:

> [!div class="nextstepaction"]
> [Korzystanie z modułu Azure Maps Services](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Przykłady kodu](https://docs.microsoft.com/samples/browse/?products=azure-maps)
