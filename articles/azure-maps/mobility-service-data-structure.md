---
title: Struktury danych usługi mobilności w Azure Maps | Mapy Microsoft Azure
description: Ten artykuł zawiera informacje o typowych polach i strukturach danych zwracanych przez Microsoft Azure Maps usługi mobilności.
author: farah-alyasari
ms.author: v-faalya
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 685810a6efa46c8eb3ad6cee0c2424299f0347d8
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209617"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Struktury danych w Azure Maps usłudze mobilności

W tym artykule wprowadzono koncepcję obszaru Metro w [Azure Maps usłudze mobilności](https://aka.ms/AzureMapsMobilityService). Omawiamy niektóre typowe pola, które są zwracane, gdy ta usługa jest przedmiotem zapytania o zatrzymanie i obsługę tranzytu publicznego. Zalecamy odczytywanie tego artykułu przed programowaniem przy użyciu interfejsów API usługi mobilności.

## <a name="metro-area"></a>Obszar Metro

Dane usługi mobilności są pogrupowane według obsługiwanych obszarów Metro. Obszary Metro nie obserwują granic miejscowości. Obszar Metro może zawierać wiele miast, gęsto wypełnione miasto i otaczające miasta. W rzeczywistości kraj/region może być jednym obszarem Metro. 

`metroID` to identyfikator obszaru Metro, którego można użyć do wywołania [interfejsu API informacji o powierzchni Metro](https://aka.ms/AzureMapsMobilityMetroAreaInfo). Użyj Azure Maps "" Get Metro ", aby zażądać typów tranzytowych, agencji tranzytowych, aktywnych alertów i dodatkowych szczegółów dla wybranej linii metra. Możesz również zażądać obsługiwanych obszarów Metro i metroIDs. Identyfikatory obszaru Metro mogą ulec zmianie.

**metroID:** 522 **Nazwa:** Seattle-Tacoma-Bellevue

![Seattle — obszar Metro](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Zatrzymywanie identyfikatorów

Zatrzymywanie tranzytu może być określane przez dwa typy identyfikatorów, identyfikator [GFTS (Generally exportion Specification)](https://gtfs.org/) i identyfikator zatrzymania Azure Maps. Identyfikator GFTS jest określany jako stopKey, a Azure Maps identyfikator zatrzymania jest określany jako stopID. Podczas częstego odwoływania się do zatrzymywania tranzytu zaleca się użycie identyfikatora zatrzymania Azure Maps. stopID jest bardziej stabilna i prawdopodobnie pozostaje taka sama, o ile istnieje fizyczne zatrzymanie. Identyfikator zatrzymania GTFS jest aktualizowany częściej. Na przykład identyfikator zatrzymania GTFS można zaktualizować na żądanie dostawcy GTFS lub w przypadku wydania nowej wersji GTFS. Mimo że fizyczne zatrzymanie nie miało żadnej zmiany, identyfikator zatrzymania GTFS może ulec zmianie.

Aby rozpocząć, możesz zażądać zatrzymania tranzytu w pobliżu za pomocą [interfejsu API pobierania pobliskich tranzytów](https://aka.ms/AzureMapsMobilityNearbyTransit).

## <a name="line-groups-and-lines"></a>Wiersze i grupy wierszy

Usługa mobilności używa równoległego modelu danych dla linii i grup liniowych. Ten model jest używany do lepszego postępowania z zmianami dziedziczonymi z tras [GTFS](https://gtfs.org/) i danych podróży.


### <a name="line-groups"></a>Grupy wierszy

Grupa wierszy jest jednostką, która grupuje ze sobą wszystkie wiersze, które są logicznie częścią tej samej grupy. Zwykle grupa wierszy zawiera dwa wiersze, jeden od punktu A do B, a druga zwraca od punktu B do A. Oba wiersze powinny należeć do tej samej publicznej agencji transportowej i mieć ten sam numer wiersza. Mogą jednak wystąpić sytuacje, w których grupa linii ma więcej niż dwa wiersze lub tylko jeden wiersz.


### <a name="lines"></a>Wiersze

Jak opisano powyżej, każda grupa wierszy składa się z zestawu wierszy. Każda grupa wierszy składa się z dwóch wierszy, a każdy wiersz opisuje kierunek.  Istnieją jednak przypadki, w których więcej wierszy redaguje grupę wierszy. Na przykład jest to linia, która czasami zawiera kilka przewodników w określonym klubie i czasami nie jest. W obu przypadkach działa pod tym samym numerem wiersza. Ponadto grupa wierszy może składać się z jednego wiersza. Cykliczna linia z pojedynczym kierunkiem jest grupą z jednym wierszem.

Aby rozpocząć, możesz zażądać grup wierszy przy użyciu [interfejsu API uzyskiwania linii tranzytowej](https://aka.ms/AzureMapsMobilityTransitLine).


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak żądać danych tranzytowych przy użyciu usługi mobilności:

> [!div class="nextstepaction"]
> [Jak żądać danych tranzytowych](how-to-request-transit-data.md)

Informacje na temat żądania danych w czasie rzeczywistym przy użyciu usługi mobilności:

> [!div class="nextstepaction"]
> [Jak żądać danych w czasie rzeczywistym](how-to-request-real-time-data.md)

Poznaj dokumentację interfejsu API usługi mobilności Azure Maps

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API usługi mobilności](https://aka.ms/AzureMapsMobilityService)
