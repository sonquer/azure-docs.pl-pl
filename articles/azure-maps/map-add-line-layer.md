---
title: Dodawanie warstwy liniowej do mapy | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak dodać warstwę linii do mapy przy użyciu zestawu Microsoft Azure Web SDK mapy.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 8503b12be628fe7d5651221c9d0379bee3e292bd
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76933481"
---
# <a name="add-a-line-layer-to-the-map"></a>Dodawanie warstwy liniowej do mapy

Warstwa linii może służyć do renderowania funkcji `LineString` i `MultiLineString` jako ścieżki lub trasy na mapie. Warstwa linii może być również używana do renderowania konspektu funkcji `Polygon` i `MultiPolygon`. Źródło danych jest połączone z warstwą linii w celu zapewnienia jej danych do renderowania. 

> [!TIP]
> Warstwy liniowe domyślnie będą renderować współrzędne wielokątów, jak również linie w źródle danych. Aby ograniczyć warstwę w taki sposób, że renderuje tylko funkcje LineString, ustaw właściwość `filter` warstwy na `['==', ['geometry-type'], 'LineString']` lub `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]`, jeśli chcesz uwzględnić również funkcje MultiLineString.

Poniższy kod pokazuje, jak utworzyć wiersz. Dodaj wiersz do źródła danych, a następnie Renderuj go za pomocą warstwy liniowej za pomocą klasy [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) .

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a line and add it to the data source.
dataSource.add(new atlas.data.LineString([[-73.972340, 40.743270], [-74.004420, 40.756800]]));
  
//Create a line layer to render the line to the map.
map.layers.add(new atlas.layer.LineLayer(dataSource, null, {
    strokeColor: 'blue',
    strokeWidth: 5
}));
```

Poniżej znajduje się kompletny przykładowy kod wykonywany z powyższymi funkcjami.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie linii do mapy' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Dodaj linię do mapy</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

Warstwy linii można stylować za pomocą [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) i [używać wyrażeń opartych na danych](data-driven-style-expressions-web-sdk.md).

## <a name="add-symbols-along-a-line"></a>Dodaj symbole wzdłuż linii

Ten przykład pokazuje, jak dodać ikony strzałek wzdłuż linii na mapie. W przypadku korzystania z warstwy symboli ustaw opcję "położenie" na "wiersz". Ta opcja spowoduje renderowanie symboli wzdłuż linii i obrócenie ikon (0 stopni = Right).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Pokaż strzałkę wzdłuż linii" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/drBJwX/'>strzałka Pokaż strzałkę obok pozycji</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Zestaw SDK sieci Web Azure Maps udostępnia kilka dostosowywalnych szablonów obrazów, których można używać z warstwą symboli. Aby uzyskać więcej informacji, zobacz dokument [jak korzystać z szablonów obrazów](how-to-use-image-templates-web-sdk.md) .

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>Dodawanie gradientu obrysu do linii

Możesz zastosować jeden kolor pociągnięcia do linii. Możesz również wypełnić linię gradientem kolorów, aby pokazać przejście z jednego segmentu linii do następnego segmentu wiersza. Na przykład gradienty linii mogą służyć do reprezentowania zmian w czasie i odległości lub w różnych temperaturach w połączonej linii obiektów. Aby można było zastosować tę funkcję do linii, źródło danych musi mieć ustawioną opcję `lineMetrics` wartość true, a następnie wyrażenie gradientu koloru może być przesyłane do opcji `strokeColor` wiersza. Wyrażenie gradientu obrysu musi odwoływać się do wyrażenia danych `['line-progress']`, które uwidacznia obliczane metryki linii dla wyrażenia.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Linia z gradientem pociągnięcia" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz linię pióra <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>z gradientem pociągnięcia</a> , Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-line-layer"></a>Dostosowywanie warstwy liniowej

Warstwa linii ma kilka opcji stylów. Oto narzędzie do wypróbowania.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy linii' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>Opcje warstwy</a> pióra według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 

> [!div class="nextstepaction"]
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Tworzenie źródła danych](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Dodawanie okna podręcznego](map-add-popup.md)

> [!div class="nextstepaction"]
> [Używanie wyrażeń stylów opartych na danych](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Jak używać szablonów obrazów](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę wielokątów](map-add-shape.md)
