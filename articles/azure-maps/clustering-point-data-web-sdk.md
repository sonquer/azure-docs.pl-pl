---
title: Dane punktu klastrowania na mapie | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak wskazać dane punktu klastra i renderować je na mapie za pomocą zestawu Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: e65681aefc047ba540d4ad0d91ef6e4d2af5f3ca
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190262"
---
# <a name="clustering-point-data"></a>Dane punktu klastrowania

Podczas wizualizacji wielu punktów danych na mapie punkty danych mogą się nakładać. Nakładanie może spowodować, że mapa może stać się nieczytelna i trudne do użycia. Dane punktu klastrowania to proces łączenia danych punktów, które są blisko siebie i reprezentowania ich na mapie jako pojedynczy klastrowany punkt danych. Gdy użytkownik powiększa mapę, klastry rozdzielą się na poszczególne punkty danych. Podczas pracy z dużą liczbą punktów danych Użyj procesów klastrowania, aby poprawić środowisko użytkownika.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>Włączanie klastrowania w źródle danych

Włącz klastrowanie w klasie `DataSource`, ustawiając dla opcji `cluster` wartość true. Ustaw `ClusterRadius`, aby wybierają punkty w pobliżu i łączą je z klastrem. Wartość `ClusterRadius` jest w pikselach. Użyj `clusterMaxZoom`, aby określić poziom powiększenia, dla którego ma zostać wyłączona logika klastrowania. Oto przykład sposobu włączania klastrowania w źródle danych.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15
});
```

> [!TIP]
> Jeśli dwa punkty danych są blisko siebie, możliwe, że klaster nigdy nie ulegnie przerwie, niezależnie od tego, jak zamyka się użytkownik. Aby rozwiązać ten rozwiązanie, można ustawić opcję `clusterMaxZoom`, aby wyłączyć logikę klastrowania i po prostu wyświetlić wszystkie elementy.

Poniżej znajdują się dodatkowe metody zapewniane przez klasę `DataSource` na potrzeby klastrowania:

| Metoda | Zwracany typ | Opis |
|--------|-------------|-------------|
| getClusterChildren (clusterId: Number) | Funkcja Promise&lt;Array&lt;funkcji&lt;geometria&gt; \|&gt;&gt; | Pobiera elementy podrzędne danego klastra na następnym poziomie powiększenia. Te elementy podrzędne mogą być kombinacją kształtów i podklastrów. Podklastry będą funkcjami o właściwościach pasujących do ClusteredProperties. |
| getClusterExpansionZoom (clusterId: Number) | Numer&lt;Promise&gt; | Oblicza poziom powiększenia, przy którym klaster rozpocznie rozszerzanie lub przerywanie. |
| getClusterLeaves (clusterId: Number, limit: Number, offset: Number) | Funkcja Promise&lt;Array&lt;funkcji&lt;geometria&gt; \|&gt;&gt; | Pobiera wszystkie punkty w klastrze. Ustaw `limit`, aby zwracał podzbiór punktów, i użyj `offset` do strony za pomocą punktów. |

## <a name="display-clusters-using-a-bubble-layer"></a>Wyświetlanie klastrów przy użyciu warstwy bąbelkowej

Warstwa bąbelków to doskonały sposób renderowania punktów klastrowanych. Użyj wyrażeń, aby skalować promień i zmienić kolor na podstawie liczby punktów w klastrze. W przypadku wyświetlania klastrów przy użyciu warstwy bąbelków należy użyć osobnej warstwy do renderowania nieklastrowanych punktów danych.

Aby wyświetlić rozmiar klastra w górnej części bąbelka, użyj warstwy symbol z tekstem i nie używaj ikony.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Podstawowe klastrowanie warstwy bąbelków" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zapoznaj się z tematem zaAzure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>warstwie bąbelkowej</a> dla języka Basic na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Wyświetlanie klastrów przy użyciu warstwy symboli

Podczas wizualizacji punktów danych warstwa symboli automatycznie ukrywa symbole, które nakładają się na siebie, aby zapewnić oczyszczarkę interfejsu użytkownika. To zachowanie domyślne może być niepożądane, jeśli chcesz pokazać gęstość punktów danych na mapie. Można jednak zmienić te ustawienia. Aby wyświetlić wszystkie symbole, ustaw opcję `allowOverlap` `iconOptions` właściwości warstwy symboli na `true`. 

Użyj klastrowania, aby pokazać gęstość punktów danych przy zachowaniu czystego interfejsu użytkownika. Poniższy przykład pokazuje, jak dodawać niestandardowe symbole i reprezentować klastry oraz poszczególne punkty danych przy użyciu warstwy symboli.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Klastrowana warstwa symboli" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>warstwą symboli klastrowanych</a> piórem według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Klastrowanie i warstwa mapy cieplnej

Mapy cieplne to doskonały sposób wyświetlania gęstości danych na mapie. Ta metoda wizualizacji może samodzielnie obsłużyć dużą liczbę punktów danych. Jeśli punkty danych są klastrowane i rozmiar klastra jest używany jako waga mapy cieplnej, Mapa cieplna może obsłużyć jeszcze więcej danych. Aby osiągnąć tę opcję, należy ustawić opcję `weight` warstwy mapy cieplnej na `['get', 'point_count']`. Gdy promień klastra jest mały, Mapa cieplna będzie wyglądać niemal identycznie jak mapa cieplna korzystająca z nieklastrowanych punktów danych, ale będzie działać znacznie lepiej. Jednak im mniejsza jest wartość promienia klastra, tym dokładniejsza jest mapa cieplna, ale z mniejszą wydajnością.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapa cieplna ważona klastra" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zapoznaj się <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>z Azure Maps</a> (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Zdarzenia myszy w klastrowanych punktach danych

Gdy zdarzenia myszy wystąpią na warstwie zawierającej klastrowane punkty danych, klastrowany punkt danych wraca do zdarzenia jako obiekt funkcji punktu GEOJSON. Ta funkcja wskazuje następujące właściwości:

| Nazwa właściwości             | Typ    | Opis   |
|---------------------------|---------|---------------|
| `cluster`                 | wartość logiczna | Wskazuje, czy funkcja reprezentuje klaster. |
| `cluster_id`              | ciąg  | Unikatowy identyfikator klastra, który może być używany z metodami DataSource `getClusterExpansionZoom`, `getClusterChildren`i `getClusterLeaves`. |
| `point_count`             | numer  | Liczba punktów, które zawiera klaster.  |
| `point_count_abbreviated` | ciąg  | Ciąg, który skróci wartość `point_count`, jeśli jest długi. (na przykład 4 000 to 4K)  |

Ten przykład pobiera warstwę bąbelkową, która renderuje punkty klastra i dodaje zdarzenie kliknięcia. Gdy wyzwalane jest zdarzenie kliknięcia, kod oblicza i powiększa mapę do następnego stopnia powiększenia, przy czym klaster zostaje podzielony na siebie. Ta funkcja jest implementowana przy użyciu metody `getClusterExpansionZoom` klasy `DataSource` i właściwości `cluster_id` klikniętego klastrowanego punktu danych.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="GetClusterExpansionZoom klastra" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/moZWeV/'>GetClusterExpansionZoom klastra</a> pióra według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Wyświetlanie obszaru klastra 

Dane punktów reprezentowane przez klaster są rozłożone na obszar. W tym przykładzie, gdy wskaźnik myszy jest aktywowany przez klaster, występują dwa główne zachowania. Najpierw poszczególne punkty danych zawarte w klastrze będą używane do obliczania wypukłych kadłubów. Następnie na mapie zostanie wyświetlony wyświetlona kadłub, aby wyświetlić obszar.  Kadłub wypukły jest wielokątem, który otacza zestaw punktów, takich jak elastyczny zakres i może być obliczany przy użyciu metody `atlas.math.getConvexHull`. Wszystkie punkty zawarte w klastrze mogą być pobierane ze źródła danych przy użyciu metody `getClusterLeaves`.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Kadłub wypukłych obszarów klastra" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>kadłubem obszar klastra piórem wypukły</a> przez Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w witrynie <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Agregowanie danych w klastrach

Często klastry są reprezentowane przy użyciu symbolu z liczbą punktów znajdujących się w klastrze. Jednak czasami pożądane jest dostosowanie stylu klastrów przy użyciu dodatkowych metryk. W przypadku agregacji klastra właściwości niestandardowe można tworzyć i wypełniać przy użyciu obliczeń [wyrażeń agregujących](data-driven-style-expressions-web-sdk.md#aggregate-expression) .  Agregacje klastrów można definiować w `clusterProperties` opcji `DataSource`.

W poniższym przykładzie zastosowano wyrażenie agregujące. Kod oblicza liczbę na podstawie właściwości typu jednostki każdego punktu danych w klastrze. Gdy użytkownik kliknie klaster, zostanie wyświetlone okno podręczne z dodatkowymi informacjami o klastrze.

<iframe height="500" style="width: 100%;" scrolling="no" title="Agregacje klastra" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>agregaty klastra</a> pióra według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [DataSource — Klasa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Obiekt DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Atlas. Math — przestrzeń nazw](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Zobacz przykłady kodu, aby dodać funkcje do aplikacji:

> [!div class="nextstepaction"]
> [Dodaj warstwę bąbelkową](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę symboli](map-add-pin.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę mapy cieplnej](map-add-heat-map-layer.md)
